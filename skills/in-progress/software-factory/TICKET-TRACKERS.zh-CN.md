# 工单跟踪器（Ticket trackers）

> 调研于 2026-10，不保证最新。用它定向，然后自己研究当前选项：产品、价格、限额和 beta 变化很快。

工单跟踪器是工厂的人类接口。下面每个类型都是一个起点：读它是什么、工厂何时选它，然后用[进一步调研](#researching-further)里的问题核对例子。

## 类型

### 1. git 托管内置跟踪器

内置于 git 托管的跟踪器，工单、分支和 PR 共享一个产品、一套权限模型、一套身份。工厂在它小、团队已住在 git 托管上、或仓库公开时选它。这就是"全在 GitHub"设计：git 托管的 CI 无需胶水地填满编排器槽位。标签是最可靠的分流器，因为状态住在单独的看板上。外部开发者在代码所在地提工单；非开发者需要账号，所以用反馈按钮走 API 提单。自托管 git 托管随身携带其跟踪器。

- [GitHub Issues](https://docs.github.com/en/copilot/concepts/agents/about-third-party-agents)：默认之选；面向 Copilot、Claude 和 Codex 的 assign-to-agent。
- [GitLab Issues](https://about.gitlab.com/blog/how-to-use-a-work-item-created-trigger/)：Duo Agent Platform 从 work item 触发的流程。
- [Forgejo](https://forgejo.org/2026-10-release-v17-0/)：自托管，Actions 兼容。

待核实：哪些 assign-to-agent 特性仍在预览、哪些套餐包含它们；原生代理是读后续评论还是只读分派时刻的工单（截至 2026-10，Copilot 只在分派时读）。

### 2. 代理原生开发跟踪器

面向软件团队的独立 SaaS 跟踪器，把代理当作一等受理人，带代理 API 和自己的编码代理。当产品、支持或设计人员安排工作、团队已经住在那里、或想要精致的分诊与规划视图时，工厂选它。"Linear 工单"设计。状态与工作流是一等的，所以"移到 Ready"是一个干净的触发器。代理以人可委派的 app 用户出现，运行能把进度流式写进工单。这里的原生代理是所有类型中最深的，一个产品还能部分填满编排器和运行器槽位。

- [Linear](https://linear.app/developers/agents)：最像为代理而生的跟踪器。
- [Jira](https://support.atlassian.com/jira-software-cloud/docs/collaborate-on-work-items-with-ai-agents/)：状态触发的代理；适合已在 Atlassian 上的组织。
- [Shortcut](https://www.shortcut.com/help/integrations/using-ai-agents)：分派一个 story，或在评论里 @ 一个代理。
- [YouTrack](https://www.jetbrains.com/youtrack/)：工作流即代码；可自托管。

待核实：代理 API 状态（截至 2026-10，Linear 的是开发者预览）；原生代理运行如何计量与计价；代理席位成本；代理 webhook 的确认截止期；哪个套餐解锁面向客户的表单；自托管版的停售日期。

### 3. 开源开发跟踪器

可以跑在自己基础设施上的 Linear 风格跟踪器，旁边还有一个云版。当数据必须留在内部（物理隔离、受监管）、或用户想边购买边阅读和修改跟踪器源码时，工厂选它。按状态、标签和自定义属性分流与类型 2 大同小异；原生代理更年轻。

- [Plane](https://developers.plane.so/dev-tools/agents/overview)：类 Linear；每个 app 一个 bot 用户；代理在评论里回复。
- [Huly](https://github.com/hcengineering/huly-selfhost)：一体化工作区，带双向 GitHub 同步；一套运维负担很重的栈。
- [OpenProject](https://www.openproject.org/)：成熟、经典的项目管理。

待核实：代理特性状态（截至 2026-10，Plane 的是 beta）；哪个版本带访客角色与 intake 表单；许可；自托管栈的运维成本。

### 4. 通用工作工具

数据库或任务列表兼任跟踪器的工作管理或文档工具。当它服务于已经住在那里的非工程团队、或工单多为内容、运营或研究而非代码时，工厂选它。表单是强项：任何有链接的人都能提单。分流靠灵活的数据库属性，所以每个工厂都发明自己的 schema。开发者上下文（diff、PR）在 git 托管上一次点击之遥。仅 SaaS。

- [Notion](https://www.notion.com/help/custom-agents)：数据库即跟踪器；代理在数据库变化时触发。
- [Asana](https://asana.com/product/ai/ai-teammates)：AI Teammates 接走分派的任务。
- [ClickUp](https://clickup.com/brain/cloud-agents)：Cloud Agents 在任务线程里交回一个 PR。
- [Trello](https://developer.atlassian.com/cloud/trello/guides/rest-api/webhooks/)：看板与列表；webhook 加 Butler 自动化。

待核实：webhook 延迟与粒度（截至 2026-10，Notion 批量投递内容更新，晚一两分钟到达）；哪个套餐含代理。

### 5. 仓库原生跟踪器

工单以文件形式存在仓库里（JSONL、Markdown），由代理通过 CLI 或 MCP 服务器编辑，像代码一样合并。当一个开发者本地驱动代理、想让计划进 git，或作为真跟踪器旁边某次运行的工作内存时，工厂选它。对 git 托管的一次 push 是唯一触发器，编排器靠 diff 文件发现变化。身份就是 git 托管的。外部提单是一次提交或 PR，是所有类型中最弱的，所以一旦有外人提工单，工厂就毕业去 git 托管内置跟踪器。

- [Beads](https://github.com/steveyegge/beads)：JSONL 带依赖跟踪，运行能挑下一张未阻塞的工单。
- [Backlog.md](https://github.com/MrLesk/Backlog.md)：Markdown 任务，带看板视图和 MCP 服务器。

### 6. 自建

工厂自己的应用把工单放在自己的数据库里，通常与编排器为邻。"自建跟踪器"设计。当跟踪器*就是*产品（内部平台、面向客户的支持工厂）或没有现成模型合适时，工厂选它。触发器变成函数调用；下面问题里的每条特质都变成要写要维护的代码，这正是本技能偏爱购买的原因。带 webhook 的 [Airtable](https://airtable.com/developers/web/api/webhooks-overview) base 是半成品的中间态。

## 进一步调研

用这些问题核对每个候选。它们决定这个槽位：

- **触发器**：哪些工单变化（创建、打标签、状态移动、分派、评论、提及）会发出编排器能变成运行的 webhook 或事件？多快、多细？
- **分流**：运行能读哪些标签、状态或字段来知道自己的类别（explore、implement、rework）？
- **线程**：人能否在评论里回答代理，那个回答是否触发下一次运行？
- **身份**：有没有 API 加 bot 身份，让运行以自己行事、人能分清代理和同事？bot 是否占席位费？
- **原生代理**：工单能否直接分派给代理？哪些代理、哪个套餐、如何计量？它读什么：分派时刻的工单，还是整个线程？
- **外部提单**：工单来源或客户如何创建工单：表单、邮件、聊天、API？哪个套餐解锁？
- **托管**：仅 SaaS，还是可自托管，什么许可？

答案住在一手来源：API 与 webhook 参考（事件列表、载荷、投递与重试规则）、代理或应用开发者文档、定价页与套餐对比表、更新日志或发行说明。优先于博客文章和比价站。

按最易过期排序：代理特性及其预览或 beta 状态、代理计量与 credit 价格、代理与表单的套餐门槛、限额、以及收购、关停和自托管版的停售日期。Webhook 事件列表与分流模型变化缓慢。

## 耐久的坑

依赖之前先核实每条仍然成立：

- **GitHub 的 `GITHUB_TOKEN` 陷阱**：用默认 `GITHUB_TOKEN` 做的变更不触发新的 workflow 运行。靠给工单打标签或评论来触发下一次运行，要通过 GitHub App token 行事。
- **分派时刻的上下文**：有的原生代理只在分派时读工单，后续上下文要放到 PR 上。
- **代理 webhook 期待快速确认**：编排器立刻确认，之后再启动运行。
- **通用工作工具的批量 webhook**到得晚；需要及时运行的工厂按属性变化触发，或改选别的类型。
