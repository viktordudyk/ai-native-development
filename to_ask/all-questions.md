# Full Question Set for the Team — AI SDLC Practice

**Context:** Principal-dev-level questions grouped by topic. Covers methodology, infrastructure, team, governance, security, and commercial model. No references to internal artifacts or confidential metrics.

---

## Group 1: Offering Scope & Engagement Structure

1. **Which AI SDLC Practice packages are sell-ready today, and which are still under construction?** Do we have a short diagnostic matrix — *client maturity / stack / team size → recommended package* — across Assessment, Introduction, Uplift, Module-based, Native Transition, and Brownfield?
2. **What artifacts does the client receive at each engagement phase (Clarify, Calibrate, Launch, Refine)?** Assessment report, capability map, target operating model, roadmap, ROI model — who writes which, in what order, and where are the key milestone checks?
3. **What's the minimum AI SDLC engagement we offer?** Standalone Assessment, single-team pilot, full roll-out — what's the entry threshold so we can scope first conversations correctly?

---

## Group 2: Methodology — SDD, Behavior Layers, Control Gates

4. **Spec Driven Development (the methodology where the specification is the primary artifact and code is derivative) — what's the spec format, and how do specs live alongside code?** EARS-style (Easy Approach to Requirements Syntax — *"When [condition], the system shall [action]"*), custom DSL (Domain-Specific Language), structured Markdown? How do specs handle ambiguity, edge cases, and non-functional requirements (performance, security, accessibility)? Are specs versioned in the same repo as code, who's the formal owner (PM, Tech Lead, AI), and is there a CI gate (Continuous Integration check that runs before merge) that flags spec/code divergence?
5. **Agent configuration layers (project rules, AGENTS.md, ARCHITECTURE.md, IDE-specific rules) — how do they compose?** What's the precedence order when layers conflict? How do we verify that a rules change doesn't silently alter agent behavior on existing tasks (regression testing on the rules themselves)?
6. **HITL gates (Human-In-The-Loop — mandatory human-approval checkpoints) — taxonomy and degradation protection.** Which classes of change cannot be merged without human approval (DB migrations — schema changes, security-sensitive code, public API, infra changes)? Is there a distinction between plan-mode approval and agent-mode approval? How do we keep HITL gates from degrading into rubber-stamp approvals — metrics on review depth and time-to-approve, minimum review SLA, escalation patterns?
7. **Brownfield SDD: do we generate specs from existing code (reverse SDD)?** If yes — how do we validate that the generated specs reflect real intent rather than accidental behavior? How do we handle undocumented invariants (things that work by convention but are written down nowhere)?

---

## Group 3: Quality & Evidence

8. **Eval methodology — what do we have beyond "tests are green," and how do we measure escape rate?** Eval harness (the test rig used to score agents), property-based testing (tests with randomized inputs), mutation testing (deliberate code changes to grade test strength), semantic equivalence (verifying that two implementations behave identically)? How do we catch silent failures (the code passes tests but doesn't fulfill the spec)? What's the defect escape rate (% of defects that made it to production) at 30 / 60 / 90 days post-merge?
9. **Evidence base: which version of the case study can we show publicly?** Pre-NDA (before a Non-Disclosure Agreement is signed), post-NDA, or invitation-only sessions?
10. **How was the baseline measured methodologically?** Stopwatch, surveys, GitHub / Jira telemetry, or estimate? If the reported improvement is a range, is that variance across sub-teams (iOS / Android / Web / Backend) or across task types within a single team?
11. **How do we attribute impact to AI tooling specifically vs. Hawthorne effect (improvement caused by the act of being observed) vs. new process vs. new stack?**

---

## Group 4: Infrastructure & Models

12. **Model Selection — is the router (the component that picks a model per task) IDE-native or a custom proxy / gateway?** What are the routing rules (by task type, complexity, sensitivity, cost budget)?
13. **Token Protection and Cost Monitoring — what's actually under the hood?** Rate limiting, budget caps, secrets scrubbing (stripping secrets from prompts before they hit the model)? How do we attribute cost-per-merged-PR back to specific tasks?
14. **Memory via Model Context Protocol (the standard for connecting agents to external systems) — how is the agent's persistent memory architected?** What's stored (decisions, learnings, context references), what's the storage backend, retention / eviction policy (rules for purging old entries)? How do we avoid contamination (context leaking between projects or clients)?
15. **How do multiple MCPs compose at runtime?** Does the agent pick the MCP itself or is it hard-coded in the prompt? Latency, timeout, failure handling — if one MCP doesn't respond, what happens? Security model — does each MCP have its own scope, or are credentials shared?
16. **Frontier API vs. self-hosting TCO — where's the crossover point?** Do we have an internal eval matrix across Claude Opus 4.7 / GPT-5 / Gemini 3 / Qwen / DeepSeek with pass rate and cost-per-task? At what monthly token volume does a self-hosted GPU cluster (H100 / H200 / Blackwell with vLLM or SGLang — LLM inference servers) beat per-token API pricing, including amortization and ops headcount?
17. **How do we navigate model changes?** When a vendor ships a new version or deprecates a SKU — what's the regression suite, the canary process, how much rework hits client specs? Are our prompts and specs portable across vendors?

---

## Group 5: Tooling & Portability

18. **IDE portability — which methodology pieces are stack-portable vs. IDE-specific?** AGENTS.md (emerging community standard) and MCPs (protocol-level) port across; project rules, modes (ask / plan / agent), prompt-standards UI are IDE-specific. What percentage of the methodology transfers as-is across Cursor, Claude Code (Anthropic's CLI), Codex CLI (OpenAI's CLI), Copilot, and Windsurf?
19. **How is the MCP set organized across the client's stack layers?** iOS (XcodeBuild, simulator), Android (Gradle, Compose), Web (browser DevTools, Playwright), Backend (Spring / .NET / Node debugger), Data (dbt, Airflow), Infra (Kubernetes, Terraform), Observability (Sentry, Datadog) — are these ready packages, or assembled ad-hoc per client?

---

## Group 6: Team Composition & Multi-Team Governance

20. **Pilot-team shape and Tech Lead role.** Optimal headcount, ratio of engineers to concurrent agent sessions, which legacy roles (juniors, dedicated QA, manual testers) get absorbed, retrained, or moved out of scope? How does the Tech Lead / Architect role change — how many parallel agents can one senior realistically supervise before quality collapse (degraded output when supervision capacity is overloaded)?
21. **New roles — jobs or skills?** Spec Author, AI Architect, Context Engineer (the engineer who owns convention files, rules, MCP configs), Agent Supervisor (the human watching parallel agent sessions) — distinct hires or skills layered on existing seniors?
22. **Where does QA go if the agent writes and runs its own tests?** Spec review (reviewing specs before implementation), adversarial testing (hunting edge cases, weak spots, security flaws), eval authoring (writing the test rigs that grade agents), or gradual headcount reduction?
23. **Who owns central artifacts across many teams, and how are they shared?** Specification templates, conventions, MCP integrations — Center of Excellence (CoE — the strategic R&D layer), Guild (cross-team horizontal community of practitioners), or a dedicated Platform team (the group that owns internal tooling and integrations)? How do reusable specs, prompts, and agent rules get shared — internal marketplace (an artifact marketplace with a review process), shared Git, hub repo of convention files, package registry — and who validates quality before publication?
24. **How do we prevent "dialects of SDD" (methodology drift between teams)?** If teams pick different CLIs / IDE agents and share a monorepo (single Git repo containing multiple projects) or shared services, what governance keeps artifacts compatible?

---

## Group 7: Security, Data, Compliance

25. **What does the data flow look like when a client engineer pastes production code into an agent?** What gets stripped, what gets logged, what hits the frontier endpoint, what's the vendor-side retention policy, where in the architecture does DLP (Data Loss Prevention — the layer that blocks leakage) sit?
26. **Do we have an EU AI Act checklist ready for high-risk clients (medtech, fintech, critical infrastructure)?** Human-oversight evidence, traceability from spec → PR → merge, audit document package?
27. **How do we document "creative human modification" for copyright?** Is there tooling that diffs agent output against the merged commit and produces an audit artifact, or is this still a manual process?

---

## Group 8: Commercial Model

28. **SOW shape, rate card, and client time investment.** What does the SOW (Statement of Work — the contractual document that defines scope and obligations) look like for a typical engagement — fixed-fee, T&M (Time and Material — pay-as-you-go billing), managed delivery with outcome SLA (Service Level Agreement)? Is there a rate card for weighted time (skill level × AI provisioning tier) → $/day, and how do we justify the AI-native premium vs. traditional T&M? How do we treat the client team's time investment in the contract — fixed scope, cap, or estimate?
29. **How is ROI breakeven calculated?** Formula, assumptions, which underlying data?
30. **What's the playbook if the client doesn't reach the claimed metrics?** Diagnostic checklist (spec quality, model choice, codebase, team), retry pattern during the final retrospective phase, SLA / money-back terms?
