# AI/ML Platform State of the Union: Google Cloud, AWS, and Azure (May 2026)

*Source: Gemini Deep Research output, fed by the prompt in [promts/03-gcp-deep-research.md](../../promts/03-gcp-deep-research.md). Saved 13 May 2026. Used to fact-check and enrich [docs/gcp-ai-platform-overview.md](../gcp-ai-platform-overview.md). Citations preserved from the original report.*

---

## Platform Names and Consolidation as of May 2026

The enterprise artificial intelligence landscape underwent a massive architectural and branding consolidation throughout late 2025 and early 2026. The three hyperscalers have universally pivoted from offering isolated foundation model application programming interfaces (APIs) toward integrated, agent-centric orchestration platforms. This shift reflects a maturing market where raw inference is becoming commoditized, and the primary enterprise value is derived from multi-agent orchestration, robust state management, and unified governance.

### Google Cloud Platform

Google executed a total deprecation of the "Vertex AI" brand identity during the opening keynote of Google Cloud Next on April 22, 2026. The suite previously known as Vertex AI was comprehensively rebranded and architecturally absorbed into the Gemini Enterprise Agent Platform. This transition was not merely a cosmetic marketing update; Google made a deliberate engineering shift to force developers toward building autonomous agents rather than simply executing stateless calls against raw model APIs.

Under this new unified umbrella, legacy tools have been systematically realigned to fit the agentic paradigm. The infrastructure formerly designated as the Vertex AI Agent Engine was officially restructured and renamed to "Agent Runtime". Furthermore, the broader conversational interface environment known internally as Google Agentspace was consolidated into a unified employee-facing product simply called the "Gemini Enterprise" app. The foundational components of the platform, such as the Model Garden and the Retrieval-Augmented Generation (RAG) engine, remain intact but are now strictly governed under the Gemini Enterprise Agent Platform control plane. This structural consolidation aligns with Google's strategy to own the complete lifecycle of agent development, offering an ecosystem that integrates Managed Model Context Protocol (MCP) servers and the Agent2Agent (A2A) communication protocol directly into the core platform.

### Microsoft Azure

Microsoft initiated a parallel consolidation strategy, formally rebranding "Azure AI Foundry" to Microsoft Foundry effective January 1, 2026. This transition was initially announced at the Microsoft Ignite conference on November 18, 2025, and signals a fundamental strategic repositioning. By explicitly dropping the "Azure" prefix, Microsoft is positioning the platform as a cross-ecosystem enterprise factory rather than an isolated cloud infrastructure service.

The unified Microsoft Foundry environment elevates workflows and agents to first-class system primitives. It merges the model catalog, custom tool integration via Model Context Protocol (MCP), and the "Foundry IQ" enterprise retrieval engine into a single administrative control plane. From an architectural standpoint, the transition eliminates the fragmentation that previously existed between Azure OpenAI Studio, Copilot Studio, and Azure Machine Learning, centralizing role-based access control (RBAC), evaluation metrics, and network policies under one namespace. All existing Azure AI Foundry deployments were automatically transitioned to the new naming convention without requiring infrastructure migrations.

### Amazon Web Services

In contrast to the total consolidation approaches of Google and Microsoft, Amazon Web Services (AWS) has maintained a highly decoupled architectural philosophy. AWS distinctly separates its serverless inference, agentic orchestration, and custom machine learning hosting environments into specific services. As of May 2026, the AWS generative AI stack relies on a tripartite relationship between Amazon Bedrock, SageMaker Unified Studio, and Bedrock AgentCore.

Amazon Bedrock functions as the foundational serverless inference gateway, providing secure, API-driven access to a wide array of first-party and third-party foundation models without requiring users to manage the underlying compute clusters. Operating above this inference layer is Bedrock AgentCore, which serves as the dedicated orchestration and workflow engine. AgentCore abstracts the complex infrastructure required for multi-agent systems, handling dynamic intent analysis, parallel execution, and state persistence. A significant development for Bedrock AgentCore occurred on May 7, 2026, when AWS integrated native autonomous microtransaction capabilities via the x402 protocol, allowing agents to execute financial transactions through Coinbase and Stripe wallets during their reasoning loops.

SageMaker Unified Studio operates as the dedicated developer interface and custom machine learning hosting plane. It allows engineering teams to fine-tune open-weight models, deploy custom predictive models, and expose them as dedicated SageMaker endpoints. The critical relationship between these services is facilitated by the AgentCore Gateway, which securely routes natural language requests from the Bedrock conversational layer directly into specialized, production-grade machine learning models hosted on SageMaker, creating a unified intelligence system that spans conversational AI and traditional predictive analytics.

---

## Foundation Models: Current Versions, IDs, and Pricing

The pricing structures for frontier foundation models have stabilized, with vendors heavily incentivizing prompt caching to reduce computational overhead. Both Anthropic and Google offer steep discounts for cached context, fundamentally altering the economics of long-context applications. The following details the current, generally available (GA) frontier and efficiency models across the three clouds as of May 2026.

### Google Cloud (Gemini Enterprise Agent Platform)

The Gemini 3 series represents Google's current frontier architecture, introducing dynamic thinking budgets, native multimodal reasoning, and a standardized 1,000,000 token context window across the flagship models. The transition to this new architecture was mandated when the previous generation's `gemini-3-pro-preview` endpoint was abruptly discontinued on March 26, 2026, forcing a hard migration to the 3.1 architecture.

| Model Family | Exact Model ID | Context (Tokens) | Input Pricing (per 1M) | Output Pricing (per 1M) | GA / Preview Date | Modalities |
|---|---|---|---|---|---|---|
| Gemini 3.1 Pro | `gemini-3.1-pro-preview` | 1,000,000 | $2.00 (≤200K), $4.00 (>200K) | $12.00 (≤200K), $24.00 (>200K) | Feb 19, 2026 (Preview) | Text, Audio, Image, Video, Code |
| Gemini 3.1 Pro Custom | `gemini-3.1-pro-preview-customtools` | 1,000,000 | $2.00 (≤200K), $4.00 (>200K) | $12.00 (≤200K), $24.00 (>200K) | Feb 23, 2026 (Preview) | Text, Audio, Image, Video, Code |
| Gemini 3 Flash | `gemini-3-flash-preview` | 1,000,000 | $0.50 | $3.00 | Jan 2026 (Preview) | Text, Audio, Image, Video |
| Gemini 3.1 Flash-Lite | `gemini-3.1-flash-lite` | 1,000,000 | $0.25 (Text/Image/Video), $0.50 (Audio) | $1.50 | May 7, 2026 (GA) | Text, Audio, Image, Video |

Context caching for the Gemini 3 series is highly optimized, priced at $0.025 per 1M tokens for text, image, and video inputs, and $0.05 per 1M tokens for audio inputs, alongside a $1.00 per 1M tokens per hour baseline storage fee. In the broader ecosystem, the Google Cloud Model Garden now hosts more than 200 first-party and partner models, providing a comprehensive repository for open-weight deployments. The Claude family is fully supported natively on Vertex AI, with Claude Opus 4.5, Claude Sonnet 4.6, and Claude Haiku 4.5 available for enterprise routing.

### Amazon Web Services (Amazon Bedrock)

AWS hosts Anthropic's Claude family using dateless snapshot IDs alongside its proprietary Amazon Nova suite. The Nova architecture is deliberately tiered, providing distinct operating points optimized for highly specific latency and cost requirements across multimodal workflows.

| Model Family | Exact Model ID | Context (Tokens) | Input Pricing (per 1M) | Output Pricing (per 1M) | GA Date | Modalities |
|---|---|---|---|---|---|---|
| Claude Opus 4.7 | `anthropic.claude-opus-4-7` | 1,000,000 | $5.00 | $25.00 | April 2026 | Text, Image, Vision |
| Claude Sonnet 4.6 | `anthropic.claude-sonnet-4-6` | 1,000,000 | $3.00 | $15.00 | Feb 17, 2026 | Text, Image, Vision |
| Claude Haiku 4.5 | `anthropic.claude-haiku-4-5-20251001-v1:0` | 200,000 | $1.00 | $5.00 | Oct 15, 2025 | Text, Image, Vision |
| Amazon Nova Premier | `amazon.nova-premier-v1:0` | Not strictly defined | $1.25 | $6.25 | April 30, 2025 | Text, Image, Video, Doc |
| Amazon Nova Pro | `amazon.nova-pro-v1:0` | 300,000 | $0.80 | $3.20 | Dec 2, 2024 | Text, Image, Video, Doc |
| Amazon Nova Lite | `amazon.nova-lite-v1:0` | 300,000 | $0.06 | $0.24 | Dec 2, 2024 | Text, Image, Video, Doc |
| Amazon Nova Micro | `amazon.nova-micro-v1:0` | 128,000 | $0.035 | $0.14 | Dec 5, 2024 | Text Only |

Bedrock also hosts the Amazon Titan family, primarily utilized for embedding generation, where Titan Embeddings V2 operates at $0.00011 per 1,000 input tokens. AWS Bedrock uniquely supports batch inference for leading foundation models at a 50% discount compared to on-demand pricing, optimizing costs for high-throughput, asynchronous workloads. Anthropic's Claude Opus 4.6, despite being superseded by Opus 4.7, remains available on the platform for legacy pipeline support.

### Microsoft Azure (Microsoft Foundry)

Microsoft's Azure OpenAI portfolio is heavily centered around the GPT-5 series, which introduces varying context configurations and specialized agentic variants optimized for coding and logical reasoning workloads. Azure extensively utilizes cached input mechanisms to drive down the effective cost of large context processing.

| Model Family | Exact Model ID | Context (Tokens) | Input Pricing (per 1M) | Output Pricing (per 1M) | GA / Release Date | Modalities |
|---|---|---|---|---|---|---|
| GPT-5.4 | `gpt-5.4` | 1,050,000 | $2.50 | $15.00 | Mar 4, 2026 | Text, Image |
| GPT-5.3 Codex | `gpt-5.3-codex` | 400,000 | $1.75 | $14.00 | Feb 23, 2026 | Text, Image, Code |
| GPT-5.2 | `gpt-5.2` | 400,000 | $1.75 | $14.00 | Dec 10, 2025 | Text, Image |
| GPT-5.1 | `gpt-5.1` | 400,000 | $1.25 | $10.00 | Nov 12, 2025 | Text, Image |

Azure heavily leverages cached inputs for the GPT-5 series, bringing the effective input price of the GPT-5.3 Codex model down from $1.75 to an incredibly competitive $0.18 per 1M cached tokens. Furthermore, the Microsoft Foundry catalog has expanded dramatically to include over 11,000 foundation, open, reasoning, and multimodal models. This vast repository includes the full Anthropic Claude lineage available via Marketplace billing, allowing enterprises to utilize models like Claude Opus 4.7 and Claude Sonnet 4.6 directly within the Azure security boundary.

---

## Image, Video, and Music Generation Models

The multimodal landscape has diverged rapidly as the hyperscalers seek dominance in generative media. Google maintains a strictly proprietary, vertically integrated stack for media generation, deeply linking its models to its hardware. Conversely, AWS and Azure rely on a hybrid approach, mixing first-party developments with heavy reliance on third-party partnerships to supplement their native offerings.

| Platform | Model Name | Current Version | GA / Preview Status | Key Capabilities | Pricing Model |
|---|---|---|---|---|---|
| GCP | Imagen | Imagen 4 | Public Preview (May 2026) | High-fidelity photorealism, multilingual prompting, strict prompt adherence | Per-image generation |
| GCP | Veo | Veo 3.1 | GA (July 2025) / 3.1 Lite in Preview (Mar 2026) | Cinematic photorealism, native audio integration, 8-second standard length | Subscription/Token tier |
| GCP | Lyria | Lyria 3 | Preview (March 25, 2026) | 48kHz stereo audio, full-length songs (Lyria 3 Pro) or 30-second clips (Lyria 3 Clip) | $0.08 per full song |
| AWS | Nova Canvas | Nova Canvas v1.0 | GA (Dec 3, 2024) | Built-in watermarking, content moderation, studio-quality rendering | $0.04 - $0.08 per image based on resolution |
| AWS | Nova Reel | Nova Reel v1.0 | GA (Dec 2024) | Video generation up to 2 minutes in length, advanced camera motion control (zoom/pan) | $0.08 per second |
| Azure | GPT-image | GPT-image-2 | GA | Text/Image to Image, inpainting, max resolution 3840px (3:1 ratio), face preservation | $5.00 / 1M input tokens |
| Azure | Sora | Sora 2 | Retiring early (June 3, 2026) | High-fidelity video generation. The premature retirement is currently creating massive migration friction for enterprise users | $0.10 per second |
| Azure | FLUX | FLUX.2-pro / FLUX.2-flex | Preview (Feb 2026) | Black Forest Labs models. Multi-reference editing, complex typography (Flex), in-context generation | $0.05 - $0.07 per generated megapixel |

The media generation space presents unique architectural challenges. Google's Lyria 3, for instance, represents a breakthrough in high-fidelity audio, generating 48kHz stereo tracks that maintain structural coherence across vocals and instrumental arrangements. Meanwhile, Azure's media strategy has hit a significant operational hurdle; the unexpected early retirement of OpenAI's Sora 2 on June 3, 2026, has left Azure customers without a clear migration path for video generation workloads, starkly contrasting with OpenAI's own API timeline that supports the model through September 2026. To compensate in the image space, Azure has tightly integrated Black Forest Labs' FLUX.2 ecosystem, positioning the FLUX.2-flex variant as a purpose-built tool for UI prototyping and typography generation, an area where traditional models struggle.

---

## Hardware Specifications

Underpinning the algorithmic advancements is a massive expansion in custom silicon across all three hyperscalers. The architectural decisions reveal highly divergent strategies: Google emphasizes raw interconnect bandwidth and liquid-cooled scaling via complex 3D Torus topologies, AWS focuses on dense scaling and memory bandwidth per server, and Microsoft aims to optimize low-precision inference economics.

### Google Cloud (TPU Trillium and Ironwood)

Google's hardware strategy relies on specialized Application-Specific Integrated Circuits (ASICs) designed exclusively for massive matrix multiplication.

The sixth-generation TPU Trillium (v6e) is engineered for balanced performance and energy efficiency. It delivers 918 TFLOPs of peak BF16 compute and 1836 TOPs of Int8 compute per chip. Each chip is equipped with 32 GB of High Bandwidth Memory (HBM) capable of 1638 GiBps bandwidth. The Trillium architecture utilizes a 2D torus interconnect topology, allowing pods of up to 256 chips to operate cohesively. The bidirectional Inter-Chip Interconnect (ICI) bandwidth reaches 800 GBps per chip (providing 6.4 Tbps aggregate bandwidth across a 4-chip slice). Furthermore, Trillium incorporates third-generation SparseCores, which selectively offload fine-grained, embedding-heavy workloads from the primary TensorCores, highly optimizing ranking and recommendation tasks.

Google's seventh-generation TPU Ironwood (v7) represents a colossal architectural leap, specifically positioned for the "Age of Inference" and massive parameter scaling required by agentic workflows. Moving to a complex dual-chiplet architecture, Ironwood boasts an immense 4,614 TFLOPs of peak FP8 compute per chip. Memory capacity has been vastly expanded to 192 GB of HBM3e per chip, running at a staggering 7.38 TB/s of bandwidth. Crucially, Ironwood moves from a 2D to a 3D torus topology, allowing pods to scale up to an unprecedented 9,216 chips. The communication between these chips is supported by a 1,200 GBps bidirectional ICI bandwidth. The massive 192 GB memory footprint allows Ironwood to hold substantially larger Key-Value (KV) caches, making it uniquely positioned to efficiently serve the 1,000,000 token context windows of the Gemini 3 and 3.1 generations without memory thrashing.

### Amazon Web Services (Trainium 2 and Trainium 3)

AWS has focused heavily on dense, highly integrated server nodes designed to maximize intra-server bandwidth. While the Trainium 2 architecture established AWS's initial footprint for large-scale training, the Trainium 3 chip, built on an advanced 3nm process, is the current focal point for frontier model workloads.

Trainium 3 achieves 2.52 PFLOPs of FP8 compute per chip, doubling the compute performance of its predecessor. Memory capacity has increased to 144 GB of HBM3e, delivering 4.9 TB/s of memory bandwidth. AWS organizes these chips into "Trn3 UltraServers," scaling up to 144 chips per server (delivering 362 FP8 PFLOPs total per integrated system). These servers are interconnected by the proprietary NeuronSwitch-v1 all-to-all fabric, which provides 2 TB/s of interconnect bandwidth per chip, drastically reducing the latency associated with parallel training synchronization. Trainium 3 is specifically designed for both dense and expert-parallel workloads, utilizing advanced data types (MXFP8 and MXFP4) to improve the memory-to-compute balance required for real-time multimodal reasoning.

### Microsoft Azure (Maia 100 and Maia 200)

Microsoft's hardware trajectory demonstrates a hyper-focus on the economics of model serving rather than distributed training. The Maia 100 laid the groundwork for Azure's custom silicon, but the newly introduced Maia 200 acts as a dedicated inference accelerator engineered specifically to drive down the cost of AI token generation.

Built on TSMC's 3nm process, the Maia 200 natively supports FP8 and FP4 precision tensor cores, delivering over 5.07 PFLOPs of FP8 compute and an astonishing 10.14 PFLOPs of FP4 compute. It features 216 GB of HBM3e memory operating at 7 TB/s, heavily augmented by 272 MB of on-die SRAM to minimize off-chip traffic and keep the massive models fed rapidly during generation. The networking design scales over standard Ethernet to clusters of up to 6,144 AI accelerators. As of May 2026, the Maia 200 is deployed and globally available in the US Central and US West 3 datacenter regions.

### NVIDIA GPU Availability

While proprietary silicon is the focus for training economics and hyper-optimized inference, the NVIDIA ecosystem remains highly available and serves as the baseline standard across all three clouds. Instances for the H100 and H200 are universally generally available. The newer Blackwell B200 generation, delivering 4,500 TFLOPS FP8 with 192GB HBM3e and operating at 8 TB/s memory bandwidth, is currently rolling out into preview and early GA instances across GCP, AWS, and Azure. The B200 serves as the primary benchmark against which the custom ASICs (Ironwood, Trainium 3, and Maia 200) are evaluated for price-to-performance efficiency.

---

## Managed AI Service Pricing

Comparing foundation model API costs only reveals a fraction of the Total Cost of Ownership (TCO) for enterprise AI platforms. The orchestration, search, memory persistence, and data retrieval layers present highly variable cost structures that often eclipse the cost of raw token inference.

### Google Gemini Enterprise Agent Platform

Google's pricing model is heavily segmented by specific functionality, charging distinct rates for search execution versus ongoing agent computation.

- **Vertex AI Search Enterprise Edition:** Pricing is tightly bound to per-query metrics. Standard search costs $1.50 per 1,000 queries. The Enterprise Edition, which incorporates Core Generative Answers, costs $4.00 per 1,000 queries. An "Advanced Generative Answers" capability incurs an additional surcharge of $4.00 per 1,000 user input queries.
- **Agent Runtime (formerly Agent Engine):** Billing for the execution environment shifted on February 11, 2026, to a compute-based consumption model. The runtime is billed at $0.0864 per vCPU-hour (mathematically equating to $0.00144 per minute) and $0.0090 per GB-hour for allocated memory. Furthermore, long-term memory and session state management are not included in the compute cost; they incur a specific fee of $0.25 per 1,000 stored session events or "memories".

### Amazon Bedrock

AWS abstracts much of the underlying infrastructure for agentic workflows, but the dependency on specific database architectures introduces significant billing floors.

- **Bedrock Knowledge Bases:** The RAG retrieval pipeline abstracts the underlying vector storage, defaulting to Amazon OpenSearch Serverless. This configuration introduces a notorious billing trap: OpenSearch Serverless mandates a minimum of 2 OpenSearch Compute Units (OCUs) for production redundancy. At a rate of $0.24 per OCU per hour, this creates a baseline cost of approximately $0.48 per hour, or roughly $345 to $350 per month just for the index to exist at rest, irrespective of actual query volume. To mitigate this, AWS launched Amazon S3 Vectors in late 2025 as a significantly cheaper alternative for vector storage. Embedding generation via Amazon Titan Embeddings V2 adds a marginal $0.00011 per 1,000 input tokens.
- **Bedrock AgentCore:** The agentic framework itself does not charge a per-invocation orchestration fee; users pay for the underlying token generation and any specialized gateway API calls. However, the introduction of AgentCore microtransactions (using the x402 protocol) facilitates autonomous API payments directly. This allows agents to pay for third-party tool usage dynamically via Coinbase or Stripe wallets, shifting variable tool costs directly to the developer's registered wallet based on session-level spending limits.

### Microsoft Foundry

Microsoft's pricing reflects its unified platform approach, leaning heavily on Azure AI Search and standard cloud compute models.

- **Foundry Agent Service:** The agentic orchestrator acts as the glue for multi-agent systems and does not carry an explicit orchestration markup. The cost is derived entirely from the underlying provisioned throughput units (PTU) or pay-as-you-go token costs of the selected models.
- **Foundry IQ:** Billed via the underlying Azure AI Search engine, agentic retrieval is charged at $0.022 per 1M tokens (with minimum reasoning applied), following a 50M token free tier per month. Additional operational tools incur heavy per-use surcharges: Code Interpreter costs $0.033 per session, and Web Search (leveraging the Bing search graph) is priced at $14.00 per 1,000 transactions.

---

## Benchmarks: May 2026 Snapshot

The evaluation methodology for Large Language Models has shifted entirely away from static question-answering toward dynamic, agentic, and long-horizon workflows. The May 2026 snapshot reveals a tightly contested frontier where specific models dominate highly distinct modalities, proving that no single architecture universally excels across all tasks.

Note: The data presented below reflects independently verified or widely recognized benchmark suites current as of May 2026.

| Benchmark | Gemini 3.1 Pro | Claude Opus 4.6 | Claude Sonnet 4.6 | GPT-5.5 | GPT-5.4 | GPT-5.3-codex |
|---|---|---|---|---|---|---|
| SWE-bench Verified | 80.6% | 80.8% | 79.6% | 82.6% | 78.2% | 78.0% |
| SWE-bench Pro | 54.2% | - | - | - | - | 56.8% |
| LiveCodeBench Pro (Elo) | 2887 | - | - | - | - | - |
| MMLU-Pro | 92.6% (MMMLU) | - | 89.3% (MMMLU) | - | - | - |
| HumanEval (pass@1) | 89.2% | 90.4% | - | - | 93.1% | - |
| GPQA Diamond | 94.3% | 91.3% | 89.9% | - | 92.0% | - |

Agentic and Tool-Use Benchmarks (Vendor/Community Reported):

- **TAU-bench (Retail Domain):** Evaluates multi-step, tool-use execution in a simulated retail environment. Claude Opus 4.6 leads slightly at 91.9%, followed closely by Claude Sonnet 4.6 (91.7%) and Gemini 3.1 Pro (90.8%).
- **Terminal-Bench 2.0 (Terminal workflows):** This benchmark tests agentic coding directly within a terminal environment. GPT-5.3-codex dominates this specific environment at 77.3%, significantly outpacing Gemini 3.1 Pro (68.5%) and Claude Opus 4.6 (65.4%).
- **GDPval-AA Elo (Expert Office Tasks):** Measures performance on economically valuable knowledge work. Claude Sonnet 4.6 leads all models in office productivity tasks with an Elo of 1633, beating out its heavier counterpart, Opus 4.6 (1606).

Analysis: A clear trifurcation of capability exists at the frontier. GPT-5.5 and Claude Opus 4.6 hold a fractional lead in generalized, single-attempt software engineering tasks (SWE-bench Verified). Conversely, Gemini 3.1 Pro dominates complex competitive coding logic (LiveCodeBench Pro) and highly abstract scientific reasoning (GPQA Diamond). For autonomous, long-running terminal execution and complex system navigation, GPT-5.3-codex is the uncontested leader. These results dictate that enterprise architectures must employ intelligent model routing, matching the specific orchestration task to the model that empirically excels in that domain.

---

## Region Availability for AI Services

Deployment architectures are heavily constrained by physical data center rollouts. Geographic compliance, data residency laws, and network latency require strict architectural mapping of where models are natively hosted.

### Google Cloud (Vertex AI)

Google utilizes a dynamic routing endpoint (global) that allows traffic to flow to the most available region, reducing resource exhaustion errors. However, for strict data residency, regional pinning is required.

- **Gemini 3 Family:** Gemini 3.1 Pro, 3 Flash, and 3.1 Flash-Lite are available via the global endpoint. For regional pinning, they are available in major hubs including Iowa (us-central1), South Carolina (us-east1), Northern Virginia (us-east4), Oregon (us-west1), and Las Vegas (us-west4).
- **Media Models (Imagen, Veo, Lyria):** The heavy compute requirements of media models restrict them primarily to us-central1, us-east1, us-west1, us-west4, and Europe (europe-west4, Eemshaven).

### Amazon Web Services (Bedrock)

AWS provides granular control over inference routing, offering In-Region processing (strict single-region execution), Geographic routing (balancing across regions within the US or EU boundaries), and Global routing.

- **Claude Family:** Supported deeply across the AWS backbone, with core availability zones anchored in us-east-1 (N. Virginia) and us-west-2 (Oregon).
- **Amazon Nova (Canvas/Reel):** The proprietary Nova models are heavily concentrated in US East (us-east-1) and are expanding into Asia Pacific (ap-southeast-1).

### Microsoft Azure (Foundry)

Azure tightly limits the initial deployment footprints of its frontier models, often restricting high-end endpoints to specific hardware clusters.

- **GPT-5 Family:** Access to the latest GPT-5.4 models is highly restricted, primarily accessible via Global Standard endpoints mapped to specific hardware hubs in East US 2, Sweden Central, and South Central US.
- **Sora 2:** As noted, Sora 2 availability is restricted and faces an imminent, complete retirement across all regions on June 3, 2026.

---

## EU Sovereign Cloud Status (May 2026)

The strict enforcement of European data boundaries, coupled with the jurisdictional shadow of the US CLOUD Act, has driven the creation of deeply isolated, sovereign cloud infrastructures.

### AWS European Sovereign Cloud (ESC)

AWS officially launched the AWS European Sovereign Cloud into General Availability on January 15, 2026, marking the completion of a massive €7.8 billion infrastructure investment. The first operational region is physically located in the state of Brandenburg, Germany.

From an architectural standpoint, the ESC is physically and logically separated from all other standard AWS regions. It features entirely independent billing, account management, and identity systems. The platform launched with an initial portfolio of over 90 services, crucially including Amazon Bedrock, SageMaker, and EC2 compute layers. The operator model is strictly controlled: AWS mandates that only EU residents can operate the infrastructure, and the managing directors of the corporate entity must be EU nationals. However, a significant legal gap remains: the ESC is legally a subsidiary of the US-based Amazon parent corporation. European compliance analysts note that this corporate structure leaves residual exposure to extraterritorial warrants under the US CLOUD Act, as AWS has not divested the entity to a non-US owner.

### Microsoft Azure

Azure relies on its EU Data Boundary commitments and a localized governance architecture termed "M365 Local" or "Sovereign." Microsoft utilizes partner operator models and local European Data Guardians to oversee data access. Similar to AWS, Microsoft executives have testified before European governing bodies (such as the French Senate) that absolute, cryptographic immunity from US data requests cannot be legally guaranteed due to the company's ultimate US domicile. To mitigate this risk for highly classified workloads, Azure enables disconnected "Foundry Local" deployments, allowing enterprises to run models entirely offline on localized hardware.

### Google Cloud

(Gap noted: Authoritative sources regarding Google's exact May 2026 sovereign operator mechanics, such as specific updates to its T-Systems or Thales joint ventures, were not found in the provided documentation. Google relies instead on its broader zero-trust, Customer-Managed Encryption Key architectures to satisfy European data sovereignty demands.)

All three hyperscalers actively pursue alignment with stringent European frameworks, including BSI C5 (Germany) and ANSSI SecNumCloud (France).

---

## Security and Networking Primitives

The transition from experimental API wrappers to enterprise-grade, production AI applications is entirely dependent on securing the network path, isolating model execution, and managing data storage lifecycles.

### Private Connectivity to AI Endpoints

To prevent sensitive intellectual property or personally identifiable information from traversing the public internet, each hyperscaler enforces strict network boundaries.

- **GCP:** Google utilizes VPC Service Controls to establish a secure perimeter around the Gemini Enterprise Agent Platform. This explicitly mitigates data exfiltration risks by ensuring that training data, prompts, and inference requests cannot leave the defined service perimeter. Private Service Connect is used to generate internal IPs for API access, ensuring all traffic remains on Google's private backbone.
- **AWS:** AWS secures Amazon Bedrock via AWS PrivateLink. Enterprise setups configure interface VPC endpoints (specifically `bedrock-runtime` and `bedrock`) within an isolated, private subnet. Traffic remains entirely within the AWS network. For agentic workflows, AgentCore gateway egress traffic is routed through elastic network interfaces (ENIs) deployed directly into the customer's VPC, allowing the agent to securely query private RDS databases or on-premises systems without public exposure.
- **Azure:** Microsoft Foundry utilizes Azure Private Endpoints to disable public network access to Foundry hubs, Azure Storage accounts, and Key Vaults. Outbound traffic from agents (for example, when an agent calls an Azure Function tool) is secured via VNet injection using delegated subnets. This ensures that internal enterprise data interacting with the AI agent never touches public infrastructure.

### Customer-Managed Encryption Keys (CMEK/KMS)

Granular control over the cryptographic lifecycle is a non-negotiable requirement for regulated industries.

- **GCP:** Cloud KMS provides Customer-Managed Encryption Keys (CMEK) to encrypt data at rest within Vertex AI. This granularity is applied at the project and resource level, allowing specific models or training datasets to be encrypted with distinct keys.
- **AWS:** Bedrock utilizes AWS Key Management Service (KMS). Crucially, AgentCore Gateways can be configured with Customer-Managed Keys (CMKs) rather than default service-managed keys. This granularity allows organizations to dictate the exact key rotation schedule, apply highly granular IAM policies to the decryption process, and audit key access per tenant.
- **Azure:** Azure Key Vault and Azure Managed HSM allow Customer-Managed Keys to encrypt Foundry environments at rest. Furthermore, Azure integrates Customer Lockbox, a feature requiring explicit, auditable approval before Microsoft support engineers can access customer environments for troubleshooting.

### Audit Logging and Data Retention

- **Audit Logging:** GCP utilizes Cloud Audit Logs to track platform access. AWS utilizes CloudTrail, which comprehensively captures every model invocation, parameter change, and key decryption event. Azure relies on Azure Monitor and Activity Logs, which are deeply integrated into the new Foundry Control Plane for real-time observability.
- **Data Retention:** All three hyperscalers have instituted strict contractual guarantees (via formal data processing addendums) that zero customer data, prompts, or inference completions are retained or utilized to train their foundation models.

### Compliance Certifications

As of May 2026, the underlying orchestration platforms (Bedrock, Foundry, Gemini Enterprise) inherit their respective cloud's major compliance frameworks. This includes FedRAMP High (achieved via GovCloud or specialized government regions), HIPAA (with standard Business Associate Agreements in place), SOC 2 Type II, ISO 27001, and PCI DSS scope adherence.

---

## Verify or Correct Specific Claims in the Current Draft

The following assertions from the existing `gcp-ai-platform-overview.md` draft have been rigorously fact-checked against official May 2026 documentation:

- **Claim:** TPU Trillium delivers approximately 4.7x peak compute over TPU v5e per chip. **Status:** Confirmed.
- **Claim:** TPU Ironwood is 4,614 TFLOPs FP8 with 192GB HBM. **Status:** Confirmed.
- **Claim:** Trillium pod is up to 256 chips with 4.8 Tbps per chip interconnect. **Status:** Corrected. The pod size is correctly stated as 256 chips, but the interconnect bandwidth is actually 800 GBps per chip. When calculated across the chip's four ports bidirectionally, this translates to 6.4 Tbps aggregate (not 4.8 Tbps per chip).
- **Claim:** Approximately $4 per 1,000 queries for Vertex AI Search Enterprise edition. **Status:** Confirmed. The Agent Search Enterprise Edition (which includes Core Generative Answers) is priced exactly at $4.00 per 1,000 queries.
- **Claim:** Approximately $0.0014 per minute for Vertex AI Agent Engine sessions. **Status:** Confirmed. The Agent Runtime computes at a published rate of $0.0864 per vCPU-hour, which equates to $0.00144 per minute.
- **Claim:** Gemini 3 has 1M token context. **Status:** Confirmed.
- **Claim:** Single-pod TPU training is competitive up to roughly 70B parameter models with standard memory footprints. **Status:** Confirmed.
- **Claim:** Imagen, Veo, Lyria are primarily served from us-central1, us-east1, and europe-west4 on Vertex. **Status:** Confirmed.
- **Claim:** AWS EU Sovereign Cloud launches in late 2025 in Brandenburg. **Status:** Corrected. The AWS European Sovereign Cloud in Brandenburg officially reached General Availability on January 15, 2026.
- **Claim:** Model Garden hosts 200+ first-party and partner models. **Status:** Confirmed.
- **Claim:** Foundry Models advertises 1,900+ entries. **Status:** Corrected. Current May 2026 Azure portal documentation advertises "more than 11,000+ models".

---

## Conflicts and Gaps

1. **Azure Sora-2 Retirement Conflict:** Microsoft's documentation states the sora-2 endpoint will be retired on June 3, 2026. OpenAI's own timeline indicates API support continues through September 24, 2026. This misalignment is creating widespread developer friction.
2. **Microsoft Foundry Catalog Size:** Marketing oscillates between "over 1,900 models" and "more than 11,000+ models". The latter figure includes Hugging Face automated mirror deployments and community fine-tunes.
3. **GCP Sovereign Operator Mechanics:** Equivalent, definitive documentation detailing Google's exact May 2026 operational partnerships for its European sovereign boundary could not be comprehensively sourced.
