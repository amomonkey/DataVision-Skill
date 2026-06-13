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

### 桑基图节点颜色 — ⚠️ 覆盖 base 主题

| 节点类型 | base 主题 | THS 覆盖 |
| --- | --- | --- |
| 源节点 + 中间节点 | `color-visualization-primary`（`#3366FF`） | **`#FF2436`** |
| 汇节点（正值） | `color-price-up`（`#FF2436`） | **`#FF9500`** |
| 汇节点（负值 / 支出） | `color-price-down`（`#07AB4B`） | **`#3366FF`** |

### 水印

沿用 base（base 水印 SVG 36×10，grid 右下角，距 grid 右 36px，6% 黑 light / 6% 白 dark）。通用机制（加载方式 / 锚定 / 资源协议 / 反例）见 [components/watermark.md](../components/watermark.md)。

