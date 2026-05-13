# GCP AI-стек, з AWS і Azure на іншому боці столу

*Створено: 13 травня 2026*

Кожен, хто консультує з AI-архітектури у 2026, мусить мати робочу модель усіх трьох гіперскейлерських стеків, навіть якщо основний деплоймент стоїть на одному з них. Клієнти питають. RFP-и питають. Член правління, який щойно прочитав статтю в Bloomberg, теж питає. До того ж платформи знову перейменували себе у травні 2026. Vertex AI консолідували в Gemini Enterprise Agent Platform на Cloud Next. Azure AI Foundry прибрав слово "Azure" і став Microsoft Foundry. AWS зібрав Bedrock, SageMaker Unified Studio та AgentCore в один агентний наратив. Брендинг новий. Можливості переважно ні.

Це референс для команд із центром тяжіння на Google Cloud, яким треба пояснювати або захищати цей вибір, коли клієнти питають про AWS-чи-Azure-еквівалент. GCP-first, з еквівалентами поряд у тій самій секції. Не buying-гайд. Не бенчмарк. Не маркетинг жодного з трьох.

Скоуп. Тільки гіперскейлери: ні Snowflake, ні Databricks, ні Oracle, ні Alibaba. Без глибокого занурення в data-side AI-продукти типу BigQuery ML, Looker або Fabric. Дані актуальні станом на травень 2026, назви SKU встигнуть протухнути за рік.

---

## Шпаргалка по трьох хмарах

| Категорія | GCP | AWS | Azure |
|---|---|---|---|
| Уніфікована AI-платформа | Gemini Enterprise Agent Platform (раніше Vertex AI) | SageMaker Unified Studio + Bedrock | Microsoft Foundry (раніше Azure AI Foundry) |
| Хаб foundation-моделей | Vertex AI Model Garden, 200+ моделей | Amazon Bedrock | Foundry Models, 1900+ записів |
| Власні LLM | Gemini 3 Pro, 3 Flash, 3.1 Pro | Amazon Nova (Pro, Lite, Micro), Titan | GPT-5, 5.1, 5.2, 5.3-codex через Azure OpenAI |
| Моделі Anthropic | Claude Opus 4.6 (Vertex) | Claude Opus, Sonnet, Haiku 4.5 | Claude через Marketplace |
| Генерація зображень | Imagen 4, Virtual Try-On | Nova Canvas, Stability | GPT-image-1.5, FLUX.2 [pro], DALL-E |
| Генерація відео | Veo 3.1, Veo 3.1 Lite | Nova Reel | Sora через Foundry |
| Генерація музики | Lyria 3 (preview) | нема | Audio models (GA з грудня 2025) |
| Рантайм для агентів | Vertex AI Agent Engine | Bedrock AgentCore | Foundry Agent Service |
| Фреймворк для агентів | Agent Development Kit (ADK) | Strands, Bedrock Agents | Semantic Kernel, AutoGen |
| Low-code білдер | Agent Builder, Express Mode | Bedrock Agents | Copilot Studio |
| RAG і knowledge | Vertex AI Search + Vector Search 2.0 | Bedrock Knowledge Bases | Foundry IQ + Azure AI Search |
| IDE-асистент | Gemini Code Assist | Amazon Q Developer, Kiro | GitHub Copilot |
| CLI-асистент | Gemini CLI | Q Developer CLI | GitHub Copilot CLI |
| Vision API | Vision AI | Rekognition | Azure AI Vision |
| Document AI | Document AI | Textract | AI Document Intelligence |
| Speech | Speech-to-Text (Chirp), TTS | Transcribe, Polly | Azure AI Speech |
| Переклад | Cloud Translation | Translate | AI Translator |
| NLP | Natural Language API | Comprehend | AI Language |
| Власний кремній для тренування | TPU Trillium (v6), Ironwood (v7) | Trainium 2, Trainium 3 | Maia 100, Maia 200 |
| GPU-варіанти | H100, H200, B200 | P5, P5en, P6 | ND H100/H200 v5/v6 |
| MCP-підтримка | Cloud API Registry | AgentCore Tool Gateway | Foundry Agent Service |
| Free tier | $300 кредитів, 90 днів | 12 місяців free tier на багатьох сервісах | $200 кредитів, 30 днів |

Далі по документу проходимо кожен рядок глибше.

---

## Core AI/ML платформа

Gemini Enterprise Agent Platform це нова парасолькова назва для того, що раніше було Vertex AI. Перейменування важить менше, ніж консолідація під ним. Google підняв Model Garden, Vertex AI Search, Vector Search 2.0, AutoML, agent-рантайм, пайплайни, registry, feature store і evaluation під одну білінгову поверхню та одну IAM-модель. Бренд Vertex AI досі в URL-ах і назвах SDK. Якщо ви пишете IaC проти `aiplatform.googleapis.com`, нічого не зламалось. Ребрендинг переважно маркетинговий плюс консолідовані доки.

Що реально змінилось на Cloud Next 2026:

- Gemini 3 Pro замінив Gemini 1.5 Pro як дефолтний флагман. Контекст 1 мільйон токенів, нативний мультимодальний інпут по тексту, коду, відео, аудіо та PDF, плюс Live API для стрімінгу з затримкою менше секунди.
- Gemini 3 Flash і Gemini 3.1 Pro доукомплектовують сімейство. Flash для дешевого bulk-інференсу. 3.1 Pro для складнішого reasoning.
- Claude Opus 4.6 доступний на Vertex через Model Garden партнерство з Anthropic, білінг іде з Google Cloud spend.
- Model Garden перетнув позначку 200 first-party і партнерських моделей, включно з DeepSeek, GLM 5, MiniMax, Mistral, Llama 4 і довгим хвостом fine-tuned відкритих ваг.

Вибір між Gemini-варіантами на практиці простий. Gemini 3 Pro це дефолт для всього, що потребує reasoning над довгими інпутами, планування для агентів або мультимодального розуміння. Gemini 3 Flash це правильний вибір для bulk-класифікації, екстракції, саммарайзу і більшості рутинного інференсу. Різниця в ціні приблизно 12x, а Flash на нерасуннінгових задачах майже не поступається Pro. Gemini 3.1 Pro це невеликий апгрейд reasoning для задач, де 3 Pro б'ється головою об стелю: складна генерація коду, багатокрокова математика, глибокий RAG-синтез. Claude Opus 4.6 на Vertex це вибір для команд, яким потрібні сильні сторони Anthropic у instruction following та якості письма, але вони хочуть тримати весь spend на Google Cloud. Прагматична команда у проді тримає три з них: Flash для високооб'ємного шару, 3 Pro робочою конячкою, і 3.1 Pro або Claude для найскладніших запитів з роутером попереду.

Vertex AI Search це менеджед-RAG і enterprise-search продукт. Із коробки індексує Cloud Storage, BigQuery, SharePoint, Confluence, Salesforce, Jira та довгий список інших. Vector Search 2.0 це векторна БД під капотом, з індексами для filtered ANN, hybrid (dense + sparse) retrieval і grounding-колбеками, що зашиті прямо в Gemini API. Для більшості команд Vertex AI Search це правильна відповідь для RAG на GCP. Своє векторне сховище на AlloyDB або BigQuery має сенс лише коли потрібна ACID-семантика на тому ж сторі, що й транзакційні дані, або коли власний реренкер настільки кастомний, що переважає інтеграційний податок.

Арифметику вартості RAG варто перевіряти перед коммітом. Vertex AI Search білить за запит (близько $4 за 1000 запитів на Enterprise edition) плюс зберігання. Self-hosting на AlloyDB з pgvector коштує приблизно вдвічі менше за запит на масштабі, але людська ціна ingestion-пайплайнів, чанкінгу, вибору ембедингів, тюнінгу реренкера та метрик додається в квартал роботи невеликої команди. Для більшості команд до 100 000 запитів на день математика на боці Vertex AI Search. Вище цього об'єму питання реальне, і відповідь зазвичай гібридна: Vertex AI Search для довгого хвоста, кастомне для гарячого шляху.

Grounding це фіча, яка відрізняє Vertex від сирого API моделі. Gemini-виклики на Vertex можна грундити проти Google Search (даючи моделі живий доступ до вебу з цитатами), проти Vertex AI Search (кастомні корпуси), або проти обох одночасно. Web grounding це killer-feature для всього, що потребує актуальних фактів (зміни в регуляціях, ринкові дані, синтез новин). AWS і Azure обидва пропонують grounding на кастомних корпусах, але жоден не має first-party еквіваленту живого web grounding порівнянної якості. Bedrock додав web search action у кінці 2025, а Foundry має Bing grounding через окремий конектор, але індекс Google Search плюс глибина інтеграції ось ширший рів у цій категорії.

MLOps на Vertex зрілий тим способом, яким зрілі речі бувають нудними. Vertex AI Pipelines використовує Kubeflow Pipelines під капотом з Google-керованим control plane. Model Registry, Feature Store, Experiments і Model Monitoring, усе GA і прилично заінструментовано. Vertex AI Evaluation Service це новіший шматок, заточений на LLM- і агент-evaluation з метриками для groundedness, instruction following, safety і rubric-скорингу за кастомними критеріями. Evaluation Service це те, що робить Gemini Enterprise Agent Platform credible для агентів у проді. Без нього кожна команда будує власний eval-harness, що є відомим способом викочувати багнисті агенти.

AutoML досі тут для tabular, image, video і text задач, але центр тяжіння переїхав на Gemini fine-tuning. Для більшості use-кейсів, які у 2023 були б AutoML, рекомендація зараз така: LoRA або повний fine-tuning на Gemini 3 Flash, з деплоєм на менеджед-ендпоінт. AutoML Tables зокрема склали в BigQuery ML і це більше не Vertex-продукт.

**AWS-еквівалент.** Amazon Bedrock це model hub. SageMaker Unified Studio це workbench. Двоє зшиті як єдине з кінця 2025, але досі відчуваються як два продукти зі спільним sign-on. Bedrock хостить Claude Opus, Sonnet і Haiku 4.5 (Anthropic тут має основну хмару), Llama, Mistral, Cohere, Stability та Amazon Nova. Bedrock Knowledge Bases це RAG-офер, з OpenSearch Serverless як дефолтним векторним сторем і недавніми додатками для Aurora pgvector та MongoDB Atlas. SageMaker Pipelines і Model Registry грають ту ж роль, що їхні Vertex-аналоги, зі звичним AWS-патерном більше кнопок і більше клею. Структурна відмінність захована у стосунках з Anthropic. Якщо команда стандартизувалась на Claude, Bedrock має найсвіжіший і найповніший каталог Anthropic, тоді як Vertex відстає на одне покоління моделей.

**Azure-еквівалент.** Microsoft Foundry консолідував те, що було Azure AI Foundry, Azure OpenAI Service і частину Azure ML, в один портал. Foundry Models рекламує 1900+ записів, хоч заголовна цифра включає long-tail варіанти і fine-tunes. Стратегічна глибина в Azure OpenAI: GPT-5, GPT-5.1, GPT-5.2 і GPT-5.3-codex складають поточне покоління, ексклюзивно на Azure поза власним API OpenAI. Azure AI Search це RAG-продукт, зрілий і добре інтегрований з Microsoft 365-джерелами через Foundry IQ. Azure ML дає MLOps-поверхню з MLflow як дефолтним tracking-наративом і помітно менш opinionated пайплайн-фреймворком, ніж Vertex Pipelines або SageMaker Pipelines. Для команд, що вже на Entra ID і Microsoft 365, Foundry має найкоротший шлях від "у нас є SharePoint-корпус" до "у нас є чатбот, грундений на цьому корпусі".

**Pick GCP when** ваше reasoning-навантаження виграє від 1M контекстного вікна Gemini 3, потрібен нативний мультимодальний хендлінг без пайплайн-клею, або дані вже живуть в BigQuery. **Look elsewhere when** свіжість моделей Anthropic важить більше за все інше (AWS веде), або ваш identity-і-документ план уже на Microsoft 365 (Azure веде). Vector Search на GCP хороший, але не унікальний. Не вибирайте GCP лише заради vector search.

---

## Агенти та dev-інструменти

Агенти на GCP лягають шарами: framework, runtime, builder і dev-поверхня. Кожен шматок використовується окремо, але інтеграційний наратив фактично і є продукт.

Agent Development Kit (ADK) це framework. Open-source, Python і Go, побудований на патернах, схожих на LangGraph, але з власними примітивами Google для tool use, planning, multi-agent handoff і memory. ADK працює проти будь-якої Gemini- або Vertex-hosted моделі і деплоїться на будь-що, що крутить Python, але канонічна ціль для нього це Agent Engine.

Vertex AI Agent Engine це менеджед-runtime. Персистентні агентські сесії, conversation memory до 8 годин, sandboxed виконання коду, інтеграція з Vertex AI Search для grounding, IAM-aware виклики тулзів. Agent Engine коштує більше, ніж запуск ADK на Cloud Run, але поглинає смислову купу плумбінгу: session state, retries, tracing, інтеграцію з evals і довгий хвіст "ваш агент крутився 40 хвилин і помер, де нам шукати".

Реальний розрив у вартості менший, ніж здається спочатку. Типова сесія Agent Engine білить близько $0.0014 за хвилину плюс токени моделі. Той самий агент на Cloud Run з self-managed Redis для сесій вийде приблизно на 30 відсотків дешевше, якщо утилізація висока, але щойно треба додати tracing, retries, multi-region failover і eval-пайплайн, розрив закривається. Більшість команд, що це бенчмарчать, осідають на Agent Engine для проду і Cloud Run для офлайн-експериментів.

Agent Builder це low-code поверхня. Drag-and-drop підключення тулзів, специфікація цілей природною мовою і шлях до деплою, що закінчується на Agent Engine. Express Mode (free tier для Agent Builder) дає ~$300 кредитів і дозволяє non-engineering команді підняти функціональний внутрішній агент за пів дня. Це справді корисно для прототипування. Це не там, де житимуть більшість прод-агентів.

Gemini Code Assist це IDE-інтеграція. VS Code, JetBrains, Android Studio, Cloud Workstations, з чатом, code completion, трансформаціями і inline-поясненнями. Диференціатор проти GitHub Copilot це repository-scale контекст. Code Assist Enterprise індексує приватне репо клієнта і підіймає результати під час inline-генерації. Для команд з монорепо це важить більше за бенчмарк-скори.

Gemini CLI це термінальний компаньйон. Вийшов у GA наприкінці 2025, освіжений на Cloud Next 2026 з підтримкою екстеншенів, слеш-командами, multi-file edit і реєстром MCP-серверів. Це найближчий GCP-нативний аналог Claude Code або Codex CLI, і розрив закрився настільки, що вибір між ними тепер питання преференцій до моделі та екосистеми, а не парітету фіч.

MCP-підтримка по платформі. Google викотив Cloud API Registry, що експонує GCP-сервіси (BigQuery, Cloud Run, Spanner, Vertex AI і довгий хвіст) як MCP-сервери, тож агент, побудований на будь-якому фреймворку, може їх викликати. Vertex AI Agent Engine приймає MCP-сервери як тулзи нативно. Gemini CLI має реєстр MCP-серверів. Наратив внутрішньо когерентний і конкурентний з push-ем Anthropic, який власне стандарт і запустив.

**AWS-еквівалент.** AgentCore це runtime, GA з середини 2025. Персистентна memory, tool gateway з MCP-підтримкою, identity-інтеграція та observability. AgentCore це духовний кузен Agent Engine, архітектурні вибори схожі. Strands це open-source framework, менша спільнота за ADK, але добре задокументований. Bedrock Agents це low-code білдер, оріентований навколо Bedrock Knowledge Bases як grounding-шару. Amazon Q Developer це IDE-асистент, з сильним AWS-specific awareness (CDK, CloudFormation, патерни SDK-викликів). Kiro це новіша spec-driven IDE від AWS, вийшла наприкінці 2025, з сильнішою автономією за Q Developer на multi-file задачах. Q Developer CLI це термінальна поверхня.

**Azure-еквівалент.** Foundry Agent Service це runtime, запечений у Foundry-портал з тісною інтеграцією Entra ID і Microsoft Graph. Foundry IQ це context-шар, що індексує SharePoint, Blob Storage, Fabric і OneDrive без per-джерельних пайплайнів. Copilot Studio це low-code білдер, і він найвідшліфованіший з трьох low-code агент-продуктів вендорів, частково тому що Microsoft ітерує над лінією Bot Framework десять років. Semantic Kernel і AutoGen це open-source фреймворки, обидва Microsoft-led. GitHub Copilot це IDE-асистент з найширшою install-base серед усіх IDE AI-тулзів, і agent mode Copilot плюс CLI закривають більшу частину розриву з Claude Code на термінал-driven воркфлоу.

**Pick GCP when** команда вже пише агентів на Python і хоче чистого runtime-наративу (Agent Engine плюс ADK це найменш surprising стек з трьох). **Look elsewhere when** агенти мають діяти всередині Microsoft 365 (Foundry IQ плюс Copilot Studio виграє), або коли MCP-driven tool gateways і найзріліший прод-runtime для агентів важливіші за developer ergonomics (AgentCore веде). Платформи для агентів це місце, де lock-in відбувається найшвидше у 2026. Вибір цього року це рішення на три роки.

---

## Спеціалізовані AI-API

Більшість спеціалізованої AI-поверхні це commodity. API роблять приблизно те саме з вимірюваними, але не стратегічними відмінностями в якості. Стратегічні відмінності в моделях ціноутворення, регіональній доступності й ергономіці, а не в якості виходу.

Згрупуємо за призначенням: перцепція проти генерації.

### Перцепція

Vision AI (Cloud Vision API) робить label detection, OCR, face detection, landmark detection і safe-search. Vertex AI Vision це кузен-продукт для відеоаналітики на edge, зі стрім-процессингом і event-based тригерами для камер та подібного.

Document AI це OCR-plus-extraction. Pre-trained процессори для інвойсів, чеків, W-2, 1099, контрактів і генеричний form-parser. Custom Document Extractor дозволяє команді fine-tune-ити екстракцію на власних схемах документів з кількома сотнями labeled-прикладів. Білінг per-page, з істотними volume-discounts для high-throughput пайплайнів.

Speech-to-Text використовує Chirp 2 (вийшов наприкінці 2025), USM-архітектура, підтримка 125+ мов плюс on-device варіанти. Text-to-Speech має Studio-голоси для high-fidelity синтезу і Standard-голоси для дешевого bulk-синтезу. Обидва API battle-tested.

Cloud Translation має два тіри. Basic це straightforward NMT для high-volume low-cost перекладу. Advanced підтримує глосарії, batch translation і document translation зі збереженням форматування.

Natural Language API робить entity extraction, sentiment analysis, syntax parsing і content classification. Чесний take на 2026: цей продукт переважно obsolete для нових білдів. Виклик Gemini 3 Flash зі структурованою схемою виходу дає кращі результати за порівнянну ціну для entity- і sentiment-задач, з додатковим бонусом підтримки будь-якої кастомної схеми. Natural Language API залишається в каталозі для compliance і stability, а не тому що це правильний інструмент для greenfield-проєкту.

**AWS-еквівалент.** Rekognition для vision, Textract для OCR, Transcribe і Polly для speech, Translate для перекладу, Comprehend для NLP. Здатності мапляться чисто. Textract має невелику перевагу на екстракції форм для US-tax і фінансових документів. Polly Neural2-голоси сильні. Comprehend Medical існує як HIPAA-aligned варіант для клінічного NLP.

**Azure-еквівалент.** Azure AI Vision, AI Document Intelligence, AI Speech, AI Translator, AI Language. Document Intelligence має найзрілішу pre-built layout-модель і часто це правильний вибір для складних multi-column документів і таблиць. AI Speech має найкращу історію кастомного клонування голосу серед трьох (Custom Neural Voice).

### Генерація

Imagen 4 це поточний флагман генерації зображень на Vertex. Фотореалістичний вихід, сильне prompt adherence, рендеринг тексту всередині зображень, який реально працює. Virtual Try-On це нішевий Imagen-варіант для ecommerce, який міняє одяг на людських зображеннях на прод-якості.

Veo 3.1 генерує відео до 8 секунд у 1080p із синхронізованим аудіо, включно з діалогом і звуковим середовищем. Veo 3.1 Lite це здешевлений варіант для прототипування. Veo тримається найздатнішою video-моделлю серед трьох гіперскейлерів у травні 2026, трохи попереду Sora на coherent multi-character сценах і помітно попереду Nova Reel на audio-інтеграції.

Lyria 3 це модель генерації музики, у preview станом на Cloud Next 2026. Генерує інструментальні та вокальні треки у різних стилях, з контролями для темпу, тональності і вибору інструментів. У травні 2026 на AWS і Azure нема конкурентної first-party музичної моделі.

**AWS-еквівалент.** Nova Canvas для генерації зображень, Nova Reel для відео. Stability і Black Forest Labs доступні через Bedrock Marketplace. Nova Reel конкурентний на швидкості, але відстає від Veo на якості виходу й аудіо-інтеграції.

**Azure-еквівалент.** GPT-image-1.5 це поточна OpenAI-модель для зображень, доступна через Foundry. FLUX.2 [pro] від Black Forest Labs це найсильніший не-OpenAI варіант на Azure. Sora доступна через Azure OpenAI для відео. First-party музичної моделі нема.

Один регіональний нюанс варто підняти на generation-моделях. Imagen, Veo і Lyria переважно подаються з us-central1, us-east1 і europe-west4 на Vertex, з поступовою експансією. Клієнти в APAC платять egress-податок за медіа-генерацію, якщо не реплікують навантаження. Sora на Azure має схожий патерн розкочення, з Sweden Central і West US 3 як основними хостами. Nova Reel широко доступний на AWS, але відстає на якості виходу. Для медіа-важких навантажень у регіонах поза Північною Америкою і Західною Європою правильна відповідь часто гібридна: multi-region деплой із кешуванням, а не вибір іншої хмари.

---

## Інфраструктура та pricing

Рішення про кремній кидає найдовшу тінь у всьому стеці. Щойно команда тренує або сервить на певному сімействі акселераторів, переїзд звідти це проект, не клік. Цей розділ покриває, що пропонує кожна хмара і де реальні точки lock-in.

### Кремній на GCP

TPU Trillium (v6) це поточний прод-акселератор тензорів, доступний у зонах us-central1, us-east1, us-east5, europe-west4 та asia-northeast1. Trillium дає приблизно 4.7x peak compute проти TPU v5e на чип, з 32GB HBM і 1.6 TB/s memory bandwidth. Pricing на Trillium приблизно на 30-40 відсотків дешевший за FLOP, ніж еквівалентна NVIDIA H100 ємність на GCP для тренувальних навантажень, що влазять у TPU programming model.

TPU Ironwood (v7) вийшов на Cloud Next 2026, націлений на інференс, а не тренування. 4614 TFLOPs FP8 compute на чип, 192GB HBM, спроєктований сервити frontier-моделі на прод-масштабі. Ironwood це чип, на якому Google внутрішньо сервить Gemini 3, ємність котиться зовнішнім клієнтам через 2026.

TPU lock-in реальний. JAX і TensorFlow це first-class на TPU. PyTorch працює через PyTorch/XLA, але з нерівними краями, зокрема навколо кастомних CUDA-кернелів, для яких нема XLA-еквівалентів. Команди, які мають тренувати і сервити на TPU, мусять планувати JAX-centric стек, що є і реальною ціною, і реальним бенефітом. JAX щиро швидший для ітерації за еквівалентний PyTorch-код на багатьох воркload-ах, але talent pool менший.

GPU на GCP це альтернатива. H100, H200 і B200 усі доступні, з machine type-ами A3 Ultra (H100), A3 Mega (H100) і A4 (B200). A4-інстанси стали GA на початку 2026 і це прод-ціль для команд, яким треба B200 поза CoreWeave чи Crusoe.

TPU networking важить так само, як TPU compute, для будь-кого, хто тренує на масштабі. Trillium pod вміщує до 256 чипів, з'єднаних 3D torus interconnect на 4.8 Tbps на чип. Multi-host JAX-тренування поверх pod-а справді прозоре: той самий код крутиться на 8 чипах і на 256. Вище pod-масштабу Google зв'язує pod-и через data center network з помітним падінням bandwidth. Імплікація така: single-pod тренування (приблизно до 70B параметрів з типовими memory footprint-ами) це точка, де економіка TPU найпереконливіша. Вище цього калькуляція зміщається до GPU-кластерів із NVLink та InfiniBand, або до новіших multi-pod TPU-конфігурацій, доступних за резервацією.

### Pricing-моделі

Pay-as-you-go це дефолт. Committed Use Discounts (CUDs) дають 20-57 відсотків економії за 1- або 3-річні коміти. Dynamic Workload Scheduler це новіший flex-commitment-офер, з двома смаками: Flex Start для нетермінових тренувальних задач, які можуть зачекати на ємність хвилини або години, і Calendar Mode для резервацій на конкретні майбутні дати. Vertex AI Provisioned Throughput це dedicated-ємність для інференсу Gemini, білінг за Generative AI Scale Unit (GSU), а не за токен, корисний для прогнозованого high-volume сервінгу.

Free tier. Стандартні $300 кредитів Google Cloud на 90 днів покривають усі AI-сервіси. Express Mode для Agent Builder це менший AI-specific credit pool, націлений на побудову першого агента за нуль. Vertex AI як такий не має per-product free tier-у поверх $300, хоча кілька спеціалізованих API (Translation, Speech, Vision) мають маленькі місячні безкоштовні квоти.

**AWS-еквівалент.** Trainium 2 у GA. Trainium 3 анонсували на re:Invent 2025 і він котиться через 2026. Inferentia 2 обробляє інференс. Trainium-наратив схожий на TPU: значні переваги за вартістю для воркload-ів, що влазять у Neuron SDK programming model, з portability-тертям для стеків, побудованих навколо CUDA. GPU-інстанси на AWS це P5 (H100), P5en (H200) і P6 (B200), ємність широко в us-east-1 і us-west-2 першими. Savings Plans покривають compute-коміти, Bedrock Provisioned Throughput це per-model dedicated capacity. Free tier включає 12 місяців часу на маленьких інстансах по багатьох сервісах.

**Azure-еквівалент.** Maia 100 це перше покоління кастомного AI-акселератора Microsoft. Maia 200 анонсували на Ignite 2025, котиться через 2026. Maia-наратив раніший за TPU чи Trainium, з меншою customer base і менш зрілим software-стеком. GPU на Azure це ND H100 v5, ND H200 v5 і ND B200 v6, широко доступні. Azure Reservations покривають compute-коміти. Provisioned Throughput Units (PTU) це dedicated capacity для Azure OpenAI та інших Foundry-моделей. Free tier дає $200 кредитів на 30 днів.

**Pick GCP when** ваше тренувальне навантаження влазить у JAX або TensorFlow і ви хочете TPU-економіки, або ваш inference-воркload на Gemini і ви хочете Ironwood-pricing-у. **Look elsewhere when** команда глибоко в PyTorch з кастомними CUDA-кернелами (будь-яка CUDA-first хмара б'є TPU тут), або коли ціль для сервінгу це Claude (Bedrock має найсвіжіше Anthropic-покоління і найкращий Anthropic-specific pricing). TPU lock-in реальний, але заслужений. Trainium lock-in схожий, але зі меншою спільнотою. Maia lock-in поки не питання, яке варто ставити, бо обсяг малий і software-стек ще дозріває.

### Data residency і compliance

Для європейських і регульованих клієнтів правила residency керують вибором хмари сильніше за порівняння здатностей. GCP крутить Gemini 3 у europe-west4 (Нідерланди), europe-west1 (Бельгія) і europe-west3 (Франкфурт) з гарантіями residency на data-at-rest і processing. Sovereign Cloud-офер через партнерів (T-Systems у Німеччині, Thales у Франції) дає жорсткіші контролі, включно з operational sovereignty. AWS пропонує схоже через EU Sovereign Cloud (стартує наприкінці 2025 у Бранденбурзі) і наявні Frankfurt та Stockholm регіони для Bedrock. Azure Sovereign Cloud-наратив тримається найзрілішим з трьох для європейських клієнтів, з EU Data Boundary commitments, що покривають Microsoft Foundry за замовчуванням. Для UK government, defense або healthcare воркload-ів Azure досі тримає найглибші акредитації. Для німецьких Schrems II турбот sovereign-офери GCP і AWS зараз конкурентні, але новіші.
