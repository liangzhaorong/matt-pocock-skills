## 它做什么

`triage` 处理你项目跟踪器上的 issue。它把每一个挪过一台由**分诊角色（triage roles）**（一个类别角色加一个状态角色）构成的小状态机。每个 issue 的结局是：一份智能体可用的简报、一个给报告者的具体问题，或一个记录了原因的已关闭 issue。

它只用于**不是你创建的** issue。也就是原始 bug 报告、涌进来的特性请求，或不请自来的外部 pull request：从外面进入跟踪器的工作，报告者留下什么形状就是什么形状。[to-tickets](https://aihero.dev/skills-to-tickets) 的[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)本来就已智能体可用，对它们跑 `triage` 是白费那份工作。规则很简单：`/triage` 只管找上门的 issue，不管你自己立的。

它也不同于手工打标签：它先推荐，然后等。它给你带理由的类别和状态判断，加上它在代码库里找到的东西，你不说“应用”，它就什么都不动。

## 何时该用它

你输入 `/triage` 再用平实的语言描述你要什么来调用它。[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)不会自己主动取用它。比如：“给我看需要我注意的东西”“我们看看 #42”“把 #42 挪到 ready-for-agent”。

| 你手上是什么 | 去哪 |
| --- | --- |
| 一个装满了别人原始报告的跟踪器 | `/triage` |
| 你自己一个粗粗的想法，什么都没写下 | [grill-with-docs](https://aihero.dev/skills-grill-with-docs) |
| 一场已谈定的对话要变成[规格](https://www.aihero.dev/ai-coding-dictionary/spec) | [to-spec](https://aihero.dev/skills-to-spec) |
| 一份规格要拆成智能体可用的工单 | [to-tickets](https://aihero.dev/skills-to-tickets) |
| 一个已确认的 bug，要的是根因，不是标签 | [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs) |

## 前置条件

`triage` 读写你的 issue 跟踪器，所以 [setup-matt-pocock-skills](https://aihero.dev/skills-setup-matt-pocock-skills) 必须先配好那个跟踪器和它的标签词汇。下面的角色名是**规范名**。你跟踪器里的标签字符串可能不同，setup 提供映射。如果你的跟踪器已经在精确使用规范名，你就没什么可映射、也没什么可设置的。

跟踪器配置还决定外部 pull request 算不算一个请求面、谁算外部。那个开关默认关闭，setup 也不再问它。要把 PR 纳入范围，在 `docs/agents/issue-tracker.md` 里打开它。

## 状态机

每个分诊过的条目最终恰好带一个类别角色和一个状态角色。类别有两个：`bug`（有东西坏了）和 `enhancement`（新特性或改进）。状态有五个：

| 状态 | 含义 |
| --- | --- |
| `needs-triage` | 需要你评估。未打标签的 issue 通常先落在这里。 |
| `needs-info` | 等报告者。他们回复后回到 `needs-triage`。 |
| `ready-for-agent` | 已完全指明，附有一份智能体简报。[AFK](https://www.aihero.dev/ai-coding-dictionary/afk) 智能体能接手。 |
| `ready-for-human` | 同样的简报，加上智能体做不了的原因：主观判断、外部访问、手动测试。 |
| `wontfix` | 关闭，原因已记录。 |

这就是全部词汇。每条目恰好一个状态角色的规则让查询保持简单。这些状态也是这个[技能](https://www.aihero.dev/ai-coding-dictionary/skill)被要求最多的地方。用户要过一个给“已指明但被另一个 issue 阻塞”的工作的状态、一个给“等未来某个触发器”的工作的 `deferred` 状态、一个终态 `implemented`。这些都没有发布。见下面的问题。

`wontfix` 有三种情况。差别要紧，因为只有其中一种写知识库：

| 为什么关 | 发生什么 |
| --- | --- |
| 已经实现 | 一条指向该特性所在地的评论。不往 `.out-of-scope/` 放任何东西，因为它是已建成的特性，不是被拒绝的，那里放文件会破坏去重检查。 |
| 被拒绝的 bug | 礼貌解释，然后关闭。 |
| 被拒绝的特性增强 | `.out-of-scope/` 里的一个文件，从关闭评论链过去，然后关闭。 |

`.out-of-scope/` 每个被拒绝的**概念**放一个 markdown 文件，不是每个 issue 一个。每个文件是一份简短的设计文档，不是一行数据库记录。它写明拒绝了什么、为什么，并列出每一个为它而来的 issue。`triage` 在评估任何东西之前先读整个目录。它按概念匹配，不按关键词，所以“夜间主题”匹配得上 `dark-mode.md`。找到匹配时，它把旧决定给你看，问你是否仍然同意，而不是从头再争一遍这个请求。

## 先验证，后简报

在任何[拷问](https://www.aihero.dev/ai-coding-dictionary/grilling)之前，`triage` 先核实说法是否站得住。对 bug，它按报告者的步骤复现。对 PR，它检出分支并跑相关测试。然后它报告三种结果之一：

- 已确认，附代码路径。
- 无法复现。
- 细节不够，没法试。这是现有最强的 `needs-info` 信号。

同一遍里，它对代码库再跑两项检查。**冗余**检查问这个特性是否已经实现，按领域概念搜，不按报告者的措辞搜。**先前拒绝**检查问 `.out-of-scope/` 是否已经说过不了。两项检查都便宜，任一命中就产出 `wontfix`。

所有这些工作都是为了一件东西：**智能体简报（agent brief）**。这是 issue 挪到 `ready-for-agent` 时 `triage` 发的那条结构化评论。`triage` 发出它之后，简报就是合同，原始报告只是背景。简报追求**耐久**而非精确，因为一个 issue 可以在 `ready-for-agent` 里坐几个星期，期间代码在变。所以简报点名类型、签名和行为契约，从不含文件路径或行号。一次确认过的复现造出的简报远强于一个猜测。

## PR 是一个带代码的 issue

如果跟踪器把外部 pull request 当作请求面，它们就走同一台状态机，用同样的类别、状态和迁移。状态作用于 diff。`ready-for-agent` 意思是简报已附上，智能体该对代码走下一步。`ready-for-human` 意思是人可以合并它。PR 上的简报描述的是对现有 diff 还剩什么要做，不是怎么从零构建这个东西。

发现只显示*外部* PR，因为协作者进行中的分支不是分诊的活。那个过滤器只作用于发现。如果你点名一个 PR，`triage` 就处理它，无论是谁写的。

## 常见问题

**我跑了 `/to-spec` 和 `/to-tickets`，现在那些工单坐在那儿没分诊。我要对它们跑 `/triage` 吗？**
不要。它们已经智能体可用。`to-tickets` 发布它们时就打上 `ready-for-agent` 标签，AFK 运行器不必再过一遍就能捡起。撞上这个问题的用户跑了规格流程，看到输出上有 `needs-triage`，他们的 AFK 运行器把一切都忽略了。`triage` 是从外面找上门的工作的入口匝道。规格流程是你自己发起的工作的车道。两条道在 `ready-for-agent` 相会，不在那之前。

**都有了 `to-spec` → `to-tickets` → `implement` 流程了，`triage` 还相关吗？**
只有当有找上门的工作时才相关。`triage` 比那条流程更老，干的是另一份活：处理别人立的报告。如果你跟踪器里的一切都来自你自己的规划，你很少会用它。如果你维护任何公开的东西，或者你的团队向你报 bug，它就是那份工作的起点。最主要的用途是从外部贡献者接收 issue 的开源仓库。

**智能体试图打 `ready-for-agent`，`gh` 却说标签不存在。**
这是一个已知的未修复 bug（[#616](https://github.com/mattpocock/skills/issues/616)）。`setup-matt-pocock-skills` 把标签词汇写进 `docs/agents/triage-labels.md`，但不在你的跟踪器里创建标签。用 `gh label create` 或跟踪器的 UI 自己建一次五个状态标签和两个类别标签，错误就停了。那个 issue 链接着一个还没合并的社区修复分支。

**五个状态不够用。加个 blocked、deferred 或 implemented 呢？**
这是这个技能被立案最多的缺口。它有三种形态：

- 一个已完全指明、却等着另一个 issue 关闭的 issue（[#139](https://github.com/mattpocock/skills/issues/139)）。报告者说 `ready-for-agent` 在那里“技术上为真”但有误导性，于是智能体捡起它然后卡住。
- 一个有意做、却在等触发器的未来工作，所以还不可行动（[#297](https://github.com/mattpocock/skills/issues/297)）。
- 一个“已实现、待验证”的终态。没有它，AFK 运行器可能把做完的工单再排一遍队。

blocked 这种情况被承认是真实的，但名字未定（`blocked` 对 `paused`）。这些都没发布。作为绕法，人们在类别旁边加一个仓库本地的额外标签。状态槽里装的值就准确了，但技能不知道那个额外标签。一个社区 fork 走得更远，加了 `needs-slicing`、`tracking` 和工作量标签。那能用，但它属于那个 fork，不属于技能。

**这和 `/diagnosing-bugs` 有什么区别？**
这里的验证步骤故意做浅。它回答“这是不是真的、大致住在哪”，不找根因。如果一个 bug 几分钟内没法按报告者的步骤复现，用 `needs-info`；想现在就深挖就用 [diagnosing-bugs](https://aihero.dev/skills-diagnosing-bugs)。两个技能的文本都还没提到对方。一位用户报告过那个缺口，它仍开着。

**我能把它对准整个积压清单撒手不管吗？**
可以要求，但盯住它读了什么。“显示需要注意什么”那一步是一次为*挑选*服务的廉价列单。你挑一个 issue，`triage` 再就那个 issue 收集完整[上下文](https://www.aihero.dev/ai-coding-dictionary/context)。如果你一次让它扫二十个 issue，智能体可能拿那份廉价列单当唯一证据而不告诉你。列单返回 issue 正文但不返回评论。一位用户正好撞上过这个。三个 issue 已经有评论写着“已修复，建议关闭”，结果三个都拿到了新的智能体简报。批量过一遍时，明确说它必须读每个 issue 的评论。

**它能配 Linear 或 GitHub Issues 以外的什么用吗？**
能。跟踪器是配置，不是写死的假设。人们对着 Linear（通过 `linear` CLI）、GitLab 和 `.scratch/` 下的纯 markdown 文件跑它。一个常见分工是 Linear 管 issue 和规划、GitHub 管代码和 PR。说“issue 跟踪器”的技能这时映射到 Linear，说“PR”的映射到 GitHub。本地 markdown 跟踪器有一个未关闭的模板 bug：生成的文件可能把验收标准装两遍，一遍在顶层，一遍在智能体简报里（[#200](https://github.com/mattpocock/skills/issues/200)）。

## 有这些表现就说明它在生效

- 它碰过的每个条目最终都恰好带一个类别角色和一个状态角色，从不是零个，也从不有两个互相冲突的状态。
- 它给你一个带理由的建议然后停。它不会改完标签就走人。
- 在任何东西到达 `ready-for-agent` 之前，它复现了 bug，或检出并跑了 PR。
- 它写的简报点名类型和行为，不含文件路径、不含行号。
- 当你六个月前拒绝过的请求再次到来时，它告诉你并引用旧理由，而不是重新分诊一遍。
- 它发的每条评论都以 `> *This was generated by AI during triage.*` 开头。

## 它的位置

`triage` 是一条**入口匝道**，不是主链条上的一步。主流程从你自己的一个想法出发（拷问、规格、工单、实现、评审）。`triage` 是来自别人的工作的平行车道。两条车道终点相同：一个打着 `ready-for-agent` 标签、附着一封简报的 issue。[implement](https://aihero.dev/skills-implement) 捡起它的方式与捡起 [to-tickets](https://aihero.dev/skills-to-tickets) 的一张工单完全一样。当一个请求需要更多细节、`triage` 才能写简报时，`triage` 把 [grilling](https://aihero.dev/skills-grilling) 和 [domain-modeling](https://aihero.dev/skills-domain-modeling) 一起跑，一次一轮问题，这样你做决定的同时它就把决定记进 `GLOSSARY.md` 和 ADR。拿不准自己在哪条车道时，[ask-matt](https://aihero.dev/skills-ask-matt) 为你路由。
