# 数据缩放轴（DataZoom）

> 📌 **常用值已在 [themes/base.md（base 主题）](../themes/base.md) 内联**——画 base 主题图表时直接从那里取值。本文件是详细规范，仅在需要边界细节（动画分档、交互态、多端差异、配置选项等）时查阅。用了业务线主题（iFinD-PC / Ainvest / THS）请同时查 [themes/](../themes/) 下对应 delta 文件。

图表底部的范围滑块，用于在大数据集上缩放局部时间区间。

---

## 尺寸与布局

| 属性 | 值 | Token |
| --- | --- | --- |
| 宽度 | 与图表宽度一致（343px） | `size-chart-width` |
| 高度 | 24px | `size-slider-height` |

---

## 滑块对齐

| 模式 | 说明 |
| --- | --- |
| **默认：中心点对齐** | 滑块中心与图表中心对齐 |
| **边缘对齐**（Ainvest 样式） | 滑块左 / 右两端贴齐图表绘制区边缘 |

---

## 把手（左 / 右各一）

| 属性 | 值 | Token |
| --- | --- | --- |
| 尺寸 | 24×24px | `size-slider-handle` |
| 背景色 | 白色 | `color-foreground-layer2` |
| 边框 | 0.33px solid `rgba(0,0,0,0.24)` | `color-text-quaternary` |
| 圆角 | 6px | `radius-slider-handle` |
| 内部图标 | 3 条竖向短线（三线 grip 图标），每条 8×1.5px，间距 2.5px，颜色 `#858585`（light / dark 一致） | — |

---

## 轨道 / 数据

| 属性 | 值 | Token |
| --- | --- | --- |
| 轨道圆角 | 6px（与把手圆角一致） | `radius-slider-handle` |

| 区域 | 颜色 | Token |
| --- | --- | --- |
| A：背景底 | `color-background-weak` | — |
| B：未选中数据 | `color-visualization-datazoom-data-bg` | — |
| C：选中区域填充 | `color-visualization-datazoom-filler` | — |
| D：选中数据 | `color-visualization-datazoom-data-select` | — |

选中范围内显示微缩版图表预览（用数据色彩描出）。

---

## 提示文本（Tooltip on Slider）

| 属性 | 默认值 |
| --- | --- |
| **PC / Web 默认** | 滑块**内侧**显示提示文本（起点 / 终点日期） |
| **移动端默认** | **不显示**提示 |
| 提示文本最小间距 | **8px** |
| 当间距 < 8px | 提示文本**统一放到滑块外侧**（统一位置逻辑） |
| 外侧展示时 | 允许文本截断显示（区间小且靠近两端时） |

---

## 点击拖动

| 行为 | 说明 |
| --- | --- |
| 拖动滑块把手 | 选取起始与结束区域 |
| 区域拖动（按住中间） | 区间范围不变，整体平移调整起始 / 结束点位 |

---

## 可配置项

1. 滑块高度
2. 滑块对齐方式（中心 / 边缘）
3. 选中色块 / 选中数据色
4. 未选中背景色 / 未选中数据色
5. 数据缩放轴圆角
6. 提示样式（位置、字号、颜色）
