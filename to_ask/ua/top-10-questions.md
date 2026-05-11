# Топ 10 питань до команди — AI SDLC Practice

**Контекст:** Питання principal-dev рівня перед виходом на ринок з AI SDLC Practice. Тон — інженерний, фокус на реальній архітектурі та методології, а не на маркетингу.

---

## Група 1: Скоуп оферингу

**1. Які пакети AI SDLC Practice сьогодні готові до продажу і як обирати правильний під клієнта?**
Чи є коротка діагностична матриця «зрілість клієнта / стек / розмір команди → рекомендований пакет», щоб впевнено орієнтуватись між Assessment, Introduction, Uplift, Module-based, Native Transition і Brownfield на першому дзвінку? Які з пакетів сьогодні справді delivery-ready, а які поки що в розробці?

---

## Група 2: Методологія (SDD і поведінкові шари)

**2. Spec Driven Development (методологія, де специфікація — primary artifact, а код — деривативний) — який у нас формат специфікацій і як вони живуть разом з кодом?**
EARS-style (Easy Approach to Requirements Syntax — формат «When [condition], the system shall [action]»), custom DSL (Domain-Specific Language — спеціалізована мова під задачу), structured markdown? Як specs версіонуються разом з кодом, як обробляють ambiguity, edge cases і non-functional requirements (performance, security, accessibility)? Чи передбачено CI-gate (Continuous Integration check — автоматизована перевірка перед merge), що сигналізує про spec/code divergence?

**3. Шари конфігурації агента (project rules, AGENTS.md, ARCHITECTURE.md, IDE-specific rules) — як вони композуються?**
Який precedence (порядок пріоритету) при конфлікті між шарами? Як ми перевіряємо, що зміна в rules не змінить поведінку агента на існуючих задачах (regression testing на rules)? На моноріпі (один Git-репо з кількома проєктами) з багатьма командами — як організовано: один global файл чи per-package, і хто його власник?

**4. HITL gates (Human-In-The-Loop — точки обов'язкового людського затвердження) — де саме спрацьовують і за якою таксономією?**
Які класи змін агент не може merge без human approval (db migrations — зміни схеми БД, security-sensitive code, public API, infra changes)? Як ми утримуємо HITL gate від деградації у формальний approve без читання — є метрики на review depth і time-to-approve? Чи є distinction між plan-mode approval (агент показує план дій) і agent-mode approval (агент виконує дії автономно)?

---

## Група 3: Якість і доказова база

**5. Eval methodology (як ми вимірюємо якість згенерованого коду) — що поза «тести зелені»?**
Eval harness (тестовий стенд для оцінки агентів) — який, що міряємо? Property-based testing (тести з випадковими вхідними даними для перевірки інваріантів), mutation testing (вмисні зміни в коді для оцінки сили тестів), semantic equivalence (перевірка, що дві реалізації роблять одне й те саме)? Як виявляємо silent failures (код проходить тести, але не виконує специфікацію)? Який defect escape rate (% дефектів, що проскочили в прод) на 30 / 60 / 90 днях post-merge?

**6. Доказова база: яку версію case study показуємо публічно і як методологічно міряли baseline?**
Що з матеріалів — pre-NDA (до підписання Non-Disclosure Agreement), post-NDA, чи лише на запрошених сесіях? Як методологічно міряли «before» (хронометраж, опитування, GitHub / Jira telemetry, чи estimate)? Якщо у заявлених покращеннях є діапазон — це варіація між суб-командами (iOS / Android / Web / Backend), чи між типами задач у межах однієї команди? Якщо частина артефактів у prompt library має виміряні метрики, а решта — ні, як ми це презентуємо? Як ми атрибутуємо вплив саме AI-tooling vs Hawthorne effect (покращення через сам факт спостереження) vs новий процес vs новий стек?

---

## Група 4: Інфраструктура і моделі

**7. AI Infrastructure: Model Selection + Token/Cost protection + Memory та MCP-композиція + TCO frontier vs self-hosted.**
Router (компонент, що обирає модель під задачу) — нативний у IDE чи власний proxy / gateway? Правила routing (за task type, complexity, sensitivity, cost budget)? Token Protection — rate limiting (обмеження частоти), budget caps, secrets scrubbing (видалення секретів з промптів перед відправкою у модель)? Cost Monitoring — як attribute cost per merged PR до конкретної задачі? Memory через Model Context Protocol (стандарт підключення агентів до зовнішніх систем) — що зберігається (decisions, learnings, context refs), storage backend, retention / eviction policy (правила видалення старих записів), як уникаємо contamination (просочування контексту) між проєктами / клієнтами? Композиція кількох MCPs — коли агент сам обирає MCP vs hard-coded chain, як обробляється latency / timeout / failure? Внутрішня eval-матриця Claude Opus 4.7 / GPT-5 / Gemini 3 / Qwen / DeepSeek з pass rate і cost per task — є? При якому місячному обсязі токенів власний GPU-кластер (H100 / H200 / Blackwell з vLLM або SGLang — inference servers для LLM) стає економічнішим за per-token API, з урахуванням амортизації і ops-хедкаунту?

---

## Група 5: Інструменти і переносність

**8. IDE-портативність методології і MCP-стек під різні шари стеку клієнта.**
Що з артефактів stack-portable (AGENTS.md — emerging community standard, MCPs — protocol-level), а що IDE-specific (project rules, modes — ask / plan / agent, prompt standards UI)? Якщо клієнт працює з Cursor, Claude Code (CLI від Anthropic), Codex CLI (CLI від OpenAI), Copilot чи Windsurf — який % методології переноситься як є? Як організовано MCP-набір під різні шари стеку: iOS (XcodeBuild, simulator), Android (Gradle, Compose), Web (browser DevTools, Playwright), Backend (Spring / .NET / Node debugger), Data (dbt, Airflow), Infra (Kubernetes, Terraform), Observability (Sentry, Datadog)? Це готові пакети, чи ad-hoc під кожного клієнта?

---

## Група 6: Склад команди і governance на масштабі

**9. Склад AI-native команди клієнта і governance на масштабі багатьох команд.**

*Форма пілотної команди:* яка оптимальна headcount, співвідношення інженерів до паралельних agent-сесій? Які legacy ролі (джуни, dedicated QA, manual testers) поглинаються, переучуються, чи виходять зі скоупу? Нові ролі — Spec Author (автор специфікацій), AI Architect, Context Engineer (інженер контексту, що володіє convention-файлами, rules, MCP-конфігами), Agent Supervisor (наглядач за паралельними agent-сесіями) — це окремі hire-и чи скіли, які надбудовуємо на існуючих сеньйорів? Як змінюється роль Tech Lead / Architect — скільки паралельних агентів реально супервізить один сеньйор без quality collapse (просідання якості при перевантаженні supervision capacity)? QA — куди йде, якщо агент сам пише і ганяє тести: spec review (рев'ю специфікацій до імплементації), adversarial testing (пошук слабких місць, edge cases, security flaws), eval authoring (написання тестових стендів для оцінки агентів), чи поступове скорочення функції?

*Governance на рівні багатьох команд:* хто володіє центральними specification templates, conventions, MCP integrations у клієнта — Center of Excellence (CoE — стратегічний R&D-шар), Guild (горизонтальна крос-команда спільнота практиків), окремий Platform team (команда, що володіє внутрішніми інструментами та інтеграціями)? Як ми запобігаємо «діалектам SDD» (розходженню методології між командами) — якщо команди використовують різні CLI / IDE-агенти і шарять monorepo (один Git-репо з кількома проєктами) або shared services, який governance тримає сумісність артефактів? Як reusable specs, prompts і agent rules шаряться між командами — internal marketplace (внутрішній маркетплейс артефактів з review-процесом), shared Git, hub-репо з convention-файлами, package registry — і хто валідує якість перед публікацією? Який ритм cross-team синхронізації реально працює (weekly demos, AI guild meetings, shared retros), а що з нашого досвіду виявилось theatre (формальністю без впливу на якість і швидкість)?

---

## Група 7: Комерційна модель

**10. SOW для типового engagement, трактування інвестиції клієнтського часу і захист ROI-claim.**
SOW (Statement of Work — контрактний документ зі скоупом і обов'язками) — fixed-fee, T&M (Time and Material — погодинна оплата за фактом), managed delivery з outcome SLA (Service Level Agreement)? Як ми трактуємо інвестицію часу команди клієнта у контракті — це fixed scope, кеп, чи розрахункова цифра? Як рахується ROI breakeven — формула, припущення, які саме дані? Який плейбук, якщо клієнт не дотягує до заявлених метрик — діагностичний чек-ліст, retry-формат у фінальній фазі ретроспективи, чи передбачені SLA / money-back умови?

