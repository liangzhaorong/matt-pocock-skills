## 它做什么

`setup-matt-pocock-skills` 回答关于一个仓库的三个问题：issue 住哪、triage 标签叫什么、领域文档放哪。它把答案记录为 `docs/agents/` 下的 markdown 文件。

这些文件是仓库之间唯一不同的东西。技能本身在哪儿都一样。它们在运行时读 `docs/agents/issue-tracker.md`，照它说的做。这就是这套技能不绑死 GitHub 的原因，也是你永远不必为指向另一个跟踪器而改技能文件的原因。用“把技能接到自定义 issue 跟踪器”来调用它，对任何你能以编程方式连接的东西都行，技能一行不用改。

它是一个提示词驱动的技能，不是确定性脚本。它读你的 `git remote`、`CLAUDE.md` 和 `GLOSSARY.md`，就发现的东西给出提议，写任何东西之前都等你确认。

## 何时该用它

你通过输入 `/setup-matt-pocock-skills` 来调用它；[智能体](https://www.aihero.dev/ai-coding-dictionary/agent)不会自己主动取用它。它的元数据故意标成不可调用，所以没有别的技能能替你触发它。

每个仓库一次，在任何其他工程技能首次使用之前。如果 [triage](https://aihero.dev/skills-triage)、[to-spec](https://aihero.dev/skills-to-spec)、[to-tickets](https://aihero.dev/skills-to-tickets) 或 [wayfinder](https://aihero.dev/skills-wayfinder) 开始猜你的 issue 放哪，或打上你的跟踪器里没有的标签，这个仓库就还没设置过。项目做到一半的仓库也能跑。技能读的是已有的东西，之前的工作不会丢。

## 前置条件

它写进你运行它的那个仓库：

| 它写什么 | 写到哪 |
| --- | --- |
| `issue-tracker.md` | `docs/agents/` |
| `domain.md` | `docs/agents/` |
| `triage-labels.md` | `docs/agents/`，仅当 `triage` 技能已安装 |
| 一个 `## Agent skills` 块 | `CLAUDE.md` 或 `AGENTS.md` 中已存在的那一个 |

你把这一切当作 markdown 提交。没有用户级或全局模式。配置住在仓库里，所以每个仓库有自己的副本。

## 三个决定

它每一节都以推荐答案开场，跳过任何它的勘察已经答出的问题。大多数运行只需要两次确认。

| 决定 | 它提议什么 | 什么时候问 |
| --- | --- | --- |
| **Issue 跟踪器** | 与你 `git remote` 相符的那个 | 总是问，因为这是唯一真正的选择 |
| **Triage 标签** | 保留五个规范名（`needs-triage`、`needs-info`、`ready-for-agent`、`ready-for-human`、`wontfix`） | 仅当 `triage` 技能已安装 |
| **领域文档** | 单上下文：根目录一个 `GLOSSARY.md` 加 `docs/adr/` | 仅当它嗅到 monorepo 信号，届时它会提供多上下文的 `GLOSSARY-MAP.md` |

跟踪器选项：

| 选项 | issue 住哪 | 需要 |
| --- | --- | --- |
| **GitHub** | 仓库的 GitHub Issues | `gh` CLI |
| **GitLab** | 仓库的 GitLab Issues | `glab` CLI |
| **本地 markdown** | 本仓库 `.scratch/<feature>/` 下的文件 | 什么都不需要，连远程都不用 |
| **其他** | 你说了算 | 你写一段话描述工作流 |

前三个以模板形式随技能发布，开箱即用。本地 markdown 是一个完整选项，不是退路。技能支持没有远程的个人项目。一条注意事项：如果你用 GitHub，就别用本地 markdown。两者是替代关系，二选一。

“其他”也是完整选项。Jira、Linear、Azure DevOps 和 Beads 走的都是这条路。你描述工作流，技能把你的话记进 `docs/agents/issue-tracker.md`，下游技能照着这段话走。用户已经这么建过：一个走 [MCP](https://www.aihero.dev/ai-coding-dictionary/mcp) 的 Jira 变体、一个长得像 `gh` 的 Gitea CLI、一个手搓的本地看板。

## 常见问题

**我必须用 GitHub 吗？**

不必。GitHub、GitLab 和 `.scratch/` 下的本地 markdown 都以现成模板发布，其他一切走“其他”这条路。这是被重复得最多的问题，措辞大同小异：*“锁死在 github 上”*、*“我能用 GitLab / Jira 吗”*、*“Azure DevOps 呢”*。答案永远是同一句：是 setup 选跟踪器，不是技能。

**更新技能之后需要重跑它吗？**

v1.1 之后直接的答案是“要”。技能自己的收尾消息说得更软：只有换跟踪器或从头再来时才重跑。两种说法都站得住。种子模板在版本之间会变，于是一份旧版本写下的 `docs/agents/issue-tracker.md` 可能与现在读它的技能对不上。如果某个下游技能做的事与文档描述的不一样，重跑 setup。它很便宜。

**它写进了 `CLAUDE.md`，可我在用 Codex。**

这是一个已知的缺口，仍开着。选文件的规则是“`CLAUDE.md` 存在就改它，否则改 `AGENTS.md`”。它查的是哪个文件存在，不是哪个 [harness](https://www.aihero.dev/ai-coding-dictionary/harness) 在跑。在一个留有 Claude Code 时代 `CLAUDE.md` 的仓库里，技能把它的 `## Agent skills` 块写进一个 Codex 从不读的文件。用户有两条绕法：手工把块挪进 `AGENTS.md`，或让 `AGENTS.md` 为准、把 `CLAUDE.md` 做成指向它的一行指针。两个文件都不存在时，技能会问你建哪个，而不是自己挑。这让指望它自己拿主意的人困惑过。

**它没替我创建 triage 标签。**

它本来就不创建。`docs/agents/triage-labels.md` 是一张*映射表*：它告诉 `/triage` 你跟踪器里的哪些字符串对应五个规范角色。它不跑 `gh label create`。在全新的 GitHub 仓库上这些标签还不存在，用户不止一次把这立为 bug。两个后果：

- 如果你的跟踪器已经在用规范名，映射就是一张恒等表，没什么可配的。那是预期的常态，不是缺失的步骤。
- 这个技能也不创建 [wayfinder](https://aihero.dev/skills-wayfinder) 的 `wayfinder:map` 和 `wayfinder:<type>` 标签，而 `gh issue create --label <missing>` 会失败，不会顺手建标签。在 GitHub 仓库上第一次跑 wayfinder 之前，手工建好它们。

**我能在这里配置其他技能的行为吗（[拷问](https://www.aihero.dev/ai-coding-dictionary/grilling)节奏、问题格式、语气）？**

不能。它配置三样东西：跟踪器、标签、文档布局。用户曾要求把它变成放每用户偏好的地方。答案是：技能保持自己的主张，不接受每用户配置。偏好属于你的 `CLAUDE.md`，写成普通指示，每个技能本来就读它。

**我能把配置放在 `~/.claude` 里，而不是提交进每个仓库吗？**

今天不行。一个跨多仓库跑这套技能的用户为此立着未决请求，但用户级模式不存在。每个仓库带自己的 `docs/agents/`。

**有一个专门配置其他技能的技能，不奇怪吗？**

一条长期抱怨说不奇怪才怪，原话是：*“用一个技能去设置其他技能，我觉得不对劲：这意味着 LLM 在配置自己的技能。”*这个权衡是真实的。没有 setup 这一步，每个碰 issue 的技能都得自带一份跟踪器指示。产出是你可以读、可以改的 markdown，这就限住了风险。它写的每个文件你都能读、都能手改。日常改动就这么做，别再跑一遍。

## 有这些表现就说明它在生效

- `docs/agents/issue-tracker.md` 和 `docs/agents/domain.md` 存在；装了 `triage` 的话，`triage-labels.md` 也在。
- 你的 harness 读的那份指令文件里出现 `## Agent skills` 一节，每个文件配一行指向它的摘要。
- 它提议的跟踪器与你在用的远程相符，标签字符串与跟踪器里已有的标签相符。
- 之后 `/to-tickets` 发布时不再问你 issue 住哪，`/triage` 打的是已有的标签而不是现编的。
- 技能文件本身什么都没变。如果 setup 改了哪个 `SKILL.md`，就是出了问题。

## 它的位置

`setup-matt-pocock-skills` 是工程流程的**一次式设置**，是其余一切默认成立的前提条件，而不是链条上的一步。它的邻居是它的读者：[triage](https://aihero.dev/skills-triage)，应用这里写下的标签词汇；[to-spec](https://aihero.dev/skills-to-spec) 和 [to-tickets](https://aihero.dev/skills-to-tickets)，往这里点名的跟踪器里发布；以及 [wayfinder](https://aihero.dev/skills-wayfinder)，读同一份跟踪器文件的“Wayfinding operations”一节，学习怎么存地图和子[工单](https://www.aihero.dev/ai-coding-dictionary/ticket)。[domain-modeling](https://aihero.dev/skills-domain-modeling) 稍后把 setup 记录的领域文档布局填满。它只在你敲定术语或决定时才创建 `GLOSSARY.md` 和 ADR，所以 setup 之后仓库没有领域文档是正常的。接下来该找哪个技能，[ask-matt](https://aihero.dev/skills-ask-matt) 路由整套。
