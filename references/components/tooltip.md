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
| 系列名过长 | 自动换行；换行后数值 / marker 顶对齐系列名**第一行**（见 [§ 系列名过长换行对齐](#系列名过长换行对齐)） | — |

---

## Tooltip 显示位置

> 📌 三个**形态档位 A / B / C**——本节只定义每档**长什么样、怎么定位**，**不区分端（PC / 移动端）、不绑定主题**。**哪个主题（含端差异）用哪档** = 主题级 delta，只在各 [themes/](../themes/) 文件维护；横向对照见 [base.md § 被业务线主题覆盖项一览](../themes/base.md#被业务线主题覆盖项一览cross-theme-diff-map)。命名习惯同 [Y 轴标签布局 形式 A / B](layout.md#y-轴标签布局)。

| 档位 | 形态定位 | 跟随行为 |
| --- | --- | --- |
| **位置 A** | 显示在触发点右下方，按容器余量左右自适应翻转（触发点在左半区 → 靠右、右半区 → 靠左），垂直贴绘制区顶部 | 连续跟随触发点 |
| **位置 B** | 下三角 + 水平跟随触发点居中 + 垂直锚 grid 上沿外侧 + 边缘 clamp + 三角反向偏移。完整算法见 [§ 位置 B 定位算法](#位置-b-定位算法库无关) | 连续跟随触发点 |
| **位置 C** | 固定上方左 / 右两侧：以图表中点为基准镜像避让（触发左半区 → 显示右、右半区 → 显示左），垂直固定贴上方。完整规则见 [§ 位置 C 定位规则](#位置-c-定位规则) | 不跟随，左 / 右离散两态 |

> 📐 **端无关说明**：A / B / C 是纯形态定义。"触发点"在 PC = 鼠标 hover 位置、移动端 = 触摸点；同一档两端形态一致。端差异（如某主题 PC 用 A、移动端用 C）一律由主题文件声明，不在本节体现。

---

## 位置 B 定位算法（库无关）

> 📌 本节是 **位置 B**（上方跟随指针）的权威算法定义，**库无关**——ECharts / D3 / Recharts / 原生 div 都按这 4 步落地。具体取值（背景色 / 三角高 / hideDelay 等）见 [ainvest.md §1.6](../themes/ainvest.md)；ECharts 的 `tooltip.position` 回调完整代码见 [echarts-implementation-hints.md § 陷阱 18](../echarts-implementation-hints.md)。
>
> ⚠️ 沿用通用网页 tooltip 的"光标位置 + `transform` 自偏移 + 居中三角"惯性写法 = **错**。根因：那是"自定位"，位置 B 要求"**锚定 + 边缘 clamp + 三角反向偏移**"。只改 `backgroundColor` + `hideDelay` 不算实现。
>
> 📐 **端无关**：下文 `cursorX` 在 PC = 鼠标 x、移动端 = 触摸点 x——Ainvest 此 tooltip 端通用，两端同走本算法。

**两条不变量**（每次算完位置回头核对，破一条即实现错）：

1. tooltip **三角尖端的 x** = **光标 x**。光标在哪三角指向哪，无论气泡贴不贴边。
2. tooltip **气泡底边的 y** = **grid 上沿 y − 三角高度**。**与光标 y 无关**——光标在 grid 内任意垂直位置，气泡都贴 grid 上沿外侧不动。

**每次 mousemove / showTip 取 4 个输入量**：

| 量 | 含义 | 来源 |
| --- | --- | --- |
| `cursorX` | 光标 x，**相对图表外层容器**（非 viewport / 非 page） | 各库的 pointer 工具，参考系传容器节点 |
| `gridTopY` | grid 区上沿 y，**相对图表外层容器** | = 容器内边距 + 绘图区 margin.top（ECharts 用 `chart.convertToPixel({gridIndex:0},[0,yMax])[1]`） |
| `hostW` | 图表外层容器宽度 | 容器节点 clientWidth |
| `tw` / `th` | tooltip 当前盒实际宽 / 高 | **必须先写 innerHTML 再量**——内容长度变 → 宽度变；先量得旧值 |

**3 个输出量（公式即代码）**：

```
tooltipLeft  = clamp(cursorX − tw / 2,        0,   hostW − tw)
tooltipTop   = gridTopY − th − 6                              // 6 = 下三角高
triangleLeft = clamp(cursorX − tooltipLeft,  10,   tw − 10)
```

**6 条禁忌**（出现任意一条 = 实现错）：

1. **禁止** `transform: translate(...)` 靠自身坐标位移定位 —— 与边缘 clamp 互斥，clamp 算了也没用
2. **禁止**三角用 CSS 伪元素 `::after { left: 50% }` 写死 —— JS 改不了 → 贴边时三角跟着气泡跑、不再指向光标。改用真元素 / CSS 变量（如 `--arrow-left`）
3. **禁止** `transition` 任何过渡（opacity / left / top）—— `transitionDuration` 必须为 0
4. **禁止** Tippy / Popper / Floating-UI 等通用 tooltip 库的 `placement` 自动定位 —— 会覆盖以上 4 步
5. **禁止** ECharts `confine: true` —— 与自定义 `position` 回调互斥
6. **禁止**为 tooltip 在 `grid.top` 预留间距 —— tooltip 是临时遮挡物，显示时遮 legend / 标题是**预期行为**

**正确性自检**（实现后 6 条全过才算落地）：

- 光标停图正中：三角在气泡正中
- 光标贴右边缘：气泡贴右、三角偏右指着光标
- 光标贴左边缘：气泡贴左、三角偏左指着光标
- 光标在 grid 内垂直移动：tooltip top 不变（一直锚 grid 上沿）
- 鼠标快速移动：tooltip 瞬移、无淡入淡出
- 切换数据点致内容长度变化：tooltip 宽度立即重算并重新 clamp（证明 `tw` / `th` 是 show 时现量、非缓存）

---

## 位置 C 定位规则

> 📌 **位置 C**（固定上方左 / 右两侧）的规则。当前 [THS 主题（含 iFinD-Mobile）](../themes/ths.md) 采用。比位置 B 简单——**无三角、不跟随指针**，水平只有"左 / 右"两个离散态。

**水平 · 中点镜像避让**：以**图表中点**为基准——

| 触发区域 | Tooltip 显示侧 |
| --- | --- |
| 落在图表**左半区** | **右侧**（贴绘制区右上角） |
| 落在图表**右半区** | **左侧**（贴绘制区左上角） |

> 永远显示在触发点的**对侧**（触发点同侧相斥），避免气泡遮住正在查看的数据。

**垂直 · 固定贴上方**：顶对齐绘制区上沿，不随触发点纵向移动、不跟随指针。

**与位置 A / B 的区别**：

- 位置 A 跟随光标**连续**移动；位置 C 只在**左 / 右两态**间切换，气泡位置离散、稳定
- 位置 B 锚 grid 上沿 + 三角指向光标 x；位置 C **无三角**，不指向具体光标 x

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

### 系列名过长换行对齐

> 📌 系列名（图例文本）超过气泡最大宽度（默认 160px）时换行成多行；本节只定义换行后**数值 / 图例标记与系列名第一行的对齐关系**——描述视觉结果与对齐意图，**不绑定实现机制**。各实现层（ECharts formatter HTML / D3 / Recharts / 原生 div / Canvas 绘制…）按自身布局模型落地；ECharts 具体写法见 [echarts-implementation-hints.md § 陷阱 15](../echarts-implementation-hints.md#陷阱-15tooltip-内的图例标记与图表-legend-不一致--主题切换不跟随)。

**3 条不变量**（每行渲染后回头核对，破一条即实现错）：

1. **数值锚系列名第一行**：系列名换行成多行时，数值与图例标记对齐到**第一行**（顶部齐平），不随系列名多行高度一起垂直居中下沉。
2. **只换系列名、数值不折行**：换行只发生在系列名列；数值（「数字 + 万 / 亿 / %」短串）始终单行、贴右，不因系列名变高而折行或被挤压。
3. **图例标记跟第一行**：marker 与系列名**第一行**文字垂直居中，不下移到多行块中部。

**自检**（构造系列名占 2 行、数值占 1 行的数据点，逐条核对）：

- 数值上边缘 = 系列名第一行上边缘（顶对齐——非居中、非底对齐）
- 数值仍贴右、单行不折
- 图例标记与第一行文字中线齐平

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
| 位置算法 | 见 [§ Tooltip 显示位置](#tooltip-显示位置) | 默认 **位置 A**（base）；改用 B / C 由主题声明，本表不重复定义 |

**光标（axisPointer）补充交互**：

| 配置 | 默认 | 说明 |
| --- | --- | --- |
| 仅渲染内容时显示 | 开 | 无内容时不显示空光标 |
| 允许超出绘图区 | 开 | 光标可延伸到绘制区外（如轴标签区） |
| 绘图区内点击隐藏 | 关 | 绘制区内点击不隐藏光标 |
