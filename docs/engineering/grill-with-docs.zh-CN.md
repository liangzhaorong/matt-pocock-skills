## 它做什么

`grill-with-docs` 就一个计划或设计访谈你，直到你和[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)对它共享同一份理解，并一边访谈一边把词汇和硬决定写进你的仓库。它跑的是 [grill-me](https://aihero.dev/skills-grill-me) 同一场访谈（一轮问题，然后等，然后下一轮），只是对准一个代码库。

它是**[有状态（stateful）](https://www.aihero.dev/ai-coding-dictionary/stateful)**的。其他所有拷问技能都把[会话](https://www.aihero.dev/ai-coding-dictionary/session)留在你脑子里；这一个在磁盘上留下文件。一个术语敲定的那一刻，技能立刻把它写进 `GLOSSARY.md`，而不是最后攒一批。一个决定通过三道门时，技能把它写成一份 ADR。这就是全部差别，而人们对这个技能的多数麻烦也源于此。这些产物是真仓库里的真文件，所以它们可能在你以为有的时候缺席，也可能在不止一个人写它们时漂移。

## 何时该用它

你通过输入 `/grill-with-docs` 来调用它，智能体不会自己主动取用它。

在一次变更的开始、在一个仓库里、当计划还模糊、这件事的用词还没定下来时，找它。它是单会话工具。要哪个拷问技能，取决于你面前是什么：

| 你手上有什么 | 用哪个 |
| --- | --- |
| 你根本不在一个工作目录里干活 | [grill-me](https://aihero.dev/skills-grill-me) |
| 一个仓库，加一次你能在一届会话里定下来的变更 | `grill-with-docs` |
| 一件大到单次会话装不下的事（一块绿地构建、一个大特性） | [wayfinder](https://aihero.dev/skills-wayfinder) |
| 一个完全没有领域文档的仓库，也没想好做什么特性 | `grill-with-docs`，对准仓库而不是某次变更 |
| 一个决定卡在别人脑子里的知识上 | [to-questionnaire](https://aihero.dev/skills-to-questionnaire) |

与 wayfinder 的分野归结为会话数：`/grill-with-docs` 管单会话规划，`/wayfinder` 管多会话规划。

## 前置条件

这个技能往你的仓库里写，所以你得待在一个写起来安全的地方。敲定的术语进根目录的 `GLOSSARY.md` 词汇表；若根目录的 `GLOSSARY-MAP.md` 把仓库标记为多上下文，则进相应上下文的 `GLOSSARY.md`。决定进 `docs/adr/`。两者都是技能需要时才创建。第一个术语或决定敲定之前什么都不会存在，所以你什么都不用提前布置。

它还需要另外两个技能在场，因为它自己的 `SKILL.md` 只有一行，是个委托。[grilling](https://aihero.dev/skills-grilling) 供应访谈，[domain-modeling](https://aihero.dev/skills-domain-modeling) 供应写作。只装 `grill-with-docs` 会得到一个不能工作的技能。

## 纸面痕迹

一场会话产出三样东西，它们并不平等。

| 敲定了什么 | 落在哪里 |
| --- | --- |
| 一个术语：项目对某样东西自己的叫法 | `GLOSSARY.md`，内联，敲定的那一刻 |
| 一个难以逆转、没有上下文会让人意外、且是真实权衡的决定 | `docs/adr/` 下的一份 ADR |
| 你定下的其他一切 | 对话里，别无他处 |

第三行是让人栽跟头的地方。`GLOSSARY.md` 只是一份词汇表。它不放实现细节、不放[规格](https://www.aihero.dev/ai-coding-dictionary/spec)、也不放草稿笔记。一份 ADR 需要三个条件同时成立，所以大多数决定不合格，大多数会话一份也产不出。一场收获更锋利词汇表、零 ADR 的会话是按设计在工作，但这意味着你们谈定的大部分东西只存在于你们谈定它的那个[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)里。把那场对话交给 [to-spec](https://aihero.dev/skills-to-spec)，而不是把它 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 掉。

词汇表是主要产出。这个技能建设领域语言：项目自己的词，敲定一次，这样你、智能体和你的同事就不必再推敲一遍。不是所有人都同意这能改善智能体的表现。最强的反对意见是：一个术语和它的平实英文展开从[模型](https://www.aihero.dev/ai-coding-dictionary/model)那里得到同样的结果，而这套词汇主要是缩短共享它的人类之间的沟通。按这种看法，词汇表仍然有价值，只是价值归人类。

## 常见问题

**该用这个还是 `/wayfinder`？**
范围说了算。一次会话能定下来的都用这个；当工作量大到一次装不下时用 [wayfinder](https://aihero.dev/skills-wayfinder)，它会先把工作画成一张由决策[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)构成的地图。Wayfinder 更慢、更重，在一个范围清晰的特性上找它是常见的错误。它不取代这个技能，而且它可以为地图中适合的部分发起一场拷问会话。

**它跑完了，可 `GLOSSARY.md` 和 ADR 都没出现。**
有两个已知原因。第一个是什么都不合格。ADR 需要三道门全过，而一场关于“没有任何新词汇的变更”的会话没有东西可写。第二个是一个真实的 bug。当这个技能跑在另一个编排层里（一个规格驱动开发的外壳、一个多智能体框架、一条把它当作别人流水线中一步的规则）时，用户报告说写文件的那一半悄悄没有发生，访谈却照常进行。bug 已立单未修。如果你在这种配置里，先检查工作目录，再相信会话的产出。

**它一口气问完了所有问题，不给建议，也从不提 `GLOSSARY.md`。**
那是这个技能没能加载它的两个依赖。因为 `SKILL.md` 是一行委托，一个没有把 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 拾起来的智能体会瞎猜拷问是什么意思，于是你得到的是毫无结构、一次全来的问题。部分加载更让人困惑：`grilling` 加载了、`domain-modeling` 没加载，你会得到一场好访谈，却没有纸面痕迹。这发生得多频繁取决于模型和[努力级别（effort）](https://www.aihero.dev/ai-coding-dictionary/effort)，而它也是这个技能被报告得最多的问题。如果你起了疑心，直接问智能体它加载了哪些技能。

**我其余的决定都去哪了？**
只进了对话。这是对这个技能最严重的未决抱怨。词汇表不是规格，大多数回答挣不到一份 ADR，也没有任何记录把每个敲定的回答连到一份规格、一张工单和一个测试。后面的步骤会把精确的回答（顺序保证、否定式需求、数值默认值）软化成更弱的散文，结果可以看起来完整，却缺了你要定的那个东西。眼下，留住会话，把它直接喂给 [to-spec](https://aihero.dev/skills-to-spec)。然后拿规格对照你自己的回答再读一遍，别假设它都收进去了。

**能把它对准一个完全没有文档的现有仓库吗？**
能。对一个没有 ADR、没有领域语言、没有设计原则的代码库，这个技能正合适：调用它，说“帮我给我的仓库建档”。用户常把它和 [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 搭配，用来建或修 `GLOSSARY.md`。准备好掌舵。它读代码、就它发现的东西问你，由你来定代码库里已有的那些词哪些是对的。

**会话结束时我该做什么？**
技能的收尾消息常常是开放式的，这是一个已知问题。在主流程里，答案是 [to-spec](https://aihero.dev/skills-to-spec)，就在同一场对话里。如果变更小到可以立刻动手建，就改走 [implement](https://aihero.dev/skills-implement)。

**它为什么叫这个名字？**
没人对这名字满意。有一个未决提议把它改名为 `grill-domain-model`，那更准确地描述了行为。此事没有任何进展。如果改名落地，文档页会跟着走，URL 也会变。

## 有这些表现就说明它在生效

- `GLOSSARY.md` 在会话*进行中*逐词变化，而不是最后一大块冒出来。
- 词汇表读起来是纯词汇（你项目的词，配紧凑的定义），不含任何实现细节或规格式的散文。
- 代码库能回答的问题靠读代码库来回答，而不是拿来问你。
- 你得到很少或零份 ADR，而得到的那几份都是“要再争一遍你会烦”的决定。
- 因为你已有的词汇表对某个词的定义不同，它会挑战你用的那个词。

## 它的位置

`grill-with-docs` 是主构建链的头：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

它排在任何东西被写成规格之前。它产出共享理解和定案的词汇，[to-spec](https://aihero.dev/skills-to-spec) 随后不必再访谈你就能综合它们。它的近邻是 [grill-me](https://aihero.dev/skills-grill-me)，同一场访谈但没有仓库没有文件；以及 [domain-modeling](https://aihero.dev/skills-domain-modeling)，它所驱动的词汇表与 ADR 纪律。两者都用 [grilling](https://aihero.dev/skills-grilling) 这个原语来做访谈。在它上游，[wayfinder](https://aihero.dev/skills-wayfinder) 为大到装不进一次会话的工作制图，并能把地图的一部分交还给它。拿不准哪个技能或流程合身时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
