# Anthropic "Build with Claude" — Developer Resources Reference

**Source:** [anthropic.com/learn/build-with-claude](https://www.anthropic.com/learn/build-with-claude) and related developer documentation  
**Compiled:** March 2026  
**Purpose:** Consolidated reference of Anthropic's official developer guidance for building with Claude — agents, tools, prompting, skills, and best practices.

---

## Table of Contents

1. [Building Effective Agents](#1-building-effective-agents)
2. [Model Context Protocol (MCP)](#2-model-context-protocol-mcp)
3. [Prompt Engineering](#3-prompt-engineering)
4. [Extended Thinking](#4-extended-thinking)
5. [Agent Skills](#5-agent-skills)
6. [Tool Use](#6-tool-use)
7. [Claude Code](#7-claude-code)
8. [Evaluations & Testing](#8-evaluations--testing)
9. [Agentic System Best Practices](#9-agentic-system-best-practices)
10. [Migration & Model Selection](#10-migration--model-selection)
11. [Quick Reference Links](#11-quick-reference-links)

---

## 1. Building Effective Agents

**Source:** [anthropic.com/engineering/building-effective-agents](https://www.anthropic.com/engineering/building-effective-agents)

### Key distinction: Workflows vs Agents

- **Workflows:** LLMs and tools orchestrated through predefined code paths. You hard-code the control flow; the LLM handles content generation within each step.
- **Agents:** LLMs dynamically direct their own processes and tool use. The model decides which steps to take, in what order, and when it's done.

**Recommendation:** Start with the simplest solution. Only increase complexity (from direct LLM calls → workflows → agents) when demonstrably needed.

### Five workflow patterns

| Pattern | How it works | Best for |
|---------|-------------|----------|
| **Prompt chaining** | Break task into sequential steps; output of step N feeds step N+1. Gate on intermediate quality. | Tasks with clear sequential stages — e.g., generate outline → write draft → review |
| **Routing** | Classify input first, then route to specialized handler/prompt for each category. | Distinct task types that need different handling — support tickets, content types |
| **Parallelization** | Run multiple LLM calls simultaneously. Two variants: *sectioning* (split task into independent pieces) and *voting* (same task, multiple runs, aggregate). | Independent subtasks, tasks where consensus improves accuracy |
| **Orchestrator-workers** | A central LLM dynamically breaks down a task and delegates subtasks to worker LLMs. | Complex tasks where subtasks aren't predictable in advance — multi-file code changes |
| **Evaluator-optimizer** | One LLM generates a response, another evaluates it, loop until good. | Tasks with clear quality criteria — code that must pass tests, translations needing review |

### When to use agents

Use agents for **open-ended problems** where the number of steps is hard to predict and you can't hard-code a fixed path. Key requirements:
- Ground truth from environment (test results, API responses, user feedback)
- Stopping conditions to prevent runaway loops
- Human feedback checkpoints for critical decisions

### Three core design principles

1. **Maintain simplicity** — Don't over-engineer. The best agent architecture is often the simplest one that works.
2. **Prioritize transparency** — Show the model's planning, tool use, and reasoning. Users need to see and verify.
3. **Carefully craft the Agent-Computer Interface (ACI)** — Tool design matters as much as prompt design: clear names, helpful descriptions, format that minimizes errors (think about poka-yoke for tools).

### ACI design tips

- Prefer formats natural to the LLM (e.g., markdown diffs over JSON patches)
- Give the model tokens to "think" before acting
- Absolute paths > relative paths to prevent ambiguity
- Reduce surface area of what can go wrong

---

## 2. Model Context Protocol (MCP)

**Source:** [modelcontextprotocol.io/introduction](https://modelcontextprotocol.io/introduction)

### What it is

MCP is an **open-source standard** for connecting AI assistants to external data sources and tools — databases, APIs, file systems, dev tools. Think of MCP as a **USB-C port for AI**: one standardized connection instead of custom integrations for every tool.

### What it enables

- AI assistants that connect to your Calendar, Notion, Figma, databases, 3D printers — through one protocol
- Replace fragmented N×M integrations (N models × M tools) with a unified protocol
- Tools built once work across any MCP-compatible host

### Why it matters

| For... | Benefit |
|--------|---------|
| **Developers** | Build once against a standard protocol; tool works everywhere |
| **AI apps** | Access a growing ecosystem of pre-built integrations |
| **End users** | AI assistants that actually know about your systems |

### Ecosystem support

MCP is supported by: **Claude, ChatGPT, VS Code, Cursor**, Windsurf, and a growing list of hosts and tool providers.

- **Spec & docs:** [modelcontextprotocol.io](https://modelcontextprotocol.io/)
- **Ready-made servers:** [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)

---

## 3. Prompt Engineering

**Source:** [platform.claude.com — Prompting Best Practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)

### General principles

| Principle | Key guidance |
|-----------|-------------|
| **Be clear and direct** | Think of Claude as a brilliant but new employee without context. Show your prompt to a colleague — if they'd be confused, Claude will too. |
| **Add context** | Explain *why* you want something, not just what. Motivation helps the model target better. |
| **Use examples** | 3–5 diverse examples wrapped in `<example>` tags dramatically improve accuracy and consistency. |
| **Structure with XML tags** | `<instructions>`, `<context>`, `<input>` tags reduce misinterpretation. Use consistent, descriptive names. |
| **Give a role** | Even one sentence in the system prompt ("You are a helpful coding assistant specializing in Python") focuses behavior. |
| **Long context** | Put documents at the top, query at the bottom (up to 30% quality improvement). Use `<document>` tags. Ask Claude to quote relevant parts first. |

### Output and formatting control

- Claude 4.6 models are more concise by default — request summaries if you want visibility into reasoning after tool calls.
- **Format steering:** Use positive instructions ("Write in flowing prose") rather than negatives ("Don't use markdown"). Match your prompt's formatting style to your desired output.
- Claude Opus 4.6 defaults to LaTeX for math — add explicit text-only formatting instructions if you need plain text.
- **Prefilled responses deprecated** in Claude 4.6 — use explicit format instructions instead.

### Tool use tips

- Be explicit about when to act vs suggest: "implement changes" vs "suggest changes" produces very different behavior.
- Claude 4.6 is more responsive to system prompts than prior models — dial back aggressive language like "CRITICAL: You MUST use this tool" to simply "Use this tool when...".
- **Parallel tool calling** is excellent in Claude 4.6 — add a `<use_parallel_tool_calls>` instruction to boost to ~100% parallel execution.

---

## 4. Extended Thinking

**Source:** [platform.claude.com — Extended Thinking](https://platform.claude.com/docs/en/build-with-claude/extended-thinking)

### What it is

Extended thinking gives Claude enhanced reasoning — the model creates internal `thinking` blocks that work through a problem step-by-step before responding. Results in better answers for complex tasks.

### Two modes

| Mode | How it works | Best for |
|------|-------------|----------|
| **Adaptive thinking** (`type: "adaptive"`) | Claude decides when and how much to think. Use with `effort` parameter (low/medium/high/max). | **Recommended for Opus 4.6.** Agentic workflows, multi-step tool use, bimodal workloads. |
| **Manual thinking** (`type: "enabled"`, `budget_tokens: N`) | You set a fixed token budget for thinking. | Sonnet 4.6 and earlier models. Predictable token usage. |

### Interleaved thinking

With tool use, Claude can think **between** tool calls — reason about results before deciding next steps. Automatically enabled with adaptive thinking on Opus 4.6; requires `interleaved-thinking-2025-05-14` beta header on Sonnet 4.6 and earlier models.

### Supported models

- **Claude Opus 4.6** — adaptive thinking only (manual mode deprecated)
- **Claude Sonnet 4.6** — both adaptive and manual with interleaved mode
- **Claude Opus 4.5, 4.1, 4 / Sonnet 4.5, 4 / Haiku 4.5** — manual extended thinking

### Best practices

- Start with minimum budget (1,024 tokens) and increase incrementally.
- For complex tasks, start at 16k+ tokens.
- For budgets above 32k, use batch processing to avoid networking timeouts.
- Claude may not use the full budget — especially above 32k.
- Guide thinking with prompts like: *"After receiving tool results, carefully reflect on their quality before proceeding."*
- To reduce overthinking: *"Choose an approach and commit to it. Avoid revisiting decisions unless new information directly contradicts your reasoning."*

### Pricing note

You're charged for **full thinking tokens generated**, not the summary shown in responses (Claude 4 models return summarized thinking by default). Set `display: "omitted"` to skip streaming thinking tokens for faster time-to-first-text with no quality loss.

---

## 5. Agent Skills

**Source:** [platform.claude.com — Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) | [Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

### What Skills are

Skills are **reusable, filesystem-based capability packages** that give Claude domain-specific expertise: workflows, instructions, and optional code. Unlike prompts (one-time instructions), Skills load on-demand and eliminate repeating the same guidance across conversations.

### Three-level progressive disclosure

| Level | When loaded | Token cost | Contains |
|-------|------------|------------|----------|
| **L1: Metadata** | Always (at startup) | ~100 tokens/skill | `name` and `description` from YAML frontmatter |
| **L2: Instructions** | When skill triggers | <5k tokens | SKILL.md body — workflows, best practices |
| **L3: Resources** | As needed | Unlimited effectively | Bundled files, scripts, reference docs (loaded via bash, not into context) |

The key insight: Skills can include **unlimited bundled content** (reference docs, scripts, data) with zero context penalty until actually accessed.

### Where Skills work

| Surface | Pre-built | Custom | Notes |
|---------|----------|--------|-------|
| **Claude API** | Yes | Yes (via Skills API `/v1/skills`) | Organization-wide sharing |
| **Claude Code** | No | Yes (filesystem `SKILL.md`) | Auto-discovered |
| **Agent SDK** | No | Yes (`.claude/skills/`) | Auto-discovered |
| **Claude.ai** | Yes (behind scenes) | Yes (zip upload, Settings > Features) | Per-user only |

### Pre-built Skills

Available out of the box: **PowerPoint** (pptx), **Excel** (xlsx), **Word** (docx), **PDF** (pdf).

### SKILL.md structure

```yaml
---
name: your-skill-name    # max 64 chars, lowercase + hyphens only
description: Brief description of what this Skill does and when to use it
---

# Your Skill Name

## Instructions
[Clear, step-by-step guidance]

## Examples
[Concrete usage examples]
```

### Authoring best practices

- **Concise is key** — Claude is already smart. Only add context it doesn't already have.
- **Set degrees of freedom** — High (general guidance) for flexible tasks, low (exact scripts) for fragile operations.
- **Keep SKILL.md under 500 lines** — split into separate files if longer.
- **One-level-deep references** — SKILL.md → reference files directly; avoid deep nesting.
- **Workflows with checklists** — for complex multi-step operations, include copy-paste checklists.
- **Feedback loops** — run validator → fix → repeat pattern greatly improves quality.
- **Evaluation-driven development** — build evals BEFORE writing extensive documentation.
- **Iterative development with Claude** — work with one Claude instance to create/refine skill, test with another.

### Security

Use Skills only from trusted sources. Skills can direct Claude to execute code and invoke tools — a malicious skill could exfiltrate data or perform unauthorized operations. Thoroughly audit third-party skills.

---

## 6. Tool Use

**Source:** [platform.claude.com — Tool Use](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)

### Two types

| Type | Execution | You implement? | Examples |
|------|-----------|---------------|----------|
| **Client tools** | On your systems | Yes | Custom functions you define, computer use, text editor |
| **Server tools** | On Anthropic's servers | No | Web search, web fetch |

### Client tool flow

1. You define tools (name, description, input schema) + send user prompt
2. Claude decides to use a tool → returns `tool_use` block
3. You execute the tool, return `tool_result` block
4. Claude uses results to formulate final response

### MCP integration

MCP tools work with Claude's API — convert `inputSchema` to `input_schema` (simple rename). Or use the **MCP Connector** to connect to remote MCP servers directly from the Messages API without building a client.

### Structured outputs

Add `strict: true` to tool definitions for **guaranteed schema validation** — Claude's tool calls always match your schema exactly. Essential for production agents.

### Pricing

Tool use is priced like regular API requests (input + output tokens). Each tool definition adds tokens to input. Server tools (web search) may have additional per-use charges.

---

## 7. Claude Code

**Source:** [code.claude.com/docs](https://code.claude.com/docs)

### What it is

An **agentic coding tool** that reads your codebase, edits files, runs commands, and integrates with development tools. Available in terminal, IDE, desktop app, and browser.

### Install

```bash
# macOS, Linux, WSL
curl -fsSL https://claude.ai/install.sh | bash

# Then in any project:
cd your-project
claude
```

Also available via Homebrew and WinGet. Auto-updates in background.

### Capabilities

- Build features and fix bugs
- Create commits and pull requests
- Connect tools with MCP
- Customize with instructions, skills, and hooks
- Run agent teams and build custom agents
- Pipe, script, and automate with CLI

### Multi-surface availability

Terminal CLI, VS Code extension, JetBrains plugin, Desktop app, Web — all connect to the same Claude Code engine. CLAUDE.md files, settings, and MCP servers work across all surfaces.

### Extended integrations

| Use case | Surface |
|----------|---------|
| Continue session from mobile | Remote Control / Web / iOS app |
| Automate PR reviews & issue triage | GitHub Actions / GitLab CI/CD |
| Code review on every PR | GitHub Code Review |
| Route bugs from Slack to PRs | Slack integration |
| Debug live web apps | Chrome extension |
| Build custom agent workflows | Agent SDK |

---

## 8. Evaluations & Testing

**Source:** [platform.claude.com — Define Success Criteria](https://platform.claude.com/docs/en/test-and-evaluate/develop-tests)

### Define success criteria first

Before prompt engineering, establish:
1. Clear definition of success criteria for your use case
2. Empirical ways to test against those criteria
3. A first draft prompt to improve

Success criteria should be **specific, measurable, achievable, and relevant**. Even "hazy" topics like safety can be quantified: *"Less than 0.1% of outputs flagged for toxicity across 10,000 trials."*

### Common evaluation dimensions

Task fidelity · Consistency · Relevance & coherence · Tone & style · Privacy preservation · Context utilization · Latency · Price

### Eval design principles

1. **Be task-specific** — mirror real-world task distribution, including edge cases
2. **Automate when possible** — multiple-choice, string match, code-graded, LLM-graded
3. **Prioritize volume over quality** — more questions with automated grading > fewer hand-graded

### Grading methods (fastest → most flexible)

| Method | Speed | Nuance | Example |
|--------|-------|--------|---------|
| **Code-based** | Fastest | Low | `output == golden_answer`, `key_phrase in output` |
| **LLM-based** | Fast | High | Claude grades against rubric, outputs score 1-5 |
| **Human** | Slow | Highest | Manual review — avoid at scale |

### LLM grading tips

- Write detailed, clear rubrics with specific pass/fail criteria
- Use empirical scales (1-5, correct/incorrect) not qualitative prose
- Ask the grading LLM to reason before scoring (then discard reasoning)
- Generate test cases with Claude from a baseline set

---

## 9. Agentic System Best Practices

**Source:** [platform.claude.com — Prompting Best Practices: Agentic Systems](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#agentic-systems)

### Long-horizon reasoning & state tracking

Claude 4.6 models excel at long-horizon tasks across multiple context windows. Key patterns:

- **Context awareness** — Claude tracks remaining context window size and manages work accordingly.
- **Multi-window workflows:**
  1. Use the first context window for setup (write tests, create scripts)
  2. Have Claude write tests in structured format (e.g., `tests.json`)
  3. Set up quality-of-life tools (e.g., `init.sh` for servers, test suites, linters)
  4. Starting fresh vs compacting: Claude is effective at discovering state from the local filesystem
  5. Provide verification tools (Playwright MCP, computer use for UI testing)
  6. Encourage complete usage of context

- **State management:** Use JSON for structured data (test results, task status), freeform text for progress notes, git for state tracking across sessions.

### Balancing autonomy and safety

Without guidance, Claude Opus 4.6 may take irreversible actions (delete files, force-push, post to external services). Add explicit guidance:

```
Consider the reversibility and potential impact of your actions. You are 
encouraged to take local, reversible actions like editing files or running 
tests, but for actions that are hard to reverse, affect shared systems, or 
could be destructive, ask the user before proceeding.
```

### Research and information gathering

Claude 4.6 has exceptional agentic search capabilities. For complex research:
- Provide clear success criteria
- Encourage source verification across multiple sources
- Use structured approach: develop competing hypotheses, track confidence levels, self-critique

### Subagent orchestration

Claude 4.6 natively recognizes when to delegate to subagents — but may overuse them. Guide usage:

*"Use subagents when tasks can run in parallel, require isolated context, or involve independent workstreams. For simple tasks, single-file edits, or tasks needing shared context across steps, work directly."*

### Avoiding common pitfalls

| Pitfall | Fix |
|---------|-----|
| **Overthinking** | Lower `effort` setting; prompt: "Choose an approach and commit" |
| **Overeagerness / overengineering** | Prompt: "Only make changes directly requested. Don't add features beyond what was asked." |
| **Excessive file creation** | Prompt: "Clean up temporary files at the end of the task" |
| **Hard-coding test values** | Prompt: "Implement the actual logic, not solutions specific to test inputs" |
| **Hallucination** | Prompt: "Never speculate about code you haven't opened. Read files before answering." |

---

## 10. Migration & Model Selection

**Source:** [platform.claude.com — Prompting Best Practices: Migration](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices#migration-considerations)

### Claude 4.6 key changes

1. **Adaptive thinking** replaces manual `budget_tokens` on Opus 4.6 — use `effort` parameter instead
2. **Prefilled responses deprecated** — use explicit format instructions
3. **More proactive tool use** — dial back aggressive triggering instructions from earlier models
4. **Concise by default** — request summaries explicitly if needed
5. **Better instruction following** — simpler language works; no need for "CRITICAL" / "MUST"

### Sonnet 4.5 → Sonnet 4.6 migration

| Scenario | Recommendation |
|----------|---------------|
| Not using extended thinking | Continue without; set `effort` to appropriate level. `low` effort ≈ Sonnet 4.5 performance. |
| Using extended thinking | Keep `budget_tokens` ~16k. Start at `medium` effort for coding, `low` for chat. |
| Agentic/multi-step workflows | Try adaptive thinking (`type: "adaptive"`) with `high` effort. |
| Computer use agents | Adaptive mode achieved best-in-class accuracy. |

### When to use Opus vs Sonnet

| Use case | Model | Why |
|----------|-------|-----|
| Large-scale code migrations | **Opus 4.6** | Complex, long-horizon reasoning |
| Deep research, extended autonomous work | **Opus 4.6** | Best reasoning quality |
| Fast turnaround, cost efficiency | **Sonnet 4.6** | Optimized speed/cost |
| High-volume production | **Sonnet 4.6** at `low` effort | Fast, cheap, good enough |

### Effort parameter quick guide

```python
client.messages.create(
    model="claude-opus-4-6",
    max_tokens=64000,
    thinking={"type": "adaptive"},
    output_config={"effort": "high"},  # low, medium, high, max
    messages=[{"role": "user", "content": "..."}],
)
```

Set `max_tokens` to 64k at medium/high effort to give the model room to think and act.

---

## 11. Quick Reference Links

### Core documentation
- **Claude API Docs Home:** [platform.claude.com/docs](https://platform.claude.com/docs)
- **Claude Code Docs:** [code.claude.com/docs](https://code.claude.com/docs)
- **MCP Specification:** [modelcontextprotocol.io](https://modelcontextprotocol.io/)

### Guides & Best Practices
- **Prompting Best Practices:** [platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- **Building Effective Agents:** [anthropic.com/engineering/building-effective-agents](https://www.anthropic.com/engineering/building-effective-agents)
- **Extended Thinking:** [platform.claude.com/docs/en/build-with-claude/extended-thinking](https://platform.claude.com/docs/en/build-with-claude/extended-thinking)
- **Agent Skills Overview:** [platform.claude.com/docs/en/agents-and-tools/agent-skills/overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- **Skills Best Practices:** [platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- **Tool Use Overview:** [platform.claude.com/docs/en/agents-and-tools/tool-use/overview](https://platform.claude.com/docs/en/agents-and-tools/tool-use/overview)

### Tutorials & Cookbooks
- **Prompt Engineering Tutorial (GitHub):** [github.com/anthropics/prompt-eng-interactive-tutorial](https://github.com/anthropics/prompt-eng-interactive-tutorial)
- **Prompt Engineering Tutorial (Google Sheets):** [docs.google.com/spreadsheets (Anthropic)](https://docs.google.com/spreadsheets/d/19jzLgRruG9kjUQNKtCg1ZjdD6l6weA6qRXG5zLIAhC8)
- **Extended Thinking Cookbook:** [platform.claude.com/cookbook/extended-thinking](https://platform.claude.com/cookbook/extended-thinking-extended-thinking)
- **Skills Cookbook:** [platform.claude.com/cookbook/skills-notebooks-01-skills-introduction](https://platform.claude.com/cookbook/skills-notebooks-01-skills-introduction)
- **Evals Cookbook:** [platform.claude.com/cookbook/misc-building-evals](https://platform.claude.com/cookbook/misc-building-evals)

### Courses
- **Anthropic Courses:** [anthropic.com/learn](https://www.anthropic.com/learn)
- **Skilljar Courses:** [anthropic.skilljar.com](https://anthropic.skilljar.com/)

### Ecosystem
- **Ready-made MCP Servers:** [github.com/modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers)
- **Claude Agent SDK:** [platform.claude.com/docs/en/agent-sdk/overview](https://platform.claude.com/docs/en/agent-sdk/overview)
- **MCP Connector:** [platform.claude.com/docs/en/agents-and-tools/mcp-connector](https://platform.claude.com/docs/en/agents-and-tools/mcp-connector)
- **Claude Console (Workbench):** [platform.claude.com/dashboard](https://platform.claude.com/dashboard)
