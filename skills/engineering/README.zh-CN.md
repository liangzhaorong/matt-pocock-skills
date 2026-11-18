# 工程（Engineering）

我日常用于代码工作的技能。

## 用户调用（User-invoked）

只有在你亲自输入时才能触达（Claude Code：`disable-model-invocation: true`；Codex：`agents/openai.yaml` 中的 `policy.allow_implicit_invocation: false`）。

- **[ask-matt](./ask-matt/SKILL.md)**：询问哪个技能或流程适合你的处境。本仓库中所有用户可调用技能的路由器。
- **[grill-with-docs](./grill-with-docs/SKILL.md)**：一场拷问式会话，同时构建你项目的领域模型，就地打磨术语并更新 `GLOSSARY.md` 和 ADR。
- **[triage](./triage/SKILL.md)**：让 issue 在一个由分诊角色构成的状态机中流转。
- **[improve-codebase-architecture](./improve-codebase-architecture/SKILL.md)**：扫描代码库寻找可加深（deepening）的机会，以可视化的 HTML 报告呈现，然后对你选中的那一个进行拷问。
- **[setup-matt-pocock-skills](./setup-matt-pocock-skills/SKILL.md)**：为本仓库配置这些工程技能（issue 跟踪器、分诊标签、领域文档布局）。每个仓库运行一次。
- **[to-spec](./to-spec/SKILL.md)**：把当前对话转成一份规格（spec），并发布到 issue 跟踪器。
- **[to-tickets](./to-tickets/SKILL.md)**：把任何计划、规格或对话拆解成一组曳光弹工单，每张工单声明自己的阻塞边，形式既可以是本地文件里的文本，也可以是真实跟踪器上的原生阻塞链接。
- **[implement](./implement/SKILL.md)**：构建由一份规格或一组工单所描述的工作，在事先商定的接缝上驱动 `/tdd`，提交前以 `/code-review` 收尾。
- **[implement-spec](./implement-spec/SKILL.md)**：在单个集成分支上实现一整份规格。把工单当作任务图来运作，在就绪的前沿上并行运行实现者子代理以获得最大并发，最后以 `/code-review` 收尾。
- **[wayfinder](./wayfinder/SKILL.md)**：把一大块工作（大到单个代理会话装不下）规划成 issue 跟踪器上一张由决策工单组成的共享地图，逐一解决，直到通往目的地的路清晰可见。
- **[retro](./retro/SKILL.md)**：会话结束后，对编码代理的环境提出改进建议（导航、自动化检查、编码规范、引导文件、工具链），最严重的排在最前面。

## 模型调用（Model-invoked）

模型或用户均可触达（触发措辞丰富，便于模型主动取用）。

- **[prototype](./prototype/SKILL.md)**：构建一个一次性原型来回答某个设计问题：一个用于状态/逻辑的可分享 HTML 文件，或多个可切换的 UI 变体。

- **[diagnosing-bugs](./diagnosing-bugs/SKILL.md)**：针对疑难 bug 和性能回归的纪律化诊断循环：构建一个碰到该 bug 就变红的反馈回路 → 最小化 → 提出假设 → 插桩 → 修复 → 回归测试。
- **[research](./research/SKILL.md)**：对照高信任度的一手来源调查某个问题，并以带引用的 Markdown 文件把发现记录在仓库中，作为后台代理运行。
- **[tdd](./tdd/SKILL.md)**：测试驱动开发，红-绿-重构循环。一次一个垂直切片地构建功能或修复 bug。
- **[domain-modeling](./domain-modeling/SKILL.md)**：通过挑战术语、用场景做压力测试、就地更新 `GLOSSARY.md` 和 ADR，主动构建并打磨项目的领域模型。
- **[codebase-design](./codebase-design/SKILL.md)**：设计深模块的共享纪律与词汇：小接口、干净的接缝、可通过接口进行测试。
- **[code-review](./code-review/SKILL.md)**：对自某个固定点以来的 diff 做双轴评审：**Standards（标准）**（是否遵循仓库的编码规范，外加一条 Fowler 坏味道基线？）与 **Spec（规格）**（是否忠实实现了来源 issue/规格？），以并行子代理运行。
- **[pr](./pr/SKILL.md)**：PR 描述主体应有的形态：一段作为最小视觉呈现、让改动一目了然的摘要；能证明它有效的前后对比证据；以及一次合并风险裁定（单向门还是双向门，外加影响范围）。
- **[wizard](./wizard/SKILL.md)**：生成一个交互式 bash 向导，带领人类一步步完成只有他们自己才能执行的步骤：配置基础设施、设置凭据或 CI 密钥、带人走一遍陌生的第三方控制台，或执行一次性迁移或切换。
