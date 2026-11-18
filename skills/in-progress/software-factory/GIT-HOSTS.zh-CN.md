# Git 托管（Git hosts）

> 调研于 2026-10，不保证最新。用它定向，然后自己研究当前选项：产品、价格、限额和 beta 变化很快。

## 类型

### 一体化平台

git 托管捆绑了工单跟踪器、充当编排器的 CI 系统、以及托管的运行器，共享一条事件流和一套权限模型。一个产品填满 git 托管、工单跟踪器、编排器和运行器，其 CI 运行页常常还覆盖可观测性平台。每个 PR、评审、标签和检查事件既是 webhook 又是原生触发器，因此这个类型的触发器目录最丰富。

**选它当**：工厂起步小、代码已经住在那里、或用户想要一张账单一个登录。这就是 [SKILL.md](SKILL.md) 里的"全在 GitHub"设计。厂商也发布自己的代理，要么与用户的运行竞争，要么嵌入为其中一员。

- [GitHub](https://docs.github.com/en/rest/using-the-rest-api/rate-limits-for-the-rest-api)：默认之选；Issues、Actions、Apps 和最大的集成生态。
- [GitLab](https://about.gitlab.com/pricing/)：issues、merge request、CI/CD 和 runner 集于一个产品，SaaS 或自管。

### 套件式 git 托管

作为更宽套件中的一个模块出售的 git 托管，工单跟踪器是兄弟产品（Jira、Azure Boards），靠链接和 webhook 相连。跟踪器事件走第二条流到达，于是编排器要连接两个事件源。

**选它当**：组织已经在跑该套件，其人员已经住在 Jira 或 Azure Boards 里。工厂继承那个跟踪器作为自己的人类接口。

- [Bitbucket Cloud](https://support.atlassian.com/bitbucket-cloud/docs/api-request-limits/)：Atlassian 的 git 托管，与 Jira 和 Bitbucket Pipelines 搭配。
- [Azure DevOps（Azure Repos）](https://learn.microsoft.com/en-us/azure/devops/integrate/concepts/rate-limits)：Repos、Boards、Pipelines 和 Entra 身份同属一个组织，因此填满全部四个槽位。
- [AWS CodeCommit](https://aws.amazon.com/blogs/devops/aws-codecommit-returns-to-general-availability/)：配 IAM 身份的裸 git 托管；搭配外部跟踪器和 CI。

### 自托管 git 托管

用户跑在自己基础设施上的 git 托管，常与编排器和运行器同网部署。限额由运营者设定，所以上限就是硬件。凡它填充的槽位，升级、备份和在线率都归用户管。

**选它当**：代码必须留在内网或物理隔离、用户想要零席位成本、或 SaaS API 预算限流了一个繁忙的工厂。Forgejo 和 Gitea 适合小团队，捆绑 issues 加 GitHub-Actions 兼容的 CI；GitLab 自管版和 GitHub Enterprise Server 适合组织，保留其 Apps 和服务账号。

- [Forgejo](https://forgejo.org/2026-10-release-v17-0/)：社区治理、轻量，带 Forgejo Actions。
- [Gitea](https://about.gitea.com/)：轻量，带 Gitea Actions；也有托管版出售。
- [GitLab 自管版](https://about.gitlab.com/install/)和 [GitHub Enterprise Server](https://docs.github.com/en/enterprise-server@latest)：完整平台跑在用户自己的基础设施上。

### 评审优先与去中心化 git 托管

评审模型偏离"分支 + PR"形状的 git 托管：按提交的补丁集加投票标签，或跨用户自运行节点联邦的仓库。大多数工厂工具假设 PR，所以要做好多建胶水的准备。在 Gerrit，一个自动检查运行从 bot 账号投 `Verified`，人投 `Code-Review`。

**选它当**：用户的团队已经这样评审，或看重联邦胜过生态。

- [Gerrit](https://www.gerritcodereview.com/)：补丁集评审加标签投票；Android 和 Chromium 的模型。
- [Tangled](https://blog.tangled.org/ci/)：AT Protocol 上的 git 托管，带堆叠 PR 和自己的 CI runner。

### 代理原生 git 托管

围绕"代理是主要作者"构建的新一代 git 托管：高提交吞吐、以服务端读取取代 clone、按代理署名。整个类型都很早期（截至 2026-10），闸门和限额可能未公开：问厂商。

**选它当**：工厂对一个大仓库跑许多并发运行，clone 时间或 API 限额成为主导。在单个仓库的镜像上试点一个，保留 GitHub 作退路。

- [Cursor Origin](https://cursor.com/origin)：来自 Graphite 团队，带 GitHub 同步和签名的 PR、评审与推送 webhook。
- [GitLab next-gen SCM](https://about.gitlab.com/blog/gitlab-next-gen-scm/)：重建的 GitLab 后端，带免 clone 的代理 API。

### 面向运行的 git 存储

以千计创建的无头、API 优先 git 仓库：分支、提交和 diff，没有 PR、评审或闸门。它们填半个槽位，配每次运行按仓库铸造的短命 token。

**选它当**：运行需要便宜的按次临时仓库或 fork（每张工单一个 fork、一个代理的工作状态），而 PR 仍落在上述类型中某个完整 git 托管上。

- [Cloudflare Artifacts](https://www.cloudflare.com/product/artifacts/)：Workers 上的 git 兼容存储；按任务 fork 一条基线。
- [Pierre code.storage](https://code.storage/docs)：白标 git API，带短命分支。

## 进一步调研

用这些问题核对每个候选：

1. **触发器**：哪些事件（PR 打开、push、评审提交、标签添加、检查完成）以 webhook 到达编排器，投递是否签名？
2. **身份**：运行能否以专属 bot 或应用身份行动？这决定运行可否归因、一个自动评审运行能否批准另一个身份创建的 PR、以及运行自己的推送是否触发下一次运行。
3. **闸门**：必需检查、必需批准和分支保护在用户的套餐和仓库可见性下是否可用，bot 能否进绕过名单？
4. **限额**：每台运行器上的每次运行共享多少 API 预算，按什么单位（installation、token、用户、工作区）计，如何扩展？
5. **填充的槽位**：同一产品覆盖工单跟踪器、编排器、运行器和可观测性平台中的哪几个？

到一手来源里找答案：REST API 与 webhook 参考、限额页、定价与套餐对比页、访问 token 或应用文档、更新日志或发行说明。

最容易过期的：beta 与预览状态（整个代理原生和 git 存储类型）、限额数字、哪个套餐解锁闸门、以及收购、关停和复活之后的可用性。例如截至 2026-10，AWS CodeCommit 已对新客户重新开放，Bitbucket Data Center 只以与 Cloud 的混合许可出售。

## 耐久的坑

设计依赖之前先核实每条仍然成立。

- **GitHub `GITHUB_TOKEN` 会掐断链路。** 由内置 Actions token 引起的事件不会启动新的 workflow 运行（`workflow_dispatch` 和 `repository_dispatch` 除外），因此用它推送的运行会无声地止住下一次运行。从第一天起就给运行一个 GitHub App 身份。
- **闸门常躲在付费套餐后。** 截至 2026-10，GitHub 私有仓库的 rulesets 需要 Pro、Team 或 Enterprise，GitLab 批准规则需要 Premium，Bitbucket 强制合并检查需要 Premium。
- **限额按身份计，bot 的类型会改变它。** 截至 2026-10，GitHub App installation 每小时 5000 次请求，`GITHUB_TOKEN` 是 1000 次；Bitbucket 用户 token停留在基础速率而 access token 可扩展；Azure DevOps 随访问级别提高 bot 的预算。
- **套件式跟踪器意味着两条事件流。** 编排器要自己连接 git 托管和工单跟踪器的事件。
