# THS 主题（Theme: THS）

> 📐 **本文件只记录 THS 相对 [base 主题](base.md) 的「差异（delta）」**。未在此列出的项一律**继承 base 主题**（[tokens.md](../tokens.md) / [components/](../components/) / [charts/](../charts/)）。
>
> 用法：先按 base 主题查到值，再回此处看有无覆盖；有则**以 THS 值为准**。

---

## 主题元信息

| 项 | 值 |
| --- | --- |
| 主题名 | THS（同花顺主站业务线） |
| 继承基准 | base 主题（[../tokens.md](../tokens.md) / [../components/](../components/) / [../charts/](../charts/)） |
| 服务的业务线 | THS 主站 / 手炒 / **iFinD 移动端**（iFinD-mobile 直接使用本主题） |
| 覆盖优先级 | base → 本文件 delta → 图表级 → 实例级（最高）（详见 [SKILL § 覆盖优先级](../../SKILL.md#覆盖优先级)） |

---

## Token 覆盖（Delta）

### 可视化色板 — ⚠️ THS 主题色板（整套独立，不与 base 叠加）

> 色板维度不走 delta 继承——本节是 **THS 主题**的完整色板，使用时整套替换 base 的色板序列。详见 [SKILL.md § 主题维度叠加规则](../../SKILL.md#维度叠加规则)。
>
> 📐 **iFinD 移动端 = 直接使用 THS 主题**（不止色板，整套规范都来自本文件）；详见 [ifind.md § 关键结论](ifind.md#关键结论先看这条)。iFinD-PC 是另一套独立主题。

顺序色板 9 色，循序由左到右：

| 序号 | 色值 |
| --- | --- |
| 1 | `#3366FF` |
| 2 | `#FF4019` |
| 3 | `#FF9500` |
| 4 | `#62B312` |
| 5 | `#14CCBD` |
| 6 | `#199FFF` |
| 7 | `#4433FF` |
| 8 | `#FF33AA` |
| 9 | `#CC41D9` |

单系列默认色 = `#3366FF`（与 base 主题一致）。

### 柱状图圆角 — ⚠️ 覆盖 base 主题

| Token | base 主题 | THS 覆盖 |
| --- | --- | --- |
| `radius-bar-top` | 0px（无圆角） | **2px（仅上左、上右；底部无圆角）** |

所有柱状图类型（基础柱状图、分组柱状图、堆叠柱状图、横向条形图、折柱组合图）的柱体均在远离 0 轴的一端添加 2px 圆角：

- **正值柱**：顶部上左、上右 2px 圆角，底部无圆角
- **负值柱**：底部下左、下右 2px 圆角，顶部无圆角
- **横向条形图**：条尾（远离 Y 轴的一端）2px 圆角，起点端无圆角
- **堆叠柱状图**：仅最顶端段的上左、上右 2px 圆角，中间分段及底部无圆角
- **归一化堆叠柱状图 / 瀑布图**：无圆角（不覆盖，继承 base 主题 0px）

### 柱体最大宽度 — ⚠️ 覆盖 base 主题

| Token | base 主题 | THS 覆盖 |
| --- | --- | --- |
| `size-bar-max` | 32px | **16px** |

所有柱状图类型（基础柱状图、分组柱状图、堆叠柱状图、折柱组合图等）的**单柱最大宽度**为 16px。

> ⚠️ **仅 THS（含 iFinD-Mobile）下成立**：base 主题柱体最大宽度为 32px（`size-bar-max`），16px 是 THS 的 delta 覆盖，不要反向套回 base。

### 桑基图节点颜色 — ⚠️ 覆盖 base 主题

| 节点类型 | base 主题 | THS 覆盖 |
| --- | --- | --- |
| 源节点 + 中间节点 | `color-visualization-primary`（`#3366FF`） | **`#FF2436`** |
| 汇节点（正值） | `color-price-up`（`#FF2436`） | **`#FF9500`** |
| 汇节点（负值 / 支出） | `color-price-down`（`#07AB4B`） | **`#3366FF`** |

### 水印

沿用 base（base 水印 SVG 36×10，grid 右下角，距 grid 右 36px，6% 黑 light / 6% 白 dark）。通用机制（加载方式 / 锚定 / 资源协议 / 反例）见 [components/watermark.md](../components/watermark.md)。

### Tooltip 显示位置 — ⚠️ 覆盖 base 主题

| 项 | base 主题 | THS 覆盖 |
| --- | --- | --- |
| Tooltip 显示位置 | 位置 A（跟随鼠标右下） | **位置 C**（带 X 轴坐标的图）+ **A**（饼图等无坐标图回退）；C = 固定上方左 / 右两侧、不跟随指针 |

> THS（含 **iFinD-Mobile**）的 Tooltip 采用 **位置 C** —— 固定贴绘制区上方、不跟随指针；水平以**图表中点**为基准镜像避让（触发**左半区 → 显示右侧**、**右半区 → 显示左侧**）。完整规则见 [tooltip.md § 位置 C 定位规则](../components/tooltip.md#位置-c-定位规则)，三档对照见 [§ Tooltip 显示位置](../components/tooltip.md#tooltip-显示位置)。
>
> **适用范围**：位置 C 仅用于**带 X 轴坐标（直角坐标系）的图表**——折线族（line / multi-line / area-highlight / marker-line / rank-line / marker）、柱状族（bar / grouped-bar / stacked-bar / normalized-stacked-bar / **horizontal-bar** / bar-line-combo / waterfall）、scatter / beeswarm。**无坐标系的图表**（pie / donut / half-donut / petal / radar / treemap / sankey / relationship / two-way-tree / venn / word-cloud）回退 **位置 A**（跟随光标）。
>
> ⚠️ 横向条形图（horizontal-bar）在 THS 下用 **C**（它带 X 轴坐标，C 不依赖 X 必须是 category 轴）；而在 Ainvest 下回退 **A**（B 要求 X 必须是 category 轴，横条 category 在 Y）——两主题判据不同，归属不同属预期。
>
> 仅「显示位置」一项覆盖；其余 Tooltip 参数（背景 `#3B3B3B` / 圆角 4px / 触发 `trigger:'axis'` / 移出延迟 2000ms 等）未列，继承 base。

