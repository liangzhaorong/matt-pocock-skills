## 它做什么

`wayfinder` 接手一件大到超出单次智能体[会话](https://www.aihero.dev/ai-coding-dictionary/session)的工作。这是一件**目的地（destination）**叫得出、路线还看不见的想法。Wayfinder 把它绘成 issue 跟踪器上一张由**决策工单（decision ticket）**构成的共享**地图（map）**，然后一次一张地解决工单，直到路线清晰。

它规划，不构建。每张工单问一个问题，答案是一个决定，不是一片构建。在有人构建这个东西之前再没有要决定的事，地图才算完成。这条规则把 wayfinder 工单和普通实现[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)区分开，而它也是智能体最常破的规则。地图清空时，wayfinder 交棒，不继续进入代码。

## 何时该用它

你通过输入 `/wayfinder` 来调用它；[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)不会自己主动取用它。

它是整套里最重的流程，所以触发条件收得很窄。这份工作必须大到超出一次智能体会话装得下的量，且通往目的地的路线必须不清楚。分野在会话数：`/grill-with-docs` 管单会话规划，`/wayfinder` 管多会话规划。

| 你面前是什么 | 跑什么 |
| --- | --- |
| 一个范围清晰、一次坐下来能定完的特性 | [grill-me](https://aihero.dev/skills-grill-me)，或有代码库时用 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 一个绿地项目，或一次跨很多次会话、路线仍不清楚的构建 | `/wayfinder` |
| 一个决定已经做完的线程 | [to-spec](https://aihero.dev/skills-to-spec)：直接跳过地图 |
| 一张已清空的 wayfinder 地图 | [to-spec](https://aihero.dev/skills-to-spec)，然后 [to-tickets](https://aihero.dev/skills-to-tickets) 和 [implement](https://aihero.dev/skills-implement) |
| 一个已经长得太大的现有会话 | 说“hand off 到 `/wayfinder`”（[handoff](https://aihero.dev/skills-handoff) 既能把工作桥接进地图，也能桥接出来） |

绿地不是必要条件。人们在遗留和半成品代码库上照常使用 wayfinder，而且在那里反而可能更有用，因为大片迷雾是“这里已经什么是真的”，而不是“我们该做什么”。

## 前置条件

地图和它的工单住在仓库的 issue 跟踪器上，所以 wayfinder 需要 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 做的跟踪器设置。那一步写下一个“Wayfinding operations”一节。这一节描述在 GitHub、GitLab 或本地 markdown 上如何表达地图、它的子工单、阻塞边和前沿查询。Wayfinder 通过你 `CLAUDE.md` / `AGENTS.md` 里的指针找到那份文档，而不是在固定路径。没配跟踪器时，它退回本地 markdown 文件。

跟踪器干真实的活。它的原生阻塞链接在跟踪器自己的 UI 里显示前沿。在没有原生依赖链接的跟踪器上（比如自建的 Gitea），wayfinder 从地图文本推断阻塞。那能用，但你必须盯得更紧。

## 地图、迷雾与前沿

**地图**是一个带 `wayfinder:map` 标签的单独 issue，它的工单是它的子 issue。它是一份**索引，不是仓库**。一个决定恰好住在一个地方，它的工单；地图只给一行摘要和一个链接。会话以低分辨率加载地图，需要时再打开单张工单。这让地图可以持续生长，而不必每个会话都加载它的全部历史。

地图有四节：

- **目的地（Destination）。**这张地图的终点长什么样。你最先说出它，在任何工单存在之前，因为目的地固定了你衡量每张工单所对照的范围。
- **已做的决定（Decisions so far）。**每张已关闭的工单一行，各带一个指向详情所在地的链接。
- **尚未指明（Not yet specified）。**这就是**战争迷雾（fog of war）**：你感觉得到会来、却还说不利索的那些决定。区分迷雾和工单的判据是你*现在*能否把问题精确地说出来，不是你能否回答它。你解决一张工单时，它前方的迷雾散开，现在能说清的东西毕业成一张新工单。
- **范围之外（Out of scope）。**目的地之外的工作。迷雾只朝目的地聚集，所以范围之外的工作保持关闭，永不毕业。

**前沿（frontier）**是开着的、未被阻塞的、未被认领的工单的集合（已知世界的边缘）。会话在做任何工作之前先把工单指派给自己来认领它。指派人*就是*认领，所以并发的会话跳过那张工单。会话用名字称呼工单，从不用光秃秃的 `#42`，因为一墙的 issue 编号在叙述里很难读。

## 四种决策工单

每张工单带一个 `wayfinder:<type>` 标签。每张工单要么是 **[HITL（人在回路）](https://www.aihero.dev/ai-coding-dictionary/human-in-the-loop)**（与一位为自己发声的人一起做），要么是 **[AFK（人不在键盘前）](https://www.aihero.dev/ai-coding-dictionary/afk)**（由智能体独自驱动）。一张 HITL 工单只能通过现场交换解决。自己回答自己[拷问](https://www.aihero.dev/ai-coding-dictionary/grilling)问题的智能体已经把它弄坏了。

| 类型 | 模式 | 什么时候用 | 由谁解决 |
| --- | --- | --- | --- |
| `grilling` | HITL | 默认。这个问题谈一谈就能定。 | [grilling](https://aihero.dev/skills-grilling) 加 [domain-modeling](https://aihero.dev/skills-domain-modeling)，在全新会话里 |
| `prototype` | HITL | “这该长什么样”或“它该怎么表现”：谈话定不了的问题。 | [prototype](https://aihero.dev/skills-prototype)，工单带一个指向做成之物的链接 |
| `research` | AFK | 工作目录之外的一个事实正卡着一个决定。 | 一个 [research](https://aihero.dev/skills-research) [子智能体](https://www.aihero.dev/ai-coding-dictionary/subagent)，制图时启动，在 `research/<name>` 分支上并行跑 |
| `task` | 皆可 | 没有要决定的事，但手动工作卡着一个决定，比如开通访问权限、注册一个服务、挪一份数据好看清它的形状。 | 智能体能干就自己干，否则给人类一张精确的清单 |

`task` 是唯一“做事”而非“做决定”的类型。它上地图只因它解除一个决定的阻塞，从不是因为它交付目的地的一部分。这个类型在实践中最容易出岔。智能体把它读成实现步骤，开始在地图里写产品代码。

调研是“每会话一工单”的唯一例外。

## 常见问题

**这和 `/grill-with-docs` 有什么区别？该从哪个开始？**
看会话数，不看项目大小。`/grill-with-docs` 是单会话规划；wayfinder 是多会话规划。如果整件事装得进一场对话，拷问更便宜更好，wayfinder 对那种情况更慢更重。社区的速记是：只有当工作装不进单次会话时，wayfinder 才说得通。这是 wayfinder 被问得最多的问题，遥遥领先。人们不断问它，因为技能描述不告诉你你自己的任务在这条线上的位置。会话数得你自己判断。

**它问“目的地”时，指的是这次会话的结束，还是一切的结束？**
整张地图的终点，不只是第一个会话。这个问题读起来有歧义，但 wayfinder 按定义就是多会话工具，所以一个会话范围的答案永远说不通。典型的目的地是一份要交出去的[规格](https://www.aihero.dev/ai-coding-dictionary/spec)、一个要在规划开始前锁死的决定、一个概念验证，或一次就地变更比如数据迁移。

**地图清空了。wayfinder 不是已经写好规格、立好工单了吗？为什么还需要 `/to-spec` 和 `/to-tickets`？**
不是。wayfinder 的工单是决策工单，地图关闭时它们也都关了。剩下的是一张满是互相链接的决定的地图，而那不是一份构建计划。[to-spec](https://aihero.dev/skills-to-spec) 把那些链接的决定收拢成一份规格（`/to-spec #<map_issue>`），[to-tickets](https://aihero.dev/skills-to-tickets) 再把那份规格切成曳光弹式的实现工单。如果你把地图直接循环进 [implement](https://aihero.dev/skills-implement)，你就跳过了收拢，丢了链接的细节。只有当工作量最后发现很小，才直奔实现。有人跑过更短的管线并报告说能用。多出来的两步给你一份评审者或同事能读的明确规格，不单干时这更要紧。

**我的智能体在 wayfinder 会话中途开始写生产代码。**
这是这个技能被报告得最多的失败，而技能里的一个真实缺口导致了它。你可以在地图的**备注（Notes）**里覆盖 wayfinder“只规划，不执行”的默认。但备注是智能体写的，于是约束和它的豁免同居在一个受约束的智能体自己拥有的文件里。一位用户眼看着一个智能体往自己的备注里写下“这张地图承载执行”。在后来的会话里，智能体把那行读成了许可，在在线服务器上动工。这个技能对“我指的是默认”没有硬停止。在它有之前，读一读任何不是你亲手绘的地图上的备注，把实现留在分开的会话里，并把任何看起来像构建切片的 `wayfinder:task` 当成打错了类型。

**我绘了 27 张工单，做到第十三张时，剩下的已经说不通了。**
这个问题逐字来自一份用户报告，其他人也报告了同样的结局。默认情况下，wayfinder 做全面规划。当后面的工单建立在前面的工单作废的假设上时，地图就落进了批评者指责这个技能的瀑布陷阱。两件事有帮助。第一，把地图的范围定在一个有界的目的地上，而不是整个产品。用户报告说，范围定在一个定义清晰的 epic 上的地图，比一张摊开的“实现 V1”表现更好。目标是交付小的增量，不是规划一件很大的事。第二，激进地做[原型](https://www.aihero.dev/ai-coding-dictionary/prototyping)。路线保持新鲜，是因为便宜的具体产物会在实现依赖不确定性之前把它暴露出来。Wayfinder 是“原型拉满（prototypemaxxing）”，不是“规划拉满（planmaxxing）”。

**我能同时做几张工单吗？**
前沿告诉你哪些工单可以拿，阻塞边让并行工作在纸面上安全。实践中，一次一张是更安全的默认。如果你同时做两张拷问工单，一个会话可能问起你刚在另一个会话里答过的问题，因为会话之间不共享[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。原型工单也有一个已知缺口。一位用户报告说，一个智能体做了三个 UI 变体，自己挑了一个，然后把工单关了。那个选择是你的，而技能还没把这一点说得足够清楚。如果你确实并行跑工单，先自己过一遍依赖图。

**我必须用 GitHub Issues 吗？**
不必。任何 issue 跟踪器都行。GitHub 支持最好，因为它的原子 issue 和阻塞关系让前沿不用打开地图就可见。人们也用 GitLab、Linear、Jira 和本地 markdown。有两条注意事项。在没有原生阻塞的跟踪器上，wayfinder 从文本推断依赖图，你必须手工纠正它。本地 markdown 把这些产物放进你的仓库，这不被推荐，因为存在仓库里的材料容易无意间留存。开源维护者撞上相反的问题（公开跟踪器被智能体生成的规划工单填满），却仍常选本地 markdown。

**拷问累死人。每个问题都三段长。**
这是对 wayfinder 最尖锐的未决抱怨，还没有人修好它。一位用户这样拆解：冗长本身就造成决策疲劳，而长度又藏住了智能体*为什么*问这个问题，于是随着地图长大，你丢掉了从决定到决定的链条。冗长看起来是当前这批[模型](https://www.aihero.dev/ai-coding-dictionary/model)的属性，而不是技能的。用户试两种缓解：调低[推理努力](https://www.aihero.dev/ai-coding-dictionary/effort)，在你的全局 `CLAUDE.md` 里放一条平实语言的指示。反正要做好在这里艰苦思考的准备。Wayfinder 要求你想很多，而那正是它大部分的目的，不是缺陷。

**一个我已经关闭的决定后来发现是错的。我该改旧工单，还是立一张新的？**
没有官方指引，而智能体的默认帮不上忙。它倾向于绕着坏决定设计，而不是挑战它，所以你必须自己掌舵。有效的做法是直白地告诉 wayfinder 什么变了。它随后更新地图、修订受影响的工单、在已关闭的工单上评论。你可以从地图中途的范围变更中恢复。但如果你把地图设计成会变的样子，那就是范围错了的信号。

**`decision-mapping` 去哪了？**
就是这个技能。v1.1 把它改名为 `wayfinder`，用 `/wayfinder` 调用。“决策地图”是行话，而且不准确，因为四种工单类型里只有一种本身就是决定。新名字给了技能一套一致的词汇（目的地、战争迷雾、前沿、地图），而不是在顶上再摞一个生造词。单位保留了“决定”这个词：一张 wayfinder 工单是一张**决策工单**，免得人们把它读成实现工单。

## 有这些表现就说明它在生效

- 在任何一张工单存在之前，目的地已写下并谈定。
- 每张开着的工单读起来都是一个问题。任何写着“构建 X”的工单要么打错了类型，要么属于地图的下游。
- 不用打开地图，看一眼跟踪器就知道哪些工单可拿，因为原生阻塞显示了前沿。
- 一个会话解决一张工单，把答案发成一条解决评论，关掉它，往地图的“已做的决定”加一行。然后它停下。
- “尚未指明”随时间收缩。迷雾毕业成工单时，它离开那一节，不会同时出现在两处。
- 当开场的广度优先拷问根本找不到迷雾时，技能停下来告诉你，这件工作小到可以跳过地图。
- 收尾地图的那个会话把你指向一份规格，而不是一个 pull request。

## 它的位置

`wayfinder` 是一条**情境性的入口匝道**，不是默认起点。大多数工作仍然从拷问领头的“想法 → 交付”链开始。当想法大到一次会话装不下时，你才用 wayfinder。它在 [to-spec](https://aihero.dev/skills-to-spec) 处重新并入那条链，因为清空的地图交棒、不构建。

大部分工作发生在 wayfinder 调度的其他技能里。[grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 解决默认工单类型，[prototype](https://aihero.dev/skills-prototype) 解决谈话定不了的工单，[research](https://aihero.dev/skills-research) 以子智能体运行，好让它的阅读待在你的会话之外。[handoff](https://aihero.dev/skills-handoff) 把工作搬进搬出：从一场长得太大的对话搬进地图，会话中途冒出支线任务时再搬出地图。其他一切，[ask-matt](https://aihero.dev/skills-ask-matt) 在整套技能之上路由。
