## 它做什么

`to-spec` 把你刚刚进行的那场对话变成一份**[规格（spec）](https://www.aihero.dev/ai-coding-dictionary/spec)**，并作为单个 issue 发布到你的 issue 跟踪器。

它不访谈你。你找到它的时候，决定已经做完了。所以它做的是综合已知的东西（来自线程、代码库、你的 `GLOSSARY.md` 和 ADR），而不是开启新一轮提问。规格记录的是你已做出的决定，它不是做新决定的地方。

## 何时该用它

你通过输入 `/to-spec` 来调用它；[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)不会自己主动取用它。

当构建大到一次智能体[会话](https://www.aihero.dev/ai-coding-dictionary/session)装不下、必须拆到几次会话时，找它。这就是全部触发条件：

| 你在哪里 | 跑什么 |
| --- | --- |
| 你什么都还没定 | 先 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 定了，且这份工作装得进一个[上下文窗口](https://www.aihero.dev/ai-coding-dictionary/context-window) | [implement](https://aihero.dev/skills-implement)：跳过规格 |
| 定了，且这份工作跨好几次会话 | `/to-spec`，然后 [to-tickets](https://aihero.dev/skills-to-tickets) |
| 一张 [wayfinder](https://aihero.dev/skills-wayfinder) 地图已经清空 | `/to-spec #<map_issue>` |

## 前置条件

`to-spec` 把规格发布为一个 issue，所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须先为这个仓库配好跟踪器和 triage 标签词汇。两种跟踪器都行：像 GitHub 这样的真跟踪器，或 `.scratch/` 下的本地 markdown 文件，后者无需额外配置。

## 规格是一份决定记录

规格的存在是因为上下文窗口会到头。[拷问（grilling）](https://www.aihero.dev/ai-coding-dictionary/grilling)时你定下了很多东西：解决方案的形状、你争辩过的选择、还有你故意拒绝掉的东西。这些全在一场你马上就要 clear 掉的对话里。规格把它留住。

所以规格不验证任何东西、也不决定任何东西。它用你自己项目的词汇记录你定下的东西，让一个全新的会话不必你再解释一遍就能接手这份工作。如果规格陈述了一个你从没说过的东西，那是一个缺陷。

## 先接缝，后散文

在写下任何东西之前，`to-spec` 先勾出特性将被测试的**接缝（seam）**，并和你核对。它偏好已有接缝而不是新接缝，并挑它能挑的最高接缝。一个变更的理想接缝数是一。

其他技能之后会用这些谈定的接缝。[tdd](https://aihero.dev/skills-tdd) 只在你事先谈定的接缝上工作。[code-review](https://aihero.dev/skills-code-review) 拿着规格评审 diff，于是一条没人同意过的接缝会变成一条评审发现。这两条连接都经过这份文档。这就是为什么接缝这场对话要在这里认真对待，不要留给实现。

## 常见问题

**`/to-prd` 去哪了？**
就是这个技能，v1.1 改的名。“规格（spec）”现在是贯穿始终的唯一用词，旧的 `to-prd` slug 不再可用，请以新名字重新安装。旧词汇被 *spec* 和 *tickets* 这一对取代。规格是目的地，以及把它定下来的那些决定；[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)是走到那里的步骤。如果你改了方向，删掉没做完的工单，留下规格。

**为什么规格会拿到 `ready-for-agent` 标签？我不想让智能体照着它实现。**
这个标签的意思是“无需进一步分诊”：文档完整到智能体可以照着干。它标记的是一个输入，不是一道工作指令。但轮询 `ready-for-agent` 的 [AFK（人不在键盘前）](https://www.aihero.dev/ai-coding-dictionary/afk)智能体看不出这个差别，它们会试图一口气把整份规格建完，而不是去捡那些工单切片。这是这个技能被报告得最多的问题。在它改变之前，在你的 AFK 智能体的提示词里显式排除父规格，或在 `/to-tickets` 跑完之后把标签摘掉。

**为什么不从拷问直接到 `/to-tickets`，跳过规格？**
常常就应该这样。只有跨会话的工作才值回规格这一步。它的价值在于：工单是一次性的，规格不是。每张工单按一个全新的上下文窗口裁尺寸，然后被删除或关闭；规格则留下来，作为记录它们背后推理的唯一地方。在单会话的变更上，这什么也给不了你，你却要为一个多余的、[模型](https://www.aihero.dev/ai-coding-dictionary/model)可能跑偏的综合步骤付钱。从拷问直接去 `/implement`。

**我刚跑完一张 wayfinder 地图，喂给它什么？**
给它主地图 issue，`/to-spec #<map_issue>`，而不是单张决策工单。[wayfinder](https://aihero.dev/skills-wayfinder) 产出的是摊在一张地图上的决定，不是交付物。`to-spec` 把它们收拢成一份你可以照着构建的文档。如果你把地图直接喂给 `/implement`，你就丢掉了这一步。

**规格是给我评审的，还是只给智能体看的？**
主要给智能体，它读起来也像：完整、致密、满是引用。去读接缝和范围之外两节。在这两个地方，一个错误的决定现在抓最便宜，日后发现最昂贵。人们确实抱怨要读完全文，而且没有摘要模式。但如果规格让你意外，那是拷问太浅了，不是规格太长。

**工单开始之后我要把规格冻结吗，还是让智能体重写它？**
没有什么会保持它同步。实践中它是你当时所知的一张快照，实现第一次教给你新东西的那天它就过时了。工作交付之后，把它当作一次性的。你的 `GLOSSARY.md` 和 ADR 才是打算长存的文件。如果实现期间你学到了该长存的东西，放进那里，不要放进一份被改过的规格。

**我的工作是一次重构或一道模块边界，不是一个特性，模板合身吗？**
不太合身，这是一个已知局限。模板重度依赖用户故事，而用户故事装不下架构性工作。你最后会围着那些其实关于接口和不变量的决定，写一堆没人要的故事。改用“实现决定”和“测试决定”两节。耐久的架构决定通过 [grill-with-docs](https://aihero.dev/skills-grill-with-docs) 记成 ADR，不要记进规格。

**它会去跟踪器里查相关工作，或引用它所遵循的 ADR 吗？**
都不会。它会读并遵循它触碰领域的 ADR，但不链接它们。它写之前也不会在跟踪器里搜重叠的 issue，所以一份规格可能重复别人已经立过的工作，而且没有东西警告你。如果这个领域很热闹，先自己去跟踪器里搜一遍。

**`/to-tickets` 读不了我的规格：它一直被截断。**
跟踪器 issue 可能不会完整返回一份非常大的规格，也没有本地副本可以替代。要解决这个问题，在 `/to-spec` 和 `/to-tickets` 之间不要 [clear](https://www.aihero.dev/ai-coding-dictionary/clearing) 或 [compact](https://www.aihero.dev/ai-coding-dictionary/compaction)。在同一个窗口里跑它们，`/to-tickets` 就永远不必重新去取规格。

## 有这些表现就说明它在生效

- 它开始写，而不是向你开启新一轮提问。
- 它在写之前把接缝给你看，并尽可能少提。
- 它用的是你项目的名词，不是泛泛的产品管理套话。
- 里面的每个决定你都记得自己做过。它没有为了填满某一节而发明任何东西。
- 范围之外一节列的是真东西。你拒绝掉的东西往往是这页上最有用的几行。

## 它的位置

`to-spec` 是主构建链上的一步，但只在它的多会话分支上：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

上游，[grill-with-docs](https://aihero.dev/skills-grill-with-docs) 做出这个技能只是记录的那些决定；一张跑完的 [wayfinder](https://aihero.dev/skills-wayfinder) 地图也在这里接入链条。下游，[to-tickets](https://aihero.dev/skills-to-tickets) 把规格切成曳光弹工单，交给 [implement](https://aihero.dev/skills-implement) 去建。拿不准哪个技能或流程合身时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
