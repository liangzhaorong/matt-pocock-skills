# 领域文档

工程技能在探索代码库时，应如何使用当前仓库的领域文档。

## 探索之前，先阅读这些内容

- 仓库根目录的 **`GLOSSARY.md`**，或者
- 如果存在，阅读仓库根目录的 **`GLOSSARY-MAP.md`**：它为每个上下文指向一个 `GLOSSARY.md`。阅读所有与当前话题相关的术语表。
- **`docs/adr/`**：阅读涉及你即将开展工作的领域的 ADR。对于多上下文仓库，还要查看 `src/<context>/docs/adr/` 中限定于该上下文的决策。

如果这些文件中有任何一个不存在，**直接继续，不作提示**。不要指出它们缺失，也不要预先建议创建。`/domain-modeling` 技能（通过 `/grill-with-docs` 和 `/improve-codebase-architecture` 调用）会在术语或决策真正确定时，按需创建这些文件。

## 文件结构

单上下文仓库（大多数仓库）：

```
/
├── GLOSSARY.md
├── docs/adr/
│   ├── 0001-event-sourced-orders.md
│   └── 0002-postgres-for-write-model.md
└── src/
```

多上下文仓库（根目录存在 `GLOSSARY-MAP.md`）：

```
/
├── GLOSSARY-MAP.md
├── docs/adr/                          ← 系统级决策
└── src/
    ├── ordering/
    │   ├── GLOSSARY.md
    │   └── docs/adr/                  ← 特定上下文的决策
    └── billing/
        ├── GLOSSARY.md
        └── docs/adr/
```

## 使用术语表中的词汇

当你的输出为领域概念命名时（无论在 issue 标题、重构提案、假设还是测试名称中），都使用 `GLOSSARY.md` 中定义的术语。不要改用术语表明确要求避免的同义词。

如果你需要的概念尚未出现在术语表中，这就是一个信号：要么你在创造项目并不使用的语言（需要重新考虑），要么确实存在缺口（记录下来，交由 `/domain-modeling` 处理）。

## 标明 ADR 冲突

如果你的输出与现有 ADR 相矛盾，明确指出，而不是默默覆盖：

> *与 ADR-0007（事件溯源订单）相矛盾，但值得重新讨论，因为……*
