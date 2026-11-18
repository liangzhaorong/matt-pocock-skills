## 它做什么

`implement` 构建已经定下来的工作。你把它指向一张[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)、一份[规格](https://www.aihero.dev/ai-coding-dictionary/spec)，或你刚在对话里谈定的计划，它写代码、在接缝上驱动 [tdd](https://aihero.dev/skills-tdd)、边做边跑类型检查、最后跑 [code-review](https://aihero.dev/skills-code-review)，并提交到当前分支。

它从不重开计划。没有访谈、没有澄清轮、不会提议另一种做法。上游定下的一切就是输入，这个技能的全部工作就是把它变成一次提交。这正是它与对一个全新[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)说“构建这个”的区别：后者常常一边建一边重新设计这份工作。

## 何时该用它

你自己输入 `/implement` 来调用它，智能体不会自己主动取用。它带着 `disable-model-invocation: true` 发布，所以别的技能也调不动它。凡是 [ask-matt](https://aihero.dev/skills-ask-matt) 或 [to-tickets](https://aihero.dev/skills-to-tickets) 说“然后逐张工单 `/implement`”的地方，那都是给你的指示，不是智能体会自己做的事。

工作眼下住在哪，决定这是不是对的技能：

| 工作是…… | 用哪个 |
| --- | --- |
| 跟踪器上的一张工单 | `/implement #42`，每张工单一次[会话](https://www.aihero.dev/ai-coding-dictionary/session)，工单之间 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 上下文 |
| 一份规格，还没拆，且构建跨会话 | 先 [to-tickets](https://aihero.dev/skills-to-tickets)，再逐张工单 `/implement` |
| 一份规格，且构建不大 | 直接对着规格 `/implement` |
| 只在你刚进行的那场对话里，且仍然不大 | 就在那儿 `/implement`，同一个窗口 |
| 还没写在任何地方 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs)，或没有代码库时用 [grill-me](https://aihero.dev/skills-grill-me) |
| 一个你想测试先行、没有规格的具体行为 | 直接 [tdd](https://aihero.dev/skills-tdd) |
| 已经建好，你想检查它 | 直接 [code-review](https://aihero.dev/skills-code-review) |

同会话这种情况值得点名，因为技能自己的第一行没覆盖它。`SKILL.md` 说“规格或工单”，这会推着[模型](https://www.aihero.dev/ai-coding-dictionary/model)去找一个不存在的文件。如果计划只住在对话线程里，调用时说清楚。

## 前置条件

`implement` 提交到你所在的分支。它不创建分支，也不问。开工前确认你在想让这份工作落上去的那条分支上。

如果工单来自 [to-tickets](https://aihero.dev/skills-to-tickets)，那么 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 已经配好它们所在的跟踪器。`code-review` 读同一份配置，在收尾时找到原始规格。

## 一次运行做什么

一次运行有五步，按顺序：

1. 读工单或规格，理出接缝。
2. 在预先谈定的接缝上驱动 [tdd](https://aihero.dev/skills-tdd)，一次一个红绿切片。
3. 勤跑类型检查，随手跑单个测试文件。
4. 最后把整套测试完整跑一次。
5. 跑 [code-review](https://aihero.dev/skills-code-review)，然后提交到当前分支。

一次运行管一张工单。[to-tickets](https://aihero.dev/skills-to-tickets) 产出的工单是按“装得进一个全新[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)”裁过尺寸的曳光弹垂直切片，所以预期的节奏是：清上下文，实现一张工单，提交，再清一次。每张工单自包含，所以你可以丢掉上一张工单的上下文。

## 预先谈定的接缝

这个技能的中心概念是**接缝（seam）**，你在不伸手进内部的情况下观察行为的公共边界。测试住在接缝上。接缝在任何代码存在之前谈定，测试就活得下来，你可以在底下重写实现而不动它们。

“预先谈定”这一半要紧，也是这个技能最弱的一点。`implement` 内部没有任何东西负责谈定接缝。`tdd` 才是发问的技能，而且它拒绝在未确认的接缝上写测试。所以实践中，谈定要么发生在上游的规格里，要么发生在运行的第一轮交换里。如果哪儿都没发生，这次运行就退化成“直接写代码”，而且没有东西警告你。在规格里点名接缝，挡住的就是这个。

## 常见问题

**它跑完了，可我的工单还开着，验收标准也没勾。**

对，而且符合预期。`implement` 没有完成这一步。它到提交为止，从不碰工作项。GitHub Issues 和本地 markdown 跟踪器上都一样，所以这不是跟踪器集成问题。它也不处理 `code-review` 产出的发现，也不去勾原始 issue 上的 `- [ ]` 复选框。工单的关闭和标准的对账由你自己做。这在依赖链上最要紧，因为 `to-tickets` 把前沿定义为“阻塞者都已关闭”的工单。什么都没关，就什么都不会看起来解除阻塞。

**我能把所有工单一次喂给它，或者并行跑几个吗？**

`/implement` 不行：一次调用，一张工单。想在一次运行里吃下整份规格，用 [implement-spec](https://aihero.dev/skills-implement-spec)，它把就绪前沿上的每张工单交给一个在自己的 worktree 里的[子智能体](https://www.aihero.dev/ai-coding-dictionary/subagent)，再把结果合到一条集成分支上。在同一个检出里并排跑多个 `/implement` 会话，比“不支持”更糟。一份现场报告描述：一个会话里的 `git commit --amend` 落到另一个会话的 commit 上、一个 stash 从 `refs/stash` 里消失、commit 落上错误的分支，全部发生在同一个下午、横跨三个 issue。这些会话共享同一个工作目录、同一个索引、同一个 HEAD。用户用 git worktree 绕，但 `refs/stash` 也跨 worktree 共享，所以光靠 worktree 修不掉 stash 那种情况。

**它能开一个 pull request 而不是提交吗？**

没有内置。它直接提交到当前分支。好几个人觉得这太心急，因为代码在他们验证能用之前就落了地。没有配置开关，也没有 PR 模式。人们在调用时覆盖它（“提交到一个分支并开一个 PR”），或改技能的本地副本。当智能体真的写 PR 时，[pr](https://aihero.dev/skills-pr) 负责正文的形状。

**`code-review` 说它看不到我的改动。**

`code-review` 评审的是 `git diff <fixed-point>...HEAD`，不含已暂存和工作区的改动。`implement` 在提交之前跑它，所以除非已有一个中间 commit，否则那个 diff 里没有东西可评审。多个人报告过这件事，两边都没修。先提交，再对着你分叉的那个点评审。

另外，有些人根本不想要评审在运行里面，因为一个评审自己刚写的代码的智能体偏向它自己的解法。在一个全新会话里对着固定点跑 [code-review](https://aihero.dev/skills-code-review) 是正当的替代。同一种偏向也是那个技能把两根轴放在分开的子智能体里跑的原因。

**一张工单烧了 15 万 token。我用错了吗？**

多半没有。更可能的是工单太大。一次运行要做代码库勘察、每条接缝一轮红绿、一整套测试、一次评审，所以一张不小的工单超过 10 万 [token](https://www.aihero.dev/ai-coding-dictionary/token) 是常态，不是出问题的信号。修法在上游。在 [to-tickets](https://aihero.dev/skills-to-tickets) 里把工单裁好尺寸，让每张装得进一个全新窗口。如果单张工单老是超，拆它，而不是调高[努力级别](https://www.aihero.dev/ai-coding-dictionary/effort)。

**在全新会话里 `/implement #2`，却做了件完全不相干的事。**

智能体把 `#2` 解析到了上下文里另一份带编号的清单，比如一个 todo 文件或检查单，而不是配置好的跟踪器。`implement` 现在会从 issue 跟踪器取回传入的引用，开工前陈述它的标题，引用有歧义时会发问。核对那个标题是不是你要的那张工单；传 issue URL 或 `owner/repo#2` 可以彻底消除歧义。

## 有这些表现就说明它在生效

- 会话开场是读工单或规格并复述它要构建什么，而不是问你要构建什么。
- 你能在 trace 里看到货真价实的 `tdd` 技能工具调用，而不只是 diff 里冒出的测试。
- 类型检查和单个测试文件在运行中反复跑，整套测试在接近尾声时完整跑一次。
- 不需要你催它继续，运行自己走到当前分支上的一次提交。
- diff 的量是一张工单的：一根穿透每一层的垂直切片，不是几张工单扫在一起。

## 它的位置

`implement` 是主链条的构建步骤：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

它的邻居是 [to-tickets](https://aihero.dev/skills-to-tickets)，产出它消费的工单并声明决定顺序的阻塞边；[tdd](https://aihero.dev/skills-tdd)，它在每条接缝上于内部驱动；以及 [code-review](https://aihero.dev/skills-code-review)，它在提交前跑。它坐在规划技能的下游，并且信任它们。它不重新验证递到手里的东西的形状，所以一张结构糟糕的地图或一张按层横切的工单会照写好的样子被建出来。

正是这份信任，[wayfinder](https://aihero.dev/skills-wayfinder) 才在 [to-spec](https://aihero.dev/skills-to-spec) 处并入链条，而不是把地图直接循环进 `implement`。只有当工作量最后发现很小，才从地图直奔 `implement`。

不确定自己身处哪条流程时，[ask-matt](https://aihero.dev/skills-ask-matt) 是整套技能的路由器。
