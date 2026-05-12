# Q9: Team Composition & Multi-Team Governance

## Intro

By mid-2026 the question for principal engineers is no longer "should we use coding agents" but "what does an org chart look like when a senior engineer is supervising five to ten of them?" Public memos from Shopify and Klarna, internal practice leaks from Anthropic and Block, and the 2025 Stack Overflow Developer Survey all point to the same restructuring: smaller teams, denser supervision, new spec/context roles, and a hub-and-spoke governance layer to keep methodology from fracturing. This note compiles the load-bearing data points and gives team-size thresholds for when each governance model kicks in.

## Pilot Team Shape

The convergent advice across Optimum Partners, Thoughtworks, and OpenAI's "Building an AI-Native Engineering Team" guide is **3-5 senior engineers replacing what used to be an 8-12 person squad**, with one Tech Lead acting as orchestrator/reviewer. Andres Max's analysis ("Are Large Software Teams Still Relevant?") and the unicrew.com 2026 guide both land on roughly **one-quarter the headcount** of pre-AI teams for greenfield work.

**Engineer-to-agent ratio:** Anthropic's own engineers run **5-10 concurrent Claude Code sessions** per dev (per the leaked "How Anthropic teams use Claude Code" PDF and Anthropic's "Building a C compiler with a team of parallel Claudes"). Addy Osmani ("Your AI coding agents need a manager") and Krischase's Cursor 3.2 Multitask analysis both warn the practical sustainable ceiling for most engineers is **3-5 parallel agents** before review debt overwhelms attention; 10 is achievable only with strong harness, shared memory files, and pre-built eval gates.

**Tech Lead supervision capacity:** Platform Engineering's "AI quality bottleneck" piece and Fortune's "supervisor class" article converge on **one senior reviewer for every 3-4 mid-level engineers running agents, or roughly 12-20 agent streams total** before quality collapses. Beyond that, you need either automated eval gates, a second-tier "critic agent" pattern (ASDLC adversarial code review), or a dedicated reviewer rotation.

**Real examples:**
- **Cursor/Anysphere:** ~60 employees driving $2B+ ARR in early 2025 (Contrary Research, eWeek).
- **Replit:** ~65 employees, $100M ARR by 2025 (VentureBeat, Sequoia podcast with Amjad Masad).
- **Anthropic internal:** 5-10 parallel Claude instances per engineer, 80% PR automation.
- **Shopify:** Tobi Lutke's April 2025 memo froze headcount expansion - teams must prove AI cannot do the job before hiring.
- **Klarna:** Workforce cut from 7,400 to 3,000 (target 2,000 by 2030), per Sebastian Siemiatkowski's Fortune interviews.

## Legacy Role Transformation

**Juniors:** Genuinely contested. The 2025 Stack Overflow survey reports entry-level tech hiring down 25% YoY and 30% drop in tech internship postings (Handshake data); 70% of hiring managers say AI can do an intern's job. Counter-evidence: Shopify is *hiring more interns* because they show the most creative AI usage (First Round Capital case study on Shopify). The defensible synthesis: juniors who use AI fluently are valuable; juniors who use AI as a crutch are not. Treat junior hiring as an AI-literacy filter, not a headcount line.

**Dedicated QA / manual testers:** Being absorbed into two roles - SDET-with-AI (Quash, Tricentis 2026 trends report) and eval/red-team specialist (Anthropic's "Demystifying evals for AI agents"). Tricentis reports 70% of enterprises will adopt AI for test authoring; World Quality Report 2025 says 58% are actively upskilling QA in AI tools. Pure manual scripted QA is the role most clearly being eliminated.

**CEO public statements:** Lutke memo (April 7, 2025, posted on X); Siemiatkowski Fortune interviews Feb and Oct 2025; Amjad Masad (Semafor, Jan 2025): "we don't care about professional coders anymore."

## New Roles Emerging

The dbreunig.com "Guide to AI Titles" (August 2025) and ODSC's "Emerging AI Job Roles for 2026" list these as the durable new titles:

- **Spec Author / Spec Engineer** - owns the executable spec under GitHub Spec-Kit or equivalent. Currently a *skill* in most orgs, hardening into a *role* at companies running 50+ engineers on spec-driven development.
- **AI Architect / AI Solutions Architect** - distinct hire at enterprises; the Coursera and Workable job descriptions show this role is mature, with MassMutual, Caterpillar, EY, and BlackRock posting publicly.
- **Context Engineer** - coined late 2025 (theinterviewguys.com, ODSC). Owns RAG, memory, chunking, retrieval, reranking. Gartner published a formal definition early 2026. Real job posts exist but most companies still bundle this into "AI Engineer."
- **Agent Supervisor / Agent Wrangler** - emerging title; Salesforce's "AI Agent trends 2026," Sierra's "AI Agent Engineer" post. Practically a skill today, becoming a role in shops running 20+ production agents.

Recommendation: at <50 engineers treat all four as *skills* layered onto existing seniors. At 50-200 engineers, dedicate Spec Engineers and an AI Architect. Above 200, dedicate Context Engineers and Agent Supervisors.

## QA Evolution

QA's new shape is **spec reviewer, eval author, adversarial tester**. Anthropic's evals guide and the ASDLC.io "Adversarial Code Review" pattern formalize the Critic Agent role - a separate AI session that reviews Builder output against the spec before human review. Tricentis, Momentic, and Test IO all describe agentic QA where the human defines quality objectives and oversees AI-authored test suites rather than writing them by hand. Where QA fits in the loop: gate 1 (spec review), gate 3 (adversarial review of generated code), gate 5 (eval suite authoring and maintenance).

## Multi-Team Governance Models

**Center of Excellence (CoE):** Fits at **200+ engineers or 5+ AI-using teams**. IBM and Oracle's CoE guides and the LeanIX framework agree on the CoE owning reference architectures, LLM catalogs, eval methods, model governance, and acting as both hub (shared assets) and coach (helping spokes pilot). Typical CoE headcount: 5-15 people - AI Architect, 2-3 Platform/MLOps engineers, eval/red-team lead, governance/risk officer, enablement lead. Gartner predicts 75% of enterprises will move from AI experimentation to operationalization by 2025, and most without a CoE will fail.

**Guild / Chapter model (Spotify-origin):** Fits **all sizes**, but most useful at 50-300 engineers spread across squads. CIO Magazine's "Reimagining the Spotify model for the human-AI enterprise" and Agenthunter's "Spotify 2.0" both describe guilds that *also train AI agents on the chapter's shared standards* - the chapter's CLAUDE.md becomes the chapter's deliverable. A global fintech reported 40% code review time reduction and 60% onboarding reduction after wiring backend chapter standards into AI copilots (CIO).

**Platform team owning AI tooling:** The Architecture and Governance Magazine and Atlan ("Centralized vs. federated AI teams") pieces converge on **hub-and-spoke** as the mature pattern: a central platform team owns the harness, eval framework, vendor contracts, secret management, and shared rules repo; product squads (spokes) own their use cases and squad-level CLAUDE.md overrides. BlackRock has publicly posted a Director, AI Enablement & Ecosystem role for exactly this function.

**Real enterprise examples:** Goldman Sachs (12,000-strong engineering team trialing Devin; 46,500 employees on GS AI Assistant), JPMorgan Chase ($1.5B annual AI business value, LLM Suite to 200,000 employees, 2,000+ AI experts), Morgan Stanley (DevGen.AI saved 280,000 developer hours rewriting legacy code) - all running hub-and-spoke with a CoE on top.

## Preventing Methodology Drift ("Dialects of SDD")

GitHub Spec-Kit (Sept 2025) ships with a "constitution" - immutable principles every spec inherits. Augment Code and Zencoder's SDD guides reinforce this: drift is prevented by **(a) lint the spec format in CI**, **(b) require constitution inheritance in every spec**, **(c) merge controls on the shared rules repo**, **(d) automated checks that generated code matches spec contracts**. Concretely: a `.claude/rules/` directory at monorepo root, service-level CLAUDE.md files with YAML frontmatter path globs (DataCamp, Shrivu Shankar's blog), and CODEOWNERS protection on those files. Best practice from sshh.io: only document tools used by 30%+ of engineers to keep the convention file lean.

## Sharing Reusable Artifacts

**Hub-and-spoke** wins for prompts, rules, and evals at enterprise scale (most public CoE writeups). Pure **federated** works only when squads are highly autonomous and convention drift is acceptable (Spotify's original model, which CIO and decocms.com note "failed at scale"). The pragmatic shape: central repo of prompts/rules/evals with merge controls, federated *consumption* (squads cherry-pick and override), central observability on which assets actually get used so dead artifacts get culled.

## Sources

- [Shopify CEO memo - TechCrunch](https://techcrunch.com/2025/04/07/shopify-ceo-tells-teams-to-consider-using-ai-before-growing-headcount/)
- [Tobi Lutke memo on X](https://x.com/tobi/status/1909251946235437514)
- [From Memo to Movement: Shopify - First Round](https://www.firstround.com/ai/shopify)
- [Klarna CEO Fortune](https://fortune.com/2025/10/10/klarna-ceo-sebastian-siemiatkowski-halved-workforce-says-tech-ceos-sugarcoating-ai-impact-on-jobs-mass-unemployment-warning/)
- [Replit CEO Semafor](https://www.semafor.com/article/01/15/2025/replit-ceo-on-ai-breakthroughs-we-dont-care-about-professional-coders-anymore)
- [Block Goose - VentureBeat](https://venturebeat.com/programming-development/jack-dorsey-is-back-with-goose-a-new-ultra-simple-open-source-ai-agent-building-platform-from-his-startup-block)
- [How Anthropic teams use Claude Code (PDF)](https://www-cdn.anthropic.com/58284b19e702b49db9302d5b6f135ad8871e7658.pdf)
- [Anthropic: Building a C compiler with parallel Claudes](https://www.anthropic.com/engineering/building-c-compiler)
- [Claude Code agent teams docs](https://code.claude.com/docs/en/agent-teams)
- [Anthropic: Demystifying evals for AI agents](https://www.anthropic.com/engineering/demystifying-evals-for-ai-agents)
- [Addy Osmani: Your AI coding agents need a manager](https://addyosmani.com/blog/coding-agents-manager/)
- [Platform Engineering: AI quality bottleneck](https://platformengineering.org/blog/the-ai-quality-bottleneck-every-platform-team-will-face)
- [Fortune: The supervisor class](https://fortune.com/2026/03/31/fortune-com-2026-03-26-ai-agents-vibe-coding-developer-skills-supervisor-class/)
- [Stack Overflow 2025 Developer Survey - AI](https://survey.stackoverflow.co/2025/ai)
- [Stack Overflow: AI vs Gen Z](https://stackoverflow.blog/2025/12/26/ai-vs-gen-z/)
- [Thoughtworks: Spec-driven development](https://www.thoughtworks.com/en-us/insights/blog/agile-engineering-practices/spec-driven-development-unpacking-2025-new-engineering-practices)
- [GitHub Spec-Kit](https://github.com/github/spec-kit)
- [ASDLC: Adversarial Code Review](https://asdlc.io/patterns/adversarial-code-review/)
- [Optimum Partners: Engineering Management 2026](https://optimumpartners.com/insight/engineering-management-2026-how-to-structure-an-ai-native-team/)
- [OpenAI Codex: Build an AI-native engineering team](https://developers.openai.com/codex/guides/build-ai-native-engineering-team)
- [Context Engineer - InterviewGuys](https://blog.theinterviewguys.com/what-is-a-context-engineer/)
- [ODSC: Emerging AI Job Roles 2026](https://opendatascience.com/from-context-engineers-to-chief-ai-officers-emerging-ai-job-roles-for-2026/)
- [dbreunig: Guide to AI Titles](https://www.dbreunig.com/2025/08/21/a-guide-to-ai-titles.html)
- [IBM: AI Center of Excellence](https://www.ibm.com/think/topics/ai-center-of-excellence)
- [Oracle: AI CoE](https://www.oracle.com/asean/artificial-intelligence/ai-center-excellence/)
- [SAP LeanIX: AI CoE framework](https://www.leanix.net/en/wiki/ai-governance/ai-center-of-excellence)
- [CIO: Reimagining Spotify model for human-AI enterprise](https://www.cio.com/article/4014026/reimagining-the-spotify-model-for-the-human-ai-enterprise.html)
- [Architecture & Governance: Hub & Spoke for AI](https://www.architectureandgovernance.com/artificial-intelligence/hub-spoke-the-operating-system-for-ai-enabled-enterprise-architecture/)
- [Atlan: Centralized vs federated AI teams](https://atlan.com/know/centralized-vs-federated-data-teams-in-the-ai-era/)
- [Lucidate: Beyond the Pilot - JPMorgan, Goldman, HSBC](https://www.lucidate.co.uk/post/beyond-the-pilot-how-jpmorgan-goldman-sachs-and-hsbc-are-scaling-ai-to-enterprise-production)
- [Goldman Sachs AI Assistant - CNBC](https://www.cnbc.com/2025/01/21/goldman-sachs-launches-ai-assistant.html)
- [Tricentis: QA trends 2026](https://www.tricentis.com/blog/qa-trends-ai-agentic-testing)
- [Enterprise Times: Agentic AI red teaming](https://www.enterprisetimes.co.uk/2025/11/17/agentic-ai-testing-gets-adversarial-with-ai-red-teaming/)
- [DataCamp: Writing CLAUDE.md](https://www.datacamp.com/tutorial/writing-the-best-claude-md)
- [Shrivu Shankar: How I use every Claude Code feature](https://blog.sshh.io/p/how-i-use-every-claude-code-feature)
- [Cursor business breakdown - Contrary Research](https://research.contrary.com/company/cursor)
- [Replit CEO Sequoia podcast](https://sequoiacap.com/podcast/training-data-amjad-masad/)
