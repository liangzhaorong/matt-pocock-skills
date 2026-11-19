# Matt Pocock Skills 使用手册

> 本手册面向第一次接触这套 skill 工具的新手，基于仓库当前版本（v1.3.2，共 27 个正式技能）编写。
> 阅读完第 1 至 5 章，你就能开始日常使用；第 8、9 章是完整参考，可按需查阅。

**技能仓库**：[mattpocock/skills](https://github.com/mattpocock/skills)
**作者**：Matt Pocock（[AI Hero](https://www.aihero.dev)）
**协议**：MIT

---

## 目录

1. [这套技能是什么](#1-这套技能是什么)
2. [核心概念](#2-核心概念)
3. [安装与初始化](#3-安装与初始化)
4. [五分钟快速上手](#4-五分钟快速上手)
5. [核心工作流：从想法到上线](#5-核心工作流从想法到上线)
6. [三条上匝道](#6-三条上匝道)
7. [代码库健康与词汇层](#7-代码库健康与词汇层)
8. [技能速查总表](#8-技能速查总表)
9. [技能详解](#9-技能详解)
10. [常见问题](#10-常见问题)
11. [附录：给想改造这套技能的人](#11-附录给想改造这套技能的人)
12. [延伸阅读](#12-延伸阅读)

---

## 1. 这套技能是什么

这是 Matt Pocock（TypeScript 社区知名教育者）公开的一组 AI 编程代理技能（agent skills），他在日常真实工程工作中每天使用。口号是 "Skills For Real Engineers"：真正的工程，而不是"氛围编程"（vibe coding）。

与 GSD、BMAD、Spec-Kit 这类"全流程接管"的工具不同，这套技能的设计哲学是：

- **小**：每个技能只做一件事
- **易于修改**：技能就是 Markdown 文件（`SKILL.md`），你可以随意魔改
- **可组合**：技能之间通过明确的调用关系拼成工作流
- **模型无关**：Claude Code、Codex、Copilot、Gemini CLI 等主流代理都能用

### 它解决的四个失败模式

理解这四个问题，就理解了整套技能为什么长这样：

| 失败模式 | 症状 | 解法 |
| --- | --- | --- |
| **代理没做我想要的** | 你以为说清楚了，它建出来的东西完全跑偏 | 拷问式访谈（grilling）：让代理在动手前向你连环提问。对应 `/grill-me` 和 `/grill-with-docs`，这是全仓库最受欢迎的技能 |
| **代理太啰嗦** | 代理被扔进项目里瞎猜黑话，20 个词说 1 个词的事 | 共享语言：维护 `GLOSSARY.md` 词汇表和 ADR（架构决策记录）。内建在 `/grill-with-docs` 里。附带收益：命名一致、代码库更好导航、代理思考消耗的 token 更少 |
| **代码跑不起来** | 代理写代码时是盲飞，没有反馈回路 | 红-绿-重构循环：`/tdd`（测试驱动开发）+ `/diagnosing-bugs`（纪律化诊断循环） |
| **我们造了个泥球** | 代理加速写代码，也加速了软件熵增，代码库越来越难改 | 每天投资设计：`/to-spec` 在写规格前先问你测试接缝（seam）；`/improve-codebase-architecture` 定期勘察"深化机会"。建议每隔几天跑一次（注意：它是勘察，不是救援） |

---

## 2. 核心概念

### 2.1 技能（skill）与两种调用方式

每个技能是一个文件夹，核心是一份 `SKILL.md`（Markdown 写的操作指令，代理读了照做）。装好后，大多数技能以斜杠命令的形式出现，例如输入 `/grill-me` 即可触发。

全仓库最重要的一个分类轴是：**谁能调用它**。

| 类型 | 谁能触发 | 特点 |
| --- | --- | --- |
| **用户调用**（user-invoked） | 只有你，输入命令才算数 | 负责编排流程，比如 `/implement`、`/to-spec`。任何其它技能都无法触发它 |
| **模型调用**（model-invoked） | 你可以输命令，代理遇到匹配任务时也会自动触发 | 承载可复用的纪律，比如 `/tdd`、`/code-review`。代理写 PR 时会自动想起 `/pr`，碰到只有人能完成的步骤会自动想起 `/wizard` |

规则：用户调用的技能可以调用模型调用的技能，但永远不能调用另一个用户调用的技能。所以"编排者"（用户调用）驱动"纪律"（模型调用）。

### 2.2 桶（bucket）：技能如何组织

技能放在 `skills/` 下的五个桶文件夹里：

| 桶 | 状态 | 说明 |
| --- | --- | --- |
| `engineering/` | 正式（promoted） | 日常代码工作，20 个技能 |
| `productivity/` | 正式（promoted） | 日常非代码工作流，7 个技能 |
| `in-progress/` | 测试版（beta） | 故意公开求反馈，不随插件分发，可单独安装 |
| `misc/` | 冻结 | 不再维护，不接受 issue |
| `deprecated/` | 废弃 | 不再使用 |

只有正式桶（engineering + productivity，共 27 个）会随插件分发、出现在顶层 README 中。本手册主要覆盖这 27 个。

### 2.3 流（flow）

一个**流**是穿过若干技能的一条路径。大多数路径沿同一条**主流程**（想法到上线，见第 5 章）走，另有两条**上匝道**（on-ramp，见第 6 章）汇入主流程，其余技能独立使用（standalone），或者作为词汇层垫在最底下（见第 7 章）。

### 2.4 关键术语表

新手最容易卡住的术语，先混个脸熟：

| 术语 | 含义 |
| --- | --- |
| **拷问（grilling）** | 让代理就你的计划连环提问，直到设计树的每个分支都有结论。事实归代理查，决策归你做 |
| **设计树 / 前沿（frontier）** | 把决策组织成一棵树；"前沿"是前置决策都已定案、现在就能问的那些问题。拷问按轮次推进，一轮问完整个前沿 |
| **接缝（seam）** | 测试所依附的公共边界。测试只写在接缝上，不测内部实现细节 |
| **深模块（deep module）** | 小接口背后藏大量行为的模块（Ousterhout《软件设计哲学》）。这套技能的核心设计观 |
| **曳光弹票（tracer-bullet ticket）** | 一张贯穿全部层次的垂直切片工单，端到端可验证，而不是"先做数据库层"那种水平切片 |
| **阻塞边（blocking edges）** | 工单之间的依赖关系。`/to-tickets` 产出的每张票都声明自己被谁阻塞 |
| **任务图 / 前沿（task graph / frontier）** | 工单加上阻塞边构成一张图；任何时刻"已经不再被阻塞、可以开干"的工单集合叫前沿 |
| **词汇表（`GLOSSARY.md`）** | 项目共享语言，代理读它来正确使用项目黑话 |
| **ADR** | 架构决策记录，难以解释、难以回头的决策写在这里 |
| **智能区（smart zone）** | 大约 150k token 的上下文窗口范围，模型在其中还能保持敏锐推理。接近了就不要硬撑，该压缩就压缩 |
| **阶段边界（phase boundary）** | 一个会话内两个工作阶段之间的切换点，在那里决定：继续、清空、交接、派子代理、还是压缩 |
| **一手来源（primary source）** | 官方文档、源码、规格本身，而不是二手转述。`/research` 只信一手来源 |

---

## 3. 安装与初始化

### 3.1 按你的代理安装

安装有两条互斥的路线，**每个代理只选一条**（都装会让每个技能出现两份）：

- **插件（plugin）路线**：托管、只读、自动更新，适合直接用
- **skills.sh 路线**：把可编辑的技能文件复制进你的项目，你手工更新，适合想魔改的人

#### Claude Code（插件路线，默认自动更新）

```bash
claude plugin install mattpocock-skills@claude-plugins-official
```

#### Codex（插件路线，启动时自动更新）

```bash
codex plugin marketplace add mattpocock/skills
codex plugin add mattpocock-skills@mattpocock
```

#### GitHub Copilot（CLI 和 VS Code）

```bash
copilot plugin marketplace add mattpocock/skills
copilot plugin install mattpocock-skills@mattpocock
```

然后，一次性地在 `~/.copilot/settings.json` 中添加：

```json
{
  "extraKnownMarketplaces": {
    "mattpocock": { "source": { "source": "github", "repo": "mattpocock/skills" }, "autoUpdate": true }
  }
}
```

在 VS Code 中，运行 **Chat: Install Plugin From Source** 并输入 `https://github.com/mattpocock/skills`。它每日更新。

#### Gemini CLI（手动更新）

```bash
gemini skills install https://github.com/mattpocock/skills.git --path skills/engineering
gemini skills install https://github.com/mattpocock/skills.git --path skills/productivity
```

重新运行这两条命令即可更新。

#### 其它代理，或要可编辑文件（skills.sh 路线）

Cursor、OpenCode、Devin、Windsurf、Amp、Pi 等：

```bash
npx skills@latest add mattpocock/skills -a <agent>  # cursor, opencode, devin, windsurf, amp, pi; omit -a to choose
```

当安装器问你要装哪些技能时，记得包含 `setup-matt-pocock-skills`。更新时运行 `npx skills@latest update`，并重新运行 `add` 以获取新技能。

### 3.2 初始化：`/setup-matt-pocock-skills`

装好后，**每个仓库运行一次**：

```
/setup-matt-pocock-skills
```

它会通过问答为你配置三样东西（后续技能都依赖这些配置）：

1. **issue 追踪器**：GitHub、GitLab、本地 Markdown 文件，或者任何你能描述的追踪器
2. **triage 标签**：五个规范分诊角色在你追踪器里的实际标签字符串（见 9.1 节）
3. **领域文档布局**：`GLOSSARY.md` 和 ADR 放在哪里，代理阅读它们的规则

配置产物写在该仓库的 `docs/agents/` 目录下（如 `issue-tracker.md`、`triage-labels.md`、`domain.md`）。配置错了随时重跑一遍即可刷新。

### 3.3 只安装单个技能

比如你只想要拷问技能：

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

```bash
npx skills@latest update <name>
```

---

## 4. 五分钟快速上手

装好并跑完 `/setup-matt-pocock-skills` 之后，从这三个入口开始就够了：

### 入口一：不知道用哪个，就问路由

```
/ask-matt 我手上有一堆 GitHub issue 没处理，该从哪个技能开始？
```

`/ask-matt` 是官方路由器：你描述处境，它推荐技能和路径。它在回答前会先读对应技能的 `SKILL.md` 原文，所以答案可靠。**新手完全可以把它当默认入口**。

### 入口二：要做一个改动，先被拷问

```
/grill-with-docs 我想给结账流程加一个优惠券功能
```

代理会一轮一轮向你提问（每个问题附它的推荐答案，回复"yes"即采纳推荐）。回答完，你会得到一个想清楚了的需求，以及顺手积累的 `GLOSSARY.md` 词条和 ADR。**作者建议每次改动都从这里开始**。

### 入口三：直接写代码，测试先行

```
/tdd 给购物车加一个"清空"按钮
```

代理会先和你确认测试接缝，然后进入红（写失败测试）-绿（最少代码让测试通过）-重构循环。

这三步走顺了，再往下读第 5 章的完整流程。

---

## 5. 核心工作流：从想法到上线

这是大多数工作走的**主流程**（main flow）：你有一个想法，想把它建出来。

### 5.1 流程总览

```
想法
 │
 ▼
/grill-with-docs   (有工作目录就用它；没有用 /grill-me)
 │    拷问式访谈，磨尖想法，顺手写 GLOSSARY.md 和 ADR
 │
 ├─ 有问题需要"可运行的答案"才能定案？
 │    是 → /handoff 出去 → 新会话里 /prototype → /handoff 把结论带回来
 │
 ├─ 这活一个会话建得完吗？
 │    否 → /to-spec (把对话沉淀成规格, 发到追踪器)
 │           → /to-tickets (拆成带阻塞边的曳光弹工单)
 │              ├─ 逐张 /implement (每张票新开一个干净上下文, /clear 间隔)
 │              └─ 或 /implement-spec (一次编排整个规格, 子代理并行扫前沿)
 │    是 → 就在这个会话里直接 /implement
 │
 │         两种路线内部都一样:
 │         /tdd 在预定接缝上做红绿循环, /code-review 双轴评审收尾
 ▼
写 PR 时: /pr 自动触发, 塑造 PR 描述
 │
 ▼
/retro   (复盘本次会话, 改进代理的"环境"而不是代码)
```

### 5.2 逐步详解

**第 1 步：`/grill-with-docs` 磨尖想法**

只要你在一个工作目录（有仓库）里，就从这里开始。它做两件事：跑 `/grilling` 拷问访谈（事实代理查、决策你做、按轮次推进前沿），同时跑 `/domain-modeling` 把学到的东西落成文档（`GLOSSARY.md` 词条、ADR）。它是有状态的：这次会话学到的词，下次会话还在。

没有工作目录（比如在聊天里打磨一篇文章的思路）就用无状态的 `/grill-me`，同样凶狠，但不留文档。

**第 2 步（分支）：需要可运行答案的问题先做原型**

有些问题纸上定不了案：这个状态模型手感对不对？这个 UI 长什么样才好？这时走一次原型支线：

1. `/handoff` 把当前对话压缩成一份便携交接文档（存在系统临时目录）
2. 开一个新会话，喂给它那份文档，跑 `/prototype` 用一次性代码回答问题
3. 再 `/handoff` 把结论带回原会话，继续主线

原型是"一次性"的写作约束，不是承诺销毁：结论会折叠进真实代码，原型本身会存在 `prototype/<name>` 分支上作为一手来源，被实现工单引用。

**第 3 步（分支）：规格、工单、实现**

- **多会话的工程**：`/to-spec` 把当前对话（不重新面试，纯综合）沉淀成一份规格并发到追踪器，自动挂 `ready-for-agent` 标签。接着 `/to-tickets` 把规格拆成曳光弹工单，每张声明自己的阻塞边（GitHub 上会建成源 issue 的 sub-issue，阻塞边是原生链接）。然后二选一：
  - **逐张 `/implement <ticket>`**：每张票在一个全新上下文里实现，做完 `/clear` 再下一张。本地追踪器时按"阻塞者优先"手工推进；真实追踪器时哪张不被阻塞就能抓哪张
  - **`/implement-spec` 一把梭**：把工单当任务图，在就绪前沿上并行派实现子代理（各自独立 worktree 和分支），全部落在一条集成分支上，最后整体跑一次 `/code-review`。适合你想当编排者而不是逐张亲自驱动时
- **一个会话建得完的小活**：直接 `/implement`，就在当前上下文里干

不管哪条路，代码都由 `/tdd`（一次一个红绿切片）写成，收尾由 `/code-review`（Standards 轴查是否符合本仓库编码规范，Spec 轴查是否忠实实现了源工单，两个子代理并行跑互不污染）把门。`/tdd` 和 `/code-review` 也可以单独用：前者适合"不要完整规格、就想测试先行地建一个具体行为"，后者适合任何时点对着某个基准点（commit、分支、merge-base）评审 diff。

**第 4 步：PR 与复盘**

工单要上 PR 时，`/pr`（模型调用，自动触发）塑造 PR 正文：一个最小的可视化摘要、能证明它工作的前后对比证据、一次合并风险判断（单向门还是双向门，波及面多大）。

`/retro` 收闭环。构建结束后（尤其是跑偏过的构建），它回顾整个会话，建议改进代理的**环境**而不是代码：导航指针、自动化检查、`/code-review` 依赖的编码规范、steering 文件、工具。机械性错误变成确定性检查，判断题变成编码规范。下一次构建从一个更好的环境出发。

### 5.3 上下文卫生（很重要）

- **第 1 至 3 步保持在同一个不间断的上下文窗口里**（`/to-tickets` 之前不要 compact、不要 clear），让拷问、规格、工单建立在同一份思考上
- **每张票的 `/implement` 都从全新上下文开始**，只带着工单本身干活，做完即弃
- `/retro` 要在被回顾的那个会话里跑，跑完再 clear
- 会话逼近**智能区**上限（约 150k token）时别硬撑 degraded 状态：在最近的阶段边界 `/compact` 后继续

**阶段边界五选项**（一个会话内两阶段之间的选择，从省到贵）：

| 选项 | 适用 |
| --- | --- |
| **继续（Continue）** | 留在原地。零成本，先排除这个再想别的 |
| **`/clear`** | 这里的事对接下来毫无影响，清空窗口 |
| **`/handoff`** | 写一份便携 Markdown 交接文档。只在四种情况用：换代理工具、换目录、交给同事、阶段中途分叉出一个支线任务。买的是可移植性 |
| **子代理（subagent）** | 把一个收窄的任务派到独立窗口，拿回一份报告 |
| **`/compact`** | 压缩当前上下文并以此开新会话。默认选项，但它是树的底端，不是第一反应 |

---

## 6. 三条上匝道

一个起始处境产生工作，然后汇入主流程。

### 6.1 `/triage`：bug 和请求堆成山

外来 issue（bug 报告、功能请求、外部 PR）在追踪器里堆着，用 `/triage` 推着它们过一个分诊角色状态机：核实、归类、必要时拷问报告人，最后写成**代理就绪**（agent-ready）的简报，打上 `ready-for-agent` 标签，之后由 `/implement` 接手。

注意两条边界：

- **只分诊不是你创建的 issue**。`/to-tickets` 产出的工单本来就代理就绪，不要再分诊
- 分诊期间代理发出的每条评论都带"AI 生成"免责声明，且每个 issue 要对照仓库的 `SCOPE.md` 判断去留

五个规范分诊角色（在 `docs/agents/triage-labels.md` 里映射到你追踪器的实际标签）：

| 角色 | 含义 |
| --- | --- |
| `needs-triage` | 待维护者评估 |
| `needs-info` | 等报告人补充信息 |
| `ready-for-agent` | 规格完整，代理可以离线开干 |
| `ready-for-human` | 需要人来实现 |
| `wontfix` | 不处理 |

### 6.2 `/diagnosing-bugs`：有东西坏了

给硬 bug 用的：第一眼看不出毛病的、偶发飘忽的、两个已知良好版本之间悄悄爬进来的回归。

它的铁律是**在没有一条"对这个 bug 变红"的紧反馈回路（一条命令、明确失败）之前拒绝建立任何理论**。Phase 1（建回路）就是这个技能的灵魂，其余都是机械操作：最小化、提出假设、插桩、修复、补回归测试。它展示的命令和输出一律先脱敏。修完之后在同一个会话里跑 `/retro` 问"什么能预防这个 bug"；如果真正的发现是"没有好接缝能把它锁住"，那就是 `/improve-codebase-architecture` 的活了。

### 6.3 `/wayfinder`：巨大而朦胧的工程

绿地项目或者超大功能，一个代理会话装不下、通往目的地的路还看不见时用。它是全仓库认知负担最重的流，**省着用**，界定清楚的功能不要上它。

它把路画成追踪器上的一张**共享地图**（一个带 `wayfinder:map` 标签的 issue，地图是索引不是存储，决策细节住在子工单里），然后逐个解决**决策工单**（解决出来的是决策，不是交付物），直到雾被推干净、路清晰为止。

地图清了它就**交棒，不施工**：汇入主流程的 `/to-spec`，把地图上互相链接的决策塌缩成一份可施工的计划，然后照常 `/to-tickets`、`/implement`。跳过塌缩直接 `/implement` 会把链接着的细节扔掉，只有工程最后真的变小了才可以直通。

---

## 7. 代码库健康与词汇层

### 7.1 `/improve-codebase-architecture`：代码库保养

不是功能开发，是日常维护。它勘察代码库里的**深化机会**（把浅模块变深模块的重构机会），以可视化 HTML 报告呈现，你挑一个，它就地拷问着把它做掉。目标只有两个：可测试性和 AI 可导航性。

- 先圈范围再扫描（YAGNI）：你指了方向就听你的；否则沿着 `git log` 找热点，最近常改的地方优先
- 用 `/codebase-design` 的深模块词汇说话（不飘向"组件""服务""API"这类词）
- 选出的深化机会就是一条新想法，带回主流程的 `/grill-with-docs`
- 隔几天跑一次；对真正老旧的代码库它能找到真候选，但它不会替你解泥球

### 7.2 垫在最底下的两个词汇技能

两个模型调用的参考技能，各自是自己词汇的唯一权威。其它技能会把它们拉进来；你也可以在"问题出在词汇而不是流程"时直接用：

- **`/domain-modeling`**：磨锐项目的**领域**语言。挑战一个含糊的词、解决一个身兼三职的 overloaded 词（比如"账户"）、把一个难回头的决策写成 ADR、更新 `GLOSSARY.md`。注意：只是"读一下 `GLOSSARY.md` 用词"不算这个技能，主动的挑战、边界场景压力测试、写 ADR 才算
- **`/codebase-design`**：深模块设计词汇（module、interface、depth、seam、adapter、leverage、locality），决定一个模块的**形状**：小接口背后藏大量行为、放在干净的接缝上、透过接口可测。`/tdd` 和 `/improve-codebase-architecture` 都说这门语言

---

## 8. 技能速查总表

共 27 个正式技能。"调用方"列：**用户**表示只能你手动触发，**模型**表示代理也会自动触发。

### engineering 桶（20 个）

| 技能 | 调用方 | 一句话 |
| --- | --- | --- |
| `/ask-matt` | 用户 | 路由器：描述处境，推荐该用哪个技能或流 |
| `/setup-matt-pocock-skills` | 用户 | 每仓库跑一次：配置追踪器、分诊标签、文档布局 |
| `/grill-with-docs` | 用户 | 拷问访谈 + 顺手建设领域文档（`GLOSSARY.md`、ADR） |
| `/triage` | 用户 | 推外来 issue 过分诊状态机，产出代理就绪简报 |
| `/to-spec` | 用户 | 把当前对话沉淀成规格发上追踪器（不面试，纯综合） |
| `/to-tickets` | 用户 | 把计划拆成带阻塞边的曳光弹工单 |
| `/implement` | 用户 | 实现一张工单或一份规格，内部驱动 `/tdd` 和 `/code-review` |
| `/implement-spec` | 用户 | 在一条集成分支上并行编排实现整个规格的任务图 |
| `/wayfinder` | 用户 | 超大朦胧工程画决策地图，逐题拨雾 |
| `/improve-codebase-architecture` | 用户 | 勘察深化机会，出 HTML 报告，挑一个拷问着做掉 |
| `/retro` | 用户 | 会话复盘，改进代理环境（不是改代码） |
| `/tdd` | 模型 | 红-绿-重构循环，一次一个垂直切片 |
| `/code-review` | 模型 | 双轴评审（规范轴 + 规格轴），并行子代理 |
| `/diagnosing-bugs` | 模型 | 硬 bug 诊断循环：先建对这个 bug 变红的反馈回路 |
| `/prototype` | 模型 | 一次性原型回答一个设计问题（HTML 单文件或 UI 变体） |
| `/research` | 模型 | 后台代理对着一手来源调研，产出带引用的 Markdown |
| `/domain-modeling` | 模型 | 建设和磨锐领域模型，维护 `GLOSSARY.md` 和 ADR |
| `/codebase-design` | 模型 | 深模块设计词汇表：接缝、深度、适配器、杠杆率 |
| `/pr` | 模型 | PR 正文的形状：最小可视化摘要 + 前后证据 + 合并风险判断 |
| `/wizard` | 模型 | 生成交互式 bash 向导，带人走过只有人能做的步骤 |

### productivity 桶（7 个）

| 技能 | 调用方 | 一句话 |
| --- | --- | --- |
| `/grill-me` | 用户 | 无状态拷问访谈（无工作目录时用） |
| `/handoff` | 用户 | 把当前对话压缩成交接文档，给下一个代理接棒 |
| `/teach` | 用户 | 多会话教学，当前目录作为有状态教学工作区 |
| `/to-questionnaire` | 用户 | 把你答不了的决策变成一份给"唯一知情者"的问卷 |
| `/wait-what` | 用户 | 刚才那条消息没看懂：用共享语言重新讲一遍 |
| `/grilling` | 模型 | 拷问原语本身（轮次、前沿、推荐答案），各拷问技能的内核 |
| `/writing-for-agents` | 模型 | 写代理消费的文档（技能、AGENTS.md、被指针引用的文档）的参考 |

---

## 9. 技能详解

每个技能给出四项：**做什么**、**何时用**、**怎么用**（示例命令）、**配合**（与谁协作）。想看官方详尽文档，链接在各节末尾。

### 9.1 engineering 桶：用户调用

#### `/ask-matt`：路由器

- **做什么**：你描述处境，它读完相关技能的 `SKILL.md` 原文后推荐技能和路径。它脑子里装着整个仓库的流地图
- **何时用**：任何时候不确定该用哪个技能。新手把它当默认入口
- **怎么用**：`/ask-matt 我有一个新功能的想法，接下来该干什么？`
- **配合**：它只推荐，由你去触发推荐到的用户调用技能

[SKILL.md](../skills/engineering/ask-matt/SKILL.md) | [中文文档页](./engineering/ask-matt.zh-CN.md)

#### `/setup-matt-pocock-skills`：初始化（前置条件）

- **做什么**：探索当前仓库现状（git remote、AGENTS.md/CLAUDE.md、遗留的 `CONTEXT.md`），然后通过问答配置追踪器、分诊标签、领域文档布局，写入 `docs/agents/`
- **何时用**：每个仓库第一次使用工程技能之前，跑一次。配置需要刷新时重跑
- **怎么用**：`/setup-matt-pocock-skills`
- **配合**：`/to-spec`、`/to-tickets`、`/triage`、`/implement` 等都靠它写的配置找追踪器

[SKILL.md](../skills/engineering/setup-matt-pocock-skills/SKILL.md) | [中文文档页](./engineering/setup-matt-pocock-skills.zh-CN.md)

#### `/grill-with-docs`：拷问 + 建文档

- **做什么**：连环访谈磨尖你的计划；同时把学到的词汇写进 `GLOSSARY.md`、把难解释的决策写成 ADR
- **何时用**：在仓库里开始任何改动之前。作者说"每次要用改动时都用它"
- **怎么用**：`/grill-with-docs 我想把用户设置页拆成独立的服务`
- **配合**：内部调用 `/grilling`（访谈）和 `/domain-modeling`（落文档）各一次

[SKILL.md](../skills/engineering/grill-with-docs/SKILL.md) | [中文文档页](./engineering/grill-with-docs.zh-CN.md)

#### `/triage`：分诊

- **做什么**：把外来 issue（和配置允许时的外部 PR）在五个分诊角色间推进：归类、向报告人要信息、必要时拷问，最后写出代理就绪的简报
- **何时用**：追踪器里有不是你创建的原始 issue 时。`/to-tickets` 产出的工单不要再分诊
- **怎么用**：`/triage`（或指向某个 issue，如 `/triage 42`）
- **配合**：产出的 `ready-for-agent` issue 之后交给 `/implement`；它内部也可能跑 `/grilling`

[SKILL.md](../skills/engineering/triage/SKILL.md) | [中文文档页](./engineering/triage.zh-CN.md)

#### `/to-spec`：对话变规格

- **做什么**：把当前对话和代码库理解综合成一份规格（问题陈述、方案、测试接缝等），发布到追踪器并挂 `ready-for-agent`
- **何时用**：一场讨论已经想清楚了，需要沉淀成可执行的规格时。**它不面试**：想不清楚的部分它不会帮你问，那是 `/grill-with-docs` 的事
- **怎么用**：讨论完成后直接 `/to-spec`
- **配合**：上游是 `/grill-with-docs` 或 `/wayfinder` 的地图；下游是 `/to-tickets`

[SKILL.md](../skills/engineering/to-spec/SKILL.md) | [中文文档页](./engineering/to-spec.zh-CN.md)

#### `/to-tickets`：规格变工单

- **做什么**：把计划、规格或当前对话拆成一组曳光弹工单，每张声明自己的阻塞边；本地追踪器写成每票一个文件，真实追踪器建成原生阻塞链接（GitHub 上还建成源 issue 的 sub-issue）
- **何时用**：规格太大，一个会话建不完，需要切成可以逐张干净开工的票
- **怎么用**：`/to-tickets`（可带引用：`/to-tickets 42` 或一个 spec 路径）
- **配合**：上游 `/to-spec`；下游 `/implement`（逐张）或 `/implement-spec`（整批）。它还会寻找预重构机会："先让改动变容易，再做那个容易的改动"

[SKILL.md](../skills/engineering/to-tickets/SKILL.md) | [中文文档页](./engineering/to-tickets.zh-CN.md)

#### `/implement`：实现一张工单

- **做什么**：取出工单（先复述标题确认没拿错），在预定的接缝上驱动 `/tdd` 一次一个切片地实现，定期跑类型检查和单测，收尾跑一次 `/code-review`，然后提交到当前分支
- **何时用**：手头有一张代理就绪的工单，或一个小到不需要规格的改动
- **怎么用**：`/implement 42`（ticket 引用），或者直接描述小任务 `/implement 把按钮文案改成"清空购物车"`
- **配合**：被 `/to-tickets` 和 `/triage` 喂；内部调用 `/tdd` 和 `/code-review`。多张票时每张新开上下文，票间 `/clear`

[SKILL.md](../skills/engineering/implement/SKILL.md) | [中文文档页](./engineering/implement.zh-CN.md)

#### `/implement-spec`：一次编排整个规格

- **做什么**：把规格的工单读成任务图，创建集成分支，在每个"就绪前沿"上并行派后台实现子代理（各自新开上下文窗口、独立 worktree 和分支），通过上下文指针（而不是复制信息）通信，最后对整条集成分支跑一次 `/code-review`
- **何时用**：你宁愿当编排者，不想逐张亲自驱动时；工单数量多、并行度可观时收益最大
- **怎么用**：`/implement-spec`（需要先有 spec 和 tickets，否则它会提示你先跑 `/setup-matt-pocock-skills`）
- **配合**：上游 `/to-spec` + `/to-tickets`；每个实现子代理内部驱动 `/tdd`；可选先派一个探索子代理把调研笔记存到仓库外共享

[SKILL.md](../skills/engineering/implement-spec/SKILL.md) | [中文文档页](./engineering/implement-spec.zh-CN.md)

#### `/wayfinder`：超大工程拨雾

- **做什么**：把一个装不进一个会话的朦胧工程画成追踪器上的共享地图（地图是索引，决策住在子工单里），然后逐个解决决策工单，产出决策而非交付物，直到路清晰
- **何时用**：绿地项目或超大功能，从这里到目的地看不见路。界定清楚的功能不要用，它更慢更重
- **怎么用**：`/wayfinder 我想把整个单体迁移到事件溯源架构`
- **配合**：地图清了交给 `/to-spec` 塌缩成计划（别跳过塌缩直通 `/implement`）；实现中途的执行冲动通常说明到了地图边缘、该交棒了

[SKILL.md](../skills/engineering/wayfinder/SKILL.md) | [中文文档页](./engineering/wayfinder.zh-CN.md)

#### `/improve-codebase-architecture`：勘察深化机会

- **做什么**：先圈范围（你指方向，或沿 `git log` 找热点），派一个新上下文的子代理走查代码库记下摩擦点，把深化机会整理成可视化 HTML 报告；你挑一个，它拷问着推进
- **何时用**：有闲工夫就跑（建议每隔几天一次），让代码库保持对代理友好。它是勘察不是救援，不会替你解泥球
- **怎么用**：`/improve-codebase-architecture`（可带方向：`/improve-codebase-architecture 重点是 src/payments`）
- **配合**：用 `/codebase-design` 的词汇；尊重 `GLOSSARY.md` 和 ADR（不重新审判已有决策）；选出的机会带回 `/grill-with-docs` 进主流程

[SKILL.md](../skills/engineering/improve-codebase-architecture/SKILL.md) | [中文文档页](./engineering/improve-codebase-architecture.zh-CN.md)

#### `/retro`：会话复盘

- **做什么**：回顾本次会话，按严重程度排序，建议改进代理的**环境**：导航指针、自动化检查、编码规范、steering 文件、工具
- **何时用**：一次构建之后，尤其是跑偏的构建。`/diagnosing-bugs` 修完 bug 后紧接着跑一次问"什么能预防它"。要在被回顾的会话里跑，跑完再 clear
- **怎么用**：`/retro`
- **配合**：机械错误沉淀成自动化检查，判断题沉淀成 `/code-review` 用的编码规范；发现"没有好接缝"时转介 `/improve-codebase-architecture`

[SKILL.md](../skills/engineering/retro/SKILL.md) | [中文文档页](./engineering/retro.zh-CN.md)

### 9.2 engineering 桶：模型调用

#### `/tdd`：测试驱动开发

- **做什么**：红-绿-重构循环。写测试前先和你确认**测试接缝**（每个接缝附一行说明：能抓到什么、漏掉什么），测试只在接缝上、像规格一样可读；提供反模式清单和 mock 指南
- **何时用**：测试先行地建一个具体行为或修一个 bug；说"红绿重构"或要集成测试时它也会自己来
- **怎么用**：`/tdd 给购物车加优惠券功能`（也可以不输命令，代理遇到匹配任务自动触发）
- **配合**：接口形状本身存疑时调用 `/codebase-design` 拿词汇；`/implement` 和 `/implement-spec` 在预定接缝上驱动它

[SKILL.md](../skills/engineering/tdd/SKILL.md) | [中文文档页](./engineering/tdd.zh-CN.md)

#### `/code-review`：双轴评审

- **做什么**：对着你给的基准点（commit、分支、tag、merge-base）评审 diff，两个子代理并行：**Standards 轴**查是否符合仓库编码规范（自动找 `CODING_STANDARDS.md`、`CONTRIBUTING.md` 等）加一条 Fowler 坏味道基线；**Spec 轴**查是否忠实实现了源 issue/规格
- **何时用**：评审分支、PR 或进行中的改动；说"review since X"时自动触发
- **怎么用**：`/code-review 对比 main`（基准点随口说即可）
- **配合**：`/implement` 每张票收尾各跑一次，`/implement-spec` 对集成分支整体跑一次

[SKILL.md](../skills/engineering/code-review/SKILL.md) | [中文文档页](./engineering/code-review.zh-CN.md)

#### `/diagnosing-bugs`：诊断循环

- **做什么**：纪律化的分阶段诊断：先不惜代价建一条"对这个 bug 变红"的紧反馈回路（失败测试、curl 脚本、脚本化复现都行），然后最小化、提假设、插桩、修复、留回归测试。展示的一切命令和输出先脱敏
- **何时用**：硬 bug 和性能回归：偶发的、第一眼看不透的、悄悄爬进来的回归。说"diagnose"、"debug this"即触发
- **怎么用**：`/diagnosing-bugs 线上偶发 500，日志里没有堆栈`
- **配合**：修完同会话跑 `/retro`；发现缺好接缝时转 `/improve-codebase-architecture`

[SKILL.md](../skills/engineering/diagnosing-bugs/SKILL.md) | [中文文档页](./engineering/diagnosing-bugs.zh-CN.md)

#### `/prototype`：一次性原型

- **做什么**：用一次性代码回答一个设计问题。状态模型或业务逻辑问题产出一个可分享的 HTML 单文件；UI 问题产出一组差异悬殊的变体挂在同一路由下切换
- **何时用**：一个设计问题纸上定不了案，必须跑起来看。典型：这个状态机手感对吗？这个界面该长什么样？
- **怎么用**：`/prototype 验证一下拖拽排序的状态模型`
- **配合**：主流程第 2 步的支线（两侧用 `/handoff` 桥接）；原型存 `prototype/<name>` 分支，实现工单引用它

[SKILL.md](../skills/engineering/prototype/SKILL.md) | [中文文档页](./engineering/prototype.zh-CN.md)

#### `/research`：后台调研

- **做什么**：把阅读跑腿活派给一个后台代理：只对着一手来源（官方文档、源码）调查，把带引用的结论写成 Markdown 文件留在仓库里。你可以同时继续干别的
- **何时用**：需要查证文档或 API 事实、收集资料，但不想停下手里活的时候
- **怎么用**：`/research 调研 SQLite 和 Postgres 在边缘部署下的取舍`
- **配合**：产出的文件带进 `/grill-with-docs` 喂思考（研究喂思考，不替代思考）

[SKILL.md](../skills/engineering/research/SKILL.md) | [中文文档页](./engineering/research.zh-CN.md)

#### `/domain-modeling`：领域建模

- **做什么**：主动建设和磨锐领域模型：拿含糊术语对照词汇表挑战、用边界场景做压力测试、把难回头的决策写成 ADR、就地更新 `GLOSSARY.md`
- **何时用**：讨论代码库术语、写或改 `GLOSSARY.md`、记录或修改 ADR 时（自动触发）；或你主动想磨锐一个词
- **怎么用**：`/domain-modeling "账户"这个词在我们代码库里身兼三职，帮我们理清`
- **配合**：被 `/grill-with-docs` 内部调用。只是被动读词汇表不算它

[SKILL.md](../skills/engineering/domain-modeling/SKILL.md) | [中文文档页](./engineering/domain-modeling.zh-CN.md)

#### `/codebase-design`：深模块词汇

- **做什么**：深模块设计的共享词汇和原则：module、interface、depth、seam、adapter、leverage、locality，加上删除测试、"接口即测试面"、"一个适配器是假想接缝、两个才是真接缝"等原则
- **何时用**：设计或改进一个模块的接口、决定接缝放哪、让代码更可测或对 AI 更可导航时。它是查阅的参考，不是跑的会话
- **怎么用**：`/codebase-design 帮我决定这个解析器模块的接口应该暴露什么`
- **配合**：`/tdd`、`/improve-codebase-architecture` 都说它的语言

[SKILL.md](../skills/engineering/codebase-design/SKILL.md) | [中文文档页](./engineering/codebase-design.zh-CN.md)

#### `/pr`：PR 正文

- **做什么**：塑造 PR 正文的形状：一个让改动一目了然的最小可视化摘要、证明它工作的前后对比证据、一次合并风险判断（单向门还是双向门，波及面多大）
- **何时用**：写任何 PR 正文时，代理自动触发
- **怎么用**：一般不用手动输；说"帮我写 PR 描述"即可
- **配合**：来自 Humanlayer 的 show-me 技能，作者注明了致谢

[SKILL.md](../skills/engineering/pr/SKILL.md) | [中文文档页](./engineering/pr.zh-CN.md)

#### `/wizard`：人类步骤向导

- **做什么**：生成交互式 bash 向导，带一个人走完只有人能做的步骤：逐个打开 URL、捕获输入值、写进 `.env` 和 GitHub secrets
- **何时用**：开基础设施、配凭据或 CI secrets、走一个陌生第三方控制台、跑一次性迁移或割接。代理自己能做的不要用它
- **怎么用**：一般代理碰到只有你能过的墙时自动触发；也可手动 `/wizard 配置 Stripe 密钥和 CI secrets`
- **配合**：与 `.env`、GitHub secrets 打交道的流程

[SKILL.md](../skills/engineering/wizard/SKILL.md) | [中文文档页](./engineering/wizard.zh-CN.md)

### 9.3 productivity 桶：用户调用

#### `/grill-me`：无状态拷问

- **做什么**：与 `/grill-with-docs` 同样的连环访谈，但无状态：不写 `GLOSSARY.md`、不留任何本地文档
- **何时用**：**没有工作目录**时：打磨一个计划、一个设计、一篇文章的思路。在仓库里就别用它，`/grill-with-docs` 严格更优
- **怎么用**：`/grill-me 我在考虑要不要把课程平台迁到另一个托管商`
- **配合**：内部调用 `/grilling`

[SKILL.md](../skills/productivity/grill-me/SKILL.md) | [文档页](./productivity/grill-me.md)

#### `/handoff`：会话交接

- **做什么**：把当前对话压缩成一份交接文档，存到系统临时目录（`$TMPDIR`，否则 `/tmp`；Windows 是 `%TEMP%`），内含"建议技能"清单；只引用已有工件（规格、ADR、issue、commit）不复制；敏感信息脱敏
- **何时用**：换代理工具、换目录、交给同事，或阶段中途分叉支线任务。买的是可移植性
- **怎么用**：`/handoff 下一个会话用来继续实现购物车功能`（参数说明下个会话的用途）
- **配合**：原型支线的两侧桥；阶段边界五选项之一

[SKILL.md](../skills/productivity/handoff/SKILL.md) | [文档页](./productivity/handoff.md)

#### `/teach`：多会话教学

- **做什么**：把当前目录当成有状态的教学工作区，跨多次会话教你一个主题。工作区里有：`MISSION.md`（你为什么想学，一切教学的锚点）、`lessons/`（每课一个自包含 HTML）、`reference/`（可打印的速查材料）、`learning-records/`（学习记录，用来算你的最近发展区）、`RESOURCES.md`、`NOTES.md`
- **何时用**：想系统学会一个概念或技能，而不是问一嘴就走
- **怎么用**：`/teach 我想学习 React 的并发渲染`
- **配合**：独立使用；出题时正确答案的位置会随机换

[SKILL.md](../skills/productivity/teach/SKILL.md) | [文档页](./productivity/teach.md)

#### `/to-questionnaire`：给知情者的问卷

- **做什么**：把一个你独自答不了的决策变成一份 Markdown 问卷，交给唯一能答的人异步填，或开会一起填。它拷问你的是"发送"（发给谁、要回什么），不是主题本身；问题瞄准对方知道而你需要的缺口
- **何时用**：拦住你的东西既不在你脑子里也不在代码库里，而在别人脑子里
- **怎么用**：`/to-questionnaire 数据迁移方案需要 CTO 拍板`（产出 `to-questionnaire-<slug>.md`）
- **配合**：收回来的材料是 `/grill-with-docs` 或 `/to-spec` 的输入

[SKILL.md](../skills/productivity/to-questionnaire/SKILL.md) | [文档页](./productivity/to-questionnaire.md)

#### `/wait-what`：没听懂，重讲

- **做什么**：让代理把刚才那条没落地的消息重新讲一遍：补上你缺的上下文，用简化技术英语，用 `GLOSSARY.md` 的共享语言
- **何时用**：对话中途，任何技能里，某条消息让你懵了的那一刻。事后补救；事前预防靠 `/grill-with-docs` 早点把共享语言定下来
- **怎么用**：`/wait-what`

[SKILL.md](../skills/productivity/wait-what/SKILL.md) | [文档页](./productivity/wait-what.md)

### 9.4 productivity 桶：模型调用

#### `/grilling`：拷问原语

- **做什么**：连环访谈本身：把决策映射成设计树，按**轮次**推进，每轮问完整个**前沿**（前置已定案、现在能问的问题），每个问题带编号和推荐答案，措辞保证"yes"即采纳推荐。事实归代理，决策归你
- **何时用**：直接用它的场合只有一种：你想要一场没有包装的纯访谈。通常你用的是它的包装版
- **怎么用**：`/grilling 直接面试我这个重构计划`
- **配合**：`/grill-me`、`/grill-with-docs` 是两个具名入口；`/triage`、`/wayfinder`、`/improve-codebase-architecture` 内部都跑它

[SKILL.md](../skills/productivity/grilling/SKILL.md) | [文档页](./productivity/grilling.md)

#### `/writing-for-agents`：写给代理看

- **做什么**：写代理消费的文档的参考：技能、`AGENTS.md`/`CLAUDE.md`、任何被指针引用的文档。核心工具是**上下文指针**（context pointer）：一个指针的措辞，而不是它的目标，决定代理何时、多可靠地够到那份材料。配套"两种负载预算"等修剪纪律
- **何时用**：创建或修改技能、改 `AGENTS.md`/`CLAUDE.md`、写任何要被代理按指针读到的文档
- **怎么用**：`/writing-for-agents 帮我收紧 AGENTS.md 里指向部署文档的那段指针`

[SKILL.md](../skills/productivity/writing-for-agents/SKILL.md) | [文档页](./productivity/writing-for-agents.md)

---

## 10. 常见问题

**Q1：我该从哪个技能开始？**
装好后先 `/setup-matt-pocock-skills`，之后任何时候不确定就 `/ask-matt`。最高频的起点是 `/grill-with-docs`（有仓库）或 `/grill-me`（无仓库）。

**Q2：`/grill-me` 和 `/grill-with-docs` 怎么选？**
看你有没有工作目录。在仓库里一律 `/grill-with-docs`：同样的访谈，还留下 `GLOSSARY.md` 和 ADR 纸面痕迹，严格更优。不在仓库里（聊天里打磨计划、设计、文章）才用 `/grill-me`。

**Q3：`/implement` 和 `/implement-spec` 怎么选？**
想逐张工单亲自驱动、每张票之间自己 `/clear`，用 `/implement`。想当编排者、让子代理并行扫任务图前沿、一次把整个规格落在一条集成分支上，用 `/implement-spec`。小到一个会话建得完的活，直接 `/implement` 就行，不用先出规格。

**Q4：插件和 skills.sh 两条安装路线能同时用吗？**
不能。插件是订阅的只读托管包，skills.sh 是写进你项目的可编辑副本。两个都装，每个技能会出现两份。选一条。

**Q5：哪些技能会被代理自动触发？我要不要主动记？**
不用记。模型调用的技能（见速查表"调用方"列）代理会在匹配任务时自动想起：写 PR 时想起 `/pr`，碰到只有人能做的步骤想起 `/wizard`，说"debug this"时想起 `/diagnosing-bugs`。用户调用的技能永远不会被自动触发，必须你输命令。

**Q6：怎么只装一个技能？**
见 3.3 节：`npx skills@latest add mattpocock/skills --skill=<name>`。in-progress 桶的测试版技能也只能这样单装，它们不随插件分发。

**Q7：`/to-tickets` 产出的工单还需要过 `/triage` 吗？**
不需要。triage 只处理外来 issue（bug 报告、外部请求）。`/to-tickets` 的产出本来就代理就绪，`/to-spec` 发规格时也直接挂了 `ready-for-agent` 标签。

**Q8：没有 GitHub 怎么办？**
`/setup-matt-pocock-skills` 支持本地 Markdown 文件追踪器（工单写成 `.scratch/<feature>/issues/` 下每票一个文件），也支持 GitLab，还能按你的描述配其它追踪器。

**Q9：我能改这些技能吗？**
能，而且作者鼓励（"Hack around with them. Make them your own"）。skills.sh 路线装的就是可编辑副本；或者直接 fork 仓库。个人化的小调整更适合写在你自己的 `CLAUDE.md`/`AGENTS.md` 里，见仓库 `SCOPE.md` 的收录门槛。

**Q10：对话变慢变笨了怎么办？**
可能逼近智能区（约 150k token）。在最近的阶段边界选择：无关就 `/clear`，要便携就 `/handoff`，能收窄就派子代理，其余 `/compact`。核心纪律：拷问到 `/to-tickets` 之间保持一个不间断的上下文，每张票的 `/implement` 从干净上下文开始。

**Q11：`in-progress` 桶里有什么值得关注的？**
测试版技能，公开求反馈，随时会变会没：`loop-me`（跨会话拷问出自己的工作流规格）、`writing-beats`/`writing-fragments`/`writing-shape`（写作三件套）、`claude-handoff`（交接给后台新代理）、`setup-ts-deep-modules`（用 dependency-cruiser 强制深模块布局）、`software-factory`（软件工厂设计的共享语言）、`chief-of-staff`（LLM wiki 式记忆）。用 `npx skills@latest add mattpocock/skills --skill=<name>` 单装。

---

## 11. 附录：给想改造这套技能的人

如果你想在本地开发、修改或给这套技能写工单，以下仓库规则需要知道（完整规则见仓库根 `CLAUDE.md` 与 `.agents/` 目录）：

### 11.1 仓库结构

```
skills/
  engineering/       正式技能（随插件分发）
  productivity/      正式技能（随插件分发）
  in-progress/       测试版，单独安装
  misc/              冻结
  deprecated/        废弃
docs/
  engineering/       每个工程技能一个文档页（含 .zh-CN.md 中文版）
  productivity/      每个效率技能一个文档页
  agents/            setup 产出的仓库配置模板（追踪器、分诊标签）
.agents/             仓库自身的工程文档（install-block、invocation、writing-docs 等）
.claude-plugin/      插件清单（plugin.json 的 skills 数组 = 正式集合）
scripts/link-skills.sh  把技能符号链接到本地 ~/.claude/skills 等目录
```

### 11.2 改动一个正式技能时要同步的地方

添加、重命名或改动 `engineering/`、`productivity/` 里的技能时，一改全改：

1. 该技能的 `SKILL.md`（本体）和同目录 `agents/openai.yaml`（Codex 元数据）
2. 顶层 `README.md` 的技能条目（技能名链接到 `SKILL.md`）
3. `.claude-plugin/plugin.json` 的 `skills` 数组（插件恰好分发正式集合；改完跑 `claude plugin validate . --strict`）
4. 所在桶的 `README.md`（一行描述）
5. `docs/<bucket>/<skill-name>.md` 文档页（四节结构：What it does、When to reach for it、Common questions、It's working if，模板见 `.agents/writing-docs.md`）
6. `ask-matt` 的 `SKILL.md`：路由地图必须保持真实，新技能没提、旧技能还在路由，都算路由器说谎
7. 安装命令必须逐字来自 `.agents/install-block.md`，不自创说法

非正式桶（`misc/`、`in-progress/`）的技能不出现在 README、plugin.json，也没有文档页。

### 11.3 写技能的两条硬规则

- **调用方式二选一**：用户调用技能带 `disable-model-invocation: true`（且 `agents/openai.yaml` 里 `policy.allow_implicit_invocation: false`），描述面向人类；模型调用技能两处都不带，描述保留丰富的触发措辞。两个 harness 里要么都是用户调用，要么都不是
- **技能间依赖用 Skill 工具调用表达**：写"Call the Skill tool with \"tdd\""，一次调用一个技能（要两个就写两次），不写裸 `/skill` 散文，也不写跨目录深链接。用户调用技能永远不能被别的技能调用，它的前置只能写成"告诉用户去跑 `/xxx`"
- 派子代理的技能都要求子代理**新开上下文窗口**（有些 harness 默认 fork 父对话，会毁掉子代理本要的干净上下文）
- 全仓库散文禁用 em-dash（中文写作同理，用逗号、冒号、句号、括号或连接词代替）

### 11.4 本地开发

跑 `scripts/link-skills.sh` 把 `deprecated/` 和 `misc/` 之外的所有技能符号链接进本机的 `~/.claude/skills`、`~/.agents/skills`，之后 `git pull` 即保持最新；增删改名技能后重跑脚本。

### 11.5 提工单的门槛

issue 须同时过两关（详见 `SCOPE.md`）：**观察到真实失败**（你跑了什么、技能做了什么、你期望什么；假设性的"要是能……就好了"不算）和**符合仓库哲学**（不是配置项、不是 harness 分支、不是个人工作流微调）。新技能提案是关闭的：如果行为能由现有技能组合出来，就不加新的。

---

## 12. 延伸阅读

- 顶层 [README.md](../README.md)：官方介绍与安装（英文）
- [`ask-matt` 的 SKILL.md](../skills/engineering/ask-matt/SKILL.md)：官方流地图，本手册第 5 至 7 章的出处
- [docs/engineering/](./engineering/) 与 [docs/productivity/](./productivity/)：每个技能一页的官方文档（engineering 桶有 `.zh-CN.md` 中文版）
- [docs/agents/triage-labels.zh-CN.md](./agents/triage-labels.zh-CN.md)：五个分诊角色的中文说明
- [SCOPE.md](../SCOPE.md)：仓库收录门槛与已拒绝概念
- [AI Hero 技能通讯](https://www.aihero.dev/s/skills-newsletter)：新技能与变更动态（约 6 万订阅者）

---

*本手册由 AI 基于仓库 v1.3.2 的内容整理生成，如与技能原文有出入，以各技能的 `SKILL.md` 为准。*
