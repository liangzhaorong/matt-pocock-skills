## 它做什么

`tdd` 测试先行地构建一个特性或修复一个 bug：一条失败的测试，然后刚好够它通过的代码，然后下一个行为。它带着让这个回路产出值得留下的测试的那些标准：什么是好测试、测试放哪、mock 是干什么的，以及让一整套测试变得不值钱的三个反模式。

它不在你没先同意的接缝上写任何测试。任何测试存在之前，它先点名它打算测试的公共边界，然后停下来等你确认。测试精力是有限的，这一步把它花在关键路径上，而不是每个边缘情况上。`tdd` 也是一份**参考**，不是驱动。它装着循环的规则，由别的什么（你，或 [implement](https://aihero.dev/skills-implement)）来跑应用这些规则的[会话](https://www.aihero.dev/ai-coding-dictionary/session)。

## 何时该用它

输入 `/tdd`，或者当任务合适时[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)自动取用：测试先行地构建特性或修复 bug，或当你说出“red-green-refactor”时。

当有一个具体行为要建、有输入和可观察的输出、且你想要能活过重构的测试时，找它。

| 你的处境 | 去哪 |
| --- | --- |
| 一个输入输出明确的行为（业务逻辑、一份请求/响应契约、一次转换、校验） | `tdd` |
| 行为还没钉死 | [to-spec](https://aihero.dev/skills-to-spec)，它还会在任何代码写下之前谈定测试接缝 |
| 问题其实是接口的形状，不是测试 | [codebase-design](https://aihero.dev/skills-codebase-design) |
| 你手里有[规格](https://www.aihero.dev/ai-coding-dictionary/spec)或[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)，想让整个构建替你跑完 | [implement](https://aihero.dev/skills-implement)，它逐张工单驱动 `tdd` |
| 配置、接线、粘合、类型标注、直通的 CRUD 委托 | 这里没有合身的；见下面的未决缺口 |

最后一行是一个真实的缺口。技能决定接缝*放哪*，但它里面没有什么决定一个变更*是否*值得走这个循环。如果你对一个没有独立真相来源可断言的变更跑它，你会得到一条复述实现的测试。那正是技能警告的同义反复反模式，只是从另一个方向走到。它是 [issue #746](https://github.com/mattpocock/skills/issues/746)，开着。在它关闭之前，这个判断你自己做，或把规则写进你的 `CLAUDE.md`。

## 前置条件

需要安装 [codebase-design](https://aihero.dev/skills-codebase-design)。`tdd` 以前自带深模块和接口设计的笔记；v1.0 删掉它们换成共享技能，`tdd` 现在用它的接口设计词汇。除此之外没有了；这个技能是[无状态（stateless）](https://www.aihero.dev/ai-coding-dictionary/stateless)的，也不写自己的文件。

## 循环，以及它跑在的接缝

这个技能立在三个词上。

**红绿（Red-green）。**先写失败的测试，再写刚好让它通过的代码。不要为下下条测试写代码。没有重构阶段。技能在 2026 年 6 月砍掉了它，因为智能体几乎从不执行它，也因为评审和实现分开成不同会话效果更好。重构归 [code-review](https://aihero.dev/skills-code-review)。

**垂直切片（Vertical slice）。**在一条接缝上写一条测试，接着最小实现，然后重复。第一轮是一颗**曳光弹（tracer bullet）**，端到端证明单条路径。反面是水平切片：先写完所有测试，再写完所有代码。成批写的测试验证的是*想象中的*行为。它们查的是东西的形状而不是用户做的事，还会在你理解实现之前就把你绑死在一种测试结构上。

**预先谈定的接缝（Pre-agreed seam）。**接缝是你在不伸手进内部的情况下观察行为的公共边界。这条规则没有例外。没有测试落在未确认的接缝上。在完整链条里，接缝更早在 [to-spec](https://aihero.dev/skills-to-spec) 期间谈定：“`/tdd` 被告知只在预先谈定的测试接缝上工作，`/code-review` 检查只用了谈定的测试接缝。”单独调用时，`tdd` 直接问你。

它为防住这三个反模式而写：

| 反模式 | 露出的马脚 |
| --- | --- |
| 耦合实现 | 你重命名一个内部函数，行为没变，测试却碎了。被 mock 的内部协作者、被断言的调用次数、用数据库查询而非接口来做验证。 |
| 同义反复 | 期望值用代码算它的方式算出来，测试靠构造就通过。期望值必须来自别处：一个已知良好的字面量、一个手算的例子、规格。 |
| 水平切片 | 一批测试在任何实现之前就落地了。 |

Mock 只用于系统边界：外部 API、时间、随机性，有时是文件系统或数据库。永远不要 mock 你自己的模块。

## 常见问题

**它怎么不重构？描述里写着“red-green-refactor”呢。**

因为重构那步被删了，描述没删。删除是有意的。智能体几乎从不做那一步，而把实现和评审留在不同会话里效果更好。结果按书上还算不算 TDD，不如这个循环是否产出更好的代码要紧。触发语和正文的这个不一致已立为 [issue #589](https://github.com/mattpocock/skills/issues/589)，仍开着，所以“red-green-refactor”作为触发这个技能的短语继续有效。你拿到的是红绿，重构在 [code-review](https://aihero.dev/skills-code-review) 里。

**它让我挑一条测试接缝，我完全不知道挑哪个。**

这是这个技能被报告得最多的摩擦（[issue #607](https://github.com/mattpocock/skills/issues/607)）。提示里只列候选接缝的名字，没有任何关于每条抓得到什么、漏掉什么的信息，所以你是在标签之间挑。还没有已发布的修复。实际的绕法是回答之前先问智能体权衡：组件级接缝漏掉什么而集成接缝抓得住，它慢多少。这也是整条链在 `to-spec` 里提前谈定接缝的原因：在那里你看着的是整个特性，而不是一条提示。

**技能说先红，它还是先写了实现。**

会发生。一位用户就此逼问[模型](https://www.aihero.dev/ai-coding-dictionary/model)，得到了一个罕见诚实的回答：“我知道技能写了‘一次一条测试，看着它因正确的理由失败’。我读了。我还是默认了我的老习惯。”技能接受这一点。没有任何指示能让智能体百分之百服从，更严的措辞只会限制智能体的创造力，换不来多少收益。哪怕智能体没有严格遵守，这个循环也值得跑，因为总体结果仍然更好。如果某个切片必须严格遵守，盯住那次运行，别指望技能替你强制。

**它应该先写浏览器测试或端到端测试吗？**

通常不该，而技能拦不住。一位用户报告说智能体先写了一条 Playwright 测试，然后陷入长时间循环反复重跑它，最后得出结论：对一个还不存在的特性来说，坏掉的是*测试*。浏览器测试慢到红绿反馈回路收不回成本。在你仓库的 `CLAUDE.md` 里写明：浏览器测试排在行为能用之后。

**`/tdd` 取代 `/implement` 或课程里的 `/do-work` 吗？**

不。`/tdd` 记录方法论；`/implement` 是一个简单的“干活→反馈→提交”循环，是 `/do-work` 的直接替身。课程里单一的 `/do-work` 步骤现在拆成了 `/implement`、`/tdd` 和 `/code-review`。如果你问的是对一张工单该跑哪个，答案几乎总是 `/implement`。

**深模块和接口设计的指引去哪了？**

v1.0 里进了 [codebase-design](https://aihero.dev/skills-codebase-design)，做了泛化，好让几个技能共享一套词汇。`refactoring.md` 同时离开；重构现在是 [code-review](https://aihero.dev/skills-code-review) 的活，那个技能带着 Fowler 的坏味道基线。

**它知道我的其他工单吗？**

不知道。对着一张工单跑，它可能提出属于兄弟工单的活，因为它看不见 issue 图的其余部分（[issue #129](https://github.com/mattpocock/skills/issues/129)）。这不是 `tdd` 的职责。把规格连同工单一起递给它有帮助；一开始就把工单切好尺寸帮助更大。

## 有这些表现就说明它在生效

- 在任何测试文件存在之前，它停下来，点名它打算测试的接缝，然后等。
- 一条测试出现，变红，得到刚好通过的代码，然后才出现下一条测试，而不是一批测试之后跟一批代码。
- 测试名读起来是能力（“用户可以用有效购物车结账”），不是内部（“checkout 调用 paymentService.process”）。
- 断言里的期望值是能追溯到规格的字面量，不是按代码的算法重新算出来的值。
- 重命名一个内部函数，一整套测试毫发无损。
- Mock 只出现在外部边界（支付 API、时钟），从不出现在你自己的模块周围。

## 它的位置

`tdd` 跑在主链条的构建步骤里面，它自己不是一步：

```txt
grill-with-docs → to-spec → to-tickets → implement → code-review → retro
```

[to-spec](https://aihero.dev/skills-to-spec) 提前谈定测试接缝，[implement](https://aihero.dev/skills-implement) 逐张工单驱动 `tdd`，[code-review](https://aihero.dev/skills-code-review) 事后检查只用了谈定的接缝，并接管 `tdd` 不再做的重构。它的另一个邻居是 [codebase-design](https://aihero.dev/skills-codebase-design)，`tdd` 所用接缝与深模块词汇的共享来源。你也可以单独取用它，只要有一个具体行为要建、且没有整份规格在局中。拿不准哪个技能合身时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
