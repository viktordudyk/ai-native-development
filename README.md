# AI-Native Development

A practical guide to AI-native software engineering — from foundational concepts to enterprise adoption.

This repository contains **prompts**, **generated results**, and **reference documentation** for introducing and scaling AI-native development practices.

---

## Prompts & Results

The core of this repo: structured prompts that produce ready-to-use deliverables via frontier AI models.

| # | Prompt | Result | Audience |
|---|--------|--------|----------|
| 1 | [Sales Pitch](promts/01-sales-pitch.md) | [Strategic Sales Guideline](results/01-sales-pitch.md) | CTOs, VPs of Engineering, Tech Leads |
| 2 | [Technical Best Practices](promts/02-technical-best-practices.md) | [Technical Playbook](results/02-technical-best-practices.md) | Senior developers, tech leads, architects |

**How it works:** Each prompt is a detailed specification. Feed it to a frontier model (Claude, GPT, Gemini) and get a polished, evidence-backed document as output. The results are committed here as reference.

---

## Documentation

### Article Series (English)

Practical notes on using AI in software development — cut through hype, focus on real-world usage.

| # | Article | Topic |
|---|---------|-------|
| 0 | [Foreword](docs/00-AI-in-Software-Development-Foreword.md) | Current state of AI in development, why it works, adoption strategies |
| 1 | [AI as Tool #2](docs/01-AI-as-Developer-Tool-Number-Two.md) | Tests, documentation, business logic, synergy with DDD/TDD |
| 2 | [Language Models Basics](docs/02-Basic-Ideas-of-Language-Models.md) | How transformers work, training, why LLMs became capable |
| 3 | [LLM Chat Interaction](docs/03-Basic-Interaction-with-LLM-Through-Chat.md) | Prompting techniques, context management, quality improvement |
| 4 | [From Chat to Agents](docs/04-From-Chat-to-Agents.md) | Autonomous task execution and workflow automation |
| 5 | [Development Agents](docs/05-LLM-Agents-for-Software-Development.md) | Agent tools, capabilities, limitations, real-world workflows |
| 6 | [Agent Extensibility](docs/06-Agent-Extensibility.md) | MCP protocol, tool integration, custom extensions |
| 7 | [Development Processes](docs/07-Bringing-AI-into-Development-Processes.md) | Enterprise adoption, security, tools, team workflows |
| 8 | [Specification Driven Development](docs/08-Specification-Driven-Development.md) | Living specifications, task decomposition, model context management |

### Updates

| Date | Article | Summary |
|------|---------|---------|
| Nov 2025 | [Models & Capabilities Update](docs/09-Update-Models-and-Capabilities-Nov-2025.md) | State of models, quantitative improvements, practical impact |
| Mar 2026 | [AI Automation Update](docs/10-Update-AI-Automation-Spring-2026.md) | Current state of agents, context management, enterprise adoption |

### Warnings

- [Autonomous Agent Tools](docs/11-Warning-Autonomous-Agent-Tools.md) — Risk analysis of highly autonomous agents: accidental errors, prompt injection, legal aspects

### Article Series (Українська)

Ukrainian translations of the article series are in the [`ukr/`](ukr/) folder.

### Reference Materials

Additional research and strategic documents in [`docs/`](docs/):

- [The Strategic Evolution of AI-Native Software Engineering](docs/The%20Strategic%20Evolution%20of%20AI-Native%20Software%20Engineering.md) — Deep research covering benchmarks, CI/CD, compliance, TCO, local models, org scaling
- [Anthropic Build with Claude](docs/Anthropic%20Build%20with%20Claude%20-%20Developer%20Resources.md) — Developer resources reference
- [How I Code With AI Agents (Spec-Driven Development)](docs/How%20I%20Code%20With%20AI%20Agents%20(Spec-Driven%20Development).md)

---

## Repository Structure

```
promts/          — Structured prompts (specifications for AI generation)
results/         — Generated deliverables (sales pitch, technical playbook)
docs/            — Article series, research, and reference materials
ukr/             — Ukrainian translations of the article series
```

---

## Quick Start

1. **For leadership buy-in** → Read [results/01-sales-pitch.md](results/01-sales-pitch.md)
2. **For engineering teams** → Read [results/02-technical-best-practices.md](results/02-technical-best-practices.md)
3. **For deep understanding** → Start with [docs/00-AI-in-Software-Development-Foreword.md](docs/00-AI-in-Software-Development-Foreword.md)

Feedback and concise, opinionated notes are welcome.
