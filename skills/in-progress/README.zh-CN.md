# 开发中（In Progress）

Beta。这些技能有意公开：试用它们，告诉我什么坏了。在毕业进入稳定桶之前，它们不进插件和顶层 README，没有文档页，而且可能毫无预警地更改或消失。

插件不会给你这些。直接安装某一个：

```bash
npx skills@latest add mattpocock/skills --skill=<name>
```

- **[loop-me](./loop-me/SKILL.md)**：跨多个会话把自己拷问成可实现的工作流规格，把当前目录用作有状态的工作区。用户调用。
- **[writing-beats](./writing-beats/SKILL.md)**：把文章塑形为一段节拍之旅，"自选冒险"风格。选一个起始节拍，只写那个节拍，然后转向下一个，直到文章到达自然的结尾。
- **[writing-fragments](./writing-fragments/SKILL.md)**：一场拷问会话，从你身上采掘碎片（一块块异质的写作原矿），并追加到单个文档里，作为未来文章的原材料。
- **[writing-shape](./writing-shape/SKILL.md)**：取一份原材料 markdown 文件，逐段把它塑形成文章，在每一步就格式选择进行争论。
- **[claude-handoff](./claude-handoff/SKILL.md)**：把当前对话交给一个全新的后台代理，让它立即接手工作，通过 `claude --bg` 以一份交接摘要作为种子。用户调用。
- **[setup-ts-deep-modules](./setup-ts-deep-modules/SKILL.md)**：把 dependency-cruiser 接入一个 TypeScript 仓库，让每个包都是深模块：实现藏在子文件夹里，只能通过其入口文件触达，测试通过那些入口来检验它。用户调用。
- **[software-factory](./software-factory/SKILL.md)**：设计软件工厂的共享语言：它运行的任务和它接入的服务。与拷问会话一起调用。用户调用。
