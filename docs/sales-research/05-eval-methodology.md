# Q5: Eval Methodology — Beyond Green Tests

"Tests are green" is necessary but radically insufficient for AI-generated code. Agents pass the tests they were given — including ones they wrote themselves — while silently violating the spec. Tricentis calls this *intent drift*: behavior diverges from specification without breaking the pipeline ([Tricentis, 2025](https://www.tricentis.com/blog/intent-drift-ai-code-fix-regression-blind-spots)). Green tests measure *what was asked*, not *what was meant*. A principal-dev eval strategy stacks complementary signals across the layers below, then closes the loop with a defect-escape-rate program.

## Layered Eval Stack

| Layer | What it catches | Tools (2025-2026) |
|---|---|---|
| Unit/integration tests | Specified behaviors on chosen inputs | Jest, pytest, JUnit, Go testing |
| **Mutation testing** | Test *strength* — whether tests would notice if code changed | [StrykerJS / Stryker.NET](https://stryker-mutator.io/), [PIT (Java)](https://pitest.org/), [mutmut](https://github.com/boxed/mutmut) (Python), [Infection](https://infection.github.io/) (PHP) |
| **Property-based testing** | Invariants holding across generated inputs; edge cases humans/LLMs miss | [Hypothesis](https://hypothesis.works/articles/what-is-property-based-testing/) (Py), QuickCheck (Haskell), [fast-check](https://github.com/dubzzz/fast-check) (TS/JS), proptest (Rust), junit-quickcheck |
| **Differential / metamorphic testing** | Semantic divergence vs. a reference implementation or prior version | DIFFSPEC, custom oracles, golden-master harnesses ([CMU S3D, 2025](http://reports-archive.adm.cs.cmu.edu/anon/s3d2025/CMU-S3D-25-101.pdf)) |
| **Formal verification** | Mathematical proof of spec satisfaction (critical paths only) | Dafny, Lean, F\*, [CLEVER benchmark](https://arxiv.org/pdf/2505.13938) |
| **System / E2E behavioural** | Cross-component intent drift not visible in unit tests | Testkube, Playwright, contract tests ([Testkube, 2025](https://testkube.io/blog/system-level-testing-ai-generated-code)) |
| **Ground-truth replay** | Drift on known-correct I/O pairs over time | Internal eval harnesses; LangSmith/Braintrust datasets |
| **Production telemetry** | Real escapes — defects users actually hit | Sentry, Datadog, SLO error budgets |

Mutation testing is the highest-leverage addition: a suite with 100% line coverage and 4% mutation score executes every line but misses 96% of behavioral paths, and mutant survival runs 15–25% higher on AI-generated than human-written code at equivalent coverage ([DEV/rsri, 2025](https://dev.to/rsri/mutation-testing-the-missing-safety-net-for-ai-generated-code-54kn)). Practical thresholds: 80% on critical paths (StrykerJS default "high"), 70% standard, 50% experimental — gated in CI on the diff, not the whole repo ([Stryker docs](https://stryker-mutator.io/docs/)).

## Public Benchmark Landscape

Public benchmarks are for *model selection*, not internal QA. All suffer training-data contamination and saturation:

- **HumanEval / MBPP** — saturated; 113/164 and 318/427 tasks solved by every modern model; short, algorithmic, no I/O ([arXiv 2511.04355](https://arxiv.org/html/2511.04355v1)).
- **SWE-bench Verified** — OpenAI *stopped reporting* it after audit found 59.4% of problems have flawed tests rejecting correct submissions ([OpenAI, 2025](https://openai.com/index/why-we-no-longer-evaluate-swe-bench-verified/)).
- **SWE-bench Pro** — Scale's successor; 1,865 tasks across GPL/proprietary repos as contamination deterrent. GPT-5 and Claude Opus 4.1 score only ~23% ([Scale, 2025](https://scale.com/blog/swe-bench-pro)).
- **LiveCodeBench** — date-tagged competitive problems; v6 covers May 2023–April 2025. Time-segmented scoring exposes contamination cliffs ([LiveCodeBench](https://livecodebench.github.io/)).
- **RepoBench** — repo-level retrieval + completion, Python/Java ([Leolty/repobench](https://github.com/Leolty/repobench)).

Trust LiveCodeBench time slices and SWE-bench Pro private set for vendor selection — but only your *own* harness for production gating.

## Eval Harnesses

For LLM-app and agent evals: [Braintrust](https://www.braintrust.dev/articles/langsmith-vs-braintrust) (CI-gated evals + prod traces), [LangSmith](https://www.langchain.com/evaluation) (LangChain-native), Langfuse (MIT OSS), and [Inspect AI](https://hamel.dev/blog/posts/eval-tools/) (UK AISI; capability/safety). OpenAI Evals has receded; the field consolidated around these four ([Arize, 2025](https://arize.com/llm-evaluation-platforms-top-frameworks/)).

## Defect Escape Rate (DER) Methodology

DER = defects-found-in-prod / total-defects × 100. Capers Jones' data puts the U.S. industry average at ~92.5% Defect Removal Efficiency (~7.5% escape); best-in-class >99% ([Capers Jones / PPI](https://www.ppi-int.com/wp-content/uploads/2021/01/Software-Defect-Removal-Efficiency.pdf)). Jones is explicit: ">95% DRE is unachievable with testing alone — it needs pre-test inspection plus static analysis."

Implementation for an AI-native team:

1. **Tag every defect** in Jira/Linear with `found-in: {dev, ci, qa, staging, prod}` and `introduced-by: {human, ai-assisted, ai-autonomous}` ([Instatus, 2025](https://instatus.com/blog/der)).
2. **Three rolling windows**: 30/60/90-day post-merge escape rates. ISBSG standardises release+30 days; 90 days catches latent defects ([Operately KPI](https://kpiexamples.operately.com/software-engineering/defect-escape-rate)).
3. **Targets**: start <10% escape, intermediate <5%, mature <1%.
4. **Segment by AI-authorship**: DER for AI-autonomous vs. AI-assisted vs. human-only PRs is the single number that tells you whether AI is net-positive on quality.
5. **Feed escapes back into the harness** as regression cases — every prod bug becomes a permanent test in the ground-truth replay set, growing organic contamination-free coverage.

Monthly principal-dev deliverable: DER by authorship cohort, mutation score on the diff, and cumulative escapes promoted into the eval harness.
