# 问题跟踪系统：GitLab

当前仓库的 issue 和规格文档都以 GitLab issue 的形式存放。所有操作都使用 [`glab`](https://gitlab.com/gitlab-org/cli) CLI。

## 约定

- **创建 issue**：`glab issue create --title "..." --description "..."`。多行描述使用 heredoc。传入 `--description -` 可打开编辑器。
- **读取 issue**：`glab issue view <number> --comments`。使用 `-F json` 获取机器可读的输出。
- **列出 issue**：使用 `glab issue list -O json`，并配合适当的 `--label` 筛选条件。
- **在 issue 下评论**：`glab issue note <number> --message "..."`。GitLab 将评论称为“note”。
- **添加 / 移除标签**：`glab issue update <number> --label "..."` / `--unlabel "..."`。多个标签可以用逗号分隔，也可以重复使用该参数。
- **关闭**：`glab issue close <number>`。`glab issue close` 不接受关闭时的评论，因此先用 `glab issue note <number> --message "..."` 发布说明，再关闭。
- **合并请求**：GitLab 将 PR 称为“merge request”。使用 `glab mr create`、`glab mr view`、`glab mr note` 等命令，其形式与 `gh pr ...` 相同，只是用 `mr` 代替 `pr`，用 `note`/`--message` 代替 `comment`/`--body`。

根据 `git remote -v` 推断仓库；在克隆目录中运行时，`glab` 会自动完成这一点。

## 将合并请求纳入分诊

**MRs as a request surface: no.** *（如果当前仓库将外部合并请求视为功能请求，则设置为 `yes`；`/triage` 会读取此标志。）*

设置为 `yes` 时，MR 使用与 issue 相同的标签和状态流程，通过对应的 `glab mr` 命令操作：

- **读取 MR**：执行 `glab mr view <number> --comments`，使用 `glab mr diff <number>` 获取差异。
- **列出待分诊的外部 MR**：执行 `glab mr list -O json`，然后只保留作者不是项目成员或所有者的 MR（贡献者的 MR，而非维护者正在进行的工作）。
- **评论 / 添加标签 / 关闭**：`glab mr note`、`glab mr update --label`/`--unlabel`、`glab mr close`。

与 GitHub 不同，GitLab 对 issue 和 MR 分别编号，因此只要知道维护者指的是哪一类对象，`#42` 就没有歧义。

## 当技能说“发布到问题跟踪系统”时

创建一个 GitLab issue。

## 当技能说“获取相关工单”时

运行 `glab issue view <number> --comments`。

## 探路操作

供 `/wayfinder` 使用。**地图**是单个 issue，**子** issue 则是工单。

- **地图**：带 `wayfinder:map` 标签的单个 issue，正文包含 Notes（笔记）/ Decisions-so-far（迄今的决策）/ Fog（迷雾）。使用 `glab issue create --label wayfinder:map`。（在支持原生 epic 的 GitLab 套餐中，也可用 epic 承载地图；带标签的 issue 则在所有套餐中都可用。）
- **子工单**：描述顶部带有 `Part of #<map>`、关联到地图（`glab issue create --linked-issues <map> --link-type relates_to`），并带有 `wayfinder:<type>` 标签（`research`/`prototype`/`grilling`/`task`）的 issue。领取后，将工单指派给负责推进的开发者。
- **阻塞关系**：使用 GitLab 的**原生阻塞链接**，这是规范且在 UI 中可见的表示方式。通过作为 note 发布的 `/blocked_by #<n>` 快捷操作添加（`glab issue note <child> --message "/blocked_by #<blocker>"`）。原生阻塞链接是 Premium/Ultimate 功能；在免费套餐中（或不可用时），退回到在描述顶部添加一行 `Blocked by: #<n>, #<n>`。所有阻塞项都关闭后，工单才解除阻塞。
- **前沿查询**：使用 `glab api projects/:id/issues/<map>/links` 列出地图的子工单，保留未关闭且带有 `wayfinder:<type>` 标签的 issue；排除存在未关闭阻塞项或已有受理人的工单。未关闭阻塞项可以是原生 `blocked_by` 链接所指向的未关闭 issue（通过 `glab api projects/:id/issues/<child-iid>/links` 查看），或 `Blocked by` 行中的未关闭 issue；按地图顺序选第一个。
- **领取**：`glab issue update <n> --assignee @me`，作为本会话的首次写操作。
- **完成**：执行 `glab issue note <n> --message "<answer>"`，再执行 `glab issue close <n>`，然后向地图的 Decisions-so-far 部分追加上下文指针（要点 + 链接）。
