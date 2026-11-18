## 它做什么

`to-tickets` 拿一份计划、一份[规格](https://www.aihero.dev/ai-coding-dictionary/spec)或你正身处的这场对话，把它拆成你 issue 跟踪器上的一组**[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)**。每张工单声明自己的**阻塞边（blocking edges）**：那些必须先完成它才能开工的其他工单。

每张工单都是一颗**曳光弹（tracer bullet）**：一条窄但完整地穿过这次变更每一层（schema、API、UI、测试）的路径，一落地就能单独演示。正是这一点让这个技能不同于拆工作最顺手的做法：一次切一层，最后再集成。它还把每张工单裁到装得进一个全新的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)，因为一个从没见过你规格的[会话](https://www.aihero.dev/ai-coding-dictionary/session)会来接这张工单。

## 何时该用它

你通过输入 `/to-tickets` 来调用它。[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)不会自己主动取用它。

| 你在哪里 | 跑什么 |
| --- | --- |
| 你有一份规格 issue，且构建跨好几次会话 | `/to-tickets`，或 `/to-tickets #<spec_issue>` |
| 计划只在对话里，从没成文 | `/to-tickets` 直接读线程，不需要规格 |
| 整个变更装得进一个上下文窗口 | [implement](https://aihero.dev/skills-implement)，跳过工单 |
| 什么都还没定 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，然后 [to-spec](https://aihero.dev/skills-to-spec) |
| 一张 [wayfinder](https://aihero.dev/skills-wayfinder) 地图已经清空 | 先 [to-spec](https://aihero.dev/skills-to-spec) 收拢地图，再 `/to-tickets` |

`to-tickets` 产出的工单生来就是智能体可用的。不要对它们跑 [triage](https://aihero.dev/skills-triage)。分诊是给从别人那里找上门的工作的。

## 前置条件

`to-tickets` 往跟踪器里发布，所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须已为这个仓库配好一个，连同 triage 标签词汇。两种都行：像 GitHub 或 Linear 这样的真跟踪器，或 `.scratch/` 下的本地 markdown 文件，后者无需额外配置。

## 曳光弹，不是分层

**水平**切片交付变更的一层。每一层都落地之前什么都不能用，而且每张工单的验收标准都得伸进别的工单拥有的工作里。**垂直**切片（曳光弹）一次交付一根穿过所有层的细路径，所以它能独自验证，并拥有它要评分的一切。

这是人们最常破的规则。一个团队跑过一堆按层切的 26 张工单（语料、生产者、聚合器、选择器），每张关闭的工单大约引来二十次智能体运行，其中约四分之三是返工。他们自己的复盘把每一类失败都追回了水平切片，而不是实现本身。

发布之前先发生两件事。`to-tickets` 找预重构（prefactoring，原则是“先让变更变容易，再做那个容易的变更”），并把那份工作排在最前。然后它把拆解排成编号清单拿来考你：粒度对不对、阻塞边是不是真的、有没有该合并或该拆的。你批准之前什么都进不了跟踪器，而那场小考就是你反驳的地方。

## 阻塞边

边才是这份产物的意义所在。它们以两种方式工作，取决于跟踪器：

| 跟踪器 | 边住在哪 | 你怎么用 |
| --- | --- | --- |
| 本地 markdown | 每张工单一个文件，位于 `.scratch/<feature>/issues/<NN>-<slug>.md`，按阻塞者在前编号 | 从上往下，手工 |
| 真跟踪器（GitHub、Linear） | 原生阻塞链接，或跟踪器有子 issue 的话用子 issue | 阻塞者都做完的任意工单都在**前沿（frontier）**上，可以抓走 |

无论哪种，边都住在工单里。跟踪器只决定有没有东西能并行地依据它们行动。`to-tickets` 产出这份产物；跑它（一次一个会话，或一支舰队）是你的活，不是技能的。

## 宽重构例外

有一种形状破掉曳光弹规则。**宽重构（wide refactor）**是一次单一的机械变更（重命名一列、改一个共享符号的类型），其**波及半径（blast radius）**盖住整个代码库。一次编辑弄碎几千个调用点，所以没有任何垂直切片能落地为绿。

`to-tickets` 把它改排成**扩张-收缩（expand-contract）**：

- **扩张（Expand）**：在旧形式旁边加上新形式，什么都不破。
- **迁移（Migrate）**：按波及半径分批（按包、按目录）挪调用点，一批一张工单，每张都被扩张阻塞。CI 保持绿色，因为旧形式还在。
- **收缩（Contract）**：一旦没有调用方剩余，删掉旧形式，在一张被每个迁移批次阻塞的工单里。

连批次都无法独自保持绿色的地方，它们共享一条集成分支，并全部阻塞最后一张“集成并验证”工单。CI 只须在那张工单上是绿的。

## 常见问题

**它为一次三行的变更产出了十二张工单。**
过度拆分是这个技能被报告得最多的问题，很多用户都遇过。[模型](https://www.aihero.dev/ai-coding-dictionary/model)默认原子单元，丢掉了能让它们有意义的分组。小考那一步就是你修它的地方。让它合并工单，它就会合。还有一条下限：如果整个变更装得进一个上下文窗口，你根本不需要这个技能。直接去 [implement](https://aihero.dev/skills-implement)。

**工单出来一层一张：schema 全在一张，API 全在另一张。**
这正是垂直切片规则要防的失败，而技能有时仍会产出它。在小考那一步，对每张工单问一个问题抓住它：这张做完我能演示什么？答不上来的工单就是水平切片。有人因此给每张工单加了一行“演示路径”，并报告说这能把模型推向垂直切片。

**在 GitHub 上，工单没有建为规格 issue 的子 issue。**
这在十几次运行、好几个模型上被报告过，[issue #554](https://github.com/mattpocock/skills/issues/554) 记录得最全。已修复：`to-tickets` 现在把每张工单建成其来源 issue 的子 issue，GitHub 跟踪器模板给出了命令（`gh issue create --parent <n>`，`gh` 2.94+）。重跑一遍 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 刷新旧的 `docs/agents/issue-tracker.md`。

**“Blocked by”被写进了 issue 正文，而不是一条真正的阻塞链接。**
这是同类问题，[issue #513](https://github.com/mattpocock/skills/issues/513) 有报告，智能体当时甚至断言 GitHub 根本没有原生的阻塞关系。它有：`gh issue create --blocked-by 12,15`。因为技能先发布阻塞者，创建时它们的编号总是现成的。正文文本本该是没有原生边的跟踪器的退路，不是默认。当边是原生的，工单模板现在会省掉 `## Blocked by`。

**本地工单放哪？v1.1 的说明说放根目录一个 `tickets.md`。**
曾经是，而那是个 bug。单一共享文件在并行智能体往里写时还引发竞态。本地模式现在把每张工单写到 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，按依赖顺序，与本地跟踪器模板早已描述的布局一致。`NN` 前缀是真正的工单 ID，所以 `/implement 03` 就行，不必重打一长串标题。

**它读我的规格时一直被截断。**
一份非常大的规格会大到超出一个 tracker issue 能干净返回的量。没有本地副本可以退回，于是智能体把[工具调用](https://www.aihero.dev/ai-coding-dictionary/tool-call)花在一遍遍取片段上，永远到不了结尾。在 `/to-spec` 和 `/to-tickets` 之间不要 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 或 [compact](https://www.aihero.dev/ai-coding-dictionary/compaction)。在同一个上下文窗口里跑它们，智能体就不必回头去取规格。

**验收标准什么都没评：有些在任何工作开始前就通过了。**
模板要标准，却对“它们能不能失败”一个字不说，于是发生这种事。三种形状反复出现：一条在基础 commit 上已经为真的标准、一条只有别的工单里的工作能满足的标准、一条复述请求而不是从产物推导的标准。垂直切片防住其中大部分，因为一根交付新行为的切片在基础 commit 上按构造就是失败的。这道检查仍值得手工做。对每条标准，说出能证明它为假的那个观察，并确认它在实现者出发的那个 commit 上是失败的。

**工单发布出去了，可我到底怎么跑它们？**
技能到产物为止，没有自动派发模式。派发是手动的：看板子，数一数没有开着的阻塞者的工单，开那么多个智能体会话。给每张工单一个全新的上下文，之间 clear。[implement](https://aihero.dev/skills-implement) 在 GitHub 或本地 markdown 上都不能可靠地关闭或勾掉工单，所以工单状态由你自己更新。

## 有这些表现就说明它在生效

- 每张工单都答得出“这张做完我能演示什么？”，而且答案是行为，不是一层。
- 在任何东西发布之前，清单编号排好回到你手里，每张带一行 “Blocked by”。
- 最上面那张没有阻塞者，可以立刻开工。
- 工单正文里没有文件路径或行号，原型产出的片段除外。
- 每张工单读起来都像一件全新会话不需要你在场就能做完的事。
- 找到的预重构排在顺序最前面，而不是混进特性工单里。

## 它的位置

`to-tickets` 是主构建链上的一步：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

上游是 [to-spec](https://aihero.dev/skills-to-spec)，递给它一份定案的规格来切片。两者放在同一个上下文窗口里，中间不要 clear。下游是 [implement](https://aihero.dev/skills-implement)，每个全新会话建一张工单，用 [tdd](https://aihero.dev/skills-tdd) 做测试，以 [code-review](https://aihero.dev/skills-code-review) 收尾。[implement-spec](https://aihero.dev/skills-implement-spec) 是另一条下行路：它把同样的阻塞边读成任务图，在一条集成分支上并行建每一张就绪工单。拿不准哪个技能或流程合身时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
