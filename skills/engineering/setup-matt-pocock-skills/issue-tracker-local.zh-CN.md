# 问题跟踪系统：本地 Markdown

当前仓库的 issue 和规格文档以 Markdown 文件的形式存放在 `.scratch/` 中。

## 约定

- 每个功能一个目录：`.scratch/<feature-slug>/`
- 规格文档为 `.scratch/<feature-slug>/spec.md`
- 实现工单每张对应一个文件，路径为 `.scratch/<feature-slug>/issues/<NN>-<slug>.md`，从 `01` 开始编号，绝不合并成单个工单文件
- 分诊状态记录在每个 issue 文件顶部附近的 `Status:` 行中（角色字符串见 `triage-labels.md`）
- 评论和对话历史追加到文件底部的 `## Comments` 标题下

## 当技能说“发布到问题跟踪系统”时

在 `.scratch/<feature-slug>/` 下创建新文件（必要时创建目录）。

## 当技能说“获取相关工单”时

读取所引用路径上的文件。用户通常会直接提供路径或 issue 编号。

## 探路操作

供 `/wayfinder` 使用。**地图**是一个文件，每张工单对应一个**子**文件。

- **地图**：`.scratch/<effort>/map.md`（正文包含 Notes（笔记）/ Decisions-so-far（迄今的决策）/ Fog（迷雾））。
- **子工单**：`.scratch/<effort>/issues/NN-<slug>.md`，从 `01` 开始编号，正文写明问题。`Type:` 行记录工单类型（`research`/`prototype`/`grilling`/`task`）；`Status:` 行记录 `claimed`/`resolved`。
- **阻塞关系**：在顶部附近写一行 `Blocked by: NN, NN`。它列出的每个文件都变为 `resolved` 后，工单才解除阻塞。
- **前沿**：扫描 `.scratch/<effort>/issues/`，寻找未完成、未被阻塞且未被领取的文件；按编号选第一个。
- **领取**：开始任何工作之前，设置 `Status: claimed` 并保存。
- **完成**：在 `## Answer` 标题下追加答案，设置 `Status: resolved`，然后向 `map.md` 中地图的 Decisions-so-far 部分追加上下文指针（要点 + 链接）。
