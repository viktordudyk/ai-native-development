# AI-Native Development

A practical guide to AI-native software engineering, from foundational concepts to enterprise adoption.

This repository contains **prompts**, **generated results**, and **reference documentation** for introducing and scaling AI-native development practices.

---

## Prompts & Results

The core of this repo: structured prompts that produce ready-to-use deliverables via frontier AI models.

| # | Prompt | Result (EN) | Result (UA) | Audience |
|---|--------|-------------|-------------|----------|
| 1 | [Sales Pitch](promts/01-sales-pitch.md) | [Strategic Sales Guideline](results/01-sales-pitch.md) | [UA](results/ua/01-sales-pitch-ua.md) | CTOs, VPs of Engineering, Tech Leads |
| 2 | [Technical Best Practices](promts/02-technical-best-practices.md) | [Technical Playbook](results/02-technical-best-practices.md) | [UA](results/ua/02-technical-best-practices-ua.md) | Senior developers, tech leads, architects |

**How it works:** Each prompt is a detailed specification. Feed it to a frontier model (Claude, GPT, Gemini) and get a polished, evidence-backed document as output. The results are committed here as reference.

---

## Documentation

### Core articles

| File | Description |
|------|-------------|
| [docs/article-series.md](docs/article-series.md) | Practical opinionated guide. AI in development, transformers, agents, MCP, SDD, autonomy risks |
| [docs/strategic-evolution-2026.md](docs/strategic-evolution-2026.md) | Deep research. Benchmarks, CI/CD, compliance, TCO, local models, org scaling |
| [docs/fact-check-2026.md](docs/fact-check-2026.md) | Evidence base and verified sources for claims in the sales guideline and technical playbook |

### Vendor references

| File | Description |
|------|-------------|
| [docs/anthropic-developer-resources.md](docs/anthropic-developer-resources.md) | Consolidated Anthropic "Build with Claude" developer reference |
| [docs/gcp-ai-platform-overview.md](docs/gcp-ai-platform-overview.md) | GCP AI stack with inline AWS / Azure equivalents (May 2026) |

### Sales research

Principal-dev-level answers to the top 10 questions an engineering lead asks before adopting AI SDLC practice. Research-backed, engineering-first tone, no marketing fluff. See [docs/sales-research/README.md](docs/sales-research/README.md) for the full index of all ten files.

### Ukrainian

| File | Description |
|------|-------------|
| [docs/ua/ai-native-implementation-guide.md](docs/ua/ai-native-implementation-guide.md) | Стратегічний посібник для впровадження AI-Native розробки |
| [docs/ua/sdd-corporate-framework.md](docs/ua/sdd-corporate-framework.md) | Корпоративний фреймворк переходу на SDD та агентний інжиніринг |
| [docs/ua/gcp-ai-platform-overview.md](docs/ua/gcp-ai-platform-overview.md) | Огляд GCP AI-стека з порівнянням до AWS і Azure |

### Presentation bundles

Each `pptx/` subfolder ships a deck plus the speaker script.

| Folder | For | Contents |
|--------|-----|----------|
| [pptx/agentic-engineering-shift/](pptx/agentic-engineering-shift/) | CTOs and VPs of Engineering | `deck.pptx`, `script.md` |
| [pptx/ai-native-playbook/](pptx/ai-native-playbook/) | Tech leads, architects, senior devs | `deck.pptx`, `script.md` |

### Source material

Raw transcripts and inputs that informed the curated articles live in [docs/_sources/](docs/_sources/), kept for citation, not for daily reading.

---

## Repository structure

```
promts/                         Structured prompts (specifications for AI generation)
  01-sales-pitch.md
  02-technical-best-practices.md
results/                        Generated deliverables
  01-sales-pitch.md
  02-technical-best-practices.md
  ua/                           Ukrainian translations of results
docs/                           Articles, vendor refs, sales research
  article-series.md
  strategic-evolution-2026.md
  fact-check-2026.md
  anthropic-developer-resources.md
  gcp-ai-platform-overview.md
  sales-research/               Top-10-question research for client calls
  ua/                           Ukrainian translations of long-form docs
  _sources/                     Raw transcripts (citation only)
pptx/                           Presentation bundles (deck + speaker script per folder)
  agentic-engineering-shift/    Sales-pitch deck for CTOs and VPs
  ai-native-playbook/           Technical playbook deck for tech leads and architects
integrations/                   Reserved for future integration notes (currently empty)
```

---

## Quick start

1. **For leadership buy-in:** read [results/01-sales-pitch.md](results/01-sales-pitch.md)
2. **For engineering teams:** read [results/02-technical-best-practices.md](results/02-technical-best-practices.md)
3. **Before pitching to clients:** review [docs/sales-research/](docs/sales-research/)
4. **For deep understanding:** start with [docs/article-series.md](docs/article-series.md)
5. **For cross-cloud AI decisions:** see [docs/gcp-ai-platform-overview.md](docs/gcp-ai-platform-overview.md)

Feedback and concise, opinionated notes are welcome.
