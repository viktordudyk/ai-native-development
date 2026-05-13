# Prompt: GCP AI Platform Deep Research

This is a one-shot research prompt for Gemini Deep Research (or equivalent). The output gets folded into [docs/gcp-ai-platform-overview.md](../docs/gcp-ai-platform-overview.md), which is a GCP-first reference doc with inline AWS / Azure equivalents. The existing draft has plausible-but-unverified numbers and is missing benchmarks and the security/networking layer. This prompt is what fills those gaps.

---

## Use the prompt below

```
You are running a deep research pass on the current state of AI/ML platforms across the three hyperscalers (Google Cloud, AWS, Azure). The output will fact-check and enrich an existing reference document written for principal-dev / CTO / architect audiences on a GCP-primary engineering team. Treat the request as a technical reference task, not a marketing brief.

## Date and scope

Current as of May 2026. Where dates matter (Cloud Next 2026, re:Invent 2025, Microsoft Ignite 2025/2026), note them explicitly. Hyperscaler-only: no Snowflake, Databricks, Oracle, Alibaba.

## Source preferences

1. Canonical vendor documentation: cloud.google.com, docs.aws.amazon.com, learn.microsoft.com, vendor pricing pages.
2. Vendor press releases for Cloud Next / re:Invent / Ignite announcements.
3. Independent third-party verification (academic benchmark papers, Artificial Analysis, LMSys, SWE-bench leaderboards) where available.
4. Do not cite marketing blog posts unless they are the only source.

For every numeric claim and every product name, include an inline link to the source.

## Output format

Markdown. One H2 section per topic listed below. Tables for any side-by-side comparison. End the output with a "Conflicts and gaps" H2 noting where sources disagreed or where you could not find authoritative data.

## Research topics

### 1. Platform names and consolidation as of May 2026

Confirm or correct:
- Was Vertex AI renamed or consolidated under "Gemini Enterprise Agent Platform" at Cloud Next 2026? Give the actual official name and date.
- Confirm the Azure AI Foundry to Microsoft Foundry rebrand. Date and source.
- How does AWS officially describe the relationship between Amazon Bedrock, SageMaker Unified Studio, and Bedrock AgentCore in May 2026?

### 2. Foundation models: current versions, IDs, pricing

For each cloud, list every currently-available foundation model with: exact model ID, context window in tokens, input pricing per 1M tokens, output pricing per 1M tokens, GA date, supported modalities.

Cover at minimum:
- GCP: Gemini 3 family (confirm whether 3 Pro, 3 Flash, 3.1 Pro are the current set; confirm context window claim of 1M tokens), Claude family on Vertex (confirm which Claude versions are available), partner models in Model Garden (current count and notable additions).
- AWS: Claude family on Bedrock (confirm Opus / Sonnet / Haiku 4.5 versions and whether 4.6 is also available), Amazon Nova family (Pro, Lite, Micro), Titan, key partner models.
- Azure: Azure OpenAI lineup (confirm GPT-5, GPT-5.1, GPT-5.2, GPT-5.3-codex exact identifiers and GA dates), Claude via Marketplace (which versions, billing model), other key partners in Foundry Models.

### 3. Image, video, music generation models

For each, list the current production version, GA / preview status, GA date, key capabilities (resolution, video length, audio support), and pricing model:
- Imagen (current version on Vertex)
- Veo (current version, max length, audio capability)
- Lyria (current version, preview vs GA, regions)
- Nova Canvas, Nova Reel (versions, capabilities)
- GPT-image (current version on Azure)
- Sora on Azure (availability, max length)
- FLUX (current version) and other Black Forest Labs models on Azure

### 4. Hardware specifications

For each accelerator family, the official specs:
- TPU Trillium (v6): peak compute (FP16, FP8 if published), HBM size, HBM bandwidth, pod size in chips, inter-chip interconnect bandwidth, regional availability.
- TPU Ironwood (v7): same metrics. Specifically inference-positioned?
- Trainium 2: peak compute, memory, pod size, regions.
- Trainium 3: GA status, specs, regions.
- Microsoft Maia 100: specs, regions, availability.
- Microsoft Maia 200: GA status, specs, regions.
- NVIDIA B200, H200, H100 availability on each cloud: official instance/machine types and regions where each generation is GA.

### 5. Managed AI service pricing

- Vertex AI Search Enterprise edition: per-query pricing, storage pricing, document indexing pricing.
- Vertex AI Agent Engine: billing model (per session, per minute, per token uplift?). Include any documented egress or session-state surcharges.
- Bedrock Knowledge Bases: pricing model (per query, per OpenSearch unit, per embedding call).
- Bedrock AgentCore: billing model.
- Azure Foundry Agent Service: billing model.
- Foundry IQ: billing model and any per-source connector fees.

### 6. Benchmarks: May 2026 snapshot

For the current versions of Gemini 3 Pro / Flash / 3.1 Pro, Claude Opus 4.5 and 4.6, and GPT-5 / GPT-5.3-codex, gather published scores on:
- SWE-bench Verified and SWE-bench Pro
- LiveCodeBench
- MMLU-Pro
- AIME 2025 or AIME 2026
- HumanEval+ or MBPP+
- Vendor-reported agentic benchmarks (TAU-bench, BFCL, AgentBench) if available
- Tool-use / function-calling benchmarks

For each score, mark whether it is self-reported by the model vendor or independently verified. Note benchmark dates. Where multiple sources disagree, list both.

### 7. Region availability for AI services

For each, list the regions where the service is GA in May 2026:
- Gemini 3 family on Vertex
- Claude on Vertex (each version separately if availability differs)
- Imagen, Veo, Lyria
- Claude family on Bedrock
- Nova Canvas, Nova Reel
- Azure OpenAI (GPT-5 family) by region
- Sora on Azure by region

### 8. EU Sovereign Cloud status (May 2026)

For each cloud:
- Operational date and host location of the sovereign offering.
- Which AI services are covered by the sovereign offering vs the standard EU regions.
- Operator model: hyperscaler-operated, partner-operated (T-Systems, Thales, etc.), or hybrid.
- Compliance commitments: EU Data Boundary, Schrems II alignment, BSI C5, ANSSI SecNumCloud.

### 9. Security and networking primitives (currently missing from the doc)

Side-by-side comparison with concrete product names and official setup patterns:
- Private connectivity to AI endpoints: VPC Service Controls (GCP) vs AWS PrivateLink for Bedrock vs Azure Private Endpoints for Foundry. The official pattern for keeping Vertex / Bedrock / Foundry traffic off the public internet.
- Customer-managed encryption keys: CMEK on Vertex, KMS on Bedrock, Customer-Managed Keys on Azure Foundry. Granularity: per-model, per-resource, per-tenant.
- Audit logging: what gets logged by default vs opt-in for AI service calls on each cloud. Cloud Audit Logs for Vertex, CloudTrail for Bedrock, Azure Monitor / Activity Log for Foundry.
- Data retention and training-data use: does any of the three clouds retain prompts or completions for training? Default policy and opt-out / contractual options.
- Compliance certifications specifically attached to AI services as of May 2026: FedRAMP High, HIPAA, PCI DSS, ISO 27001, SOC 2, EU AI Act readiness statements.

### 10. Verify or correct specific claims in the current draft

For each item below, either confirm the number with a source, or give the correct number with a source:
- TPU Trillium delivers approximately 4.7x peak compute over TPU v5e per chip.
- TPU Ironwood is 4,614 TFLOPs FP8 with 192GB HBM.
- Trillium pod is up to 256 chips with 4.8 Tbps per chip interconnect.
- Approximately $4 per 1,000 queries for Vertex AI Search Enterprise edition.
- Approximately $0.0014 per minute for Vertex AI Agent Engine sessions.
- Gemini 3 has 1M token context.
- Single-pod TPU training is competitive up to roughly 70B parameter models with standard memory footprints.
- Imagen, Veo, Lyria are primarily served from us-central1, us-east1, and europe-west4 on Vertex.
- AWS EU Sovereign Cloud launches in late 2025 in Brandenburg.
- Model Garden hosts 200+ first-party and partner models.
- Foundry Models advertises 1,900+ entries.

## What NOT to include

- Marketing language and superlatives ("industry-leading", "world-class", "revolutionary").
- Subjective quality comparisons. Only numeric, spec, pricing, availability, or capability-list data.
- Roadmap speculation or unannounced products.
- Comparisons of developer experience or ergonomics. This pass is for facts and figures.
```

---

## How to use this output

Take the Deep Research report and update [docs/gcp-ai-platform-overview.md](../docs/gcp-ai-platform-overview.md) section by section. The big drops in:
- Section 6 of the research (benchmarks) becomes a new H2 in the doc, probably between "Core AI/ML platform" and "Agents and developer tools".
- Section 9 (security and networking) becomes a new H2, probably after "Infrastructure and pricing" or as a subsection there.
- Sections 4, 5, 10 replace existing numbers in the Infrastructure and Core AI/ML sections.
- Sections 2 and 3 confirm or correct the master cheat-sheet table at the top.

Run the same prompt every quarter to refresh.
