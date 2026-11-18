<p>
  <a href="https://www.aihero.dev/s/skills-newsletter">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skills-repo-dark_2x.png">
      <source media="(prefers-color-scheme: light)" srcset="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png">
      <img alt="Skills" src="https://res.cloudinary.com/total-typescript/image/upload/v1777382277/skill-repo-light_2x.png" width="369">
    </picture>
  </a>
</p>

# 给真正工程师的技能

[![skills.sh](https://skills.sh/b/mattpocock/skills)](https://skills.sh/mattpocock/skills)

我每天用来做真正工程的智能体技能，而非 vibe coding。

开发真正的应用很难。GSD、BMAD、Spec-Kit 这类方法试图通过接管整个流程来帮忙。但这样做的代价是拿走了你的控制权，也让流程中的 bug 变得难以解决。

这些技能被设计得小巧、易于改造、可组合。它们在任何模型上都能用。它们基于数十年的工程经验。拿去随意折腾，把它们变成你自己的，好好享受。

如果你想持续跟进这些技能的变更，以及我新创作的技能，可以加入我的 newsletter，和大约 60,000 名开发者在一起：

[订阅 Newsletter](https://www.aihero.dev/s/skills-newsletter)

## 安装（30 秒搞定）

插件会自己更新。[skills.sh](https://skills.sh/mattpocock/skills) 则是把可编辑的文件复制进你的项目，由你手动更新。每个智能体二选一即可，因为两种都装会让每个技能装两遍。

### 1. 获取技能

<details>
<summary><strong>Claude Code</strong></summary>

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

默认自动更新。

</details>

<details>
<summary><strong>Codex</strong></summary>

```bash
codex plugin marketplace add mattpocock/skills
codex plugin add mattpocock-skills@mattpocock
```

启动时自动更新。

</details>

<details>
<summary><strong>GitHub Copilot</strong>（CLI 与 VS Code）</summary>

```bash
copilot plugin marketplace add mattpocock/skills
copilot plugin install mattpocock-skills@mattpocock
```

然后（只需一次），把下面内容加进 `~/.copilot/settings.json`：

```json
{
  "extraKnownMarketplaces": {
    "mattpocock": { "source": { "source": "github", "repo": "mattpocock/skills" }, "autoUpdate": true }
  }
}
```

在 VS Code 中，运行 **Chat: Install Plugin From Source** 并输入 `https://github.com/mattpocock/skills`。它每天更新。

</details>

<details>
<summary><strong>Gemini CLI</strong>（手动更新）</summary>

```bash
gemini skills install https://github.com/mattpocock/skills.git --path skills/engineering
gemini skills install https://github.com/mattpocock/skills.git --path skills/productivity
```

更新时重新运行这两条命令。

</details>

<details>
<summary><strong>任何其他智能体，或可编辑文件</strong>（手动更新）</summary>

```bash
npx skills@latest add mattpocock/skills -a <agent>  # cursor, opencode, devin, windsurf, amp, pi; omit -a to choose
```

当安装器问你要装哪些技能时，把 `setup-matt-pocock-skills` 也选上。更新时运行 `npx skills@latest update`，并重新运行 `add` 来获取新技能。

</details>

### 2. 运行 `/setup-matt-pocock-skills`

在你的智能体中，每个仓库运行一次。它会：

- 询问你想用哪个 issue 跟踪器（GitHub、GitLab、本地文件，或你描述的任何其他方式）
- 询问你在对工单做分诊时会打哪些标签（`/triage` 会用到标签）
- 询问你想把我们创建的任何文档保存到哪里

### 3. 搞定，可以开工了。

## 这些技能为何存在

我构建这些技能，是为了修复我在 Claude Code、Codex 和其他编程智能体身上常见的失败模式。

### #1：智能体做的不是我想要的

> “没有人确切知道自己想要什么”
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**问题**。软件开发中最常见的失败模式是错位。你以为开发者明白你想要什么，等你看到他们做出来的东西，才意识到他们根本没理解你。

AI 时代一模一样。你和智能体之间存在一道沟通鸿沟。解法是一场**拷问环节（grilling session）**：让智能体就你正在构建的东西向你提出细致的问题。

**解法**是使用：

- [`/grill-me`](./skills/productivity/grill-me/SKILL.md)：用于非代码场景
- [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md)：与 [`/grill-me`](./skills/productivity/grill-me/SKILL.md) 相同，但额外加了不少好东西（见下文）

这些是我最受欢迎的技能。它们帮你在动手之前与智能体对齐，并深入思考你要做的变更。_每一次_要做变更时都请使用它们。

### #2：智能体实在太啰嗦

> 有了通用语言，开发者之间的交流与代码的表达全都派生自同一个领域模型。
>
> Eric Evans, [Domain-Driven-Design](https://www.amazon.co.uk/Domain-Driven-Design-Tackling-Complexity-Software/dp/0321125215)

**问题**：在项目初期，开发者和软件的服务对象（领域专家）通常说着不同的语言。

我在自己的智能体身上感受到了同样的张力。智能体通常被空投进一个项目，被要求边干边弄懂行话。于是一个词就够的地方，它们会用上二十个词。

**解法**是建立一门共享语言。它是一份文档，帮助智能体解码项目中使用的行话。

<details>
<summary>
示例
</summary>

这是一个来自我 `course-video-manager` 仓库的[词汇表](https://github.com/mattpocock/course-video-manager/blob/076a5a7a182db0fe1e62971dd7a68bcadf010f1c/CONTEXT.md)示例（在那个固定 commit 里它还叫 `CONTEXT.md`，是这些技能重命名该约定之前的事）。哪种更容易读？

- **之前**：“课程中某个小节里的课时被‘落地’（即在文件系统中得到一个位置）时会出问题”
- **之后**：“物化级联（materialization cascade）出了问题”

这种简洁会在一次又一次会话中持续带来回报。

</details>

这一点已内建于 [`/grill-with-docs`](./skills/engineering/grill-with-docs/SKILL.md)。它是一场拷问环节，但同时帮你与 AI 建立共享语言，并把难以解释的决策记录成 ADR。

很难解释它有多强大。它可能是这个仓库里最酷的一项技术。试试看。

> [!TIP]
> 共享语言除了减少啰嗦之外，还有很多别的好处：
>
> - **变量、函数和文件的命名保持一致**，都使用共享语言
> - 因此，**代码库对智能体更易导航**
> - 智能体还会**在思考上花费更少的 token**，因为它手头有一门更简洁的语言

### #3：代码跑不通

> “永远迈小步、有意图的步子。反馈的速率就是你的速度上限。永远不要接下太大的任务。”
>
> David Thomas & Andrew Hunt, [The Pragmatic Programmer](https://www.amazon.co.uk/Pragmatic-Programmer-Anniversary-Journey-Mastery/dp/B0833F1T3V)

**问题**：假设你和智能体已经就构建什么对齐了。可智能体_仍然_产出一堆垃圾，怎么办？

该审视你的反馈回路了。如果得不到关于它产出的代码实际如何运行的反馈，智能体就是在盲飞。

**解法**：你需要惯常的那一批反馈回路：静态类型、浏览器访问，以及自动化测试。

在自动化测试方面，红-绿-重构（red-green-refactor）循环至关重要：智能体先写一个失败的测试，再修复测试。这能给智能体提供一致水平的反馈，从而产出好得多的代码。

我构建了一个可以插进任何项目的 **[`/tdd`](./skills/engineering/tdd/SKILL.md) 技能**。它推行红-绿-重构，并就什么是好测试、什么是坏测试给智能体大量指导。

在调试方面，我还构建了 **[`/diagnosing-bugs`](./skills/engineering/diagnosing-bugs/SKILL.md)** 技能，把最佳调试实践包进一个按阶段设闸的纪律化循环。

### #4：我们造出了一个大泥球

> “_每天_都在系统的设计上投资。”
>
> Kent Beck, [Extreme Programming Explained](https://www.amazon.co.uk/Extreme-Programming-Explained-Embrace-Change/dp/0321278658)

> “最好的模块是深模块。它们让大量功能可以通过一个简单的接口被访问。”
>
> John Ousterhout, [A Philosophy Of Software Design](https://www.amazon.co.uk/Philosophy-Software-Design-2nd/dp/173210221X)

**问题**：大多数用智能体构建的应用都复杂且难以修改。智能体能极大地加速编码，同样也加速了软件熵增。代码库正以前所未有的速度变得更复杂。

**解法**是一种激进的 AI 编程新思路：关心代码的设计。

这一点内建于这些技能的每一层：

- [`/to-spec`](./skills/engineering/to-spec/SKILL.md)：在创建规格之前，先考你将要触碰哪些模块

而最关键的是，[`/improve-codebase-architecture`](./skills/engineering/improve-codebase-architecture/SKILL.md) 会勘察代码库中可做深化的机会，并把候选项交到你手上。我建议每隔几天在你的代码库上跑一次。它是勘察，不是抢救：在一个真正老旧的代码库上它确实能找到真实的候选项，但它不会替你解开这团泥。

### 小结

软件工程基本功比以往任何时候都更重要。这些技能是我把这些基本功凝练成可复用实践的最佳努力，帮你交付你职业生涯里最好的应用。好好享受。

## 参考

这些技能沿一条轴划分：谁能调用它们。**用户调用（User-invoked）**技能只有在你亲手输入时才能触达（例如 `/grill-me`），它们的职责是编排。**模型调用（Model-invoked）**技能既可以由你调用，_也_可以在任务对口时被智能体自动取用；它们承载可复用的纪律。用户调用的技能可以调用模型调用的技能，但绝不会调用另一个用户调用的技能。

### Engineering（工程类）

我每天用于代码工作的技能。

**用户调用**

- **[ask-matt](./skills/engineering/ask-matt/SKILL.md)**：询问哪个技能或流程适合你的处境。本仓库用户调用技能之上的一个路由器。
- **[grill-with-docs](./skills/engineering/grill-with-docs/SKILL.md)**：拷问环节，同时构建你项目的领域模型：打磨术语，并就地更新 `GLOSSARY.md` 和 ADR。
- **[triage](./skills/engineering/triage/SKILL.md)**：让 issue 穿过一个由分诊角色组成的状态机。
- **[improve-codebase-architecture](./skills/engineering/improve-codebase-architecture/SKILL.md)**：扫描代码库寻找深化机会，以可视化的 HTML 报告呈现，然后就你选中的那一个展开拷问。
- **[setup-matt-pocock-skills](./skills/engineering/setup-matt-pocock-skills/SKILL.md)**：为本仓库的工程技能做配置（issue 跟踪器、分诊标签、领域文档布局）。每个仓库运行一次，先于其他工程技能使用。
- **[to-spec](./skills/engineering/to-spec/SKILL.md)**：把当前对话变成一份规格并发布到 issue 跟踪器。不做访谈，只综合你们已经讨论过的内容。
- **[to-tickets](./skills/engineering/to-tickets/SKILL.md)**：把任何计划、规格或对话拆成一组曳光弹（tracer-bullet）工单，每张工单都声明自己的阻塞边，可以写成本地文件里的文本，也可以做成真实跟踪器上的原生阻塞链接。
- **[implement](./skills/engineering/implement/SKILL.md)**：构建一份规格或一组工单所描述的工作，在事先约定的接缝处驱动 `/tdd`，提交前以 `/code-review` 收尾。
- **[implement-spec](./skills/engineering/implement-spec/SKILL.md)**：在一条集成分支上实现整份规格。把工单当作任务图来推进，在就绪的前沿上运行多个实现子智能体以获得最大并发，最后以 `/code-review` 收尾。
- **[wayfinder](./skills/engineering/wayfinder/SKILL.md)**：为一大块超过单个智能体会话所能承载的工作做规划：在 issue 跟踪器上建立一张由决策工单构成的共享地图，然后逐个解决它们，直到通往目的地的路径清晰。
- **[retro](./skills/engineering/retro/SKILL.md)**：在一次会话结束后，对编程智能体的环境提出改进建议（导航、自动化检查、编码规范、引导文件、工具链），最严重的排前面。

**模型调用**

- **[prototype](./skills/engineering/prototype/SKILL.md)**：构建一个用完即弃的原型来回答某个设计问题：状态/逻辑类问题给单个可分享的 HTML 文件，UI 类问题给几个可从同一路由切换的、截然不同的变体。
- **[diagnosing-bugs](./skills/engineering/diagnosing-bugs/SKILL.md)**：针对疑难 bug 和性能回归的纪律化诊断循环：构建一个在该 bug 上变红的反馈回路 → 最小化 → 提出假设 → 埋点 → 修复 → 回归测试。
- **[research](./skills/engineering/research/SKILL.md)**：对照高可信度的一手来源调查某个问题，并把调查结果以带引用的 Markdown 文件沉淀在仓库里，以后台智能体方式运行。
- **[tdd](./skills/engineering/tdd/SKILL.md)**：以红-绿-重构循环进行的测试驱动开发。一次一个垂直切片地构建功能或修复 bug。
- **[domain-modeling](./skills/engineering/domain-modeling/SKILL.md)**：主动构建并打磨项目的领域模型：拿词汇表质询术语，用边界场景做压力测试，并就地更新 `GLOSSARY.md` 和 ADR。
- **[codebase-design](./skills/engineering/codebase-design/SKILL.md)**：用于设计深模块的共享纪律与词汇：大量行为藏在一个小接口后面，放在干净的接缝处，可通过该接口测试。
- **[code-review](./skills/engineering/code-review/SKILL.md)**：对自某个固定点以来的 diff 做双轴评审：**标准（Standards）**（是否遵循仓库的编码规范，外加一条 Fowler 坏味道基线？）与**规格（Spec）**（是否忠实实现了其来源的 issue/规格？），以并行子智能体运行，互不污染。
- **[pr](./skills/engineering/pr/SKILL.md)**：PR 描述应有的形状：一份以最小可视形式讲清变更的摘要、证明它可用的前后对比证据，以及一次合并风险判断（单向门还是双向门，加上影响半径）。
- **[wizard](./skills/engineering/wizard/SKILL.md)**：生成一个交互式 bash 向导，带人类一步步完成只有他们能做的步骤：配置基础设施、设置凭据或 CI 密钥、摸清一个陌生的第三方控制台，或执行一次性的迁移或切换。

### Productivity（效率类）

通用工作流工具，不针对代码。

**用户调用**

- **[grill-me](./skills/productivity/grill-me/SKILL.md)**：就一个计划或设计接受毫不留情的访谈，直到设计树的每一个分支都被解决。
- **[handoff](./skills/productivity/handoff/SKILL.md)**：把当前对话压缩成一份交接文档，让另一个智能体可以继续这项工作。
- **[teach](./skills/productivity/teach/SKILL.md)**：跨多次会话教会用户一项新技能或概念，把当前目录当作有状态的授课工作区。
- **[to-questionnaire](./skills/productivity/to-questionnaire/SKILL.md)**：把一个你独自无法回答的决策，变成一份给那位能回答的人的 Markdown 问卷，可以异步填写，也可以在会议上一起填。它拷问你的是这次发送（发给谁、需要收回什么），而不是主题本身。
- **[wait-what](./skills/productivity/wait-what/SKILL.md)**：在消息没看懂的那一刻触发它。智能体会用你缺失的上下文、以大白话重新讲一遍，并使用你 `GLOSSARY.md` 里的词汇。

**模型调用**

- **[grilling](./skills/productivity/grilling/SKILL.md)**：就一个计划、决策或想法对用户进行毫不留情的访谈，直到设计树的每一个分支都被解决。它是 `grill-me`、`grill-with-docs`、`triage`、`wayfinder` 和 `improve-codebase-architecture` 背后可复用的访谈原语。
- **[writing-for-agents](./skills/productivity/writing-for-agents/SKILL.md)**：为智能体写文档：技能、AGENTS.md/CLAUDE.md，以及任何智能体通过指针触达的文档。
