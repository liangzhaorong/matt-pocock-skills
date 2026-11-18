# 生产力（Productivity）

通用工作流工具，不针对代码。

## 用户调用（User-invoked）

只有在你亲自输入时才能触达（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[grill-me](./grill-me/SKILL.md)**：围绕一个计划或设计接受毫不留情的访谈，直到设计树的每个分支都被解决。
- **[handoff](./handoff/SKILL.md)**：把当前对话压缩成一份交接文档，让另一个代理能继续这项工作。
- **[teach](./teach/SKILL.md)**：跨多个会话教用户一项新技能或新概念，把当前目录用作有状态的教学工作区。
- **[to-questionnaire](./to-questionnaire/SKILL.md)**：把你独自无法回答的决策变成一份 Markdown 问卷，交给那个能回答的人（异步填写，或会议上一起当场填）。
- **[wait-what](./wait-what/SKILL.md)**：某条消息没讲明白的那一刻就触发它。代理带着你缺失的上下文、用浅白的英语重新讲一遍，并使用你 `GLOSSARY.md` 里的词汇。

## 模型调用（Model-invoked）

模型或用户均可触达（触发措辞丰富，便于模型主动取用）。

- **[grilling](./grilling/SKILL.md)**：就一个计划、决策或想法毫不留情地访谈用户，直到设计树的每个分支都被解决。
- **[writing-for-agents](./writing-for-agents/SKILL.md)**：为代理撰写文档：技能、AGENTS.md/CLAUDE.md，以及代理经由指针触达的任何文档。
