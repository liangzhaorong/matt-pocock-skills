## 它做什么

`ask-matt` 是本仓库技能之上的路由器。你描述自己当下的处境（一个不知从何下手的想法、一堆涌进来的 bug 报告、一段已经跑得很长的[会话](https://www.aihero.dev/ai-coding-dictionary/session)），它便给出合适的那一个技能或技能序列，并指出这条序列里哪些地方需要人来决策。

它只推荐，到推荐为止。它不会拷问你、不会写[规格](https://www.aihero.dev/ai-coding-dictionary/spec)、不会打开文件、也不会触发它刚点名的技能；你拿回来的是下一步该输入什么，由你自己输入。它还是一张手工绘制的本仓库技能地图，而不是对你已安装内容的扫描，所以它不会把你路由到你自己的技能或别的作者写的技能上。

## 何时该用它

你通过输入 `/ask-matt` 来调用它；智能体不会自己主动取用它。

| 你的处境 | 路由器给出的回答 |
| --- | --- |
| 有一个想法，却不知道从哪里开始 | 主流程的入口，以及这个构建是否小到可以跳过规格 |
| 别人报来的 bug 和需求 | [triage](https://aihero.dev/skills-triage) 这条入口匝道，以及为什么你自己生成的[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)不该走上它 |
| 两个看起来可以互换的技能 | 它们之间的分界线，而这通常是一条具体的判据，不是口味问题。[grill-me](https://aihero.dev/skills-grill-me) 还是 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，取决于你是否在一个工作目录里；[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 还是 [wayfinder](https://aihero.dev/skills-wayfinder)，取决于这份工作量是否装得进一次会话 |
| 一段很长的会话，外加一个关于[上下文](https://www.aihero.dev/ai-coding-dictionary/context)的决定 | 阶段边界处五个选项的有序决策树 |
| 已经选好的技能 | 没什么有用的。直接调用那个技能。 |

## 前置条件

路由器只点名技能，不安装技能。它指向的每样东西都得已经装好，推荐才可执行；而且它只认识本仓库中已推广（promoted）的技能。

依赖跟踪器的路线（triage、`to-spec`、`to-tickets`、`implement`）假定 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 已经在仓库里配置好一个 issue 跟踪器。哪怕还没配置，路由器也会推荐它们。

## 是流程，不是技能

这个技能的头号关键词是**流程（flow）**：一条穿*过*各技能的路径，而不是单个技能。你说出处境时，路由器把你放到某条流程的某一步上。这与“这是匹配你关键词的技能”是两种不同的回答。路线共分五类，技能本身对它们有完整描述：

- **主流程**，从想法到交付。拷问、规格、工单、实现（一次一张工单，或用 [implement-spec](https://aihero.dev/skills-implement-spec) 并行整个任务图）、评审，然后是 [retro](https://aihero.dev/skills-retro)，把这次构建学到的东西回灌进智能体的环境。它有两条分支。一条是原型绕行，用于某个问题需要可运行的代码才能定论的时候。另一条是“规格与工单”的拆分，只有当构建跨越不止一次会话时，才值得付出这份成本。
- **入口匝道（On-ramps）**，用于那些会产出工作、随后并入主流程的处境：涌进来的 bug 报告、坏掉的东西，或一件太模糊、太大、装不进单次会话的工作。
- **代码库健康**，是保养而不是功能开发。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 勘察代码中可深化的机会，它找到的每一项都会作为一个想法重新进入主流程。
- **独立件（Standalones）**，不在任何流程上，单独使用：原型、问卷、调研。
- **底下的一个词汇层**，即那两份参考，供其他技能在问题出在用词而非流程时取用。

## 阶段边界

这个技能的另一个关键概念是**阶段边界（phase boundary）**。一个阶段是会话内的一块工作（[拷问](https://www.aihero.dev/ai-coding-dictionary/grilling)、实现、QA），两个阶段之间的边界是“我该拿这个上下文怎么办？”这个问题唯一该出现的地方。阶段进行中没有要决定的事：要么继续，要么把剩下的拆给[子智能体](https://www.aihero.dev/ai-coding-dictionary/subagent)。

| 选项 | 什么时候选它 |
| --- | --- |
| **继续** | 下一阶段需要这一阶段的内容原封不动，或者你的[聪明区（smart zone）](https://www.aihero.dev/ai-coding-dictionary/smart-zone)还有余量。它是唯一能让会话保持为[一手来源（primary source）](https://www.aihero.dev/ai-coding-dictionary/primary-source)的做法，所以先对它做排除 |
| **`/clear`** | 你身后的一切都是可丢弃的。最便宜的选项，而一旦判断错了就无法撤销 |
| **[handoff](https://aihero.dev/skills-handoff)** | 有东西需要转移到别处：一个新的[harness](https://www.aihero.dev/ai-coding-dictionary/harness)、一个新目录、一位同事、一件在阶段中途 fork 出来的支线任务 |
| **子智能体** | 任务的范围收得足够紧，可以让你[人不在键盘前（AFK）](https://www.aihero.dev/ai-coding-dictionary/afk)运行 |
| **`/compact`** | 以上皆非。它是默认选项，这棵决策树也常常到它为止 |

其中两项人们常常选错，所以路由器给出的不只是清单，还有顺序。`/handoff` 看起来像是在上下文窗口之间挪动的通用办法，但它不是。它给你的只是可携带性。`/compact` 是这棵树里的最后一个选项，而不是第一个，因为它前面的四个选项每一个都更便宜或更精确。

## 常见问题

**就不能给一份按正确顺序排好的技能清单吗？**

人们不断在 README 里要这样一份清单。这个技能就是那份清单。一张静态表格会写成 `wayfinder → to-spec → to-tickets → implement → code-review → retro`，而这对大多数情况都是错的。重要的部分在于分支：有没有代码库、构建是否跨会话、这个问题能不能靠谈来解决。代价是这张路由图靠手工维护，所以会落后于仓库。`/grilling` 早已发布，路由器却很久之后才点到它。

**它告诉我有一半技能没装。**

这是一个已知 bug，而且没有修复。路由器带你走过的技能大多设置了 `disable-model-invocation: true`，这意味着 harness 会把它们从注入智能体上下文的技能清单里略掉。智能体把那份清单当成完备的，于是报告它们缺失。在一次被报告的会话里，它宣称整条“规格与工单”流程都不存在，改路到光秃秃的 `/grilling` 和 `/tdd`。插件的二十七个技能里有十六个带这个标志，所以这是常态而不是边缘情况。它们是装了的。照常输入斜杠命令即可，或者查 `.claude-plugin/plugin.json`，它才是“装了什么”的权威。

**它描述了某个技能的行为，可那个技能并不那样做。**

这同样是一个真实存在、且未修复的 bug。路由器靠它自己为每个技能写的一行摘要来回答，而不是靠技能本身。一份详细的报告在单次会话里追到了三个实例，其中一次它仅凭摘要“把这段对话变成规格”就建议跳过 [to-spec](https://aihero.dev/skills-to-spec)。路由器从头到尾没有打开过 `to-spec/SKILL.md`。每一次都是等用户提出质疑后它才去核实，从不主动核实。那次跳过 `to-spec` 付出了一次真正的接缝检查的代价，产出的工单也低估了工作量。当路由器陈述某个你将据此行动的关于其他技能的说法时，先让它打开那个技能的 `SKILL.md`。这条同样适用于地图完全没覆盖的问题，比如是否该用[计划模式（plan mode）](https://www.aihero.dev/ai-coding-dictionary/agent-mode)：那种回答是[模型](https://www.aihero.dev/ai-coding-dictionary/model)的推断，不是这里写下的东西。

**为什么写成散文，而不是带编号的清单？**

这是个合理的抱怨。一个 open issue 主张：大多数路由是确定性的，而叙事体让人难以扫读。你完全可以要求压缩版：说“直接给我序列”就能拿到序列。散文体承载的是条件部分：那些分支、哪些地方需要人来决策、哪些步骤之间该 clear 或 compact。而一张扁平的清单丢掉的恰恰是这些。

**它能路由我自己的技能，或其他作者的吗？**

不能。先后有三份提案想要一个能读你本地 `skills/` 目录、从已安装的技能里做推荐的路由器。`ask-matt` 不是那个东西。它是一套特定技能的手工地图，对你自己写的或从别处安装的技能一无所知。

**它让我去改某个 SKILL.md。**

这个建议常常是对的，但那次改动很少能活下来。有人问它怎么让 [implement](https://aihero.dev/skills-implement) 关闭工单，它让对方往技能里加一行，那人立刻看到了问题所在。`npx skills update` 会覆盖这个文件，插件安装则是只读的。把长期行为放进你自己的 `CLAUDE.md` 或 `AGENTS.md`，或在调用时口头说明。你在提示词里做的改动能活过更新。人们就是这样把流程指向 Linear 而不是 GitHub 的，或者问它哪些 open 工单可以并行。

**它点到了我没有的技能，或漏掉了我有的。**

在断定某个技能消失之前，先查 changelog 里有没有改名。`writing-great-skills` 改成了 [writing-for-agents](https://aihero.dev/skills-writing-for-agents)，没有留别名；`to-prd` 改成了 [to-spec](https://aihero.dev/skills-to-spec)；`pathfinder` 改成了 [wayfinder](https://aihero.dev/skills-wayfinder)。还有四个技能被直接退役，并入了吸收它们的技能：`ubiquitous-language`、`design-an-interface`、`qa` 和 `request-refactor-plan`。如果它漏掉了你有的技能，那就是上文说过的路由器自身的滞后。

## 有这些表现就说明它在生效

- 它以点出你该输入什么收尾，并到此为止，而不是自己动手开工。
- 它给出的路线提到了在哪里该 clear 或 compact 上下文、哪里需要你来做评审，而不只是一串技能名。
- 当两个技能很接近时，它说得用哪一个、另一个为什么不适合你。
- 它对另一个技能行为的任何断言，都会在 trace 里表现为它读过那个技能的 `SKILL.md`。
- 你能在它交回的东西里认出自己的处境，而不是最接近的某个泛泛场景。

## 它的位置

`ask-matt` 是一个凌驾于整套技能之上的**独立路由器**。它从不是链条上的一环。它指向每一条链，其他文档页又链接回它，这样谁都不用重画这张图。从这里出发，你最常落到 [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，即主流程的入口，或 [triage](https://aihero.dev/skills-triage)，即“找上门来的工作”（而非你发起的工作）的入口匝道。

它是它所描述技能的[二手来源（secondary source）](https://www.aihero.dev/ai-coding-dictionary/secondary-source)。凡路由器与某个 `SKILL.md` 说法不一致处，以 `SKILL.md` 为准。
