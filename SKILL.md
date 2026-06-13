---
name: vis-design-system
description: 提供可视化图表的设计规范——共享规范（图例 / 坐标轴 / Tooltip / 滑块）、各图表类型的尺寸 / 颜色 / 交互 / 可配置项，并支持多业务线主题（base / iFinD / Ainvest / THS）在 base 主题之上的差异化覆盖。查询图表规范细节、或按业务线主题出图时触发。
version: 1.0.4
---

# 可视化设计规范（Vis Design System）

本 skill 是可视化图表的**单一事实来源**（single source of truth）。实现任何图表之前，先确定主题、再查阅对应主题文件、图表类型规范与 tokens。

> 📌 **主题分层**：所有图表规范以 [**base 主题**](references/themes/base.md)（即「默认通用 / Default / General」的参数集，下文统称 base）为基准；业务线主题（[iFinD-PC](references/themes/ifind.md) / [Ainvest](references/themes/ainvest.md) / [THS](references/themes/ths.md)）仅记录相对 base 的 **delta** 覆盖。SKILL.md 本身只承载跨主题的全局规则与索引。
>
> 📐 术语对照（base = 默认通用 = Default、PC = Web = 桌面端 等旧叫法）统一见文末 [§ 术语约定](#术语约定glossary)——边缘消歧用，无需提前记忆。

---

## 默认假设（Defaults）

> 📌 **当用户用自然语言提问、未明确指定时，按以下默认值取值**：

| 维度 | 默认值 | 说明 |
| --- | --- | --- |
| **主题** | **base 主题** | 用户未点名业务线（iFinD / Ainvest / THS）时取 base |
| **端 / 平台** | **Web / PC（桌面端）** | 用户未说"移动端 / mobile / 手机"时取 PC；表内值带 "(PC)" / "(Web)" 标记的取那个；只标 "(移动端)" 的项需查 [components/](references/components/) 获取 PC 等价值或就近推导 |
| **色彩模式** | **light（浅色）** | 用户未说 "dark / 深色 / 暗黑模式" 时取 light；tokens / 主题文件的浅 / 深双列默认取浅色列 |
| **实现库** | **不预设默认**——由模型按上下文自行选择 | 项目代码上下文有线索（如 `import echarts` / `import { Chart } from '@antv/g2'`）就跟随；用户点名（"用 ECharts / AntV / G2 / D3 / Recharts"）就用其指定；都无线索时模型按合理推断（如 React 项目偏 Recharts、数据可视化偏 ECharts、自定义可视化偏 D3）并**在输出中告知所选** |
| **图表族** | 按用户语义推导 | 见 [图表索引](#图表索引chart-index) |

**用户明确点名时按其覆盖**：
- "移动端 / 手机版" → 端改 Mobile
- "dark / 深色" → 模式改 dark
- "用 Ainvest 主题 / 海外版" → 主题改 Ainvest
- "用 ECharts / AntV / G2 / D3 / Recharts" → 实现库切换为指定

**用户问句涉及不确定时**：
- spec 标"待确认"项（如 Ainvest 柱顶圆角）→ **fallback 回 base 主题对应值**，并在输出中告知"按 base 处理"
- spec 完全缺失（如 K 线图无文档）→ 询问用户或基于 base + 常识保守生成，并标注"未在 spec 中找到"

---

## 适用场景

- **查规范**：查询图表规范细节（柱距比例 / Tooltip 颜色 / 轴标签字号 / 色板序列等）——取到值即可，不必走完查阅流程。
- **出图 / 出代码**：按主题（base / iFinD / Ainvest / THS）生成图表代码（ECharts / AntV / D3 等）——走完整 [§ 查阅顺序](#查阅顺序lookup-order)。

---

## 查阅顺序（Lookup Order）

> 📌 出图 / 出代码任务走完整流程；仅口头查个别规范值时，取到即可、不必走完。**任一步漏查 = 踩对应的坑。**

**第 0 步 · 定主题 / 端 / 库**：主题默认 base，用户点名业务线（iFinD / Ainvest / THS）则激活之（见 [§ 主题](#主题themes)）；端 / 色彩模式 / 实现库按 [§ 默认假设](#默认假设defaults) 取值。

**第 1–4 步 · 按序开文件取值**：

| # | 必开文件（base 默认 / 业务线主题分支） | 查什么 | 漏查会踩的坑 |
|---|---|---|---|
| 1 | **base**：先 [themes/base.md](references/themes/base.md)<br>**业务线**：先 [themes/\<主题\>.md](references/themes/) 拿全部 delta，**再回 [base.md](references/themes/base.md)** 补未覆盖项 | 色板 / 字体 / 图例 / 坐标轴 / Tooltip / 网格 / 尺寸等基础值 | 硬编码色值 / 字体名；忽略业务线 delta（色板 / 字体 / 间距） |
| 2 | [charts/\<chart-name\>.md](references/charts/) | 该图表专属图形规范（柱宽 / 圆角 / 环径）、变体、专属交互、**§ 主题覆盖速查** | 具体尺寸错误；忽略主题覆盖 |
| 3 | **仅 ECharts**：[echarts-implementation-hints.md](references/echarts-implementation-hints.md) | 规范 → ECharts option 映射 + 全部陷阱 | legend icon path 不可见、Tooltip marker 不跟随 legend、splitNumber 不生效、Y 轴单位行错位等 |
| 4 | 按需：[components/](references/components/)（边界细节）/ [tokens.md](references/tokens.md)（完整 token 字典） | 动画分档 / 深色模式 / 交互态 / 多端差异 | — |

> **第 3 步 hints**：用 ECharts **必查**（记录"知道规范值仍会写错 option"的高频陷阱 + 正确写法完整片段）；用 AntV / G2 / D3 / Recharts 等其他库**跳过**——映射全部针对 ECharts，按 base + chart spec 的"设计意图"自行映射目标库 API。

**通用规则**：

- base.md 末尾 **§ 被业务线主题覆盖项一览** = base ↔ 业务线 delta 横向对比速查
- 各 chart 文档末尾 **§ 主题覆盖速查** = 该图表颜色 / 字体 / 形态在主题下可能被覆盖处
- **覆盖优先级**：base → 主题级 → 图表级 → 实例级（最高），权威定义见 [§ 覆盖优先级](#覆盖优先级)
- 维度叠加方式（色板整套替换 vs 其他维度 delta 覆盖）见 [§ 维度叠加规则](#维度叠加规则)

---

## 全局规则（Global Rules）

### 全局规则：token 与数据语义

> 📌 **本节是跨所有主题成立的硬约束**——base / iFinD-PC / Ainvest / THS 等任一主题下都必须遵守。**具体取值**（字体名 / 色值 / 字重数字 / 1px 等）见 [themes/base.md](references/themes/base.md)（base 主题）或对应业务线主题文件、各图表 [charts/](references/charts/) 文件。

#### 1. 禁止硬编码，必须用 token

- 颜色值（如 `#3366ff`）、字体名（如 `'DIN Alternate'`）、字号 / 字重 / 间距 / 圆角的数字均**不得在代码中硬编码**——必须引用 [tokens.md](references/tokens.md) 中的 token
- token 的底层取值（实际 hex / font 名 / 数字）随主题切换，硬编码会破坏主题机制
- token 的 fallback 链（如 font-family 多层降级）在 token 层统一管理，代码侧不重写

#### 2. 数字与中文用不同字体 token

- 所有数值（轴刻度 / 数据标签 / Tooltip 数值 / 滑块把手数字）必须用 `font-family-number`
- 中文 / 系列名 / 单位说明等非数字部分必须用 `font-family-cn`
- 数字与中文混排时，数字部分单独包裹（`<Text>` / `<span>`）应用 `font-family-number`
- 具体字体名见 [base.md § 字体](references/themes/base.md#字体)（base = THS Money font（数字）+ 系统无衬线链（中文，Apple 端 PingFang）；Ainvest 覆盖为 `font-family-en`（Plus Jakarta Sans 链）；iFinD-PC 覆盖为 Microsoft YaHei）

#### 3. 颜色按用途使用对应 token category

- **图表系列色**：用 `color-visualization-primary` 或 `color-visualization-01` ~ `-09`（原子色 token，所有主题共享）
- **涨跌色**：用 `color-price-up` / `-down` / `-even`——**不得复用** `color-status-error` / `-success`（状态色带色弱分支与国际版变体，与涨跌语义不等价）
- 标注「可视化规范不涉及」的 token（状态色 / 辅助色 / 蒙层等）不要在图表中使用
- 具体色板序列见 [base.md § 色板](references/themes/base.md#色板按系列序号取色)；业务线主题各持独立色板，**整套替换不与 base 叠加**（见 [§ 维度叠加规则](#维度叠加规则)）

#### 4. 0 值与无数据的数据语义

- **数据为 0**：保留位置占位，数据标签显示「0」；图形按图表族特殊处理（如柱状退化为 1px 细线占位，见 [charts/bar.md § 0 值](references/charts/bar.md)）
- **无数据 / null**：完全隐藏该位置的图形与数据标签
- 区分二者是规范级硬约束——避免「无数据」被误读为「数据为 0」

#### 5. 资源文件优先于参数描述（asset over params）

> ⚠️ **本规则覆盖所有未来类似场景**——水印、品牌 logo、icon、tooltip 指示器图、自定义 marker 等只要 spec 引用了 `.svg` / `.png` / `.webp` 等文件，就吃这条。

- skill 引用的 `assets/examples/_shared/*.svg`、品牌 logo、icon 文件是**生产级资源**，不是说明图 / 示意图——出图 / 出代码时**必须直接加载使用**（`<img>` / `graphic.image` / Data URI / fetch 任一）
- **不要**按文档表里的尺寸 / 颜色 / 字形参数自行用 `rect + text` 重绘——真实资源往往是多 path 组成的字形 / 图形，任何参数都无法等价于"加载这个文件"
- 参数表（如「96×20、5% 黑、24×24」）仅用于**布局校验**——确认资源加载后的摆放、尺寸、透明度是否符合规范，**不是绘制依据**
- **典型反例**（实际发生过）：spec 写「Ainvest 水印 96×20，5% 黑」→ 模型画了一个灰矩形 + "Ainvest" 文字 ❌——真实 SVG 是 9 条 path 组成的字形 logo，矩形 + 文字根本不像
- **速判规则**：md 里 `![名字](xxx.svg)` 同时该资源被表格 / 列表 / 「来源 / 资源文件」节引用过 → **就是要直接用这个文件**，不是页面装饰图


---

## 主题（Themes）

本 skill 以 **base 主题** 为基准（[references/themes/base.md](references/themes/base.md)）。不同业务线在此基准上叠加差异化覆盖（颜色 / 间距 / 圆角 / 字体等），用**独立 delta 文件**表达——只记与 base 的差异，未列项一律继承 base。

| 主题 | 适用 | 文档 |
| --- | --- | --- |
| **base 主题** | 默认；未指定业务线时 | [references/themes/base.md](references/themes/base.md) — 所有图例 / 坐标轴 / Tooltip / 色板 / 字体 / 尺寸的基准值，配套 [components/](references/components/) / [charts/](references/charts/) / [tokens.md](references/tokens.md) |
| iFinD-PC | 同花顺旗舰业务线 / PC 静态图 | [references/themes/ifind.md](references/themes/ifind.md)（YaHei / 独立 24 色板 / 白 Tooltip / 行高=字号 / 两侧 5% 空白等） |
| iFinD-Mobile | 同花顺旗舰业务线 / 移动端 | **直接使用 [THS 主题](references/themes/ths.md)**（不维护独立规范） |
| Ainvest | 海外业务线（mobile + PC） | [references/themes/ainvest.md](references/themes/ainvest.md)（换色板 + `font-family-en` 字体；**单文件按端分节**——一、共享 delta；二、Ainvest-Mobile 专有；三、Ainvest-PC 专有[待补]） |
| THS | 同花顺主站 / 手炒 / 也是 iFinD-Mobile 的实现 | [references/themes/ths.md](references/themes/ths.md)（独立 9 色色板、柱顶 2px 圆角、桑基语义反转等） |

**主题选择规则**：

- **默认用 base 主题**——用户未点名业务线时，只查 base.md 与配套基准文档，不叠加任何 delta。
- 用户**点名业务线**（如「用 Ainvest 主题画柱状图」「iFinD 风格」）时，激活该主题：在 base 之上叠加 `references/themes/<主题>.md` 的覆盖。

**覆盖优先级**见下 [§ 覆盖优先级](#覆盖优先级)（base → 主题级 → 图表级 → 实例级）。

**端（mobile / PC）的处理**：端**不是完全正交**于业务线主题——同一业务线在不同端常有独立 delta（如 iFinD-PC 和 iFinD-Mobile 几乎是两套不同主题；Ainvest-Mobile 与 Ainvest-PC 也各自有端专属覆盖）。处理约定：

| 端差异规模 | 文档组织 | 当前实例 |
| --- | --- | --- |
| 差异大、几乎两套 | **拆文件**（`<业务>-pc.md` / `<业务>-mobile.md`，或一边沿用另一主题文件） | iFinD：PC 用 [ifind.md](references/themes/ifind.md)，Mobile = [ths.md](references/themes/ths.md) |
| 差异中、可共存一文件 | **单文件按端分节**（"共享 delta / Mobile 专有 / PC 专有"） | Ainvest：[ainvest.md](references/themes/ainvest.md) 按此三节组织 |
| 差异小、表内并列即可 | **单文件、表内单元格双值并列**（如 `10px（移动端） / 12px（PC）`） | base：[base.md](references/themes/base.md) 通篇此模式 |

**色彩模式**（light / dark）仍与业务线主题正交——同一主题文件在 light / dark 下都成立，沿用 tokens.md 的浅 / 深双列。

### 覆盖优先级

⚠️ **本节是「配置覆盖优先级」的权威定义**——其他文件（[tokens.md](references/tokens.md)、各主题文件元信息行、[base.md § Z-Index 层级](references/themes/base.md#z-index-层级)）一律引用此处，不再各自重列。

**优先级链**（后者覆盖前者，越靠后越具体、优先级越高）：

> 全局 token / base 主题 → 主题级 → 图表级 → 实例级（最高）

| 档位 | 作用范围 | 例 |
| --- | --- | --- |
| 全局 token / base | 所有图、所有主题的默认基准 | 柱顶圆角默认 0px |
| 主题级 | 某条**业务线**的所有图（见 [themes/](references/themes/)） | Ainvest 全图改用 `font-family-en` |
| 图表级 | 某一**类**图表的规范 | 所有柱状图最大柱宽 32px |
| 实例级（最高） | 某**一个**具体图表出图时自传的覆盖——最具体 | 首页那一根营收柱图，手动把某根柱改红 / 单独设 `barWidth` |

> **「图表级 vs 实例级」**：图表级是"柱状图**这个类型**"的规则、对每个柱图都生效；实例级只对**那一个**具体图表生效，且压过前面所有档（ECharts 里即该图 `setOption({...})` 写死的值）。
>
> 与下方 [§ 维度叠加规则](#维度叠加规则) 的关系：**本节定「谁覆盖谁」（排序）**；维度叠加规则定**「每个维度怎么合并」**（色板整套替换 vs 其他 delta 覆盖）。两者配合使用。

### 维度叠加规则

⚠️ **不同维度的覆盖方式不同**——不是所有维度都走 delta 叠加。模型 / 开发者按下表判断"业务线主题文件里没列这一项，要不要回 base 取值"：

| 维度 | 叠加方式 | 说明 |
| --- | --- | --- |
| **色板**（顺序色 / 折线 / 柱图 / 折柱组合 / 浅色色盘 / datazoom 选中色） | **整套替换** | 每个主题各持完整一套色板，**不与 base 叠加**。业务线主题文件里列的色板就是该主题用的全部；base 的色板序列**不参与**。**iFinD-mobile = 整套使用 THS 主题**（不止色板，所有维度都来自 THS）。 |
| **涨跌色**（`color-price-up` / `-down` / `-even` / 桑基涨跌语义色） | **delta 覆盖** | base 是默认；业务线主题文件中列出的覆盖项以业务线为准（如 THS 桑基语义反转、Ainvest 桑基汇正绿/汇负红） |
| **字体 / 字号 / 行高 / 字重** | delta 覆盖 | 业务线只记差异（如 Ainvest 全图 `font-family-en`），未列项继承 base |
| **间距 / 圆角 / 尺寸** | delta 覆盖 | 同上（如 THS 柱顶 2px 圆角覆盖 base 的 0px） |
| **Tooltip / 光标 / 滑块 / 网格 / 轴 形态** | delta 覆盖 | 同上（如 Ainvest 三角 Tooltip、iFinD-PC 虚线光标 + 白 Tooltip） |
| **水印** | 整套替换 | 业务线主题各自指定 logo / 文字 |

**速判**：查色板就**只**看当前主题文件那一节，绝不混 base；查其他维度先看主题文件 delta，没列就回 base 取值。


---

## 图表索引（Chart Index）

### 类别比较 / 趋势分析

| 图表 | 文档 |
| --- | --- |
| 基础柱状图（Bar） | [references/charts/bar.md](references/charts/bar.md) |
| 分组柱状图（Grouped Bar） | [references/charts/grouped-bar.md](references/charts/grouped-bar.md) |
| 折柱组合图（Bar + Line） | [references/charts/bar-line-combo.md](references/charts/bar-line-combo.md) |

### 排名 / 长标签

| 图表 | 文档 |
| --- | --- |
| 基础条形图（Horizontal Bar，横向） | [references/charts/horizontal-bar.md](references/charts/horizontal-bar.md) |

### 部分占整体 / 构成

| 图表 | 文档 |
| --- | --- |
| 堆叠柱状图（Stacked Bar） | [references/charts/stacked-bar.md](references/charts/stacked-bar.md) |
| 归一化堆叠柱状图（Normalized Stacked Bar，百分比堆叠） | [references/charts/normalized-stacked-bar.md](references/charts/normalized-stacked-bar.md) |
| 矩形树图（Treemap） ⚠️ | [references/charts/treemap.md](references/charts/treemap.md) |

### 趋势 / 时间序列（折线图族）

| 图表 | 文档 |
| --- | --- |
| 基础折线图（Line） | [references/charts/line.md](references/charts/line.md) |
| 多折线图（Multi-Line） | [references/charts/multi-line.md](references/charts/multi-line.md) |
| 区域高亮折线图（Area-Highlight Line） | [references/charts/area-highlight-line.md](references/charts/area-highlight-line.md) |
| 特殊标记折线图（Marker Line） | [references/charts/marker-line.md](references/charts/marker-line.md) |
| 排名线图（Rank Line） ⚠️ | [references/charts/rank-line.md](references/charts/rank-line.md) |

### 占比 / 比例（饼图族）

| 图表 | 文档 |
| --- | --- |
| 饼图（Pie） | [references/charts/pie.md](references/charts/pie.md) |
| 环状图（Donut） | [references/charts/donut.md](references/charts/donut.md) |
| 半环状图（Half-Donut） | [references/charts/half-donut.md](references/charts/half-donut.md) |
| 花瓣图（Petal） ⚠️ | [references/charts/petal.md](references/charts/petal.md) |

### 多维对比

| 图表 | 文档 |
| --- | --- |
| 雷达图（Radar） | [references/charts/radar.md](references/charts/radar.md) |

### 分布

| 图表 | 文档 |
| --- | --- |
| 散点图（Scatter） ⚠️ | [references/charts/scatter.md](references/charts/scatter.md) |
| 蜂群图（Beeswarm） ⚠️ | [references/charts/beeswarm.md](references/charts/beeswarm.md) |

### 流程 / 累积变化

| 图表 | 文档 |
| --- | --- |
| 瀑布图（Waterfall） ⚠️ | [references/charts/waterfall.md](references/charts/waterfall.md) |

### 层级关系

| 图表 | 文档 |
| --- | --- |
| 双向树图（Two-Way Tree） ⚠️ | [references/charts/two-way-tree.md](references/charts/two-way-tree.md) |

### 流向 / 网络关系

| 图表 | 文档 |
| --- | --- |
| 桑基图（Sankey） ⚠️ | [references/charts/sankey.md](references/charts/sankey.md) |
| 关系图谱（Relationship Diagram） ⚠️ | [references/charts/relationship-diagram.md](references/charts/relationship-diagram.md) |

### 集合关系

| 图表 | 文档 |
| --- | --- |
| 韦恩图（Venn） ⚠️ | [references/charts/venn.md](references/charts/venn.md) |

### 文本可视化

| 图表 | 文档 |
| --- | --- |
| 词云（Word Cloud） ⚠️ | [references/charts/word-cloud.md](references/charts/word-cloud.md) |

### 辅助标记（叠加到其他图表）

| 图表 | 文档 |
| --- | --- |
| 标记系列（Marker） ⚠️ | [references/charts/marker.md](references/charts/marker.md) |

> ⚠️ 标记的图表来自 paradigm-chart 代码实现，**尚无独立设计 PDF**——文档基于工程实现整理，等设计师补 PDF 后复核。


---

## 图表结构（Chart Structure）

「图表结构」是顶层布局概念，不是具体图表类型。当一个完整界面由多个独立绘制区组成时，按以下结构规范布局：

| 结构 | 文档 | 说明 |
| --- | --- | --- |
| 主副图（Main / Sub） | [references/main-sub.md](references/main-sub.md) | 上下两个独立绘制区共享 X 轴和滑块（如 K 线主图 + MACD 副图） |


---

## Base 主题高频踩坑速查（Gotchas）

> 📌 **base 主题的完整取值表在 [references/themes/base.md](references/themes/base.md)**——画 base 图直接打开该文件取值，**不要从本节找具体数值**。本节只留几条**反直觉 / 易写错**的默认值作防错提醒；其余值（图表尺寸、字体、色值、Tooltip 背景、Z-Index 等）一律去 base.md。
>
> base.md 中标「⚠️ 主题差异点」的项即高频被业务线主题覆盖项；用了业务线主题就再打开对应主题文件叠 delta。

| 易错点 | 正确值（反直觉处） | 详查 |
| --- | --- | --- |
| 图例默认对齐 | **左对齐**（不是 center） | [base.md § 图例legend](references/themes/base.md#图例legend) |
| Y 轴分割线 | **强制 5 条**（`splitNumber:4` 仅建议，需配 `min/max/interval`） | [base.md § 网格线grid](references/themes/base.md#网格线grid) + [echarts-implementation-hints.md 陷阱 3](references/echarts-implementation-hints.md) |
| 形式 A 数据内缩 | Y 轴标签在 grid 内部时，数据区两侧 **10%** 内缩；value/time 轴用 `boundaryGap:['10%','10%']`；category 轴 `boundaryGap:true` 仅 N≤5 达标，**N≥6 首尾补空类目** | [layout.md § Y 轴标签布局](references/components/layout.md#y-轴标签布局) + [echarts-implementation-hints.md 陷阱 19](references/echarts-implementation-hints.md) |
| 柱体最大宽 | 32px（**不是 16px**） | [base.md § 关键尺寸](references/themes/base.md#关键尺寸) |


---

## Tokens & Assets 索引

- **可视化专属 tokens**：[references/tokens.md](references/tokens.md)（色板 / 字号 / 字体 / 字重 / 间距 / 圆角）；**z 轴层级（z-index）见 [base.md § Z-Index 层级](references/themes/base.md#z-index-层级)**
- **组件规范**（原 shared.md，按组件拆分）：
  - [components/layout.md](references/components/layout.md) — 整体布局 / 尺寸 / 字体规则 / 动画 / 多端差异
  - [components/legend.md](references/components/legend.md) — 图例容器 / 标记形状 / 排列 / 交互 / 分页器
  - [components/axes.md](references/components/axes.md) — X/Y 轴标签 / 轴标题 / 刻度线 / 双 Y 轴
  - [components/grid.md](references/components/grid.md) — 网格线 / 0 轴线
  - [components/tooltip.md](references/components/tooltip.md) — Tooltip 气泡 / 光标线 / 高亮标签轴 / 交互行为
  - [components/data-label.md](references/components/data-label.md) — 数据标签通用规范
  - [components/datazoom.md](references/components/datazoom.md) — 数据缩放滑块
  - [components/playback.md](references/components/playback.md) — 动态播放轴
  - [components/watermark.md](references/components/watermark.md) — 水印通用结构 / 加载机制 / 锚定基准 / 资源协议
- **图表结构**：[references/main-sub.md](references/main-sub.md)（主副图结构概念）
- **主题（Themes）**：[references/themes/](references/themes/)
  - [base.md](references/themes/base.md) — **base 主题（基准）**，所有图例 / 坐标轴 / Tooltip / 色板 / 字体 / 尺寸的一站式查询表
  - [ifind.md](references/themes/ifind.md) — iFinD-PC 主题（YaHei / 24 色板 / 白 Tooltip 等；**iFinD 移动端不在此文件，直接用 THS 主题**）
  - [ainvest.md](references/themes/ainvest.md) — Ainvest 主题（相对 base 的 delta；换色板 + `font-family-en` 字体等）
  - [ths.md](references/themes/ths.md) — THS 主题（相对 base 的 delta；可视化色板覆盖）
- **ECharts 实现提示**（**仅当用 ECharts 实现时必查**）：[references/echarts-implementation-hints.md](references/echarts-implementation-hints.md)（规范→ECharts option 的高频踩坑速查——legend icon path 写法、splitNumber 强制算法、Tooltip marker 同步、z-index 映射等）。用 AntV / G2 / D3 / Recharts 等其他库实现时**跳过此文件**，按设计意图自行映射到目标库 API。
- **示例图**：[assets/examples/](assets/examples/)（按图表名分子目录；共享元素示意图在 `_shared/`；token 规范图在 `_tokens/`）


---

## 术语约定（Glossary）

> 📌 边缘消歧用——正文一律使用下表**左列**写法；读到右列旧叫法时回此处对照即可，无需提前记忆。

| 术语（正文统一写法） | 等价 / 旧叫法 | 指向 |
| --- | --- | --- |
| **base 主题** | 默认通用主题 / Default / General theme / 通用基准 | [themes/base.md](references/themes/base.md) 中的参数集（所有业务线主题的基准） |
| **PC** | Web / 桌面端 | 非移动端；表内带 "(PC)" / "(Web)" 标记的值同义 |
| **移动端** | Mobile / 手机 / 触屏端 | 表内带 "(移动端)" 标记的值 |

> 各 reference 文件中如出现「Web 1px」「PC 12px」等写法，均按本表指桌面端。
