---
name: setup-matt-pocock-skills
description: "为当前仓库配置工程技能：设置问题跟踪系统、分诊标签词汇和领域文档布局。首次使用其他工程技能之前运行一次。"
disable-model-invocation: true
---

# 配置 Matt Pocock 的技能

为各个仓库搭建工程技能所依赖的配置：

- **问题跟踪系统**：issue 存放在哪里（默认为 GitHub；也开箱即用地支持本地 Markdown）
- **分诊标签**：五种规范分诊角色所使用的字符串
- **领域文档**：`GLOSSARY.md` 和 ADR 的存放位置，以及使用这些文档时的阅读规则

这是一个由提示驱动的技能，而不是确定性的脚本。先探索、展示发现、与用户确认，然后再写入。

## 流程

### 1. 探索

查看当前仓库，了解其初始状态。阅读已有内容，不要想当然：

- `git remote -v` 和 `.git/config`：这是 GitHub 仓库吗？具体是哪个？
- 仓库根目录的 `AGENTS.md` 和 `CLAUDE.md`：是否有其中任一文件？是否已经有 `## Agent skills` 部分？
- 仓库根目录的 `GLOSSARY.md` 和 `GLOSSARY-MAP.md`，以及旧版的 `CONTEXT.md` / `CONTEXT-MAP.md`。如果存在旧版文件，提议通过 `git mv` 将其重命名为对应的 `GLOSSARY` 名称，并在 `docs/agents/domain.md` 中更新这些名称。
- `docs/adr/` 和所有 `src/*/docs/adr/` 目录
- `docs/agents/`：是否已存在本技能之前生成的内容？
- `.scratch/`：它表明仓库可能已采用本地 Markdown 问题跟踪约定
- 是否安装了 `triage` 技能？（本技能旁有 `triage` 技能文件夹，或你的可用技能列表中有 `triage`。）这决定是否执行 B 部分。
- 单体仓库（monorepo）的迹象：`pnpm-workspace.yaml`、`package.json` 中的 `workspaces` 字段，或有实际内容且包含各自 `src/` 的 `packages/*`。这些只会出现在真正的大型多包仓库中；没有这些迹象，就意味着单上下文，几乎所有仓库都属于这种情况。

### 2. 展示发现并询问

总结已有内容和缺失内容。然后依次处理各部分。一部分、一个回答，再进入下一部分。

每部分先给出推荐答案，让用户只需一个词就能接受。只有选择确实存在分支时，才用一行解释；探索已确定结果时，直接跳过该部分（未安装 `triage` 时跳过 B 部分，不是 monorepo 时跳过 C 部分）。

**A 部分：问题跟踪系统。**

> 说明：“问题跟踪系统”是当前仓库存放 issue 的地方。`to-tickets`、`triage` 和 `to-spec` 等技能会从中读取和写入内容。它们需要知道，是调用 `gh issue create`、在 `.scratch/` 下写 Markdown 文件，还是遵循你描述的其他工作流程。选择你实际用于跟踪该仓库工作的地方。

默认立场：这些技能最初是为 GitHub 设计的。如果某个 `git remote` 指向 GitHub，就推荐 GitHub。如果某个 `git remote` 指向 GitLab（`gitlab.com` 或自托管主机），就推荐 GitLab。否则（或用户另有偏好时），提供以下选项：

- **GitHub**：issue 存放在仓库的 GitHub Issues 中（使用 `gh` CLI）
- **GitLab**：issue 存放在仓库的 GitLab Issues 中（使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI）
- **本地 Markdown**：issue 以文件形式存放在当前仓库的 `.scratch/<feature>/` 下（适合个人项目或没有远程仓库的项目）
- **其他**（Jira、Linear 等）：请用户用一段话描述工作流程；本技能将以自由文本的形式记录

将选择记录在 `docs/agents/issue-tracker.md` 中。GitHub 和 GitLab 模板包含一个“将 PR 作为请求入口”的标志，默认**关闭**。保持关闭，不主动提起：如果用户希望把外部 PR 纳入分诊队列，以后可以在文件中切换该标志。

GitHub 或 GitLab 问题跟踪系统需要一个可访问的仓库。用户选定其中一种后，如果没有 `git remote` 指向该主机，就向用户获取仓库 URL 并完成连接（必要时执行 `git init`，再执行 `git remote add origin <url>`），然后才能继续。在当前目录内运行 `gh repo view` / `glab repo view` 能输出该项目时，才算完成；如果不能，停下来，告诉用户还缺什么。

**B 部分：分诊标签词汇。** 如果未安装 `triage` 技能（探索阶段已查明），则完全跳过本部分，因为没有安装的技能不需要标签。

如果已安装，只问一个问题：

> 是否保留默认的分诊标签？（推荐：**是**）

默认值为五种规范角色，各标签字符串与角色名称相同：`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`。用户回答**是**时，原样写入。只有用户回答否时，才收集覆盖值，通常是因为其问题跟踪系统已使用其他名称（例如用 `bug:triage` 对应 `needs-triage`），这样 `triage` 会使用现有标签，而不是创建重复标签。

**C 部分：领域文档。** 默认采用**单上下文**（仓库根目录下一个 `GLOSSARY.md` 加上 `docs/adr/`）。这适合几乎所有仓库；无需询问，直接写入。

只有探索发现 monorepo 迹象时，才提供**多上下文**选项（根目录的 `GLOSSARY-MAP.md` 指向各上下文的 `GLOSSARY.md` 文件）。此时再确认用户希望采用哪种布局。

### 3. 确认并修改

向用户展示以下内容的草稿：

- 要加入所选 `CLAUDE.md` / `AGENTS.md` 文件中的 `## Agent skills` 块（选择规则见步骤 4）
- `docs/agents/issue-tracker.md`、`docs/agents/domain.md` 和 `docs/agents/triage-labels.md` 的内容（最后一个仅在安装了 `triage` 时提供）

写入前，让用户有机会修改。

### 4. 写入

**选择要编辑的文件：**

- 如果存在 `CLAUDE.md`，就编辑它。
- 否则，如果存在 `AGENTS.md`，就编辑它。
- 如果两者都不存在，询问用户要创建哪一个；不要替用户选择。

已有 `CLAUDE.md` 时，绝不创建 `AGENTS.md`，反之亦然；始终编辑已经存在的那个文件。

如果所选文件中已有 `## Agent skills` 块，就原地更新其内容，不要重复追加。不要覆盖用户对周围部分所做的修改。

该块如下：

```markdown
## Agent skills

### Issue tracker

[用一行总结在哪里跟踪 issue]。参见 `docs/agents/issue-tracker.md`。

### Triage labels

[用一行总结标签词汇]。参见 `docs/agents/triage-labels.md`。

### Domain docs

[用一行总结布局：“单上下文”或“多上下文”]。参见 `docs/agents/domain.md`。
```

只有安装了 `triage` 且执行过 B 部分时，才包含 `### Triage labels` 子块，并写入 `docs/agents/triage-labels.md`。否则，两者都省略。

如果在 GitHub 或 GitLab 上执行过 B 部分，就创建问题跟踪系统中缺少的每一个已配置标签（`gh label create` / `glab label create`）。

然后，以本技能文件夹中的初始模板为起点，写入文档文件：

- [issue-tracker-github.md](./issue-tracker-github.md)：GitHub 问题跟踪系统
- [issue-tracker-gitlab.md](./issue-tracker-gitlab.md)：GitLab 问题跟踪系统
- [issue-tracker-local.md](./issue-tracker-local.md)：本地 Markdown 问题跟踪系统
- [triage-labels.md](./triage-labels.md)：标签映射（仅在安装了 `triage` 时）
- [domain.md](./domain.md)：领域文档使用规则和布局

对于“其他”问题跟踪系统，根据用户的描述，从头编写 `docs/agents/issue-tracker.md`。

### 5. 完成

告诉用户配置已完成，以及哪些工程技能现在会读取这些文件。说明以后可以直接编辑 `docs/agents/*.md`；只有想切换问题跟踪系统或从头重新配置时，才需要再次运行本技能。
