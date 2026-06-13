# Visualization Design Tokens

可视化图表专用的设计 tokens。**禁止硬编码原始值**——所有颜色、字号、字重、字体、间距、圆角必须通过 token 引用。

> 本文件是 **base 主题**（即「默认通用」，详见 [SKILL.md § 术语约定](../SKILL.md#术语约定glossary)）的 token 基准。业务线主题（iFinD-PC / Ainvest / THS）只在 [themes/](themes/) 记录与此处的差异（delta），未覆盖项一律继承本文件。
>
> 📐 **原子 token 与色板序列分层**：本文件下方"可视化色板"中的 `color-visualization-primary` / `-01..09` 是**原子色 token（所有主题共享同一池）**；而**色板序列**（哪些原子色按什么顺序组成"折线色板"/"柱图色板"）是**主题级配置**——base 在本文件给出默认序列，业务线主题（[themes/](themes/)）各自完整给出自己的序列，**整套替换不与 base 叠加**。详见 [SKILL.md § 维度叠加规则](../SKILL.md#维度叠加规则)。

---

## Color — 色板

### 品牌色

| Token                       | 浅色             | 深色             | 用途             | 可视化场景     |
| --------------------------- | -------------- | -------------- | -------------- | --------- |
| `color-brand-primary`       | `#FF2436` 100% | `#FF2436` 100% | 品牌主色常态         | 涉及（仅识别场景） |
| `color-brand-primary-hover` | —              | —              | 品牌主色悬停态（**新**） | 涉及        |

### 涨跌色（Price Up / Down）

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| `color-price-up` | `#FF2436` 100% | `#FF2436` 100% | A 股上涨 / 红绿柱正值 / K 线阳线 |
| `color-price-down` | `#07AB4B` 100% | `#07AB4B` 100% | A 股下跌 / 红绿柱负值 / K 线阴线 |
| `color-price-down-weakness` | `#0CA3B0` 100% | `#15A9B6` 100% | A 股下跌 - 色弱模式 |
| `color-deal-price-down` | `#4691EE` 100% | `#3D76B8` 100% | 交易业务 - 下跌（蓝跌） |
| `color-price-even` | `#000000` 84% | `#FFFFFF` 84% | 平 / 停牌 / 集合竞价确认下 |

> 涨跌色**不得复用** `color-status-error` / `color-status-success`——前者带色弱 / 国际版分支，后者是 UI 状态。

### 可视化色板（Sequential Palette）— 核心

| Token | 值 | 用途 |
| --- | --- | --- |
| **`color-visualization-primary`** | `#3366FF` 100% | **可视化色 - 折柱默认色**（单系列基础柱状图 / 折线图第 1 条 / 折柱图的柱） |
| `color-visualization-01` | `#FF4019` 100% | 可视化色 |
| `color-visualization-02` | `#FF9500` 100% | 可视化色 |
| `color-visualization-03` | `#62B312` 100% | 可视化色 |
| `color-visualization-04` | `#14CCBD` 100% | 可视化色 |
| `color-visualization-05` | `#199FFF` 100% | 可视化色 |
| `color-visualization-06` | `#4433FF` 100% | 可视化色 |
| `color-visualization-07` | `#FF33AA` 100% | 可视化色 |
| `color-visualization-08` | `#CC41D9` 100% | 可视化色 |
| `color-visualization-09` | `#858585` 100% | 平、中性色、光标等元素（中灰，常用作参考线 / 红绿柱叠加折线） |

**色板拆分（柱图 / 折线独立）**

柱图与折线图使用**两套独立的顺序色板**，均从上方 `color-visualization-*` 色板取色。

**折线色板**（多折线图 / 折柱图叠加折线 / 雷达图，按系列序号取色）：

| 序号  | Token                         | 值         |
| --- | ----------------------------- | --------- |
| 1   | `color-visualization-primary` | `#3366FF` |
| 2   | `color-visualization-02`      | `#FF9500` |
| 3   | `color-visualization-08`      | `#CC41D9` |
| 4   | `color-visualization-04`      | `#14CCBD` |
| 5   | `color-visualization-05`      | `#199FFF` |
| 6   | `color-visualization-09`      | `#858585` |

**柱图色板**（分组柱状图等多系列）：**直接使用上方折线色板**——同序列、同色值（`color-visualization-primary` → `-02` → `-08` → `-04` → `-05` → `-09`）。

**折柱组合图色板**（柱与折线分别独立取色，避免柱 / 线同色）：

折线（按系列序号取色）：

| 序号  | Token                    | 值         |
| --- | ------------------------ | --------- |
| 1   | `color-visualization-02` | `#FF9500` |
| 2   | `color-visualization-09` | `#858585` |

柱状（按系列序号取色）：

| 序号  | Token                         | 值         |
| --- | ----------------------------- | --------- |
| 1   | `color-visualization-primary` | `#3366FF` |
| 2   | `color-visualization-04`      | `#14CCBD` |
| 3   | `color-visualization-05`      | `#199FFF` |
| 4   | `color-visualization-06`      | `#4433FF` |
| 5   | `color-visualization-07`      | `#FF33AA` |
| 6   | `color-visualization-08`      | `#CC41D9` |

**通用规则**：

- 单系列柱图 / 折线图第 1 条 / 折柱图的柱：固定用 `color-visualization-primary`
- 折柱图的叠加折线：使用折柱组合图色板的折线序列（`color-visualization-02` → `color-visualization-09`）
- 多折线图 / 雷达图：使用上方折线色板顺序
- **禁止引入色板之外的颜色**作为系列色

### datazoom（数据缩放轴）色

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| `color-visualization-datazoom-data-bg` | `#000000` 8% | `#FFFFFF` 12% | datazoom 未选中数据（echarts 底色与数据色无法叠加，故显示为最终不透明度） |
| `color-visualization-datazoom-data-select` | `#3366FF` 16% | `#3366FF` 16% | datazoom 选中数据 |
| `color-visualization-datazoom-filler` | `#3366FF` 8% | `#3366FF` 8% | datazoom 选中区域色块 |

### 图表元素色

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| `color-visualization-divider` | `#000000` 6% | `#FFFFFF` 6% | 可视化网格线 |
| `color-visualization-divider-deep` | `#858585` 100% | `#858585` 100% | 可视化网格线 - 加深（0 轴线） |
| `color-visualization-tooltip` | `#3B3B3B` 100% | `#3B3B3B` 100% | 表内看板 - 背景 |
| `color-visualization-highlight-background-tick` | `#3366FF` 100% | `#3366FF` 100% | 选中标签背景（高亮标签轴胶囊） |
| `color-visualization-highlight-line` | `#000000` 60% | `#FFFFFF` 60% | 选中光标线 |
| `color-visualization-highlight-block` | `#000000` 10% | `#FFFFFF` 10% | 选中光标色块（块面选中光标） |

### 文字 / 图标颜色

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| `color-text-primary` | `#000000` 84% | `#FFFFFF` 84% | 一级文本色 |
| `color-text-secondary` | `#000000` 60% | `#FFFFFF` 60% | 二级文本色（轴标签 / 图例标签默认色） |
| `color-text-tertiary` | `#000000` 40% | `#FFFFFF` 40% | 三级文本色 |
| `color-text-quaternary` | `#000000` 24% | `#FFFFFF` 24% | 四级文本色（滑块把手边框） |
| `color-text-inverse` | `#FFFFFF` 100% | `#FFFFFF` 100% | 彩底白字 / 反色（高亮标签轴胶囊白字） |
| `color-text-link` | `#4167D9` 100% | `#4167D9` 100% | 文字链 |
| `color-text-inverse-primary` | `#FFFFFF` 84% | `#FFFFFF` 84% | 黑底一级文本（Tooltip 系列名 / 数值） |
| `color-text-inverse-secondary` | `#FFFFFF` 60% | `#FFFFFF` 60% | 黑底二级文本（Tooltip 日期行） |
| `color-text-inverse-tertiary` | `#FFFFFF` 40% | `#FFFFFF` 40% | 黑底三级文本 |
| `color-text-inverse-quaternary` | `#FFFFFF` 24% | `#FFFFFF` 24% | 黑底四级文本 |

### 背景色 / 前景层

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| `color-background-layer1` | `#F5F5F5` 100% | `#0F0F0F` 100% | 页面一级背景（最底层） |
| `color-foreground-layer1` | `#FFFFFF` 100% | `#1C1C1C` 100% | 页面一级前景（大卡 / 模块背景） |
| `color-foreground-layer1-hover` | — | — | 一级前景悬停色（**新**） |
| `color-foreground-layer1-selected` | — | — | 一级前景选中色（**新**） |
| `color-foreground-layer2` | `#FFFFFF` 100% | `#2B2B2B` 100% | 二级前景（滑块把手白底等） |
| `color-background-weak` | `#000000` 4% | `#FFFFFF` 6% | 输入框 / 浅底灰色 / 柱状选中态底色 / 滑块未选中蒙层 / 播放轴底色 |

### 基础灰阶

| Token | 浅色 | 深色 | 用途 |
| --- | --- | --- | --- |
| `color-grey-01` | `#E6E6E6` 100% | `#292929` 100% | 基础灰阶（播放轴次刻度 / 短刻度） |
| `color-grey-02` | `#D1D1D1` 100% | `#3B3B3B` 100% | 基础灰阶 |
| `color-grey-03` | `#B3B3B3` 100% | `#575757` 100% | 基础灰阶 |
| `color-grey-04` | `#9C9C9C` 100% | `#6E6E6E` 100% | 基础灰阶（播放轴主刻度 / 长刻度） |
| `color-grey-05` | `#858585` 100% | `#858585` 100% | 基础灰阶（同 `color-visualization-09`） |


---

## Typography — 字号 & 行高

### font-size（10 级）

| Token | 值 | 用途 |
| --- | --- | --- |
| `font-size-super-large` | 24px | 重要信息、导航大标题、资讯标题 |
| `font-size-extra-large` | 20px | 栏目大标题 |
| `font-size-large` | 18px | 模块一级标题 |
| `font-size-base` | 16px | 模块二级标题 |
| `font-size-medium` | 14px | 模块三级内容 |
| `font-size-small` | 13px | 数据信息 |
| `font-size-extra-small` | 12px | 辅助信息、描述等（图例 / Tooltip 系列名+数值） |
| `font-size-super-small` | 11px | 中标签 |
| `font-size-xxs` | 10px | 小标签 / 坐标（**轴标签 / 轴标题 / 数据标签 / 高亮标签轴 / 播放轴**） |
| `font-size-xxxs` | 9px | 极小标签 |

### line-height（9 级）

| Token | 值 | 用途 |
| --- | --- | --- |
| `line-height-super-large` | 30px | 重要信息、导航大标题、资讯标题 |
| `line-height-extra-large` | 24px | 栏目大标题、首页二分之一卡标题 |
| `line-height-large` | 22px | 模块一级标题 |
| `line-height-base` | 20px | 模块二级标题 |
| `line-height-medium` | 18px | 模块三级内容、二分之一卡片模块内容 |
| `line-height-small` | 16px | 数据信息（与 `font-size-extra-small` 配对：图例 / Tooltip） |
| `line-height-extra-small` | 14px | 中标签 |
| `line-height-super-small` | 12px | 极小标签（与 `font-size-xxs` 配对：轴 / 数据标签 / 播放轴） |
| `line-height-xxs` | 10px | 极小标签 |

### font-weight（3 级）

| Token | 数值 | 用途 |
| --- | --- | --- |
| `font-weight-regular` | **400** | 常规字重 / 内容文本（图例标签 / Tooltip 标题 / 中文系列名等） |
| `font-weight-medium` | **500** | 中黑 - 标题等强调场景（轴标签 / 轴标题 / 数据标签 / Tooltip 数值） |
| `font-weight-bold` | **600** | 中粗 - 重点强化、行情指数等场景（**高亮标签轴胶囊 / Tooltip 数值单位「万 / 亿 / %」**） |

> ⚠️ 重要：`font-weight-bold` 是 **600**，不是 700。Tooltip 单位字重以 PDF 权威值 600 为准。

### font-family

| Token                | 含义             | 适用                                      |
| -------------------- | -------------- | --------------------------------------- |
| `font-family-number` | THS Money font | 所有数值（轴刻度、数据标签、Tooltip 数值、价格、涨跌幅、播放轴时间等） |
| `font-family-cn`     | 系统无衬线  | 中文（系列名、轴标题、图例标签、单位说明等非数字部分）             |
| `font-family-en`     | Plus Jakarta Sans | 西文 / 英文（非数字、非中文的文本部分）                    |

**实际字体名**（token 的底层取值，由 token 层统一管理，代码中禁止硬编码）：

| Token | 实际字体名 |
| --- | --- |
| `font-family-number` | `THSJinRongTi` |
| `font-family-cn` | `system-ui,-apple-system,Noto Sans,San Francisco,BlinkMacSystemFont,Helvetica Neue,Helvetica,sans-serif` |
| `font-family-en` | `Plus Jakarta Sans,-apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,"Helvetica Neue",Arial,sans-serif,"Apple Color Emoji","Segoe UI Emoji","Segoe UI Symbol"` |

> 📐 `font-family-cn` 是 **system-ui 链**——在 Apple 端解析为 **PingFang SC**、其他端为 Noto Sans / SF 等。文档其他位置出现「PingFang SC」时，指的是它在 Apple 端的呈现，**不是字面字体名**；代码一律引用 `font-family-cn` token，不要写死 PingFang。

**Fallback 链**在 token 层统一管理，代码中禁止硬编码字体名（如 `'DIN Alternate'`、`'Helvetica Neue'`）。

---

## 共享元素 Token 映射汇总

跨图表共享元素的统一字号 / 字重 / 颜色 token，便于设计 / 开发对照。详细规范见 [components/](components/).

| 元素 | font-size | font-weight | line-height | color |
| --- | --- | --- | --- | --- |
| A：图例 / 分页 | `font-size-extra-small`（12） | 配置 | `line-height-small`（16） | `color-text-secondary` |
| B：轴标题 / 数据标签 | `font-size-xxs`（10） | `font-weight-medium`（500） | `line-height-super-small`（12） | `color-text-secondary` |
| C：轴标签 | `font-size-xxs`（10） | 配置 | `line-height-super-small`（12） | `color-text-secondary` |
| D：高亮标签轴文字 | `font-size-xxs`（10） | `font-weight-bold`（600） | `line-height-super-small`（12） | `color-text-inverse` |
| D：高亮标签轴背景 | — | — | — | `color-visualization-highlight-background-tick` |

> 若 A / B / C 元素使用同一个 token 色值（如同为 `color-text-secondary`），修改此 token 时会同时影响三者；如需独立调整，需单独覆盖配置。

---

## Spacing — 间距

| Token                         | 值    | 用途                       |
| ----------------------------- | ---- | ------------------------ |
| `spacing-chart-region-gap`    | 8px  | 图例 / 图表区 / 滑块三个垂直区域之间的间距 |
| `spacing-legend-item-h`       | 12px | 图例项之间水平间距                |
| `spacing-legend-row-v`        | 4px  | 图例换行时的行间距                |
| `spacing-legend-marker-label` | 2px  | 图例标记与标签之间间距              |
| `spacing-tooltip-padding`     | 8px  | Tooltip 气泡内边距            |
| `spacing-tooltip-row`         | 4px  | Tooltip 内行间距             |
| `spacing-tooltip-row-gap`     | 12px | Tooltip 数据行内系列名与数值之间间距   |

---

## Sizing — 尺寸

| Token                          | 值         | 用途                                             |
| ------------------------------ | --------- | ---------------------------------------------- |
| `size-chart-width`             | 343px     | 标准移动端图表整体宽度                                    |
| `size-chart-region-height`     | 160px     | 图表绘制区高度（移动端实证值；设计 PDF 用占比表达高度，此为代码侧 grid 实际高度） |
| `size-slider-height`           | 24px      | 范围滑块行高                                         |
| `size-slider-handle`           | 24×24px   | 滑块把手尺寸                                         |
| `size-legend-marker`           | 12×12px   | 图例标记**容器**尺寸（点击热区）                             |
| `size-legend-mark-body`        | 6×6px     | 图例标记**本体**尺寸（容器内可见图形）                          |
| `size-bar-max`                 | 32px      | 柱状图柱体最大宽度                                      |
| `size-bar-container-max`       | 48px      | 基础 / 堆叠 / 归一化堆叠柱状图单柱容器最大宽度（含柱距）                |
| `size-bar-bar-gap-ratio`       | 2:1       | 柱体宽与柱间距最小比例（高密度时等比缩放保持）                        |
| `size-bar-group-container-max` | 100px     | 分组柱状图组合容器最大宽度（含 N 个柱 + N-1 内间距）                |
| `size-bar-group-inner-gap-max` | 2px       | 分组柱状图组内相邻双柱之间最大间距                              |
| `size-hbar-row-max`            | 24px      | 横向条形图单条容器最大高度                                  |
| `size-hbar-y-label-max`        | 80px      | 横向条形图 Y 轴分类标签最大宽度（超出 `…` 截断）                   |
| `size-hbar-data-label-max`     | 40px      | 横向条形图数据标签最大宽度（超出 `…` 截断）                       |
| `size-line-stroke`             | 1.5px     | 单折线 / 多折线图的主线（mainLine）线段描边粗细                  |
| `size-line-stroke-multi`       | 1px       | 多折线图中非主线的其他折线描边粗细                              |
| `size-line-point`              | 6px       | 折线图数据点直径（含 1.5px 描边）                           |
| `size-donut-container`         | 160×160px | 环图 / 半环图移动端容器固定尺寸（1:1）                         |
| `size-donut-radius`            | 70px      | 环图外半径                                          |
| `size-donut-ring-width`        | 28px      | 环图环宽（外径 - 内径）                                  |
| `size-tooltip-max-width`       | 160px     | Tooltip 默认最大宽度（移动端最大 1/2 图表宽度）                 |
| `size-tooltip-min-gap`         | 8px       | datazoom 滑块提示文本最小间距（< 8 切换到外侧）                 |

---

## Radius — 圆角

| Token | 值 | 用途 |
| --- | --- | --- |
| `radius-bar-top` | 0px | 柱体无圆角（四角直角） |
| `radius-tooltip` | 4px | Tooltip 气泡圆角 |
| `radius-axis-label-tag` | 2px | 高亮标签轴胶囊圆角 |
| `radius-slider-handle` | 6px | 滑块把手圆角 |
| `radius-slider-mask` | 4px | 滑块选中范围外蒙层左 / 右端圆角 |
| `radius-legend-mark` | 1px | 图例标记内部小元素圆角（柱形、虚线段、红绿柱小块等） |

---

## Token 命名约定

| 前缀 | 含义 | 可视化场景 |
| --- | --- | --- |
| `color-visualization-*` | 可视化专属颜色 | ✅ 首选 |
| `color-price-*` | 涨跌色 | ✅ 涨跌相关场景 |
| `color-text-*` | 文字色（含 `inverse` 黑底变体） | ✅ |
| `color-background-*` / `color-foreground-*` | 背景 / 前景层 | ✅ 部分 |
| `color-grey-*` | 基础灰阶 | ✅ 播放轴刻度等 |
| `font-family-*` / `font-size-*` / `font-weight-*` / `line-height-*` | 字体 | ✅ |
| `spacing-*` | 间距 | ✅ |
| `size-*` | 固定尺寸 | ✅ |
| `radius-*` | 圆角 | ✅ |
| `z-index-*` | 层级（绘制层叠顺序，权威表见 [base.md § Z-Index 层级](themes/base.md#z-index-层级)） | ✅ |

---

## 配置覆盖优先级

> 📌 「优先级档位」指**配置取值的覆盖顺序**（后者覆盖前者），与 [base.md § Z-Index 层级](themes/base.md#z-index-层级)（元素的**绘制层叠顺序**）是两个独立概念——「层级」一词本规范只保留给 z-index 叠放，配置覆盖一律称「优先级档位」，勿混。

权威定义（全局 token / base → 主题级 → 图表级 → 实例级）见 [SKILL.md § 覆盖优先级](../SKILL.md#覆盖优先级)——本文不再重列以免漂移。

