# Q7: AI Infrastructure — Model Selection, Memory/MCP, TCO

By mid-2026 the "AI plumbing" for a consulting team is no longer a side project — it is the difference between margin and ruin. SDK downloads for the Model Context Protocol grew from ~100K to 97M/month in 18 months ([SitePoint](https://www.sitepoint.com/model-context-protocol-mcp/)), enterprise teams routinely run five or more models per repo ([Inworld](https://inworld.ai/resources/what-is-an-ai-router)), and six frontier-class coding models now sit within 0.8 points of each other on SWE-bench Verified ([MindStudio](https://www.mindstudio.ai/blog/best-open-source-llms-agentic-coding-2026)). The principal-dev job is to compose a stack that picks the right model per task, stops cost and secrets bleeding, gives agents durable memory without cross-tenant contamination, and knows the exact volume at which self-hosting beats the API.

## Model Selection: IDE Routers vs Gateways

Cursor's built-in router (Auto mode) is heuristic — task length, repo metadata, latency budget — and benchmarks show it matches or beats manual Sonnet selection on clean codebases but degrades on large messy ones ([dev.to test](https://dev.to/nedcodes/i-tested-whether-cursors-auto-mode-actually-picks-the-right-model-20ml)). For anything beyond a single team, front the IDE with a proxy.

| Tool | Best for | Catalog | Markup | Self-host |
|---|---|---|---|---|
| **LiteLLM** | Full control, auditable rules | 100+ providers | $0 + infra | Yes |
| **OpenRouter** | Exploration, broad model access | 300+ models / 60+ providers | ~5.5% | No |
| **Portkey** | Compliance, PII guardrails, audit | Major providers | ~$49/mo + usage | Yes (enterprise) |
| **Helicone** | Observability-first, analytics | Provider-agnostic | Per-tier | Yes |

At $1K/month spend: OpenRouter ≈ $1,055, LiteLLM ≈ $1,020-1,050 (incl. VPS), Portkey ≈ $1,049 ([PkgPulse](https://www.pkgpulse.com/guides/portkey-vs-litellm-vs-openrouter-llm-gateway-2026)).

**Routing rules that actually matter in 2026** ([Codersera Cursor guide](https://codersera.com/blog/cursor-ide-complete-guide-2026/)):

- **By task type**: Gemini 3 Flash for refactors/scaffolds; Claude Sonnet for feature dev and code review; Opus 4.7 / GPT-5.5 reserved for architecture, security audits, hard algorithms.
- **By complexity**: token-budget triggers and tool-call depth signals — escalate when stack depth > N or test failures > M.
- **By sensitivity**: PII/secret-positive prompts go to on-prem (DeepSeek V4 / Qwen 3.6 self-hosted) only.
- **By cost**: hard daily caps per tenant, with 75/90/95/100% alert thresholds.

Use the IDE router for solo work and the gateway proxy once you need cost attribution, key rotation, or PII redaction.

## Token/Cost Protection

**Rate-limiting and budget caps** are now table stakes. Every multi-tenant agent product needs three enforced layers: per-tenant daily spend cap (highest-leverage control), per-user/per-request token cap, and an alert ladder firing at 75/90/95/100% ([Digital Applied](https://www.digitalapplied.com/blog/llm-agent-cost-attribution-guide-production-2026)). LiteLLM, Portkey, and TrueFoundry all expose budget primitives natively.

**Secrets and PII scrubbing** before tokens leave the building:

- **Microsoft Presidio** — open-source, NER + regex + checksum; integrates as a LiteLLM pre-hook ([LiteLLM Presidio guide](https://docs.litellm.ai/docs/tutorials/presidio_pii_masking)).
- **GitGuardian / ShieldGemma** — known-secret entropy detection for code prompts.
- **Custom regex layer** — repo-specific (internal hostnames, customer IDs); cheap, fast, runs before Presidio.

Stack them: regex first (μs), Presidio second (ms), provider-side guardrails (Portkey) third. Deanonymise on response so the model still sees structurally valid placeholders.

**Cost attribution per-PR / per-task**: tag every request with `repo`, `pr_number`, `engineer`, `step` metadata. Helicone and Langfuse both accept arbitrary metadata for slicing; Helicone tracks per-user cost natively and is free up to 10K requests ([Helicone cost tracking](https://docs.helicone.ai/guides/cookbooks/cost-tracking)). A documented 50-engineer team running a Claude-Code PR-review agent (~15 PRs/eng/week, ~400K input tokens/PR) discovered they could cut the summarisation step 80% by swapping Sonnet → Haiku — only because they had per-step attribution ([TrueFoundry](https://www.truefoundry.com/blog/llm-cost-attribution-agentic-cicd)). Forecast using P95 rolling per repo/team, not averages — that's where the burst risk lives.

## Memory and MCP Architecture

MCP (Anthropic, late 2024) standardised tool/data exposure to LLMs; by 2026 it is the dominant integration substrate. **Memory MCP servers** persist state across sessions and now ship in three flavours ([modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers/tree/main/src/memory), [mem0 State of Memory 2026](https://mem0.ai/blog/state-of-ai-agent-memory-2026)):

- **Knowledge-graph memory** — entities, relations, observations (Anthropic's reference, `mcp-knowledge-graph`, `memory-graph`); best for code-architectural facts.
- **Vector stores** — semantic recall of conversations and docs; pairs well with RAG MCPs.
- **Hybrid** (`mcp-memory-service`) — REST API + KG + autonomous consolidation for LangGraph/CrewAI pipelines.

**Retention/eviction**: TTLs per memory class (code patterns: long; ephemeral tickets: short), autonomous consolidation runs (dedupe + summarise stale entries), and explicit eviction APIs. Silent staleness is the dominant failure mode — answers become "less grounded and more generic" without errors ([Orca Security](https://orca.security/resources/blog/bringing-memory-to-ai-mcp-a2a-agent-context-protocols/)).

**Contamination prevention**: scope `USER_ID`/`TENANT_ID` from the authenticated caller, not the memory layer; partition containers by tenant; metadata-filter every read; never run unscoped queries. Cross-tenant leakage typically arises from session-queue bugs that merge traces ([Prefactor](https://prefactor.tech/blog/mcp-security-multi-tenant-ai-agents-explained)). Sandbox per project (k8s namespace + dedicated VPC for sensitive clients).

**Multi-MCP composition**: agent-driven (the model picks tools) is flexible but unbounded — chain depth and latency explode. Scripted chains (e.g., LangGraph state machines) are bounded and observable but brittle. The 2026 IBM/Microsoft pattern is **hybrid**: thin ingress + approval checkpoints + scripted critical paths + agent-driven exploration inside guarded sub-graphs ([IBM MCP patterns](https://developer.ibm.com/articles/mcp-architecture-patterns-ai-systems/)). Enforce per-server 30s timeouts, `skip_failed_servers=True`, circuit breakers, and resource arbitration to prevent deadlocks ([Cogent failure playbook](https://cogentinfo.com/resources/when-ai-agents-collide-multi-agent-orchestration-failure-playbook-for-2026)).

## TCO: Frontier API vs Self-Hosted

**Frontier per-token (May 2026, $/M tokens, input/output)**:

| Model | Input | Output | Notes |
|---|---|---|---|
| Claude Opus 4.7 | $5 | $25 | Cache reads $0.50; Batch API halves both ([CloudZero](https://www.cloudzero.com/blog/claude-opus-4-7-pricing/)) |
| GPT-5.5 | $15 | $60 | Highest flagship rate ([IntuitionLabs](https://intuitionlabs.ai/articles/ai-api-pricing-comparison-grok-gemini-openai-claude)) |
| Gemini 3.1 Pro | $2 | $12 | Flash variant $0.50/$3 |
| DeepSeek V3.2 | $0.28 | $0.42 | Cost floor for capable coding |

**Self-hosted** (vLLM or SGLang; SGLang ~29% faster on H100, 16.2K vs 12.5K tok/s, up to 6× on prefix-heavy RAG; [particula.tech](https://particula.tech/blog/sglang-vs-vllm-inference-engine-comparison)):

- 8×H100 on-demand: **$22-28K/mo**; 1-year reserved cuts ~25-30%.
- 8×H200 141GB single node handles DeepSeek V4-Pro full precision ([Codersera](https://codersera.com/blog/self-hosting-llms-complete-guide-2026/)).
- Blackwell B200/GB200 + NVFP4 is the 2026 cost lever for high-volume coding.

**Break-evens** ([TokenMix](https://tokenmix.ai/blog/self-host-llm-vs-api)):

- 4×H100 FP8: ~50-100M tokens/day (~1.5-3B/mo).
- Coding workloads: ~600M tok/mo; chat: ~1.2B tok/mo.
- 4×H100 INT4: ~5B tok/mo for chat.
- DeepSeek V4-Flash via API: only break even at **3-4B tokens/day** on reserved instances — a strong "stay on the API" signal for most consultancies.

Critical caveat: break-even assumes one full-time inference engineer keeping vLLM/SGLang tuned, batched, and quantised. Below ~3B tokens/month, frontier APIs win on TCO once headcount is priced in.

**Coding quality (SWE-bench Verified, 2026)**: GLM-5.1 58.4% on SWE-bench Pro beats GPT-5.4 (57.7%) and Claude Opus 4.6 (57.3%); DeepSeek V4-Pro 80.6% on Verified, within 0.2 of Opus 4.6; Qwen 3.6-35B-A3B 73.4% with only 3B active params ([AkitaOnRails benchmarks](https://akitaonrails.com/en/2026/04/24/llm-benchmarks-parte-3-deepseek-kimi-mimo/), [SWE-bench leaderboard](https://www.swebench.com/)). Open-weight has effectively closed the gap for code; default to frontier for greenfield reasoning, route bulk diff/refactor to self-hosted Qwen/DeepSeek above break-even.

## Recommendation

Start with **LiteLLM proxy + Helicone observability + Presidio pre-hook + Cursor/Claude Code on top**, per-tenant budgets enforced, metadata-tagged for per-PR attribution. Adopt MCP memory (knowledge-graph for code facts, vector for conversation) with strict tenant scoping. Stay on frontier APIs until sustained usage clears ~600M coding tokens/month — then add an 8×H100 SGLang cluster running DeepSeek V4 or Qwen 3.6 for bulk traffic, keep Opus/GPT-5.5 for the top 10% of hard tasks.

## Sources

- [LiteLLM Presidio Tutorial](https://docs.litellm.ai/docs/tutorials/presidio_pii_masking)
- [Portkey vs LiteLLM vs OpenRouter — PkgPulse](https://www.pkgpulse.com/guides/portkey-vs-litellm-vs-openrouter-llm-gateway-2026)
- [Best LLM Router and AI Gateway 2026 — Inworld](https://inworld.ai/resources/best-llm-router-ai-gateway)
- [Claude Opus 4.7 Pricing — CloudZero](https://www.cloudzero.com/blog/claude-opus-4-7-pricing/)
- [LLM API Pricing Comparison 2026 — IntuitionLabs](https://intuitionlabs.ai/articles/ai-api-pricing-comparison-grok-gemini-openai-claude)
- [Self-Hosting LLMs 2026 — Codersera](https://codersera.com/blog/self-hosting-llms-complete-guide-2026/)
- [Self-Host vs API Break-Even — TokenMix](https://tokenmix.ai/blog/self-host-llm-vs-api)
- [SGLang vs vLLM 2026 — Particula](https://particula.tech/blog/sglang-vs-vllm-inference-engine-comparison)
- [MCP Knowledge Graph Memory Server](https://github.com/modelcontextprotocol/servers/tree/main/src/memory)
- [State of AI Agent Memory 2026 — mem0](https://mem0.ai/blog/state-of-ai-agent-memory-2026)
- [MCP Multi-Tenant Security — Prefactor](https://prefactor.tech/blog/mcp-security-multi-tenant-ai-agents-explained)
- [Multi-Agent Orchestration Failure Playbook — Cogent](https://cogentinfo.com/resources/when-ai-agents-collide-multi-agent-orchestration-failure-playbook-for-2026)
- [MCP Architecture Patterns — IBM](https://developer.ibm.com/articles/mcp-architecture-patterns-ai-systems/)
- [LLM Agent Cost Attribution — Digital Applied](https://www.digitalapplied.com/blog/llm-agent-cost-attribution-guide-production-2026)
- [Agentic Token Explosion — TrueFoundry](https://www.truefoundry.com/blog/llm-cost-attribution-agentic-cicd)
- [Helicone Cost Tracking Cookbook](https://docs.helicone.ai/guides/cookbooks/cost-tracking)
- [Cursor IDE Complete Guide 2026 — Codersera](https://codersera.com/blog/cursor-ide-complete-guide-2026/)
- [SWE-bench Leaderboard](https://www.swebench.com/)
- [Open-Source LLMs Agentic Coding 2026 — MindStudio](https://www.mindstudio.ai/blog/best-open-source-llms-agentic-coding-2026)
- [LLM Coding Benchmarks May 2026 — AkitaOnRails](https://akitaonrails.com/en/2026/04/24/llm-benchmarks-parte-3-deepseek-kimi-mimo/)
- [Bringing Memory to AI: MCP/A2A — Orca Security](https://orca.security/resources/blog/bringing-memory-to-ai-mcp-a2a-agent-context-protocols/)
- [Model Context Protocol Guide — SitePoint](https://www.sitepoint.com/model-context-protocol-mcp/)
