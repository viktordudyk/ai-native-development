# Slide 8 — "AI-Native SDLC" (~3 min)

> ⭐ **The "this is real" slide — walk through the diagram deliberately. This is where theory becomes an operational system.**
>
> This slide has more depth than the others. Take your time. Use the diagram as a visual anchor — point to each element as you discuss it.

---

## Speech

This is what we've built — the AI-Native Software Development Lifecycle. Not a concept — an operational workflow we run in production. We've developed blueprints and agentic configurations on this exact pattern that generate working product from structured requirements.

---

### Part 1: The Three Context Layers (~45 sec)

> *Point to the three boxes at the top of the diagram*

Let me walk you through how we've structured this, starting at the top — the **three context layers**. This is critical. Models are still context-limited. That's the fundamental constraint. So we manage context deliberately — not dumped in randomly. If you just write flat markdown requirement files, it becomes unmanageable almost immediately — there are simply too many requirements for a model to process coherently.

That's why we structure context into three tiers:

- **Persistent context** — the problem and solution canvas, engineering guidelines, brand guidelines, user demographics, product templates. Things that never change sprint to sprint.
- **System context** — feature-product dependencies, data model, architecture, APIs. The structural skeleton.
- **Feature-product context** — the specific problem statement, acceptance scenarios, and requirements for what you're building right now.

> **Key point to emphasize:** In our approach, context management is not optional — it's the primary engineering discipline. Get this wrong and everything downstream degrades.

---

### Part 2: The Agent Flow (~1 min)

> *Walk clockwise through the agents starting from bottom-left*

**Step 1 — Spec Agent** (human co-authoring). This is where we start. It defines the feature-product boundary, the unit-of-value architecture, transforms the prompt into a structured model, and scores for ambiguity, completeness, and consistency. We don't hand this off blindly — our architects own the spec, the agent structures it.

**Step 2 — Architecture Agent.** Stack and framework selection, architecture-defining constraints, APIs, data models. We use this to feed the structural decisions into the pipeline.

**Step 3 — Test Design Agent** (human co-authoring). This is where we see most teams get it wrong. In our workflow, it generates acceptance tests from requirements, E2E and integration tests, and proof-of-concept tests.

> **Pause here and deliver this with conviction:**

Notice the order we enforce: **tests come from requirements, before a single line of code is written.** Not the other way around. The popular advice of "requirements → code → unit tests on code" is fundamentally backwards. We test the spec, not the implementation.

**Step 4 — Code & Review & Test.** Autonomous code generation, code review, test execution. This is where we let the agent do the heavy lifting.

**Step 5 — Observability.** Readiness reports, consumption metrics, monitoring. We need visibility into what's happening at every stage.

And underpinning everything — the **Orchestration Agent** that we use to coordinate the entire flow.

---

### Part 3: The Car/OEM Analogy (~30 sec)

> *This analogy makes the abstract concrete — use it confidently*

Here's how we think about it. A modern car at an OEM service center — they don't repair individual parts. They replace the entire module. We apply the same principle to software. We build it so that modules can be regenerated entirely from spec. If a module breaks, we don't debug it — we regenerate it.

---

### Part 4: Human Gates (~30 sec)

> *Point to the red-dashed "Human co-authoring" boxes and the Production Approval Gate*

But notice the red-dashed boxes — this is deliberate. We keep **Human co-authoring** on the Spec Agent and Test Design Agent. And we enforce a **Production Approval Gate** with human sign-off — readiness report check, stats and metrics analysis, risk assessment, and a 5-minute smoke test.

We insist on these human gates for multiple reasons. IP ownership requires human creative decisions. Quality assurance requires human judgment. And practically — we always preserve the last known-good production build as a safety net.

This is not full autonomy. This is what we propose: **structured autonomy with human control at the critical junctures**.

---

## Delivery Notes

### Pacing
- This slide is ~3 min, the longest in the deck. It's justified — this is the operational core.
- If time-constrained: skip Part 3 (the car analogy) — saves ~30 sec.
- If audience is very technical: spend extra time on the test-before-code point; they'll push back productively.

### Gestures
- Physically point to the diagram as you walk through each agent. Don't just talk — guide eyes.
- When you say "tests come from requirements, before a single line of code" — pause, look at the audience.

### Anticipated Questions
| Question | Answer |
|----------|--------|
| "What tools run the spec agent?" | This pattern is tool-agnostic. Amazon Kiro, Claude Code with custom agents, Cursor with rules — all implement variants. The pattern matters more than the tool. |
| "How do you handle specs that are too large?" | That's exactly why context is tiered. The feature-product context is scoped. You never dump the entire system into one agent call. Task atoms stay at 1k–3k lines. |
| "What if the test agent generates wrong tests?" | That's why it's human co-authored. The human validates acceptance criteria. The agent structures and expands them. Garbage-in-garbage-out still applies — the spec quality is the control. |
| "Where does CI/CD fit?" | The Code & Review & Test step feeds directly into your existing CI/CD pipeline. The orchestration agent triggers it. Nothing replaces your deployment infrastructure. |
| "Why not let the agent do everything?" | You can — for demos and prototypes. For production, you lose IP protection (no human creative modification), you lose quality control, and you lose your safety net. The human gates exist for legal, quality, and practical reasons. |

### CEO Insights (Background Context)
These points from the CEO informed this slide's narrative:
1. **Blueprints already exist** — production-ready agentic configurations that generate products from requirements
2. **Context is the real challenge** — models are context-limited; context must be managed in structured tiers (persistent, system, per-feature)
3. **Task breakdown must be delegated carefully** — you can hand full breakdown to the model, but then you lose control over the core asset: the product's unit-of-value architecture
4. **Car/OEM analogy** — don't repair, replace the module; software must be built for regeneration
5. **Flat requirements don't scale** — without structured context layers, requirements become unmanageable
6. **Tests from requirements, not from code** — the popular "requirements → code → unit tests" sequence is wrong
7. **Human gates are non-negotiable** — for IP, quality, and last-known-good production preservation
