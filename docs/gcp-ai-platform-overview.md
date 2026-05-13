# The GCP AI Stack, With AWS and Azure on the Other Side of the Table

*Created: 13 May 2026. Verified against Deep Research output dated May 2026, see [docs/_sources/gcp-ai-platform-deep-research-may-2026.md](_sources/gcp-ai-platform-deep-research-may-2026.md).*

Anyone consulting on AI architecture in 2026 needs a working model of all three hyperscaler stacks, even if their primary deployment is on one of them. Clients ask. RFPs ask. A board member who read a Bloomberg piece asks. Worse, the platforms renamed themselves again. Vertex AI was retired as a brand at Cloud Next on 22 April 2026 and the suite consolidated under Gemini Enterprise Agent Platform. Azure AI Foundry dropped the "Azure" prefix on 1 January 2026 (announced at Ignite, 18 November 2025) and became Microsoft Foundry. AWS kept its decoupled approach but tightened the tripartite story around Amazon Bedrock, SageMaker Unified Studio, and Bedrock AgentCore. The branding is new. The capabilities mostly are not.

This document is a reference for teams whose center of gravity is Google Cloud but who need to explain or defend that choice when clients want to know what the AWS or Azure equivalent looks like. GCP-first, with the equivalents called out inline. Not a buying guide. Not a benchmark. Not a marketing piece for any one of the three.

Scope limits worth stating up front. Hyperscaler-only, so no Snowflake, Databricks, Oracle, or Alibaba. No deep coverage of data-side AI products like BigQuery ML, Looker, or Fabric. The information here is current as of May 2026 and the SKU names will rot within the year.

---

## The cross-cloud cheat sheet

| Capability | GCP | AWS | Azure |
|---|---|---|---|
| Unified AI platform | Gemini Enterprise Agent Platform (renamed from Vertex AI, 22 Apr 2026) | Bedrock + SageMaker Unified Studio + AgentCore | Microsoft Foundry (renamed from Azure AI Foundry, 1 Jan 2026) |
| Foundation model hub | Vertex AI Model Garden, 200+ models | Amazon Bedrock | Foundry Models, 11,000+ entries (Hugging Face mirrors included) |
| First-party LLMs | Gemini 3.1 Pro, 3 Flash, 3.1 Flash-Lite | Amazon Nova (Premier, Pro, Lite, Micro), Titan | GPT-5.4, 5.3-codex, 5.2, 5.1 via Azure OpenAI |
| Anthropic models | Claude Opus 4.5, Sonnet 4.6, Haiku 4.5 (Vertex) | Claude Opus 4.7, Sonnet 4.6, Haiku 4.5 | Claude family via Marketplace billing |
| Image generation | Imagen 4 (preview), Virtual Try-On | Nova Canvas v1, Stability | GPT-image-2, FLUX.2-pro, FLUX.2-flex (preview) |
| Video generation | Veo 3.1 (GA), Veo 3.1 Lite (preview) | Nova Reel v1 (up to 2 min) | Sora 2 (retiring 3 Jun 2026) |
| Music generation | Lyria 3 (preview, 25 Mar 2026) | none | none |
| Agent runtime | Vertex AI Agent Runtime (renamed from Agent Engine, 11 Feb 2026) | Bedrock AgentCore (+ x402 payments preview) | Foundry Agent Service |
| Agent framework | Agent Development Kit (ADK) | Strands, Bedrock Agents | Semantic Kernel, AutoGen |
| Low-code builder | Agent Builder, Express Mode | Bedrock Agents | Copilot Studio |
| RAG and knowledge | Vertex AI Search + Vector Search 2.0 | Bedrock Knowledge Bases | Foundry IQ + Azure AI Search |
| IDE assistant | Gemini Code Assist | Amazon Q Developer, Kiro | GitHub Copilot |
| CLI assistant | Gemini CLI | Q Developer CLI | GitHub Copilot CLI |
| Training silicon | TPU Trillium (v6e), Ironwood (v7) | Trainium 2, Trainium 3 | Maia 100, Maia 200 |
| GPU options | H100, H200, B200 (A4) | P5, P5en, P6 | ND H100/H200 v5/v6 |
| MCP support | Cloud API Registry | AgentCore Tool Gateway | Foundry Agent Service |
| Free tier headline | $300 in credits, 90 days | 12 months free tier across services | $200 in credits, 30 days |

The rest of this doc walks each row in more depth.

---

## Core AI/ML platform

Gemini Enterprise Agent Platform is the new umbrella name for what used to be Vertex AI. The rename happened at Cloud Next on 22 April 2026, and it is more than cosmetic. Google consolidated Model Garden, Vertex AI Search, Vector Search 2.0, AutoML, the agent runtime (now called Agent Runtime), pipelines, registry, feature store, and evaluation tooling under a single billing surface and a single IAM model. The Vertex AI brand still appears in URLs and SDK names. For anyone writing IaC against `aiplatform.googleapis.com`, nothing broke. The intent of the consolidation was to push developers toward agent-shaped workloads as the default rather than stateless inference calls.

What changed in this generation:

- The Gemini 3 series ships as Gemini 3.1 Pro (flagship, preview since 19 February 2026), Gemini 3 Flash (preview since January 2026), and Gemini 3.1 Flash-Lite (GA on 7 May 2026). All three share a 1 million token context window. The previous `gemini-3-pro-preview` endpoint was forcibly retired on 26 March 2026, which caused a hard migration scramble for teams not watching the changelog.
- Claude on Vertex covers Opus 4.5, Sonnet 4.6, and Haiku 4.5, billed against Google Cloud spend through the Anthropic partnership. Note that Vertex tracks one major version behind Bedrock on Opus (4.5 vs Bedrock's 4.7).
- Model Garden crossed 200 first-party and partner models, including DeepSeek, GLM 5, MiniMax, Mistral, Llama 4, and a long tail of fine-tuned open weights.

Pricing snapshot for the Gemini family on Vertex (per 1M tokens, input / output):

| Model | Input ≤200K | Input >200K | Output ≤200K | Output >200K |
|---|---|---|---|---|
| Gemini 3.1 Pro | $2.00 | $4.00 | $12.00 | $24.00 |
| Gemini 3 Flash | $0.50 | $0.50 | $3.00 | $3.00 |
| Gemini 3.1 Flash-Lite | $0.25 | $0.25 | $1.50 | $1.50 |

Context caching costs $0.025 per 1M cached text, image, or video tokens; $0.05 per 1M cached audio tokens; plus $1.00 per 1M tokens per hour as a storage baseline. For any workload that re-reads a corpus, caching is the single biggest lever on bill size.

Picking among the Gemini variants is straightforward in practice. Gemini 3.1 Pro is the default for anything that requires reasoning over long inputs, agent planning, or hard multimodal understanding. Gemini 3 Flash handles the high-volume layer (classification, extraction, summarization, routine inference) at a fraction of the cost. Gemini 3.1 Flash-Lite is the new bottom rung, useful for cost-sensitive bulk pipelines where Flash itself is overkill. Claude Opus 4.5 on Vertex is the choice for teams who want Anthropic's particular strengths on instruction following and writing quality but who need to keep all spend on Google Cloud, with the caveat that Bedrock has the newer Opus 4.7. A pragmatic production stack will use three of these: Flash or Flash-Lite for the high-volume layer, 3.1 Pro as the workhorse, and either an escalation to 200K-plus context or Claude for the hardest queries with a router in front.

Vertex AI Search is the managed RAG and enterprise search product. Out of the box it indexes Cloud Storage, BigQuery, SharePoint, Confluence, Salesforce, Jira, and a long list of others. Vector Search 2.0 is the underlying vector database, with index types for filtered ANN, hybrid (dense plus sparse) retrieval, and grounding callbacks built directly into Gemini API calls.

The cost arithmetic on RAG is worth checking before committing. Vertex AI Search Standard is $1.50 per 1,000 queries. The Enterprise edition with Core Generative Answers is $4.00 per 1,000 queries, and the Advanced Generative Answers add-on tacks on another $4.00 per 1,000 user input queries. Self-hosting on AlloyDB with pgvector cuts per-query cost by roughly half at scale, but the human cost of ingestion pipelines, chunking, embedding choice, reranker tuning, and metrics adds up to a small team's quarter. For most teams under 100,000 queries per day, the math favors Vertex AI Search. Past that volume, the question is real, and the answer is usually a hybrid: Vertex AI Search for the long tail, custom for the hot path.

Grounding is the feature that separates Vertex from a raw model API. Gemini calls on Vertex can ground responses against Google Search (giving the model live web access with citations), against Vertex AI Search (custom corpora), or against both. Web grounding is the killer feature for anything that needs current facts. AWS and Azure both offer custom-corpus grounding, but neither has a first-party live web grounding equivalent of comparable quality. Bedrock added a web search action in late 2025, and Foundry has Bing grounding through a separate connector, but Google's Search index plus integration depth is the wider moat in this category.

MLOps on Vertex is mature in the way that mature things are also boring. Vertex AI Pipelines uses Kubeflow Pipelines under the hood with a Google-managed control plane. Model Registry, Feature Store, Experiments, and Model Monitoring are all GA and reasonably well-instrumented. Vertex AI Evaluation Service is the newer piece, focused on LLM and agent evaluation with metrics for groundedness, instruction following, safety, and rubric-based scoring against custom criteria.

AutoML is still there for tabular, image, video, and text tasks, but the center of gravity moved to Gemini fine-tuning. For most use cases that would have been AutoML in 2023, the recommendation is now LoRA or full fine-tuning on Gemini 3 Flash, deployed to a managed endpoint. AutoML Tables specifically was folded into BigQuery ML.

**AWS equivalent.** Amazon Bedrock is the model hub. SageMaker Unified Studio is the workbench. The two are stitched together but still feel like two products with a shared sign-on. Bedrock hosts the deepest Anthropic catalog of the three clouds: Claude Opus 4.7 (April 2026, $5 / $25 per 1M), Sonnet 4.6 (February 2026, $3 / $15), and Haiku 4.5 (October 2025, $1 / $5), plus the Amazon Nova lineup. Nova Premier sits at $1.25 / $6.25 per 1M, Nova Pro at $0.80 / $3.20, Nova Lite at $0.06 / $0.24, and Nova Micro at $0.035 / $0.14, making Micro the cheapest serious model on any cloud. Bedrock Knowledge Bases is the RAG offering, defaulting to OpenSearch Serverless. The catch worth flagging: OpenSearch Serverless mandates a minimum 2 OCU configuration at $0.24 per OCU-hour, which floors the cost at roughly $350 per month before a single query lands. AWS launched S3 Vectors in late 2025 to address this. Bedrock also supports batch inference at a 50 percent discount versus on-demand, a real lever for high-throughput async pipelines.

**Azure equivalent.** Microsoft Foundry consolidated Azure AI Foundry, Azure OpenAI Service, and parts of Azure ML into a single portal on 1 January 2026. Foundry Models advertises 11,000+ entries, a jump from the 1,900 number that circulated through 2025. Most of the new count is community fine-tunes and Hugging Face mirrors, not first-party additions, so the headline number is more a SEO claim than a usable signal. The strategic depth is in Azure OpenAI: GPT-5.4 (March 2026, $2.50 / $15 per 1M), GPT-5.3-codex (February 2026, $1.75 / $14), GPT-5.2 (December 2025), and GPT-5.1 (November 2025), all exclusive to Azure outside OpenAI's own API. Azure aggressively discounts cached input. GPT-5.3-codex cached input drops from $1.75 to $0.18, a 10x reduction that materially changes the economics of long-context coding workloads. Azure AI Search is the RAG product, mature and well-integrated with Microsoft 365 sources via Foundry IQ, billed at $0.022 per 1M tokens with a 50M token monthly free tier. Foundry IQ operational tools have their own line items: Code Interpreter at $0.033 per session, Web Search via Bing at $14.00 per 1,000 transactions.

**Pick GCP when** your reasoning workload benefits from Gemini 3.1 Pro's 1M token context window, you need native multimodal handling without pipeline glue, or your data already lives in BigQuery. **Look elsewhere when** Anthropic model recency matters most (Bedrock has Opus 4.7 vs Vertex's 4.5), or your identity and document plane is already Microsoft 365 (Azure leads). The Vector Search story on GCP is good but not unique. Don't pick GCP for vector search alone.

---

## Benchmarks

A May 2026 snapshot. The frontier is tightly contested, and the "best" model varies sharply by task. Self-reported vendor scores are common in this category; the numbers below are drawn from the most widely cited public sources but should be treated as approximate.

| Benchmark | Gemini 3.1 Pro | Claude Opus 4.6 | Claude Sonnet 4.6 | GPT-5.5 | GPT-5.4 | GPT-5.3-codex |
|---|---|---|---|---|---|---|
| SWE-bench Verified | 80.6% | 80.8% | 79.6% | 82.6% | 78.2% | 78.0% |
| SWE-bench Pro | 54.2% | - | - | - | - | 56.8% |
| LiveCodeBench Pro (Elo) | 2887 | - | - | - | - | - |
| MMLU-Pro (MMMLU) | 92.6% | - | 89.3% | - | - | - |
| HumanEval pass@1 | 89.2% | 90.4% | - | - | 93.1% | - |
| GPQA Diamond | 94.3% | 91.3% | 89.9% | - | 92.0% | - |

Agentic and tool-use benchmarks (vendor or community reported):

- **TAU-bench (retail):** Claude Opus 4.6 leads at 91.9%, Sonnet 4.6 at 91.7%, Gemini 3.1 Pro at 90.8%.
- **Terminal-Bench 2.0:** GPT-5.3-codex dominates at 77.3%, far ahead of Gemini 3.1 Pro (68.5%) and Claude Opus 4.6 (65.4%).
- **GDPval-AA Elo (expert office tasks):** Claude Sonnet 4.6 (1633 Elo) leads, ahead of Opus 4.6 (1606).

The honest read: GPT-5.5 and Claude Opus 4.6 hold a slim lead on single-shot software engineering tasks. Gemini 3.1 Pro is the best at hard reasoning (GPQA Diamond) and competitive coding logic (LiveCodeBench Pro). GPT-5.3-codex is the uncontested winner on long-running terminal work. Anyone serious about production agents is routing by task, not picking one model and calling it done. These numbers will be partially wrong within a quarter. The pattern (specialized leaders by task domain) lasts longer than the specific scores.

---

## Agents and developer tools

Agents on GCP layer as framework, runtime, builder, and dev surface. Each piece is usable on its own, but the integration story is the actual product.

Agent Development Kit (ADK) is the framework. Open-source, Python and Go, modeled on the same patterns as LangGraph but with Google's own primitives for tool use, planning, multi-agent handoff, and memory. ADK works against any Gemini or Vertex-hosted model and can be deployed to anything that runs Python, but the canonical target is Agent Runtime.

Vertex AI Agent Runtime (renamed from Agent Engine on 11 February 2026) is the managed runtime. Persistent agent sessions, conversation memory up to 8 hours, sandboxed code execution, integration with Vertex AI Search for grounding, and IAM-aware tool calling. Billing shifted to compute-based at the rename: $0.0864 per vCPU-hour ($0.00144 per minute), $0.0090 per GB-hour for memory, plus $0.25 per 1,000 stored memories for long-term session state. Agent Runtime costs more than running ADK on Cloud Run but absorbs a meaningful pile of plumbing: session state, retries, tracing, evals integration, and the long tail of "your agent ran for 40 minutes and then died, where do we look".

The actual cost gap versus self-managed is smaller than the sticker price suggests. The same agent on Cloud Run with self-managed Redis for sessions runs about 30 percent cheaper if utilization stays high, but the moment you have to add tracing, retries, multi-region failover, and an eval pipeline, the gap closes. Most teams that benchmark this end up on Agent Runtime for production and Cloud Run for offline experimentation.

Agent Builder is the low-code surface. Drag-and-drop tool wiring, natural language goal specification, and a deployment path that ends at Agent Runtime. Express Mode (the free tier for Agent Builder) gives ~$300 of usage credits and lets a non-engineering team get a functional internal agent live in an afternoon. It is genuinely useful for prototyping. It is not where most production agents will live.

Gemini Code Assist is the IDE integration. VS Code, JetBrains, Android Studio, Cloud Workstations, with chat, code completion, transformation, and inline explanations. The differentiator versus GitHub Copilot is repository-scale context. Code Assist Enterprise indexes a customer's private repo and surfaces results during inline generation. For monorepo shops, this matters more than benchmark scores.

Gemini CLI is the terminal counterpart. Released GA in late 2025, refreshed at Cloud Next 2026 with extension support, slash commands, multi-file editing, and an MCP server registry. It is the closest GCP-native analogue to Claude Code or Codex CLI, and the gap has closed enough that picking between them is a question of model preference and ecosystem rather than feature parity.

MCP support across the platform. Google shipped a Cloud API Registry that exposes GCP services (BigQuery, Cloud Run, Spanner, Vertex AI, and the long tail) as MCP servers, so an agent built on any framework can call them. Agent Runtime accepts MCP servers as tools natively. Gemini CLI has an MCP server registry.

**AWS equivalent.** AgentCore is the runtime, GA since mid-2025. Persistent memory, tool gateway with MCP support, identity integration, and observability. The most interesting recent addition is the payments preview on 7 May 2026 that lets agents transact through Coinbase or Stripe wallets via the x402 protocol mid-reasoning. If your agent needs to buy compute, API calls, or third-party tools autonomously, AWS is the only cloud where this is a first-class capability today. Strands is the open-source framework, smaller community than ADK but well-documented. Bedrock Agents is the low-code builder, oriented around Bedrock Knowledge Bases as the grounding layer. Amazon Q Developer is the IDE assistant. Kiro is the newer spec-driven IDE from AWS, released late 2025, with stronger autonomy on multi-file tasks. Q Developer CLI is the terminal surface.

**Azure equivalent.** Foundry Agent Service is the runtime, baked into the Foundry portal with tight Entra ID and Microsoft Graph integration. No orchestration markup: you pay for the underlying tokens or PTU. Foundry IQ is the context layer, indexing SharePoint, Blob Storage, Fabric, and OneDrive without per-source pipeline work. Operational tools have their own pricing as noted above. Copilot Studio is the low-code builder and is the most polished of the three vendors' low-code agent products, partly because Microsoft has been iterating on the Bot Framework lineage for a decade. Semantic Kernel and AutoGen are the open-source frameworks, both Microsoft-led. GitHub Copilot is the IDE assistant with the broadest install base of any IDE AI tool.

**Pick GCP when** your team is already writing agents in Python and wants the cleanest runtime story (Agent Runtime plus ADK is the least surprising stack of the three). **Look elsewhere when** your agents need to act inside Microsoft 365 (Foundry IQ plus Copilot Studio wins), or when autonomous agent payments are part of the use case (AgentCore with x402 is the only first-party option today). Agent platforms are where lock-in is happening fastest in 2026. Picking one this year is a three-year decision.

---

## Specialized AI APIs

Most of the specialized AI surface is commodity. The APIs do roughly the same things, with measurable but not strategic quality differences. The strategic differences are in pricing models, regional availability, and ergonomics, not output quality.

Group these by purpose: perception versus generation.

### Perception

Vision AI (Cloud Vision API) does label detection, OCR, face detection, landmark detection, and safe-search. Vertex AI Vision is the cousin product for video analytics at the edge, with stream processing and event-based triggering for cameras and similar.

Document AI is the OCR-plus-extraction product. Pre-trained processors for invoices, receipts, W-2s, 1099s, contracts, and a generic form parser. Custom Document Extractor lets a team fine-tune extraction on their own document schemas with a few hundred labeled examples. The pricing model is per-page, with significant volume discounts for high-throughput pipelines.

Speech-to-Text uses Chirp 2 (released late 2025), USM-based architecture, support for 125+ languages plus on-device variants. Text-to-Speech has Studio voices for high-fidelity synthesis and Standard voices for cheap bulk synthesis. Both APIs are battle-tested.

Cloud Translation has two tiers. Basic is straightforward NMT for high-volume low-cost translation. Advanced supports glossaries, batch translation, and document translation with formatting preserved.

Natural Language API does entity extraction, sentiment analysis, syntax parsing, and content classification. The honest 2026 take: this product is mostly obsolete for new builds. Calling Gemini 3 Flash with a structured output schema gets better results at comparable cost for entity and sentiment work, with the bonus of supporting whatever custom schema the team needs.

**AWS equivalent.** Rekognition for vision, Textract for OCR, Transcribe and Polly for speech, Translate for translation, Comprehend for NLP. The capabilities map cleanly. Textract has a slight edge on form extraction for US tax and financial documents. Polly Neural2 voices are strong. Comprehend Medical exists as a HIPAA-aligned variant for clinical NLP.

**Azure equivalent.** Azure AI Vision, AI Document Intelligence, AI Speech, AI Translator, AI Language. Document Intelligence has the most mature pre-built layout model and is often the right choice for complex multi-column documents and tables. AI Speech has the best custom voice cloning story of the three (Custom Neural Voice).

### Generation

Imagen 4 is the current image generation model on Vertex, in public preview as of May 2026. Photorealistic output, strong prompt adherence, text rendering inside images that actually works. Virtual Try-On is the niche Imagen variant for ecommerce, swapping garments onto person images at production quality.

Veo 3.1 (GA July 2025) generates video up to 8 seconds at 1080p with synchronized audio, including dialogue and environmental sound. Veo 3.1 Lite is the cost-reduced preview variant released March 2026 for prototyping. Veo remains the most capable video model the three hyperscalers offer in May 2026, narrowly ahead of Sora on coherent multi-character scenes and notably ahead of Nova Reel on audio integration.

Lyria 3 (preview since 25 March 2026) is the music generation model, in two variants. Lyria 3 Pro generates full-length songs at $0.08 per song with 48kHz stereo output, including coherent vocals and instrumental arrangements. Lyria 3 Clip generates 30-second clips. No competing first-party music model exists on AWS or Azure in May 2026.

**AWS equivalent.** Nova Canvas v1 for image generation ($0.04 to $0.08 per image based on resolution), Nova Reel v1 for video ($0.08 per second, up to 2 minutes). Stability and Black Forest Labs models available through Bedrock Marketplace. Nova Reel's 2-minute length is the longest first-party video on any cloud, though output quality trails Veo.

**Azure equivalent.** GPT-image-2 is the current OpenAI image model on Foundry, supporting text-to-image, inpainting, and resolutions up to 3840px (3:1 ratio). Token-based pricing at $5 per 1M input tokens. FLUX.2-pro and FLUX.2-flex from Black Forest Labs entered preview in February 2026, with FLUX.2-flex specifically tuned for UI prototyping, complex typography, and multi-reference editing, an area where standard image models struggle. Pricing is $0.05 to $0.07 per generated megapixel.

Sora 2 is available through Azure OpenAI for video, but with a serious operational problem worth flagging. Microsoft has scheduled Sora 2 for early retirement on 3 June 2026, despite OpenAI's own API continuing support through 24 September 2026. This is the most disruptive lifecycle decision on any cloud in 2026 and is currently driving migration scrambles among Azure-resident media teams. Anyone betting production media workloads on Azure should plan around this gap before June.

One regional nuance worth flagging on generation models. Imagen, Veo, and Lyria are predominantly served from us-central1, us-east1, us-west1, us-west4, and europe-west4 on Vertex. Customers outside North America and Western Europe pay an egress tax for media generation unless they replicate workloads. Sora 2 on Azure is concentrated in East US 2, Sweden Central, and South Central US in the time it has left. Nova Reel is broadly available on AWS. For media-heavy workloads in regions outside North America and Western Europe, the right answer is often a multi-region deployment with caching, not picking a different cloud.

---

## Infrastructure and pricing

The silicon decision is the longest-shadow decision in the whole stack. Once a team trains or serves on a particular accelerator family, leaving it is a project, not a switch flip. This section covers what each cloud offers and where the real lock-in points are.

### GCP silicon

TPU Trillium (v6e) is the current general-purpose tensor accelerator, available in us-central1, us-east1, us-east5, europe-west4, and asia-northeast1. Each chip delivers 918 TFLOPs of peak BF16 compute and 1,836 TOPs of INT8, with 32 GB of HBM at 1,638 GiBps. Pods scale to 256 chips in a 2D torus topology. Inter-chip interconnect is 800 GBps per chip (6.4 Tbps aggregate across a 4-chip slice). Trillium adds third-generation SparseCores for embedding-heavy workloads, which materially helps ranking and recommendation systems. Pricing on Trillium runs roughly 30 to 40 percent cheaper per FLOP than equivalent H100 capacity on GCP for training workloads that fit the TPU programming model.

TPU Ironwood (v7) launched at Cloud Next 2026, specifically positioned for the "age of inference" rather than training. The architecture is a dual-chiplet design with 4,614 TFLOPs of peak FP8 compute per chip, 192 GB of HBM3e at 7.38 TB/s, and a move from 2D to 3D torus topology with pods scaling to 9,216 chips. Per-chip ICI bandwidth is 1,200 GBps bidirectional. The 192 GB memory footprint matters: it lets Ironwood serve Gemini 3's 1M token context with full KV cache resident, no memory thrashing. Google serves Gemini 3 internally on Ironwood, and external capacity is rolling out through 2026.

The TPU lock-in question is real. JAX and TensorFlow are first-class on TPU. PyTorch works via PyTorch/XLA but has rough edges, particularly around custom CUDA kernels that don't have XLA equivalents. Teams that need to train and serve on TPU should plan for a JAX-centric stack, which is a real cost and a real benefit. JAX is genuinely faster to iterate on than equivalent PyTorch code for many workloads, but the talent pool is smaller.

GPU on GCP is the alternative. H100, H200, and B200 are all available, with A3 Ultra (H100), A3 Mega (H100), and A4 (B200) machine types. The A4 instances became GA in early 2026 and are the production target for teams that want B200 capacity outside of CoreWeave or Crusoe. B200 itself delivers 4,500 TFLOPS FP8 with 192 GB HBM3e and 8 TB/s memory bandwidth, the benchmark Trillium, Ironwood, Trainium 3, and Maia 200 are all measured against.

TPU networking matters as much as TPU compute for anyone training at scale. Single-pod training (up to roughly 70B parameter models with standard memory footprints, typically using multi-slice deployments across 32 chips) is where TPU economics are most compelling. Above pod scale, Google connects pods through the data center network with a meaningful bandwidth drop. Multi-pod TPU configurations are available by reservation. Above that, the calculus shifts toward GPU clusters with NVLink and InfiniBand.

### Pricing models

Pay-as-you-go is the default. Committed Use Discounts (CUDs) save 20 to 57 percent for 1- or 3-year commitments. Dynamic Workload Scheduler is the newer flexible-commitment offering, with two flavors: Flex Start for non-time-critical training jobs that can wait minutes to hours for capacity, and Calendar Mode for reservations on specific future dates. Vertex AI Provisioned Throughput is the dedicated capacity model for Gemini inference, billed by Generative AI Scale Unit (GSU) rather than per-token, useful for predictable high-volume serving.

Free tier specifics. Google Cloud's standard $300 in credits for 90 days applies to all AI services. Express Mode for Agent Builder is a smaller, AI-specific credit pool aimed at building a first agent at zero cost. Vertex AI itself has no per-product free tier beyond the $300, though several specialized APIs (Translation, Speech, Vision) have small monthly free quotas.

**AWS equivalent.** Trainium 3, built on a 3nm process, is the current focal point: 2.52 PFLOPs of FP8 compute per chip, 144 GB of HBM3e at 4.9 TB/s, scaling to 144 chips per Trn3 UltraServer (362 FP8 PFLOPs total) over a proprietary NeuronSwitch-v1 fabric at 2 TB/s per chip. Trainium 3 supports MXFP8 and MXFP4 data types for the memory-to-compute balance needed in real-time multimodal reasoning. Trainium 2 is still widely deployed. Inferentia 2 handles dedicated inference. The Trainium story is similar to TPU: significant cost advantages for workloads that fit the Neuron SDK programming model, with portability friction for stacks built around CUDA. GPU instances on AWS are P5 (H100), P5en (H200), and P6 (B200). Savings Plans cover compute commitments, Bedrock Provisioned Throughput is the per-model dedicated capacity. Bedrock supports batch inference at a 50 percent discount versus on-demand, a real lever for high-throughput async pipelines.

**Azure equivalent.** Maia 200 is Microsoft's second-generation custom AI accelerator, in production since late January 2026 and explicitly positioned as an inference chip rather than a training chip. Built on TSMC's 3nm process, it delivers 5.07 PFLOPs of FP8 compute and 10.14 PFLOPs of FP4 (the FP4 emphasis is the differentiating bet), with 216 GB of HBM3e at 7 TB/s and 272 MB of on-die SRAM to keep large models fed during generation. Networking scales over standard Ethernet to clusters of up to 6,144 accelerators. Initial deployment is in US Central and US West 3. Maia 100 is the first-generation chip, still in use for non-frontier workloads. The Maia story is earlier than TPU or Trainium, with a smaller customer base and a less mature software stack. GPU on Azure is ND H100 v5, ND H200 v5, and ND B200 v6, broadly available. Azure Reservations cover compute commitments. Provisioned Throughput Units (PTUs) are the dedicated capacity model for Azure OpenAI and other Foundry models, with aggressive cached-input discounts (often 10x off) that materially change the economics of context-heavy serving.

**Pick GCP when** your training workload fits JAX or TensorFlow and you want TPU economics, or your inference workload is on Gemini and you want Ironwood pricing with the 1M token context resident in memory. **Look elsewhere when** your team is deep in PyTorch with custom CUDA kernels (any CUDA-first cloud beats TPU here), when your serving target is the freshest Claude (Bedrock has Opus 4.7), or when your serving target needs aggressive cached-input pricing on long context (Azure's PTU discounts on GPT-5 cached input are the most aggressive on the market). TPU lock-in is real but earned. Trainium 3 lock-in is similar but with a smaller community. Maia 200 lock-in is a small bet today, with the FP4-heavy positioning being either prescient or a niche optimization depending on how the next two years of inference workloads shape up.

---

## Security, networking, and compliance

This section covers the production-readiness layer that comparisons usually skip: how to keep traffic off the public internet, how to control encryption keys, what gets logged, and what survives a US CLOUD Act subpoena.

### Private connectivity

GCP uses VPC Service Controls to establish a security perimeter around the Gemini Enterprise Agent Platform, ensuring training data, prompts, and inference requests cannot leave the defined perimeter. Private Service Connect provides internal IPs for API access, keeping traffic on Google's private backbone.

AWS uses PrivateLink for Bedrock. Interface VPC endpoints for `bedrock-runtime` and `bedrock` live in a private subnet, and traffic stays inside the AWS network. AgentCore Gateway egress traffic is routed through customer-VPC elastic network interfaces, which lets an agent query private RDS or on-premises systems without public exposure.

Azure uses Private Endpoints to disable public access to Foundry hubs, Storage accounts, and Key Vaults. Agent outbound traffic uses VNet injection through delegated subnets. The pattern is the same across all three clouds: configure private endpoints, disable public access, force all traffic through the VPC.

### Encryption and key management

All three offer customer-managed keys. GCP Cloud KMS provides CMEK at project and resource granularity for Vertex AI. AWS Bedrock supports KMS Customer-Managed Keys, including for AgentCore Gateways where the granularity reaches per-tenant. Azure Key Vault and Azure Managed HSM cover Foundry, plus Customer Lockbox, which requires explicit, auditable customer approval before Microsoft support engineers can access a customer environment for troubleshooting. Customer Lockbox is the strictest of the three by default.

### Audit logging and data retention

GCP logs platform access through Cloud Audit Logs. AWS logs every model invocation, parameter change, and key decryption event through CloudTrail. Azure routes audit data through Azure Monitor and Activity Logs, integrated into the Foundry control plane for real-time observability.

All three hyperscalers contractually guarantee (via data processing addendums) that customer prompts and completions are not retained or used to train their foundation models. The contractual language is similar across vendors. Default behavior on each cloud is no-retention; opting into retention or sharing requires explicit configuration.

### Compliance certifications

The orchestration platforms (Bedrock, Foundry, Gemini Enterprise) inherit the major frameworks of their parent cloud: FedRAMP High in GovCloud or specialized government regions, HIPAA with standard BAAs, SOC 2 Type II, ISO 27001, PCI DSS scope adherence. For workloads under the EU AI Act, all three clouds publish readiness statements; the regulation's implementation timeline runs through 2026 and the practical differences between providers are still emerging.

### Data residency and sovereignty

For European and regulated customers, residency rules drive cloud choice more than capability comparison does. GCP runs Gemini 3 in europe-west4 (Netherlands), europe-west1 (Belgium), and europe-west3 (Frankfurt) with data-at-rest and processing residency guarantees.

AWS launched the AWS European Sovereign Cloud (ESC) into General Availability on 15 January 2026, with the first operational region in Brandenburg, Germany. The ESC is physically and logically separated from all other AWS regions, with independent billing, account management, and identity systems. Only EU residents can operate the infrastructure, and the corporate entity's managing directors must be EU nationals. The launch portfolio includes 90+ services, notably Bedrock, SageMaker, and EC2. The unresolved legal question: the ESC remains a subsidiary of the US-based Amazon parent, which leaves residual exposure to extraterritorial warrants under the US CLOUD Act. AWS has not divested the entity.

Azure relies on EU Data Boundary commitments and the "M365 Local" / Sovereign architecture, using partner operator models and European Data Guardians for data access oversight. Microsoft executives have testified publicly (most recently before the French Senate) that absolute cryptographic immunity from US data requests cannot be legally guaranteed given Microsoft's US domicile. For the strictest workloads, Azure supports Foundry Local, which runs models entirely offline on customer-controlled hardware.

GCP's sovereign offering details are less well-documented in May 2026. The publicly visible posture leans on Customer-Managed Encryption Keys, partner-operated regions (T-Systems in Germany, Thales in France), and zero-trust architecture rather than a structurally independent EU subsidiary along the AWS ESC pattern. A buyer evaluating sovereign requirements should treat this as a known gap and ask Google directly for current commitments.

All three clouds are pursuing alignment with BSI C5 (Germany) and ANSSI SecNumCloud (France). AWS ESC has the cleanest current public posture on operational sovereignty in the GA window. Azure has the deepest pre-existing accreditations for UK government, defense, and healthcare. GCP's sovereign path is the least crisp of the three.
