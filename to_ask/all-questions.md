# Full Question Set for the Team — AI SDLC Practice

**Context:** Expanded list of principal-dev-level questions grouped by topic. Covers methodology, infrastructure, team, governance, security, and commercial model. No references to internal artifacts or confidential metrics.

---

## Group 1: Offering Scope & Engagement Structure

1. **Which AI SDLC Practice packages are sell-ready today, and which are still under construction?** Do we have a short diagnostic matrix — *client maturity / stack / team size → recommended package* — across Assessment, Introduction, Uplift, Module-based, Native Transition, and Brownfield?
2. **What artifacts does the client receive at each engagement phase (Clarify, Calibrate, Launch, Refine)?** Assessment report, capability map, target operating model, roadmap, ROI model — who writes which, in what order, and where are the key milestone checks?
3. **What's the minimum AI SDLC engagement we offer?** Standalone Assessment, single-team pilot, full roll-out — what's the entry threshold so we can scope first conversations correctly?
4. **What happens after the engagement wraps?** Next phase — scale-out to additional teams, managed delivery, ongoing support? How do we pitch this during the final retrospective?
5. **How do we position AI SDLC alongside AI features in the client's product (AI in Product Engineering)?** *"How we write the code"* vs. *"AI inside your product"* — is there a recommended framing that separates these two messages on the first call?

---

## Group 2: Methodology — SDD, Behavior Layers, Control Gates

6. **Spec Driven Development (the methodology where the specification is the primary artifact and code is derivative) — what's the specification format?** EARS-style (Easy Approach to Requirements Syntax — *"When [condition], the system shall [action]"*), custom DSL (Domain-Specific Language), structured Markdown? How do specs handle ambiguity, edge cases, and non-functional requirements (performance, security, accessibility)?
7. **How do specs live alongside code?** Same repo? Who's the formal owner (PM, Tech Lead, AI)? Is there a CI gate (Continuous Integration check that runs before merge) that flags spec/code divergence?
8. **Agent configuration layers (project rules, AGENTS.md, ARCHITECTURE.md, IDE-specific rules) — how do they compose?** What's the precedence order when layers conflict? How do we verify that a rules change doesn't silently alter agent behavior on existing tasks (regression testing on the rules themselves)?
9. **HITL gates (Human-In-The-Loop — mandatory human-approval checkpoints) — what's the taxonomy?** Which classes of change cannot be merged without human approval (DB migrations — schema changes, security-sensitive code, public API, infra changes)? Is there a distinction between plan-mode approval and agent-mode approval?
10. **How do we keep HITL gates from degrading into rubber-stamp approvals?** Metrics on review depth and time-to-approve, minimum review SLA, escalation patterns?
11. **Brownfield SDD: do we generate specs from existing code (reverse SDD)?** If yes — how do we validate that the generated specs reflect real intent rather than accidental behavior? How do we handle undocumented invariants (things that work by convention but are written down nowhere)?

---

## Group 3: Quality & Evidence

12. **Eval methodology (how we measure generated-code quality) — what do we have beyond "tests are green"?** Eval harness (the test rig used to score agents), property-based testing (tests with randomized inputs), mutation testing (deliberate code changes to grade test strength), semantic equivalence (verifying that two implementations behave identically)?
13. **How do we catch silent failures (code passes tests but doesn't fulfill the spec)?** Defect escape rate (% of defects that made it to production) at 30 / 60 / 90 days post-merge?
14. **Evidence base: which version of the case study can we show publicly?** Pre-NDA (before a Non-Disclosure Agreement is signed), post-NDA, or invitation-only sessions?
15. **How was the baseline measured methodologically?** Stopwatch, surveys, GitHub / Jira telemetry, or estimate? If the reported improvement is a range, is that variance across sub-teams (iOS / Android / Web / Backend) or across task types within a single team?
16. **How do we attribute impact to AI tooling specifically vs. Hawthorne effect (improvement caused by the act of being observed) vs. new process vs. new stack?**
17. **Which external sources do we use to reinforce the case study?** Research (GitHub, METR, DORA, McKinsey), vendor whitepapers (Anthropic, OpenAI, GitHub), public case studies (Shopify, Klarna, Block) — do we have an approved short-list?

---

## Group 4: Infrastructure & Models

18. **Model Selection — is the router (the component that picks a model per task) IDE-native or a custom proxy / gateway?** What are the routing rules (by task type, complexity, sensitivity, cost budget)?
19. **Token Protection and Cost Monitoring — what's actually under the hood?** Rate limiting, budget caps, secrets scrubbing (stripping secrets from prompts before they hit the model)? How do we attribute cost-per-merged-PR back to specific tasks?
20. **Memory via Model Context Protocol (the standard for connecting agents to external systems) — how is the agent's persistent memory architected?** What's stored (decisions, learnings, context references), what's the storage backend, retention / eviction policy (rules for purging old entries)? How do we avoid contamination (context leaking between projects or clients)?
21. **How do multiple MCPs compose at runtime?** Does the agent pick the MCP itself or is it hard-coded in the prompt? Latency, timeout, failure handling — if one MCP doesn't respond, what happens? Security model — does each MCP have its own scope, or are credentials shared?
22. **Frontier API vs. self-hosting TCO — where's the crossover point?** Do we have an internal eval matrix across Claude Opus 4.7 / GPT-5 / Gemini 3 / Qwen / DeepSeek with pass rate and cost-per-task? At what monthly token volume does a self-hosted GPU cluster (H100 / H200 / Blackwell with vLLM or SGLang — LLM inference servers) beat per-token API pricing, including amortization and ops headcount?
23. **How do we navigate model changes?** When a vendor ships a new version or deprecates a SKU — what's the regression suite, the canary process, how much rework hits client specs? Are our prompts and specs portable across vendors?

---

## Group 5: Tooling & Portability

24. **IDE portability — which methodology pieces are stack-portable vs. IDE-specific?** AGENTS.md (emerging community standard) and MCPs (protocol-level) port across; project rules, modes (ask / plan / agent), prompt-standards UI are IDE-specific. What percentage of the methodology transfers as-is across Cursor, Claude Code (Anthropic's CLI), Codex CLI (OpenAI's CLI), Copilot, and Windsurf?
25. **How is the MCP set organized across the client's stack layers?** iOS (XcodeBuild, simulator), Android (Gradle, Compose), Web (browser DevTools, Playwright), Backend (Spring / .NET / Node debugger), Data (dbt, Airflow), Infra (Kubernetes, Terraform), Observability (Sentry, Datadog) — are these ready packages, or assembled ad-hoc per client?

---

## Group 6: Team Composition & Multi-Team Governance

26. **Pilot-team shape — what's the optimal headcount and ratio?** How many engineers per concurrent agent session? Which legacy roles (juniors, dedicated QA, manual testers) get absorbed, retrained, or moved out of scope?
27. **New roles — jobs or skills?** Spec Author, AI Architect, Context Engineer (the engineer who owns convention files, rules, MCP configs), Agent Supervisor (the human watching parallel agent sessions) — distinct hires or skills layered on existing seniors?
28. **How does the Tech Lead / Architect role change?** How many parallel agents can one senior realistically supervise before quality collapse (degraded output when supervision capacity is overloaded)?
29. **Where does QA go if the agent writes and runs its own tests?** Spec review (reviewing specs before implementation), adversarial testing (hunting edge cases, weak spots, security flaws), eval authoring (writing the test rigs that grade agents), or gradual headcount reduction?
30. **Who owns central artifacts across many teams?** Specification templates, conventions, MCP integrations — Center of Excellence (CoE — the strategic R&D layer), Guild (cross-team horizontal community of practitioners), or a dedicated Platform team (the group that owns internal tooling and integrations)?
31. **How do we prevent "dialects of SDD" (methodology drift between teams)?** If teams pick different CLIs / IDE agents and share a monorepo (single Git repo containing multiple projects) or shared services, what governance keeps artifacts compatible?
32. **How do reusable specs, prompts, and agent rules get shared between teams?** Internal marketplace (an artifact marketplace with a review process), shared Git, hub repo of convention files, package registry? Who validates quality before publication?
33. **Which cross-team sync cadence actually works?** Weekly demos, AI guild meetings, shared retros — and which ones turned out to be theatre (ceremony with no impact on quality or velocity)?

---

## Group 7: Security, Data, Compliance

34. **What does the data flow look like when a client engineer pastes production code into an agent?** What gets stripped, what gets logged, what hits the frontier endpoint, what's the vendor-side retention policy, where in the architecture does DLP (Data Loss Prevention — the layer that blocks leakage) sit?
35. **Do we have an EU AI Act checklist ready for high-risk clients (medtech, fintech, critical infrastructure)?** Human-oversight evidence, traceability from spec → PR → merge, audit document package?
36. **How do we document "creative human modification" for copyright?** Is there tooling that diffs agent output against the merged commit and produces an audit artifact, or is this still a manual process?

---

## Group 8: Commercial Model

37. **What does the SOW (Statement of Work — the contractual document that defines scope and obligations) look like for a typical engagement?** Fixed-fee, T&M (Time and Material — pay-as-you-go billing), managed delivery with outcome SLA (Service Level Agreement)?
38. **Is there a rate card for weighted time?** (Skill level × AI provisioning tier) → $/day. How do we justify the AI-native premium vs. traditional T&M?
39. **How do we treat the client team's time investment in the contract?** Fixed scope, cap, or estimate?
40. **How is ROI breakeven calculated?** Formula, assumptions, which underlying data?
41. **What's the playbook if the client doesn't reach the claimed metrics?** Diagnostic checklist (spec quality, model choice, codebase, team), retry pattern during the final retrospective phase, SLA / money-back terms?
