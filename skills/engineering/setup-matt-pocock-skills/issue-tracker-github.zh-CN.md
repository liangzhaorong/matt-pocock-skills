# 问题跟踪系统：GitHub

当前仓库的 issue 和规格文档都以 GitHub issue 的形式存放。所有操作都使用 `gh` CLI。

## 约定

- **创建 issue**：`gh issue create --title "..." --body "..."`。多行正文使用 heredoc。
- **读取 issue**：`gh issue view <number> --json number,title,body,labels,comments`。
- **列出 issue**：使用 `gh issue list --state open --json number,title,body,labels,comments --jq '[.[] | {number, title, body, labels: [.labels[].name], comments: [.comments[].body]}]'`，并配合适当的 `--label` 和 `--state` 筛选条件。
- **将 issue 设为父 issue 的子 issue**：`gh issue create --parent <parent> ...`，或之后执行 `gh issue edit <parent> --add-sub-issue <child>`（`gh` 2.94+）。旧版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<parent>/sub_issues -F sub_issue_id=<child-db-id>`（使用数据库 ID，见下文的**阻塞关系**）。不支持子 issue 时，在子 issue 正文顶部添加 `Part of #<parent>`。
- **阻塞关系**：使用 GitHub 的**原生 issue 依赖关系**，这是规范且在 UI 中可见的表示方式。通过 `gh issue create --blocked-by <n>,<n> ...` 或 `gh issue edit <child> --add-blocked-by <blocker>` 添加一条边（`gh` 2.94+）。旧版 `gh`：`gh api --method POST repos/<owner>/<repo>/issues/<child>/dependencies/blocked_by -F issue_id=<blocker-db-id>`，其中 `<blocker-db-id>` 是阻塞项的数字**数据库 ID**（通过 `gh api repos/<owner>/<repo>/issues/<n> --jq .id` 获取，*不是* `#number` 或 `node_id`）。无法使用依赖关系时，退回到在子 issue 正文顶部添加一行 `Blocked by: #<n>, #<n>`。所有阻塞项都关闭后，工单才解除阻塞。
- **统计未关闭的阻塞项**：`gh api repos/<owner>/<repo>/issues/<n> --jq .issue_dependencies_summary.blocked_by`（仅统计未关闭的阻塞项；0 表示可以开始）。`gh issue view <n> --json blockedBy` 也会列出已关闭的阻塞项，因此需要筛选：`--jq '[.blockedBy.nodes[] | select(.state == "OPEN")] | length'`。
- **在 issue 下评论**：`gh issue comment <number> --body "..."`
- **添加 / 移除标签**：`gh issue edit <number> --add-label "..."` / `--remove-label "..."`
- **关闭**：先用 `gh issue comment <number> --body "..."` 发布说明，再执行 `gh issue close <number>`。对于已经关闭的 issue，`gh issue close --comment` 会丢弃要发布的评论，但仍以退出码 0 结束。

根据 `git remote -v` 推断仓库；在克隆目录中运行时，`gh` 会自动完成这一点。

只向用户自己的仓库提交 issue。除非用户拥有 `mattpocock/skills`（这些技能的来源仓库），否则绝不向该仓库提交 issue。在 fork 仓库中，`gh` 可能默认指向上游父仓库，因此在会话中首次执行 `gh issue create` 之前，先用 `gh repo view --json nameWithOwner` 检查目标；如果不是用户的仓库，就与用户确认。

## 将 Pull Request 纳入分诊

**PRs as a request surface: no.** *（如果当前仓库将外部 PR 视为功能请求，则设置为 `yes`；`/triage` 会读取此标志。）*

设置为 `yes` 时，PR 使用与 issue 相同的标签和状态流程，通过对应的 `gh pr` 命令操作：

- **读取 PR**：先执行 `gh pr view <number>`，再执行 `gh pr view <number> --comments`（不在 TTY 中运行时，`--comments` 只输出评论，不输出正文），使用 `gh pr diff <number>` 获取差异。
- **列出待分诊的外部 PR**：`gh api --paginate 'repos/{owner}/{repo}/pulls?state=open' --jq '.[] | select(.author_association | IN("OWNER","MEMBER","COLLABORATOR") | not) | {number, title, author: .user.login, author_association, labels: [.labels[].name]}'`。
- **评论 / 添加标签 / 关闭**：`gh pr comment`、`gh pr edit --add-label`/`--remove-label`、`gh pr close`。

GitHub 的 issue 和 PR 共用同一编号空间，因此单独一个 `#42` 可能指任意一种：先用 `gh pr view 42` 解析，不成功则改用 `gh issue view 42`。

## 当技能说“发布到问题跟踪系统”时

创建一个 GitHub issue。

## 当技能说“获取相关工单”时

按上文**读取 issue**中的方式读取。

## 探路操作

供 `/wayfinder` 使用。**地图**是单个 issue，**子** issue 则是工单。

- **地图**：带 `wayfinder:map` 标签的单个 issue，正文包含 Notes（笔记）/ Decisions-so-far（迄今的决策）/ Fog（迷雾）。使用 `gh issue create --label wayfinder:map`。
- **子工单**：作为 GitHub 子 issue 关联到地图的 issue（参见**将 issue 设为父 issue 的子 issue**）。未启用子 issue 时，将子工单加入地图正文的任务列表，并在子工单正文顶部添加 `Part of #<map>`。标签为 `wayfinder:<type>`（`research`/`prototype`/`grilling`/`task`）。领取后，将工单指派给负责推进的开发者。
- **阻塞关系**：参见“约定”下的**阻塞关系**。
- **前沿查询**：列出地图下未关闭的子工单（`gh issue list --state open`，限定为地图的子 issue / 任务列表），排除存在未关闭阻塞项（参见**统计未关闭的阻塞项**，或检查 `Blocked by` 行中的未关闭 issue）或已有受理人的工单；按地图顺序选第一个。
- **领取**：`gh issue edit <n> --add-assignee @me`，作为本会话的首次写操作。
- **完成**：执行 `gh issue comment <n> --body "<answer>"`，再执行 `gh issue close <n>`，然后向地图的 Decisions-so-far 部分追加上下文指针（要点 + 链接）。
