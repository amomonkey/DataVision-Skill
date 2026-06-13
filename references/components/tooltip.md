# Tooltip / 提示框

> 📌 **常用值已在 [themes/base.md（base 主题）](../themes/base.md) 内联**——画 base 主题图表时直接从那里取值。本文件是详细规范，仅在需要边界细节（动画分档、交互态、多端差异、配置选项等）时查阅。用了业务线主题（iFinD-PC / Ainvest / THS）请同时查 [themes/](../themes/) 下对应 delta 文件。

鼠标悬停或触摸时显示的数据气泡卡片、光标线、高亮标签轴规范。

---

## 气泡卡片

| 属性 | 值 | Token |
| --- | --- | --- |
| 背景色 | `#3B3B3B` | `color-visualization-tooltip` |
| 内边距 | 8px | `spacing-tooltip-padding` |
| 圆角 | 4px | `radius-tooltip` |
| 内部行间距 | 4px | `spacing-tooltip-row` |
| **默认最大宽度** | **160px**（根据业务可调整） | — |
| 移动端最大宽度 | **1/2 图表宽度** | — |
| 最小行间距 | 12px | — |
| 布局 | 固定双列结构（系列名 + 数值），宽度以最长一行为准 | — |
| 排列顺序 | 与图例顺序一致 | — |
| 图例字数溢出 | 自动换行显示 | — |

---

## Tooltip 显示位置

### PC 端（默认）

| 位置 | 触发 |
| --- | --- |
| **默认**：跟随鼠标，显示在右下方（根据容器余量自动变化） | 默认行为 |
| **固定位置**：图表上方左 / 右两侧 | 可配置 |
| **图表上方，跟随指针** | 可配置 |

### 移动端

| 位置 | 说明 |
| --- | --- |
| **默认**：固定在图表内侧（grid 区域）左 / 右两侧，顶对齐显示 | 触摸点在图表**左半区** → Tooltip 固定在 grid 右侧内边缘；触摸点在**右半区** → 固定在 grid 左侧内边缘。垂直方向与 grid 顶部对齐 |

---

## 日期 / 标题行

| 属性 | 值 | Token |
| --- | --- | --- |
| 字号 | 12px / 16px 行高 | `font-size-extra-small` / `line-height-small` |
| 字重 | Regular | `font-weight-regular` |
| 字体 | 跟随业务配置（中文 / 数字混排） | — |
| 颜色 | `color-text-inverse`（白） | — |

---

## 数据行（每条数据系列一行，flex justify-between，12px 间距）

| 元素 | 字号 | 字体 | 颜色 | Token |
| --- | --- | --- | --- | --- |
| 图例标记 | **形状 / 尺寸 / 圆角 / 颜色完全与 legend 保持一致**（详见 [legend.md § 图例标记形状](legend.md#图例标记形状按图表类型)）；主题切换时同步 | — | 跟随系列色 | — |
| 系列名称 | 12px | `font-family-cn` Regular | `rgba(255,255,255,0.84)` | `font-family-cn`, `color-text-inverse-primary` |
| 数值 | 12px | THS Money font Medium | `rgba(255,255,255,0.84)` | `font-family-number`, `color-text-inverse-primary` |
| 数值单位（万 / 亿 / %） | 12px | THS Money font **Bold** | 同上 | `font-family-number`, `font-weight-bold` |

数值右对齐，系列名称左对齐。

---

## 光标线 / 高亮标签轴

| 元素 | 默认 | Token |
| --- | --- | --- |
| **默认竖轴光标** | 仅显示垂直细线（**solid 实线**，贯穿图表全高） | `color-visualization-highlight-line` |
| 十字光标 | 可选（垂直线 + 水平线交叉，均 **solid 实线**） | 同上 |
| 块面选中光标 | 支持（块状选中区域） | `color-visualization-highlight-block` |
| 轴标签高亮 | **默认显示**（X 轴标签胶囊高亮） | — |
| 默认 X 轴标签高亮 | 是 | — |
| Y 轴光标 + Y 轴标签高亮 | 可配置（默认关闭） | — |

> ⚠️ **光标线必须 solid 实线**——不得使用 dashed / dotted 虚线。虚线视觉碎、与数据线易混淆；实线是规范要求。

**高亮标签轴胶囊**

| 属性 | 值 | Token |
| --- | --- | --- |
| 文字字号 | 10px | `font-size-xxs` |
| 字重 | Bold | `font-weight-bold` |
| 颜色 | 白 | `color-text-inverse` |
| 背景 | 蓝（K 线）或自定义 | `color-visualization-highlight-background-tick` |
| 圆角 | 2px | `radius-axis-label-tag` |

---

## 看板 vs 十字光标

| 概念 | 适用图表 | 行为 |
| --- | --- | --- |
| 看板（Tooltip） | 折线 / 柱状 / 折柱 / 红绿柱 | 弹出 Tooltip 气泡 + 十字准线 + 滑块 |
| 十字光标（Crosshair） | K 线图专用 | 绘制十字线 + 在轴上打标签（蓝底白字胶囊），**无气泡** |

K 线图的「十字光标」与其他图表的「看板」机制不同，不可混用。

---

## Tooltip 交互行为

| 属性 | 值 | 说明 |
| --- | --- | --- |
| 触发方式 | 按坐标轴触发 | 鼠标每移到一个 X 坐标即触发（而非停在数据项上才触发），保证光标紧跟 |
| 过渡动画 | 关闭 | Tooltip 气泡无位移过渡，紧跟鼠标，避免拖影 |
| 移出延迟隐藏 | 2000ms | 鼠标移出图表后，Tooltip 与光标延迟 2 秒消失 |
| 自动隐藏 | 默认开启 | 若配置「常驻显示」（always-show）则关闭自动隐藏 |
| 位置算法 | 跟随鼠标，靠左 / 靠右自适应 | 鼠标在图表**左半区** → Tooltip 靠右显示；在**右半区** → 靠左显示。垂直方向贴绘制区顶部 |

**光标（axisPointer）补充交互**：

| 配置 | 默认 | 说明 |
| --- | --- | --- |
| 仅渲染内容时显示 | 开 | 无内容时不显示空光标 |
| 允许超出绘图区 | 开 | 光标可延伸到绘制区外（如轴标签区） |
| 绘图区内点击隐藏 | 关 | 绘制区内点击不隐藏光标 |
