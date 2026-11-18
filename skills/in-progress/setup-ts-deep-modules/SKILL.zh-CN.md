---
name: setup-ts-deep-modules
description: 把 dependency-cruiser 接入 TypeScript 仓库，让每个包都成为深模块：实现藏在子文件夹里，只能通过其入口文件触达。用户调用。
disable-model-invocation: true
---

# 配置 TS 深模块（Setup TS Deep Modules）

让本仓库的每个包都成为**深模块（deep module）**：小接口背后的大量行为。一个包的公共面是它的**入口（entry point，包根目录下的文件）**，其子文件夹里的一切都是隐藏的。本技能安装 [dependency-cruiser](https://github.com/sverweij/dependency-cruiser) 和让入口成为唯一进路的规则，然后证明规则真的咬人。

关于词汇（deep module、interface、seam、depth），用 Skill 工具调用 "codebase-design"，并全程使用它的语言。

## 它强制的形状

```
src/packages/
  <name>/
    index.ts        ← 一个入口（公共）。从外部导入这个。
    client.ts       ← 另一个入口。一个包可以暴露好几个。
    lib/            ← 实现：对外隐藏，彼此可自由导入。
    tests/          ← 同址测试 + 夹具（子文件夹，所以私有）。
```

公共面是包的**根文件**，不是某个钦定的 `index.ts`。按惯例，实现放 `lib/`、测试放 `tests/`，让每个包都有相同的两文件夹形状。但规则本身是通用的：*任何*子文件夹里的*任何*东西都是私有的，所以你永远不用为新增文件夹而扩展配置。

四条规则，全部 `error`：

1. **入口边界**：包外的代码（应用代码或另一个包）只能导入该包的入口（其根文件），绝不导入其子文件夹里的任何东西。
2. **包内自由**：包自己的文件彼此自由导入。
3. **测试走入口**：`<pkg>/tests/` 下的文件可以导入任何包的入口以及自己的 `tests/` 夹具，但绝不导入任何包的子文件夹内部（连自己的也不行）。跨包的集成测试没问题；深导入不行。
4. **无环**：不允许依赖环。

**是入口，不是桶（barrel）。** 因为公共面是*每一个*根文件，一个包可以暴露几个小入口（`index.ts`、`client.ts`、`server.ts`），而不是把所有东西漏斗进一个巨大的 `index.ts`。不鼓励重新导出整棵子树的桶文件；保持入口小，把实现藏进子文件夹。

分层（哪些包可以依赖哪些）是*另一*个关注点，在配置里留作注释桩，由本仓库自己填。

## 步骤

### 1. 探测环境

- **包管理器**：`pnpm-lock.yaml` → pnpm，`yarn.lock` → yarn，`bun.lockb` → bun，否则 npm。下面每条命令都用它（`pnpm`/`yarn`/`npm run`/`bunx`）。
- **包根**：有 `src/` 就用 `src/packages`，否则 `packages`。若仓库已有另一套明显惯例，与用户确认选择。
- **既有配置**：检查是否存在 `.dependency-cruiser.*` 文件。若有，**不要**覆盖：把四条规则和选项合并进去，并告诉用户你加了什么。

**完成标准：** 包管理器、包根、既有配置状态全部已知。

### 2. 安装 dependency-cruiser

用探测到的包管理器把 `dependency-cruiser` 安装为 devDependency。

**完成标准：** `dependency-cruiser` 出现在 `devDependencies`。

### 3. 写配置

把 [`dependency-cruiser.config.cjs`](./dependency-cruiser.config.cjs) 复制到仓库根目录，命名为 `.dependency-cruiser.cjs`。把 `PACKAGES_ROOT` 设为第 1 步探测到的根。规则基于路径深度、与扩展名无关，其他都不需要适配。

**完成标准：** `.dependency-cruiser.cjs` 存在且 `PACKAGES_ROOT` 正确，四条禁止规则在场。

### 4. 接入检查

- 加一个 `lint:boundaries` 脚本：`depcruise <packages-root>`（或 `depcruise src`）。
- 把它折进仓库的总检查命令，即已经在跑 typecheck 的那条（比如 `check` / `ci` / `validate` 脚本）。**不要**动 `tsconfig` 或添加路径别名。
- 若没有总脚本，加上 `lint:boundaries`，并告诉用户把它纳入 CI。

**完成标准：** `lint:boundaries` 存在，且与 typecheck 同属一条命令运行。

### 5. 搭建示例包

创建一个提交进库的 `<packages-root>/example/`，作为"复制我"模板：

- `index.ts` 是一个入口。导出一个委托给内部文件的函数（让这个包看上去是真的*深*，而不是直通）。
- `lib/impl.ts`：**子文件夹**里的内部文件，被 `index.ts` 导入，外部不可达。
- `tests/example.test.ts` **只**导入 `../index`（一个入口），并对公共函数做断言。

告诉用户这是一个可复制或删除的起步模板。

**完成标准：** 示例包存在，通过根级入口暴露其行为，并把 `impl` 藏在子文件夹里。

### 6. 证明规则咬人

这是整个技能的完成标准：在违规面前不失败的配置一文不值。

1. 跑 `lint:boundaries`。在干净的示例上它必须**通过**。
2. 暂时给 `tests/example.test.ts` 加一个深导入（如 `import { thing } from "../lib/impl"`）。再跑 `lint:boundaries`；它必须以 `tests-through-entrypoints` **失败**。
3. 还原深导入。再跑一次，它必须**通过**。

**完成标准：** 你观察到一次通过，然后在深导入上一次失败，然后再一次通过。若第 2 步不失败，说明规则没接对，先修复再收尾。

### 7. 记录惯例

在**包文件夹里**写一个 `README.md`（`<packages-root>/README.md`，与它管辖的包为邻），覆盖：`src/packages/<name>/` 布局（入口在根、`lib/` 放实现、`tests/` 放测试）、"只通过包的入口（其根文件）导入"、以及如何运行 `lint:boundaries`。明确**劝阻桶文件**：暴露几个小入口，而不是用一个 index 重新导出整棵子树。篇幅保持在"复制我"片段加四条规则各一段。

然后从仓库的代理指令文件（有 `CLAUDE.md` 用它，否则 `AGENTS.md`，两者都没有就创建 `AGENTS.md`）加一条指向它的**上下文指针**。一行就够，例如 `Packages are deep modules: see [src/packages/README.md](./src/packages/README.md) before adding or importing one.`。正是它让代理发现边界规则，而不是一头撞上去。

**完成标准：** `<packages-root>/README.md` 存在且劝阻桶文件，仓库的 `CLAUDE.md`/`AGENTS.md` 链接到它。

## 注记

- 配置的 `$1` 反向引用（dependency-cruiser 的分组匹配）让一个包够得着自己的内部、外人却不行。别把它们拍平成每个包一条的独立规则。
- 公与私由**深度**决定：包的根文件是入口；子文件夹里的任何东西是私有。惯例子文件夹是 `lib/`（实现）和 `tests/`，但规则不硬编码它们：任何子文件夹都私有，所以新文件夹永远不需要改配置。加入口只是加一个根文件（不用桶）。
- 包是**扁平的**：根下只有一层直接子级。一个包的内部想嵌多深都行；一个包里不能再有包。
- 用 `.cjs`（不是 `.js`），让配置的 `module.exports` 在 `"type": "module"` 的仓库里也能工作。
