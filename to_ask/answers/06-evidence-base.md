# Q6: Evidence Base — Public Case Study & Baseline Methodology

This question is **internal** — it requires Practice / Marketing / Legal to define what we can publish and how we defended our measurements. Research won't answer it. This file is a framework for getting a clean answer from the team.

---

## Part 1: What materials are publishable, and to whom

Capture per-artifact disclosure tier. The standard four-tier model:

| Tier | Audience | What we can show |
|------|----------|------------------|
| **Public / no NDA** | Cold outreach, conference talks, website | Sanitized one-pager, no client names, no specific numbers tied to industry/scale |
| **Pre-NDA, gated** | Qualified prospect, before legal | High-level metrics with ranges, no client identification |
| **Post-NDA** | Mutual NDA signed | Detailed metrics, methodology, sub-team breakdowns |
| **Invitation-only** | Closed reference call | Direct conversation with the case study customer |

A clean answer is: every slide / asset we have, with the tier label attached and named owner approving disclosure.

Ask Marketing / Legal for:
- The current **sanitized one-pager** (or commit to producing one)
- The **NDA template** and the trigger to send it
- The **reference-customer list** and who can authorize a reference call

---

## Part 2: Baseline measurement methodology — how we defend the numbers

Any quoted improvement (cycle time reduction, throughput lift, defect rate change, navigation overhead drop) needs four backing facts. Without them, an experienced CTO can take the claim apart on a first call.

| Question | What we need on record |
|----------|------------------------|
| **What was the "before"?** | Specific baseline period, measurement method (stopwatch, surveys, telemetry, estimate), sample size |
| **What changed?** | Tooling, process, team composition, codebase scope — separated so we know what to attribute |
| **How long did we observe "after"?** | Duration of the post-period and stability of the metric over time |
| **What was the variance?** | Std dev or range, by sub-team or task type — a single point estimate is suspicious |

The two attack vectors to expect:

- **Hawthorne effect** — improvement caused by the act of being observed, not by the tooling. Defense: keep a control group, or report metrics from a stable post-period after the novelty has worn off.
- **Confounded variables** — was it AI tooling, or also a new process, new stack, new team members hired at the same time? Defense: list every change, name what's attributable to AI specifically.

A clean answer is: for each headline metric, a one-paragraph "how we measured" note that a senior engineer could reproduce on their own org.

---

## Part 3: Range vs. point estimates

If our public number is a range (e.g., *"X-Y% improvement"*), be ready to explain on the first call:

- Is the range across **sub-teams** (iOS / Android / Web / Backend behaved differently)?
- Across **task types** (some workflows benefited more than others)?
- Across **engagement phases** (early adoption vs steady state)?

Saying "between X and Y" without explaining the source of variance is a credibility hole.

---

## Part 4: Acceptable "we don't know yet"

If for some metrics we genuinely don't have post-period data, the honest answer is *"this metric was estimated by the team rather than instrumented"* — and we mark it as such in the deck. This is fine for a first call. **Quietly omitting unmeasured metrics from a slide where they appear alongside measured ones is not fine** — it conflates evidence levels.

Ask the team: for each artifact in the prompt library / workflow inventory, which entries are *instrumented* vs *team-estimated*? Mark them visibly.

---

## Output format for the team's answer

Three artifacts:

1. **Disclosure matrix** — every public-facing asset × tier × approving owner
2. **Baseline-methodology note** per headline metric (one paragraph each)
3. **Measured vs estimated tag** on every per-workflow / per-prompt time saving claim

Until these exist, every technical call risks a credibility hit on the first 15 minutes.

---

## What NOT to do

- Cite a single point estimate without range or methodology
- Conflate measured and estimated improvements in the same table
- Show the full case study deck pre-NDA — even by accident in a Zoom share
- Promise the same metrics for a different client context without saying *"this is what we saw in a comparable setting"*
