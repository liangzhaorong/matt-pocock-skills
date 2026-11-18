## 它做什么

`improve-codebase-architecture` 勘察一个代码库，寻找**可深化之处（deepening opportunity）**。这些是一个浅模块（接口几乎和它藏住的东西一样复杂）可以变深的地方。它把它们写成一份自包含的 HTML 报告，然后就你挑中的那一个[拷问](https://www.aihero.dev/ai-coding-dictionary/grilling)你。

它从不改代码。整次运行产出一场对话，加你操作系统临时目录里的一个 HTML 文件。重构你之后另开一次[会话](https://www.aihero.dev/ai-coding-dictionary/session)、走正常构建流程去做。这使它是一次勘察，不是重构工具，所以你可以对还没准备好动手的代码库跑它。

两道过滤器挡住报告变成泛泛的清理建议。第一，每个候选必须过**删除测试**：如果移掉这个模块，它的复杂度会挪到一个更小的接口背后，还是摊到调用方身上？只有第一种情况拿到卡片。第二，除非你把它指向一个具体区域，它会先读近期 commit 历史，把扫描聚焦在常改的路径上。在没人碰的代码里做深化，是一次永远收不回成本的重构。

## 何时该用它

你通过输入 `/improve-codebase-architecture` 来调用它；[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)不会自己主动取用它。

它不是主构建循环里的一步。你定期跑它，好排出更多改善代码库的工作。人们在四种情况下用它：

| 处境 | 怎么用 |
| --- | --- |
| 例行保养 | 每几天跑一次，或有空闲时跑，让结构不在特性之间烂掉。 |
| 大构建之前 | 把它指向[规格](https://www.aihero.dev/ai-coding-dictionary/spec)，问“怎么让这次变更变容易？”这是对它最有效的提示词。 |
| 存量审计 | 对一个庞大的、无结构的或[氛围编程（vibe-coded）](https://www.aihero.dev/ai-coding-dictionary/vibe-coding)出来的仓库跑它，弄清它现在什么体形。 |
| 遗留测试工作 | 在对着不可测代码写测试之前，用它找出缺失的接缝。 |

容易与同类弄混的地方：

- 要设计一个你已经选定的模块，用 [codebase-design](https://aihero.dev/skills-codebase-design)。这个技能找出要动的模块，`codebase-design` 是你设计它的地方。
- 一整件大到单次会话装不下的工作，用 [wayfinder](https://aihero.dev/skills-wayfinder)。
- “这个具体的东西坏了”，用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)。当真正的发现是“没有好接缝能把 bug 锁住”时，它会把你送回这里。

## 前置条件

跑它不需要什么。如果 `GLOSSARY.md` 或 `docs/adr/` 里的 ADR 存在，它读它们，用你自己领域的名词。于是一个候选读起来是“深化订单接入模块”，而不是“重构 FooBarHandler”。

它往两个地方写。报告进 `<tmpdir>/architecture-review-<timestamp>.html`，在仓库之外。拷问循环期间，它在 `GLOSSARY.md` 里增补或打磨术语，文件不存在就创建它。它还会提议把一个被拒绝的候选记成 ADR，这样未来某次运行不会再提它。

## 深度，和猎取深度的报告

这个技能立在一个概念上：**深度（depth）**。一个深模块把大量行为放在一个小而稳的接口背后。一个浅模块用一个几乎和底下代码一样宽的接口暴露自己的实现。报告找三种形式的浅：

- 仅为可测试性抽出的纯函数，而真正的 bug 在调用方怎么用它们（没有**局部性（locality）**）。
- 越过自己的**接缝（seam）**泄漏的模块。
- 一个不开五个文件就无法理解的概念。

对每一种，它给出能修好它的深化方案。

每个候选是一张卡片：涉及的文件、摩擦点、一个平实的解法、以**局部性**和**杠杆（leverage）**计的收益、一张前后对比图，以及一个力度徽章。

| 徽章 | 对你意味着什么 |
| --- | --- |
| `Strong` | 删除测试清晰通过，摩擦是真的。认真对待这些。 |
| `Worth exploring` | 说得通的深化，但回报取决于代码接下来往哪走。 |
| `Speculative` | 为完整性而收录。这些大多数可以忽略。 |

报告以一条**首要推荐（Top recommendation）**收尾，即它会先做的那一个候选。然后技能停下，问你想探索哪个候选。在那一刻你什么都没决定，也没有一行代码变过。

## 你挑了一个之后发生什么

你挑中一个候选后，一场[拷问](https://aihero.dev/skills-grilling)会话在它身上开始。它覆盖约束、接缝背后放什么、哪些测试活下来、深化后的接口该长什么样。那场会话的产出是一个决定，不是一份 diff。从那里起走正常流程：把决定带进 [to-spec](https://aihero.dev/skills-to-spec)，然后 [to-tickets](https://aihero.dev/skills-to-tickets)，然后 [implement](https://aihero.dev/skills-implement)。

## 常见问题

**它就一个点子拷问了我一个小时，而不是给我看选项。能关掉吗？**

能。调用时说明（“别拷问我，只给报告”）。这是对这个技能最常见的抱怨。一位用户本来喜欢它，当作“拿到一份彻底的改进分析的便利途径”，但拷问循环加上之后，觉得它“接近不可用”。在他们的会话里，它提出单一方案，然后问“几十上百个问题”。设计意图是报告在前，拷问只在你选中的候选上开始。但较弱的[模型](https://www.aihero.dev/ai-coding-dictionary/model)会直接跳到对它第一个点子的访谈。那条讨论串里的结果因模型差异很大。这是一个未决 issue，技能也还没有写进文档的免拷问模式。

**报告打开是无样式的原始 HTML，没有图。怎么回事？**

报告从 CDN 加载 Tailwind 和 Mermaid，所以打开它需要网络。有东西挡住那些脚本时，页面无提示地坏掉。在被报告的案例里，一个安全钩子要求 SRI 哈希。智能体加上了，但 CDN 发给浏览器的字节与发给算哈希的 `curl` 的不同，浏览器于是拦下了脚本。离线和上锁环境有同样的问题。智能体看不见它，因为它从不渲染页面。绕法是改要内联 CSS 和手搓的 SVG 图，不要 CDN 那套。这是一个未决 issue。

**它给了我十二个候选。我在同一个会话里做完，还是新开一个？**

一个会话一个候选。如果一场对话里做几个，[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window)会同时塞满报告、拷问、领域模型的编辑和代码改动。报告只是个临时文件，所以往前带的是候选，不是文件。挑一个，拷问它，把决定带进 `/to-spec`。把其余的变成[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)，之后可以分开捡起。把选中的改进放进规格，而不是直奔实现。这问题常被问起，而技能自己没有为它记录任何流程。

**我该怎么提示它？**

用你接下来要构建的东西提示它。如果一场大构建临近，把它指向规格，问“怎么让这次变更变容易？”没有提示词的一次运行会自己找热点。例行保养这样没问题，但一个方向让报告可付诸行动。

**它在大型的遗留代码库上能用吗？**

部分能。它在缺乏一致结构的大型存量代码库上工作得不错，也是任何一次式结构搭建之后被推荐的保养工具。但项目已经失控的用户报告说它“帮了点忙，但似乎还是不够”。一位守着八年遗留代码库的开发者报告说模型在原地转圈，而同一个技能在整洁的仓库上画出干净的图。那种情况目前还没有专门的 `/refactor` 技能。如果代码库没有共享词汇，先跑 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 建一套。那通常能让这个技能的产出好很多。

**这和 `/codebase-design` 有什么区别？**

`/codebase-design` 是一份参考，不是会话驱动。它供应词汇（模块、接口、深度、接缝、适配器、杠杆、局部性），这个技能用它。如果你把 `/codebase-design` 当任务交给一个全新智能体，它会以一种已知的方式失败。它没有自己的过程可循，于是智能体发明一个、重新勘察代码、跑很久才问你点什么。跑这个技能，让它去用那一个。

**它会不会告诉我代码库挺好的？**

很少，开工前先知道这一点。这个技能为产出发现而存在，所以它倾向于产出候选，而不是得出“没什么问题”的结论。用力度徽章来校正这一点。如果一份报告里每个候选都是 `Speculative`，技能什么也没找到。

**它在 Codex 或别的 harness 里能用吗？**

部分能。勘察步骤直接点名 Claude Code 的 `Agent` 工具并带 `subagent_type=Explore`。没有那个工具的 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 可能直接跳过并行勘察，而不是用自己的等价物。技能仍然跑，但扫描不那么彻底。有人提议过一个 harness 中立的重写，但未合并。

**在 TypeScript 里我到底怎么落地深模块？**

技能没随附好答案。人们常要一份 `TYPESCRIPT.md`，写明这些原则对应的具体文件和模块布局，而它不存在。技能告诉你深化放在哪、接缝背后放什么。把它变成包或目录结构是你自己的活。

## 有这些表现就说明它在生效

- 候选点名你领域的概念，不是编造的类名：“订单接入模块”，而不是“FooBarHandler”。
- 候选落在你最近编辑过的文件里，不是仓库里没人碰的角落。
- 运行期间没有代码变过。唯一的新文件是你临时目录里的 HTML 报告。
- 报告之后它停下，问你想要哪个候选。它不自己继续。
- 每张卡片把收益讲成局部性或杠杆，并说出哪些测试变简单，而不只是一句“这样更干净”。
- 当你以一个耐久的理由拒绝某个候选时，它提议记一份 ADR，这样下一次运行不会再提它。

## 它的位置

`improve-codebase-architecture` 是**定期保养**。你隔几天跑它一次，在任何链条之外，为了排队工作，不是为了做它。它的邻居：

- [codebase-design](https://aihero.dev/skills-codebase-design) 拥有每个候选都在用的深度与接缝词汇。
- [grilling](https://aihero.dev/skills-grilling) 在你选中候选之后走决策树。
- [domain-modeling](https://aihero.dev/skills-domain-modeling) 在你做决定时保持 `GLOSSARY.md` 和 ADR 不过时。

它的产出是一个想法，回到主构建流程的 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 或 [to-spec](https://aihero.dev/skills-to-spec)。主流程末端与它相对的是 [retro](https://aihero.dev/skills-retro)。这个技能改善智能体工作于其中的代码，`retro` 在一次构建之后改善它周围的环境（检查、标准、引导文件）。哪种处境用哪个技能，[ask-matt](https://aihero.dev/skills-ask-matt) 是整套技能的路由器。
