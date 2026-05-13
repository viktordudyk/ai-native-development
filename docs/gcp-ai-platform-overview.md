# The GCP AI Stack, With AWS and Azure on the Other Side of the Table

*Created: 13 May 2026*

Anyone consulting on AI architecture in 2026 needs a working model of all three hyperscaler stacks, even if their primary deployment is on one of them. Clients ask. RFPs ask. A board member who read a Bloomberg piece asks. Worse, the platforms renamed themselves again in May 2026. Vertex AI consolidated as Gemini Enterprise Agent Platform at Cloud Next. Azure AI Foundry dropped the "Azure" and became Microsoft Foundry. AWS pulled Bedrock, SageMaker Unified Studio, and AgentCore into a single agentic stack story. The branding is new. The capabilities mostly are not.

This document is a reference for teams whose center of gravity is Google Cloud but who need to explain or defend that choice when clients want to know what the AWS or Azure equivalent looks like. GCP-first, with the equivalents called out inline. Not a buying guide. Not a benchmark. Not a marketing piece for any one of the three.

Scope limits worth stating up front. Hyperscaler-only, so no Snowflake, Databricks, Oracle, or Alibaba. No deep coverage of data-side AI products like BigQuery ML, Looker, or Fabric. The information here is current as of May 2026 and the SKU names will rot within the year.

---

## The cross-cloud cheat sheet

| Capability | GCP | AWS | Azure |
|---|---|---|---|
| Unified AI platform | Gemini Enterprise Agent Platform (formerly Vertex AI) | SageMaker Unified Studio + Bedrock | Microsoft Foundry (formerly Azure AI Foundry) |
| Foundation model hub | Vertex AI Model Garden, 200+ models | Amazon Bedrock | Foundry Models, 1,900+ entries |
| First-party LLMs | Gemini 3 Pro, 3 Flash, 3.1 Pro | Amazon Nova (Pro, Lite, Micro), Titan | GPT-5, 5.1, 5.2, 5.3-codex via Azure OpenAI |
| Anthropic models | Claude Opus 4.6 (Vertex) | Claude Opus, Sonnet, Haiku 4.5 | Claude via Marketplace |
| Image generation | Imagen 4, Virtual Try-On | Nova Canvas, Stability | GPT-image-1.5, FLUX.2 [pro], DALL-E |
| Video generation | Veo 3.1, Veo 3.1 Lite | Nova Reel | Sora via Foundry |
| Music generation | Lyria 3 (preview) | none | Audio models (GA Dec 2025) |
| Agent runtime | Vertex AI Agent Engine | Bedrock AgentCore | Foundry Agent Service |
| Agent framework | Agent Development Kit (ADK) | Strands, Bedrock Agents | Semantic Kernel, AutoGen |
| Low-code builder | Agent Builder, Express Mode | Bedrock Agents | Copilot Studio |
| RAG and knowledge | Vertex AI Search + Vector Search 2.0 | Bedrock Knowledge Bases | Foundry IQ + Azure AI Search |
| IDE assistant | Gemini Code Assist | Amazon Q Developer, Kiro | GitHub Copilot |
| CLI assistant | Gemini CLI | Q Developer CLI | GitHub Copilot CLI |
| Vision API | Vision AI | Rekognition | Azure AI Vision |
| Document AI | Document AI | Textract | AI Document Intelligence |
| Speech | Speech-to-Text (Chirp), TTS | Transcribe, Polly | Azure AI Speech |
| Translation | Cloud Translation | Translate | AI Translator |
| NLP | Natural Language API | Comprehend | AI Language |
| Training silicon | TPU Trillium (v6), Ironwood (v7) | Trainium 2, Trainium 3 | Maia 100, Maia 200 |
| GPU options | H100, H200, B200 | P5, P5en, P6 | ND H100/H200 v5/v6 |
| MCP support | Cloud API Registry | AgentCore Tool Gateway | Foundry Agent Service |
| Free tier headline | $300 in credits, 90 days | 12 months free tier across services | $200 in credits, 30 days |

The rest of this doc walks each row in more depth.

---

## Core AI/ML platform

Gemini Enterprise Agent Platform is the new umbrella name for what used to be Vertex AI. The rename matters less than the consolidation underneath it. Google folded Model Garden, Vertex AI Search, Vector Search 2.0, AutoML, the agent runtime, pipelines, registry, feature store, and evaluation tooling under a single billing surface and a single IAM model. The Vertex AI brand still appears in URLs and SDK names. For anyone writing IaC against `aiplatform.googleapis.com`, nothing broke. The rebrand is mostly marketing and consolidated docs.

What actually changed at Cloud Next 2026:

- Gemini 3 Pro replaced Gemini 1.5 Pro as the default flagship. 1 million token context, native multimodal input across text, code, video, audio, and PDF, plus a Live API for sub-second streaming.
- Gemini 3 Flash and Gemini 3.1 Pro round out the family. Flash for cost-sensitive bulk inference. 3.1 Pro for harder reasoning.
- Claude Opus 4.6 is available on Vertex through the Model Garden partnership with Anthropic, billed against Google Cloud spend.
- Model Garden crossed 200 first-party and partner models, including DeepSeek, GLM 5, MiniMax, Mistral, Llama 4, and a long tail of fine-tuned open weights.

Picking among the Gemini variants is straightforward in practice. Gemini 3 Pro is the default for anything that requires reasoning over long inputs, agent planning, or multimodal understanding. Gemini 3 Flash is the right call for high-volume classification, extraction, summarization, and the bulk of routine inference work. The price gap is roughly 12x, and Flash matches Pro on most non-reasoning tasks. Gemini 3.1 Pro is the small reasoning upgrade for problems where 3 Pro hits its ceiling: complex code generation, multi-step math, deep RAG synthesis. Claude Opus 4.6 on Vertex is the choice for teams who want Anthropic's particular strengths on instruction following and writing quality but who need to keep all spend on Google Cloud. A pragmatic team will use three of these in production: Flash for the high-volume layer, 3 Pro as the workhorse, and either 3.1 Pro or Claude for the hardest queries with a router in front.

Vertex AI Search is the managed RAG and enterprise search product. Out of the box it indexes Cloud Storage, BigQuery, SharePoint, Confluence, Salesforce, Jira, and a long list of others. Vector Search 2.0 is the underlying vector database, with index types for filtered ANN, hybrid (dense plus sparse) retrieval, and grounding callbacks built directly into Gemini API calls. For most teams, Vertex AI Search is the right answer for RAG on GCP. Rolling your own vector storage in AlloyDB or BigQuery only makes sense when you need ACID semantics on the same store as your transactional data, or when your reranker is custom enough to outweigh the integration tax.

The cost arithmetic on RAG is worth checking before committing. Vertex AI Search bills per query (around $4 per 1,000 queries with Enterprise edition) plus storage. Self-hosting on AlloyDB with pgvector is roughly half the per-query cost at scale, but the human cost of ingestion pipelines, chunking, embedding choice, reranker tuning, and metrics adds up to a small team's quarter. For most teams under 100,000 queries per day, the math favors Vertex AI Search. Past that volume, the question is real, and the answer is usually a hybrid: Vertex AI Search for the long tail, custom for the hot path.

Grounding is the feature that separates Vertex from a raw model API. Gemini calls on Vertex can ground responses against Google Search (giving the model live web access with citations), against Vertex AI Search (custom corpora), or against both. Web grounding is the killer feature for anything that needs current facts (regulatory changes, market data, news synthesis). AWS and Azure both offer custom-corpus grounding, but neither has a first-party live web grounding equivalent of comparable quality. Bedrock added a web search action in late 2025, and Foundry has Bing grounding through a separate connector, but Google's Search index plus integration depth is the wider moat in this category.

MLOps on Vertex is mature in the way that mature things are also boring. Vertex AI Pipelines uses Kubeflow Pipelines under the hood with a Google-managed control plane. Model Registry, Feature Store, Experiments, and Model Monitoring are all GA and reasonably well-instrumented. Vertex AI Evaluation Service is the newer piece, focused on LLM and agent evaluation with metrics for groundedness, instruction following, safety, and rubric-based scoring against custom criteria. The Evaluation Service is what makes Gemini Enterprise Agent Platform credible for production agents. Without it, every team builds their own eval harness, which is a known way to ship buggy agents.

AutoML is still there for tabular, image, video, and text tasks, but the center of gravity moved to Gemini fine-tuning. For most use cases that would have been AutoML in 2023, the recommendation is now LoRA or full fine-tuning on Gemini 3 Flash, deployed to a managed endpoint. AutoML Tables specifically was folded into BigQuery ML and is no longer a Vertex product.

**AWS equivalent.** Amazon Bedrock is the model hub. SageMaker Unified Studio is the workbench. The two are stitched together as of late 2025 but still feel like two products with a shared sign-on. Bedrock hosts Claude Opus, Sonnet, and Haiku 4.5 (Anthropic's primary cloud), Llama, Mistral, Cohere, Stability, and Amazon Nova. Bedrock Knowledge Bases is the RAG offering, with OpenSearch Serverless as the default vector store and recent additions for Aurora pgvector and MongoDB Atlas. SageMaker Pipelines and Model Registry play the same role as their Vertex counterparts, with the usual AWS pattern of more knobs and more glue. The Anthropic relationship is the structural difference. If your team has standardized on Claude, Bedrock has the most recent and most complete Anthropic catalog, while Vertex trails by a model generation.

**Azure equivalent.** Microsoft Foundry consolidated what was Azure AI Foundry, Azure OpenAI Service, and parts of Azure ML into a single portal. Foundry Models advertises 1,900+ entries, though the headline number includes long-tail variants and fine-tunes. The strategic depth is in Azure OpenAI: GPT-5, GPT-5.1, GPT-5.2, and GPT-5.3-codex are the current generation, exclusively on Azure outside OpenAI's own API. Azure AI Search is the RAG product, mature and well-integrated with Microsoft 365 sources via Foundry IQ. Azure ML provides the MLOps surface, with MLflow as the default tracking story and a noticeably less opinionated pipeline framework than Vertex Pipelines or SageMaker Pipelines. For teams already on Entra ID and Microsoft 365, Foundry has the shortest path from "we have a SharePoint corpus" to "we have a chatbot grounded on that corpus".

**Pick GCP when** your reasoning workload benefits from Gemini 3's 1M token context window, you need native multimodal handling without pipeline glue, or your data already lives in BigQuery. **Look elsewhere when** Anthropic model recency matters more than anything else (AWS leads), or your identity and document plane is already Microsoft 365 (Azure leads). The Vector Search story on GCP is good but not unique. Don't pick GCP for vector search alone.

---

## Agents and developer tools

Agents on GCP layer as framework, runtime, builder, and dev surface. Each piece is usable on its own, but the integration story is the actual product.

Agent Development Kit (ADK) is the framework. Open-source, Python and Go, modeled on the same patterns as LangGraph but with Google's own primitives for tool use, planning, multi-agent handoff, and memory. ADK works against any Gemini or Vertex-hosted model and can be deployed to anything that runs Python, but the canonical target is Agent Engine.

Vertex AI Agent Engine is the managed runtime. Persistent agent sessions, conversation memory up to 8 hours, sandboxed code execution, integration with Vertex AI Search for grounding, and IAM-aware tool calling. Agent Engine costs more than running ADK on Cloud Run but absorbs a meaningful pile of plumbing: session state, retries, tracing, evals integration, and the long tail of "your agent ran for 40 minutes and then died, where do we look".

The actual cost gap is smaller than it looks at first glance. A typical Agent Engine session bills around $0.0014 per minute plus the underlying model tokens. The same agent on Cloud Run with self-managed Redis for sessions runs about 30 percent cheaper if utilization stays high, but the moment you have to add tracing, retries, multi-region failover, and an eval pipeline, the gap closes. Most teams that benchmark this end up on Agent Engine for production and Cloud Run for offline experimentation.

Agent Builder is the low-code surface. Drag-and-drop tool wiring, natural language goal specification, and a deployment path that ends at Agent Engine. Express Mode (the free tier for Agent Builder) gives ~$300 of usage credits and lets a non-engineering team get a functional internal agent live in an afternoon. It is genuinely useful for prototyping. It is not where most production agents will live.

Gemini Code Assist is the IDE integration. VS Code, JetBrains, Android Studio, Cloud Workstations, with chat, code completion, transformation, and inline explanations. The differentiator versus GitHub Copilot is repository-scale context. Code Assist Enterprise indexes a customer's private repo and surfaces results during inline generation. For monorepo shops, this matters more than benchmark scores.

Gemini CLI is the terminal counterpart. Released GA in late 2025, refreshed at Cloud Next 2026 with extension support, slash commands, multi-file editing, and an MCP server registry. It is the closest GCP-native analogue to Claude Code or Codex CLI, and the gap has closed enough that picking between them is a question of model preference and ecosystem rather than feature parity.

MCP support across the platform. Google shipped a Cloud API Registry that exposes GCP services (BigQuery, Cloud Run, Spanner, Vertex AI, and the long tail) as MCP servers, so an agent built on any framework can call them. Vertex AI Agent Engine accepts MCP servers as tools natively. Gemini CLI has an MCP server registry. The story is internally consistent and competitive with Anthropic's MCP push, which started the standard.

**AWS equivalent.** AgentCore is the runtime, GA since mid-2025. Persistent memory, tool gateway with MCP support, identity integration, and observability. AgentCore is the spiritual cousin to Agent Engine and the architectural choice is similar. Strands is the open-source framework, smaller community than ADK but well-documented. Bedrock Agents is the low-code builder, oriented around Bedrock Knowledge Bases as the grounding layer. Amazon Q Developer is the IDE assistant, with strong AWS-specific awareness (CDK, CloudFormation, SDK call patterns). Kiro is the newer spec-driven IDE from AWS, released late 2025, with stronger autonomy than Q Developer on multi-file tasks. Q Developer CLI is the terminal surface.

**Azure equivalent.** Foundry Agent Service is the runtime, baked into the Foundry portal with tight Entra ID and Microsoft Graph integration. Foundry IQ is the context layer, indexing SharePoint, Blob Storage, Fabric, and OneDrive without per-source pipeline work. Copilot Studio is the low-code builder and is the most polished of the three vendors' low-code agent products, partly because Microsoft has been iterating on the Bot Framework lineage for a decade. Semantic Kernel and AutoGen are the open-source frameworks, both Microsoft-led. GitHub Copilot is the IDE assistant with the broadest install base of any IDE AI tool, and Copilot's agent mode plus its CLI close most of the gap with Claude Code on terminal-driven workflows.

**Pick GCP when** your team is already writing agents in Python and wants the cleanest runtime story (Agent Engine plus ADK is the least surprising stack of the three). **Look elsewhere when** your agents need to act inside Microsoft 365 (Foundry IQ plus Copilot Studio wins), or when MCP-driven tool gateways and the most mature production agent runtime matter more than developer ergonomics (AgentCore leads). Agent platforms are where lock-in is happening fastest in 2026. Picking one this year is a three-year decision.

---

## Specialized AI APIs

Most of the specialized AI surface is commodity. The APIs do roughly the same things, with measurable but not strategic quality differences. The strategic differences are in pricing models, regional availability, and ergonomics, not output quality.

Group these by purpose: perception versus generation.

### Perception

Vision AI (Cloud Vision API) does label detection, OCR, face detection, landmark detection, and safe-search. Vertex AI Vision is the cousin product for video analytics at the edge, with stream processing and event-based triggering for cameras and similar.

Document AI is the OCR-plus-extraction product. Pre-trained processors for invoices, receipts, W-2s, 1099s, contracts, and a generic form parser. Custom Document Extractor lets a team fine-tune extraction on their own document schemas with a few hundred labeled examples. The pricing model is per-page, with significant volume discounts for high-throughput pipelines.

Speech-to-Text uses Chirp 2 (released late 2025), USM-based architecture, support for 125+ languages plus on-device variants. Text-to-Speech has Studio voices for high-fidelity synthesis and Standard voices for cheap bulk synthesis. Both APIs are battle-tested.

Cloud Translation has two tiers. Basic is straightforward NMT for high-volume low-cost translation. Advanced supports glossaries, batch translation, and document translation with formatting preserved.

Natural Language API does entity extraction, sentiment analysis, syntax parsing, and content classification. The honest 2026 take: this product is mostly obsolete for new builds. Calling Gemini 3 Flash with a structured output schema gets better results at comparable cost for entity and sentiment work, with the additional benefit of supporting whatever custom schema the team needs. The Natural Language API stays in the catalog for compliance and stability, not because it's the right tool for a greenfield project.

**AWS equivalent.** Rekognition for vision, Textract for OCR, Transcribe and Polly for speech, Translate for translation, Comprehend for NLP. The capabilities map cleanly. Textract has a slight edge on form extraction for US tax and financial documents. Polly Neural2 voices are strong. Comprehend Medical exists as a HIPAA-aligned variant for clinical NLP.

**Azure equivalent.** Azure AI Vision, AI Document Intelligence, AI Speech, AI Translator, AI Language. Document Intelligence has the most mature pre-built layout model and is often the right choice for complex multi-column documents and tables. AI Speech has the best custom voice cloning story of the three (Custom Neural Voice).

### Generation

Imagen 4 is the current image generation flagship on Vertex. Photorealistic output, strong prompt adherence, text rendering inside images that actually works. Virtual Try-On is the niche Imagen variant for ecommerce, swapping garments onto person images at production quality.

Veo 3.1 generates video up to 8 seconds at 1080p with synchronized audio, including dialogue and environmental sound. Veo 3.1 Lite is the cost-reduced variant for prototyping. Veo is the most capable video model the three hyperscalers offer in May 2026, narrowly ahead of Sora on coherent multi-character scenes and notably ahead of Nova Reel on audio integration.

Lyria 3 is the music generation model, in preview as of Cloud Next 2026. It generates instrumental and vocal tracks in a range of styles, with controls for tempo, key, and instrument selection. No competing first-party music model exists on AWS or Azure in May 2026.

**AWS equivalent.** Nova Canvas for image generation, Nova Reel for video. Stability and Black Forest Labs models available through Bedrock Marketplace. Nova Reel is competitive on speed but trails Veo on output quality and audio integration.

**Azure equivalent.** GPT-image-1.5 is the current OpenAI image model, available through Foundry. FLUX.2 [pro] from Black Forest Labs is the strongest non-OpenAI image option on Azure. Sora is available through Azure OpenAI for video. No first-party music model.

One regional nuance worth flagging on generation models. Imagen, Veo, and Lyria are predominantly served from us-central1, us-east1, and europe-west4 on Vertex, with rolling expansion. Customers in APAC pay an egress tax for media generation unless they replicate workloads. Sora on Azure has a similar rollout pattern, with Sweden Central and West US 3 as the primary hosts. Nova Reel is broadly available on AWS but trails on output quality. For media-heavy workloads in regions outside North America and Western Europe, the right answer is often a multi-region deployment with caching, not picking a different cloud.

---

## Infrastructure and pricing

The silicon decision is the longest-shadow decision in the whole stack. Once a team trains or serves on a particular accelerator family, leaving it is a project, not a switch flip. This section covers what each cloud offers and where the real lock-in points are.

### GCP silicon

TPU Trillium (v6) is the current production tensor accelerator, available in zones across us-central1, us-east1, us-east5, europe-west4, and asia-northeast1. Trillium delivers about 4.7x the peak compute of TPU v5e per chip, with 32GB of HBM and 1.6 TB/s memory bandwidth. Pricing on Trillium is roughly 30 to 40 percent cheaper per FLOP than equivalent NVIDIA H100 capacity on GCP for training workloads that fit the TPU programming model.

TPU Ironwood (v7) launched at Cloud Next 2026, aimed at inference rather than training. 4,614 TFLOPs of FP8 compute per chip, 192GB HBM, designed for serving frontier models at production scale. Ironwood is the chip Google uses to serve Gemini 3 internally, and capacity is rolling out to external customers through 2026.

The TPU lock-in question is real. JAX and TensorFlow are first-class on TPU. PyTorch works via PyTorch/XLA but has rough edges, particularly around custom CUDA kernels that don't have XLA equivalents. Teams that need to train and serve on TPU should plan for a JAX-centric stack, which is a real cost and a real benefit. JAX is genuinely faster to iterate on than equivalent PyTorch code for many workloads, but the talent pool is smaller.

GPU on GCP is the alternative. H100, H200, and B200 are all available, with A3 Ultra (H100), A3 Mega (H100), and A4 (B200) machine types. The A4 instances became GA in early 2026 and are the production target for teams that want B200 capacity outside of CoreWeave or Crusoe.

TPU networking matters as much as TPU compute for anyone training at scale. A Trillium pod is up to 256 chips connected by a 3D torus interconnect at 4.8 Tbps per chip. Multi-host JAX training across a pod is genuinely transparent; the same code runs on 8 chips or 256. Above pod scale, Google connects pods via the data center network with a meaningful bandwidth drop. The implication is that single-pod training jobs (up to roughly 70B parameter models with standard memory footprints) are where TPU economics are most compelling. Above that, the calculus shifts toward GPU clusters with NVLink and InfiniBand or toward newer multi-pod TPU configurations available by reservation.

### Pricing models

Pay-as-you-go is the default. Committed Use Discounts (CUDs) save 20 to 57 percent for 1- or 3-year commitments. Dynamic Workload Scheduler is the newer flexible-commitment offering, with two flavors: Flex Start for non-time-critical training jobs that can wait minutes to hours for capacity, and Calendar Mode for reservations on specific future dates. Vertex AI Provisioned Throughput is the dedicated capacity model for Gemini inference, billed by Generative AI Scale Unit (GSU) rather than per-token, useful for predictable high-volume serving.

Free tier specifics. Google Cloud's standard $300 in credits for 90 days applies to all AI services. Express Mode for Agent Builder is a smaller, AI-specific credit pool aimed at building a first agent at zero cost. Vertex AI itself has no per-product free tier beyond the $300, though several specialized APIs (Translation, Speech, Vision) have small monthly free quotas.

**AWS equivalent.** Trainium 2 is GA. Trainium 3 was announced at re:Invent 2025 and is rolling out through 2026. Inferentia 2 handles inference. The Trainium story is similar to TPU: significant cost advantages for workloads that fit the Neuron SDK programming model, with portability friction for stacks built around CUDA. GPU instances on AWS are P5 (H100), P5en (H200), and P6 (B200), with capacity broadly available in us-east-1 and us-west-2 first. Savings Plans cover compute commitments, Bedrock Provisioned Throughput is the per-model dedicated capacity. Free tier includes 12 months of small-instance time across many services.

**Azure equivalent.** Maia 100 is Microsoft's first-generation custom AI accelerator. Maia 200 was announced at Ignite 2025 and is rolling out through 2026. The Maia story is earlier than TPU or Trainium, with a smaller customer base and a less mature software stack. GPU on Azure is ND H100 v5, ND H200 v5, and ND B200 v6, broadly available. Azure Reservations cover compute commitments. Provisioned Throughput Units (PTUs) are the dedicated capacity model for Azure OpenAI and other Foundry models. Free tier is $200 in credits for 30 days.

**Pick GCP when** your training workload fits JAX or TensorFlow and you want TPU economics, or your inference workload is on Gemini and you want Ironwood pricing. **Look elsewhere when** your team is deep in PyTorch with custom CUDA kernels (any CUDA-first cloud beats TPU here), or when your serving target is Claude (Bedrock has the freshest Anthropic generation and the best Anthropic-specific pricing). TPU lock-in is real but earned. Trainium lock-in is similar but with a smaller community. Maia lock-in is not yet a question worth asking, since the volume is small and the software stack still maturing.

### Data residency and compliance

For European and regulated customers, residency rules drive cloud choice more than capability comparison does. GCP runs Gemini 3 in europe-west4 (Netherlands), europe-west1 (Belgium), and europe-west3 (Frankfurt) with data-at-rest and processing residency guarantees. The Sovereign Cloud offering through partners (T-Systems in Germany, Thales in France) gives stricter controls including operational sovereignty. AWS offers similar through the EU Sovereign Cloud (launching late 2025 in Brandenburg) and existing Frankfurt and Stockholm regions for Bedrock. Azure's Sovereign Cloud story is the most mature of the three for European customers, with EU Data Boundary commitments covering Microsoft Foundry by default. For UK government, defense, or healthcare workloads, Azure still has the deepest accreditations. For German Schrems II concerns, the GCP and AWS sovereign offerings are now competitive but newer.
