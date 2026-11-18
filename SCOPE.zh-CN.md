# 范围

这个仓库是我每天在用的技能集合。它是经过精心筛选的：欢迎提出想法，每个想法都会按照下面的门槛来评判。Issue 用于跟踪技能的变更。提问和讨论请前往 [GitHub Discussions](https://github.com/mattpocock/skills/discussions)。

## 门槛

一个想法类或反馈类的 issue，只有同时满足以下**两个**部分，才会保持开放：

1. **实际观察到的失败。** 它描述的是一次真实会话中出错的事情：你运行了什么、技能做了什么、你期望的是什么。假设性的改进（“如果……会更好”）不满足这一部分。
2. **符合理念。** 它与 [`.out-of-scope/`](./.out-of-scope/) 中的任何内容或以往被拒绝的想法都不相符，并且它不是配置项、针对特定 harness 的分支，也不是为迁就某个人工作流而做的调整。这类内容应该放在你自己的 `CLAUDE.md` / `AGENTS.md` 里，或者放在 fork 中（skills.sh 会提供一份可编辑的副本）。

改动大小无关紧要：一个词的修正和一次完整重写面对的是同一道门槛。表情回应和 +1 评论不计入其中。

## 按分类（bucket）划分

- **`engineering/`、`productivity/`、`in-progress/`**：适用上面的门槛。
- **`misc/`**：已冻结、不再维护。所有 issue 均已关闭。参见 [`frozen-misc-skills.md`](./.out-of-scope/frozen-misc-skills.md)。
- **新技能**：有关新技能的提案和贡献都会被关闭。如果该行为可以由现有技能组合实现，就不会为它新增技能。参见 [`new-skills.md`](./.out-of-scope/new-skills.md)。

## 已有定论

[`.out-of-scope/`](./.out-of-scope/) 中的每个文件都记录了一个已被拒绝的概念及其原因。提交 issue 之前请先阅读它们：

- [`frozen-misc-skills.md`](./.out-of-scope/frozen-misc-skills.md)
- [`harness-name-collisions.md`](./.out-of-scope/harness-name-collisions.md)
- [`mainstream-issue-trackers-only.md`](./.out-of-scope/mainstream-issue-trackers-only.md)
- [`native-question-tool.md`](./.out-of-scope/native-question-tool.md)
- [`new-skills.md`](./.out-of-scope/new-skills.md)
- [`question-limits.md`](./.out-of-scope/question-limits.md)
- [`setup-skill-verify-mode.md`](./.out-of-scope/setup-skill-verify-mode.md)
- [`subagent-recursion.md`](./.out-of-scope/subagent-recursion.md)

## 不明确的 issue

无法对照门槛评判的 issue，会得到一轮提问并被加上 `needs-info` 标签。若报告者 14 天内没有回复，该 issue 就会被关闭。被拒绝的 issue 以“not planned”（未计划）状态关闭。
