# iFinD 主题（Theme: iFinD）

> ✅ **本主题有权威设计稿**：`resources/ifind静态图最新设计规范.pdf`（设计权威）+ `resources/thsc-ifind-aigc-token.7z`（token 代码，精确值）。值已交叉验证。
>
> 📐 **本文件只描述 iFinD-PC 静态图主题**。iFinD 移动端**直接使用 [THS 主题](ths.md)**，不维护独立规范。

---

## 关键结论（先看这条）

iFinD 业务线下分两个端，**用法完全分离**：

- **iFinD 移动端 = 直接使用 [THS 主题](ths.md)** —— iFinD-mobile 不维护独立规范，整体（色板 / 字体 / Tooltip / 滑块 / 折线 / 图例 / 涨跌色等）与 THS 主题一致。**打开 [ths.md](ths.md) 取值即可，不要查本文件**。
- **iFinD PC / 静态图 = 本文件的全部内容** —— Microsoft YaHei、字号 14、独立 24 色板 `#4D5999…`、白色 Tooltip、Y 轴标签外部、bold=400、行高=字号、两侧 5% 空白等。`ifind静态图最新设计规范.pdf` 描述的就是这个 PC 静态 profile。

下面所有 token 覆盖、形态差异均针对 **iFinD-PC**；移动端请改用 [ths.md](ths.md)。

---

## 主题元信息

| 项 | 值 |
| --- | --- |
| 主题名 | iFinD-PC（同花顺旗舰业务线 / PC 静态图） |
| 代码对应 | `token.light` / `token.dark` |
| 继承基准 | base 主题（[../tokens.md](../tokens.md) / [../components/](../components/) / [../charts/](../charts/)） |
| 端 | **仅 PC 静态图**（移动端使用 [THS 主题](ths.md)） |
| 色彩模式 | light / dark 均支持（dark 为各色值的深色镜像） |
| 覆盖优先级 | base → 本文件 delta → 图表级 → 实例级（最高）（详见 [SKILL § 覆盖优先级](../../SKILL.md#覆盖优先级)） |

---

## 一、设计通则差异（来自权威 PDF）

PDF「通用问题」级规则（描述 PC / 静态图）：

1. **行高 = 字号**（如 14px 字 → 14px 行高）。base 是「行高 > 字号」。
2. **图表两侧空白 = 容器宽度的 5%**，钳制 **28~60px**，且取 **4 的倍数**（例：836×0.05=41.8→40）——iFinD-PC **独有**的外圈空白机制（在 ECharts grid 之外的容器层，CSS / 父容器 padding）。详见 [§ 2.6 坐标轴 / 网格](#26-坐标轴--网格)。base / Ainvest / THS 等其他主题**没有此机制**，外圈空白由具体实例自行配置。
3. 单位名称 / 图例 / 轴数字 **左 / 右对齐**。
4. **0 轴线 1px + 颜色淡化 + 置于图形下方**（与折线重叠时层级降到最低，不压数据）。
5. **定位线（光标线）= 虚线**——`type:[3,3]`、色 `#7D7D94`、宽 1。⚠️ base 规定光标线 solid 实线（业务线差异，非 bug）。
6. **标记形状按图表类型**：折线 / 面积 → 圆形（Tooltip 内为 8×2 线段标记）；柱 / 矩形树 → 方形（10×10）；气泡 / 散点 / 饼 / 环 → 圆形（10×10）。
7. **叠加柱状图**：去掉段间白色分割线；图例 hover 时高亮该系列、**其余系列 + 图例透明度降到 10%**；图形上交互选中整组。
8. **饼图**：去分割线（扇区无白边）+ 扇区**从大到小排序**；图例置右、与饼图**垂直居中**、距饼图 **16~100px**。
9. **面积图**：图例标记改用**柱形（rect）**而非线形；去分割线。
10. **Tooltip**：顶部文案区加一条分割线；移入的标记与浮层内标记保持一致。

---

## 二、Token 覆盖（iFinD-PC vs base）

### 2.1 字体

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 字族 | THS 数字 + 系统无衬线链 | **Microsoft YaHei**（数字也用，含 Tooltip 用 Arial） |
| 基础字号 | 10 / 12 分级 | **14**（轴 / 图例 / 标签统一 14，轴标签 14） |
| 行高 | > 字号 | **= 字号（14）** |
| font-weight-bold | 600 | **400** |
| 图例字色 | `color-text-secondary` | **`#474762`** |
| 图中字色（轴/标签） | `color-text-secondary` | **`#7D7D94`** |

### 2.2 序列色板 — ⚠️ iFinD-PC 主题色板（整套独立，不与 base 叠加）

> 色板维度不走 delta 继承——本节是 **iFinD-PC 主题**的完整色板，使用时整套替换 base 的色板序列。详见 [SKILL.md § 主题维度叠加规则](../../SKILL.md#维度叠加规则)。
>
> 📐 iFinD 移动端**不使用本色板**，而是使用 [THS 主题色板](ths.md#可视化色板--ths-主题色板整套独立不与-base-叠加)。

**PC 默认色板（柱状图、饼图、矩形树图、旭日图）— 独立 24 色强对比**，循序由左到右：

```
#4D5999 #3DB4CC #F2A355 #458BD1 #F261AA #F2D755 #705AE0 #A6CA46 #F26868 #449CC2 #AF4CE0 #C5D962
#363E6B #2A7E8F #A9723B #306191 #A84376 #A9963B #4E3F9D #738D30 #A84848 #2F6C87 #7A359C #8A9844
```

**浅色色盘**（柱 + 折线 + 基准线**同时存在**时，折线/基准线改用此盘避免与柱色相撞；柱图规则不变）：

```
#FFBF80 #51E5E6 #B3BFFF #AAF065 #FF80BF #FF8080 #75E9FF
```

### 2.3 Tooltip（浮层）

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 显示位置 | 位置 A | **位置 A**（PC 不覆盖，继承 base）；⚠️ iFinD-**移动端** = [THS](ths.md) = **位置 C**。档位定义见 [tooltip.md § Tooltip 显示位置](../components/tooltip.md#tooltip-显示位置) |
| 背景 | `#3B3B3B`（深） | **`rgba(255,255,255,.95)`（白）** |
| 边框 | 无 | **`1px solid #ECECF7`** |
| 阴影 | 无 | **`0 2px 8px rgba(0,0,0,.12)`** |
| 文字 | 反白 | `#7D7D94` / Arial |
| 圆角 | 4px | 4px |
| 内边距 | 8px | `0 8px 8px 8px` |
| 顶部分割线 | — | 有 |

### 2.4 范围滑块（datazoom）

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 选中填充 | `#3366FF` 8% | **`rgba(95,146,228,.2)`** |
| 背景 | `color-background-weak` | `#fff` + 边框 `#ececf7` |
| 轨道高 | 24px | **16px** |
| 把手 | — | 圆角矩形，色 `#1b63d9` |
| 数据底纹 | — | `rgba(77,89,153,.1)` |

### 2.5 折线 / 数据点

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 线宽 | 单线 1.5 / 多线主 1.5 其他 1 | **2px（统一）** |
| symbol | 圆形，常显 | **diamond（菱形）**，`showSymbol:false`（默认不显点），size 10 |
| 点描边 | 1.5px 折线色 | 1px `#fff` |
| 平滑 | — | `smooth:false`（折线不平滑） |

### 2.6 坐标轴 / 网格

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 主 Y 轴标签位置 | 内部 | **外部（`inside:false`）** |
| 轴线颜色 | — | `#ADAEC5`（X 轴） |
| 网格线颜色 | `#000` 6% | `#e9e9f2` |
| grid 内边距 | — | `containLabel:true`，top 32，**L/R 5** |
| 外圈空白（iFinD-PC 独有机制） | — | **容器宽 5%，钳制 28~60px，取 4 倍数**（band-level 通则，见 [§ 一、2](#一设计通则差异来自权威-pdf)） |

> 📐 **iFinD-PC 独有的双层边距机制**：iFinD-PC 在 ECharts grid 之外还有一层**容器层外圈空白**（CSS / 父容器 padding，非 ECharts option），值为容器宽 5%（钳 28~60px，取 4 倍数）。这层空白与 `grid.left:5` 内边距**叠加**——`containLabel:true` 让坐标轴标签的额外空间由 ECharts 自动撑出，主要外圈空白由容器层完成。
>
> ⚠️ **此机制为 iFinD-PC 专属**——base / Ainvest / THS 等其他主题**没有容器层外圈空白概念**，外圈空白由具体实例自行配置。

### 2.7 图例

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 溢出布局 | 换行 / 分页 | **分页（pagination）** |
| 标记尺寸 | 容器 12 / 本体 6 | `dvIcon`：线 12×3、方 宽12、圆 r12；标记-文字间距 gap 6 |
| 图例项间距 | `spacing-legend-item-h` 12 | `dvLegendGap` 20 |

### 2.8 K 线 / 涨跌

| 项 | base | iFinD-PC |
| --- | --- | --- |
| 涨 / 跌色 | `color-price-up` `#FF2436` / `color-price-down` `#07AB4B` | 涨 **`#eb5454`** / 跌 **`#47b262`**（红涨绿跌；色相同义、色值不同，待确认） |

### 2.9 水印

沿用 base（base 水印 SVG 36×10，grid 右下角，距 grid 右 36px，6% 黑 light / 6% 白 dark）。通用机制（加载方式 / 锚定 / 资源协议 / 反例）见 [components/watermark.md](../components/watermark.md)。

---

## 三、与 base 的形态级冲突（业务线差异，iFinD-PC 内以 iFinD-PC 为准；base 不动）

| # | 项 | base | iFinD-PC |
| --- | --- | --- | --- |
| 1 | 光标线样式 | solid 实线 | **虚线 `[3,3]`** `#7D7D94` 宽1 |
| 2 | 折线线宽 | 1.5 / 1 | **2px** |
| 3 | 折线数据点 | 圆形常显、6px、描边折线色 | **菱形、默认隐藏**、描边 `#fff` |
| 4 | 行高 | > 字号 | **= 字号** |
| 5 | 主 Y 轴标签 | 内部 | **外部** |
| 6 | K 线涨跌色 | `#FF2436` / `#07AB4B` | `#eb5454` / `#47b262` |

---

## 四、待设计确认 / 复核清单

1. iFinD-PC 24 色色板的权威性（已与 token 代码一致，建议设计师终确）。
2. 行高 = 字号、两侧空白 5%（28–60px、取 4 倍数）的精确边界值。
3. 折线线宽 2px、菱形隐藏点 与 base 1.5 / 圆点的差异如何对外表述。
4. K 线 `#eb5454/#47b262` vs A 股权威 `#FF2436/#07AB4B`——是否 iFinD 静态图专用浅色版。
5. **代码冲突待澄清**：paradigm-chart `mobile.spec.js` 中若存在 iFinD-mobile 独立的 axisPointer / formatter / 折线 / 图例覆盖（例如 v0.4 版本前文档曾标注的「PC + 移动端一致」条目），与本文件「iFinD-mobile = THS 主题」的设计意图冲突——需确认这些 mobile 代码应当：（a）迁出归入 THS；（b）保留为 iFinD-mobile 的额外 delta（此时需新建 ifind-mobile.md）；（c）删除。

---

## 五、来源映射（便于复核）

| 差异域 | 来源 |
| --- | --- |
| 设计通则（行高/空白/对齐/0轴/光标/标记/叠加柱/饼/面积） | `ifind静态图最新设计规范.pdf`（band00–band05） |
| PC 精确值（字体/24色/Tooltip/datazoom/轴/折线/K线） | `token.light.js` |
| 浅色色盘、色板循序 | PDF band06 + token 代码 `color[]` |
| dark 模式 | `token.dark.js`（各色值深色镜像） |
| 移动端实现 | **见 [ths.md § 来源](ths.md)**（iFinD-mobile = THS 主题） |
