# 分诊标签

这些技能用五个规范的分诊角色来表述。本文件把这些角色映射到本仓库 issue 跟踪器中实际使用的标签字符串。

| mattpocock/skills 中的标签 | 我们跟踪器中的标签 | 含义                                |
| -------------------------- | ------------------ | ----------------------------------- |
| `needs-triage`             | `needs-triage`     | 维护者需要评估这个 issue            |
| `needs-info`               | `needs-info`       | 等待报告者补充更多信息              |
| `ready-for-agent`          | `ready-for-agent`  | 规格已完整，可交给 AFK 智能体       |
| `ready-for-human`          | `ready-for-human`  | 需要人类来实现                      |
| `wontfix`                  | `wontfix`          | 不会对其采取行动                    |

当某个技能提到一个角色（例如“打上 AFK 就绪的分诊标签”）时，使用本表中对应的标签字符串。

在选定状态之前，每个 issue 都要对照 [`SCOPE.md`](../../SCOPE.md) 评判。被拒绝的 issue 以“not planned”（未计划）状态关闭。
