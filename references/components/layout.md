# 整体布局（Layout）

> 📌 **常用值已在 [themes/base.md（base 主题）](../themes/base.md) 内联**——画 base 主题图表时直接从那里取值。本文件是详细规范，仅在需要边界细节（动画分档、交互态、多端差异、配置选项等）时查阅。用了业务线主题（iFinD-PC / Ainvest / THS）请同时查 [themes/](../themes/) 下对应 delta 文件。

图表的整体尺寸、区域结构、多端差异。

---

## 尺寸与结构

| 属性 | 值 | Token |
| --- | --- | --- |
| 图表整体宽度 | 343px（标准移动端） | `size-chart-width` |
| 区域布局 | flex-col（图例 / 图表区 / 滑块） | — |
| 三区域垂直间距 | 8px | `spacing-chart-region-gap` |
| 图表绘制区高度 | 160px | `size-chart-region-height` |

> 设计 PDF 用占比（95% / 5%）表达高度，未给绝对值；160px 为 paradigm-chart 代码侧 grid 实证高度。

结构：

```
[ 图例区域           ]   ← 始终显示
[ 图表绘制区          ]   ← 160px 高
[ 范围滑块           ]   ← 仅"看板=on"时显示，24px 高
```

主副图结构（K 线主图 + MACD 副图等）见 [main-sub.md](../main-sub.md)。

---

## Y 轴标签布局

Y 轴标签有两种布局形式，**默认使用形式 A**：

### 形式 A：坐标轴在网格内部 + 标签避让网格线（默认）

![Y 轴标签避让网格线](../../assets/examples/_shared/y-axis-label-position.png)

- Y 轴标签**渲染在 grid 绘图区内部**（不进入 grid 外的 padding 圈）
- **顶部轴标签**（最大值）：与**最顶部网格线顶对齐**——标签顶边贴线，向下延伸进第一个网格单元
- **其余轴标签**（含中间值与 0 值）：分别落在各自对应的网格线上，**底对齐**——标签底边贴线，向上延伸进上方网格单元
- 因此每个标签始终位于其对应网格线的**单侧**（顶标签在线下、其余在线上），与网格线本身**永不重叠**
- **数据绘制范围左右内缩**：因 Y 轴标签住在 grid 内，柱体 / 折线 / 散点等数据图形的起止 X 范围必须从 grid 左右边缘**进一步向内缩进**，让出 Y 轴标签所在的水平条带——**条带起点 = grid 边缘**（紧贴内侧），条带宽度 ≥ 该侧 Y 轴最长标签宽度 + 安全间距；数据图形从条带末端（= 数据绘制区起点）开始绘制，与标签**水平分离、永不重叠**
  - 单 Y 轴（仅一侧有标签）：仅该侧需内缩
  - 双 Y 轴：两侧均需内缩
  - 形式 B 不存在此约束（标签在 grid 外侧）
- **内缩配置**：`xAxis.boundaryGap: ['10%', '10%']`（数据区两侧各从 grid 边缘向内 10%；2026-06-12 由 5% 上调——加大与内嵌 Y 标签的水平间隔，规避数据图形遮挡标签）
- **适用图表族**：

  | 适用 | 图表 |
  | --- | --- |
  | ✅ 柱状（category X） | bar / grouped-bar / stacked-bar / normalized-stacked-bar / waterfall / bar-line-combo |
  | ✅ 折线（category X） | line / multi-line / area-highlight / marker-line / rank-line / marker |
  | ❌ 不适用 | horizontal-bar（Y=category，本规则与之无关）/ 无 grid 类（pie / donut / half-donut / petal / radar / treemap / sankey / relationship / two-way-tree / venn / word-cloud） |
- **ECharts 落地**：value / time 轴用 `boundaryGap: ['10%', '10%']` 直接生效；**category 轴**（即上表 12 个适用图表）ECharts 不支持数组形式 boundaryGap——`boundaryGap: true`（半 band 缓冲，每侧 ≈ `1/(2N)`）仅 **N ≤ 5** 时满足 10%；**N ≥ 6 需首尾补空类目**（categories 两端各加 `''`、series 数据补 `null`；每侧内缩 = `(0.5+k)/(N+2k)`，k = 每侧空类目数，k=1 覆盖 N ≤ 13、k=2 覆盖 N ≤ 21）。详见 [echarts-implementation-hints.md § 陷阱 19](../echarts-implementation-hints.md)

### 形式 B：坐标轴在网格外部 + 标签与网格线居中对齐

![Y 轴标签与网格线居中对齐](../../assets/examples/_shared/y-axis-grid-spacing.png)

- 坐标轴位于网格**外部**（左右两侧）
- 标签与网格线**上下居中对齐**
- 网格线与坐标轴间距：**8px**

---

## 0 值与无数据的视觉规则

| 情况 | 柱状图 | 折线图 |
| --- | --- | --- |
| 数值 = 0 | 显示 1px 粗细的柱条占位 | 节点正常显示在 0 位置 |
| 无数据 / null | 完全隐藏该位置柱条 | 折线断开，不连接前后点 |
| 数据标签 | 0 值：显示「0」　/ 无数据：不显示 | 同左 |

---

## 字体规则（硬约束）

- **所有数值用 `font-family-number`**（THS Money font）
- 数值单位（万 / 亿 / %）用 **font-family-number Bold**
- 中文系列名 / 标题 / 单位说明用 `font-family-cn`
- 数字与中文混排，数字部分单独节点包裹
- 禁止硬编码字体名

---

## 动画（Animation）

### 入场动画（柱状图）

柱状图入场动画时长**按绘制区高度分档**——图越高，动画越长，保持视觉节奏一致：

| 绘制区高度 | 动画时长 | 缓动 |
| --- | --- | --- |
| 0 – 80px | 320ms | `cubicOut` |
| 80 – 160px | 480ms | `cubicOut` |

### 折线图动画 / 性能

| 项 | 规则 |
| --- | --- |
| 主线选中放大 | 主线数据点在悬停 / 选中态放大 **1.2 倍** |
| 高密度采样 | 数据量大时启用降采样算法，避免渲染卡顿（不影响视觉趋势） |

### 通用

- 交互态（Tooltip / 光标）**无过渡动画**，紧跟鼠标
- 缓动统一用 `cubicOut`（先快后慢，自然减速）

---

## 水印（Watermark）

通用结构 / 加载机制 / 锚定基准 / 资源协议见 [components/watermark.md](watermark.md)。各主题具体 logo / 透明度 / 锚定见 [themes/](../themes/) 各主题文件 § 水印。

---

## 多端差异（Mobile / PC）速查

> 📐 术语对照（PC = Web = 桌面端、移动端 = Mobile 等）见 [SKILL.md § 术语约定](../../SKILL.md#术语约定glossary)。各组件文件中「Web 1px」「PC 12px」等写法均按该表指桌面端。

本规范的尺寸 / 字号默认以**移动端**为基准。具体差异分散在各组件文件中，速查索引如下：

| 项             | 文件                 | 说明                         |
| ------------- | ------------------ | -------------------------- |
| 轴标签 / 轴标题字号 | [axes.md](axes.md) | 字号 10 → 12px（颜色多端一致） |
| 网格线 / 0 轴线宽   | [grid.md](grid.md) | 移动 0.5px → Web 1px         |

> 各 chart 文档与 [tokens.md](../tokens.md) 中标注的字号值（如「轴标签 10px」）默认指移动端，其余规范（颜色 / 圆角 / 交互逻辑）多端一致。

---

## Token 映射汇总（共享元素）

| 元素分类 | Token 字号 | Token 字重 | Token 颜色 |
| --- | --- | --- | --- |
| A：图例 / 分页 | `font-size-extra-small` | 配置 | `color-text-secondary` |
| B：轴标题 / 数据标签 | `font-size-xxs` | `font-weight-medium` | `color-text-secondary` |
| C：轴标签 | `font-size-xxs` | 配置 | `color-text-secondary` |
| D：高亮标签轴 | `font-size-xxs` | `font-weight-bold` | `color-text-inverse` |
| D：高亮标签轴背景 | — | — | `color-visualization-highlight-background-tick` |
