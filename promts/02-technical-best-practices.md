# 2. Technical Best Practices — AI-Native Development Playbook

> **Result:** [results/02-technical-best-practices.md](../results/02-technical-best-practices.md)

## Prompt

Create a comprehensive technical best practices guide for AI-native software development — a practical playbook that convinces engineering teams through concrete examples, measurable impact, and proven patterns rather than hype.

### Context & Purpose

This document is meant for **hands-on engineers, tech leads, and architects** who need to see real evidence before adopting new practices. It should work as:
- A reference handbook teams can adopt incrementally
- A convincing artifact backed by specific examples and metrics
- A bridge between "I've heard of AI coding" and "here's exactly how to do it well"
- **A business case builder** — give technical leaders the numbers and arguments they need to justify adoption to their organization

### Target audience
Senior developers, tech leads, and architects who are skeptical but open-minded. They want patterns they can try today, not theory.

### Structure requirements

The guide should cover the following sections, each with **concrete examples, before/after comparisons where applicable, and measured or estimated impact**:

#### 1. Getting Started — First 30 Minutes
- Tooling setup: which IDE + agent combination to pick and why (Copilot in VS Code, Cursor, Claude Code CLI)
- First productive task: have the reader accomplish something real in their codebase within 30 minutes
- Three quick wins to try immediately: generate tests, generate documentation from code, refactor with explanation
- What to expect vs what not to expect
- The mindset shift: from "write code for me" to "execute this well-specified task and verify"

#### 2. Specification-Driven Development (SDD) in Practice
- Why "code is no longer the primary artifact — the specification is"
- The 4-phase SDD lifecycle: Description → Decomposition → Validation → Living updates
- EARS syntax explained with real examples ("When [condition], the system Shall [action]")
- Atomic task decomposition: keeping units to 1k-3k lines that fit model context
- The 50/50 rule: invest ~50% time in spec → get ~10x implementation acceleration
- Document hierarchy: PRD → Technical Design → AI Execution Spec
- Storage: specs in Git as markdown + Mermaid diagrams (not images — they bloat context)
- Before/after example: a real feature built with vague prompt vs SDD approach

#### 3. Context Engineering — The Make-or-Break Skill
- "If knowledge isn't written down in the codebase or a Markdown file, it doesn't exist for the agent"
- Fresh and condensed context > dumping everything
- Project conventions files (.instructions.md, AGENTS.md, copilot-instructions.md) — what to put in them, with full examples
- Skills and agent customization: Anthropic Skills, custom agent modes — encoding team-specific workflows as reusable, shareable agent configurations
- Incremental disclosure: let the agent self-discover rather than preload
- Parallel multi-agent pass: separate agents for analysis / planning / verification
- Extended thinking / reasoning modes: when to enable deep reasoning for complex architecture decisions vs fast mode for routine tasks
- The "second brain" pattern: storing domain knowledge in markdown + RAG

#### 4. Agent Workflow Patterns (With Step-by-Step Examples)
- The standard 5-step agent loop: Define → Plan → Propose diffs → Local checks → Wrap-up
- Autonomy modes matched to task size: Inline (small edits) → Editor agent (multi-file) → Async agent (long tasks)
- Multi-agent coordination for large features
- AI-assisted code review: using agents to review PRs (detect logic errors, security issues, style violations, missed edge cases) — not just write code
- Documentation generation: auto-generate API docs, README updates, architecture decision records from code changes
- When NOT to use an agent: small tight-loop edits, high-security code, learning a new API
- Real example walkthrough: implementing a feature end-to-end with an agent

#### 5. MCP Integration — Connecting Agents to Your Stack
- What MCP is and why it matters (Model Context Protocol)
- Practical integrations with impact:
  - Jira/Confluence: auto-fetch context, update status
  - GitHub: read contracts (OpenAPI/Protobuf), detect breaking changes, create PRs
  - Figma: extract design specs → generate component skeletons
  - Database: seed data, validate state
  - Browser: navigate, validate UI, check DOM/console/network
- Setup effort: typically 1-2 minutes per integration
- Security: least privilege, explicit approvals for destructive ops, PII filtering, prompt-injection defense

#### 6. Testing & Validation — AI Makes TDD Practical
- Per-test specification: name/preconditions/steps/expectations/mocks
- Growth pattern: happy path → negative scenarios → edge cases
- Cost curve: first test expensive, subsequent tests progressively cheaper
- Agentic self-validation: tests for backend, screenshots for frontend, ADB for mobile
- Property-based testing from EARS requirements
- Impact: "100% test coverage becomes the standard because the main pain of TDD — writing tests — is removed"

#### 7. Code Organization for AI Readability
- DDD + AI synergy: clear contracts + ubiquitous language = reliable agent output
- Clarity over cleverness: unambiguous names, bounded contexts, explicit > magical
- Module separation: strong cohesion + loose coupling = agent context stays small
- Anti-patterns that break agents: spaghetti code, unclear names, overloaded prompts, magic abstractions

#### 8. Security & Safety Practices
- Corporate licenses only — no personal accounts for company code
- Treat autonomous agents decisions with maximum scrutiny
- MCP security model: OAuth/OIDC, least privilege, sandbox writes, audit trails
- License risk: LLMs may generate code resembling non-compliant licenses
- Prompt injection defense
- What "full autonomy" actually means in April 2026 (bounded + well-specified atoms, not sci-fi)

#### 9. Measurable Impact — Real Numbers
- Code generation: ~10% of traditional time
- Review effort: comparable or less than average developer output
- Test creation: radical cost reduction
- Token costs: ~$1,000/month at API rates vs ~$100-300/month consumer plan (vendor subsidized at ~10x)
- The pricing dependency risk: what happens when subsidies end
- Local model lag: 12+ months behind frontier service models

#### 10. Tool Comparison Matrix
- GitHub Copilot: strengths, weaknesses, best for...
- Cursor: strengths, weaknesses, best for...
- Claude Code (CLI): strengths, weaknesses, best for...
- Windsurf, Gemini CLI, OpenAI Codex: brief notes
- Recommendation matrix by team size / skill level / use case

#### 11. Working with Legacy Code — The Highest-Impact Use Case
- Why legacy modernization is the killer app for AI-native practices
- Pattern: Read → Understand → Document → Refactor → Test (the agent does all 5)
- Before/after: taking an undocumented 2000-line legacy module and making it maintainable
- Generating tests for untested legacy code as a safety net before refactoring
- Extracting documentation and architecture knowledge from old codebases
- Migration patterns: framework upgrades, language migrations, API version bumps
- Impact metrics: legacy refactoring tasks that took weeks now take days

#### 12. Team Adoption Roadmap — From First Use to Organization-Wide
- Phased rollout plan:
  - **Week 1:** Individual exploration — pick a tool, try quick wins (tests, docs, simple features)
  - **Month 1:** Team conventions — create conventions files, establish review practices, first SDD specs
  - **Quarter 1:** Process integration — MCP integrations, SDD as default process, compound engineering flywheel
  - **Quarter 2+:** Organization-wide — shared specs, cross-team conventions, metrics tracking
- How SDD fits into existing Scrum/Kanban (spec writing in refinement, agent execution in sprint)
- How standups/reviews change: "I wrote the spec, the agent implemented, I reviewed"
- Training plan: what skills engineers need to develop (spec writing, context engineering, prompt design, review)
- Measuring adoption success: velocity metrics, coverage metrics, developer satisfaction

#### 13. Compound Engineering — The Flywheel Effect
- The 5th pillar: every spec, convention, MCP integration, and skills file accumulates as organizational knowledge capital
- Each project gets faster: conventions refine, specs become templates, agent behavior improves
- Knowledge compounding: new team members onboard faster because the codebase is self-documenting
- Competitive moat: organizations that start compounding now build an accelerating advantage
- Concrete example: Month 1 vs Month 6 — same task, radically different speed

#### 14. ROI & Business Case — Justifying Adoption
- Cost-benefit framework for presenting to leadership
- Payback calculation: tooling cost vs developer time saved (show the math)
- Headcount efficiency: not "replace developers" but "deliver more with the same team"
- Time-to-market: from feature request to production — before and after
- Quality improvements: defect reduction from better test coverage and spec-driven clarity
- Risk reduction: less tribal knowledge dependency, better documentation, faster onboarding
- The pricing reality: true costs vs subsidized costs, and how to plan for both
- Comparison: cost of NOT adopting (competitor acceleration, talent retention risk)

#### 15. Common Objections & Rebuttals
- "AI writes bad code" → Quality is a function of input quality; SDD + review produces consistently good output
- "We'll lose engineering skills" → Engineers shift UP the stack; spec writing and architecture become more important, not less
- "It's just hype / another fad" → Show the numbers: 10x code gen, TDD cost elimination, actual adoption curves
- "Security risk" → Address with enterprise plans, review gates, and MCP security model
- "Our codebase is too complex / special" → Legacy modernization is actually the highest-impact use case
- "We tried Copilot and it wasn't impressive" → Autocomplete ≠ agentic; the real value is in SDD + agent workflows, not inline suggestions
- "Developers won't adopt it" → Start with tests and docs (universal pain points), not core feature work

### Tone & style
- Technically precise, no marketing language
- Opinionated where the data supports it — the reader wants recommendations, not "it depends"
- Use tables, bullet lists, and code/spec examples liberally
- Reference external learning resources where relevant:
  - https://www.anthropic.com/learn/build-with-claude (agents, MCP, skills, prompt engineering, evals)
  - https://www.aihero.dev/ (Matt Pocock's AI engineering courses — Claude Code, practical workflows)
  - https://modelcontextprotocol.io/ (MCP documentation)
  - https://www.anthropic.com/engineering/building-effective-agents (Anthropic's effective agents guide)

### Length
Comprehensive — aim for a thorough reference document (~5000-7000 words). Quality and depth over brevity. Each section should be self-contained enough to be useful on its own. The document should work both as a sequential read AND as a reference where teams can jump to any section independently.
