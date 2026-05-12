# 10 questions for the team on AI SDLC practice

Stuff we need to figure out before we start selling. Dev talk, no marketing.

---

## Scope

**1. Which packages can we actually sell right now?**

We have Assessment, Introduction, Uplift, Module-based, Native Transition, Brownfield. Which ones are ready to ship and which are still half-built? Do we have any kind of cheat sheet for picking one based on client maturity, stack, team size?

---

## Methodology

**2. SDD. What does our spec look like, and where does it live?**

EARS, custom DSL, plain markdown? How do we version specs with code? What happens with ambiguity, edge cases, perf, security, a11y? Any CI check that yells when the spec and the code don't match anymore?

**3. Agent config layers (project rules, AGENTS.md, ARCHITECTURE.md, IDE rules). How do they stack?**

What wins when they conflict? How do we make sure tweaking the rules doesn't quietly break stuff that used to work? In a monorepo with many teams: one file or per-package, and who owns it?

**4. HITL gates. Where do they fire?**

What can't be merged without a human (DB migrations, security stuff, public API, infra)? How do we stop them from turning into rubber stamps? Do we measure review depth or time to approve? Plan-mode approval vs agent-mode approval, same or different?

---

## Quality

**5. Evals. What do we have beyond "tests pass"?**

What does our eval harness do? Property-based, mutation, semantic equivalence? How do we catch the case where it builds, tests are green, but it doesn't do what the spec says? Defect escape rate at 30, 60, 90 days?

**6. Case study. What can we show, and how did we measure "before"?**

Pre-NDA, post-NDA, invite only? How did we get the baseline number: stopwatch, surveys, git/Jira data, gut feel? If the win is a range, is that variance between iOS, Android, Web, Backend, or between task types in one team? Parts of the prompt library that don't have numbers, how do we talk about those without lying? How do we tell apart "AI helped" from Hawthorne effect, new process, new stack?

---

## Infra and models

**7. AI infra: model picking, cost/token protection, memory and MCPs, frontier vs self-hosted.**

Router. IDE-native or our own proxy? Rules by task type, complexity, sensitivity, budget?

Token protection. Rate limits, budget caps, secrets scrubbing before stuff hits the model?

Cost monitoring. Can we tie cost to a merged PR?

Memory over MCP. What goes in there, where does it live, how long, how do we keep one client's context from leaking into another's?

Multi-MCP. Does the agent pick the MCP or do we hardcode it? What happens on timeout or failure?

Internal eval across Opus 4.7, GPT-5, Gemini 3, Qwen, DeepSeek with pass rate and cost per task, do we have one? And at what monthly token volume does a self-hosted box (H100, H200, Blackwell on vLLM or SGLang) actually beat API pricing once you count ops?

---

## Tooling

**8. IDE portability and MCP stack across the client's stack.**

What's portable (AGENTS.md, MCPs) and what's tied to one IDE (project rules, ask/plan/agent modes, prompt UI)? Client on Cursor, Claude Code, Codex CLI, Copilot, Windsurf, how much of our methodology survives the switch?

How do we organize MCPs across the stack: iOS (XcodeBuild, simulator), Android (Gradle, Compose), Web (DevTools, Playwright), Backend (Spring, .NET, Node debuggers), Data (dbt, Airflow), Infra (k8s, Terraform), Observability (Sentry, Datadog)? Ready bundles or do we build it from scratch every time?

---

## Team and governance

**9. Team shape on the client side, and governance across many teams.**

Pilot team. How many people, how many parallel agent sessions per dev? Which old roles get absorbed or retrained (juniors, dedicated QA, manual testers)? The new roles (Spec Author, AI Architect, Context Engineer, Agent Supervisor): real hires or just hats on top of seniors? How does the Tech Lead role change, how many agents can one senior babysit before quality tanks? If the agent writes and runs its own tests, where does QA go: spec review, adversarial testing, writing evals, or just smaller team?

Multi-team governance. Who owns the central spec templates, conventions, MCP integrations on the client side: CoE, Guild, Platform team? How do we stop "ten flavors of SDD" if teams pick different CLIs and share a monorepo? How do specs, prompts, rules get shared between teams (internal marketplace, shared git, hub repo, package registry), and who reviews before publishing? Which cross-team rituals actually work (weekly demos, AI guild, shared retros) and which were just theatre?

---

## Commercial

**10. SOW, client's own time, defending ROI.**

SOW shape: fixed fee, T&M, or managed delivery with an SLA on outcome? How do we treat the client team's own time in the contract: fixed scope, cap, estimate? ROI breakeven: formula, assumptions, what data? What's the playbook if the client doesn't hit the numbers we promised: diagnostic, retry during the closing retro, money back?
