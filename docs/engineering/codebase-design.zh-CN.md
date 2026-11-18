## 它做什么

`codebase-design` 固定你设计模块时用的词：**模块（module）**、**接口（interface）**、**深度（depth）**、**接缝（seam）**、**适配器（adapter）**、**杠杆（leverage）**、**局部性（locality）**。它精确地定义每一个，禁用松散的替身（“组件”、“服务”、“API”、“边界”），并陈述由它们引出的那几条原则。

它是一份参考，不是一个过程。它不跑循环、不产出工件、也不会停下来问你问题。其他所有涉及设计的技能都用它的词汇。单独调用时，它给你词语，然后停止。调用之前先知道这一点。如果你把一个[会话](https://www.aihero.dev/ai-coding-dictionary/session)指向一个没有过程、没有停止规则的技能，说“开始”，智能体就会发明一个过程。下面的问题展示了那是什么样。

## 何时该用它

输入 `/codebase-design`，或者当设计任务合适时智能体自动取用它。

当你已经知道要重设计哪段代码、需要思考它的形状时，找它：接缝放哪、接口能缩到多小、一次抽取是否把复杂度从调用方移走。也用它来平息一场关于某个设计词含义的争论。

好几个技能与它接近。按你的问题挑：

| 问题 | 技能 |
|---|---|
| 一个模块的形状：它的接口、接缝、深度 | `codebase-design` |
| *领域的用词*：“账户”有三个意思，两个人说的“取消”不是一回事 | [domain-modeling](https://aihero.dev/skills-domain-modeling) |
| 你还不知道要重设计的*是哪个*模块 | [improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture)（那个找出候选者的勘察） |
| 你想有人跟设计对着辩，而不只是给它命名 | [grilling](https://aihero.dev/skills-grilling) |
| 有一个具体行为要建，你想要能活过重构的测试 | [tdd](https://aihero.dev/skills-tdd) |

## 词汇表

这份词汇表就是这个技能。技能让每个术语都相对于其他术语得到定义，并给每个术语配上它所取代的词。

| 术语 | 它是什么意思 | 别再说 |
|---|---|---|
| **模块（Module）** | 任何有接口和实现的东西。刻意不区分规模：一个函数、一个类、一个包、一段跨层的切片。 | unit、component、service |
| **接口（Interface）** | 调用方要正确使用它所必须知道的一切：类型签名，加上不变量、顺序约束、错误模式、必需配置、性能特征。 | API、signature |
| **深度（Depth）** | 接口处的杠杆：调用方或测试每学一个单位的接口，能驱动多少行为。**深**：小接口背后大量行为。**浅**：接口几乎和实现一样复杂。 | 无 |
| **接缝（Seam）** | Michael Feathers 的术语：一个不用就地编辑就能改变行为的地方。它是接口的*位置*，把它放哪是一个独立于“它背后放什么”的决定。 | boundary |
| **适配器（Adapter）** | 在接缝处满足一个接口的具体之物。它命名的是一个角色，不是一类东西：内存假实现和 Postgres 仓库都是适配器。 | 无 |
| **杠杆（Leverage）** | 调用方从深度中得到的：每学一个单位接口换来更多能力。 | 无 |
| **局部性（Locality）** | 维护者从深度中得到的：变更、bug 和验证待在一个地方，一次修复覆盖所有调用方。 | 无 |

技能*没有*把深度定义为实现行数与接口行数之比，那是 Ousterhout 本人的定义。那个指标奖励往实现里注水，所以技能改用“深度即杠杆”。

## 四条原则

- **深度是接口的属性，不是实现的属性。**一个深模块内部可以由小的可替换部件构成，只是调用方看不见。一个模块可以有给自己测试用的内部接缝，以及在接口处的一条外部接缝。
- **删除测试。**想象删掉这个模块。如果复杂度消失了，它原来只是个传声筒。如果复杂度在 N 个调用方身上重新出现，它原来藏着真复杂度。
- **接口就是测试面。**调用方和测试穿过同一条接缝。如果你想测到接口*背后*去，这个模块的形状就错了。
- **一个适配器意味着一条假想的接缝，两个适配器才意味着一条真的。**没有什么在接缝两侧变化之前，别切接缝。只有一个适配器的接缝只是间接层。

两份支撑文件走得更远，技能按需读它们，而不是一上来就读。[DEEPENING.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DEEPENING.md) 把候选模块的依赖分进四类（进程内、本地可替换、远程但自有、真外部），因为类别决定你如何跨接缝测试加深后的模块。[DESIGN-IT-TWICE.md](https://github.com/mattpocock/skills/blob/main/skills/engineering/codebase-design/DESIGN-IT-TWICE.md) 启动并行[子智能体](https://www.aihero.dev/ai-coding-dictionary/subagent)，为同一个模块产出三个或更多截然不同的接口，然后按深度、局部性和接缝位置比较它们。

## 常见问题

**在 TypeScript 里我到底怎么建一个深模块？**

这是关于这个技能被问得最多的问题，而技能不回答它。它定义一个深模块*是*什么；对如何挡住一个越过接口的杂散 import，它一个字不说。[Issue #458](https://github.com/mattpocock/skills/issues/458) 说得直白：“假设我们对接口满意，它藏住了细节，等等。可我们怎么强制执行它？我想没有 lint 或清晰的护栏，人和 LLM 一样，都会随着时间把它弄乱。”那条讨论串里的回答给了三个选项：包进一个 class 或 IIFE，并接受这个 class 变得巨大；做成 monorepo 里的一个包，并接受 monorepo 工具链；或用像 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 这样的 linter 禁止绕过接口的 import。这些机制里，Effect 最好，dependency-cruiser 次之。仓库的 `in-progress/` 桶里有一个 `setup-ts-deep-modules` 技能，铺下 `src/packages/<name>/index.ts` 约定，但它是 beta 频道的技能，没有文档页，也不带 lint 规则。

**我把一个会话指向它，它烧了 10 万 [token](https://www.aihero.dev/ai-coding-dictionary/token) 重设计我根本没问的东西。**

这是一个已知问题，已立为 [issue #449](https://github.com/mattpocock/skills/issues/449)。这个技能可被模型调用，且自我描述为词汇，但它里面没有任何东西能硬性拦住智能体把它当成可运行的过程。被指示“在 /codebase-design 里继续，推进未决的决定”后，一个智能体挑中了技能里最像过程的那部分：`DESIGN-IT-TWICE.md` 里的并行子智能体。它把前一个会话已经勘察过的代码重新勘察了一遍，干了很久才开口问点什么。这个技能没有驱动型技能的那些护栏（检查点、一次只问一个问题、不自动推进），因为它是参考。绕法是调用一个驱动型技能（`/grill-with-docs`、`/improve-codebase-architecture` 或 `/tdd`），把 `codebase-design` 当它的词汇用。issue 仍开着。

**`design-an-interface` 去哪了？另外有没有一个 `/interface-design` 技能？**

这个技能取代了 `design-an-interface` 并接手了它的内容。什么都没丢。它的“设计两遍”技法（并行子智能体生成截然不同的设计，出自 Ousterhout）以 `DESIGN-IT-TWICE.md` 的形式随这里发布。另外，好几个人要过一个专管“深模块/薄接口”哲学的 `/interface-design` 技能；这个技能已经覆盖那套哲学，没有另立技能的计划。如果你是冲着这两个名字来的，页面就是这一页。

**这不就是一种文件结构约定吗，比如文件夹、barrel 文件、特性切片？**

不是。人们对此反对过很多次，技能没有变。[Issue #95](https://github.com/mattpocock/skills/issues/95) 提议把一种形式化的分形树文件结构当作深模块的具体实现；得到的回复是两者相互独立：“深模块关乎接口的设计、关乎通过严格接口访问，文件系统长什么样无所谓。用这套文件结构做出浅模块，看起来完全可能。”同样的说法也出现在 #458 里：“我想你可能是把模块的概念和文件系统绑得太紧了。文件系统当然可以是对模块形状的有用提示，但构建深模块不需要用到文件系统。”词汇表故意把**模块**定义为不区分规模。

**`tdd` 真的用了这套词汇吗？**

现在用了，但很长一段时间没有。v1.0 把 `tdd` 里内联的深模块笔记删掉，换成了这个共享技能，但没人补一个指针回去，于是 `tdd` 自己给“接缝”下了定义，什么也不引用。指针现在在 `tdd` 里了，当悬而未决的问题是接口的形状而非测试时，智能体会跟着它走。`tdd` 仍然拥有“接缝”作为你*测试*所在的边界；这个技能拥有它背后的模块形状。

**design-it-twice 这套模式在 Claude Code 之外能用吗？**

不能干净地用。`DESIGN-IT-TWICE.md` 说“用 Agent 工具并行起 3 个以上子智能体”，而“Agent”是 Claude Code [工具](https://www.aihero.dev/ai-coding-dictionary/tool)的名字。仓库为其他 [harness](https://www.aihero.dev/ai-coding-dictionary/harness)（包括 Codex）发布了元数据，而那些可能没有叫这个名字的工具。所以并行设计阶段的可移植性不如技能的元数据暗示的那样好。[Issue #564](https://github.com/mattpocock/skills/issues/564) 追踪此事，仍开着。

**我能往词汇表里加自己的概念吗，比如 connascence、module secrets、[渐进披露（progressive disclosure）](https://www.aihero.dev/ai-coding-dictionary/progressive-disclosure)？**

有人提议过这些。[Issue #180](https://github.com/mattpocock/skills/issues/180) 把 Parnas 的模块秘密（module secrets）和 Page-Jones 的 connascence 加为“接缝上漏过去的是*什么*”的命名层，附带可用的 diff；[issue #303](https://github.com/mattpocock/skills/issues/303) 提议在实现内部做渐进披露，让一个公共接口上深的模块内部也有结构。两条都开着、未合并。已发布的词汇表刻意保持小，技能自己给出了理由：一致的语言就是全部意义所在，一个没人一致使用的术语比没有术语更糟。

## 有这些表现就说明它在生效

- 设计对话不再产出“组件”“服务”“边界”这些词，开始产出“模块”“接口”“接缝”。
- 有人能指着一次提议的抽取，不含糊地说它过没过删除测试。
- 一条提议的接缝总带着第二个被点名的适配器，而不止第一个。
- 对接口的讨论覆盖不变量、顺序和错误模式，而不只是类型签名。
- 调用它不会开启一个会话。如果智能体仅凭 `/codebase-design` 就开始读文件、提重构，它就是把参考错当成了驱动。

## 它的位置

`codebase-design` 是一个**随取随用的独立件**，是工程技能底下的词汇层，不是任何链条上的一环。它最近的邻居是 [domain-modeling](https://aihero.dev/skills-domain-modeling)，一份平行的参考，管*问题领域*的词而非模块的形状。两者通常要一起用，因为给一个深模块起好名字两样都得要。[improve-codebase-architecture](https://aihero.dev/skills-improve-codebase-architecture) 是另一个：它勘察代码库寻找可深化的候选，并把每一个都用这套词汇写下来，它找到模块，你用这个技能的词来设计它。拿不准哪个技能或流程合身时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
