> **已归档。** 该技能已于 v1.3.0 从插件中移除，不再维护。没有任何替代品：智能体不需要专门的技能就能处理 merge 或 rebase 冲突。本页面保留仅作参考。

## 它做什么

`resolving-merge-conflicts` 会逐个 hunk 地处理一个进行中的 git merge 或 rebase，然后运行项目自己的检查，并以一次 commit 为这个操作收尾。

它拒绝把冲突当成一个文本问题。在碰任何一个 hunk 之前，它先把每一侧回溯到它的**[一手来源（primary source）](https://www.aihero.dev/ai-coding-dictionary/primary-source)**（commit message、PR、原始 issue），这样它是在两个意图之间做选择，而不是在两块文本之间做选择；只要两侧兼容，它就把两者都保留。在不兼容的地方，它选择与 merge 声明的目标相符的那一侧，并点明代价。它不会发明新行为来掩盖冲突，也从不使用 `--abort`。它总是把 merge 带到一次完成的 commit 为止。

## 何时该用它

输入 `/resolving-merge-conflicts`，或者当任务合适时，[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)自动取用它。

当 git 已经停在它自己解决不了的冲突上时，找它。它的范围只限于眼前这个冲突，不包括冲突两侧的任何东西：

| 你的处境 | 技能 |
| --- | --- |
| merge 或 rebase 进行到一半，树里有冲突标记 | 这一个 |
| merge 已完成，某个东西出于你看不见的原因开始行为异常 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |
| 在规划怎么切分工作，好让分支少打架 | 都不是：见下面关于并行工作的问题 |

## 一手来源优先于 `ours` 和 `theirs`

这个技能的存在就是为了阻止靠标志解决冲突：`--ours`、`--theirs`，或者手删看起来不太重要的那块，好让标记消失、构建通过。这样的解决方案在语法上可以完美无缺，却仍然悄悄丢掉某人故意做出的变更。

你没有读过的意图，就无法保留。所以工作从历史（commit、PR、[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)）开始，然后才走到 diff。检查这一步的存在出于同一个理由。技能会找到仓库自己的[自动化检查](https://www.aihero.dev/ai-coding-dictionary/automated-check)并在提交前运行它们，因为 merge 是 git 里最容易产出“同时满足两个分支、却过不了任何一方测试”的代码的地方。

## 常见问题

**Claude Code 自己已经能把冲突解决得挺好，为什么还需要一个技能？**

附加价值在“找一手来源”和“跑反馈回路”这两步，否则你每次都得手动提示。没有提示的智能体通常只凭 diff 就给出一个貌似合理的解决方案，然后就此打住。这个技能的价值在于它不许智能体跳过的那两步：读懂每一侧为什么存在，以及事后运行检查。相对一个好的[模型](https://www.aihero.dev/ai-coding-dictionary/model)，这只是个小增益，而这正是有意为之。至少有一位读者预测，随着模型进步，这整个技能会变成一个空操作（no-op）。

**为了避免冲突，我是否应该一开始就让并行智能体避开同一批文件？**

多半不必。在并行任务之间给文件划界，代价大于收益，因为智能体处理 merge 冲突已经足够好，这笔交易没有看起来那么亏。唯一值得保持的纪律是先做大重构。一个大改名在十条分支已经从它分叉出去之后才落地，才是那个始终昂贵的情况。

一条来自用户报告的关于并行 worktree 的注意事项：当兄弟[会话](https://www.aihero.dev/ai-coding-dictionary/session)各自在自己的树里构建一张工单时，合并回去的工作最好由写下变更的那个会话来做，因为它才是已经知道意图的那一方。如果最后由一个智能体统一解决所有人的冲突，它就丢掉了本技能第 2 步必须重建的[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。

**为什么从不用 `--abort`？**

中止会扔掉已做的解决工作，让你下次尝试时回到一模一样、毫无变化的冲突面前。这个技能是为“merge 注定要发生”的情况写的。如果你已经决定它不该发生，那是在调用之前就要做的决定，不是循环里的一个分支。

## 有这些表现就说明它在生效

- 智能体在解决冲突时会向你引用 commit message、PR 或 issue，而不只是 diff hunk。
- 每个 hunk 的结局都是保留两侧的行为，或者有一条明确的备注，写清丢了什么、为什么。
- 结果里没有出现任何一侧分支上都不存在的东西。
- 智能体找到 typecheck、测试和格式化，并在提交*之前*把它们跑绿，而不是等你发现坏掉之后。
- 你最终得到一棵干净的树，操作完整收尾，包括多 commit rebase 里剩下的每一个 commit。

## 它的位置

`resolving-merge-conflicts` 是一个**随取随用的独立件**，不依赖任何其他技能：git 卡住时开始，树干净并已提交时结束。它唯一真正的邻居是 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)，后者在“merge 干净地解决了、但合并后的代码行为异常”那个点上接手：那是诊断问题，不是冲突问题。它位于“从想法到交付”的主流程之外，所以它之前和之后该跑什么，地图在 [ask-matt](https://aihero.dev/skills-ask-matt)。
