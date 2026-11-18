技能按分类（bucket）文件夹组织，放在 `skills/` 之下：

- `engineering/`：日常代码工作
- `productivity/`：日常的非代码工作流工具
- `misc/`：保留着但很少使用，不推广
- `in-progress/`：测试版（beta）：有意公开、想要反馈，但不在插件中发布
- `deprecated/`：不再使用

`engineering/` 或 `productivity/`（即**推广（promoted）**分类）中的每个技能，必须在顶层 `README.md` 中有一条引用，并在 `.claude-plugin/plugin.json` 的 `skills` 数组中有一项（Claude Code 插件发布的正好就是这套推广集合）。`misc/`、`in-progress/` 和 `deprecated/` 中的技能绝不能出现在这两处。

安装命令逐字复制自 [.agents/install-block.md](./.agents/install-block.md)。`.claude-plugin/marketplace.json` 是本仓库自己的 marketplace。除 Claude Code 之外的每个智能体都从它安装以获得自动更新，而 Claude Code 使用 `claude-plugins-official`。改动任一 manifest 之后，运行 `claude plugin validate . --strict`。[ADR 0002](./.agents/adr/0002-ship-as-a-claude-code-plugin.md) 记录了原因。

顶层 `README.md` 中的每个技能条目，都必须把技能名链接到它的 `SKILL.md`。

每个分类文件夹都有一个 `README.md`，列出该分类下的每个技能并附一行描述，技能名链接到它的 `SKILL.md`。推广分类的 `README.md` 和顶层 `README.md` 会把条目分成**用户调用（User-invoked）**和**模型调用（Model-invoked）**两组；非推广分类的 `README.md`（`misc/`、`in-progress/`）则使用扁平列表。

`engineering/` 和 `productivity/` 中的技能还有一面向人类的文档页，位于 `docs/<bucket>/<skill-name>.md`（docs 目录树镜像了 `skills/` 下的这两个分类文件夹）。无论技能属于哪个分类，对外发布的 URL 一律是 `https://aihero.dev/skills-<skill-name>`：docs 路径只用于仓库内部组织。当你新增、重命名 `engineering/` 或 `productivity/` 中的技能，或改变其行为时，要按照 [.agents/writing-docs.md](./.agents/writing-docs.md) 创建或重新同步它的文档页。一份完成的文档页包含四个小节：**What it does**（它做什么）、**When to reach for it**（何时该用它）、**Common questions**（常见问题）和 **It's working if**（有这些表现就说明它在生效）。`writing-docs.md` 给出了模板、小节顺序，以及去哪里挖掘这些常见问题。非推广分类（`misc/`、`in-progress/`、`deprecated/`）中的技能**没有**文档页。唯一的例外是被整个移除的推广技能：它的文档页保留，标记为归档（见 `writing-docs.md`）。

每个 `SKILL.md` 要么是用户调用（user-invoked：`disable-model-invocation: true`，外加 `agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`，只有人类能触达），要么是模型调用（model-invoked：模型或用户都能触达）。参见 [.agents/invocation.md](./.agents/invocation.md)。

[`ask-matt`](./skills/engineering/ask-matt/SKILL.md) 是路由器，绘制出每个用户可触达的技能以及它们之间的关系。触发文档页重新同步的同一条件对它同样适用：每当你新增、重命名、移除某个用户可触达的技能，或改变它在各流程中的位置时，都要重读 `ask-matt` 的 `SKILL.md` 并更新它，让这张地图保持准确：一个它从未提及的新技能，或一个它仍在路由的已失效技能，都意味着这个路由器在撒谎。

要把 `deprecated/` 和 `misc/` 之外的所有技能（重新）链接到本地 harness 的技能目录（`~/.claude/skills`、`~/.agents/skills`），运行 `scripts/link-skills.sh`。每个条目都是指向本仓库的符号链接，因此一次 `git pull` 就能让已安装的技能保持最新；在新增、移除或重命名技能之后，重新运行该脚本。

本仓库的所有散文体文本（`SKILL.md` 文件、docs、`README.md`、`CHANGELOG.md`、ADR、changeset、代码注释）中一律不得出现 em-dash（长破折号）。哪句话想用破折号，就改用逗号、冒号、句号、圆括号或连接词重写，选这句话真正需要的那种；绝不许做盲目的字符替换。

每个会派发子智能体的技能都写明：要让子智能体在全新的上下文窗口中启动。某些 harness（比如 Codex）默认会 fork 父会话，而 fork 会继承父级的上下文，这恰恰是子智能体本来想要摆脱的东西。

## 智能体技能

### 分诊标签

使用规范名称，不做改动。参见 `docs/agents/triage-labels.md`。Issue 依照 [`SCOPE.md`](./SCOPE.md) 来评判。
