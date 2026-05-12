# Q3: Agent Configuration Layers — Composition & Precedence

By May 2026 the ecosystem has converged on **AGENTS.md** as the portable baseline, with each agent still reading its native file when present. There is no cross-tool merge engine: each agent walks its own resolution path. The job for a consulting team is to (a) pick a canonical file, (b) understand the nested-directory rule each tool applies, and (c) avoid contradictions between layers a tool actually loads. AGENTS.md was formalised in August 2025 by OpenAI, Google, Cursor, Factory and Sourcegraph and donated to the Linux Foundation's Agentic AI Foundation in December 2025; ~60k+ public repos now ship one.

## Rule systems across IDEs (current state)

| Tool | Native file(s) | AGENTS.md support | Nested-dir rule | Notes |
|---|---|---|---|---|
| **Claude Code** | `CLAUDE.md`, `.claude/CLAUDE.md`, `.claude/rules/*.md` (YAML `paths:` frontmatter), `~/.claude/CLAUDE.md`, managed policy file | No — workaround: `@AGENTS.md` import or symlink | Ancestor + subdir CLAUDE.md files are **concatenated**, not overridden; subdir files load on-demand | Managed policy > project > user > local; `claudeMdExcludes` to skip noisy ancestors in monorepos |
| **Cursor** | `.cursor/rules/*.mdc` (frontmatter: Always / Auto-Attached glob / Agent-Requested / Manual); legacy `.cursorrules` deprecated | Yes (alongside `.cursor/rules`) | Team > Project > User; nested AGENTS.md merges, closer wins on conflict | `.mdc` adds glob-scoped activation that AGENTS.md lacks |
| **OpenAI Codex CLI** | `AGENTS.md` (+ `AGENTS.override.md` local) | Native primary | Closest file to edited path wins; explicit chat prompt overrides all | Origin tool for AGENTS.md |
| **GitHub Copilot** | `.github/copilot-instructions.md`, `.github/instructions/*.instructions.md` (with `applyTo:` glob), `AGENTS.md` (since Aug 2025) | Native | Path-scoped `.instructions.md` apply by glob; AGENTS.md from root, cwd, or `COPILOT_CUSTOM_INSTRUCTIONS_DIRS` | 2-page recommended cap for `copilot-instructions.md` |
| **Windsurf** | `.windsurfrules` (root) or `.windsurf/rules/*.md`; global rules file | Yes | Project rules override global | Workflows precedence: System > Workspace > Global > Built-in |
| **Aider / Zed / Jules / Gemini CLI / Amp / RooCode / Factory / Warp** | — | Native AGENTS.md | Closest-wins | Treat AGENTS.md as the only file |
| **GitLab Duo Agent Platform** | `AGENTS.md` (root + nested) | Native | Closest-wins | |

Out-of-band conventions: **`ARCHITECTURE.md`** (matklad pattern, 2021) for the codemap, and **`CONTRIBUTING.md`** for human workflow. Neither is auto-loaded by agents — pull them in explicitly via `@ARCHITECTURE.md` imports inside AGENTS.md/CLAUDE.md when relevant.

## Composition pattern that actually works

1. **One canonical file: AGENTS.md at repo root.** Keep it under ~150 lines. Lead with commands (build, test, lint), then conventions, then "do not touch" boundaries, then example workflows. The most consistent finding from GitHub's 2,500-repo study is that command-led, procedurally-structured 100-150 line files outperform long prose by 10-15%.
2. **Claude Code bridge.** Either `ln -s AGENTS.md CLAUDE.md` or a one-line `CLAUDE.md` containing `@AGENTS.md` plus any Claude-only addenda. `/init` in newer Claude Code versions reads AGENTS.md, `.cursorrules`, and `.windsurfrules` and composes them.
3. **Tool-specific files only for tool-specific concerns** — Cursor `.mdc` glob activation, Copilot `.instructions.md` `applyTo:` scoping, Claude `.claude/rules/*.md` with `paths:` frontmatter. Do not duplicate content already in AGENTS.md; cross-reference instead.
4. **Nested AGENTS.md per package in monorepos.** Root AGENTS.md is a *router* ("for emails read `@emails/AGENTS.md`"). Per-package files own package-specific rules. Closest file wins for every supported tool. Real example: the `openai/codex` repo ships 88 nested AGENTS.md.
5. **Local overrides:** `AGENTS.local.md` and `CLAUDE.local.md` (gitignored) for personal preferences.

## Precedence cheat-sheet (when multiple files load inside one tool)

- **Claude Code:** managed policy > project (root) > nested project > user (`~/.claude/`) > local. Concatenated, not filtered — conflicts resolved arbitrarily, so audit `/memory` output.
- **Cursor:** Team > Project (`.cursor/rules` + AGENTS.md) > User; closer nested AGENTS.md beats parent.
- **Codex / Copilot / Windsurf / GitLab Duo:** closest-to-edited-file wins; explicit chat prompt always wins.

## Real OSS examples

- `openai/codex` — canonical multi-section AGENTS.md plus 88 nested files: <https://github.com/openai/codex/blob/main/AGENTS.md>
- `promptfoo/promptfoo` — AGENTS.md plus `.claude/skills/`: <https://github.com/promptfoo/promptfoo/blob/main/AGENTS.md>
- `temporalio/sdk-python` (Temporal SDK) — production-grade build/test conventions in AGENTS.md
- Curated lists: [`Ischca/awesome-agents-md`](https://github.com/Ischca/awesome-agents-md), [`danielrosehill/awesome-agents-md`](https://github.com/danielrosehill/awesome-agents-md), [`noahbald/awesome-architecture-md`](https://github.com/noahbald/awesome-architecture-md)

## Regression testing rules changes

Treat AGENTS.md/CLAUDE.md edits like prompt changes: version them and run an eval suite on PR. **Promptfoo** is the de-facto tool — YAML config, deterministic assertions (`equals`, `contains`, `is-json`), LLM-as-judge for fuzzier checks, `--compare baseline/eval-results.json --fail-on-regression` in CI, and an official `promptfoo-action` for GitHub Actions. Recommended gates: task success ≥ baseline, tool-selection accuracy ≥ 0.9, cost/latency thresholds, `--repeat 3` for known-flaky prompts. A handful of fixed coding tasks (refactor, add test, fix bug) run against the configured agent gives a regression signal in minutes.

## What to clarify with the team

1. **Canonical file** — are we standardising on AGENTS.md for every client engagement, with CLAUDE.md as an import-only bridge? Mixed shops pay a sync tax.
2. **Ownership in monorepos** — does each package team own its `AGENTS.md`, or is there a central docs owner? Mercari's model is collective + AI-assisted updates on PR.
3. **What lives in `ARCHITECTURE.md` vs. AGENTS.md** — codemap and invariants in ARCHITECTURE.md (refreshed quarterly), operational/agent rules in AGENTS.md. Avoid duplication.
4. **Eval harness ownership** — who maintains the promptfoo suite, and what is the budget (USD/run) we accept per PR?
5. **Cursor `.mdc` and Copilot `.instructions.md`** — do we adopt path-scoped rules, or stay AGENTS.md-only for portability?

## Sources

- [AGENTS.md spec & site](https://agents.md/)
- [agentsmd/agents.md repo](https://github.com/agentsmd/agents.md)
- [InfoQ — AGENTS.md emerges as open standard (Aug 2025)](https://www.infoq.com/news/2025/08/agents-md/)
- [OpenAI Codex — Custom instructions with AGENTS.md](https://developers.openai.com/codex/guides/agents-md)
- [Anthropic — Claude Code memory (CLAUDE.md hierarchy)](https://code.claude.com/docs/en/memory)
- [GitHub Docs — Adding repository custom instructions for Copilot](https://docs.github.com/copilot/customizing-copilot/adding-custom-instructions-for-github-copilot)
- [Cursor — .mdc rules reference](https://github.com/sanjeed5/awesome-cursor-rules-mdc/blob/main/cursor-rules-reference.md)
- [Windsurf — rules docs (.windsurfrules / .windsurf/rules/)](https://docs.windsurf.com/windsurf/cascade/workflows)
- [GitLab Docs — AGENTS.md customization files](https://docs.gitlab.com/user/duo_agent_platform/customize/agents_md/)
- [GitHub Blog — How to write a great agents.md (lessons from 2,500 repos)](https://github.blog/ai-and-ml/github-copilot/how-to-write-a-great-agents-md-lessons-from-over-2500-repositories/)
- [Datadog Frontend Dev — Steering AI agents in monorepos with AGENTS.md](https://dev.to/datadog-frontend-dev/steering-ai-agents-in-monorepos-with-agentsmd-13g0)
- [Mercari Engineering — Taming agents in the Mercari web monorepo](https://engineering.mercari.com/en/blog/entry/20251030-taming-agents-in-the-mercari-web-monorepo/)
- [matklad — ARCHITECTURE.md (2021)](https://matklad.github.io/2021/02/06/ARCHITECTURE.md.html)
- [DeployHQ — CLAUDE.md, AGENTS.md & Copilot instructions guide](https://www.deployhq.com/blog/ai-coding-config-files-guide)
- [Promptfoo — Evaluate coding agents](https://www.promptfoo.dev/docs/guides/evaluate-coding-agents/)
- [Promptfoo — CI/CD integration](https://www.promptfoo.dev/docs/integrations/ci-cd/)
- [openai/codex — root AGENTS.md](https://github.com/openai/codex/blob/main/AGENTS.md)
- [promptfoo/promptfoo — AGENTS.md](https://github.com/promptfoo/promptfoo/blob/main/AGENTS.md)
