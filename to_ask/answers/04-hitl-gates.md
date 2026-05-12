# Q4: HITL Gates — Taxonomy & Degradation Protection

Human-In-The-Loop (HITL) gates are mandatory approval checkpoints inserted between an autonomous coding agent and an irreversible side effect (a merge, a migration, a deploy). By 2025–2026 the question is no longer *whether* to insert them — every serious agent shipped a permission model — but *where* to insert them so they catch real harm without collapsing into rubber-stamping. EU AI Act Article 14 turned this from a hygiene concern into a legal one for any high-risk system: the deployer must be able to monitor, interpret, and override agent output, and must remain explicitly aware of *automation bias* ([artificialintelligenceact.eu](https://artificialintelligenceact.eu/article/14/)).

## Taxonomy — change class → required gate

| Change class | Reversibility | Minimum gate | Typical mechanism |
|---|---|---|---|
| DB schema migration (prod) | Low | Two-person review + staging dry-run + manual prod approval | Policy-as-code (e.g. Atlas), CODEOWNERS on `/migrations`, change-advisory pause ([atlasgo.io](https://atlasgo.io/blog/2026/04/29/policy-as-code)) |
| Security-sensitive code (auth, crypto, IAM) | Low | Required reviewer from security CODEOWNER team | GitHub ruleset "require review from specific team" ([github.blog](https://github.blog/changelog/2025-11-03-required-review-by-specific-teams-now-available-in-rulesets/)) |
| Public API / contract change | Medium-Low | Plan-mode approval + reviewer + consumer notification | Cursor/Devin plan checkpoint + API-owner CODEOWNER |
| Infrastructure / IaC | Low | Plan output approval + restricted apply role | Agent runs `terraform plan`; human approves `apply` |
| Dependency upgrade (major / transitive new license) | Medium | SCA gate + reviewer; auto-approve allowed only for patch with green tests | Auto-approval bots for low-risk PRs ([ona.com](https://ona.com/stories/auto-approving-low-risk-prs)) |
| Dependency upgrade (patch, locked range) | High | Required CI green; reviewer optional | Renovate auto-merge with branch protection |
| Refactor / formatting / typo | High | None beyond branch protection CI | Agent ships PR |
| Destructive shell (`rm`, `DROP`, `force-push`) | None | Per-call approval, never bypassed | Claude Code `canUseTool` / deny rules ([docs.anthropic.com](https://docs.anthropic.com/en/docs/claude-code/sdk/sdk-permissions)) |

Principle: gate by *blast radius and reversibility*, not by line count.

## Platform comparison

| Tool | Plan-mode gate | Per-tool approval | Bulk-yes escape hatch | Notes |
|---|---|---|---|---|
| **Claude Code** | `plan` mode (Shift-Tab cycles default → acceptEdits → plan); `ExitPlanMode` requires confirmation | `canUseTool` callback, allow/deny rules, PreToolUse hooks | `bypassPermissions` (containers only) | Six modes incl. `dontAsk`, `readOnly`; processing order Hook → Deny → Allow → Ask → Mode → `canUseTool` ([platform.claude.com](https://platform.claude.com/docs/en/agent-sdk/permissions)) |
| **Cursor** | `Ask` (read-only) / `Plan` (writes Markdown plan file, editable) / `Agent` | File edit & shell confirmations; "Build in Parallel" requires split-plan approval | Per-command allowlist | Recommended flow: plan in Ask, implement in Agent ([cursor.com](https://cursor.com/docs/agent/plan-mode)) |
| **Devin (Cognition)** | Planning Checkpoint (mandatory) | Inside session sandbox | — | Two non-negotiable gates: Plan + PR ([devin.ai/agents101](https://devin.ai/agents101)) |
| **Aider** | None native | Per-edit / per-shell prompts | `--yes-always` (alias `--yes`, env `AIDER_YES`) | Granular `--yes-file` / `--yes-lint` requested but not shipped ([aider.chat](https://aider.chat/docs/config/options.html)) |
| **GitHub (merge layer)** | — | Branch protection, CODEOWNERS, required reviewers, merge queue | Bypass list (admins only) | CODEOWNERS treated as hard prereq when merge queue is on — bypass does not apply at queue entry ([docs.github.com](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)) |

## Anti-degradation: detect rubber-stamping before it ships a CVE

Faros / GitHub data shows AI-heavy teams merge 98% more PRs but PR review time rises 91% — reviewers face volume their attention can't absorb ([atomicrobot.com](https://atomicrobot.com/blog/ai-review-fatigue/), [molten.bot](https://molten.bot/blog/agent-approval-fatigue/)). Track these as leading indicators of degradation:

- **Time-to-approve vs. PR size/complexity** — flat or falling time on rising complexity = automation bias.
- **Comment density per review** — questions asked / lines changed; declining trend is the canonical rubber-stamp signal.
- **Late-day approval defect rate** — post-17:00 approvals correlate with higher escaped-bug rate.
- **Reviewer rotation / concentration** — % of agent PRs approved by top-3 reviewers; cap concentration.
- **Bug escape rate** vs. test-coverage trend — if both rise together, review is theatre.
- **Override rate** — how often the human disagrees with the agent. Near-zero = the human has stopped reading ([pmc.ncbi.nlm.nih.gov](https://pmc.ncbi.nlm.nih.gov/articles/PMC10857587/)).

Structural countermeasures: per-reviewer daily PR caps; mandatory cooldowns between approvals; rotate CODEOWNERS quarterly; pair-review on high-blast-radius classes (Article 14's biometric-style "two competent persons" rule, generalised to migrations and prod IaC); explainability requirement — the agent must surface *why* a change is safe, not just *what* it changed ([sloanreview.mit.edu](https://sloanreview.mit.edu/article/ai-explainability-how-to-avoid-rubber-stamping-recommendations/)). The Robodebt and Toeslagenaffaire post-mortems are the cautionary baseline: humans formally signed off on hundreds of thousands of decisions they neither understood nor could override ([raphaelnagel.com](https://www.raphaelnagel.com/human-in-the-loop-automation-bias)).

## Sources

- [EU AI Act, Article 14 — Human Oversight](https://artificialintelligenceact.eu/article/14/)
- [Claude Code — Handling Permissions (Agent SDK)](https://docs.anthropic.com/en/docs/claude-code/sdk/sdk-permissions)
- [Claude Agent SDK — Configure permissions](https://platform.claude.com/docs/en/agent-sdk/permissions)
- [Cursor — Plan Mode docs](https://cursor.com/docs/agent/plan-mode)
- [Cursor — Agent best practices](https://cursor.com/blog/agent-best-practices)
- [Devin — Coding Agents 101 (Plan + PR checkpoints)](https://devin.ai/agents101)
- [Aider — Options reference (`--yes-always`)](https://aider.chat/docs/config/options.html)
- [GitHub Docs — About protected branches](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches)
- [GitHub Changelog — Required review by specific teams in rulesets (Nov 2025)](https://github.blog/changelog/2025-11-03-required-review-by-specific-teams-now-available-in-rulesets/)
- [Atlas — Policy as Code for Database Migrations](https://atlasgo.io/blog/2026/04/29/policy-as-code)
- [Ona — Auto-approving low-risk PRs cut lead time 74%](https://ona.com/stories/auto-approving-low-risk-prs)
- [Atomic Robot — AI Review Fatigue](https://atomicrobot.com/blog/ai-review-fatigue/)
- [Molten.Bot — The Agent Approval Fatigue Problem](https://molten.bot/blog/agent-approval-fatigue/)
- [PMC — Putting a human in the loop: increasing uptake, decreasing accuracy](https://pmc.ncbi.nlm.nih.gov/articles/PMC10857587/)
- [MIT SMR — AI Explainability: How to Avoid Rubber-Stamping](https://sloanreview.mit.edu/article/ai-explainability-how-to-avoid-rubber-stamping-recommendations/)
- [Raphael Nagel — Human in the Loop & Automation Bias: The Oversight Facade](https://www.raphaelnagel.com/human-in-the-loop-automation-bias)
