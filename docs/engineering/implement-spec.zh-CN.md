## 它做什么

`implement-spec` 拿一份[规格](https://www.aihero.dev/ai-coding-dictionary/spec)和它的[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)，一次运行把整件事落地。编排[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)把每张工单交给一个在独立 git worktree 里工作的实现[子智能体](https://www.aihero.dev/ai-coding-dictionary/subagent)，把每条完成的分支合并进一条单一的**集成分支（integration branch）**，对结果跑 [code-review](https://aihero.dev/skills-code-review)，然后解决工单。

它把工单读成一张**任务图（task graph）**，而不是一张清单。阻塞边决定什么能开工，所以任一时刻都存在一条由“阻塞者都已落地”的工单构成的**前沿（frontier）**，前沿上的每张工单同时开跑。这就是与逐张做工单的差别。定节奏的是图的形状，不是工单在跟踪器上的顺序。

## 何时该用它

你通过输入 `/implement-spec` 来调用它，智能体不会自己主动取用它。

| 你的处境 | 用哪个 |
| --- | --- |
| 一份规格，已按阻塞边拆成工单，你想一次运行全部落地 | `/implement-spec` |
| 一次一张工单，在你自己的[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)里，工单之间 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) | [implement](https://aihero.dev/skills-implement) |
| 一份还没拆成工单的规格 | 先 [to-tickets](https://aihero.dev/skills-to-tickets) |
| 一件小活，谈不上有什么图 | 直接 [implement](https://aihero.dev/skills-implement) |

## 前置条件

- **一个 issue 跟踪器。**技能从 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 配好的跟踪器上读工单，也在它上面解决工单。如果一个都没配，它会停下来叫你先去跑那个，而不是瞎猜。
- **带阻塞边的工单**，按 [to-tickets](https://aihero.dev/skills-to-tickets) 写出的那样。没有边，图就是平的，每张工单同时开工。
- **一个能在后台跑子智能体、并给每个一个 git worktree 的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)。**这个技能为同时开工而存在，所以在一次只跑一个子智能体的 harness 上，它只是一个更慢的 `implement`。

## 集成分支

一切落在一条分支上。每个实现者：

1. 开工前确认自己的 worktree 基于集成分支，
2. 用 [tdd](https://aihero.dev/skills-tdd) 构建自己的工单，一次一个切片地红绿循环，
3. 报告完成前把集成分支的最新提交合进自己的分支，这样落地就是一次快进（fast-forward）。

要不要 pull request 由跟踪器决定。如果你的跟踪器通过 PR 关闭工作，或你主动要一个，技能会在第一次合并后开一个 draft PR，并在最后标记为 ready。否则运行就停在集成分支上，每张工单都以你的跟踪器关闭工作的方式解决，对着一个本地 markdown 跟踪器可以完全离线工作。

实现者通过[上下文指针](https://www.aihero.dev/ai-coding-dictionary/context-pointer)（规格、工单、共享的勘察笔记、更早的 commit）与编排者沟通，而不是贴来贴去的摘要。这让每个子智能体的提示词保持小，也给编排者的窗口留出装下图的空间。

## 常见问题

**这和我自己对每张工单跑一遍 `/implement` 有什么区别？**

这正是这个技能存在要回答的问题。在它发布之前，人们不断自造轮子，一位用户这样描述需求：他们想要“子智能体来实现工单”，而不是“一个一个地新建会话、再挨个叫它们实现一张工单，而一份规格可能包含超过 5 张工单”。用 `implement` 时你是调度员：每张工单一次[会话](https://www.aihero.dev/ai-coding-dictionary/session)，中间 clear，还得自己记着哪些工单已解除阻塞。`implement-spec` 把这份活交给一个编排会话。代价是每张工单的工作落地时你不再逐张读；你在最后评审集成分支。要开始一次运行，清掉上下文，输入 `/implement-spec` 加一个指向规格的指针（issue 编号或文件路径）。没有真正图结构的小改动，跳过它，直接用 `implement`。

**它需要 GitHub 吗？我想让它停在分支就好。**

不需要，现在不需要了。一位喜欢开发中版本的用户提的正是这条意见：“它最后会创建一个 PR，这需要一个像 GitHub 那样的在线仓库。我希望它能在离线状态下干同样的活，停在所有工作都已合并的那条分支上。”现在运行就结束于集成分支。只有当配置的跟踪器通过 PR 关闭工作、或你主动要求时才开 PR，所以在本地 markdown 跟踪器上，运行以“每张工单已解决、工作已合并到分支上”收尾。

**它的评审修复循环一跑几个小时，或者不停“修复”还没建过的工单。**

两种都发生在 `code-review` 跑在技能给它的那个点以外的时候。它拿代码对照整份规格，所以只在每张工单都落地之后才有意义。如果它在运行中途跑，每张未建的工单都会被读成一次失败。智能体于是去建那张工单，那又触发一轮评审。在结尾，技能跑一次 `code-review`，把所有发现发给一个修复子智能体，但它还没说这次修复之后何时停。一位用户报告一个五工单的特性“评审修复循环花了大约四个小时”。如果你看到第二轮宽泛评审开始，让它只对修过的发现跑聚焦检查并停下。预期第一轮评审会找到真问题。这次运行的产出是一份草稿，由评审把它补完，不是可以直接交付的东西。

**它像 implement 那样驱动 tdd 吗？**

现在驱动了，起初并不。跑开发中版本的用户注意到“实现子智能体不继承 /tdd 指示”，于是一从单张工单放大到整份规格，红绿就停了。现在每个实现者都用 `tdd` 构建自己的工单。仍然没有像 `implement` 会话里那样让你交互式敲定接缝的步骤，所以想把接缝钉死，就在规格或工单里点名。

**两个并行实现的智能体撞在同一个文件上，或给同一样东西起了不同的名字。**

Worktree 不能消除碰撞，只能把它推迟到合并时。一条从工单文本写出的阻塞边，只是对“每张工单会碰哪些文件”的猜测，而两张“代码库不同部分”的工单仍会共享一个消息目录、一个配置注册表或一个类型。每个实现者只看到自己的工单和共享笔记，从看不到对方进行中的工作，于是一位用户的 web 和移动端工单给同一个字符串分别加了 `blockedSince` 和 `blockedOn`。当前沿上的两张工单碰同一个共享文件时，要么在它们之间加一条阻塞边让它们一前一后跑，要么在勘察笔记里定死每张工单要加的确切名字。

**被阻塞的工单永远不开工，哪怕阻塞它的工作已经合并了。**

这是 GitHub 上的一个已知问题。跟踪器的 blocked-by 计数只在阻塞者*关闭*时才降，而工单通常在 PR 合并时关闭，那已经是运行的结尾。跟踪器是起始图正确的来源，运行中途却是陈旧的。告诉编排者自己去跟踪哪些工单已合入集成分支，并据此计算前沿。

**它取代 Sandcastle 或某个 AFK 脚本吗？**

不。人们这么问是因为这套技能如今伸进了实现领域：“Sandcastle 还有意义吗？你们的技能现在似乎也能处理实现了。”`implement-spec` 让一个智能体在一次 harness 会话内负责编排，这不需要基础设施，也让你能看着并掌舵。对真正 [AFK（人不在键盘前）](https://www.aihero.dev/ai-coding-dictionary/afk)的工作，一个确定性循环（[Sandcastle](https://github.com/mattpocock/sandcastle)、一个 shell 脚本、一个 CI 任务）更快、更便宜、更可靠，因为没有智能体在做编排决策。

**某张工单的关键测试在它的 worktree 里被跳过，它却报告绿色。**

Worktree 只装 git 跟踪的东西。读取 gitignore 掉的夹具、本地数据库或凭据的测试，在那里会被悄悄跳过。对一张验证依赖未跟踪材料的工单，告诉编排者改在主检出里跑它。

## 有这些表现就说明它在生效

- 只要图允许，就有多个实现者同时在跑，而不是一个接一个。
- 一张工单在它最后一个阻塞者落上集成分支的那一刻就开工，而不是等整次运行结束。
- 每张工单的 trace 里都有 `tdd` 在跑，代码之前先有失败的测试。
- 合入集成分支的都是快进，不是冲突解决。
- 运行结束在一条分支上，每张工单都已解决，PR 只在你的跟踪器需要时才有。

## 它的位置

`implement-spec` 是主链条的构建步骤，是“每张工单跑一次 [implement](https://aihero.dev/skills-implement)”的并行替代：

```txt
grill-with-docs → to-spec → to-tickets → implement-spec → retro
```

它的邻居是 [to-tickets](https://aihero.dev/skills-to-tickets)，它声明那些被当作任务图来读的阻塞边；还有 [code-review](https://aihero.dev/skills-code-review)，它在收尾前对集成分支跑一遍。不确定自己身处哪条流程时，[ask-matt](https://aihero.dev/skills-ask-matt) 是整套技能的路由器。
