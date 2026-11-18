# 可观测性平台（Observability platforms）

> 调研于 2026-10，不保证最新。用它定向，然后自己研究当前选项：产品、价格、限额和 beta 变化很快。

这是为填可观测性平台槽位、或深思熟虑地决定"无"而写的定向。各类型按**人在一次运行内部需要看什么**划分：它的日志、它的代理 trace（每次模型调用和工具调用各是一个 span），或那条 trace 与用户系统其余部分的关联。自托管 vs SaaS 横切后三个类型。当运行应根据平台所见采取行动时，把平台变成工单来源：监视器提工单，运行读它。

## 运行器如何接入

每个类型都通过五个钩子之一接收数据。填这个槽位时，把钩子与产品一起点名。

- **宿主 OTel（Harness OTel）**：代理宿主自己导出 OpenTelemetry。Claude Code 和 Claude Agent SDK 接受 `CLAUDE_CODE_ENABLE_TELEMETRY=1` 加 `OTEL_*` 变量，提示词和工具输出需 opt-in；Codex CLI 接受 config.toml 里的一个 `[otel]` 块。（[Claude Agent SDK](https://code.claude.com/docs/en/agent-sdk/observability)、[Claude Code](https://code.claude.com/docs/en/monitoring-usage)、[Codex config](https://developers.openai.com/codex/config-advanced)）
- **平台推送（Platform push）**：模型平台替你发送 trace，如 Claude Managed Agents 发送到 Claude Console 里设置的 OTLP 目的地。（[Claude docs](https://platform.claude.com/docs/en/manage-claude/opentelemetry/api)）
- **SDK 包装（SDK wrapper）**：运行器代码里的一次调用，如 LangSmith 的 `configure_claude_agent_sdk()`。
- **Hook 插件（Hook plugin）**：一个 Claude Code hook 在每次 Stop 时读会话转录并寄出，如 Langfuse 的 Claude Code 插件。
- **网关（Gateway）**：运行器的模型 base URL 指向一个记录日志的代理。零运行器代码，且它只看得见模型调用：宿主内部的工具调用不可见。

OpenTelemetry 是公共线规：宿主 OTel 和平台推送落入任何 OTLP 后端。GenAI 语义约定（`invoke_agent`、`chat`、`execute_tool` span）仍在收敛，span 名称会漂移。（[OTel GenAI agent spans](https://opentelemetry.io/docs/specs/semconv/gen-ai/gen-ai-agent-spans)）

## 类型 1：无（运行器或编排器产品已经显示得够多）

**它是什么**：人在运行器或编排器已展示的地方读每次运行：CI 运行页、沙箱仪表盘、作为运行工件保存的代理转录。无需建设钩子，自托管程度随运行器，费用也捆绑其中。

**工厂何时选它**：每天运行不多、一个人盯着、问题是"它失败了吗，打印了什么？"。大多数工厂从这里起步，当需要跨运行搜索、每次运行的 token 或成本汇总、或超过运行器保留期的留存时，再长大离开。

**起点**：[GitHub Actions run logs](https://docs.github.com/en/organizations/managing-organization-settings/configuring-the-retention-period-for-github-actions-artifacts-and-logs-in-your-organization)、[E2B sandbox logs and metrics](https://e2b.dev/docs)、[Daytona sandbox telemetry](https://www.daytona.io/docs/en/observability/otel-collection/)、[OpenAI Agents SDK traces](https://openai.github.io/openai-agents-python/tracing/)。沙箱产品常通过 OTLP 转发同一批数据，这是通往类型 2 的出口。

**核实**：每个运行器产品的保留天数；有的沙箱留得很少（Daytona：3 天，截至 2026-10）。

## 类型 2：LLM 与代理追踪平台

**它是什么**：围绕代理 trace 构建的后端：每次运行是一棵模型调用与工具调用的树，带 token 成本、提示词和会话，通常上面还有评测和数据集。

**工厂何时选它**：人跨许多运行问"代理为什么那样做？"；一次 retro 运行或一个人在比较多次运行；token 开销需要按运行汇总。自托管开源款可让转录留在内部。

**类型特质**：接受宿主 OTel、SDK 包装或 Hook 插件，常常也接受平台推送。按量计费，而代理运行 span 密集（每次工具调用一个 span），所以按运行而非按请求做预算。

**起点**：[Langfuse](https://langfuse.com/pricing)和 [Arize Phoenix](https://phoenix.arize.com)（开源、可自托管）、[LangSmith](https://www.langchain.com/pricing-langsmith)、[Braintrust](https://www.braintrust.dev/pricing)。

**核实**：免费档限额、计费单位（trace、span、unit 还是 GB；Braintrust 在 2026-03 改成了 GB）、哪一档解锁自托管、以及当前所有者。

## 类型 3：通用 APM 与日志后端

**它是什么**：用户既有的可观测栈（日志、指标、每个服务的分布式 trace），加上一个代理或 LLM 视图。

**工厂何时选它**：团队已经在为它付费；运行需要与应用自身的 trace 关联（Agent SDK 会传播 `TRACEPARENT`，于是运行嵌在其调用方的 trace 里）；或一个监视器工单来源已经在读它。

**类型特质**：宿主 OTel 直达厂商的 OTLP 端点。按 GB、事件或主机计价，所以工厂的量在既有账单上是零头，在新账单上是惊吓。

**起点**：[Datadog Agent Observability](https://www.datadoghq.com/product/llm-observability/)、[Grafana Cloud](https://grafana.com/pricing/)、[Honeycomb](https://www.honeycomb.io/pricing)、[SigNoz](https://signoz.io/pricing/)（开源）。

**核实**：代理视图是 GA 还是预览、哪些宿主有安装指南、免费档量。

## 类型 4：带请求日志的 LLM 网关

**它是什么**：运行器与模型提供商之间的一个代理，记录每个请求和响应，附带路由、密钥、预算和缓存。

**工厂何时选它**：用户想要按运行的支出上限、或多个运行器共用一个密钥库而日志随之而来；或宿主自身没有导出。它只看得见模型调用，工具调用要搭配类型 2 或 3。

**起点**：[LiteLLM Proxy](https://docs.litellm.ai/docs/proxy/logging)（开源，转发日志到 Langfuse 或 OTel）、[Portkey](https://portkey.ai/pricing)。

**核实**：SaaS 的按请求计价、产品是否仍在积极开发（截至 2026-10，Helicone 被收购后进入维护模式）。

## 进一步调研

用决定这个槽位的问题核对每个候选：

1. **运行器或编排器已经显示得够吗？** 把人需要的（跨运行搜索、按运行成本、留存）与运行页或沙箱仪表盘给的作比较。这里是"是"，就是深思熟虑的"无"。
2. **代理宿主如何导出 trace？** 找出宿主支持五个钩子中的哪个，再找产品接受哪些。确认工具调用以 span 到达，提示词与工具输出是否需要 opt-in。
3. **可否自托管？** 许可、哪一档允许、跑在什么上。转录必须留在内部时这是决定性的。
4. **成本形状是什么？** 计费单位、免费档、按每次工具调用一个 span 估算每次运行，再乘以每天运行数。
5. **留存多长？** 各档保留天数，对上运行出问题时人会回看多远。

**答案住在哪**：厂商定价页（单位、档位、留存）；针对具体宿主的厂商集成文档；宿主自己的监控文档（导出什么、哪些旗标是 beta）；OpenTelemetry GenAI 语义约定；产品仓库看许可与发布活跃度；厂商更新日志或博客看所有权变化。

**最快过期的是什么**：免费档限额与价格；beta 与预览旗标（Claude Code trace 导出、Console 平台推送、厂商代理视图）；所有权与维护状态；计费单位；保留天数；GenAI span 名称。
