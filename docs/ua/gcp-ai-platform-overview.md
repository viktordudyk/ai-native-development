# GCP AI-стек, з AWS і Azure на іншому боці столу

*Створено: 13 травня 2026. Звірено зі звітом Deep Research, див. [docs/_sources/gcp-ai-platform-deep-research-may-2026.md](../_sources/gcp-ai-platform-deep-research-may-2026.md).*

Кожен, хто консультує з AI-архітектури у 2026, мусить мати робочу модель усіх трьох гіперскейлерських стеків, навіть якщо основний деплоймент стоїть на одному з них. Клієнти питають. RFP-и питають. Член правління, який щойно прочитав статтю в Bloomberg, теж питає. До того ж платформи знову перейменували себе. Vertex AI як бренд офіційно припинив існування на Cloud Next 22 квітня 2026, а весь стек консолідували під Gemini Enterprise Agent Platform. Azure AI Foundry прибрав префікс "Azure" 1 січня 2026 (анонс на Ignite 18 листопада 2025) і став Microsoft Foundry. AWS зберіг decoupled-підхід, але затягнув tripartite-наратив навколо Amazon Bedrock, SageMaker Unified Studio і Bedrock AgentCore. Брендинг новий. Можливості переважно ні.

Це референс для команд із центром тяжіння на Google Cloud, яким треба пояснювати або захищати цей вибір, коли клієнти питають про AWS-чи-Azure-еквівалент. GCP-first, з еквівалентами поряд у тій самій секції. Не buying-гайд. Не бенчмарк. Не маркетинг жодного з трьох.

Скоуп. Тільки гіперскейлери: ні Snowflake, ні Databricks, ні Oracle, ні Alibaba. Без глибокого занурення в data-side AI-продукти типу BigQuery ML, Looker або Fabric. Дані актуальні станом на травень 2026, назви SKU встигнуть протухнути за рік.

---

## Шпаргалка по трьох хмарах

| Категорія | GCP | AWS | Azure |
|---|---|---|---|
| Уніфікована AI-платформа | Gemini Enterprise Agent Platform (перейменовано з Vertex AI, 22 квіт 2026) | Bedrock + SageMaker Unified Studio + AgentCore | Microsoft Foundry (перейменовано з Azure AI Foundry, 1 січ 2026) |
| Хаб foundation-моделей | Vertex AI Model Garden, 200+ моделей | Amazon Bedrock | Foundry Models, 11000+ записів (включно з Hugging Face дзеркалами) |
| Власні LLM | Gemini 3.1 Pro, 3 Flash, 3.1 Flash-Lite | Amazon Nova (Premier, Pro, Lite, Micro), Titan | GPT-5.4, 5.3-codex, 5.2, 5.1 через Azure OpenAI |
| Моделі Anthropic | Claude Opus 4.5, Sonnet 4.6, Haiku 4.5 (Vertex) | Claude Opus 4.7, Sonnet 4.6, Haiku 4.5 | Claude через Marketplace billing |
| Генерація зображень | Imagen 4 (preview), Virtual Try-On | Nova Canvas v1, Stability | GPT-image-2, FLUX.2-pro, FLUX.2-flex (preview) |
| Генерація відео | Veo 3.1 (GA), Veo 3.1 Lite (preview) | Nova Reel v1 (до 2 хв) | Sora 2 (виводиться 3 червня 2026) |
| Генерація музики | Lyria 3 (preview, 25 бер 2026) | нема | нема |
| Рантайм для агентів | Vertex AI Agent Runtime (перейменовано з Agent Engine, 11 лют 2026) | Bedrock AgentCore (+ x402 payments preview) | Foundry Agent Service |
| Фреймворк для агентів | Agent Development Kit (ADK) | Strands, Bedrock Agents | Semantic Kernel, AutoGen |
| Low-code білдер | Agent Builder, Express Mode | Bedrock Agents | Copilot Studio |
| RAG і knowledge | Vertex AI Search + Vector Search 2.0 | Bedrock Knowledge Bases | Foundry IQ + Azure AI Search |
| IDE-асистент | Gemini Code Assist | Amazon Q Developer, Kiro | GitHub Copilot |
| CLI-асистент | Gemini CLI | Q Developer CLI | GitHub Copilot CLI |
| Власний кремній для тренування | TPU Trillium (v6e), Ironwood (v7) | Trainium 2, Trainium 3 | Maia 100, Maia 200 |
| GPU-варіанти | H100, H200, B200 (A4) | P5, P5en, P6 | ND H100/H200 v5/v6 |
| MCP-підтримка | Cloud API Registry | AgentCore Tool Gateway | Foundry Agent Service |
| Free tier | $300 кредитів, 90 днів | 12 місяців free tier на багатьох сервісах | $200 кредитів, 30 днів |

Далі по документу проходимо кожен рядок глибше.

---

## Core AI/ML платформа

Gemini Enterprise Agent Platform це нова парасолькова назва для того, що раніше було Vertex AI. Перейменування відбулося на Cloud Next 22 квітня 2026, і це більше за косметику. Google підняв Model Garden, Vertex AI Search, Vector Search 2.0, AutoML, agent-рантайм (тепер Agent Runtime), пайплайни, registry, feature store і evaluation під одну білінгову поверхню та одну IAM-модель. Бренд Vertex AI досі в URL-ах і назвах SDK. Якщо ви пишете IaC проти `aiplatform.googleapis.com`, нічого не зламалось. Намір консолідації полягав у тому, щоб виштовхнути розробників до agent-shaped навантажень як дефолту, а не до stateless-викликів моделей.

Що змінилось у цьому поколінні:

- Серія Gemini 3 ходить як Gemini 3.1 Pro (флагман, preview з 19 лютого 2026), Gemini 3 Flash (preview з січня 2026) і Gemini 3.1 Flash-Lite (GA з 7 травня 2026). Усі троє з контекстом 1 мільйон токенів. Попередній ендпоінт `gemini-3-pro-preview` примусово вивели 26 березня 2026, що спричинило hard-migration скрембл у командах, які не стежили за changelog.
- Claude на Vertex покриває Opus 4.5, Sonnet 4.6 і Haiku 4.5, білінг проти Google Cloud spend через партнерство з Anthropic. Зауваження: Vertex відстає на одне major-покоління від Bedrock на Opus (4.5 проти Bedrock 4.7).
- Model Garden перетнув позначку 200 first-party і партнерських моделей, включно з DeepSeek, GLM 5, MiniMax, Mistral, Llama 4 і довгим хвостом fine-tuned відкритих ваг.

Pricing-снепшот по Gemini-сімейству на Vertex (за 1M токенів, input / output):

| Модель | Input ≤200K | Input >200K | Output ≤200K | Output >200K |
|---|---|---|---|---|
| Gemini 3.1 Pro | $2.00 | $4.00 | $12.00 | $24.00 |
| Gemini 3 Flash | $0.50 | $0.50 | $3.00 | $3.00 |
| Gemini 3.1 Flash-Lite | $0.25 | $0.25 | $1.50 | $1.50 |

Кешування контексту коштує $0.025 за 1M закешованих text/image/video токенів; $0.05 за 1M audio-токенів; плюс $1.00 за 1M токенів на годину як storage-базлайн. Для будь-якого навантаження, що перечитує корпус, кешування це найбільший важіль на розмір рахунку.

Вибір між Gemini-варіантами на практиці простий. Gemini 3.1 Pro це дефолт для всього, що потребує reasoning над довгими інпутами, планування для агентів або складного мультимодального розуміння. Gemini 3 Flash тримає high-volume шар (класифікація, екстракція, саммарайз, рутинний інференс) за частку ціни. Gemini 3.1 Flash-Lite це новий найдешевший рівень, корисний для cost-sensitive bulk-пайплайнів, де Flash уже надлишок. Claude Opus 4.5 на Vertex це вибір для команд, яким потрібні сильні сторони Anthropic у instruction following і якості письма, але вони хочуть тримати весь spend на Google Cloud, з caveat що Bedrock має новіший Opus 4.7. Прагматичний прод-стек тримає три з них: Flash або Flash-Lite для high-volume шару, 3.1 Pro робочою конячкою, і escalation до 200K+ контексту або Claude для найскладніших запитів з роутером попереду.

Vertex AI Search це менеджед-RAG і enterprise-search продукт. Із коробки індексує Cloud Storage, BigQuery, SharePoint, Confluence, Salesforce, Jira та довгий список інших. Vector Search 2.0 це векторна БД під капотом, з індексами для filtered ANN, hybrid (dense + sparse) retrieval і grounding-колбеками, що зашиті прямо в Gemini API.

Арифметику вартості RAG варто перевіряти перед коммітом. Vertex AI Search Standard коштує $1.50 за 1000 запитів. Enterprise edition з Core Generative Answers це $4.00 за 1000 запитів, а Advanced Generative Answers додає ще $4.00 за 1000 user input queries поверх цього. Self-hosting на AlloyDB з pgvector скорочує per-query вартість приблизно вдвічі на масштабі, але людська ціна ingestion-пайплайнів, чанкінгу, вибору ембедингів, тюнінгу реренкера та метрик додається в квартал роботи невеликої команди. Для більшості команд до 100 000 запитів на день математика на боці Vertex AI Search. Вище цього об'єму питання реальне, і відповідь зазвичай гібридна: Vertex AI Search для довгого хвоста, кастомне для гарячого шляху.

Grounding це фіча, яка відрізняє Vertex від сирого API моделі. Gemini-виклики на Vertex можна грундити проти Google Search (даючи моделі живий доступ до вебу з цитатами), проти Vertex AI Search (кастомні корпуси), або проти обох одночасно. Web grounding це killer-feature для всього, що потребує актуальних фактів. AWS і Azure обидва пропонують grounding на кастомних корпусах, але жоден не має first-party еквіваленту живого web grounding порівнянної якості. Bedrock додав web search action у кінці 2025, а Foundry має Bing grounding через окремий конектор, але індекс Google Search плюс глибина інтеграції ось ширший рів у цій категорії.

MLOps на Vertex зрілий тим способом, яким зрілі речі бувають нудними. Vertex AI Pipelines використовує Kubeflow Pipelines під капотом з Google-керованим control plane. Model Registry, Feature Store, Experiments і Model Monitoring, усе GA і прилично заінструментовано. Vertex AI Evaluation Service це новіший шматок, заточений на LLM- і агент-evaluation з метриками для groundedness, instruction following, safety і rubric-скорингу за кастомними критеріями.

AutoML досі тут для tabular, image, video і text задач, але центр тяжіння переїхав на Gemini fine-tuning. Для більшості use-кейсів, які у 2023 були б AutoML, рекомендація зараз така: LoRA або повний fine-tuning на Gemini 3 Flash, з деплоєм на менеджед-ендпоінт. AutoML Tables зокрема склали в BigQuery ML.

**AWS-еквівалент.** Amazon Bedrock це model hub. SageMaker Unified Studio це workbench. Двоє зшиті, але досі відчуваються як два продукти зі спільним sign-on. Bedrock хостить найглибший Anthropic-каталог серед трьох хмар: Claude Opus 4.7 (квітень 2026, $5 / $25 за 1M), Sonnet 4.6 (лютий 2026, $3 / $15) і Haiku 4.5 (жовтень 2025, $1 / $5), плюс лінійку Amazon Nova. Nova Premier сидить на $1.25 / $6.25 за 1M, Nova Pro на $0.80 / $3.20, Nova Lite на $0.06 / $0.24, а Nova Micro на $0.035 / $0.14, що робить Micro найдешевшою серйозною моделлю на будь-якій хмарі. Bedrock Knowledge Bases це RAG-офер, дефолтить на OpenSearch Serverless. Один підводний камінь варто підняти: OpenSearch Serverless вимагає мінімум 2 OCU за $0.24/OCU-годину, що флорить вартість на ~$350 на місяць ще до першого запиту. AWS випустив S3 Vectors наприкінці 2025, щоб це закрити. Bedrock також підтримує batch inference з 50% знижкою проти on-demand, реальний важіль для high-throughput async-пайплайнів.

**Azure-еквівалент.** Microsoft Foundry консолідував Azure AI Foundry, Azure OpenAI Service і частину Azure ML в один портал 1 січня 2026. Foundry Models рекламує 11000+ записів, стрибок із цифри 1900, що ходила через 2025. Більшість нового count це community fine-tunes і Hugging Face дзеркала, а не first-party додавання, тож заголовна цифра радше SEO-claim, ніж корисний сигнал. Стратегічна глибина в Azure OpenAI: GPT-5.4 (березень 2026, $2.50 / $15 за 1M), GPT-5.3-codex (лютий 2026, $1.75 / $14), GPT-5.2 (грудень 2025) і GPT-5.1 (листопад 2025), усі ексклюзивно на Azure поза власним API OpenAI. Azure агресивно знижує cached input. У GPT-5.3-codex cached input падає з $1.75 до $0.18, це 10x скорочення, що матеріально змінює економіку long-context coding-навантажень. Azure AI Search це RAG-продукт, зрілий і добре інтегрований з Microsoft 365-джерелами через Foundry IQ, білінг $0.022 за 1M токенів з 50M free tier місячно. Operational-тулзи Foundry IQ мають свої лайн-айтеми: Code Interpreter $0.033 за сесію, Web Search через Bing $14.00 за 1000 транзакцій.

**Pick GCP when** ваше reasoning-навантаження виграє від 1M контекстного вікна Gemini 3.1 Pro, потрібен нативний мультимодальний хендлінг без пайплайн-клею, або дані вже живуть в BigQuery. **Look elsewhere when** свіжість моделей Anthropic важить найбільше (Bedrock має Opus 4.7 проти Vertex 4.5), або ваш identity-і-документ план уже на Microsoft 365 (Azure веде). Vector Search на GCP хороший, але не унікальний. Не вибирайте GCP лише заради vector search.

---

## Бенчмарки

Снепшот станом на травень 2026. Фронтир тісно оспорений, і "найкраща" модель сильно залежить від задачі. Self-reported vendor-скори тут поширені; цифри нижче взяті з найширше цитованих публічних джерел, але мають читатися як приблизні.

| Бенчмарк | Gemini 3.1 Pro | Claude Opus 4.6 | Claude Sonnet 4.6 | GPT-5.5 | GPT-5.4 | GPT-5.3-codex |
|---|---|---|---|---|---|---|
| SWE-bench Verified | 80.6% | 80.8% | 79.6% | 82.6% | 78.2% | 78.0% |
| SWE-bench Pro | 54.2% | - | - | - | - | 56.8% |
| LiveCodeBench Pro (Elo) | 2887 | - | - | - | - | - |
| MMLU-Pro (MMMLU) | 92.6% | - | 89.3% | - | - | - |
| HumanEval pass@1 | 89.2% | 90.4% | - | - | 93.1% | - |
| GPQA Diamond | 94.3% | 91.3% | 89.9% | - | 92.0% | - |

Агентні та tool-use бенчмарки (vendor або community reported):

- **TAU-bench (retail):** Claude Opus 4.6 веде з 91.9%, Sonnet 4.6 на 91.7%, Gemini 3.1 Pro на 90.8%.
- **Terminal-Bench 2.0:** GPT-5.3-codex домінує з 77.3%, помітно попереду Gemini 3.1 Pro (68.5%) і Claude Opus 4.6 (65.4%).
- **GDPval-AA Elo (expert office tasks):** Claude Sonnet 4.6 (1633 Elo) веде, попереду Opus 4.6 (1606).

Чесне прочитання: GPT-5.5 і Claude Opus 4.6 тримають незначну перевагу на single-shot software engineering задачах. Gemini 3.1 Pro найкращий на hard reasoning (GPQA Diamond) і competitive coding logic (LiveCodeBench Pro). GPT-5.3-codex беззаперечний переможець на long-running termінал-роботі. Хто серйозно ставить на прод-агентів, той роутить за задачами, а не вибирає одну модель і називає це готовим. Ці цифри будуть частково неправильні протягом кварталу. Патерн (спеціалізовані лідери по доменах) живе довше за конкретні скори.

---

## Агенти та dev-інструменти

Агенти на GCP лягають шарами: framework, runtime, builder і dev-поверхня. Кожен шматок використовується окремо, але інтеграційний наратив фактично і є продукт.

Agent Development Kit (ADK) це framework. Open-source, Python і Go, побудований на патернах, схожих на LangGraph, але з власними примітивами Google для tool use, planning, multi-agent handoff і memory. ADK працює проти будь-якої Gemini- або Vertex-hosted моделі і деплоїться на будь-що, що крутить Python, але канонічна ціль для нього це Agent Runtime.

Vertex AI Agent Runtime (перейменовано з Agent Engine 11 лютого 2026) це менеджед-runtime. Персистентні агентські сесії, conversation memory до 8 годин, sandboxed виконання коду, інтеграція з Vertex AI Search для grounding, IAM-aware виклики тулзів. Білінг перейшов на compute-based під час перейменування: $0.0864 за vCPU-годину ($0.00144 за хвилину), $0.0090 за GB-годину для memory, плюс $0.25 за 1000 збережених memories для long-term session state. Agent Runtime коштує більше, ніж запуск ADK на Cloud Run, але поглинає смислову купу плумбінгу: session state, retries, tracing, інтеграцію з evals і довгий хвіст "ваш агент крутився 40 хвилин і помер, де нам шукати".

Реальний розрив у вартості проти self-managed менший, ніж здається. Той самий агент на Cloud Run з self-managed Redis для сесій вийде приблизно на 30 відсотків дешевше, якщо утилізація висока, але щойно треба додати tracing, retries, multi-region failover і eval-пайплайн, розрив закривається. Більшість команд, що це бенчмарчать, осідають на Agent Runtime для проду і Cloud Run для офлайн-експериментів.

Agent Builder це low-code поверхня. Drag-and-drop підключення тулзів, специфікація цілей природною мовою і шлях до деплою, що закінчується на Agent Runtime. Express Mode (free tier для Agent Builder) дає ~$300 кредитів і дозволяє non-engineering команді підняти функціональний внутрішній агент за пів дня. Це справді корисно для прототипування. Це не там, де житимуть більшість прод-агентів.

Gemini Code Assist це IDE-інтеграція. VS Code, JetBrains, Android Studio, Cloud Workstations, з чатом, code completion, трансформаціями і inline-поясненнями. Диференціатор проти GitHub Copilot це repository-scale контекст. Code Assist Enterprise індексує приватне репо клієнта і підіймає результати під час inline-генерації. Для команд з монорепо це важить більше за бенчмарк-скори.

Gemini CLI це термінальний компаньйон. Вийшов у GA наприкінці 2025, освіжений на Cloud Next 2026 з підтримкою екстеншенів, слеш-командами, multi-file edit і реєстром MCP-серверів. Це найближчий GCP-нативний аналог Claude Code або Codex CLI, і розрив закрився настільки, що вибір між ними тепер питання преференцій до моделі та екосистеми, а не парітету фіч.

MCP-підтримка по платформі. Google викотив Cloud API Registry, що експонує GCP-сервіси (BigQuery, Cloud Run, Spanner, Vertex AI і довгий хвіст) як MCP-сервери. Agent Runtime приймає MCP-сервери як тулзи нативно. Gemini CLI має реєстр MCP-серверів.

**AWS-еквівалент.** AgentCore це runtime, GA з середини 2025. Персистентна memory, tool gateway з MCP-підтримкою, identity-інтеграція та observability. Найцікавіше нещодавнє додавання це payments preview від 7 травня 2026, що дозволяє агентам транзактити через гаманці Coinbase або Stripe через протокол x402 під час reasoning. Якщо ваш агент має автономно купувати compute, API-виклики або third-party тулзи, AWS це єдина хмара, де це first-class capability сьогодні. Strands це open-source framework, менша спільнота за ADK, але добре задокументований. Bedrock Agents це low-code білдер, оріентований навколо Bedrock Knowledge Bases як grounding-шару. Amazon Q Developer це IDE-асистент. Kiro це новіша spec-driven IDE від AWS, вийшла наприкінці 2025, з сильнішою автономією на multi-file задачах. Q Developer CLI це термінальна поверхня.

**Azure-еквівалент.** Foundry Agent Service це runtime, запечений у Foundry-портал з тісною інтеграцією Entra ID і Microsoft Graph. Жодного orchestration-markup: платите за токени або PTU моделей під капотом. Foundry IQ це context-шар, що індексує SharePoint, Blob Storage, Fabric і OneDrive без per-джерельних пайплайнів. Operational-тулзи мають окремий pricing, як зазначено вище. Copilot Studio це low-code білдер, і він найвідшліфованіший з трьох low-code агент-продуктів вендорів, частково тому що Microsoft ітерує над лінією Bot Framework десять років. Semantic Kernel і AutoGen це open-source фреймворки, обидва Microsoft-led. GitHub Copilot це IDE-асистент з найширшою install-base серед усіх IDE AI-тулзів.

**Pick GCP when** команда вже пише агентів на Python і хоче чистого runtime-наративу (Agent Runtime плюс ADK це найменш surprising стек з трьох). **Look elsewhere when** агенти мають діяти всередині Microsoft 365 (Foundry IQ плюс Copilot Studio виграє), або коли автономні agent payments частина use-кейсу (AgentCore з x402 єдиний first-party варіант сьогодні). Платформи для агентів це місце, де lock-in відбувається найшвидше у 2026. Вибір цього року це рішення на три роки.

---

## Спеціалізовані AI-API

Більшість спеціалізованої AI-поверхні це commodity. API роблять приблизно те саме з вимірюваними, але не стратегічними відмінностями в якості. Стратегічні відмінності в моделях ціноутворення, регіональній доступності й ергономіці, а не в якості виходу.

Згрупуємо за призначенням: перцепція проти генерації.

### Перцепція

Vision AI (Cloud Vision API) робить label detection, OCR, face detection, landmark detection і safe-search. Vertex AI Vision це кузен-продукт для відеоаналітики на edge, зі стрім-процессингом і event-based тригерами для камер та подібного.

Document AI це OCR-plus-extraction. Pre-trained процессори для інвойсів, чеків, W-2, 1099, контрактів і генеричний form-parser. Custom Document Extractor дозволяє команді fine-tune-ити екстракцію на власних схемах документів з кількома сотнями labeled-прикладів. Білінг per-page, з істотними volume-discounts для high-throughput пайплайнів.

Speech-to-Text використовує Chirp 2 (вийшов наприкінці 2025), USM-архітектура, підтримка 125+ мов плюс on-device варіанти. Text-to-Speech має Studio-голоси для high-fidelity синтезу і Standard-голоси для дешевого bulk-синтезу. Обидва API battle-tested.

Cloud Translation має два тіри. Basic це straightforward NMT для high-volume low-cost перекладу. Advanced підтримує глосарії, batch translation і document translation зі збереженням форматування.

Natural Language API робить entity extraction, sentiment analysis, syntax parsing і content classification. Чесний take на 2026: цей продукт переважно obsolete для нових білдів. Виклик Gemini 3 Flash зі структурованою схемою виходу дає кращі результати за порівнянну ціну для entity- і sentiment-задач, з додатковим бонусом підтримки будь-якої кастомної схеми.

**AWS-еквівалент.** Rekognition для vision, Textract для OCR, Transcribe і Polly для speech, Translate для перекладу, Comprehend для NLP. Здатності мапляться чисто. Textract має невелику перевагу на екстракції форм для US-tax і фінансових документів. Polly Neural2-голоси сильні. Comprehend Medical існує як HIPAA-aligned варіант для клінічного NLP.

**Azure-еквівалент.** Azure AI Vision, AI Document Intelligence, AI Speech, AI Translator, AI Language. Document Intelligence має найзрілішу pre-built layout-модель і часто це правильний вибір для складних multi-column документів і таблиць. AI Speech має найкращу історію кастомного клонування голосу серед трьох (Custom Neural Voice).

### Генерація

Imagen 4 це поточна модель генерації зображень на Vertex, у public preview станом на травень 2026. Фотореалістичний вихід, сильне prompt adherence, рендеринг тексту всередині зображень, який реально працює. Virtual Try-On це нішевий Imagen-варіант для ecommerce, який міняє одяг на людських зображеннях на прод-якості.

Veo 3.1 (GA з липня 2025) генерує відео до 8 секунд у 1080p із синхронізованим аудіо, включно з діалогом і звуковим середовищем. Veo 3.1 Lite це здешевлений preview-варіант з березня 2026 для прототипування. Veo тримається найздатнішою video-моделлю серед трьох гіперскейлерів у травні 2026, трохи попереду Sora на coherent multi-character сценах і помітно попереду Nova Reel на audio-інтеграції.

Lyria 3 (preview з 25 березня 2026) це модель генерації музики у двох варіантах. Lyria 3 Pro генерує повноформатні пісні за $0.08 за пісню з 48kHz stereo на виході, включно з coherent вокалом та інструментальними аранжуваннями. Lyria 3 Clip генерує 30-секундні кліпи. У травні 2026 на AWS і Azure нема конкурентної first-party музичної моделі.

**AWS-еквівалент.** Nova Canvas v1 для генерації зображень ($0.04 - $0.08 за зображення залежно від роздільної здатності), Nova Reel v1 для відео ($0.08 за секунду, до 2 хвилин). Stability і Black Forest Labs доступні через Bedrock Marketplace. 2-хвилинна довжина Nova Reel найдовша first-party для відео на будь-якій хмарі, хоча якість виходу відстає від Veo.

**Azure-еквівалент.** GPT-image-2 це поточна OpenAI-модель для зображень на Foundry, підтримує text-to-image, inpainting і роздільності до 3840px (3:1 ratio). Token-based pricing на $5 за 1M input токенів. FLUX.2-pro і FLUX.2-flex від Black Forest Labs увійшли в preview у лютому 2026, причому FLUX.2-flex спеціально тюнінгований для UI-прототипування, складної типографіки і multi-reference editing, території, де стандартні моделі зображень буксують. Pricing $0.05 - $0.07 за згенерований мегапіксель.

Sora 2 доступна через Azure OpenAI для відео, але з серйозною операційною проблемою, яку варто винести окремо. Microsoft запланував раннє виведення Sora 2 на 3 червня 2026, попри те що OpenAI у власному API продовжує підтримку до 24 вересня 2026. Це найбільш disruptive lifecycle-рішення на будь-якій хмарі у 2026, і воно зараз провокує migration-скрембли серед Azure-resident медіа-команд. Хто ставить прод-медіа-навантаження на Azure, має планувати навколо цього розриву до червня.

Один регіональний нюанс варто підняти на generation-моделях. Imagen, Veo і Lyria переважно подаються з us-central1, us-east1, us-west1, us-west4 і europe-west4 на Vertex. Клієнти поза Північною Америкою і Західною Європою платять egress-податок за медіа-генерацію, якщо не реплікують навантаження. Sora 2 на Azure концентрується в East US 2, Sweden Central і South Central US протягом залишку життя. Nova Reel широко доступний на AWS. Для медіа-важких навантажень у регіонах поза Північною Америкою і Західною Європою правильна відповідь часто гібридна: multi-region деплой із кешуванням, а не вибір іншої хмари.

---

## Інфраструктура та pricing

Рішення про кремній кидає найдовшу тінь у всьому стеці. Щойно команда тренує або сервить на певному сімействі акселераторів, переїзд звідти це проект, не клік. Цей розділ покриває, що пропонує кожна хмара і де реальні точки lock-in.

### Кремній на GCP

TPU Trillium (v6e) це поточний загальнопризначений тензорний акселератор, доступний у us-central1, us-east1, us-east5, europe-west4 та asia-northeast1. Кожен чип дає 918 TFLOPs peak BF16 compute і 1836 TOPs INT8, з 32 GB HBM на 1638 GiBps. Pod-и масштабуються до 256 чипів у 2D torus топології. Inter-chip interconnect це 800 GBps на чип (6.4 Tbps aggregate по 4-чиповому slice). Trillium додає third-generation SparseCores для embedding-важких навантажень, що матеріально допомагає ranking і recommendation системам. Pricing на Trillium приблизно на 30-40 відсотків дешевший за FLOP, ніж еквівалентна H100-ємність на GCP для тренувальних навантажень, що влазять у TPU programming model.

TPU Ironwood (v7) вийшов на Cloud Next 2026, спеціально позиціонується для "age of inference", а не тренування. Архітектура це dual-chiplet дизайн з 4614 TFLOPs peak FP8 compute на чип, 192 GB HBM3e на 7.38 TB/s, і перехід з 2D на 3D torus топологію з pod-ами, що масштабуються до 9216 чипів. Per-chip ICI bandwidth це 1200 GBps bidirectional. 192 GB memory footprint важить: дозволяє Ironwood сервити 1M контекст Gemini 3 з повним KV cache резидентним, без memory thrashing. Google внутрішньо сервить Gemini 3 на Ironwood, зовнішня ємність котиться через 2026.

TPU lock-in реальний. JAX і TensorFlow це first-class на TPU. PyTorch працює через PyTorch/XLA, але з нерівними краями, зокрема навколо кастомних CUDA-кернелів, для яких нема XLA-еквівалентів. Команди, які мають тренувати і сервити на TPU, мусять планувати JAX-centric стек, що є і реальною ціною, і реальним бенефітом. JAX щиро швидший для ітерації за еквівалентний PyTorch-код на багатьох воркload-ах, але talent pool менший.

GPU на GCP це альтернатива. H100, H200 і B200 усі доступні, з machine type-ами A3 Ultra (H100), A3 Mega (H100) і A4 (B200). A4-інстанси стали GA на початку 2026 і це прод-ціль для команд, яким треба B200 поза CoreWeave чи Crusoe. B200 сам дає 4500 TFLOPS FP8 з 192 GB HBM3e і 8 TB/s memory bandwidth, це бенчмарк, проти якого вимірюються Trillium, Ironwood, Trainium 3 і Maia 200.

TPU networking важить так само, як TPU compute, для будь-кого, хто тренує на масштабі. Single-pod тренування (приблизно до 70B параметрів з типовими memory footprint-ами, зазвичай через multi-slice deployments по 32 чипах) це точка, де економіка TPU найпереконливіша. Вище pod-масштабу Google зв'язує pod-и через data center network з помітним падінням bandwidth. Multi-pod TPU-конфігурації доступні за резервацією. Вище цього калькуляція зміщається до GPU-кластерів із NVLink та InfiniBand.

### Pricing-моделі

Pay-as-you-go це дефолт. Committed Use Discounts (CUDs) дають 20-57 відсотків економії за 1- або 3-річні коміти. Dynamic Workload Scheduler це новіший flex-commitment-офер, з двома смаками: Flex Start для нетермінових тренувальних задач, які можуть зачекати на ємність хвилини або години, і Calendar Mode для резервацій на конкретні майбутні дати. Vertex AI Provisioned Throughput це dedicated-ємність для інференсу Gemini, білінг за Generative AI Scale Unit (GSU), а не за токен, корисний для прогнозованого high-volume сервінгу.

Free tier. Стандартні $300 кредитів Google Cloud на 90 днів покривають усі AI-сервіси. Express Mode для Agent Builder це менший AI-specific credit pool, націлений на побудову першого агента за нуль. Vertex AI як такий не має per-product free tier-у поверх $300, хоча кілька спеціалізованих API (Translation, Speech, Vision) мають маленькі місячні безкоштовні квоти.

**AWS-еквівалент.** Trainium 3, побудований на 3nm процесі, поточний фокус: 2.52 PFLOPs FP8 compute на чип, 144 GB HBM3e на 4.9 TB/s, масштабування до 144 чипів на Trn3 UltraServer (362 FP8 PFLOPs total) через пропрієтарну NeuronSwitch-v1 fabric на 2 TB/s на чип. Trainium 3 підтримує MXFP8 і MXFP4 data types для memory-to-compute балансу, потрібного у real-time multimodal reasoning. Trainium 2 досі широко задеплоєний. Inferentia 2 обробляє dedicated inference. Trainium-наратив схожий на TPU: значні переваги за вартістю для воркload-ів, що влазять у Neuron SDK programming model, з portability-тертям для стеків, побудованих навколо CUDA. GPU-інстанси на AWS це P5 (H100), P5en (H200) і P6 (B200). Savings Plans покривають compute-коміти, Bedrock Provisioned Throughput це per-model dedicated capacity. Bedrock підтримує batch inference з 50% знижкою проти on-demand, реальний важіль для high-throughput async-пайплайнів.

**Azure-еквівалент.** Maia 200 це друге покоління кастомного AI-акселератора Microsoft, у проді з кінця січня 2026, явно позиціонується як inference-чип, а не training-чип. Побудований на 3nm процесі TSMC, дає 5.07 PFLOPs FP8 compute і 10.14 PFLOPs FP4 (FP4-акцент це диференціальна ставка), з 216 GB HBM3e на 7 TB/s і 272 MB on-die SRAM, щоб тримати великі моделі нагодованими під час генерації. Networking масштабується по стандартному Ethernet до кластерів до 6144 акселераторів. Початковий деплой у US Central і US West 3. Maia 100 це чип першого покоління, досі в ужитку для non-frontier навантажень. Maia-наратив раніший за TPU чи Trainium, з меншою customer base і менш зрілим software-стеком. GPU на Azure це ND H100 v5, ND H200 v5 і ND B200 v6, широко доступні. Azure Reservations покривають compute-коміти. Provisioned Throughput Units (PTU) це dedicated capacity для Azure OpenAI та інших Foundry-моделей, з агресивними cached-input знижками (часто 10x off), що матеріально змінює економіку context-важкого сервінгу.

**Pick GCP when** ваше тренувальне навантаження влазить у JAX або TensorFlow і ви хочете TPU-економіки, або ваш inference-воркload на Gemini і ви хочете Ironwood-pricing-у з 1M контекстом резидентним у пам'яті. **Look elsewhere when** команда глибоко в PyTorch з кастомними CUDA-кернелами (будь-яка CUDA-first хмара б'є TPU тут), коли ціль для сервінгу це найсвіжіший Claude (Bedrock має Opus 4.7), або коли ціль потребує агресивного cached-input pricing на довгому контексті (Azure PTU-знижки на GPT-5 cached input найагресивніші на ринку). TPU lock-in реальний, але заслужений. Trainium 3 lock-in схожий, але зі меншою спільнотою. Maia 200 lock-in це маленька ставка сьогодні, де FP4-важка позиція або prescient, або niche optimization залежно від того, як наступні два роки inference-навантажень складуться.

---

## Безпека, networking і compliance

Цей розділ покриває production-readiness шар, який порівняння зазвичай пропускають: як тримати трафік поза публічним інтернетом, як контролювати ключі шифрування, що логується і що переживе US CLOUD Act повістку.

### Приватна конективність

GCP використовує VPC Service Controls для встановлення security-периметра навколо Gemini Enterprise Agent Platform, гарантуючи, що training-дані, prompt-и та inference-запити не покинуть визначений периметр. Private Service Connect дає внутрішні IP для доступу до API, тримаючи трафік на приватному бекбоні Google.

AWS використовує PrivateLink для Bedrock. Interface VPC endpoints для `bedrock-runtime` і `bedrock` живуть у приватному сабнеті, і трафік залишається всередині AWS-мережі. AgentCore Gateway egress-трафік роутиться через customer-VPC elastic network interfaces, що дозволяє агенту запитувати приватні RDS або on-premises системи без публічного експозиції.

Azure використовує Private Endpoints для вимкнення публічного доступу до Foundry-хабів, Storage-аккаунтів і Key Vaults. Agent outbound-трафік іде через VNet injection у делегованих сабнетах. Патерн однаковий на трьох хмарах: налаштувати private endpoints, вимкнути публічний доступ, проганяти весь трафік через VPC.

### Шифрування та key management

Усі три пропонують customer-managed keys. GCP Cloud KMS дає CMEK на гранулярності проєкту і ресурсу для Vertex AI. AWS Bedrock підтримує KMS Customer-Managed Keys, включно з AgentCore Gateways, де гранулярність доходить до per-tenant. Azure Key Vault і Azure Managed HSM покривають Foundry, плюс Customer Lockbox, що вимагає explicit auditable approval перед тим, як Microsoft support engineers отримають доступ до клієнтського середовища для troubleshooting. Customer Lockbox найжорсткіший з трьох за замовчуванням.

### Audit logging і data retention

GCP логує платформний доступ через Cloud Audit Logs. AWS логує кожну model invocation, parameter change і key decryption event через CloudTrail. Azure роутить audit-дані через Azure Monitor і Activity Logs, інтегровані в Foundry control plane для real-time observability.

Усі три гіперскейлери контрактно гарантують (через формальні data processing addendum-и), що клієнтські prompt-и і completion-и не зберігаються і не використовуються для тренування foundation-моделей. Контрактна мова схожа між вендорами. Дефолтна поведінка на кожній хмарі це no-retention; opt-in у retention чи sharing потребує явної конфігурації.

### Compliance-сертифікації

Orchestration-платформи (Bedrock, Foundry, Gemini Enterprise) успадковують основні фреймворки своєї хмари: FedRAMP High у GovCloud або спеціалізованих government-регіонах, HIPAA зі стандартними BAA, SOC 2 Type II, ISO 27001, PCI DSS scope. Для воркload-ів під EU AI Act усі три хмари публікують readiness-стейтменти; timeline імплементації регуляції тягнеться через 2026 і практичні відмінності між провайдерами поки що формуються.

### Data residency і суверенітет

Для європейських і регульованих клієнтів правила residency керують вибором хмари сильніше за порівняння здатностей. GCP крутить Gemini 3 у europe-west4 (Нідерланди), europe-west1 (Бельгія) і europe-west3 (Франкфурт) з гарантіями residency на data-at-rest і processing.

AWS запустив AWS European Sovereign Cloud (ESC) у General Availability 15 січня 2026, з першим operational-регіоном у Бранденбурзі, Німеччина. ESC фізично і логічно відокремлений від усіх інших AWS-регіонів, з незалежним білінгом, account management і identity-системами. Лише EU-резиденти можуть оперувати інфраструктурою, а managing directors корпоративної entity мають бути EU-громадянами. Launch-портфоліо включає 90+ сервісів, зокрема Bedrock, SageMaker та EC2. Невирішене юридичне питання: ESC лишається дочкою US-headquartered Amazon parent, що залишає residual exposure до extraterritorial warrants під US CLOUD Act. AWS не диверстував entity.

Azure покладається на EU Data Boundary commitments і "M365 Local" / Sovereign архітектуру, використовуючи partner operator-моделі та European Data Guardians для нагляду за доступом до даних. Microsoft executives публічно свідчили (нещодавно перед Французьким Сенатом), що абсолютний криптографічний імунітет від US data requests не може бути юридично гарантований через US-domicile Microsoft. Для найжорсткіших воркload-ів Azure підтримує Foundry Local, що крутить моделі повністю офлайн на customer-controlled hardware.

GCP sovereign-офер документований менше у травні 2026. Публічно видима позиція спирається на Customer-Managed Encryption Keys, partner-operated регіони (T-Systems у Німеччині, Thales у Франції) і zero-trust архітектуру радше, ніж на структурно незалежну EU-дочку у патерні AWS ESC. Покупець, що оцінює sovereign-вимоги, має трактувати це як відомий gap і питати Google напряму про поточні зобов'язання.

Усі три хмари переслідують alignment з BSI C5 (Німеччина) і ANSSI SecNumCloud (Франція). AWS ESC має найчистішу поточну публічну позицію щодо operational sovereignty у GA-вікні. Azure має найглибші pre-existing акредитації для UK government, defense і healthcare. Sovereign-шлях GCP найменш чіткий з трьох.
