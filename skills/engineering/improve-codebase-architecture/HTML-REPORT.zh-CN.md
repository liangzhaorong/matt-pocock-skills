# HTML 报告格式

架构审查以单个自包含的 HTML 文件呈现，存放在操作系统的临时目录中。Tailwind 和 Mermaid 均通过 CDN 加载。Mermaid 能可靠地处理图结构的示意图；手工构建的 div 和内联 SVG 则用于更具编辑设计感的视觉表现（体量示意图、剖面图）。两者结合使用：不要什么都依赖 Mermaid，否则报告会开始显得千篇一律。

## 骨架

```html
<!doctype html>
<html lang="zh-CN">
  <head>
    <meta charset="utf-8" />
    <title>{{repo name}} 的架构审查</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script type="module">
      import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@11/dist/mermaid.esm.min.mjs";
      mermaid.initialize({ startOnLoad: true, theme: "neutral", securityLevel: "loose" });
    </script>
    <style>
      /* 少量自定义样式，用于 Tailwind 不便处理的细节：
         虚线接缝、带手绘感的箭头等。 */
      .seam { stroke-dasharray: 4 4; }
      .leak { stroke: #dc2626; }
      .deep { background: linear-gradient(135deg, #0f172a, #1e293b); }
    </style>
  </head>
  <body class="bg-stone-50 text-slate-900 font-sans">
    <main class="max-w-5xl mx-auto px-6 py-12 space-y-12">
      <header>...</header>
      <section id="candidates" class="space-y-10">...</section>
      <section id="top-recommendation">...</section>
    </main>
  </body>
</html>
```

## 页首

仓库名称、日期，以及简洁的图例：实线框 = 模块，虚线 = 接缝，红色箭头 = 泄漏，深色粗边框 = 深模块。不写介绍段落。直接进入候选项。

## 候选项卡片

由图表承担主要的表达任务。文字要少、直白，自然使用术语表中的术语（来自 `/codebase-design` 技能），不必刻意铺陈。

每个候选项使用一个 `<article>`：

- **标题**：简短，点明要做的深化（例如“合并 Order 接收流水线”）。
- **徽章行**：推荐程度（`Strong` = 翠绿色，`Worth exploring` = 琥珀色，`Speculative` = 石板灰色），加上依赖类别标签（`in-process`、`local-substitutable`、`ports & adapters`、`mock`）。
- **文件**：等宽字体列表，使用 `font-mono text-sm`。
- **改造前 / 改造后示意图**：核心内容。两列并排展示。参见下文的图表模式。
- **问题**：一句话说明痛点。
- **解决方案**：一句话说明变化。
- **收益**：项目符号列表，每条不超过 6 个词。例如“测试只针对一个接口”“定价逻辑不再泄漏”“删除 4 个浅包装器”。
- **ADR 提示**（如适用）：在琥珀色底的框中写一行提示。

不要用段落解释。如果一张图需要配一段话才能理解，就重画这张图。

## 图表模式

选择适合候选项的模式，混合使用。不要让每张图看起来都一样。多样性本身就是目标的一部分。

### Mermaid 图（依赖关系 / 调用流程的主力工具）

当重点是“X 调用 Y，Y 调用 Z，看看这有多乱”时，使用 Mermaid 的 `flowchart` 或 `graph`。将其放在用 Tailwind 设置样式的卡片中，避免显得突兀。使用 classDef 设置样式，将泄漏边标为红色、深模块标为深色。对于“改造前：6 次往返；改造后：1 次”这样的情况，时序图很合适。

```html
<div class="rounded-lg border border-slate-200 bg-white p-4">
  <pre class="mermaid">
    flowchart LR
      A[OrderHandler] --> B[OrderValidator]
      B --> C[OrderRepo]
      C -.泄漏.-> D[PricingClient]
      classDef leak stroke:#dc2626,stroke-width:2px;
      class C,D leak
  </pre>
</div>
```

### 手工构建方框与箭头（当 Mermaid 的布局难以满足需求时）

用带边框和标签的 `<div>` 表示模块。箭头使用内联 SVG 的 `<line>` 或 `<path>` 元素，在相对定位的容器上方进行绝对定位。如果你希望“改造后”的图呈现为一个粗边框的深模块，内部结构灰显，就采用这种方式，因为 Mermaid 无法以恰当的视觉分量呈现这种效果。

### 剖面图（适合展示层层堆叠的浅层结构）

堆叠水平条带（`h-12 border-l-4`），展示一次调用穿过的各层。改造前：6 个几乎不做任何事的薄层。改造后：1 个粗条带，标明合并后的职责。

### 体量示意图（适合展示“接口与实现一样宽”的情况）

每个模块使用两个矩形：一个表示接口表面积，一个表示实现。改造前：接口矩形几乎与实现矩形一样高（浅）。改造后：接口矩形矮，实现矩形高（深）。

### 调用图折叠

改造前：将函数调用树绘制为嵌套方框。改造后：同一棵树折叠成一个方框，原有调用现已成为内部调用，在框内以淡化效果显示。

## 样式指导

- 偏向编辑设计风格，而非企业仪表盘。留白充足。标题可选用衬线字体（`font-serif` 与石色 / 石板灰色搭配效果很好）。
- 克制用色：一种强调色（翠绿色或靛蓝色），再加上表示泄漏的红色和表示警告的琥珀色。
- 图表高度保持在约 320px，使改造前后能够舒适地并排展示，无需滚动。
- 图中模块标签使用 `text-xs uppercase tracking-wider`，使其看起来像示意图标注，而不是 UI。
- 唯一的脚本是 Tailwind CDN 脚本和 Mermaid ESM 导入。除此之外，报告都是静态的：没有应用代码，也没有超出 Mermaid 自身渲染的交互。

## 首选建议部分

使用一张较大的卡片。包含候选项名称、一句话说明原因，以及跳转到该候选项卡片的锚点链接。仅此而已。

## 语气

使用通俗、简洁的英语，但架构相关的名词和动词要直接取自 `/codebase-design` 技能。简洁不是偏离术语的借口。

**准确使用：** 模块（module）、接口（interface）、实现（implementation）、深度（depth）、深（deep）、浅（shallow）、接缝（seam）、适配器（adapter）、杠杆（leverage）、局部性（locality）。

**绝不要替换为：** 组件（component）、服务（service）、单元（unit）（替代模块）· API、签名（signature）（替代接口）· 边界（boundary）（替代接缝）· 层（layer）、包装器（wrapper）（本意是模块时，用它们替代模块）。

**符合此风格的表述：**

- “Order 接收模块很浅：接口几乎与实现一样复杂。”
- “定价逻辑跨接缝泄漏。”
- “深化：一个接口，一个测试位置。”
- “两个适配器证明了接缝存在的合理性：生产环境使用 HTTP，测试使用内存适配器。”

**收益列表项**应使用术语表中的术语说明所得：*“局部性：bug 集中在一个模块中”*、*“杠杆：一个接口，N 个调用点”*、*“接口缩小；实现吸收包装器”*。不要写*“更容易维护”*或*“代码更整洁”*，因为这些说法不在术语表中，也没有足够的价值值得写进去。

不要含糊其辞，不要铺垫，也不要说“值得注意的是……”。如果一个句子可以写成列表项，就写成列表项。如果一个列表项可以删，就删掉。如果某个术语不在 `/codebase-design` 的术语表中，先尝试使用已有术语，再考虑创造新词。
