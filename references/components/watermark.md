# 水印（Watermark）

> 📌 水印是所有图表通用的附加元素。本文件定义**通用结构 / 加载机制 / 锚定基准 / 资源协议**——具体 logo 资源、透明度、位置偏移由 [themes/](../themes/) 各主题 delta 覆盖。
>
> 用法：先按本文件查通用机制；再回对应主题文件（[base.md](../themes/base.md) / [ifind.md](../themes/ifind.md) / [ainvest.md](../themes/ainvest.md) / [ths.md](../themes/ths.md)）取主题 delta。

---

## 适用范围

| 适用 | 不适用 |
| --- | --- |
| ✅ 所有图表族（柱 / 折线 / 饼 / 雷达 / 桑基 / treemap / 散点 / ...）每个图各挂一份 | ❌ 主副图结构里副图共用主图水印（不重复加） |

---

## 锚定基准

- **默认**：锚定 **grid 绘图区**角落（左下 / 右下，具体哪个角由主题指定）
- **不要锚定图表容器边缘**——窄屏 / resize 时水印会与 X 轴标签 / 数据图形位置抢
- 主副图：水印挂主图 grid，副图不再加

### 偏移基准（⚠️ 模型注意）

> 主题如指定偏移值（如 "**距 grid 右侧 36px**"），按以下规则解读，避免与其他 grid 内规则混淆：

| 名词 | 含义 | 像素位置（以 ECharts 为例） |
| --- | --- | --- |
| **grid 边缘** | grid 矩形的物理边——`grid.left/right/top/bottom` 框出的四条边 | `chart.convertToPixel({gridIndex:0}, [xVal, yVal])` 取边像素 |
| **数据区边缘** | grid 边缘**再**向内按 [layout.md § 形式 A 10% 内缩](layout.md#y-轴标签布局) 缩进后的虚拟边 | grid 边缘 + 10% × grid 宽 |
| **容器边缘** | 整个图表 `<div>` / `<canvas>` 的物理边 | `chart.getWidth()` / `chart.getHeight()` 全尺寸 |

**水印偏移值始终相对 grid 边缘**，不是数据区边缘、不是容器边缘：

- ✅ "距 grid 右侧 36px" = 从 grid 右物理边向内 36px
- ❌ 不要理解为 "从 10% 内缩后数据区右边再 36px"——那会让水印多缩 10% × grid 宽
- ❌ 不要理解为 "从容器右边 36px"——容器宽变化时水印会跟 X 轴标签 / 数据抢位置

**与 [layout.md § 形式 A 数据区两侧 10% 内缩](layout.md#y-轴标签布局) 的关系**：
- 两条规则**用同一个 grid 坐标系，但走两条独立轨道**——数据图形按 10% 内缩、水印按各自偏移值
- 不要叠加：水印 36px 偏移 ≠ "10% + 36px"，就是 36px
- 实现上：水印是 `graphic.image` 独立元素，与 `xAxis.boundaryGap` 互不影响

---

## 层级（z-index）

水印**置顶**——与轴标签同层（`z-index-axisLabel` 57），压在所有数据图形之上，防止被裁剪 / 遮挡 / 移除。**不是垫底背景**，不要把它放到数据下方。完整层级表见 [base.md § Z-Index 层级](../themes/base.md#z-index-层级)。

---

## 资源协议（⚠️ 必读）

> skill 引用的 `assets/examples/_shared/*-watermark-*.svg` 是**生产级资源**——不是说明图，**必须直接加载使用**。详见 [SKILL.md § 全局规则 5：资源文件优先于参数描述](../../SKILL.md#5-资源文件优先于参数描述asset-over-params)。

- 出图 / 出代码时**直接 `<img>` / `graphic.image` / Data URI 加载这些 SVG 文件**
- **不要**按主题表里的尺寸 / 颜色 / 字形参数自行用 `<rect>` + `<text>` 重绘
- 参数表（如 `96×20`、`5% 黑`）仅用于**布局校验**——确认资源加载后的摆放、尺寸、透明度符合规范
- 真实 SVG 通常包含多条 `<path>` 字形 / logo 路径，任何手画矩形 + 文字都不像

---

## 加载方式（任选一种）

### A. HTML overlay（最稳，与 ECharts 解耦）

```html
<div style="position:relative">
  <div id="chart" style="width:343px;height:280px"></div>
  <img src="assets/examples/_shared/ainvest-watermark-light.svg"
       width="96" height="20"
       style="position:absolute;left:12px;bottom:6px;pointer-events:none">
</div>
```

### B. ECharts `graphic.image`（与 chart 同渲染管线）

```js
chart.setOption({
  graphic: {
    elements: [{
      type: 'image',
      left: 12, bottom: 6,
      silent: true,
      style: {
        image: 'assets/examples/_shared/ainvest-watermark-light.svg',
        width: 96, height: 20
      }
    }]
  }
}, { replaceMerge: ['graphic'] });
```

⚠️ **必须用 `{ elements: [...] }` 包裹** + `replaceMerge: ['graphic']`——否则二次 `setOption` 合并时 ECharts 按 index 错配 graphic，新水印元素可能被吃掉。详见 [echarts-implementation-hints.md § 陷阱 17](../echarts-implementation-hints.md)。

### C. 内嵌 Data URI（`file://` 协议或跨域兜底）

```js
const svgText = '<svg width="96" height="20" ...>... 原文 ...</svg>';
const dataUri = 'data:image/svg+xml;utf8,' + encodeURIComponent(svgText);
// 把 dataUri 传给 graphic.image.style.image 或 <img src>
```

---

## 视觉规格（主题 delta 对照）

> 具体值是各主题对本文件「默认机制」的覆盖。未列出的项一律继承本文件默认。

| 主题 | 资源 | 尺寸 | 透明度 (light) | 透明度 (dark) | 锚定 | 端 |
| --- | --- | --- | --- | --- | --- | --- |
| **base** | [light SVG](../../assets/examples/_shared/base-watermark-light.svg) / [dark SVG](../../assets/examples/_shared/base-watermark-dark.svg) | 36×10 | 6% 黑 | 6% 白 | **grid 右下角，距 grid 右侧 36px** | mobile + PC 同 |
| **iFinD-PC** | （沿用 base） | — | — | — | — | PC |
| **Ainvest** | [light SVG](../../assets/examples/_shared/ainvest-watermark-light.svg) / [dark SVG](../../assets/examples/_shared/ainvest-watermark-dark.svg) | 96×20 | 5% 黑 | 20% 白 | **grid 左下角** | mobile + PC 同 |
| **THS** | （沿用 base） | — | — | — | — | mobile |

> 完整 delta 背景与未列项：
> - Ainvest → [ainvest.md § 1.10](../themes/ainvest.md#110-水印端通用)
> - 其他主题 → 对应 [themes/](../themes/) 文件 § 水印

---

## 反例（⚠️ 不要这样做）

```js
// ❌ 看到主题表里 "96×20 / 5% 黑" 就自己拼一个矩形 + 文字
// 这是占位符，不是真实 logo——真实 SVG 是多条 path 组成的字形
{ type: 'rect', shape: { x: 0, y: 0, width: 96, height: 20 },
  style: { fill: 'rgba(0,0,0,0.05)' } },
{ type: 'text', style: { text: 'Ainvest', fontSize: 11, fill: 'rgba(0,0,0,0.45)' } }

// ❌ 直接 graphic: [...] 不用 elements 包裹
chart.setOption({ graphic: [...existing, watermark] })
// 二次 setOption 合并按 index 走，水印可能被吃掉或与已有 graphic 错位
```

---

## 端通用 / 端差异约定

- **业务线身份级项**（logo / 透明度 / 锚定角）：通常 mobile + PC 共用一套
- **尺寸 / 偏移**：偶有端差异，按主题 delta 单列

---

## ECharts 实现陷阱

见 [echarts-implementation-hints.md § 陷阱 17](../echarts-implementation-hints.md)（`graphic.image` 合并坑、`file://` 加载、`width/height` 必填、5% 黑视觉等）。
