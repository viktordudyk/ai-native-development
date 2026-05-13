# Answers — Research & Frameworks for Top 10 Questions

Each file answers one of the top 10 questions an engineering lead asks before adopting AI SDLC practice — current (2025-2026) best practices, concrete tooling, and cited sources. Internal-only questions (#1, #6) ship as frameworks to collect the answer from the team rather than research.

| # | File | Type | Topic | Highlights |
|---|------|------|-------|------------|
| 1 | [01-packages-readiness.md](01-packages-readiness.md) | Internal framework | Package sell-readiness & selection diagnostic | Readiness matrix template, client-to-package decision questions |
| 2 | [02-sdd-format.md](02-sdd-format.md) | Research | SDD spec format options | EARS vs Markdown templates vs OpenSpec DSL vs Constitution+spec; Spec Kit / Kiro / OpenSpec layouts; drift prevention |
| 3 | [03-agent-config-layers.md](03-agent-config-layers.md) | Research | Agent configuration composition | AGENTS.md under Linux Foundation; per-IDE precedence; monorepo router pattern; Promptfoo for rules regression |
| 4 | [04-hitl-gates.md](04-hitl-gates.md) | Research | HITL gates & anti-rubber-stamp | 8-class taxonomy by blast radius × reversibility; Cursor/Claude Code/Devin/Aider/GitHub comparison; EU AI Act Article 14 |
| 5 | [05-eval-methodology.md](05-eval-methodology.md) | Research | Eval beyond green tests | 8-layer eval stack; SWE-bench Pro / LiveCodeBench / RepoBench; mutation testing thresholds; Defect Escape Rate methodology |
| 6 | [06-evidence-base.md](06-evidence-base.md) | Internal framework | Public case study & baseline defense | 4-tier disclosure matrix; baseline-methodology checklist; range vs point-estimate framing |
| 7 | [07-ai-infrastructure.md](07-ai-infrastructure.md) | Research | Model selection, MCP, TCO | Per-token pricing 2026; gateway comparison (OpenRouter / LiteLLM / Portkey); SGLang vs vLLM throughput; self-hosting break-even |
| 8 | [08-ide-portability-mcps.md](08-ide-portability-mcps.md) | Research | IDE portability & MCP catalog | 8 IDE × 6 feature matrix; full MCP catalog per stack layer with links; ecosystem gaps; ~75% methodology portability |
| 9 | [09-team-governance.md](09-team-governance.md) | Research | Team shape & multi-team governance | Pilot 3-5 seniors; TL supervision ceiling 12-20 agents; CoE/Guild/Platform thresholds; real headcounts (Cursor, Replit, Klarna) |
| 10 | [10-commercial-model.md](10-commercial-model.md) | Research | SOW shape, pricing, ROI defense | 67% buyer fixed-fee preference; AI-native senior premium 20-40%; defensible 3.6 hrs/wk ROI; fee-at-risk vs money-back norms |

---

## How to read these

- **Research files (Q2-5, Q7-10):** drawn from public 2025-2026 sources, cite via markdown links. Treat them as a starting position — validate against our actual practice and current model/tool versions before quoting on a client call.
- **Internal frameworks (Q1, Q6):** the answer must come from our team. The file lays out *what to ask* and *what NOT to accept as an answer*.

---

## What's missing (deliberately)

- **Confidential metrics** from our internal case study — not used in any research-side question.
- **Proprietary tooling stack** — neither named nor implied. Examples in answers use public tool names as illustrations.
- **Sales positioning** — the research is technical, not commercial framing. For sales angles, see the commercial-model file (10-commercial-model.md) and the sales pitch at [results/01-sales-pitch.md](../../results/01-sales-pitch.md).
