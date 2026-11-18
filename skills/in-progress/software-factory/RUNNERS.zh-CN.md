# 运行器（Runners）

> 调研于 2026-10，不保证最新。用它定向，然后自己研究当前选项：产品、价格、限额和 beta 变化很快。

运行器是**一次性的**：每次运行都从一台全新机器开始，不留任何东西。五个类型填这个槽位。它们的主要差别在于谁拥有代理循环、谁拥有机器：

| 类型 | 代理循环 | 机器 | 兼填 |
| --- | --- | --- | --- |
| CI runner | 你的，在 CI job 内 | CI 厂商的，或你的 | 编排器、可观测性平台 |
| MicroVM API | 你的，在编排器或运行器上 | 提供商的 | 无 |
| 捆绑代理平台 | 厂商的 | 厂商的 | 部分编排器、可观测性平台 |
| 自带 worker | 厂商的 | 你的 | 部分编排器、可观测性平台 |
| 自有 VPS 或容器 | 你的 | 你的 | 无 |

运行器按运行选择：评审运行用便宜的 CI runner、实现者运行用 microVM，是正常设计。

## CI 运行器

每个 CI job 一台全新 VM 或 pod，由 git 托管自己的工作流引擎启动；代理 CLI 是 job 里的一步。编排器用工作流触发器（issue、PR、日程、手动 dispatch）启动一次运行，并发组防止两次运行撞上同一张工单。托管 runner 天生一次性；自托管 runner 靠临时模式达到同样效果，每 job 一个 pod，用完即删。按分钟计费。

**选它当**：git 托管是 GitHub 或 GitLab、每个触发器都是 git 托管事件或时钟：工作流文件就是编排器，运行页就是可观测性平台，一个产品填三个槽位。

起点：
- [GitHub Actions](https://docs.github.com/en/actions/reference/limits)：全 GitHub 工厂的默认；[定价](https://docs.github.com/en/billing/reference/actions-runner-pricing)。
- [Claude Code GitHub Action](https://code.claude.com/docs/en/github-actions)：Claude Code 作为一步工作流。
- [GitLab hosted runners](https://docs.gitlab.com/ci/runners/hosted_runners/linux/)：每 job 一个临时 VM。
- [Actions Runner Controller](https://docs.github.com/en/actions/concepts/runners/actions-runner-controller)：自托管，在用户的 Kubernetes 上每 job 一个临时 pod。

## MicroVM API

一次 SDK 调用在大约一秒内创建一个隔离的 microVM（厂商叫"沙箱"）；编排器向其中 exec 命令，或在里面启动代理 CLI。按 vCPU 和 RAM 的使用秒数计费。编排器每次运行创建一个，结束时杀掉，并设超时兜底。提供商也卖持久化（暂停/恢复、快照、磁盘）；工厂把它用作缓存，把运行之间的记忆放在工单跟踪器、git 托管和编排器里。

**选它当**：工厂有自己的编排器（一台服务器、一条队列），想要许多短命的并行运行且无需管理机器。比较项：最长运行时长、并发上限、等待模型的时间是否计费：截至 2026-10，Vercel 只按活跃 CPU 计费，等模型的时间在那里几乎免费。

起点：[E2B](https://e2b.dev/pricing)、[Daytona](https://www.daytona.io/pricing)、[Modal Sandboxes](https://modal.com/docs/guide/sandboxes)、[Vercel Sandbox](https://vercel.com/docs/vercel-sandbox/pricing)。

## 捆绑代理平台

自带运行器的托管编码代理：厂商为每次运行开通一台全新 VM 或容器，在里面跑自己的代理循环，并推送分支或 PR。只有分支幸存。运行从 git 托管或工单跟踪器里的 @ 提及或分派、一条聊天消息、一个日程或一次 HTTP 调用开始，所以平台填掉编排器的大半；其转录视图填掉可观测性平台。费用并入席位或订阅，或按运行计。

**选它当**：用户想要最少的活动部件、且满意该厂商的代理。确认其触发器覆盖用户的工单跟踪器。

起点：
- [Claude Code cloud](https://code.claude.com/docs/en/claude-code-on-the-web)，配[定时任务](https://code.claude.com/docs/en/routines)支持 cron、API 和 GitHub 事件触发。
- [Claude Managed Agents](https://platform.claude.com/docs/en/managed-agents/overview)：API 优先；对一个环境发起运行并流式接收事件。
- [Codex cloud](https://developers.openai.com/codex/cloud)：每任务一个容器。
- [Copilot coding agent](https://docs.github.com/en/copilot/concepts/agents/coding-agent/about-coding-agent)：把 issue 分派给 Copilot；它的 runner 是一个 GitHub Actions runner。

## 自带 worker

捆绑平台保留其代理循环和运行队列，但工具调用在一个用户自托管的 worker 上执行。worker 向厂商外呼轮询、认领一次运行、执行、退出：与自托管 CI runner 同形。一次性是你的责任：把 worker 跑在 Kubernetes 或 microVM API 之下，让每次运行得到全新磁盘。费用是厂商的席位或用量，加上你的机器。

**选它当**：用户想要捆绑代理，但运行必须触达内网、数据要留在用户机器上、或要用定制硬件。

起点：[Claude Code self-hosted environments](https://code.claude.com/docs/en/self-hosted-environments)、[Cursor self-hosted pool](https://cursor.com/docs/cloud-agent/self-hosted-pool)、[Copilot coding agent on ARC](https://docs.github.com/en/copilot/how-tos/use-copilot-agents/coding-agent/customize-the-agent-environment)。

## 自有 VPS 或容器

用户租用或自运行的机器，每次运行一个全新容器（或机器）：机器可以活几个月，容器只活一次运行。最长运行时长就是编排器设定的期限。一次性来自 `docker run --rm`、带 `ttlSecondsAfterFinished: 0` 的 Kubernetes Job，或结束时销毁机器；每个缓存都放在下次运行可视为可选的路径上。费用是包月，或运行期间按秒。

**选它当**：运行长、稳、重，按小时成本主导，或已有服务器承载编排器。

起点：[Fly Machines](https://docs.fly.io/machines/overview)（[定价](https://docs.fly.io/about/pricing)）、[Hetzner Cloud](https://www.hetzner.com/cloud/)加 Docker、[Kubernetes Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)。

## 进一步调研

### 决定槽位的问题

手里拿着工厂的运行，用这些核对每个候选：

1. **编排器如何启动一个？** 工作流触发器、SDK 调用、@ 提及、HTTP 调用，还是 worker 认领的厂商队列。它契合设计已点名的那个编排器吗？
2. **最长运行时长和并发上限是多少？** 按套餐档找出默认超时和上限，以及到顶之后会发生什么。拿它们对上设计里最长的运行和最忙的爆发。
3. **成本形状是什么？** 按分钟、按 vCPU 和 RAM 秒、只按活跃 CPU、按运行、按席位，还是包月。等模型的时间计费吗？模型 token 在价内还是另算？
4. **一次性如何达成？** 默认全新、一个临时旗标，还是编排器自己的拆除。有哪些持久化特性，设计能否把它们限制在缓存上？
5. **一次运行能触达什么？** 内网访问、出站防火墙或允许名单、秘密如何到达运行器、还有谁能读它们。
6. **它兼填哪些槽位？** 把它能读的触发器计作编排器覆盖，把它的运行页或转录视图计作可观测性平台覆盖。

### 答案住在哪

- 厂商的**限额或配额页**和**定价页**，优先于落地页。
- **SDK 或 API 参考**里的创建调用：其超时、生命周期与网络参数。
- **安全或网络文档**里的防火墙、允许名单与秘密处理。
- **更新日志或发行说明**里的 beta、预览与 GA 状态，以及套餐门槛（Team、Enterprise）。
- CI 运行器还要看 git 托管的**计费文档**。

### 最快过期的是什么

- **价格与免费额度。** 截至 2026-10，GitHub 宣布过对自托管 runner 按分钟收费，随后又推迟了。
- **beta、预览与早期访问状态**，以及哪些套餐解锁某特性。截至 2026-10，Claude Managed Agents 和 Claude Code 自托管环境都在 beta。
- 各档的**默认超时与最长运行时长**。
- 捆绑平台上的**触发器集成**：哪些工单跟踪器和聊天工具能启动运行。
- **启动耗时**，那是厂商的说法；要紧就自己测。

上面的类型特质（谁拥有循环、谁拥有机器、填哪些槽位）变化缓慢；信任它们胜过这里的任何数字。
