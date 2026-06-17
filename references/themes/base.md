# Base 主题（Theme: base / 默认通用 / Default / General）

> 📌 **本文件是 base 主题的一站式查询表**——画 base 主题图表时，直接从此处取值，无需逐个打开 [components/](../components/) 文件。仅当需要边界细节（动画分档、交互态、多端差异等）时再进 [components/](../components/) 详查。
>
> 📐 **本文件 = 所有业务线主题的基准**。iFinD-PC / Ainvest / THS 等业务线主题仅记录相对本文件的 **delta（差异）**，未在主题 delta 中列出的项一律继承本文件。
>
> 名词约定：「base 主题」=「默认通用」=「Default / General theme」——同一概念三种叫法，统一称 **base 主题**。详见 [SKILL.md § 术语约定](../../SKILL.md#术语约定glossary)。
>
> 用了业务线主题（[iFinD-PC](ifind.md) / [Ainvest](ainvest.md) / [THS](ths.md)）？打开对应主题文件叠 delta；或翻到本文件末尾 [§ 被业务线主题覆盖项一览](#被业务线主题覆盖项一览cross-theme-diff-map) 横向对比。
>
> 📐 术语对照（PC = Web = 桌面端、移动端 = Mobile 等）见 [SKILL.md § 术语约定](../../SKILL.md#术语约定glossary)。下表「Web 1px」「PC 12px」等写法均按该表指桌面端。

---

## 主题元信息

| 项 | 值 |
| --- | --- |
| 主题名 | **base 主题** ／ 别名：默认通用（General / Default） |
| 适用 | 默认；用户未指定业务线时 |
| 继承基准 | 无——本文件**即**基准；原子 token 见 [../tokens.md](../tokens.md) |
| 端 | mobile / PC 同时支持，多端差异沿用 [components/](../components/) 中的细节 |
| 色彩模式 | light / dark 双列写法（沿用 [../tokens.md](../tokens.md)） |
| 覆盖优先级 | 本文件 → 业务线主题 delta → 图表级 → 实例级（最高）（详见 [SKILL § 覆盖优先级](../../SKILL.md#覆盖优先级)） |

---

## 整体布局

| 元素 | 值 |
| --- | --- |
| 图表整体宽度（移动端） | 343px |
| 图表绘制区高度 | 160px |
| 三区域垂直间距 | 8px |

## 字体

| 用途 | 字体 |
| --- | --- |
| 所有数值 | `font-family-number`（THS Money font） |
| Tooltip 数值单位（万/亿/%） | `font-family-number` **Bold** 600 |
| 中文 / 系列名 / 标题 | `font-family-cn`（系统无衬线 fallback 链） |

## 色板（按系列序号取色）

> ⚠️ **色板维度不走 delta 继承**——每个主题各持完整一套色板，互不叠加。本节是 **base 主题**的色板；使用其他业务线主题（iFinD-PC / Ainvest / THS）时**整套切换**为该主题的色板，不混用本节序列。详见 [SKILL.md § 主题维度叠加规则](../../SKILL.md#维度叠加规则)。

**折线色板**（多折线 / 雷达，6 色）：
```
1. #3366FF (visualization-primary)    2. #FF9500 (vis-02)
3. #CC41D9 (vis-08)                   4. #14CCBD (vis-04)
5. #199FFF (vis-05)                   6. #858585 (vis-09，中性色)
```

**柱图色板**：**直接使用折线色板**（同序列、同色值）

**折柱组合图色板**（柱与折线分别独立取色，避免撞色，详见 [../tokens.md](../tokens.md#可视化色板sequential-palette-核心)）：

- 折线：`color-visualization-02`（#FF9500）→ `color-visualization-09`（#858585）
- 柱状：`color-visualization-primary`（#3366FF）→ `-04`（#14CCBD）→ `-05`（#199FFF）→ `-06`（#4433FF）→ `-07`（#FF33AA）→ `-08`（#CC41D9）

**单系列默认色**：`#3366FF`（`color-visualization-primary`）

**涨跌色**：上涨 `#FF2436`（`color-price-up`）/ 下跌 `#07AB4B`（`color-price-down`）

## 图例（Legend）

| 项 | 值 |
| --- | --- |
| **默认对齐** | **左对齐**（`left:'left'`，不是 center） |
| **单系列也必须显示图例** | 给 series 加 `name`，配置 legend |
| 标记本体 | **6×6px**（柱/饼/雷达） 或 **8×2px**（折线短横线） |
| 标记容器(点击热区) | 12×12px |
| 标记颜色 | `currentColor` 跟随系列色 |
| 标记↔文字间距 | **2px**（`spacing-legend-marker-label`） |
| 文字字号 / 字体 / 颜色 | 12px / `font-family-cn` Regular / `color-text-secondary` |
| 项左右间距（同行内相邻 legend 项，`spacing-legend-item-h`） | 12px |
| 项上下间距（换行时两行之间，`spacing-legend-row-v`） | **4px** ⚠️ ECharts 内置 legend 在含方块 icon 的图表上 stride 多 4px，需 SVG 后处理破解，见 [../echarts-implementation-hints.md 陷阱 16](../echarts-implementation-hints.md) |
| 折柱混合图例 | **每系列独立 icon**——柱用方块、线用短横线，不统一 |
| 溢出 | 移动端 换行→（超 2 行仍装不下）滑动；Web/PC 换行→分页器。详见 [components/legend.md § 图例溢出](../components/legend.md#图例溢出) |
| 与绘图区 | 不允许重叠，图例占位优先 |

## 坐标轴（Axes）

| 项             | 值                                                                                                                                                           |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
| X / Y 轴标签字号   | **10px**（移动端） / 12px（PC）                                                                                                                                    |
| 轴标签字体 / 字重    | `font-family-number` Medium                                                                                                                                 |
| 轴标签颜色         | `color-text-secondary`（`rgba(0,0,0,0.6)`）                                                                                                                   |
| 主 Y 轴位置       | **左侧**                                                                                                                                                      |
| Y 轴标签布局形式     | **形式 A**（坐标轴在网格内部 + 标签避让网格线，默认）；详见 [../components/layout.md § Y 轴标签布局](../components/layout.md#y-轴标签布局)                                                    |
| Y 轴分割线数量      | **5 条**（`splitNumber: 4` 在 ECharts 中是 4 段=5 线）⚠️ 单设 `splitNumber:4` 不够，ECharts 仍可能输出 3/6 段；必须配合 `min/max/interval` 三件套强制对齐，见下方"分割线段数严格 4 段算法"               |
| X 轴标签对齐       | 中间标签居中；**首尾对齐取决于数据是否贴 grid 边缘**（`boundaryGap:false` 首左/尾右防裁、`boundaryGap:true` 全居中）——权威规则与落地统一见 [../components/axes.md § X 轴标签](../components/axes.md) |
| 轴刻度线          | 默认不显示                                                                                                                                                       |
| 轴标题           | 默认不显示                                                                                                                                                       |
| Y 轴单位（亿元 / %） | 当存在时，**独占一行**位于「图例下方 / 刻度上方」；**绝不与图例同行**。图表宽度变化引发图例换行时，单位行须跟随下移（动态布局）。见 [../echarts-implementation-hints.md 陷阱 14](../echarts-implementation-hints.md) |

## 网格线（Grid）

| 项 | 值 |
| --- | --- |
| Y 方向网格线 | **默认显示**，5 条横向（含 0 轴） |
| X 方向网格线 | 默认隐藏 |

**强制 5 条分割线**：`splitNumber:4` 只是建议值，ECharts 在不同数据下仍会输出 3 / 6 / 7 段。所有数值轴必须配合 `min` / `max` / `interval` 三件套强制对齐。完整算法（含 `niceScale` 函数 + 校验范例 + 7 个易错点）见 [../echarts-implementation-hints.md 陷阱 3](../echarts-implementation-hints.md)。

| 项 | 值 |
| --- | --- |
| 线宽 | 0.5px（移动端） / 1px（Web） |
| 网格线颜色 | `color-visualization-divider`（`rgba(0,0,0,0.06)`） |
| **0 轴线** | **颜色加深 `#858585`**（`color-visualization-divider-deep`），与普通网格区分 |

## Tooltip / 提示框

| 项 | 值 |
| --- | --- |
| 显示位置 | **位置 A**：跟随鼠标右下，按容器余量自适应翻转（左半区→靠右、右半区→靠左）；详见 [tooltip.md § Tooltip 显示位置](../components/tooltip.md#tooltip-显示位置)。⚠️ 主题差异点：Ainvest 折线 / 柱状族覆盖为 **位置 B**（上方跟随指针，见 [ainvest.md §1.6](ainvest.md#16-tooltip-形态端通用--仅折线柱状)）；THS（含 iFinD-Mobile）覆盖为 **位置 C**（带 X 轴坐标图；饼图等无坐标图回退 A，见 [ths.md](ths.md)） |
| 背景色 | **`#3B3B3B`** 深底 |
| 圆角 | 4px |
| 内边距 | 8px |
| 默认最大宽度 | 160px（移动端最大 1/2 图表宽度） |
| 触发方式 | `trigger:'axis'`（按坐标轴触发） |
| 过渡动画 | 关闭（`transitionDuration:0`） |
| 移出延迟隐藏 | **2000ms** |
| 标题字号 | 12px Regular，白色 |
| 数据行：系列名 | 12px `font-family-cn` Regular，`rgba(255,255,255,0.84)`，左对齐 |
| 数据行：数值 | 12px THS Money font Medium，同色，右对齐 |
| 数据行：数值单位 | THS Money font **Bold 600** |
| 数据行：图例标记 | **形状 + 尺寸 + 圆角必须与图表 legend 完全一致**（柱→6×6 方块、折线→8×2 1px 圆角胶囊、饼/雷达→6×6 圆点）；主题切换时（如 Ainvest 全部统一为 10×10 圆形）tooltip marker 同步切换 ⚠️ ECharts 默认 `params.marker` 是固定圆点，不会自动跟随 legend，需在 `formatter` 中手动拼接同款 SVG，见 [../echarts-implementation-hints.md 陷阱 15](../echarts-implementation-hints.md) |

## 光标线 / 高亮标签轴

| 项 | 值 |
| --- | --- |
| 默认光标 | 垂直细线 ，**solid 实线**（不得 dashed） |
| 光标颜色 | `color-visualization-highlight-line`（`rgba(0,0,0,0.6)`） |
| **折线图特例** | 光标线 z 必须低于折线本身（避免压住数据线） |
| 高亮标签胶囊 | 10px Bold 白字 / 蓝底 `#3366FF` / 圆角 2px |
| X 轴标签高亮 | 默认显示 |

## 数据标签（Data Label）

| 项 | 值 |
| --- | --- |
| 字号 | 10-12px |
| 字体 / 字重 | `font-family-number` Medium |
| 颜色（浅底） | `color-text-primary`（`rgba(0,0,0,0.84)`） |
| 颜色（深底/段内） | `color-text-inverse-primary`（`rgba(255,255,255,0.84)`） |
| 默认显示规则 | 柱状默认显示；多折线 / 归一化堆叠默认隐藏 |
| 隐藏阈值 | 移动端数据 > 5 隐藏；Web 端碰撞隐藏 |
| 0 值 | 显示「0」；无数据不显示标签 |

## 数据缩放滑块（DataZoom，仅看板=on 时显示）

| 项 | 值 |
| --- | --- |
| 高度 | 24px |
| 选中区域填充 | `color-visualization-datazoom-filler`（`#3366FF 8%`） |
| 把手 | 24×24px / 白底 / 圆角 6px / 三线 grip 图标 |
| 提示文本 | PC/Web 默认内侧；移动端默认不显示 |

## 图例交互

| 行为 | 触发 | 效果 |
| --- | --- | --- |
| Web 端：hover 图例 | 多系列图表 | 当前系列突出；**其他系列图表图形不透明度降至 20%**（图例本身不变） |
| 点击图例 | 通用 | 切换该系列显示/隐藏 |
| 移动端 | — | 通常无 hover 态 |

## 动画

| 场景 | 时长 / 缓动 |
| --- | --- |
| 柱状图入场（0-80px 高） | 320ms `cubicOut` |
| 柱状图入场（80-160px） | 480ms `cubicOut` |
| 折线主线选中放大 | 1.2× |
| 交互态（Tooltip/光标） | 无过渡动画，紧跟鼠标 |

## 水印

base 水印 SVG，**锚定 grid 绘图区右下角，距 grid 右侧 36px**，不遮挡数据。通用机制（加载方式 / 锚定 / 资源协议 / 反例）见 [components/watermark.md](../components/watermark.md)。

| 模式 | 资源 | 尺寸 | 透明度 |
| --- | --- | --- | --- |
| Light | [`assets/examples/_shared/base-watermark-light.svg`](../../assets/examples/_shared/base-watermark-light.svg) | 36 × 10 | 6% 黑（SVG 内 `fill="black" fill-opacity="0.06"`） |
| Dark  | [`assets/examples/_shared/base-watermark-dark.svg`](../../assets/examples/_shared/base-watermark-dark.svg)   | 36 × 10 | 6% 白（SVG 内 `fill="white" fill-opacity="0.06"`） |

**预览**：

| Light | Dark |
| --- | --- |
| ![base 水印 light](../../assets/examples/_shared/base-watermark-light.svg) | ![base 水印 dark](../../assets/examples/_shared/base-watermark-dark.svg) |

## 关键圆角 / 间距

| 项 | 值 |
| --- | --- |
| 柱顶圆角 | 0px（柱体无圆角） |
| Tooltip 圆角 | 4px |
| 高亮标签胶囊圆角 | 2px |
| 滑块把手圆角 | 6px |
| Tooltip 内行间距 | 4px |
| Tooltip 数据行：系列名↔数值间距 | 12px |

## 关键尺寸

| 项 | 值 |
| --- | --- |
| 柱体最大宽度 | 32px |
| 单柱容器最大宽度（含柱距） | 48px（柱距比 2:1） |
| 分组柱状图组合容器最大宽度 | 100px |
| 折线线宽 | 单线/多线主线 1.5px；多线其他 1px |
| 折线数据点（默认态） | 6px **实心圆**，**fill = 折线色**；含 1.5px 描边，描边色 = 折线色 |
| 折线数据点（hover 态） | **仅 hover 时** fill 切白；**描边色保持折线色不变**（默认态 fill 不是白色） |
| ⚠️ legend marker 视觉传播 | 折线 series 的 `itemStyle.borderWidth: 1.5` 会通过继承链让 legend 圆视觉直径变成 10+1.5×2≈12（非设计期望的 10×10）——需 `legend.itemStyle.borderWidth: 0` 显式 override，详见 [echarts-implementation-hints.md 陷阱 20](../echarts-implementation-hints.md) |
| 环图 | 外径 70 / 内径 42 / 环宽 28 / 容器 160×160 |

## Z-Index 层级

> 📌 **本节是 z 轴层级（绘制时的前后层叠顺序）的权威定义**——全项目其他文件引用此处，不再各自重列。数值越大越靠上层，用于解决元素互相遮挡（如数据点被网格线压住、轴标签被柱条覆盖）。

| Token | 典型值 | 用途 |
| --- | --- | --- |
| `z-index-axisLabel` | 57 | 轴标签（最上层，确保不被数据图形遮挡）；**水印同层置顶**（防裁剪，见下「覆盖规则」） |
| `z-index-marker` | 50 | 折线数据点 / 标记符号 |
| `z-index-axisPointer` | 6 | 选中光标线 / 十字光标 |
| `z-index-axis` | 5 | 坐标轴线 |
| `z-index-data-item` | 4 | 数据图形（柱条 / 标记线等） |
| `z-index-graphic` | 2 | 自定义图形元素 |
| `z-index-line` | 1 | 0 轴线 |
| `z-index-splitLine` | 0 | 网格分割线（最底层） |

**层级原则**：

- 网格线 / 0 轴线在最底（0-1），数据图形在中层（2-5），交互元素（光标、数据点）在上层（6 / 50），轴标签在最顶（57）
- 数据点（`z-index-marker` 50）必须高于数据图形和网格，避免被压住
- **折线 series 本体高于柱条**——折柱组合图中折线必须绘制在柱之上（ECharts 落地：折线 `z: 5`、柱保持默认，见 [echarts-implementation-hints.md 陷阱 6](../echarts-implementation-hints.md)）
- 轴标签（57）必须最高，任何场景下保持可读

> 典型值来自移动端主题实现（`Z_LIST`）。不同主题的具体数值可能微调，但**相对顺序**是规范的硬约束。业务线主题默认继承本表层级顺序（如 [ainvest.md](ainvest.md) 直接引用 `mobile/static/z`）。

### 覆盖规则

本表是**基线层级**，可被更高优先级的单独层级声明覆盖——遵循 [SKILL.md § 覆盖优先级](../../SKILL.md#覆盖优先级)（base → 主题级 → 图表级 → 实例级）：

- **主题级覆盖**：业务线主题可整体调整层级顺序 / 数值（默认继承本表）。
- **图表级覆盖**：单个图表可声明专属层级。例：**词云整体置顶 `z: 999`**（压过 57，确保任何场景不被遮挡），见 [word-cloud.md § 层级](../charts/word-cloud.md#层级z-index)。
- **水印置顶**：水印固定与轴标签同层（57）**置顶**——防止被裁剪 / 移除，**不是垫底背景**。主题只覆盖水印的资源 / 位置 / 透明度，不下调其层级。

### 折线图 / 折柱组合特例（重要）

**折线图中，光标线（axisPointer）的 z 必须低于折线本身和数据点**——光标只是辅助指示，不应覆盖核心数据。

**折柱组合图中，折线 series 本体必须高于柱条**——柱属数据图形层（`z-index-data-item` 4），折线整体抬高，确保折线不被柱条遮挡。

| 元素（折线图 / 折柱组合） | z 相对顺序 |
| --- | --- |
| 数据点（marker） | 最高（保持 ≥ `z-index-marker` 50） |
| 折线 series 本体 | 高 |
| **柱条（折柱组合中）** | **低于折线**（数据图形层 `z-index-data-item` 4） |
| **光标线（axisPointer）** | **低于折线** |
| 网格 / 0 轴 | 最底 |

> 其他图表（柱图 / 饼图 / 堆叠等）保持上方表格的默认顺序——axisPointer 在数据图形之上。**仅折线族特例**：光标线在折线下方。
> ECharts 落地映射（折线 `z: 5` / 光标 `z: 1` / 柱保持默认 `z: 2`）见 [echarts-implementation-hints.md 陷阱 6](../echarts-implementation-hints.md)。
>
> 反馈来源：测试者反映折线图光标线（默认 z 高）盖在折线上，破坏数据可读性。

---

## 被业务线主题覆盖项一览（Cross-Theme Diff Map）

> 📌 **本节是 base ↔ 业务线主题的差异速查表**——base 主题用户可跳过；切换业务线主题时按此表快速定位 delta，详细值与解释见对应 [themes/{ifind,ainvest,ths}.md](../themes/)。
>
> **维护规则**：本表是**导航索引**，权威 delta 值在业务线主题文件中；本表与业务线文件**值不符时以业务线文件为准**。新增 / 修改业务线 delta 时同步本表（不动 base 上方主表）。
>
> **图例**：
> - `—` = 该主题继承 base，无覆盖
> - 「⚠️ 整套替换」= 不走 delta 叠加，整套独立（见 [SKILL.md § 维度叠加规则](../../SKILL.md#维度叠加规则)）
>
> **列的含义**：
> - **iFinD-PC**：iFinD 业务线 PC 静态图。iFinD-Mobile 不单列，因为 iFinD-Mobile = 直接使用 THS 主题，值参见 "THS" 列。
> - **Ainvest**：以 **Ainvest-Mobile** 为主（业务线身份级项如色板 / 字体 / 涨跌色 mobile + PC 共享；其他端专属项当前 PC 端 TBD）。Ainvest-PC 完整 delta 待补，见 [ainvest.md § 三、Ainvest-PC 专有](ainvest.md#三ainvest-pc-专有-delta)。
> - **THS**：THS 主题本身（同时也是 iFinD-Mobile 的实现）。

### 整体布局

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 绘制区高度 | 160px | — | **200px**（端通用 · PC + Mobile） | — |
| grid 外圈空白 | — | **容器宽 5% 外圈空白**（钳 28~60，取 4 倍数）+ `containLabel:true`，top 32，**L/R 5**——**iFinD-PC 特有的双层边距，见 [ifind.md § 2.6](ifind.md#26-坐标轴--网格)** | — | — |

### 字体

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 字族（数字） | `font-family-number`（THS Money font） | **Microsoft YaHei**（含 Tooltip Arial） | **`font-family-en`** | — |
| 字族（中文） | `font-family-cn`（系统无衬线链） | Microsoft YaHei | `font-family-en` | — |
| 基础字号 | 10 / 12 分级 | **14**（统一） | — | — |
| 行高 | > 字号 | **= 字号** | — | — |
| font-weight-bold | 600 | **400** | — | — |

### 色板 ⚠️ 整套替换

| 项 | base | iFinD-PC | Ainvest | THS（含 iFinD-Mobile） |
| --- | --- | --- | --- | --- |
| 折线 / 顺序色板 | **6 色**（primary/02/08/04/05/09） | **24 色独立强对比** `#4D5999…` | **8 色硬编码** `#265FFC…` | **9 色** `#3366FF/#FF4019/#FF9500/…` |
| 单系列默认色 | `#3366FF` | `#4D5999`（PC 24 色第 1） | `#265FFC` | `#3366FF` |
| 浅色色盘 | 无 | `#FFBF80 #51E5E6 …`（柱 + 折线共存时折线改用） | 无 | 无 |

### 涨跌色（delta 覆盖）

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 涨 / 跌色 | `#FF2436` / `#07AB4B` | **`#eb5454` / `#47b262`**（红涨绿跌浅色版） | **`#00B53C` / `#FF381A`**（绿涨红跌，语义反转） | — |
| 桑基源/中间节点 | primary `#3366FF` | — | **`#265FFC`** | **`#FF2436`**（涨色！语义反转） |
| 桑基汇正 | `color-price-up` `#FF2436` | — | **`#00B53C`**（绿） | **`#FF9500`**（橙） |
| 桑基汇负 | `color-price-down` `#07AB4B` | — | **`#FF381A`**（红） | **`#3366FF`**（蓝） |

### 图例

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 标记本体 | 6×6 方 / 8×2 线 | **12×12 方 / 12×3 线 / r12 圆**（按 seriesType） | **统一 10×10 圆形** | — |
| 标记 ↔ 文字间距 | 2px | **6px** | **6px** | — |
| 项左右间距 | 12px | **20px** | **16px** | — |
| 溢出策略 | 移动端换行 / Web 分页 | **强制分页** | — | — |

### 坐标轴 / 网格

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 主 Y 轴位置 | **左侧** | — | **右侧** | — |
| Y 轴标签布局 | 形式 A（内部 + 避让） | **形式 B**（外部 + 居中） | **形式 B** | — |
| 网格线颜色 | `rgba(0,0,0,0.06)` | `#e9e9f2` | `rgba(0,0,0,0.10)` | — |
| X 轴轴线 | 默认不显示 | 显示 `#ADAEC5` | 显示 `#808080` / `#666666` | — |

### Tooltip

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 显示位置 | 位置 A（跟随右下，base 默认） | — | **位置 B**（折线 / 纵向柱状族）+ A（其余回退） | **位置 C**（带 X 轴坐标图）+ A（无坐标图回退） |
| 背景 | `#3B3B3B` 深底 | **`rgba(255,255,255,.95)` 白底** + 边框 + 阴影 | **`#383838`** + **下三角指示器** | — |
| 移出延迟 | 2000ms | — | **0ms** | — |
| 顶部分割线 | 无 | **有** | — | — |

### 光标线

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 样式 | **solid 实线** | **虚线 `[3,3]`** `#7D7D94` 宽 1 | — | — |

### DataZoom 滑块

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 轨道高 | 24px | **16px** | **4px 细轨** | — |
| 选中填充 | `#3366FF 8%` | `rgba(95,146,228,.2)` | **深灰** `rgba(0,0,0,.6)` | — |
| 把手 | 24×24px 圆角 6px | 圆角矩形 `#1b63d9` | **32×32px 全圆 50%**，边缘对齐 | — |

### 折线 / 数据点

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 线宽 | 1.5 / 1 | **2px 统一** | **2px 统一** | — |
| 数据点 | 圆形 6px 常显，描边折线色 | **菱形 10px 默认隐藏**，描边 `#fff` | 圆 6px，**描边 2px** | — |
| 平滑 | — | `smooth:false` | — | — |

### 柱状

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| 柱顶圆角 | 0px | — | 待确认 | **2px**（远 0 轴端 / 仅最顶段） |
| 柱体最大宽度 | 32px | — | — | **16px** |
| barGap | 2:1 | — | **5%**（柱挨近） | — |
| 多柱组内单柱宽 | 容器公式推 | — | **66 / 组内柱数** | — |
| 柱标签色 | 继承系列色 | — | **固定 `rgba(0,0,0,.6)` 灰** | — |

### 水印 ⚠️ 整套替换

| 项 | base | iFinD-PC | Ainvest | THS |
| --- | --- | --- | --- | --- |
| logo | base 水印 SVG（36×10） | — | **Ainvest logo 96×20** | — |
| 锚定 | grid 右下，距 grid 右 **36px** | — | **grid 左下** | — |
| 透明度（light） | 6% 黑 | — | **5% 黑** | — |
| 透明度（dark） | 6% 白 | — | **20% 白** | — |

---

> 📐 **查阅完整 delta 含背景与未列项**：
> - iFinD-PC → [ifind.md](ifind.md)
> - Ainvest → [ainvest.md](ainvest.md)
> - THS（也是 iFinD-Mobile 实现）→ [ths.md](ths.md)
