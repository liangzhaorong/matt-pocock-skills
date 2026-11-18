## 它做什么

`code-review` 沿两根轴评审 `HEAD` 与你点名的固定点（一个 commit、一条分支、一个 tag、`main`、`HEAD~5`）之间的 diff。**标准（Standards）**问代码是否遵循这个仓库写代码的方式。**规格（Spec）**问代码是否做到了原始 issue 或[规格](https://www.aihero.dev/ai-coding-dictionary/spec)所要求的。每根轴跑在自己的[子智能体](https://www.aihero.dev/ai-coding-dictionary/subagent)里，谁也看不见谁的推理。

这个技能从不把两根轴合并或重新排序。报告以*每根轴*各自的最严重问题收尾，拒绝跨轴点名唯一赢家。一个变更可以过一根轴、挂另一根。遵循每条约定却实现了错误东西的代码过标准、挂规格。精确做到[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)所要求却破坏仓库约定的代码恰好相反。一个混合的裁决会让通过的那根轴把挂掉的那根藏起来。

## 何时该用它

输入 `/code-review`，或者当你要求评审一条分支、一个 PR、进行中的工作或任何“自 X 以来”的东西时，智能体自动取用它。

| 你的处境 | 用哪个 |
| --- | --- |
| 已经有一个 diff，你想知道它*既*建得对、*又*是对的东西 | `code-review` |
| 你想在 diff 里猎 bug：空指针路径、竞态、差一错误 | Claude Code 自带的内置评审，不是这一个（见下面的重名问题） |
| 什么都还没写，你想测试先行地写 | [tdd](https://aihero.dev/skills-tdd) |
| 一整份规格需要构建，评审也在内 | [implement](https://aihero.dev/skills-implement)，它自己会调用这个技能 |
| 整个代码库都漂了，不止一个 diff | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) |
| 有东西坏了，你不知道为什么 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |

固定点必须由你提供。你不给，技能就问，而不是猜。在孵化任何东西之前，它会检查 ref 能否解析、diff 是否为空，所以一个打错的分支名当着你的面失败，而不是在两个子智能体内部失败。

## 前置条件

标准轴不需要什么。仓库记了什么就读什么（`CODING_STANDARDS.md`、`CONTRIBUTING.md` 之类），仓库什么都没记时退回内置基线。

规格轴需要一份规格存在且找得到。它按这个顺序找：

1. commit message 里的 issue 引用（`#123`、`Closes #45`、GitLab 的 `!67`），通过跟踪器文档取回。
2. 你当参数传进来的路径。
3. `docs/`、`specs/` 或 `.scratch/` 下与分支或特性名匹配的规格文件。
4. 问你。

第 1 步依赖跟踪器文档，它由 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 写下。没有它，只要你递给这根轴一个路径，它仍然工作。完全没有规格时，技能跳过规格子智能体，报告写“无可用规格”，而不是发明需求。

## 两根轴

| | 标准（Standards） | 规格（Spec） |
| --- | --- | --- |
| 问题 | 它建得对吗？ | 它是对的东西吗？ |
| 读什么 | 仓库写明的标准，加上坏味道基线 | 原始 issue 或规格 |
| 报什么 | 有据可查的违规（可以是硬性的），以及坏味道（永远是主观判断） | 缺失或不全的需求、范围蔓延、实现错了的需求 |
| 每条发现都引用 | 标准文件和规则，或点名坏味道加 hunk 原文 | 规格的那一行 |

这样设计是为了避免一个不了解你标准的泛型评审技能。那样的技能会把你代码库里故意为之的东西标成问题，又漏掉你代码库依赖的不变量。所以仓库自己的文档是标准轴上的[一手来源](https://www.aihero.dev/ai-coding-dictionary/primary-source)，而且**仓库永远压过一切**。

**坏味道基线**坐在仓库的标准之下。它是 Fowler《重构》第 3 章的十二种代码坏味道：诡异命名（Mysterious Name）、重复代码（Duplicated Code）、依恋情结（Feature Envy）、数据泥团（Data Clumps）、基本类型偏执（Primitive Obsession）、重复的 switch（Repeated Switches）、霰弹式修改（Shotgun Surgery）、发散式变化（Divergent Change）、夸夸其谈的泛化（Speculative Generality）、消息链（Message Chains）、中间人（Middle Man）、被拒绝的遗赠（Refused Bequest）。每一个都是一条带标签的启发式（“可能是依恋情结”），从来不是硬性违规。每一条都写明坏味道是什么、怎么修，所以一条发现自带修法，而不只是一句抱怨。两根轴都跳过你的 linter 已经强制的东西。

## 常见问题

**它和 Claude Code 自带的 `/code-review` 撞名，我该怎么办？**

这是这个技能被报告得最多的问题，而且没有修复。Claude Code 自带一个 `/code-review`，干的却是另一件事：它在 diff 里猎 bug，而这一个检查规格符合度和仓库标准。装下这个库之后，两者只有一个能赢，哪个赢取决于你怎么装的：

- **插件市场。**每个技能都带上 `mattpocock-skills:` 前缀，内置的那个就很难再用不带前缀的名字够到。
- **纯 skills 安装。**本地文件赢，这个技能遮住内置的。

一个答案是干脆把 Claude Code 的内置技能整个移除。那省下大量[上下文](https://www.aihero.dev/ai-coding-dictionary/context)，撞名也不再要紧。遮蔽本身说得上是 Claude Code [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 的一个 bug（技能作者理应可以给技能起任何名字），所以另一个答案是给本地副本改名。`npx skills update` 会撤销对 frontmatter 的编辑或对目录的改名。用户报告的耐久绕法是把这个技能 fork 成一个新名字，并从受管集合里去掉 `code-review`。记下你 fork 自哪个 commit，这样还能手工再同步。

**它的子智能体不断再次调用 `/code-review`，孵出更多智能体。**

这是一个已知的未修复 bug。好几个人在不止一个 harness 里复现过。标准和规格的提示词都没有禁止转委托，于是子智能体能再次找到这个技能并再次扇出。一份报告达到了 50 多个智能体。人们在 fork 上用的修法是往两份子智能体简报里各追加一行：“不要调用 `/code-review`，也不要孵化更多智能体：直接执行本次评审。”有人更喜欢在 harness 层处理，让每个技能都继承这道护栏。两者都还没进已发布的技能。如果你无人值守地跑它，盯住智能体数量。

**我该在写代码的同一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)里跑它吗？**

最好换一个新的。正如一位读者所说：“同一个上下文评审自己不是评审，是带着斜杠命令的确认偏误。”在写作会话里评审的智能体，上下文里装着塑造这份代码的每一个假设。一个独立的评审者不会有这份上下文。这也是人们要求 [implement](https://aihero.dev/skills-implement) 去掉内置评审步骤的原因：那一步在刚写完 diff 的会话内部跑评审。独立的版本是你自己从一个干净会话调用 `/code-review`。

**每张工单之后跑，还是最后跑一次？**

都行，技能不替你决定。逐单跑让每个 diff 小到规格轴有一份清晰的规格可对照，这是 `implement` 用的模式。攒到分支末尾一次跑，能抓到逐单跑各自漏掉的工单之间的相互作用。拿不准的话，逐单评审，再对着分支点跑最后一遍。

**这些发现能信吗？**

不查就不能信。子智能体的输出是假设，不是证据。一个团队报告过十二条散文式评审漏掉的破坏性变更。技能把两份报告原样或轻度清理后合并。它不逐条对照文件重新核实，所以一条发现可能引错位置或夸大影响。行动之前读每条发现上的引用。技能要求每条发现都带引用（一条标准规则、一个坏味道加它的 hunk，或规格的一行），而正是这个让发现变得可查。

**为什么我每跑一次它都找出新问题？**

每次修复都会新增待评审的代码，而标准轴里主观判断的那一半每次跑结果都不同。一位读者这样描述这个循环：“/code-review 和 /improve-code-architecture 每次总能找到新东西。我实现修复，重跑这些技能，一次又一次。”没有收敛保证。把一轮评审当作一份线索清单。处理那些背后有被引用规则撑着的，然后停。不要循环跑到它干净为止，因为它永远不会。

**它评审我未提交的工作吗？**

不。它 diff 的是 `<fixed-point>...HEAD`。三点形式从 merge-base 起算，不含已暂存和工作区的改动。如果 `implement` 还没做中间 commit，评审就看不见即将进入下一个 commit 的那份工作。先提交，再评审，然后 amend 或补一个 fixup。

## 有这些表现就说明它在生效

- 在孵化任何子智能体之前，它拒绝在一个坏 ref 或空 diff 上启动。
- 报告以两个分开的块到达，标题是 `## Standards` 和 `## Spec`，不是一份合并清单。
- 每条标准发现要么点名你仓库某个文件里的规则、要么点名十二种坏味道之一并引用 hunk 原文；每条规格发现都引用规格的一行。
- 收尾摘要给出每根轴各自的最严重问题，拒绝选出一个总赢家。
- 没有规格可用时，规格块如实说明，而不是列出它从代码里推断出的需求。

## 它的位置

`code-review` 是构建链条近尾部的评审步骤：`grill-with-docs → to-spec → to-tickets → implement → code-review → retro`。它也独立站在你指向的任何分支或 PR 上。

- [implement](https://aihero.dev/skills-implement) 是最近的邻居。它驱动构建，并在提交前调用这个技能作为自己的收尾评审。[implement-spec](https://aihero.dev/skills-implement-spec) 在整条集成分支上同样做一次。
- [retro](https://aihero.dev/skills-retro) 在它之后并调校它。当一次会话显示评审漏掉某一类错误时，`retro` 提议标准轴随后会读到的检查或 `CODING_STANDARDS.md` 规则。
- 评审过的工作要上传时，[pr](https://aihero.dev/skills-pr) 写 pull request 正文。
- [to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets) 产出规格轴对照的那份文档，所以一份含糊的规格让那根轴含糊。
- [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是全代码库的对应物，因为这个技能只看一个 diff。

拿不准处境要哪个技能时，[ask-matt](https://aihero.dev/skills-ask-matt) 带你在整套技能之间路由。
