# AI in Software Development

## Foreword

This series is meant to inform and discuss the use of AI in software development. The field is very dynamic, and many developers find it hard to follow news and technologies.

Marketing often raises expectations about AI in programming, which leads to two extremes: either developers consider AI unusable, or they expect AI to do everything for them. The truth is somewhere in the middle.

The goal of this series is to give a realistic picture of the capabilities and ways to use modern AI in software development, based on practical experience rather than marketing promises.

## Status as of early spring 2026

_This article was updated for spring 2026. Updates are marked with italic notes; outdated claims are shown with strikethrough._

1. As of spring 2026, just like in August 2025, AI in development is the main tool for software development after human judgment, more important, for example, than the IDE. Some teams still have not fully integrated AI into their process because of inertia and caution, but developers who actively use modern AI tools show very large productivity gains, and for most of them the central role of AI is no longer a bold assumption but a fact. The debate is now mostly not about whether AI became a central tool, but about how to fit it correctly into a team's own process, stack, and engineering practices. This is very different from 2022-2023, when AI was mostly a toy, and from 2024, when it was already useful but not yet the key tool for developers.
2. The key factor behind the current AI boom is the transformer architecture and powerful LLMs (Large Language Models), which can imitate human language and to some extent reasoning. Unlike earlier neural network architectures, transformers scale well, which made it possible to train modern models on almost all text data available on the internet.

   _Update as of spring 2026:_ direct model scaling has slowed down, but progress did not stop. The situation now looks more and more like CPU evolution: clock speed stopped being the main source of progress, but total system power kept growing through architecture and engineering improvements. For LLMs this means better quality, higher stability, better tool use, and a lower practical cost of mistakes. At the same time, the core limitation of the architecture is still here: models still do not have native STM and LTM, and attempts to compensate for this only by increasing context did not lead to a truly major breakthrough.

3. The first way to use LLMs was a simple chat. Despite the apparent simplicity, in practice things are not so straightforward. Because an LLM is not intelligence in the broad sense but a statistical next-token predictor, it is critical to ask high-quality prompts. The request is called a prompt, and the methods of crafting it are prompt engineering.

   _Update as of spring 2026:_ the importance of clever prompt engineering has clearly gone down. Models became better, and users now more often work not directly with a bare model but through agents with strong system prompts, action templates, and built-in workflows. What matters much more now is the ability to control context size and context quality.

   _This was true in August 2025, but is outdated now:_
   ~~Writing prompts by hand is a difficult task that requires experience and practice.~~

4. Even though new models are no longer breakthroughs compared to previous ones, their capabilities keep growing. The achieved level of "smartness" enabled wide development and deployment of tools based on LLMs. These tools give models "eyes and hands," allowing them to do real work and save time. They are called LLM agents.

   _Update as of spring 2026:_ the biggest practical jump probably happened in agents. Both the number and the quality of tools grew a lot. Agent progress now plays a role comparable to model progress, and in some scenarios it matters even more, because the agent is what turns model capability into real productivity.

5. LLM agents perform different tasks using models. Usually they include a set of prepared prompts that tune the model for specific tasks and define the protocol of interaction between the model and the agent. In addition, an agent has a protocol interpreter that performs actions on the machine where it runs: browsing the file system, reading/writing files, running commands, searching the web, and so on.

   _Update as of spring 2026:_ multi-agent work deserves special attention. A scenario where one agent can launch subordinate agents, sometimes even on other models, and coordinate their work is no longer exotic but a practical tool. This is one of the most effective ways to fight context overflow and loss of focus on large tasks.

6. It is hard to build one agent that can interact with all tools. Specialized extensions exist. In November 2024, the MCP protocol was proposed; it lets a tool implement an interface for an agent. This allows an agent with an MCP client to interact with a tool that has an MCP server without prior direct integration.

   _Update as of spring 2026:_ MCP servers appeared for almost everything they could be practically useful for. Their quality, support, and security are still very uneven, but the approach itself already became ordinary. Besides MCP, the way agents are extended with "skills" also became more or less standardized. These are ready packages of behavior, instructions, and tool integrations.

7. Deep AI adoption in the development process is a complex path that needs legal and technical decisions. At the same time, you can start using AI in a basic form and get most of the benefits quickly and simply. Most software development companies have an AI usage policy that you must read. Its main goal is to prevent data leaks through AI services, which is possible when using non-corporate or personal accounts. For your personal open-source projects, you can use any AI tool from a trusted vendor with any pricing plan, including free.

   _Update as of spring 2026:_ deep AI adoption in development is still hard, but the need for it keeps growing because the possible gains keep growing too. As agents become more autonomous, safety becomes much more serious: technical, legal, and organizational safety.

8. _New as of spring 2026:_ SDD (Specification Driven Development) is looking more and more like the practical default standard for enterprise AI use, because it helps keep control over code and quality, and often improves them, while still giving a clear speed-up in development. More on this is in [ai_8.md](ai_8.md).

Details for each point are covered in the later posts of the series. Please respond if you want to help expand this series of publications. I will appreciate any wishes, notes, and suggestions. What is described here is only my personal opinion, though backed by other sources, so discussion in search of truth is welcome. The only request is to not post bare links to AI news without your own conclusion, because there are far too many of them and their reliability is very low.

[List of series updates as of early spring 2026](upd_ai_2.md).


---

# 1. AI - tool #2 right after the developer's mind

_This article was updated for spring 2026. Updates are marked with italic notes; outdated claims are shown with strikethrough._

Modern AI in development is a jump beyond a “better IDE”: it adds speed, reduces routine to near zero, and raises quality by freeing human time that can be used for well‑known quality practices that never had time. Used correctly, AI does not replace thinking; it amplifies it, turning ideas into results much faster and more reliably.

## Why now

- **Accessibility**: getting started is very simple. You can proceed step by step, adding new AI use cases as needed and as you are ready. Start with tools you already use and install AI extensions for them.
- **Context**: models can already hold enough context to complete a step of a typical decomposed task in a typical project.
- **Quality**: under the supervision of a person who can manage task decomposition, verify and adjust the plan, AI can perform tasks with an acceptable level of quality.
- **Speed**:

  _As of spring 2026:_ for almost any development task, it is already more profitable to involve AI than not to involve it. Sometimes the gain is small, sometimes it is very large, but the default strategy has changed: if a task can be given to AI safely and in a meaningful way, it is usually faster than doing it fully by hand.

  _This was true in August 2025, but is outdated now:_
  ~~some tasks, especially simple and typical ones, AI can do much faster than a person. AI can also quickly complete complex but very clearly described and bounded tasks that a person might spend a lot of time on. Even if the share of such tasks is small, AI reduces their duration by an order of magnitude, which noticeably increases overall development speed.~~
- **Tools**: today AI is not just a bare model that can only chat, but a powerful tool that can do many tasks with minimal human involvement. This almost solves last years’ problem where explaining context to AI and applying the result took longer than doing the task yourself.

## Where and how can you start using AI in development today?

This is a surface‑level list of short examples you can start using today. Do not treat it as exhaustive, but it is a good start. How to do it is also described very briefly, but this will already be a good beginning.

### 1) Tests: unit and integration

- **Describe the API**: describe the API of the module you test right away. Add the file or folder with it into context.
- **Work step by step**: do not give the agent a too general request like “write tests for all methods.” Describe each test separately: name, preconditions, steps, expectations, and what to mock. This is a bit longer, but you get exactly the tests you need and you also re‑check the validity of the requirement.
- **Grow gradually**: “happy path” → negative scenarios → edge cases. The more tests the module has, the better AI generates the next, more complex tests.

Result: good test coverage for little cost that can grow gradually; the cost of each new test keeps decreasing.

### 2) XML comments, logs, traces: autocomplete “out of the box”

- **Automate built‑in documentation**: if the method body is clean and without “spaghetti,” AI confidently completes XML comments, parameter and return descriptions.
- **Logging and tracing**: provide a template for what and how to log, and the agent will do it quickly. For new code, rely on autocomplete which adds the needed log by context in seconds.

Result: documentation and diagnostics do not slow you down, do not overload the developer, but sharply improve observability and clarity of the program’s behavior.

### 3) Implement business logic by contract

- **Business logic**: copy the required fragment of the requirements into context or refer to the ticket number in Jira (if MCP is available).
- **Contract**: define the contract and create an empty method with the signature that needs to be implemented.
- **Tests**: write tests for the contract according to the requirements; if the ticket has acceptance criteria, AI will cope with minimal interventions.
- **Implementation**: ask AI to provide the implementation - it will write code that meets the requirements, the contract, and the tests.
- **Iterate**: adjust the code as needed, by hand or with the agent, until it is correct and meets the desired quality.

Result: fast and high‑quality implementation of business logic.

### 4) Work by analogy and spread solutions

- **Create an example**: create the example manually or with assistance that you need to spread.
- **Add to context**: add the example to context and describe what to pay attention to in it.
- **Spread**: specify where to apply the example, and AI will do it for you.

Result: scaling best practices without manual routine and standardization of style and practices.

### 5) Test HTTP requests and SQL scripts

- **HTTP**: by looking at the code, especially if it has well‑documented public contracts, AI can create the needed test request for the VS Code REST Client according to your wishes.
- **SQL**: using database extensions (for example, PostgreSQL for VS Code), AI can create a script to create test data or collect data from the DB and analyze it. For its work, AI can rely on the DB schema, example data, and how your code uses it, so even a basic task description gives a good result. The main rule is to never grant write access to the production DB.
- **HTTP + SQL**: together this allows AI to perform the needed check from your text description. If the check scenario is successful, you can save it as an integration test.

Result: faster testing and problem diagnostics.

### 6) Almost any task with a clear specification

- **Detailed description**: if you have a clear and detailed enough task description, AI can already do not only separate typical subtasks, but almost any kind of development work.
- **Decomposition**: the key condition is proper decomposition. One task atom must be small enough to fully fit into the model's working context together with the needed artifacts.
- **Context control**: the better the task boundary, the fewer assumptions the model makes, and the higher the speed and reliability of its work.
- **Natural consequence**: this leads directly to the SDD approach, where specification, decomposition, and control of task atoms become the central part of the process. More about this is explained in [ai_8.md](ai_8.md).

Result: AI can be used effectively not only for separate "convenient" scenarios, but for almost any task if it is described well enough and decomposed correctly.

## The importance of AI in development

Today, starting to use AI in development is more important than the switch from a plain text editor to an IDE was a decade ago.

The switch “Notepad → IDE” gave autocompletion, refactoring, and debugging. Adding an AI agent gives:

- **Artifact creation**: tests, mocks, built‑in documentation, telemetry, method implementations, mappings, validations - not just “helps write,” but “writes itself” from your task description.
- **Structured transformations**: complex refactoring by example or by a description across a large volume of code.
- **Code behavior analysis**: answers to how the code works, what problems are possible, what solutions exist; explanations of the cause of an already found problem.

This is a larger step than just a “better editor.” It is adding a second performer to your team. Right now, using AI in development is more important than using an IDE has been in the last 15 years.

### Not only speed: quality and reliability

- **More time for design**: AI removes routine and frees hours for domain modeling, contract alignment, and simplifying the architecture.
- **Large refactorings**: multi‑stage changes can be made quickly, and better test coverage (thanks to their low cost) increases refactoring safety.
- **Less “magic”**: where there used to be “magical” frameworks to reduce boilerplate, now you can write explicit, transparent code - the agent will generate the boilerplate. Clarity > magic.

### Synergy with DDD

- **Unambiguous context**: bounded context, ubiquitous language, aggregates, invariants - this is exactly what LLMs need. Clear names and boundaries minimize ambiguity.
- **Turning design into code**: time spent on design pays back immediately because AI can work faster and more autonomously when there is a clear technical design.
- **Evolution without breaking the language**: when the business changes, the agent allows quick updates while keeping the unity of the ubiquitous language in reality and in code.

Conclusion: DDD reduces complexity and gives AI a high‑quality, bounded context; AI reduces the cost of implementing DDD and prevents Time‑to‑Market growth.

### Synergy with TDD

- **Cheaper tests**: the time spent on writing tests is the main pain of TDD. AI radically lowers the cost of creating and maintaining tests.
- **Tests as a specification for LLM**: every test is a precise expectation. The agent “understands” what is wanted and has an explicit success criterion.

Together: TDD makes requirements clearer for AI, and AI removes the cost barrier for tests.

### Anti‑patterns to avoid

- Overloaded “do everything at once” prompts. Better: small, sequential tasks with clear artifacts at each step.
- Unclear names and no contracts. Introduce a glossary and fix interfaces before code generation.
- _As of spring 2026:_ for complex and non-typical tasks, you should first prepare a detailed specification and decomposition. Trying to solve such a task directly through an AI agent without this preparation usually only wastes time and tokens.

  _This was true in August 2025, but is outdated now:_
  ~~Using LLMs for complex, non-typical tasks with fuzzy requirements - the result will be unclear with wasted time and tokens.~~

## In closing

The most important synergy is between the human brain and AI. LLMs generate many simple, trivial things instantly and reliably solve complex but standard and deterministic tasks. Humans remain unmatched where there is uncertainty: they form hypotheses, choose trade‑offs, see meaning, accumulate experience, and can abstract. Together this lets you move faster and more reliably: the machine does what can be described unambiguously, and the human directs, defines, and improves the description. AI is tool #2, and because of that it turns a strong developer’s mind into a team able to build a quality product significantly faster.

## Recommended links

- Vaswani et al., "Attention Is All You Need" (2017): [arxiv.org](https://arxiv.org/abs/1706.03762)
- Kaplan et al., "Scaling Laws for Neural Language Models" (2020): [arxiv.org](https://arxiv.org/abs/2001.08361)
- Wei et al., "Chain‑of‑Thought Prompting Elicits Reasoning in Large Language Models" (2022): [arxiv.org](https://arxiv.org/abs/2201.11903)
- Wang et al., "Self‑Consistency Improves Chain of Thought Reasoning in Language Models" (2022): [arxiv.org](https://arxiv.org/abs/2203.11171)
- Schick et al., "Toolformer: Language Models Can Teach Themselves to Use Tools" (2023): [arxiv.org](https://arxiv.org/abs/2302.04761)
- GitHub Blog - "Research: quantifying GitHub Copilot's impact on developer productivity" (2022): [github.blog](https://github.blog/2022-09-07-research-quantifying-github-copilot-s-impact-on-developer-productivity/)
- Microsoft Learn - "Develop with GitHub Copilot" (learning path): [learn.microsoft.com](https://learn.microsoft.com/en-us/training/paths/ai-developer-github-copilot/)
- Vaughn Vernon - "Implementing Domain‑Driven Design" (Addison‑Wesley): [informit.com](https://www.informit.com/store/implementing-domain-driven-design-9780321834577)
- Vaughn Vernon - "Domain‑Driven Design Distilled" (Addison‑Wesley): [informit.com](https://www.informit.com/store/domain-driven-design-distilled-9780134434421)
- Kent Beck - "Test‑Driven Development: By Example": [informit.com](https://www.informit.com/store/test-driven-development-by-example-9780321146533)
- Martin Fowler - "Refactoring: Improving the Design of Existing Code": [martinfowler.com](https://martinfowler.com/books/refactoring.html)


---

# 2. Basic ideas of modern language models

_This article was updated for spring 2026. Updates are marked with italic notes._

## Historical context and basic concepts

Neural networks were created as a way to teach a machine to approximate complex dependencies in data. The simplest element - the perceptron - takes input features, weights them, adds a bias, and checks whether a threshold is crossed. A single perceptron gives only linear separation, but a multilayer perceptron (MLP) with nonlinearities can approximate almost any function.

Weights are adjusted automatically with backpropagation: the network compares its prediction with the correct value and step by step adjusts parameters to reduce the loss.

Language needs one more step - turning text into numbers. Tokenization does this: it splits text into tokens, most often subwords. This makes the system robust to rare or new words. Then tokens are projected into high‑dimensional vectors (embeddings). The semantic intuition is simple: words close in meaning have close vectors; the geometry encodes similarity and associations.

For a long time, recurrent networks (RNN) and their variants LSTM/GRU dominated. They read the sequence token by token, keeping a hidden state as “memory.” In practice this often broke on long‑range dependencies (vanishing or exploding gradients), and more importantly, computation was sequential and scaled poorly on modern hardware. This is where transformers offered a new path.

## Fundamental principles of transformers

First of all, a transformer is an autocompletion model: given a sequence of tokens, it estimates the probability distribution of the next token and chooses the most likely one. Then this predicted token is appended to the sequence, and the process repeats step by step until we get a complete result. How does it guess the next token? The key is the attention mechanism, which lets the model focus on relevant parts of the context.

Before each new step, every word in the sentence looks at all the others and decides whom to consider and by how much. The word formulates its “question”: what do I need right now? This is the query (Q). Each other word has a “label” that says what it is about - the key (K). And it has “content” it can contribute - the value (V). Attention computes “how much” weights between the current word’s question and the labels of the others, and then mixes their content according to these weights. The result is a new representation of the word that has taken relevant context into account.

A simple example without formulas. “Ivan bought oranges. They were ripe.” For the word “They,” the most useful is to look at “oranges,” so the attention weight to “oranges” will be the largest; to “Ivan” - smaller; to function words - smaller still. The new representation of the token “They” becomes a weighted mixture of the contents of other words, where “content” is what the word can “pass” (the value, V).

From here follow the components and dimensions (short and with an example). We have the matrix of token representations X of size n×d_model (n is sequence length, d_model is the vector size of a token representation, typically 512–4096 numbers, the “width” of a layer).

For simplicity, take one attention “head” with dimension d_head (the “width” of one head’s subspace: how many numbers a Q/K/V vector has for this head, typically 64–128). An attention head is a separate “channel” with its own weight matrices W_Q, W_K, W_V (each of size d_model×d_head for this head) - these are the parameters the model learns during training. Such a head “catches” its own type of dependencies.

This can be written for one attention head as:

$$
A = \mathrm{softmax}\!\left(\frac{QK^{\top}}{\sqrt{d_{head}}}\right),\quad \text{out} = A\,V.
$$

The product QK^T gives the “similarities” between queries and keys: how much my “what do I need?” matches your “what are you about?”. Division by √d_head keeps numbers in a reasonable range so softmax does not wipe out small differences. Softmax over rows turns similarities into weights from 0 to 1 that sum to 1 - that is, “how much trust” we allocate to each word. Multiplying A by V takes a weighted mixture of their contents - we get the same “context taken into account.”

In multi‑head attention, several heads work in parallel in different subspaces, their outputs are concatenated and passed through an output linear projection W_O (of size H·d_head×d_model), which returns the dimension back to d_model. Then a residual connection and layer normalization are added, after which the result goes through a small MLP and again gets a residual connection with normalization. At the end, the last layer’s representation is projected to the vocabulary size and softmax gives the probability distribution of the next token.

The key advantage of the transformer is full parallelization over tokens during training: we do not need to process the sequence step by step, we can compare everything with everything at once. This improves modeling of long‑range dependencies and opens the door to efficient scaling. Empirical scaling laws show that quality grows systematically as we increase parameters, data, and compute in the right proportions. In practice this is done through different forms of parallelism (data/model/pipeline/tensor), mixed precision (FP16/BF16 instead of 32‑bit for speed), optimizer sharding, and checkpointing. At the same time, careful data preparation and corpus balance are often no less important than extra FLOPs (floating‑point operations - a measure of compute power).

The cost of self-attention grows quadratically with context length: twice the window is roughly four times the compute and memory. Therefore, extending context needs engineering: relative positional encodings, caching/buffering, and sparse or local attention. This can be improved only partly with approximate algorithms, for example sliding-window or local attention, sparse attention in the style of Longformer or BigBird, and different variants of linear attention such as Performer. FlashAttention is also worth mentioning separately: it is not an approximate algorithm, but a very effective IO-aware way to compute exact attention with lower overhead on memory and memory access. But even that does not remove the core problem completely. All of these are only partial compromises, not a full solution. In applied systems this is complemented by external knowledge search (RAG - Retrieval-Augmented Generation) and caching so we do not push everything into the model at once.

_Update as of spring 2026:_ this problem is still very serious. Models are still classic transformers without native STM and LTM, and they are still trying to compensate for that with context length, which is still hard to increase in a major way.

## Limitations and future directions

Despite breakthrough abilities with language, LLMs have systemic limits. They are prone to "hallucinations" - confidently generating falsehoods in zones of uncertainty; they are sensitive to prompt wording; they lack built-in fact-checking. The most noticeable limitation is a lack of true long-term memory: anything beyond the context window is lost, so the model does not "remember" the user or a long process in a strict sense.

_Update as of spring 2026:_ at the model level, this remains almost unchanged: the problems caused by the lack of STM and LTM are still not solved, and bigger context alone did not become enough compensation. Cost and energy, bias, safety and privacy, and limited planning and reasoning without tools also matter.

Promising directions include integration of external memory and knowledge indexes (RAG, personal stores), new attention schemes with lower complexity, compression and recurrent memory over long ranges, more reliable validation of statements (source checking, self‑assessment of confidence). Critically important is post‑deploy learning: safe loops for continual learning, online updates of knowledge, and guided RLHF in production. Integration with tools and agents is also growing actively, allowing the model to act in the world and not only generate text.

## Summary

Transformer LLMs are a technological breakthrough on the level of the first computers or the Internet. They learned to see long‑range dependencies, scale across clusters, and work with language in ways that seemed impossible not long ago. But this is likely not the last step on the path to real AGI (artificial general intelligence). To move from “large autocompletion” to broad reasoning ability, we need true long‑term memory, safe post‑deploy learning, and new inductive biases for reliable planning and reasoning. The breakthrough has already happened; now it is time for the next ones.

## Sources and further reading

- [Attention Is All You Need](https://arxiv.org/abs/1706.03762) - A. Vaswani et al., 2017.
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/) - J. Alammar, 2018.
- [Language Models are Few-Shot Learners](https://arxiv.org/abs/2005.14165) - T. Brown et al., 2020.
- [Scaling Laws for Neural Language Models](https://arxiv.org/abs/2001.08361) - J. Kaplan et al., 2020.
- [Training Compute-Optimal Large Language Models](https://arxiv.org/abs/2203.15556) - J. Hoffmann et al., 2022.
- [SentencePiece: A simple and language independent subword tokenizer and detokenizer for Neural Text Processing](https://arxiv.org/abs/1808.06226) - T. Kudo, J. Richardson, 2018.
- [Neural Machine Translation of Rare Words with Subword Units](https://arxiv.org/abs/1508.07909) - R. Sennrich, B. Haddow, A. Birch, 2016.
- [Efficient Transformers: A Survey](https://arxiv.org/abs/2009.06732) - Y. Tay, M. Dehghani, D. Bahri, 2020.
- [Retrieval-Augmented Generation for Knowledge-Intensive NLP](https://arxiv.org/abs/2005.11401) - P. Lewis et al., 2020.
- [Training language models to follow instructions with human feedback](https://arxiv.org/abs/2203.02155) - L. Ouyang et al., 2022.
- [Constitutional AI: Harmlessness from AI Feedback](https://arxiv.org/abs/2212.08073) - Y. Bai et al., 2022.
- [3Blue1Brown: Neural network](https://www.youtube.com/watch?v=aircAruvnKk&list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi) - 3Blue1Brown video series about neural networks


---

# 3. Basic interaction with an LLM through chat

_This article was updated for spring 2026. Updates are marked with italic notes._

_Important: as models and agents improved, a large part of the prompting techniques described below is now either built into modern agents or less critical than before. Today the main skills are controlling context size and context quality, decomposing tasks correctly, and working with agent tools. Still, understanding the basic principles of prompting remains useful for non-trivial and domain-specific cases._

An LLM is not a thinking subject but a powerful statistical next‑token predictor. Because of this, answer quality depends much more on the wording and context of the input than in usual human communication.

The basic interaction looks like this:

- the user formulates a prompt (instructions, questions, examples) and sends it with the dialog context;
- the model sequentially predicts the next tokens and forms a reply;
- chat history accumulates context, so relevance and clarity of input are critical.

Input to an LLM is called a “prompt,” and the techniques to build effective prompts are “prompt engineering.” These appeared in response to the high sensitivity of models to the wording and structure of the request.

Here is a list of practically effective prompting methods today that I regularly use:

1. Give the AI a role. Copilot (or similar tool) already does this, setting a developer role, but you can clarify it with language, framework, and extra technologies. It is very important to state that it must create production‑ready code (with error handling, logging, documentation).

   Examples of roles:
   - “You are a senior .NET developer with 10+ years of experience. Write production‑ready code with error handling and logging.”
   - “You are a QA engineer. Create a test plan for this API, including edge cases.”
   - “You are a security expert. Analyze this code for vulnerabilities and propose improvements.”

   _Update as of spring 2026:_ this is often better done through custom agents rather than directly in every chat. This matters especially when you try to override a role: if the agent's system prompt already fixes one role and you manually set another, the conflict can make the result worse instead of better.

2. Explicitly ask to create a plan before doing the task: "Create a plan, break it down into sub-points if necessary; for each point, specify the cause and effect, verify the cause-and-effect relationships, and make sure the plan can accomplish the task without causing side effects."

   _Update as of spring 2026:_ most agents already have a built-in planning mode, and it is usually better to use that instead of manually repeating the same logic in every prompt.

3. For complex tasks first ask only for the plan and tell it to stop. If the plan is poor, stop and describe the problems. For large tasks ask to save the global plan into a separate .md file and mark completed steps - this helps refresh context without losing progress and make intermediate commits.

   _Update as of spring 2026:_ same as in point 2, modern agents support this through a built-in plan mode, so manual prompting of this behavior is needed less often.

4. If the task meets a large codebase, ask to explore the seam line, decide whether refactoring is needed, and if yes, add the needed steps to the start of the plan.

   _Update as of spring 2026:_ modern agents often explore code context automatically while planning, so this technique became less critical for everyday work, though it is still useful for hard refactorings.

5. When designing solutions, it can help to generate alternative plan branches, compare them, criticize them, choose the best, discard dead ends, and go back when needed.

6. To check solutions, ask the AI for a counterexample or a scenario where the proposed approach might fail. For example: “Under what conditions will this algorithm be inefficient?” or “What edge cases can cause problems?” This helps find weak spots at the planning stage.

7. Evaluate solutions by criteria: maintainability, scalability, reusability, security, performance, and so on. Not all at once - pick 3–4 most important for the current task. You can also set priorities.

8. To reduce hallucinations, it sometimes helps to ask for a confidence level from 1 to 10 when creating a plan. This reduces made‑up facts and gives points for extra checking.

   _Update as of spring 2026:_ modern models hallucinate much less often when the context is good, and a numeric "confidence level" can be an unreliable indicator of actual answer quality. The technique can still help sometimes, but you should not rely on it.

9. The best prompt structure:

   - Context (what it is about)
   - Task (what to do)
   - Notes (strategy, what to focus on, what is desired/undesired, etc.)

   Example:

   ```md
   **Context:** I have an ASP.NET Core Web API with authorization via JWT tokens.
   The UserService has a method GetUserById() that returns null for non‑existing users.

   **Task:** Create an endpoint GET /api/users/{id} that returns user data or 404.

   **Notes:**
   - Use standard HTTP status codes
   - Add logging at Information level
   - The method must be async
   - Do not use magic strings for logs
   ```

   Sometimes it makes sense to change the order, especially if a note keeps being ignored.

   _Update as of spring 2026:_ when you work through agents, prompt structuring mostly happens automatically through system prompts and built-in templates. This structure is still useful when you work directly with a model or on difficult non-typical tasks.

10. Some things are faster to do manually. If you see the AI “stuck,” stop it and help by describing how you did it. Then it can continue and adapt.

11. TDD works well with AI. You can state this explicitly in the planning task: first create empty contracts, then write tests, and only then write code. All these should be separate steps in the plan. This effectively gives another intermediate stage: text plan → contracts + tests → implementation. An extra stage is another chance for timely intervention and correction of drift, which grows catastrophically in LLMs.

12. Ask to support decisions with links, for example for solutions based on facts about technologies or libraries, require links to those facts. Even if you do not read them, this can sometimes protect you from hallucinations.

   _Update as of spring 2026:_ modern models often generate non-existing or inaccurate links, but the requirement to provide a source often forces the model to admit that it is not sure about a fact. If a link was provided, you still need to verify it manually.

Many prompting rules that were critical for early LLMs are now either less important or already built into the models. Also, many AI tools apply system prompts and other settings under the hood automatically.

However, the skill of building good prompts remains useful in complex, non‑trivial, and domain‑specific cases where you need control, reproducibility, and precision.

## Sources and further reading

- [Prompt Engineering Guide](https://www.promptingguide.ai) - DAIR‑AI, ongoing.
- [Prompt engineering best practices](https://platform.openai.com/docs/guides/prompt-engineering) - OpenAI, guide.
- [Prompt engineering overview](https://docs.anthropic.com/en/docs/build-with-claude/prompt-engineering/overview) - Anthropic, overview.
- [Prompt Engineering](https://lilianweng.github.io/posts/2023-03-15-prompt-engineering/) - L. Weng, 2023.
- [Chain‑of‑Thought Prompting Elicits Reasoning in Large Language Models](https://arxiv.org/abs/2201.11903) - J. Wei et al., 2022.
- [Self‑Consistency Improves Chain of Thought Reasoning in Large Language Models](https://arxiv.org/abs/2203.11171) - X. Wang et al., 2022.
- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629) - S. Yao et al., 2022.
- [Tree of Thoughts: Deliberate Problem Solving with Large Language Models](https://arxiv.org/abs/2305.10601) - S. Yao et al., 2023.
- [Program of Thoughts Prompting: Disentangling Computation from Reasoning for Numerical Reasoning](https://arxiv.org/abs/2211.12588) - X. Lei et al., 2022.
- [Pre‑train, Prompt, and Predict: A Systematic Survey of Prompting Methods in Natural Language Processing](https://arxiv.org/abs/2107.13586) - P. Liu et al., 2023.


---

# 4. From chat to agents: from miracles to practice

_This article was updated for spring 2026. Updates and additions are in a separate section at the end._

Although the time of miracles is over, the time of practical use has begun. In a few leaps, LLMs jumped into the zone of high practical usefulness and keep improving steadily. But for real results, a simple chat is usually not enough.

There are fundamental limits that are far from fully solved: long‑term memory and context size. This is not only about compute resources but also mathematical complexity. Attention scales poorly, information gets diluted, and “context routing” (picking relevant information from a lot of data) becomes critical. The allowed context size is a compromise for each model. Simply increasing the window does not remove the problem; it only helps the model “not fall over.” Answer quality and speed degrade, cost grows, and errors accumulate.

## Current fundamental limitations

- **Context length and attention complexity**: the basic attention mechanism has quadratic complexity in sequence length (O(n^2)). Even with optimizations, throughput and latency get worse as the window grows. Extending context is a compromise: the model “does not crash,” but reasoning quality, stability, and speed degrade, and inference cost grows disproportionately.
- **Information dilution and relevance**: in a large window, relevant fragments are lost in noise. The model is prone to positional and recency biases (over‑weighting last or first information), overvalues the last hints and undervalues early ones. So selection mechanisms are needed - context routing and knowledge retrieval - but they have their own errors (false positives/negatives - may pick the wrong things or miss the important ones).
- **Long‑term memory as external state**: a pure LLM has no stable memory between sessions. Any “memory” requires external stores (databases, vectors, notes) and access policies. Problems: addressing (how to find), relevance (what exactly to load), consistency (how to update without conflicts), and privacy.
- **Degradation on long chains of reasoning**: errors accumulate in a cascade. Even with step‑by‑step prompting, there is drift of instructions, loss of intermediate assumptions, and contradictions between steps. Positional encoding and limited internal state do not guarantee reliable “tracing” of long proofs.
- **Reproducibility and sensitivity to context**: the stochastic nature of sampling and high sensitivity to wording lead to variable answers. Small changes in order or form of facts yield different conclusions; full determinization often reduces quality (mode collapse).
- **Cost and latency**: long contexts and large models need significant compute (memory, bandwidth, time). This limits interactive scenarios and production scale, forcing trade‑offs among quality, speed, and price.

## Using chat only exposes common difficulties

- **Establishing context**: it is hard to load large material in a structured way, keep it up‑to‑date, and relevant.
- **High prompting demands**: you must craft precise instructions, build chains of hints, and control style and output format.
- **Applying the result**: integrating answers into work artifacts (code, docs, data) is often manual, tedious, and error‑prone.
- **Preserving progress**: you need mechanisms for memory - setup prompts, personal preferences, work contexts, and prompt reuse.
- **Reproducibility and control**: without verification and logging tools, it is hard to guarantee process stability and final quality.

From here the idea of an **LLM agent** grows naturally. An agent is not just a chat, but a system that creates sub‑tasks, plans steps, calls tools (memory, file system, repositories, execution environment), evaluates intermediate results, and corrects the course.

Key properties of an agent: task decomposition, use of tools, iterative checking, stable state/memory, and controlled context routing (selecting needed information) and knowledge retrieval for the task.

The agent’s goal is not “generate an answer to a prompt,” but to organize execution of a process with constraints, acceptance criteria, and checks.

Here is an example in programming where an agent is much stronger than chat: migrating code to a new framework version in a monorepo. The agent:

1) scans the repo and builds a migration plan;
2) finds locations where APIs change;
3) makes local edits in batches, runs tests and linters;
4) collects error logs and refines the plan;
5) updates documentation and prepares a pull request with a clear diff.
Plain chat will give code fragments or advice, but will not run the full “plan → changes → checks → report” cycle.

Another example is fixing a flaky test (a test that sometimes fails for no clear reason). The agent can automatically reproduce the bug, vary the environment, collect artifacts (logs, traces), propose a patch, run tests in a matrix of configurations, and prepare a short cause‑and‑effect report.

The key strength of the agent here is in controlling tools, memory, and feedback loops that go beyond a single chat answer.

I will cover agents in more detail in the next post.

## Update for early spring 2026 (08.03.2026)

The main ideas of this article not only stayed relevant, but became even more important in practice.

_Update as of spring 2026:_

1. One of the biggest practical jumps happened exactly in the area of agents. Both the number and the quality of tools grew a lot, and agent progress now plays a role comparable to progress in the models themselves.
2. The move from chat to agents is now even more clearly a move from "answering a prompt" to "executing a process." In practice this means planning, tool use, checks, memory, context management, and iterative correction of the result.
3. Multi-agent work became especially important: one agent can launch subordinate agents, sometimes even on other models, and coordinate their work. This is one of the most effective ways to fight context overflow and loss of focus on large tasks.

## Further reading

- [Retrieval-Augmented Generation for Knowledge-Intensive NLP (2020)](https://arxiv.org/abs/2005.11401) - the foundational RAG paper combining retrieval with generation.
- [A Survey on Retrieval-Augmented Generation (2023)](https://arxiv.org/abs/2312.10997) - survey of RAG variants, indexing, knowledge refresh, and evaluation.
- [ReAct: Synergizing Reasoning and Acting in Language Models (2022)](https://arxiv.org/abs/2210.03629) - a pattern for combining reasoning with tool use.
- [Toolformer: Language Models Can Teach Themselves to Use Tools (2023)](https://arxiv.org/abs/2302.04761) - training LLMs to call external tools.
- [Reflexion: Language Agents with Verbal Reinforcement Learning (2023)](https://arxiv.org/abs/2303.11366) - self-correction and memory for agents.
- [LLM-based Agents: A Survey (2023)](https://arxiv.org/abs/2308.11432) - overview of agent architectures, planning, memory, and tool use.


---

# 5. LLM agents for software development: practical autonomy, not magic

_This article was updated for spring 2026. Updates are marked with italic notes._

An LLM agent for software development is a tool built on top of a large language model. It reads repository context, plans small steps, and performs actions (edits files, runs commands, writes tests) on the engineer’s request.

Unlike a simple chat, an agent follows a process: it proposes changes, shows diffs, can repeat iterations, and follows acceptance criteria.

## Core characteristics of an agent

- Context awareness: reads multiple files/modules, considers project structure, dependencies, and instructions in `README`/`Makefile`/CI.
- Small‑step planning: decomposes a request into a series of small edits/runs and keeps a history of attempts.
- Tool use: can call the compiler, tests, linters, package managers, scripts.
- Multi-agent work: can delegate separate subtasks to other agents or specialized subprocesses and then merge the results back into one working flow.
- Transparency: shows diffs/plans before applying; you can accept/decline parts.
- Reproducibility: keeps intermediate artifacts (logs, errors) and can form a PR.
- User control: any significant action only after confirmation; rollback is available.

## Honest note on “full autonomy”

_Update as of spring 2026:_

Fully autonomous agents for complex development (multi-module systems, non-functional requirements, integrations, security) are still closer to fantasy than reality. In early spring 2026 this mode looks technically closer than before, but truly reliable autonomy is still very far away. Most end-to-end demos work only for narrow, controlled scenarios. The "autonomous developers" mentioned in media, such as Devin, use marketing language; in real conditions their quality and stability drop sharply outside trivial tasks. A newer hype example is OpenClawn: it shows well that such modes became technically closer, but it does not prove that they are already safe for real work. In practice we still need checks, reviews, tests/CI runs, and human supervision.

**THE SAFETY PROBLEM OF THIS APPROACH IS ABSOLUTELY NOT SOLVED. YOU SHOULD EXPERIMENT WITH SUCH TOOLS ONLY USING PRACTICES SIMILAR TO WORKING WITH MALWARE. SEE [warn_1.md](warn_1.md).**

So the focus is on limited‑autonomy agents that work synchronously and “next to” the engineer.

## A typical workflow in agent mode (Cursor / Copilot)

1) Define the task: select code and describe desired changes in natural language (e.g., “migrate library X from v4 to v5; update imports and tests”).
2) Build a plan and choose files: the agent gathers context (imports, config, dependencies) and proposes a change plan.
3) Propose diffs: it shows a series of edits per file; you accept or edit parts.
4) Local checks: runs linters/tests/build; collects logs and refines the plan on failures.
5) Wrap‑up: updates docs/scripts if needed; creates a commit or a PR draft.

_Update as of spring 2026:_

In early spring 2026, multi-agent work became especially important. Earlier it looked more like a research scenario, but now it is much easier to use in practice. The idea is simple: instead of one agent pulling everything into one context window, it can delegate separate subtasks to subordinate agents - for example one for code analysis, one for plan building, one for result verification, sometimes even on other models. This saves context because each agent works only with its own local slice of the task. At the same time, it speeds up work because some subtasks can run in parallel, and it improves quality because instead of one overloaded context you get several narrowly focused passes that are merged afterward.

## Brief overview of popular agents

_Update as of spring 2026:_ the comparison below already reflects the current practical state of these tools, not only their basic description.

Cursor

- Pros:
  - As of early spring 2026, it still has a very good agent core, though no longer clearly the best: stable multi-file diffs and the "plan -> edits -> checks" loop; it appeared earlier and has more polished scenarios.
  - Fast autocomplete with good retention of the active work context.
  - A wider choice of models (OpenAI/Anthropic/Google/XAI), so you can pick for task, budget, and speed.
  - Suitable as an agent: synchronous sessions, command execution, iterations on errors.
- Cons:
  - A separate IDE (a fork of VS Code): not all VS Code Marketplace extensions are available due to licensing; alternatives are often weaker - verify critical plugins.
  - Habit factor: moving to another IDE can be painful for a team (shortcuts, configs, ecosystem).

GitHub Copilot

- Pros:
  - Almost “native” for VS Code; minimal friction in existing environments.
  - Available in Visual Studio with helpful features during debugging (explanations, fix suggestions using collected variable values).
  - Available in Xcode; also works in JetBrains/Neovim - covers most popular IDEs.
  - As of early spring 2026, it can run external agents such as Claude Code or OpenAI Codex directly inside Copilot with unified billing, which is very convenient if you do not want to assemble a custom tool stack yourself.
  - Strong for inline suggestions; convenient for small local edits.
  - It also got its own CLI, so it can be used without an IDE.
- Cons:
  - As an agent for multi‑step/multi‑file changes, it lags behind Cursor; fewer tool‑based scenarios inside the IDE.
  - Less control over model choice; harder to optimize for specific tasks/cost.
  - Autocomplete quality depends more on the active file and sometimes looks more general.

Windsurf

- Pros:
  - Still interesting because of its focus on an agent-first way of working and reducing manual coding.
  - Good as a transition option: you can start with integration into your usual IDE and later move into a separate environment if needed.
  - In spring 2026 it remains a good candidate for teams that want a more agent-centered workflow.
- Cons:
  - Like before, a separate IDE based on VS Code still brings old compatibility problems with part of the extension ecosystem.
  - In practice, the choice between Windsurf, Cursor, and Copilot is now more often defined not by absolute superiority, but by which flow fits the team better.

Google Antigravity

- Pros:
  - Interesting because of its agent capabilities and its approach to different levels of autonomy.
  - Looks like a serious candidate in this niche, not just an experimental demo product.
- Cons:
  - Still very early by the standards of real daily development.
  - Ecosystem, stability, and integration maturity still do not allow it to be treated as a default choice.

## Console agents (e.g., Claude Code / OpenAI Codex / Gemini CLI)

- Pros
  - IDE-agnostic: complete independence from the development environment - just open a terminal in your favorite IDE and work with the agent.
  - Remote execution: the CLI/API allows running tasks non-interactively, storing artifacts (logs, reports), outputting diffs/patches to files, and returning exit codes. This is convenient for embedding into GitHub Actions/GitLab CI for bulk lint/fix, test/doc generation, and audits with pipeline failure on violations.
  - These tools often get useful new features among the first, because vendors usually develop their own native agent shells first.
  - Very often the best pricing is here too, especially if you already prefer one specific model vendor.

- Cons
  - CLI flow is still not as comfortable for everyone as full IDE work.
  - For some tasks, console mode is still worse in visibility and control over small local edits.
  - At the same time, in early spring 2026 these disadvantages became much less important: such agents more and more often have IDE integrations instead of living fully separately.
  - The need for Tab completion also dropped sharply, because manual code writing is less and less the main way to work with these tools.

More specifically, in early spring 2026 Claude Code and OpenAI Codex deserve special attention. Claude Code is one of the strongest console agents in practical use: it gets new capabilities fast, works well for iterative repository work, and is a very strong option if your main development model is Anthropic. OpenAI Codex is especially interesting for people who already live in the OpenAI ecosystem. Both tools show how much CLI agent workflows have grown as a class.

## Non‑autonomous scenarios - autocomplete/inline chat in the IDE - are sometimes better than agent mode

- Small local changes and the “hot” edit loop when the cost of plan/diffs exceeds the benefit.
- High style/security demands: you want full control of every line.
- Learning a new API: hints/snippets help wire up sample code faster without extra transformations.

_Update as of spring 2026:_ in early spring 2026 it is important that the number of situations where this approach is truly better than agent mode keeps shrinking. Inline mode is still useful, but much less often the best default choice.

## More autonomous scenarios: asynchronous agents

- Cursor and Claude Code have async mode: long tasks (mass refactors, generating tests/docs, metrics, dependency inventory) run in the background; after completion you get a report and diffs for review. This is useful when work takes tens of minutes and should not block active editing.
- There are “mostly async” products like Google Jules, aimed at longer, multi‑step tasks with minimal user involvement. Their strengths are planning, environment watching, and plan repair on failures; but quality and predictability are still far from what is acceptable for critical production without human control.

_Update as of spring 2026:_ multi-agent scenarios also became much more accessible in practice. Earlier they often required custom orchestration, but now it is more and more common that you only need to start the right agent mode or delegate a subtask from the interface. That is what turns multi-agent work from a theoretical feature into a real daily tool.

Asynchronous modes in general-purpose products are used more and more often in spring 2026 and are becoming more practical. At the same time, products that are async-first by design are still relatively rare in real applied work: technically interesting, but not as broadly useful as general-purpose agents with a good async mode.

For specific tasks, for example upgrading dependencies with breaking changes using release notes, this can be useful. Or replacing one framework or library with an equivalent. In the future this may become useful for a wider set of tasks.

## Practical advice for choosing a mode

- Minimal necessary autonomy: pick the level that solves the task with the least overhead.
- Inline suggestions - for local edits, templates, and small functions.
- Agent in the editor - for changes across several files, mechanical migrations, systemic refactorings.
- Async agent - for long, noisy, resource‑intensive procedures that can be described unambiguously in one prompt and are easy to verify with reports/diffs.
- Always demand transparency: preliminary plan, per‑file diffs, run logs, and repeatable steps.

The lower bound of truth is this: agents already save hours on routine, but without human engineering judgment - not a step. The best results come from combining clear task formulation, small iterations, test checks, and careful code review. This is how agents become a productivity multiplier rather than a source of hidden debt.

## Sources and further reading

- [LLM Powered Autonomous Agents](https://lilianweng.github.io/posts/2023-06-23-agent/) - Lilian Weng, overview of approaches.
- [ReAct: Synergizing Reasoning and Acting in Language Models](https://arxiv.org/abs/2210.03629) - S. Yao et al., 2022.
- [Toolformer: Language Models Can Teach Themselves to Use Tools](https://arxiv.org/abs/2302.04761) - T. Schick et al., 2023.
- [Reflexion: Language Agents with Verbal Reinforcement Learning](https://arxiv.org/abs/2303.11366) - N. Shinn et al., 2023.
- [LangGraph](https://langchain-ai.github.io/langgraph/) - LangChain, 2024: graph agents and orchestration.
- [LangChain - Agents](https://python.langchain.com/docs/concepts/agents/) - concepts and examples.
- [LlamaIndex - Agents](https://docs.llamaindex.ai/en/stable/understanding/agent/) - overview and guides.
- [Microsoft AutoGen](https://microsoft.github.io/autogen/) - multi-agent scenarios and coordination.
- [Anthropic Claude - Tool Use / Function Calling](https://docs.anthropic.com/claude/docs/tool-use)
- [OpenAI - Assistants API and tools](https://platform.openai.com/docs/assistants/overview)
- [AgentBench](https://github.com/THUDM/AgentBench) - comparative evaluation of agents.
- [SWE-bench](https://github.com/princeton-nlp/SWE-bench) - benchmark for software engineering agents.
- [Cursor documentation](https://docs.cursor.com)
- [GitHub Copilot documentation](https://docs.github.com/en/copilot)


---

# 6. Agent extensibility

_This article was updated for spring 2026. Updates are marked with italic notes._

Although modern agents include the most needed tools, more specialized tools are usually not available “out of the box.” For developers these can be integrations with ALM systems (Jira, Azure DevOps), design prototyping (Figma), API contract schemas, or databases. Since these integrations are often complex and vendors offer different capabilities, baking all of them directly into an agent is not practical. So even early generations of popular agents provided extension mechanisms that allowed adding separate modules with extra functionality.

A typical and very useful example from practice is the PostgreSQL extension for VS Code with Copilot. It lets the agent run queries against PostgreSQL, quickly create seed or test data, check current data state, and draw useful conclusions. The advantage is that the extension is “perfectly fitted” to a specific client (editor/agent). The drawback is duplicated effort: maintaining separate extensions for each agent and each tool is very costly.

This is a classic case for a "bridge" - a shared protocol understood by both agents and tools. In 2024, Anthropic proposed the Model Context Protocol (MCP) - a standard for exchanging commands, data, and context between an agent and external services. MCP defines roles and transport so that integrations can be reusable and portable across agents.

_Update as of spring 2026:_ in early spring 2026 the importance of MCP became even higher. MCP servers already exist for almost everything that can be practically useful in development: ALM systems, repositories, documentation, design, databases, search, browser automation, and local tools. At the same time, the situation should not be idealized: the quality, support, and security of these servers are very uneven. The mere existence of an MCP server does not mean it is safe and convenient for real work.

## Roles in MCP: agent as client, tool as server

- The agent acts as an MCP client. It initiates a connection, discovers server capabilities, requests resources, calls tools, subscribes to events, and sends relevant session context to the server.
- The tool/service implements an MCP server. It describes available resources (documents, datasets, endpoints), commands (tools/actions), parameters, access rights, and response formats. The server can also offer streaming events or notifications for reactive scenarios.

The key idea: the agent does not embed support for a specific system. Instead, it calls MCP servers in a unified way. As a result, the same MCP server (for example, for Jira) can work with different agents, and any agent that supports MCP can use any compatible server.

## Connection transports: pipes and HTTP

MCP supports several ways to connect:

- Pipes/stdio: local connection via standard streams or named pipes (on Windows). Minimal latency; simpler to run locally next to the agent/IDE.
- HTTP(S): network connection to a remote MCP server. Suitable for cloud services, team work, and scaling. Allows TLS, proxies, and network‑level access control.

“Remote MCP” is also gaining momentum - deploying servers outside the local dev environment (in the cloud or internal infrastructure) that agents connect to over HTTP(S). This simplifies centralized access control, updates, and shared use of integrations by the whole team.

At the same time, another important layer is becoming more or less standardized together with MCP: extending agents with so-called "skills." If MCP describes unified access to external tools, a skill usually packages ready behavior: a set of instructions, templates, rules for working with tools, and sometimes bundled MCP integrations. In practice this means agent extensibility is no longer only about calling an external API - more and more often it comes as reusable behavior modules.

## Useful examples of MCP servers

- Atlassian Remote MCP (Jira, Confluence): practical value - quick enrichment of task context. If the description is spread across several Jira tickets and Confluence pages, the agent follows the links, gathers relevant fragments, builds a concise context, and uses it for planning or implementation. After finishing, the agent can update the ticket: set status, fill fields (for example, affected area), add links to MR/PR - the things developers often postpone.
- GitHub MCP: useful for microservice architectures with separate repositories. The agent can automatically fetch the expected service contract (OpenAPI/Protobuf/GraphQL schema), compare versions, find breaking changes, and create a PR with client updates or compatibility tests.
- Figma MCP: for frontend developers - a strong accelerator. The agent extracts specs from designs (sizes, styles, tokens), generates component/page skeletons, forms a checklist for layout, and compares implementation with the design.
- Browser MCP (for example, chrome-mcp): especially useful for web development. Such a server lets the agent not only read code or designs, but also actually open application pages, check the result of its work in the browser, walk through user scenarios, inspect the DOM, console errors, and network requests, and use that for better iterations. This matters a lot when the goal is not only to write code, but to confirm that the UI really works as expected.

The advantage for vendors like Atlassian is that they do not need a plugin for each agent or IDE. One MCP server is enough. In turn, agent authors do not need separate integrations with each ALM - they only need a stable MCP client.

## MCP security: risks and mitigations

Standardizing integrations raises the risk of wrong or excessive access. Key security practices:

- Least privilege: use separate technical accounts with the minimum rights; split read and write; disable dangerous actions by default.
- Explicit action approvals: the agent should request explicit user confirmation for destructive operations (status changes, mass updates, DB writes). Use allowlists of tools and domains.
- Authentication and audit: OAuth/OIDC, short‑lived tokens, key rotation, binding to specific workspaces. Access logs, call tracing, and storing artifacts for investigations.
- Data‑leak protection: filter PII/secrets in server responses, redaction policies, encryption in transit (TLS) and at rest when needed. Limit the amount of content returned into the LLM context.
- Prompt‑injection defense: when the agent “clicks through” Jira/Confluence pages or repository READMEs, normalize content and isolate unknown HTML/scripts. Apply content security policies and heuristics to detect instructions that change agent behavior.
- Network controls: segmentation, egress filtering for HTTP transport, rate limits, SSRF protection, and restrictions on external domains.
- Sandbox for write operations: for repos - work in forks/branches with protection; for DBs - separate test schemas or read‑replicas for reads; for ALM - sandbox projects.

## Implementation practice

### Connect a ready MCP server to a ready agent

- This is really simple: usually a few lines in configuration that the agent itself suggests or generates. In new versions of VS Code, adding many servers is available right from the marketplace with auto‑filled configuration.
- Typical scenario: choose a server (for example, GitHub/Brave/Figma), confirm the launch command, and add needed environment variables (token/key). The agent shows status (running/failed) and available tools.
- In Cursor, servers are added via settings; in VS Code - via the “MCP: Add Server” command or the extension card. Connection time is 1–2 minutes without deep technical details.

### Practices for developing MCP servers and clients in agents

- Servers: clear contract (capabilities/tools/resources), stable response schemas, versioning. Pagination/limits for context control, transparent error model, idempotence for write operations. Authentication (OAuth/OIDC), observability (logs/tracing), prompt‑injection protection, and filtering of secrets/PII.
- Clients (agents): discovery flow and feature gating, parameter validation before calls, timeouts/retries. Context budgeting, explicit confirmations for destructive actions, allowlists of tools/domains. Graceful degradation on outages, usage telemetry.
- Important: in many modern products, you should plan and offer an MCP server to the customer at the design stage. This lets not only people but also LLM agents use the service - without separate plugins for each tool/IDE.

_Update as of spring 2026:_ in early spring 2026 this should already be treated as a practical norm. If a tool or service may become part of an agent workflow, then having an MCP server or a compatible skill mechanism is more and more often not a nice bonus, but an expected property of a mature product.

## Summary

MCP removes the barrier “one tool - one extension - one agent” and moves integrations to a shared bus. The agent as a client opens tools in a unified way, and tools as servers publish reusable capabilities.

Pipes are a good fit for local scenarios with minimal latency, and HTTP(S) works for remote and team scenarios, remote MCP makes integrations a shared team resource.

Practical wins include fast task‑context building (Jira/Confluence), automatic access to contracts (GitHub), and faster layout work (Figma).

All this needs security discipline: least privilege, confirmations for dangerous actions, audit, and protection from leaks and prompt injection. Following these principles, MCP gives a balanced way to scale agent capabilities without exploding integration costs.

## Sources

- [Official MCP docs/spec](https://modelcontextprotocol.io)
- [GitHub organization](https://github.com/modelcontextprotocol)
- [TypeScript SDK](https://www.npmjs.com/package/@modelcontextprotocol/sdk)
- [Python SDK](https://pypi.org/project/mcp/)
- [Example servers and frameworks](https://github.com/modelcontextprotocol/servers)
- [Using MCP with agents (OpenAI Agents)](https://openai.github.io/openai-agents-js/guides/mcp)
- [Build an MCP server (Cloudflare Workers)](https://developers.cloudflare.com/agents/guides/build-mcp-server/)
- [Using MCP with Claude Desktop](https://docs.anthropic.com/claude/docs/mcp)
- [Azure agents with MCP](https://learn.microsoft.com/en-us/azure/developer/ai/intro-agents-mcp)


---

# 7. Bringing AI into development processes

_This article was updated for spring 2026. Updates are marked with italic notes; outdated claims are shown with strikethrough._

_Update as of spring 2026:_

By early spring 2026, we can already speak not only about future impact, but about the fact that AI has really started to change familiar software development processes and methodologies. This still does not mean the whole industry has already moved to one new "correct" way of working, but old processes are more and more often not optimal for an environment where a large part of analysis, coding, checks, and preparation is done by agent tools. At the same time, there is still no clear universal "map" of what exactly must be changed and how, and the constant growth of model capability adds uncertainty. This should not stop adoption: start with point integrations into those places of existing processes where AI already gives a clear boost in speed and quality.

_This was true in August 2025, but is outdated now:_
~~AI will significantly change familiar processes and methodologies of software development in the coming years.~~

## Safe use of AI in corporate development

The main rule: for any work tasks, use only corporate licenses and accounts of approved AI tools; personal or free accounts for working with company code and data are prohibited. This ensures enterprise privacy: data isolation, no training on your content, audit, SSO/SAML, and access control. The rest is common sense: do not paste secrets/PII into prompts and minimize the volume of context you send to the model.

An extra risk-reduction option is running LLMs locally. For example, models like Gemma 3 can be run via Ollama or LM Studio, keeping data inside your infrastructure. This greatly reduces the probability of leaks, but in early 2026 the practical balance here became worse: the quality gap between local models and the best hosted models grew even more, and the hardware demands for running strong local models also grew. The worst part is that hosted models already crossed the quality threshold after which agent-based development can often be treated as the default mode, while local models still usually do not reach that threshold in most real scenarios. So local LLMs remain important for privacy, experiments, and narrow internal tasks, but in 2026 they usually do not look realistic as a universal replacement for strong cloud agents.

There is also a less likely but real risk of license incompatibility: an LLM can generate code fragments whose license does not comply with company policies. Protection against such situations relies on provider reliability and careful use of LLMs when generating analogs of commercial algorithms.

## Tools for developers

In early spring 2026, developer tools should already be understood not only as "chat + autocomplete," but as a set of agent modes with different levels of autonomy. The practical difference between an IDE tool, a separate AI IDE, and a CLI agent became much smaller than before: the borders between these product classes are getting blurred, and the choice is more and more defined not by the mere presence of agent features, but by how well a specific tool fits your process, access policies, and normal ecosystem.

GitHub Copilot in new versions goes far beyond autocomplete: it is effectively an agent that understands workspace context, helps with PRs and issues, generates tests, explains code, navigates projects, and runs commands. It is available in most popular IDEs - from VS Code and JetBrains to Visual Studio. In enterprise settings, Copilot is purchased within GitHub organizations; if the tool is set up in one organization and repositories are in another or outside GitHub, some features may be limited by access and integration policies. It is also important that Copilot is working more and more as a shell for running different agent modes, not only as one isolated assistant.

Cursor is a separate IDE designed for agent-assisted development. It works deeply with the working folder, enabling "conversational" edits and running common tasks in the background with asynchronous agents. Since Cursor is a fork of VS Code, not all extensions are compatible: the authors offer their own alternatives for the most popular plugins, but sometimes they are not powerful or convenient enough. Even so, products like this helped make agent mode an everyday workflow for many developers.

Windsurf evolved from an extension into a full IDE tailored for AI-assisted development needs. Results are often similar to Cursor, but the approach has specifics: it has its own lightweight models for trivial tasks (they are fast, though less powerful) and limited plugins for other IDEs. The separate IDE implementation makes the environment fast and light, but some functions may be missing; before adoption, check support for your stack. Extensions partly reduce the problem but do not eliminate it.

CLI agents deserve separate attention. By early spring 2026 this is no longer a "secondary" format for enthusiasts, but one of the strongest tool classes for serious agent-based development. CLI mode is especially good for long iterative repository tasks, for running on remote servers, in CI, or in semi-autonomous scenarios with strict boundaries. Claude Code is especially worth naming: it is one of the strongest practical agent tools for developers in general, not only among CLI tools. It gets new capabilities fast, works well on large repositories, fits multi-step changes, and shows how powerful native console agents from vendors have become. OpenAI Codex and Gemini CLI should also be mentioned nearby: such tools look less and less like a compromise compared to IDE mode, and more and more like a full and often even preferred way of working for some users.

At the same time, it is important to remember that a stronger tool does not remove the need for control. The closer an agent gets to real command execution, repository changes, service access, or browser interaction, the more important it is to restrict its environment, transparently review diffs and results, and not replace engineering judgment with belief in demo effects.

## Access to model APIs

Access via Copilot, Cursor, or Windsurf does not provide “free” API keys to models. If you need integration into internal services or backends, buy APIs directly from providers like OpenAI, Google, or Anthropic (Claude) or through aggregators like OpenRouter, which provide access to different models under one contract.

API keys are useful to developers to build their own agents and RAG systems, create internal tools and bots, integrate checks into CI, and for semi‑automated migrations and refactoring. For a quick start you can use AnythingLLM as a boxed RAG. For semi‑manual testing of UIs, BrowserUse can help - an agent that can control a browser from text instructions and needs access to an LLM. If the infrastructure is in the cloud, Azure AI Foundry and AWS Bedrock let you connect external keys or buy models directly, providing enterprise‑level access control, monitoring, and billing.

## AI for working with requirements and tickets

In Atlassian products, Rovo is useful - a built‑in assistant in Jira and Confluence that helps search, summarize, and generate content. You can also reach Jira/Confluence via extensions or MCP servers from Copilot or agent IDEs like Cursor. In practice, LLMs save time with requirements: they concisely summarize epics and tickets, extract facts and risks, propose links between tasks, and detect duplicates. They can form acceptance criteria, checklists, and basic test scenarios, and speed up grooming - from splitting an epic and estimating complexity to refining wording. After work is done, an LLM can help with release notes and short status updates based on PRs and commits.

## Transcription and meeting summaries

Transcription gives the full text of a meeting, but the main practical value is summarization: quick highlighting of key decisions, action items, risks, and open questions. For example, Fireflies integrates with calendars and video-conferencing systems and provides APIs through which notes can be automatically loaded into internal tools.

## Separate warning about highly autonomous agents

By early spring 2026, this is worth repeating once again: highly autonomous agents remain dangerous, and the risk scenarios can look very different. This is not only about "the agent accidentally wrote bad code." Problems can come from tool bugs, excessive permissions, unsafe integrations, prompt injection during web work, compromised artifacts, legally toxic actions done in your name, or gradual loss of control when marketing hype creates a false feeling of product maturity. That is why loud demos of autonomous modes should not be confused with safe corporate reality.

The practical rule is simple: if a tool claims high autonomy and broad access to a repository, commands, browser, cloud, or external services, you should treat it with maximum distrust and very strict limits. For more detail, see [warn_1.md](warn_1.md).

## Sources on data‑leak and security risks

- [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) - key threats (prompt injection, data leakage, access escalation) and mitigations.
- [NIST AI Risk Management Framework 1.0](https://www.nist.gov/itl/ai-risk-management-framework) - risk management framework for AI, including privacy and security.
- [NCSC (UK): Guidelines for secure AI system development](https://www.ncsc.gov.uk/files/Guidelines-for-secure-AI-system-development.pdf) - guidelines for secure AI system development.
- [OpenAI: API data usage policies](https://openai.com/policies/api-data-usage-policies) - API data usage policy: whether data is used for training and what privacy controls are available.

## Links to tools and providers

- [OpenAI](https://openai.com/) - provider of GPT models; APIs for text/vision with enterprise privacy options.
- [Anthropic Claude](https://www.anthropic.com/claude) - Claude model family focused on safety and reasoning.
- [Google Gemini](https://ai.google.dev/) - Gemini models; access via Gemini API and Vertex AI.
- [xAI (Grok)](https://x.ai/) - provider of the Grok model; text‑focused APIs.
- [Copilot for Microsoft 365](https://www.microsoft.com/microsoft-copilot) - assistant across Microsoft 365 apps with org data and enterprise controls.
- [GitHub Copilot](https://github.com/features/copilot) - coding assistant in IDE/CLI with chat and completion; enterprise variants.
- [Cursor](https://www.cursor.com/) - IDE with deep LLM integration and agentic workflows.
- [Windsurf](https://codeium.com/windsurf) - AI‑first IDE by Codeium.
- [OpenRouter](https://openrouter.ai/) - aggregator offering multiple models behind a single API and billing.
- [Atlassian Rovo](https://www.atlassian.com/software/rovo) - built‑in assistant in Jira/Confluence for search, summarization, generation.
- [Fireflies](https://fireflies.ai/) - meeting transcription and summarization with integrations.

## Summary

The potential of LLMs for development is very large, and by spring 2026 it already moved from promise to practical reality: these tools started showing visible results both in development speed and in code quality. There is no single "ideal" methodology yet, but it is time to systematically adopt individual elements. Right now, SDD (Specification Driven Development) looks especially interesting and practical for enterprise development, because it combines AI advantages with control, reproducibility, and manageable quality; more on that is in [ai_8.md](ai_8.md). The era when these tools were toys has ended, and by spring 2026 the period when using them was optional has in practice ended as well.

_This was true in August 2025, but is outdated now:_
~~The era when these tools were toys has ended, and the period when using them was optional is quickly ending.~~


---

# 8. Specification Driven Development

_Update as of spring 2026: this approach already looks not just interesting, but like the most practical base candidate for an enterprise AI development methodology._

## Philosophy

Specification Driven Development is a return to a more classical waterfall strategy of working with code. The idea can be described in one paragraph, but there are many practical details and they change dynamically: what mattered for models half a year ago may already be losing relevance now. It is a philosophy that each team adapts to its own structure and process.

The approach is not tied to one specific tool. If needed, you can use specialized tools, but the main condition is that they fit your process and do not create extra friction.

In essence, this is a return to practices described in classic engineering literature, but later abandoned because teams did not have enough time for them under the methodologies of the last decade.

## Mindset Shift

The key mindset change is this: it is normal to spend half of a task's time, or even more, on the specification, because the direct act of writing code has in practice already been solved.

The key is to allow yourself to have no code until the very last moment, and instead work with text at a higher level of abstraction, where knowledge is compressed to its essence and it is easier to stay within the model's context size.

## Specification Building Process

Every step below is documented — the specification is a living artifact that accumulates all decisions.

### Description

1. Clearly define the problem.
2. Sketch out the solution.
3. Describe individual important scenarios in detail — how they work.
4. Describe technical solutions at various scales.
5. Describe the code style.
6. Describe the testing approach.
7. Describe integrations, the role of the current module within them, etc.

### Decomposition

1. Decompose into subtasks.
2. For each subtask — detail the business problem.
3. For each subtask — detail the technical solution.
4. If needed — hierarchical nesting. Depth is determined by complexity: a simple feature — a flat list, a complex system — a tree of subtasks with multiple levels.

### Validation

1. Completeness check — are all scenarios covered.
2. Consistency check — are there any conflicts between decisions.
3. Feasibility check - does each atomic unit of the plan fit into the model's context.
4. Improve the specification based on validation results - by yourself and with AI. Repeat until AI stops finding issues that are significant for you. The better the specification is, the faster AI will implement the plan.

If there is a technical risk, it can be checked quickly with a PoC using AI, separately from the main solution.

### Specification Updates

The specification is not a static document. During implementation, new details, clarifications, and edge cases appear, so the specification is updated together with the code. AI is very good at this: you describe what changed, and it updates the relevant parts of the specification.

## Specification Format

- The specification is best stored in `*.md` format - it gives both full text freedom and anchors that are convenient for both AI and humans.
- Embed diagrams in `*.md` using **Mermaid**. AI builds them excellently from text descriptions or from a picture (in Paint or pencil on paper — whatever is faster for you), and also perfectly understands the essence of a diagram from its source.
- **Do not add** DrawIO diagrams or images to the specification - they bloat the context and are often interpreted incorrectly. Convert sketches and ideas into Mermaid while building the specification.

## Working with AI

With AI there is no need to cut the specification short - it is written quickly. You give AI a dump of thoughts and facts, it structures and formats them, and then you review the result. That is why specification documents can be created very fast.

### Model Context

A single minimal atomic unit of the plan must fit into the model's context - today this is, depending on task complexity, around 1k to 3k lines of code.

> Crossing this threshold should be treated as critical - context degradation is practically the **main** reason for errors in modern models when everything needed for implementation is known and the model does not have to make assumptions.

### Model Assumptions

To reduce assumptions when there are gaps in the specification, your system prompt should state firmly that the model must **never** make assumptions and must ask questions instead.

Model assumptions are very often wrong, reduce speed, and waste tokens. Modern AI is an excellent translator, which is expected from the architecture, but an unreliable analyst and decision-maker.

### Prompts and Formulation

There are no magic prompts - this is no longer relevant for modern AI and agents, which already have built-in system prompts. It is enough to express your thoughts clearly, without extra noise, in the language you know best.

## Results

If the plan in the specification is correct, code generation itself takes about 10% of the time compared to traditional implementation of the same feature, based on the author's practice. Building a good specification can take much more time than code generation, but the total time is still clearly lower than in the traditional approach, with clearly better quality.

With a good specification, a good agent with a good model can implement each item of the plan with phenomenal quality and speed that even very strong human teams cannot reach. The important distinction is this: "full autonomy" without clear boundaries and decomposition is hype and a huge risk of mistakes, while autonomous execution of a well-specified and tightly bounded task atom inside SDD is already a reliable practical reality.

> Less total time, significantly higher quality, full test coverage, no magic code and no cut corners.

It needs no more review than code written by an average developer, and often less, provided the specification is clear and the context size is respected.

## Compatibility

All top models and agents fully support this approach and do not require any extra tools:

- **Models:** Opus 4.6, Gemini 3.1 Pro, GPT 5.3 Codex
- **Agents/IDEs:** Cursor, Copilot, Windsurf, Antigravity, Claude Code, OpenAI Codex

## Tools

The first well-known ready-made tool in this direction is [spec-kit](https://github.com/github/spec-kit) from GitHub. It gives an easy way to start with the approach, so you can try SDD without building your own process from zero. At the same time, it imposes many structures and conventions that may be unnecessary or not suitable for your project. Anything unnecessary consumes precious context window size and can make the result worse. Also, spec-kit is too universal to let you fully use newer and more specific agent tools. If you want to start somewhere but do not know where, it can be a good option. But once you have enough understanding and experience, a custom SDD process usually gives a better result.

## Sources

- [Spec-Driven Development & Spec-Kit - Microsoft Developer Blog](https://developer.microsoft.com/blog/spec-driven-development-spec-kit)
- [Spec-Driven Development with AI: Get Started with a New Open Source Toolkit - GitHub Blog](https://github.blog/ai-and-ml/generative-ai/spec-driven-development-with-ai-get-started-with-a-new-open-source-toolkit/)
- [SDD Tools - Martin Fowler](https://martinfowler.com/articles/exploring-gen-ai/sdd-3-tools.html)


---

# Update on Models and Capabilities as of End of 2025 (November 26, 2025)

_This is a one-time snapshot of the state at publication time (November 2025). This text is not updated; the current state is reflected in the main articles of the series, which receive regular updates._

In general, my predictions and expectations regarding models and their capabilities have not changed significantly. As I said before, the capabilities of the transformer architecture are almost exhausted, and now we only see quantitative improvements. Such quantitative improvements do not change the approaches to using AI in development described by me in previous articles, but significantly increase the performance gain and comfort from their use. If back in the summer, even those tasks that AI solves well were not always profitable to entrust to it due to unreliability and slow speed, now entrusting a suitable task to AI almost always brings more benefits than losses. The idea that AI is the main tool of a developer has now been fully confirmed (but one should not forget about the rest of the tools).

## Models

Here I describe my experience; on the one hand, it may be biased, but considering the marketing boom, practical experience is often much more useful than advertising charts and benchmarks far from real tasks. It is worth noting that all the latest models from top players (OpenAI, Anthropic, Google) are very close in capabilities, but still, there are certain strengths and weaknesses of each model.

### 1. GPT-5.1 and GPT-5.1 Codex

Models from OpenAI. The first is more general, the second is more specialized in development.
Very versatile, inexpensive in terms of tokens. They show themselves quite well in complex tasks.
But there is a big minus - like their predecessors GPT-5 and GPT-5 Codex, they are quite slow, with unstable speed, which greatly reduces productivity and comfort of working with them.
They can be interesting in cases where the main model failed, or the result raises doubts and requires verification. I would not recommend it as a default model for development.

### 2. Claude Sonnet 4.5 and Claude Opus 4.5

Models from Anthropic, basically designed for programming tasks. Sonnet 4.5 is cheaper and suitable for simple tasks. Opus 4.5 is more expensive and suitable for complex tasks. But in version 4.5, the differences between them are not as great as in previous versions; in terms of price, Opus 4.5 is sometimes cheaper on complex tasks because it finds a solution in fewer tokens. Therefore, both Opus and Sonnet can become your default models depending on your preferences, or you can change them depending on the task. The models are fast, and somewhat verbose, but this can be controlled using prompt engineering. Compared to other leaders, they will probably be the most expensive, especially on simple tasks, where if you are already used to Anthropic models, you can use Haiku 4.5 to save money. But in any case, they are worth their money and are a good default choice for development.

### 3. Gemini 3 Pro

A model from Google. It is not specialized for programming tasks, but has good capabilities for development. The model is approximately equal in speed to Claude Sonnet 4.5, but more stable in this performance, which is pleasant for working in interactive mode. A very big plus of the model is the combination of its obedience, minimum of self-activity, and stable considerable speed of work. This is especially valuable when a developer uses the model for interactive work on small subtasks - and it is this mode of operation that is most productive and does not degrade code quality. In addition, the model works great with tools, for example, a browser, emulates spatial imagination well, which makes it perhaps the best for frontend development. And for the backend, the model is also a good candidate for the role of default, and the choice between it and Anthropic models is a matter of taste.

## Tools

Little has changed here, but I will describe the main points again; perhaps it will be interesting for those who are choosing now.

### 1. GitHub Copilot

Still lags behind the leaders in capabilities and speed. But it still leads in ease of use and adaptation to your stack, as it is present for most popular IDEs in almost full form. It works best in VS Code, but in other IDEs, the limitations and problems are insignificant. It also received its own CLI, which allows using it without an IDE. It remains the optimal choice for those who do not want to change their usual development tools. But one should not forget about the binding to GitHub and the limitations associated with it (described in the previous article).

### 2. Cursor

Still leads in capabilities and speed. It has the best autocomplete, which is fast and of very high quality. And it still has certain problems and limitations for some development stacks, which depend on licensed components and extensions for VS Code. The possibilities for working with asynchronous agents have grown significantly, and there is a convenient interface for controlling them. New, more powerful models increase the range of effective application of the approach with asynchronous agents, but so far there are few tasks where this is really justified.

### 3. Windsurf

Also a great option for transition if the developer uses an IDE with Windsurf plugin support. Since the developer has the opportunity to use an extension for the start, which provides basic capabilities in their usual IDE, and in parallel, if possible, without loss of productivity, master a separate Windsurf IDE (fork of VS Code, like Cursor, with the same problems). Interesting for its focus specifically on the agent approach, with minimization of manual code writing, which becomes increasingly relevant with the development of the quality and speed of models.

### 4. Claude Code from Anthropic or Codex Cli from OpenAI or Gemini CLI from Google

Purely console tools tailored to the models of their vendor. They are the first to receive many interesting features, but still, on their own, they are suitable only for agent development. And banal AI autocomplete is often more optimal for many tasks. I still cannot recommend it as a main tool, but if there is an opportunity to try it as an addition to other tools, it can be useful, as the agent approach to development becomes increasingly viable.

### 5. Google Antigravity

IDE based on VS Code, with great agent capabilities. A novelty from Google, suitable for development with different levels of agent autonomy. So far only a preview, but it looks very similar to Cursor in its current form both in idea and capabilities. If Google can ensure better support for plugins for VS Code, develop its own or agree with Microsoft, then this can become a very interesting option for development. For now, we are observing and playing, it is too early for real projects.

_And finally, let's not forget that although AI is growing very well quantitatively, qualitatively it is still standing in place for now. So despite all the hype and marketing, do not forget the rest of the developer's tools, and most importantly the developer's own mind and skills!_


---

# Update on the State of AI Automation in Early Spring 2026 (08.03.2026)

_As of spring 2026, all texts in the series received local updates. Where fundamentally new or strongly clarified points were added, this is marked with italic notes. If an older claim became outdated, this is said directly and the outdated text is shown with strikethrough, so readers who already read the articles earlier can quickly see what is no longer current._

This is the second update to the article series. It briefly collects the changes made to the main texts and links to the files where those updates were added. By early spring 2026, the main difference is no longer in loud one-off model breakthroughs, but in the accumulation of quantitative changes. These changes moved AI automation from the category of "this will soon become normal" to the category of "this is already normal for a significant part of development."

_This is a one-time snapshot of the state at publication time (March 2026). This text is not updated; the current state and further changes are reflected directly in the main articles of the series and in the overview article [ai_0.md](ai_0.md), which continues to be updated._

## What changed by early spring 2026

1. The status of AI as the **main development tool** is now seen by most active developers not as a bold assumption, but as a fact. The debate is now mostly not about whether AI became the central tool, but about how to fit it correctly into a personal workflow, stack, and team practices. AI is now the main tool for writing code regardless of task type. The practical conclusion from the first article is now even stronger: AI can be used for almost any task if it has a clear enough description and decomposition such that one task atom fully fits into the model's context.
2. Direct model scaling really slowed down, but model progress did not stop. The situation more and more looks like CPU evolution: clock speed stopped being the main source of progress, but total system power kept growing through architecture and engineering improvements. For LLMs this means better quality, higher stability, better work with tools, and lower practical cost of mistakes. At the same time, the core weakness of the architecture is still here: models still do not have native STM and LTM, and attempts to compensate for this by simply increasing context did not create a truly major breakthrough. The context-length problem is still very serious in 2026, with almost no real progress from the models themselves, even though approximate attention schemes such as sparse, local, and linear attention only partly soften this problem.
3. The importance of "clever" prompt engineering went down a lot. Models became better, and the user more and more often works not directly with a bare model, but through agents with strong system prompts, action templates, and built-in workflows. In this area, what matters much more now is not the ability to invent a magic prompt, but the ability to control context size and context quality. In particular, setting a role is now often better done through a custom agent, because manually overriding a role on top of an existing system prompt can create instruction conflicts and make the result worse. The same is true for planning: most modern agents already have a built-in planning mode, and it is usually better to use it directly.
4. The area of agents saw probably the biggest practical jump. Both the number and the quality of tools grew a lot. Agent progress now plays a role comparable to the progress of the models themselves, and in some scenarios it matters even more, because the agent is what turns model capability into real productivity. This also changed expectations about speed: in August 2025, a very large gain was expected mostly for simple, typical, or very clearly bounded tasks, but by early spring 2026 almost any development task is usually more profitable to do with AI than without it, if it can be delegated safely and meaningfully inside a controlled process. It is also important that multi-agent scenarios became much more available in real use, not only in experiments, and asynchronous modes in general-purpose products are used more and more often.
5. Multi-agent work deserves special attention. A scenario where one agent can launch subordinate agents, sometimes even on other models, and coordinate their work is no longer exotic, but a practical tool. This is one of the most effective ways to fight one of the main problems of modern LLMs: context overflow and loss of focus on large tasks. Multi-agent work saves context window space, speeds up work through parallel execution of subtasks, and improves result quality through narrow specialization of separate passes.
6. MCP servers appeared for almost everything they could be useful for in practice. Their quality, support, and security are still very uneven, but the approach itself already became ordinary. Besides MCP, the way agents are extended with "skills" also became more or less standardized. At the tool level this also means that the borders between IDE agents, console agents, and external agent integrations became much more blurred. Native console agents from vendors such as Claude Code and OpenAI Codex became noticeably stronger, and the CLI format now more and more often looks not like a compromise, but like one of the strongest practical forms of agent work.
7. The process of deep AI adoption in development is still difficult, but the need for it keeps growing because of the possible gains. By early spring 2026 it is already clear that AI started changing not only the speed of separate tasks, but also the familiar processes and methodologies of software development themselves. As agent autonomy grows, the safety question becomes much sharper: technical, legal, and organizational. Marketing hype can easily hide these risks. For complex and non-typical tasks, the problem now is usually not the use of AI itself, but the lack of a detailed enough specification: trying to solve such tasks directly through an agent without preparation usually only wastes time and tokens. It is also important that local models are now even further behind hosted models both in quality and in the practical threshold needed for agent-based development, while hardware demands for local setup also keep growing. Full autonomy in spring 2026 is still closer to fantasy, even if technically it became nearer. At the same time, the safety of such modes is absolutely not solved, which can be seen especially well in hyped examples like OpenClawn. For more about this risk class, see [warn_1.md](warn_1.md).
8. SDD (Specification Driven Development) is more and more looking like the practical standard for enterprise AI use, because it allows teams to keep control over code and quality, and often even improve them, while still speeding development up noticeably. This naturally leads to [ai_8.md](ai_8.md).


---

# Warning About Autonomous Agent Tools (OpenClawn as an Example)

## TL;DR

OpenClawn (ex. ClawdBot, Moltbot) is a highly autonomous agent with elevated permissions by default, built-in web search, and additional modules — thousands of which have already been created.

Despite the hype (and buying of the tool by OpenAI), it is **not recommended** for any real-world tasks, as the current risk level is unacceptable. In its present form, it is more of a dangerous "toy" that should only be experimented with using approaches similar to malware research.

**Important:** although this text uses OpenClawn as an example, the risks described apply to **any** highly autonomous agent with broad permissions. This is a problem of the solution class, not a specific product.

## Risk Categories

Risks can be divided into two groups:

- Accidental (bugs, errors, incorrect actions without malicious intent)
- Intentional (attacks/influence by malicious actors)

---

## 1) Accidental Errors and Bugs

The tool may be raw and buggy. Combined with auto-approvals, this can lead to severe situations due to errors, for example:

- deletion or corruption of your data;
- populating data with content that looks "normal" on the surface but is incorrect;
- sending an email/message with absurd or dangerous content;
- deploying a flawed product to the cloud with reputational or financial losses;
- other scenarios where the result looks plausible but is wrong.

Practical takeaway: everything you give such an agent access to can be ruined simply due to a bug.

### Why Running in a VM Doesn't Always Help

You can run the agent in a VM without personal data or credentials, with limited internet access (only to the model provider). But if you **don't give the agent "hands"** (tools/access for actions in the system, network, repositories, cloud), it essentially becomes a **regular chatbot**, and the practical value of an autonomous agent disappears.

Between the extremes of "full autonomy" and "chatbot without hands" there is a spectrum of intermediate options: human-in-the-loop (confirmation for each dangerous action), read-only modes, sandboxed execution with a limited set of allowed operations. While this does not guarantee safety, having a human as a validator can eliminate most of the most powerful and destructive errors and attacks. This is currently the recommended way to apply AI in real-world tasks.

Separately: even in a well-isolated environment, **artifacts created by the agent can be compromised** (code, scripts, configs, IaC, containers, documents, etc.). Therefore, everything you take out of the VM should be treated as untrusted and verified (change audits, scanning, reproducible builds, manual review of critical sections).

If you give the agent full authority for real tasks without additional confirmations, there is a risk that everything will be botched in the worst-case scenario.

---

## 2) Intentional Actions by Malicious Actors

This is more dangerous than accidental errors.

### Legal Aspect

You may involuntarily become both a victim of a crime and an unwitting accomplice. Everything done on the network from your machine or from a server purchased in your name legally appears as your actions — making the risks of "complicity" no less serious than the risks of being a "victim."

### Typical Attack Vectors

- Backdoors in modules/plugins from the author (who may be a malicious actor) or modules that have been compromised.
- Prompt injection during web browsing by the agent: a malicious instruction that forces the agent to perform a dangerous action.

### Why Prompt Injection Works

In current LLMs, there is no reliable separation between context and commands/data. When the agent feeds website content to the model, the subsequent output — which the agent interprets as an action plan — can be dictated by instructions hidden in the page content.

It is especially dangerous that even "trusted" sites with UGC (where third-party users can add content) can contain such instructions, because it is just text.

Prompt injection techniques include:

- hidden text on a page (white text on white background, `display:none`, HTML comments), invisible to humans but read by the agent;
- instructions split across different parts of a page (or even across different sites), which only combine into a single command within the agent's context;
- "poisoning" popular resources (Stack Overflow, GitHub issues, forums) — an attacker posts an answer with a hidden instruction that the agent finds while searching for a solution.

### Why Defense Is Hard

Countermeasures typically lag behind attack tools:

- a malicious prompt can be well hidden;
- spread across different parts of a site or across multiple sites;
- the attack can be multi-step, where each step appears safe to LLM filters.

In fresh, hyped tools like OpenClawn, even primitive attacks can work.

### Potential Consequences

In practice, you should assume that an attacker can gain effective access to everything the agent has access to.

Even if the agent "only collects data from the web," this too can be used against the user:

- an attacker can force your agent (for whose actions you are legally responsible) to perform harmful actions on the network;
- restricting to GET requests only does not guarantee safety: not all content on the web is legal to even retrieve, which can be used for blackmail;
- the attack may target not the agent's host, but rather making it produce a compromised product — for which you are also responsible (since legally you are the product's author).

---

## Recommendations

- Don't fall for the hype — do not use such agents for production tasks with real data, credentials, and consequences until the risks become manageable.
- For research and educational purposes, treat them as potentially malicious software:
  - in a non-personalized virtual machine;
  - with constant monitoring and analysis;
  - with restricted actions and traffic;
  - with detailed analysis of artifacts created by the agent.


---

