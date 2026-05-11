# Повний перелік питань до команди — AI SDLC Practice

**Контекст:** Перелік питань principal-dev рівня, згрупований за темами. Стосовно методології, інфраструктури, команди, governance, безпеки і комерції. Без посилань на внутрішні артефакти та конфіденційні метрики.

---

## Група 1: Скоуп оферингу і структура engagement

1. **Які пакети AI SDLC Practice сьогодні готові до продажу, а які поки що в розробці?** Чи є коротка діагностична матриця «зрілість клієнта / стек / розмір команди → рекомендований пакет» між Assessment, Introduction, Uplift, Module-based, Native Transition і Brownfield?
2. **Що клієнт отримує як артефакти на кожній фазі engagement (Clarify, Calibrate, Launch, Refine)?** Assessment report, capability map, target operating model, roadmap, ROI-модель — які з них хто пише, в якому порядку, і де ключові milestone-перевірки?
3. **Який мінімальний engagement по AI SDLC ми пропонуємо?** Окремий Assessment, пілот однієї команди, повний roll-out — який порог входу, щоб коректно скоупити перші розмови?

---

## Група 2: Методологія — SDD, поведінкові шари, control gates

4. **Spec Driven Development (методологія, де специфікація — primary artifact, а код — деривативний) — який формат специфікацій і як вони живуть разом з кодом?** EARS-style (Easy Approach to Requirements Syntax — «When [condition], the system shall [action]»), custom DSL (Domain-Specific Language), structured markdown? Як specs обробляють ambiguity, edge cases і non-functional requirements (performance, security, accessibility)? Чи specs версіонуються в тому ж репо, що й код, хто формальний owner (PM, Tech Lead, AI), і чи передбачено CI-gate (Continuous Integration check — автоматизована перевірка перед merge), що сигналізує про spec/code divergence?
5. **Шари конфігурації агента (project rules, AGENTS.md, ARCHITECTURE.md, IDE-specific rules) — як композуються?** Який precedence (порядок пріоритету) при конфлікті? Як ми перевіряємо, що зміна в rules не змінить поведінку агента на існуючих задачах (regression testing на rules)?
6. **HITL gates (Human-In-The-Loop — точки обов'язкового людського затвердження) — таксономія і захист від деградації.** Які класи змін агент не може merge без human approval (db migrations — зміни схеми БД, security-sensitive code, public API, infra changes)? Чи є distinction між plan-mode approval (агент показує план дій) і agent-mode approval (агент виконує дії автономно)? Як ми утримуємо HITL gate від деградації у формальний approve без читання — метрики на review depth і time-to-approve, мінімальний review SLA, escalation patterns?
7. **Brownfield SDD: чи генеруємо специфікації з існуючого коду (reverse SDD)?** Якщо так — як валідуємо, що згенеровані specs відображають справжній intent, а не accidental behaviour? Як обробляємо undocumented invariants (речі, що працюють по convention, ніде не описані)?

---

## Група 3: Якість і доказова база

8. **Eval methodology — що поза «тести зелені», і як міряємо escape rate?** Eval harness (тестовий стенд для оцінки агентів), property-based testing (тести з випадковими вхідними даними), mutation testing (вмисні зміни в коді для оцінки сили тестів), semantic equivalence (перевірка, що дві реалізації роблять одне й те саме)? Як виявляємо silent failures (код проходить тести, але не виконує специфікацію)? Який defect escape rate (% дефектів, що проскочили в прод) на 30 / 60 / 90 днях post-merge?
9. **Доказова база: яку версію case study показуємо публічно?** Pre-NDA (до підписання Non-Disclosure Agreement), post-NDA, чи лише на запрошених сесіях?
10. **Як методологічно міряли baseline?** Хронометраж, опитування, GitHub / Jira telemetry, чи estimate? Якщо у заявлених покращеннях є діапазон — це варіація між суб-командами (iOS / Android / Web / Backend), чи між типами задач у межах однієї команди?
11. **Як ми атрибутуємо вплив саме AI-tooling vs Hawthorne effect (покращення через сам факт спостереження) vs новий процес vs новий стек?**

---

## Група 4: Інфраструктура і моделі

12. **Model Selection — router (компонент, що обирає модель під задачу) нативний у IDE чи власний proxy / gateway?** Правила routing (за task type, complexity, sensitivity, cost budget)?
13. **Token Protection і Cost Monitoring — що під капотом?** Rate limiting (обмеження частоти), budget caps, secrets scrubbing (видалення секретів з промптів перед відправкою у модель)? Як attribute cost per merged PR до конкретної задачі?
14. **Memory через Model Context Protocol (стандарт підключення агентів до зовнішніх систем) — як влаштована persistent memory агента?** Що зберігається (decisions, learnings, context references), storage backend, retention / eviction policy (правила видалення старих записів)? Як уникаємо contamination (просочування контексту) між проєктами / клієнтами?
15. **Композиція кількох MCPs — як вони взаємодіють?** Коли агент сам обирає MCP vs hard-coded chain у промпті? Latency, timeout, failure handling — якщо один MCP не відповідає, що відбувається? Security model — кожен MCP має свій scope доступу, чи це shared credentials?
16. **TCO frontier API vs self-hosting: де точка перетину?** Внутрішня eval-матриця Claude Opus 4.7 / GPT-5 / Gemini 3 / Qwen / DeepSeek з pass rate і cost per task — є? При якому місячному обсязі токенів власний GPU-кластер (H100 / H200 / Blackwell з vLLM або SGLang — inference servers для LLM) стає економічнішим за per-token API, з амортизацією і ops-хедкаунтом?
17. **Як ми проходимо через зміни моделей?** Коли вендор релізить нову версію або депрекейтить SKU — який regression suite, canary-процес, скільки rework падає на клієнтські специфікації? Наші промпти і specs портабельні між вендорами?

---

## Група 5: Інструменти і переносність

18. **IDE-портативність методології — що stack-portable vs IDE-specific?** AGENTS.md (emerging community standard), MCPs (protocol-level) переносяться; project rules, modes (ask / plan / agent), prompt standards UI — IDE-specific. Який % методології переноситься як є між Cursor, Claude Code (CLI від Anthropic), Codex CLI (CLI від OpenAI), Copilot, Windsurf?
19. **Як організовано MCP-набір під різні шари стеку клієнта?** iOS (XcodeBuild, simulator), Android (Gradle, Compose), Web (browser DevTools, Playwright), Backend (Spring / .NET / Node debugger), Data (dbt, Airflow), Infra (Kubernetes, Terraform), Observability (Sentry, Datadog) — це готові пакети, чи ad-hoc під кожного клієнта?

---

## Група 6: Склад команди і governance на масштабі

20. **Форма пілотної команди і роль Tech Lead.** Оптимальна headcount, ratio інженерів до паралельних agent-сесій, які legacy ролі (джуни, dedicated QA, manual testers) поглинаються, переучуються, чи виходять зі скоупу? Як змінюється роль Tech Lead / Architect — скільки паралельних агентів реально супервізить один сеньйор без quality collapse (просідання якості при перевантаженні supervision capacity)?
21. **Нові ролі — jobs чи skills?** Spec Author (автор специфікацій), AI Architect, Context Engineer (інженер контексту, що володіє convention-файлами, rules, MCP-конфігами), Agent Supervisor (наглядач за паралельними agent-сесіями) — це окремі hire-и чи скіли, які надбудовуємо на існуючих сеньйорів?
22. **Що відбувається з QA, якщо агент сам пише і ганяє тести?** Куди йде функція: spec review (рев'ю специфікацій до імплементації), adversarial testing (пошук слабких місць, edge cases, security flaws), eval authoring (написання тестових стендів), чи поступове скорочення?
23. **Хто володіє central artifacts на масштабі багатьох команд, і як вони шаряться?** Specification templates, conventions, MCP integrations — Center of Excellence (CoE — стратегічний R&D-шар), Guild (горизонтальна крос-команда спільнота практиків), окремий Platform team (команда, що володіє внутрішніми інструментами та інтеграціями)? Як reusable specs, prompts і agent rules шаряться — internal marketplace (внутрішній маркетплейс артефактів з review-процесом), shared Git, hub-репо з convention-файлами, package registry — і хто валідує якість перед публікацією?
24. **Як ми запобігаємо «діалектам SDD» (розходженню методології між командами)?** Якщо команди використовують різні CLI / IDE-агенти і шарять monorepo (один Git-репо з кількома проєктами) або shared services, який governance тримає сумісність артефактів?

---

## Група 7: Безпека, дата, комплаєнс

25. **Як виглядає потік даних, коли інженер клієнта вставляє prod-код в агента?** Що стрипається, що логується, що йде на frontier-ендпоінт, яка retention-політика у вендора, де в архітектурі сидить DLP (Data Loss Prevention — шар запобігання витоку даних)?
26. **Чи маємо ми готовий EU AI Act чек-ліст для high-risk клієнтів (medtech, fintech, critical infrastructure)?** Докази human oversight, traceability від spec → PR → merge, пакет документів для аудиту?
27. **Як ми документуємо «creative human modification» для копірайту?** Чи є тулінг, який дифає agent output проти фінального коміту і генерує audit-артефакт, чи цей процес поки що ручний?

---

## Група 8: Комерційна модель

28. **SOW shape, rate card і інвестиція часу клієнта.** Як виглядає SOW (Statement of Work — контрактний документ зі скоупом і обов'язками) для типового engagement — fixed-fee, T&M (Time and Material — погодинна оплата за фактом), managed delivery з outcome SLA (Service Level Agreement)? Чи є rate card для weighted time (skill level × AI provisioning tier) → $/день, і як ми обґрунтовуємо AI-native премію порівняно з традиційним T&M? Як ми трактуємо інвестицію часу команди клієнта у контракті — fixed scope, кеп, чи розрахункова цифра?
29. **Як рахується ROI breakeven?** Формула, припущення, які саме дані?
30. **Який наш плейбук, якщо клієнт не дотягує до заявлених метрик?** Діагностичний чек-ліст (специфікація, модель, кодова база, команда), retry-формат у фінальній фазі ретроспективи, передбачені SLA / money-back умови?
