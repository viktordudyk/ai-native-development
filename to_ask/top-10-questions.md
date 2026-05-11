# Top 10 Questions for the Team — AI SDLC Practice

**Context:** Principal-dev-level questions to clear before going to market with AI SDLC Practice. Engineering-first tone, focused on real architecture and methodology rather than marketing positioning.

---

## Group 1: Offering Scope

**1. Which AI SDLC Practice packages are sell-ready today, and how do we pick the right one per client?**
Do we have a short diagnostic matrix — *client maturity / stack / team size → recommended package* — to confidently navigate between Assessment, Introduction, Uplift, Module-based, Native Transition, and Brownfield on a first call? Which packages are truly delivery-ready right now, and which are still under construction?

---

## Group 2: Methodology — SDD & Behavior Layers

**2. Spec Driven Development (the methodology where the specification is the primary artifact and code is derivative) — what is our actual specification format, and how do specs live alongside code?**
EARS-style (Easy Approach to Requirements Syntax — the *"When [condition], the system shall [action]"* format), custom DSL (Domain-Specific Language — a language tailored to the problem), or structured Markdown? How are specs versioned with code, how do we handle ambiguity, edge cases, and non-functional requirements (performance, security, accessibility)? Is there a CI gate (Continuous Integration check that runs before merge) that flags spec/code divergence?

**3. Agent configuration layers (project rules, AGENTS.md, ARCHITECTURE.md, IDE-specific rules) — how do they compose?**
What is the precedence order when layers conflict? How do we verify that a rules change doesn't silently alter agent behavior on existing tasks (regression testing on the rules themselves)? In a monorepo (single Git repo containing multiple projects) shared by many teams — is there one global file or per-package files, and who owns it?

**4. HITL gates (Human-In-The-Loop — mandatory human-approval checkpoints) — where exactly do they fire, and by what taxonomy?**
Which classes of change cannot be merged without human approval (DB migrations — schema changes, security-sensitive code, public API, infra changes)? How do we keep HITL gates from degrading into rubber-stamp approvals — are there metrics on review depth and time-to-approve? Is there a distinction between plan-mode approval (agent presents the plan) and agent-mode approval (agent executes autonomously)?

---

## Group 3: Quality & Evidence

**5. Eval methodology (how we measure the quality of generated code) — what do we have beyond "tests are green"?**
Eval harness (the test rig used to score agents) — what does it look like, what does it measure? Property-based testing (tests with randomized inputs to verify invariants), mutation testing (deliberate code changes to grade test strength), semantic equivalence (verifying that two implementations behave identically)? How do we catch silent failures (the code compiles and tests pass, but it doesn't fulfill the spec)? What's the defect escape rate (% of defects that made it to production) at 30 / 60 / 90 days post-merge?

**6. Evidence base: which version of the case study can we show publicly, and how was the baseline actually measured?**
Which materials — pre-NDA (before a Non-Disclosure Agreement is signed), post-NDA, or invitation-only sessions? How was *"before"* methodologically measured — stopwatch, surveys, GitHub / Jira telemetry, or estimate? If the reported improvement is a range, is that variance across sub-teams (iOS / Android / Web / Backend) or across task types within a single team? If part of the prompt library has measured savings and part doesn't, how do we present that honestly? How do we attribute impact to AI tooling specifically vs. Hawthorne effect (improvement caused by the act of being observed) vs. new process vs. new stack?

---

## Group 4: Infrastructure & Models

**7. AI infrastructure: model selection + token/cost protection + memory and MCP composition + frontier-vs-self-hosted TCO.**
Router (the component that picks a model per task) — is it IDE-native or a custom proxy / gateway? Routing rules (by task type, complexity, sensitivity, cost budget)? Token Protection — is this rate limiting, budget caps, or secrets scrubbing (stripping secrets from prompts before they hit the model)? Cost Monitoring — how do we attribute cost-per-merged-PR back to specific tasks? Memory via Model Context Protocol (the standard for connecting agents to external systems) — what's stored (decisions, learnings, context refs), what's the backend, retention / eviction policy (rules for purging old entries), and how do we avoid contamination (context leaking between projects or clients)? Multi-MCP composition — does the agent pick the MCP itself or is it hard-coded in the prompt, and how are latency / timeout / failure handled? Do we have an internal eval matrix across Claude Opus 4.7 / GPT-5 / Gemini 3 / Qwen / DeepSeek with pass rate and cost-per-task? At what monthly token volume does a self-hosted GPU cluster (H100 / H200 / Blackwell with vLLM or SGLang — LLM inference servers) beat per-token API pricing, including amortization and ops headcount?

---

## Group 5: Tooling & Portability

**8. IDE portability of the methodology, and the MCP stack across different layers of the client's tech stack.**
Which artifacts are stack-portable (AGENTS.md — an emerging community standard, MCPs — protocol-level) and which are IDE-specific (project rules, modes — ask / plan / agent, prompt-standards UI)? If the client uses Cursor, Claude Code (Anthropic's CLI), Codex CLI (OpenAI's CLI), Copilot, or Windsurf — what percentage of the methodology transfers as-is? How is the MCP set organized across stack layers: iOS (XcodeBuild, simulator), Android (Gradle, Compose), Web (browser DevTools, Playwright), Backend (Spring / .NET / Node debugger), Data (dbt, Airflow), Infra (Kubernetes, Terraform), Observability (Sentry, Datadog) — are these ready packages, or assembled ad-hoc per client?

---

## Group 6: Team Composition & Multi-Team Governance

**9. Client AI-native team composition and governance across many teams.**

*Pilot-team shape:* what's the optimal headcount and ratio of engineers to concurrent agent sessions? Which legacy roles (juniors, dedicated QA, manual testers) get absorbed, retrained, or moved out of scope? Are the new roles — Spec Author, AI Architect, Context Engineer (the engineer who owns convention files, rules, MCP configs), Agent Supervisor (the human watching parallel agent sessions) — distinct hires or skills layered on existing seniors? How does the Tech Lead / Architect role change — how many parallel agents can one senior realistically supervise before quality collapse (degraded output when supervision capacity is overloaded)? Where does QA go if the agent writes and runs its own tests — spec review (reviewing specs before implementation), adversarial testing (hunting edge cases, weak spots, security flaws), eval authoring (writing the test rigs that grade agents), or gradual headcount reduction?

*Multi-team governance:* who owns central specification templates, conventions, and MCP integrations on the client side — a Center of Excellence (CoE — the strategic R&D layer), a Guild (cross-team horizontal community of practitioners), or a dedicated Platform team (the group that owns internal tooling and integrations)? How do we prevent "ten dialects of SDD" (methodology drift between teams) — if teams pick different CLIs / IDE agents and share a monorepo or shared services, what governance keeps artifacts compatible? How do reusable specs, prompts, and agent rules get shared between teams — internal marketplace (an artifact marketplace with a review process), shared Git, a hub repo of convention files, or a package registry — and who validates quality before publication? Which cross-team sync cadence actually works (weekly demos, AI guild meetings, shared retros) vs. which ones turned out to be theatre (ceremony with no impact on quality or velocity)?

---

## Group 7: Commercial Model

**10. SOW for a typical engagement, treatment of the client's time investment, and how we defend the ROI claim.**
SOW (Statement of Work — the contractual document that defines scope and obligations) — fixed-fee, T&M (Time and Material — pay-as-you-go billing), or managed delivery with outcome SLA (Service Level Agreement)? How do we treat the client team's time investment in the contract — fixed scope, cap, or estimate? How is ROI breakeven calculated — formula, assumptions, which underlying data? What's the playbook if the client doesn't reach the claimed metrics — diagnostic checklist, retry pattern during the final retrospective phase, or SLA / money-back terms?

