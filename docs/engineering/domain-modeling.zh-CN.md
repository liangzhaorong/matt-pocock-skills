## 它做什么

`domain-modeling` 在你做设计的同时构建和打磨一个项目的**统一语言（ubiquitous language）**。它会质疑一个与词汇表冲突的术语，会在你用了模糊词的地方要一个精确的词，并拿具体场景去考验一个关系，直到边界精确为止。

它是**主动的**纪律，不是被动的。任何技能都能读 `GLOSSARY.md` 来借用它的词汇。这个技能用于你正在*改变*模型的时刻，也正因此它会打断你。它在对话进行中、术语敲定的那一刻就把它写进 `GLOSSARY.md`，而不是在结尾产出一份整洁的词汇表。结尾写的词汇表是一份[会话](https://www.aihero.dev/ai-coding-dictionary/session)的摘要；内联写的这一份是会话的产出。

## 何时该用它

输入 `/domain-modeling`，或者当任务合适时智能体自动取用它。实践中，自动调用是这个技能最弱的一环。当 `grill-with-docs` 或 `wayfinder` 说要加载它时，[模型](https://www.aihero.dev/ai-coding-dictionary/model)常常加载了 `grilling` 却跳过这一个。如果一场[拷问](https://www.aihero.dev/ai-coding-dictionary/grilling)会话跑完而 `GLOSSARY.md` 原封未动，那就是发生了这种事。和其他技能一起、按名字点名调用它。

当问题出在*用词*上时，找它：

| 处境 | 动作 |
| --- | --- |
| 两个人说的“取消”不是一回事 | `domain-modeling`：选定规范术语，把另一个列进 `_Avoid_` |
| “账户”在三个文件里干着三种活 | `domain-modeling`：把它拆成 Customer 和 User |
| 你刚做了一个难以逆转的架构选择 | `domain-modeling`：如果这个选择够格，它会提议一份 ADR |
| 问题在模块的*形状*：接缝放哪、接口多深 | [codebase-design](https://aihero.dev/skills-codebase-design) |
| 你想在动手之前让整个计划被审问一遍 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，它在底下驱动这个技能 |
| 你只是想查一个词，不是改它 | 什么都不用。读 `GLOSSARY.md`，它就是个文件。 |

## 前置条件

事先什么都不需要。这个技能往两个地方写，两处都是惰性创建：

- 仓库根目录的 **`GLOSSARY.md`**。你敲定第一个术语时技能创建它。在根目录有 `GLOSSARY-MAP.md` 的仓库里，术语改放进地图所指的那个按上下文划分的 `GLOSSARY.md`。
- **`docs/adr/`**。第一份够格的 ADR 出现时技能创建它。

开始前不需要任何东西存在，技能也不会提前创建任何东西。

## 两种产物，两条门槛

词汇表和 ADR 的门槛不同。这个技能的多数麻烦都来自把两者弄混。

| | `GLOSSARY.md` | `docs/adr/NNNN-slug.md` |
| --- | --- | --- |
| 装什么 | 术语。一样东西**是**什么，一两句话，被否决的同义词列在 `_Avoid_` 下 | 一个决定，一到三句话：背景、选择、理由 |
| 写入门槛 | 一个模糊术语成为了规范用词 | **三条全占**：难以逆转、没有上下文会让人意外、是一次真实权衡的结果 |
| 写入方式 | 内联，术语敲定的那一刻 | 提议，而非自作主张 |
| 绝不装 | 实现细节、[规格](https://www.aihero.dev/ai-coding-dictionary/spec)、草稿本、泛泛的编程概念 | 本次会话每个选择的流水账 |

ADR 的三条测试缺任何一条，就没有 ADR。容易逆转的决定到时候直接逆转就好；不让人意外的决定没人会问；没有真实替代方案的决定，记录的只是“你做了显然的事”。

`GLOSSARY.md` 这条规矩最要紧，因为实践中先破的就是它。**它是一份词汇表，仅此而已。**放任不管的话，模型会把“写入 `GLOSSARY.md`”当成把你给出的每个回答都持久化的许可，这个文件就变成一份滚动更新的规格。这是这个技能被报告得最多的问题，横跨好几个模型。

## 交叉核对，以及它止步于何处

当你陈述某样东西如何工作时，技能会去核对代码并指出任何矛盾。*“你的代码取消的是整张订单，可你刚说部分取消是可能的，哪个对？”*技能让语言和代码先大声达成一致，然后才改其中任何一方。

它交叉核对的是**代码**、已提交的 `GLOSSARY.md` 和 ADR，仅此而已。它不搜你的 issue 跟踪器。如果你的团队几个月前在某个已关闭的 issue 里吵清并解决过一个命名冲突，技能会把它当新问题重新提起来。修复此事有一个[未决请求](https://github.com/mattpocock/skills/issues/717)。在那之前，绕法是把指示放进你自己的 `docs/agents/domain.md`，技能们本来就读它。

## 常见问题

**我的 `GLOSSARY.md` 有 500 行、1000 行、3000 行，怎么办？**
尺寸是症状。原因是这个文件吃进了本来就不属于词汇表的实现细节和决定。修法是一条直接指示：`/grill-with-docs make my GLOSSARY.md more concise and remove any implementation details from it`。对着一个浮肿的文件跑一遍，大半会消失。只有当文件已经精炼、却仍然覆盖两个读者不想同时装进脑子的领域时，才用 `GLOSSARY-MAP.md` 拆它。拆一个浮肿的文件只会得到好几个浮肿的文件。技能在这方面的指引还不足以从源头阻止膨胀，追踪此事的 issue 仍未关闭。

**为什么是 `GLOSSARY.md` 而不是 `CONTEXT.md`？**
1.3.0 之前它确实叫 `CONTEXT.md`（和 `CONTEXT-MAP.md`）。这个文件是一份词汇表、仅此而已，所以 `GLOSSARY.md` 把这一点说出了口；而且正如一位读者所说：“有了 AI 智能体，什么都是[上下文](https://www.aihero.dev/ai-coding-dictionary/context)”。如果你的仓库还有 `CONTEXT.md`，重跑一遍 `/setup-matt-pocock-skills`：它会提议 `git mv` 这个文件并更新 `docs/agents/domain.md`。整套技能里的每一个都找 `GLOSSARY.md`，留着旧名字意味着给它们全都打补丁。

**`/ubiquitous-language` 去哪了？**
被移除了，而且不是退役。它的工作并入了 `domain-modeling`，后者持续维护整个模型，而不是从一场对话里倒出一份词汇表。词汇的执行现在更重要了，不是更不重要。它跑在拷问、分诊和制图的底下，而不是一道你要记得去跑的独立工序。

**怎么给一个没有词汇表的代码库建一份？**
明确开口要，而不是等它自己攒出来。有文档记载的路子是 `/grill-with-docs help me scaffold my existing repo with a GLOSSARY.md`。做好被长期审问的准备。一位用户报告说，文件成型之前被问了 50 多个问题。在存量仓库上，顺带使用攒词汇表的速度太慢了。

**我能保留领域模型，但用自己团队的 ADR 格式吗？**
今天还做不到干净利落。词汇表这一半和 ADR 那一半装在同一个技能里，于是一个已有成熟 ADR 约定（不同模板、不同位置、不同命名）的团队拿到的指示会与自家风格打架。目前的选项是把技能复制到本地改掉，或在你仓库自己的智能体文档里覆盖 ADR 约定。把两者拆开是一个[未决请求](https://github.com/mattpocock/skills/issues/557)。

**词汇表真的值回成本吗？它又多了一件要评审的产物，还会过时。**
有时不值。DDD 离实现越近越没用。回报在上游，在命名和概念对齐上，不在聚合根和分层仪式上。同义词控制在命名边界上要紧：模块名、表名、状态枚举、issue 标题、CLI 命令。在普通散文里它要紧得多。还有一种反对意见说，领域术语压缩的是已经共享它们的*人类之间*的沟通，而智能体对平实的英文描述响应得一样好。按这种读法，词汇表的价值是让你和你的评审者与智能体正在做的事保持对齐，而不是让智能体变好。一天的构建，跳过它。而且一份没人评审、由智能体代写的词汇表比没有更糟：它会装满自信的断言，后续会话会把它们当真。

**它能替我把含糊的提示词变成领域语言吗？**
不能，也没有做这件事的技能的计划。一种连你自己都不理解的领域语言，写下来也是无意义的。这个技能在你有了理解之后执行精确；它不发明你没有的词汇。相关的陷阱是用着领域词却没做建模。正确的名词架在错误的概念上，产出的东西读起来像是对的，其实不是。

## 有这些表现就说明它在生效

- 它会在你话说一半时停下来，问你指的是两样东西中的哪一样，而不是自己挑一个继续。
- `GLOSSARY.md` 在对话**进行中**变化，而不是结尾爆发一次。
- 它拒绝为一件你明天就能撤销的事写 ADR，并说三条测试败在哪一条。
- 新词条用一两句话定义一样东西*是*什么，并在 `_Avoid_` 下点出你放弃的那些词。
- 当你的代码和你的话不一致时，它把代码原文引回来给你看。
- `GLOSSARY.md` 变短的频率和变长一样高。

## 它的位置

`domain-modeling` 是一份**模型可调用的参考**，更多时候跑在其他技能*底下*，而不是单独运行。[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 在拷问会话中驱动它，[wayfinder](https://aihero.dev/skills-wayfinder) 制图时加载它，[triage](https://aihero.dev/skills-triage) 用它让[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)保持项目自己的用词，[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 在决定落定时调用它。它最近的同类是 [codebase-design](https://aihero.dev/skills-codebase-design)。两者共同构成其余一切之下的词汇层：这一个管*领域*，那一个管模块的*形状*。它也可以直接调用，用于你想要这份纪律、却不想套上通常会加载它的那些技能的步骤的时候。拿不准哪个技能合身时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
