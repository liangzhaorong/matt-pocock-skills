---
name: scaffold-exercises
description: 创建能通过 lint 的练习目录结构，含小节、题目、解答和讲解。当用户想搭建练习、创建练习桩、或新建课程小节时使用。
---

# 搭建练习（Scaffold Exercises）

创建能通过 `pnpm ai-hero-cli internal lint` 的练习目录结构，然后用 `git commit` 提交。

## 目录命名

- **小节（Sections）**：`exercises/` 内的 `XX-section-name/`（如 `01-retrieval-skill-building`）
- **练习（Exercises）**：小节内的 `XX.YY-exercise-name/`（如 `01.03-retrieval-with-bm25`）
- 小节编号 = `XX`，练习编号 = `XX.YY`
- 名称用 dash-case（小写、连字符）

## 练习变体

每个练习至少需要下列子文件夹之一：

- `problem/` - 带 TODO 的学生工作区
- `solution/` - 参考实现
- `explainer/` - 概念性材料，无 TODO

搭桩时，除非计划另有说明，默认用 `explainer/`。

## 必需文件

每个子文件夹（`problem/`、`solution/`、`explainer/`）都需要一个 `readme.md`，它要：

- **不为空**（必须有真实内容，哪怕只有一行标题也行）
- 没有断链

搭桩时，创建带标题和描述的最简 readme：

```md
# 练习标题

描述写在这里
```

如果子文件夹有代码，还需要一个 `main.ts`（多于 1 行）。但对桩来说，只有 readme 的练习就够了。

## 工作流

1. **解析计划** - 提取小节名、练习名和变体类型
2. **创建目录** - 为每条路径 `mkdir -p`
3. **创建 readme 桩** - 每个变体文件夹一个带标题的 `readme.md`
4. **跑 lint** - `pnpm ai-hero-cli internal lint` 验证
5. **修复错误** - 迭代直到 lint 通过

## Lint 规则摘要

linter（`pnpm ai-hero-cli internal lint`）检查：

- 每个练习有子文件夹（`problem/`、`solution/`、`explainer/`）
- `problem/`、`explainer/` 或 `explainer.1/` 至少存在一个
- 主子文件夹中 `readme.md` 存在且非空
- 无 `.gitkeep` 文件
- 无 `speaker-notes.md` 文件
- readme 无断链
- readme 无 `pnpm run exercise` 命令
- 每个子文件夹需要 `main.ts`，除非它只有 readme

## 移动/重命名练习

重新编号或移动练习时：

1. 用 `git mv`（不是 `mv`）重命名目录 - 保留 git 历史
2. 更新数字前缀以维持顺序
3. 移动后重跑 lint

示例：

```bash
git mv exercises/01-retrieval/01.03-embeddings exercises/01-retrieval/01.04-embeddings
```

## 示例：从计划搭桩

给定这样一个计划：

```
Section 05: Memory Skill Building
- 05.01 Introduction to Memory
- 05.02 Short-term Memory (explainer + problem + solution)
- 05.03 Long-term Memory
```

创建：

```bash
mkdir -p exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer
mkdir -p exercises/05-memory-skill-building/05.02-short-term-memory/{explainer,problem,solution}
mkdir -p exercises/05-memory-skill-building/05.03-long-term-memory/explainer
```

然后创建 readme 桩：

```
exercises/05-memory-skill-building/05.01-introduction-to-memory/explainer/readme.md -> "# Introduction to Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/explainer/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/problem/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.02-short-term-memory/solution/readme.md -> "# Short-term Memory"
exercises/05-memory-skill-building/05.03-long-term-memory/explainer/readme.md -> "# Long-term Memory"
```
