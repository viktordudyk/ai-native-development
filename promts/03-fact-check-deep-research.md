# Fact-Check Prompt for Gemini Deep Research

**Goal:** Verify or find credible sources for the following claims used in two documents about AI-native software development practices (as of March 2026). For each claim, provide: the verdict (confirmed / partially true / unverifiable / false), the best available source with URL, and a corrected version if the original is inaccurate.

---

## 1. Productivity Multipliers

- What is the credible, evidence-based productivity improvement range for developers using AI coding agents (not just autocomplete) with structured specification workflows? Specifically looking for peer-reviewed studies, large-scale surveys, or reports from GitHub, Microsoft Research, Google, McKinsey, or similar organizations. We currently claim "2-5x for well-specified tasks."

- Did GitHub's own research on Copilot productivity ever measure beyond inline autocomplete (i.e., agent mode / multi-file edits)? What were the actual numbers?

## 2. McKinsey Report on AI in Software Development

- Does a McKinsey report from 2025 or 2026 exist that specifically claims "20-40% operating cost reductions" for organizations adopting AI-native development practices, with a "3-6 month calibration period"? Find the exact report title, date, and URL if it exists. If not, what is the closest McKinsey finding on AI impact on software development costs?

## 3. AI Tooling Pricing & Subsidies

- What are the actual current prices (as of early-mid 2026) for: GitHub Copilot (individual, business, enterprise), Cursor (free, pro, business), Claude Code / Anthropic API, Windsurf, and other major AI coding tools?

- Is there any public evidence or credible analysis quantifying the subsidy ratio (how much vendors lose per seat on AI coding tools)? We removed a "10x subsidy" claim as unverifiable — is there a defensible number?

## 4. SWE-bench Scores (Spring 2026)

- What are the current top scores on SWE-bench Verified for frontier models (Claude, GPT, Gemini) as of early 2026?

- What are the best open-source model scores on SWE-bench Verified (DeepSeek, Qwen, Llama, Mistral)?

- Does "SWE-bench Pro" exist as a separate benchmark? If so, what are the scores? If not, what is the correct name for the harder/private-repo variant?

## 5. EU AI Act — Application to Source Code

- EU AI Act Article 50 transparency obligations: do they specifically require AI-generated **source code** to be marked/watermarked, or do they only apply to synthetic media content (images, video, audio, text presented to end users)? What is the exact scope as defined in the final regulation text?

- For software companies not building high-risk AI systems: what specific EU AI Act obligations apply to their use of AI coding tools internally?

## 6. AI-Generated Code Copyright

- US Copyright Office: what is the current (2025-2026) official position on copyrightability of AI-generated code? Cite the specific guidance documents.

- EU: is there a unified EU position on AI-generated code copyright, or does it vary by member state? What are the key differences?

- Is a "human spec → AI execution → human review" workflow legally sufficient for copyright protection under current US guidance?

## 7. EARS Syntax Adoption

- EARS (Easy Approach to Requirements Syntax) by Alistair Mavin: how widely is it adopted outside automotive/aerospace? Is there evidence of its use in software engineering for AI agent specifications? Any academic papers or industry reports on EARS adoption in tech companies?

## 8. NVIDIA B200 Pricing

- What is the actual market price for NVIDIA B200 GPUs (individual and in 8-GPU server configurations) as of early 2026? What is a realistic total cost for an 8xB200 on-premise deployment including networking, storage, cooling, and rack infrastructure?

## 9. On-Premise vs Cloud Cost Comparison

- Are there credible TCO analyses comparing on-premise GPU inference (for LLM coding agents) vs cloud API costs for a 200-engineer organization? What cost advantage ratio, if any, is documented?

## 10. Model Names & Versions (Spring 2026)

Verify which of these model names/versions actually exist as of March 2026:
- GPT-5.1 Codex
- Claude Opus 4.5
- Gemini 3 Pro
- DeepSeek V3+ / DeepSeek V4
- Llama 4
- Qwen 3 (72B)
- Mistral Large (latest version number)
- Codestral (latest version)
- Phi-4

## 11. Enterprise AI Agent Adoption Rate

- Is there a credible source for the claim that "40% of enterprise applications have integrated task-specific AI agents into delivery pipelines" by 2026? What do Gartner, Forrester, or IDC say about enterprise AI agent adoption rates in software delivery?

## 12. Design-to-Code Accuracy

- What is the realistic accuracy of Figma-to-code tools (v0, Anima, Locofy, Builder.io, or similar) for production React/Tailwind components? Any benchmarks or case studies with measurable accuracy metrics?

## 13. OWASP Standards for AI Agents

- Does OWASP have a standard called "ASI08" related to AI agent security? If not, what is the correct OWASP reference for AI/LLM security? List the relevant OWASP projects with their correct names and URLs.

---

**Output format:** For each numbered section, provide:
1. **Verdict:** Confirmed / Partially True / Unverifiable / False
2. **Best source:** Title, author/org, date, URL
3. **Key finding:** 1-2 sentence summary of what the evidence actually says
4. **Suggested correction:** If our claim needs updating, provide the corrected text
