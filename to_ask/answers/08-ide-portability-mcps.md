# Q8: IDE Portability & MCP Catalog by Stack Layer

## Intro

As of May 2026, the AI-native IDE/CLI market has converged on two portable contracts: **AGENTS.md** (now stewarded by the Agentic AI Foundation under the Linux Foundation) for repo-level context, and **MCP** (Model Context Protocol) for tool access. Everything else — rules, modes, skills, workflows, panels — is IDE-specific glue. For a consulting team, this means roughly **70-80% of methodology survives an IDE swap** if you author against AGENTS.md + MCP. The remaining 20-30% (Cursor `.cursorrules`, Windsurf Workflows, Claude `CLAUDE.md`/Skills, Copilot `.agent.md`) is tool-specific surface area you accept as cost of leverage. Below is the portability matrix, then a per-stack-layer MCP catalog with concrete servers and gaps.

## IDE Portability Matrix

| Feature | Cursor | Claude Code | Codex CLI | GitHub Copilot | Windsurf | Aider | Continue | Zed AI |
|---|---|---|---|---|---|---|---|---|
| `AGENTS.md` support | Yes | Yes (fallback after `CLAUDE.md`) | Yes (native) | Yes (via `.agent.md`/instructions) | Yes | Yes | Yes | Yes |
| MCP servers | Yes | Yes | Yes | Yes (repo + user scope) | Yes | Partial (via ACP) | Yes | Yes (native + ACP forwarding) |
| Tool-specific rules file | `.cursor/rules/*.mdc` | `CLAUDE.md`, `.claude/skills/` | `AGENTS.md` only | `.github/copilot-instructions.md`, `.agent.md` | `.windsurf/rules/` + Workflows | `.aider.conf.yml` | `config.json` rules | `.rules` |
| Skills / reusable prompts | Custom modes | SKILL.md (portable across 20+ agents) | Codex Skills | Custom Agents (`.agent.md`) | Workflows | Conventions only | Slash commands | Slash commands |
| Sub-agents / parallel | Background Agents | Subagents | Codex parallel | Cloud agent | Cascade | No | No | Parallel agents (Zed 1.0) |
| External agent host (ACP) | No | N/A | N/A | No | No | Aider-as-ACP (in progress) | No | Yes (host for Claude/Codex/Gemini) |

**Portability rule of thumb:** AGENTS.md + MCP + SKILL.md = portable. Cursor rules, Windsurf Workflows, and Copilot `.agent.md` require translation (typically 2-4 hours per project per swap).

## MCP Catalog by Stack Layer

### iOS

| Server | Type | Link |
|---|---|---|
| **XcodeBuildMCP** (Sentry-maintained, 82 tools: sim+device+LLDB+UI automation) | Community/vendor-backed | [github.com/getsentry/XcodeBuildMCP](https://github.com/getsentry/XcodeBuildMCP) |
| **Apple xcrun mcpbridge** (Xcode 26.3, XPC into Xcode, ~20 tools) | Official (Apple) | Ships with Xcode 26.3+ |
| **ios-simulator-mcp** (Joshua Yoes — UI inspection, gesture sim) | Community | [github.com/joshuayoes/ios-simulator-mcp](https://github.com/joshuayoes/ios-simulator-mcp) |

### Android

| Server | Type | Link |
|---|---|---|
| **Gemini in Android Studio MCP host** (built-in MCP client via `mcp.json`) | Official (Google) | [developer.android.com/studio/gemini/add-mcp-server](https://developer.android.com/studio/gemini/add-mcp-server) |
| **Android MCP SDK** (Jason Pearson — Gradle-integrated debug-build server) | Community | [kaeawc.github.io/android-mcp-sdk](https://kaeawc.github.io/android-mcp-sdk/getting-started/) |
| **Kotlin Android Dev MCP** (31+ tools: Compose, Gradle, ktlint, Room, Retrofit) | Community | [pulsemcp.com/servers/kotlin-android](https://www.pulsemcp.com/servers/kotlin-android) |
| **android-mcp-server** (minhalvp — raw ADB control) | Community | [github.com/minhalvp/android-mcp-server](https://github.com/minhalvp/android-mcp-server) |

### Web / Browser

| Server | Type | Link |
|---|---|---|
| **Playwright MCP** (`@playwright/mcp`, accessibility-tree driving) | Official (Microsoft) | [github.com/microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) |
| **Chrome DevTools MCP** (live debugging, perf, network, console) | Official (Google) | Bundled w/ Chrome; see Google DevTools blog |
| **browser-use / Stagehand / Browserbase / Skyvern** (Playwright/CDP-based agent harnesses) | Community / commercial | Various |

### Backend / Languages

| Server | Type | Link |
|---|---|---|
| **Spring AI MCP** (Spring Boot starter exposes beans as MCP tools) | Official (VMware) | Spring AI docs |
| **Node.js Debugger MCP** (breakpoints, step, eval at runtime) | Community | [cursor.directory/mcp/node-js-debugger](https://cursor.directory/mcp/node-js-debugger) |
| **.NET / ASP.NET Core MCP** (community servers via NuGet) | Community | scattered |
| **LSP-MCP bridges** (any LSP via generic adapter) | Community | varies |

### Data

| Server | Type | Link |
|---|---|---|
| **dbt MCP Server** (official, warehouse-agnostic) | Official (dbt Labs) | dbt Labs docs |
| **dbt-core-mcp** (NiclasOlofsson — adapter-agnostic) | Community | [github.com/NiclasOlofsson/dbt-core-mcp](https://github.com/NiclasOlofsson/dbt-core-mcp) |
| **Snowflake MCP** | Official (Snowflake) | snowflake.com |
| **BigQuery MCP** | Official (Google) | cloud.google.com |
| **Databricks MCP** | Official (Databricks) | databricks.com |
| **MotherDuck/DuckDB MCP** | Official (MotherDuck) | motherduck.com |
| **ClickHouse MCP**, **Redshift MCP** | Official (vendors) | resp. vendor docs |
| **Airflow MCP** | Community (no first-party yet) | gap — see below |

### Infra / Cloud

| Server | Type | Link |
|---|---|---|
| **AWS MCP** (managed remote, full SDK + docs) | Official (AWS) | [awslabs.github.io/mcp](https://awslabs.github.io/mcp/) |
| **Terraform MCP** | Official (HashiCorp, also on AWS Marketplace) | [aws.amazon.com/marketplace](https://aws.amazon.com/marketplace/pp/prodview-v7liwliuew3f4) |
| **Kubernetes MCP** (containers/kubernetes-mcp-server, native Go, K8s + OpenShift) | Community (Red Hat-led) | [github.com/containers/kubernetes-mcp-server](https://github.com/containers/kubernetes-mcp-server/) |
| **Azure DevOps MCP** | Official (Microsoft) | Microsoft docs |
| **GCP MCP** (Compute, Cloud Run, GKE) | Community + partial Google | GCP docs |

### Observability

| Server | Type | Link |
|---|---|---|
| **Grafana MCP** (`mcp-grafana`, 40+ tools, 2.6k+ stars, leader by adoption) | Official (Grafana Labs) | github.com/grafana/mcp-grafana |
| **Datadog MCP** (Bits AI — metrics, logs, traces, incidents) | Official (Datadog) | [docs.datadoghq.com/bits_ai/mcp_server](https://docs.datadoghq.com/bits_ai/mcp_server/) |
| **Sentry MCP** (issues, Seer analysis, traces) | Official (Sentry) | mcp.sentry.dev |
| **PagerDuty MCP**, **Honeycomb MCP**, **New Relic MCP** | Official (vendors) | resp. vendor docs |

### Generic / Shared

| Server | Type | Link |
|---|---|---|
| **GitHub MCP** | Official (GitHub/Anthropic-collab) | [github.com/github/github-mcp-server](https://github.com/github/github-mcp-server) |
| **GitLab MCP** | Official (GitLab) | gitlab.com docs |
| **Atlassian Rovo MCP** (Jira + Confluence + Compass, OAuth 2.1) | Official (Atlassian) | [github.com/atlassian/atlassian-mcp-server](https://github.com/atlassian/atlassian-mcp-server) |
| **mcp-atlassian** (sooperset, community, more flexible auth) | Community | [github.com/sooperset/mcp-atlassian](https://github.com/sooperset/mcp-atlassian) |
| **Slack MCP** | Official (Slack/Anthropic) | modelcontextprotocol/servers |
| **Linear MCP**, **Notion MCP**, **Asana MCP** | Official (vendors) | resp. vendor docs |
| **Filesystem MCP** (`@modelcontextprotocol/server-filesystem`) | Official reference | [modelcontextprotocol/servers](https://github.com/modelcontextprotocol/servers) |
| **Memory MCP** (`@modelcontextprotocol/server-memory`, knowledge-graph) | Official reference | same repo |

## Gaps (where nothing solid exists in mid-2026)

- **Airflow MCP**: no first-party server; community options thin and brittle. DAG introspection + run-triggering is mostly manual.
- **iOS device farm MCP**: nothing wraps BrowserStack/Sauce/Firebase Test Lab cleanly for iOS.
- **Mobile crash-symbolication MCP**: Sentry handles symbolicated stacks but there's no generic dSYM/ProGuard workflow server.
- **Design-to-code MCP**: Figma MCP exists (official) but no canonical Sketch/Penpot equivalent.
- **Cost / FinOps MCP**: AWS Billing MCP is production-ready, but cross-cloud FinOps (Vantage, Infracost) is fragmented.
- **`.NET runtime debugger MCP`**: nothing equivalent to the Node.js Debugger MCP for stepping through ASP.NET Core at runtime.
- **Generic LSP-MCP bridge**: would let any Language Server become an MCP — exists in toy form, no production-grade option.
- **Secrets / Vault MCP**: HashiCorp Vault and 1Password have community servers, no battle-tested official one.

## Methodology Transfer Score

For a typical engagement, expect:
- **~75% portable** if you commit to AGENTS.md + MCP + SKILL.md as primary contracts.
- **~15% translation cost** for rules/workflows/custom agents on an IDE swap (couple of hours).
- **~10% pure tool-specific UX** (panels, key bindings, model picker, billing) — accept as non-portable.

Bet your methodology on AGENTS.md and MCP. Treat Cursor rules, Windsurf workflows, and Copilot custom agents as thin wrappers, not core IP.

## Sources

- [agents.md - AGENTS.md open standard](https://agents.md/)
- [Agensi - AI Agent Configuration Guide 2026](https://www.agensi.io/learn/ai-agent-configuration-guide-2026)
- [Agensi - Claude Code Skills vs Cursor Rules vs Codex Skills](https://www.agensi.io/learn/claude-code-skills-vs-cursor-rules-vs-codex-skills)
- [DeployHQ - CLAUDE.md, AGENTS.md & Copilot Instructions guide](https://www.deployhq.com/blog/ai-coding-config-files-guide)
- [Augment Code - How to Build Your AGENTS.md (2026)](https://www.augmentcode.com/guides/how-to-build-agents-md)
- [GitHub - getsentry/XcodeBuildMCP](https://github.com/getsentry/XcodeBuildMCP)
- [GitHub - joshuayoes/ios-simulator-mcp](https://github.com/joshuayoes/ios-simulator-mcp)
- [Android Developers - Add an MCP server in Android Studio](https://developer.android.com/studio/gemini/add-mcp-server)
- [Android MCP SDK docs](https://kaeawc.github.io/android-mcp-sdk/getting-started/)
- [GitHub - microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp)
- [Steve Kinney - Playwright vs Chrome DevTools MCP](https://stevekinney.com/writing/driving-vs-debugging-the-browser)
- [GitHub - NiclasOlofsson/dbt-core-mcp](https://github.com/NiclasOlofsson/dbt-core-mcp)
- [ChatForest - Data Warehouse MCP Servers review](https://chatforest.com/reviews/data-warehouse-lakehouse-mcp-servers/)
- [AWS Labs - Open Source MCP Servers for AWS](https://awslabs.github.io/mcp/)
- [GitHub - containers/kubernetes-mcp-server](https://github.com/containers/kubernetes-mcp-server/)
- [Terraform MCP Server on AWS Marketplace](https://aws.amazon.com/marketplace/pp/prodview-v7liwliuew3f4)
- [Datadog MCP Server docs](https://docs.datadoghq.com/bits_ai/mcp_server/)
- [k8slens - 18 Best DevOps MCP Servers for 2026](https://medium.com/k8slens/18-best-devops-mcp-servers-for-2026-the-definitive-guide-bfde04654a35)
- [ChatForest - Observability & Monitoring MCP Servers](https://chatforest.com/categories/observability-monitoring/)
- [GitHub - atlassian/atlassian-mcp-server](https://github.com/atlassian/atlassian-mcp-server)
- [GitHub - sooperset/mcp-atlassian](https://github.com/sooperset/mcp-atlassian)
- [GitHub - modelcontextprotocol/servers (reference servers incl. Memory, Filesystem)](https://github.com/modelcontextprotocol/servers)
- [Official MCP Registry](https://registry.modelcontextprotocol.io/)
- [GitHub Docs - Copilot agent mode + MCP](https://docs.github.com/en/copilot/tutorials/enhance-agent-mode-with-mcp)
- [Zed - External Agents docs](https://zed.dev/docs/ai/external-agents)
- [Zed Blog - ACP progress report (Aider implementation)](https://zed.dev/blog/acp-progress-report)
- [Cursor Directory - Node.js Debugger MCP](https://cursor.directory/mcp/node-js-debugger)
