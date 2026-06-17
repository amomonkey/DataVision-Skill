# ECharts 实现提示（Implementation Hints）

> 📘 **本文件是「实现层」补充**——主体规范（[tokens.md](tokens.md) / [components/](components/) / [charts/](charts/)）保持**库无关**；此文档仅在用 ECharts 落地实现时查阅。
>
> **不绑定使用者必须用 ECharts**——用 AntV G2 / D3 / Recharts 等其他库的开发者可类比映射本文档的「设计意图」到对应库的字段。
>
> 这份文档专门解决一个结构性问题：skill 规范说「Y 轴标签默认在网格内部」，但 Claude 写 ECharts 代码时不知道该写 `yAxis.axisLabel.inside: true`。本文档把高频踩坑的「设计概念 → ECharts option 字段」配对列清楚。

---

## 一、设计概念 → ECharts option 字段对照表

### 坐标轴 / 网格

| 设计概念（来自规范）                 | ECharts option                                                                                                                         | 关键提示                                                                                                                                                                                       |
| -------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Y 轴标签默认在**网格内部**           | `yAxis.axisLabel.inside: true`                                                                                                         | 默认 `false`（外部），必须显式设 `true`；允许特色情况设 `false` 外部                                                                                                                                             |
| Y 轴主轴**左侧**                | `yAxis.position: 'left'`                                                                                                               | ECharts 默认即左，但多轴时要明确                                                                                                                                                                       |
| Y 轴 **5 条**等距分割线           | `yAxis.splitNumber: 4`                                                                                                                 | ⚠️ `splitNumber` 是「段数」不是「线数」；4 段 = 5 条线                                                                                                                                                    |
| X 轴标签**左 / 中 / 右**三个位置     | `xAxis.axisLabel.showMinLabel: true` + `showMaxLabel: true` + `interval: 'auto'`                                                       | 中间标签按需自定义 formatter                                                                                                                                                                        |
| 首尾标签**贴边不裁切**（首左对齐 / 尾右对齐） | `xAxis.axisLabel.alignMinLabel: 'left'` + `alignMaxLabel: 'right'`（ECharts 5.3+）；可选 `grid.containLabel: true` 让 ECharts 自动撑出标签所需空间 | ⚠️ 仅 `showMinLabel/showMaxLabel: true` 只是「显示」首尾标签，**默认仍以刻度居中**——首尾刻度贴边时标签外半截溢出被裁（症状：`2025-05`→`-05`、`2026-…`→`26-`）。必须再加 alignMinLabel / alignMaxLabel。用 paradigm-chart 则 `axisLabel.dvAlignEdge: true` 一项搞定 |
| 网格默认 Y 方向显示、X 方向隐藏         | `xAxis.splitLine.show: false` + `yAxis.splitLine.show: true`                                                                           | ECharts 默认 X 轴 splitLine 在 category 轴关闭、value 轴开启；显式写更稳                                                                                                                                    |

### 0 轴线 / 网格线区分

| 设计概念 | ECharts option | 关键提示 |
| --- | --- | --- |
| **0 轴线颜色加深**（与普通网格线区分） | 普通网格线 `yAxis.splitLine.lineStyle.color: '#000000 6%'`；0 轴线**单独叠加** | ECharts 没有原生「0 轴突出」配置；需用 `graphic` 或 `markLine type:'average'` value:0 实现，颜色用 `color-visualization-divider-deep` (`#858585`) |
| 0 轴线浮动（不固定底部） | 用 `yAxis.min/max` 含负值时自然浮动 | 数据含负值时 0 自动出现在中间 |

### Tooltip / 光标

| 设计概念 | ECharts option | 关键提示 |
| --- | --- | --- |
| Tooltip 光标线 **solid 实线** | `tooltip.axisPointer.lineStyle.type: 'solid'` | ⚠️ ECharts 某些版本/主题默认是 `dashed`，**必须显式设 solid** |
| 光标线颜色 | `tooltip.axisPointer.lineStyle.color: '#000000 60%'` | 对应 `color-visualization-highlight-line` |
| Tooltip 触发方式 | `tooltip.trigger: 'axis'` | 鼠标移到坐标点即触发（不是 `item` 停留触发） |
| Tooltip 无过渡动画 | `tooltip.transitionDuration: 0` | 紧跟鼠标，无拖影 |
| Tooltip 默认最大宽度 160 | `tooltip.extraCssText: 'max-width: 160px;'` | ECharts 没有原生 maxWidth，用 css |
| 移出延迟隐藏 2000ms | `tooltip.hideDelay: 2000` | |
| **折线图光标在折线下方** / **折柱组合折线在柱上方** | `tooltip.axisPointer.z: 1`（或更低） + 折线 series `z: 5`（柱保持默认 `z: 2`） | ⚠️ ECharts 默认 axisPointer 在最上层，**折线图必须显式调低 z**；折线 `z: 5` 同时落实 base「折线高于柱条」（陷阱 6） |
| X 轴标签 hover 高亮（背景胶囊） | `xAxis.axisPointer.label.show: true` + `padding` + `backgroundColor` | 用 `color-visualization-highlight-background-tick` |

### 图例

| 设计概念 | ECharts option | 关键提示 |
| --- | --- | --- |
| 图例**从左侧开始** | `legend.left: 'left'` | ⚠️ ECharts 默认居中（`'center'`），**必须显式设 `'left'`** |
| 柱状图例标记 **6×6 方块** | `legend.itemWidth: 6` + `itemHeight: 6` + `icon: 'roundRect'` | ⚠️ `itemWidth/Height` 是**标记本体**尺寸，6（不是 8 不是 12）；12×12 容器由 padding 实现 |
| 折线图例标记 **8×2 短横线（1px 圆角）** | `legend.itemWidth: 8` + `itemHeight: 2` + `icon: 'path://M1,0 L7,0 A1,1 0 0 1 7,2 L1,2 A1,1 0 0 1 1,0 Z'` | ⚠️ 折线标记宽 8、高 2、1px 圆角胶囊；**不要用柱图的 6×6** 也不要用直角矩形或零高度 path。path 坐标系 8×2，ECharts 按 itemWidth×itemHeight 缩放 |
| 饼/环/雷达图例标记 **6×6 圆点** | `legend.itemWidth: 6` + `itemHeight: 6` + `icon: 'circle'` | |
| **折柱混合图例各自独立 icon** | `legend.data: [{name:'柱', icon:'roundRect'}, {name:'线', icon:'path://...'}]` | ⚠️ 混合图表不要统一 icon——柱系列用方块、折线系列用短线，在 `legend.data` 的每个 item 上单独设 `icon` |
| **单系列也要显示图例** | 给 series 加 `name`，不要省略 `legend` 配置 | ⚠️ 即使只有一条系列，也必须显示图例（常见遗漏） |
| 图例文字字号 12 | `legend.textStyle.fontSize: 12` | |
| 图例溢出**换行** | `legend.type: 'plain'` + 不设固定宽 | ECharts 默认换行 |
| 图例溢出**分页器**（Web/PC） | `legend.type: 'scroll'` | |
| **图例与绘图区不重叠** | `legend.top: 0` + 计算 `grid.top` 适配 | ECharts 没有自动让位，**必须手动协调** `grid.top` |

### 饼图 / 环图

| 设计概念 | ECharts option | 关键提示 |
| --- | --- | --- |
| 扇区**无白色边框** | `series.itemStyle.borderWidth: 0` 或 `borderColor: 'transparent'` | ⚠️ ECharts 饼图默认 `borderColor: '#fff'` + `borderWidth: 1`，**必须显式去掉** |
| 数据标签**必有引导线** | `series.label.show: true` + `labelLine.show: true` | |
| 引导线**起点距饼图边缘 +8px** | `series.labelLine.length: 8` | 第一段引线长度 |
| 引线颜色跟随扇区色 | `series.labelLine.lineStyle.color: 'inherit'` | 或不设，默认继承 itemStyle.color |
| 环图半径 70 + 环宽 28 | `series.radius: [42, 70]`（内径 42 = 70-28，外径 70） | |
| 半环 180° | `series.startAngle: 180` + `endAngle: 0` | |

### 柱状图 / 瀑布 / 归一化堆叠

| 设计概念                | ECharts option                                | 关键提示                                        |
| ------------------- | --------------------------------------------- | ------------------------------------------- |
| 柱体最大宽度 32           | `series.barMaxWidth: 32`                      | ⚠️ 用 `barMaxWidth` 不用 `barWidth`，数据密集时自适应缩放 |
| 柱间距比 2:1            | `series.barGap: '50%'`                        | 50% = 间距是柱宽的 50%                            |
| 柱体无圆角               | `series.itemStyle.borderRadius: 0`            | 所有柱状图均无圆角                                   |
| 0 值显示 1px 占位        | `series.barMinHeight: 1`                      |                                             |

### 折线图

| 设计概念 | ECharts option | 关键提示 |
| --- | --- | --- |
| 单线线宽 1.5 / 多线主线 1.5 / 其他 1 | `series.lineStyle.width: 1.5`（主线）/ `1`（其他） | 用 series 的 `id: 'mainLine'` 标识主线 |
| 数据点直径 6 | `series.symbolSize: 6` | |
| 数据点**描边色跟随折线色** | `series.itemStyle.borderColor` = 折线色（不要设白色） | ⚠️ 默认描边色不是折线色 |
| hover fill 切白，描边不变 | `series.emphasis.itemStyle.color: '#fff'`（fill 白） + `borderColor` 不变 | 描边色继承默认（折线色），**不要在 emphasis 里覆盖 borderColor** |
| 数据点 >13 隐藏 | `series.showSymbol: false`（数据点多时）+ 手动控制 | ECharts 没自动密度控制，需 series.showAllSymbol + 自定义判断 |
| 高密度采样 | `series.sampling: 'lttb'` | |

### 蜂群图

| 设计概念 | ECharts option | 关键提示 |
| --- | --- | --- |
| 标签默认隐藏 | `series.label.show: false` | |
| hover 显示当前点标签 | `series.emphasis.label.show: true` | |
| 仅最大点显示 | 用 `data[i].label.show = true` 给最大值单独配置 | |

---

## 二、常见陷阱速查（来自反馈高频踩中）

### 陷阱 1：光标线显示为虚线

**症状**：hover Tooltip 时光标线是 `dashed` 而非 `solid`。

**修法**：
```js
tooltip: {
  axisPointer: {
    type: 'line',
    lineStyle: {
      type: 'solid',  // ⚠️ 必须显式 solid
      color: 'rgba(0,0,0,0.6)',
      width: 1
    }
  }
}
```

### 陷阱 2：Y 轴标签出现在网格外部 / 右侧

**症状**：默认 Y 轴标签飘在网格外。

**修法**：
```js
yAxis: {
  position: 'left',     // 主 Y 轴左侧
  axisLabel: {
    inside: true        // 默认网格内部
  }
}
```

特色情况外部时 `inside: false`，符合规范的「灵活外部」。

### 陷阱 3：网格线数量过多 / 不固定为 5 条

**症状**：
- Y 轴分割线 8-10 条，看起来碎；或
- 同一份代码在不同高度 / 不同数据下输出 3 条 / 6 条 / 7 条，**无法稳定到 5**。

**根因**：`splitNumber` 在 ECharts 里是**建议值**，不是强制。ECharts 内部会基于"nice 数字"挑选刻度，数据跨度落在 `[step×3.5, step×6.5]` 之间时段数会自然漂移。

**只设 `splitNumber:4` 不够**——必须把 `min` / `max` / `interval` 一并下发，强制对齐 4 段（含 0 轴 = 5 条线）。

**修法**：用下面这个 `niceScale` 函数算出三件套：

```js
// 严格 4 段（5 条线，含 0 轴）。算法：在 nice 因子里找最小 step 使 step*4 ≥ 数据跨度
function niceScale(vals, splits = 4) {
  const flat = [].concat(...vals.map(v => Array.isArray(v) ? v : [v]))
                 .filter(v => typeof v === 'number' && isFinite(v));
  if (!flat.length) return null;
  let lo = Math.min(...flat), hi = Math.max(...flat);
  if (lo > 0) lo = 0;            // 正数数据必须从 0 起（柱图前提）
  if (hi < 0) hi = 0;
  const span = (hi - lo) || Math.abs(hi || 1);
  const nice = [1, 1.5, 2, 2.5, 3, 4, 5, 6, 7.5, 8, 10];
  const baseMag = Math.pow(10, Math.floor(Math.log10(span / splits)));
  for (let p = -2; p <= 2; p++) {
    const m = baseMag * Math.pow(10, p);
    for (const f of nice) {
      const step = f * m;
      if (step * splits < span - 1e-9) continue;
      const negSegs = lo < 0 ? Math.ceil(-lo / step - 1e-9) : 0;
      const posSegs = hi > 0 ? Math.ceil( hi / step - 1e-9) : 0;
      if (negSegs + posSegs > splits) continue;
      // 剩余段数补到正方向（顶部留白）
      return { min: -negSegs * step, max: (splits - negSegs) * step, interval: step };
    }
  }
  return null;
}

// 使用：把数据传入，结果合并进 yAxis
const sc = niceScale(seriesData);
yAxis: {
  ...baseY,
  splitNumber: 4,                 // 兜底
  min: sc.min, max: sc.max, interval: sc.interval   // 强制
}
```

**校验范例**（splits=4）：

| 数据 max | step | 输出刻度 |
| --- | --- | --- |
| 862    | 250  | 0 / 250 / 500 / 750 / 1000 |
| 1741   | 500  | 0 / 500 / 1000 / 1500 / 2000 |
| 501    | 150  | 0 / 150 / 300 / 450 / 600 |
| 18.04  | 5    | 0 / 5 / 10 / 15 / 20 |
| -16~31 | 10   | -10 / 0 / 10 / 20 / 30 |

**易错点**：

1. **正数数据必须强制 lo=0**——否则单系列柱图 hi=862 时 ECharts 会以 min=466 起跳，柱子贴顶看不到差异。
2. **堆叠柱图传单系列会算偏**——要先把各 category 的 series 加总成数组，再传入 `niceScale`。
3. **多系列折线只传第一条 max 不够**——要 `.reduce((a,s) => a.concat(s.data), [])` 合并所有系列。
4. **双 Y 轴左右独立调用**，不要共用 step。
5. **百分比堆叠柱例外**：固定 `min:0 / max:100 / interval:25`，不调用 `niceScale`。
6. **类目轴 (`type:'category'`) 不适用**——段数由数据决定。
7. **降级**：移动端 ≤400px 标签拥挤时可降到 `splits=3`（4 条线）。

### 陷阱 4：饼图扇区之间有白色分割

**症状**：扇区之间有 1-2px 白色线条。

**修法**：
```js
series: [{
  type: 'pie',
  itemStyle: {
    borderWidth: 0          // 或
    // borderColor: 'transparent'
  }
}]
```

### 陷阱 5：饼图数据标签贴着饼图边缘

**症状**：数据标签和饼图边缘重叠。

**修法**：
```js
series: [{
  type: 'pie',
  label: {
    show: true,
    formatter: '{b}\n{d}%'
  },
  labelLine: {
    show: true,
    length: 8,           // ⚠️ 默认 +8px 间距
    length2: 8,
    lineStyle: {
      color: 'inherit'   // 跟随扇区色
    }
  }
}]
```

### 陷阱 6：折线图光标线压在折线上

**症状**：光标线 z-index 高于折线，盖住数据。

**修法**：
```js
// 折线 series
series: [{
  type: 'line',
  z: 5,           // 折线 z 提高
  ...
}],
// Tooltip 光标线
tooltip: {
  axisPointer: {
    z: 1          // ⚠️ 光标 z 调低（默认很高）
  }
}
```

> **关联要求：折线必须高于柱条**（[base.md § Z-Index 折线图 / 折柱组合特例](themes/base.md#折线图--折柱组合特例重要)）——折柱组合图中柱 series 默认 `z: 2`（规范数据图形层 `z-index-data-item` 4），折线统一 `z: 5` 即同时满足「高于光标线」与「高于柱条」两条要求。**不要给柱 series 设 `z ≥ 5`**，否则柱会反过来盖住折线。

### 陷阱 7：图例标记尺寸 = 8（默认）而非 6

**症状**：图例标记看起来比设计稿略大。

**修法**：
```js
legend: {
  itemWidth: 6,   // ⚠️ 不是 8 也不是 12
  itemHeight: 6
}
```

### 陷阱 8：折线数据点 hover 时描边变白

**症状**：hover 数据点时整个圆变白（fill + 描边都白）。

**修法**：
```js
series: [{
  type: 'line',
  itemStyle: {
    borderColor: '#3366FF',  // 折线色
    borderWidth: 1.5,
    color: '#3366FF'         // 默认 fill = 折线色
  },
  emphasis: {
    itemStyle: {
      color: '#FFFFFF'       // hover fill 切白
      // ⚠️ 不要覆盖 borderColor —— 保持折线色
    }
  }
}]
```

### 陷阱 9：0 轴线和普通网格线一样浅

**症状**：分不出 0 轴。

**修法**（用 `markLine`）：
```js
yAxis: {
  splitLine: {
    lineStyle: { color: '#000000', opacity: 0.06 }  // 普通网格浅
  }
},
series: [{
  // 任意 series 上挂 markLine
  markLine: {
    silent: true,
    symbol: 'none',
    data: [{ yAxis: 0 }],
    lineStyle: {
      color: '#858585',  // 0 轴加深
      width: 1,
      type: 'solid'
    }
  }
}]
```

### 陷阱 10：柱体出现圆角

**症状**：柱体有圆角（ECharts 默认或误配）。

**修法**：所有柱状图均不设圆角，确保 `borderRadius: 0`。
```js
series: [{
  type: 'bar',
  itemStyle: {
    borderRadius: 0
  }
}]
```

### 陷阱 11：X 轴首尾日期标签被裁切（只剩半截）

**症状**：X 轴最左标签只显示 `-05`、最右只显示 `26-`（完整应是 `2025-05` / `2026-XX`）。首尾标签的外半截溢出绘图区被容器裁掉。

**根因**：折线 / 面积图 `boundaryGap: false` 时，首尾数据点（及其刻度）正好落在绘图区左右边缘；标签默认**以刻度居中**，于是首标签的左半、尾标签的右半溢出被裁。仅设 `showMinLabel/showMaxLabel: true` 只能让它们「显示」，挡不住裁切。

**修法**（规范要求：首左对齐 / 尾右对齐，见 [axes.md](components/axes.md) X 轴标签节）：
```js
grid: {
  containLabel: true   // ECharts 自动为坐标轴标签撑出空间（可选；按落地实例需要决定）
},
xAxis: {
  axisLabel: {
    showMinLabel: true,
    showMaxLabel: true,
    alignMinLabel: 'left',   // ⚠️ 首标签左对齐 —— 锚点在左，向右伸进图里，不再从左缘溢出
    alignMaxLabel: 'right'   // ⚠️ 尾标签右对齐 —— 锚点在右，向左伸，不再从右缘溢出
    // ECharts < 5.3 无 alignMinLabel：改用 formatter 给首尾标签留空格
  }
}
```
> 用 paradigm-chart / iFinD 主题时，`xAxis.axisLabel.dvAlignEdge: true` 已内置此对齐（iFinD 另有「两侧空白 5%、28~60px」留白规则双保险）；走原生 ECharts 才需手动加 `alignMinLabel/alignMaxLabel`。

> ⚠️ **仅 `boundaryGap: false`（首尾数据贴 grid 边）适用**。`boundaryGap: true`（柱状族 / category 轴——首尾落在 band 中心、距边有半 band 缓冲）**不要加 `alignMinLabel/alignMaxLabel`**：首尾标签本应居中、与柱 / 点对齐，强行首左 / 尾右对齐会把它们推离 band 中心，造成 X 轴标签与数据错位（带 datazoom 时每个窗口边缘标签都会中招）。权威规则见 [axes.md § X 轴标签](components/axes.md)。

### 陷阱 12：图例标记形状全部一样（没有区分柱/线/圆）

**症状**：所有图表的图例标记都是 `roundRect` 方块（或都是 `circle` 圆点），折线图的图例看不出是线形。

**修法**：
```js
// 柱状图
legend: {
  left: 'left',
  itemWidth: 6, itemHeight: 6,
  icon: 'roundRect'            // 6×6 小方块
}
// 折线图
legend: {
  left: 'left',
  itemWidth: 8, itemHeight: 2,
  icon: 'path://M1,0 L7,0 A1,1 0 0 1 7,2 L1,2 A1,1 0 0 1 1,0 Z'  // 8×2 胶囊（1px 圆角）
}
// 饼/环/雷达
legend: {
  left: 'left',
  itemWidth: 6, itemHeight: 6,
  icon: 'circle'               // 6×6 圆点
}
// 折柱混合——每个 series 单独指定 icon、itemWidth、itemHeight（混合尺寸）
legend: {
  left: 'left',
  data: [
    { name: '营业收入', icon: 'roundRect', itemWidth: 6, itemHeight: 6 },
    { name: '同比增长', icon: 'path://M1,0 L7,0 A1,1 0 0 1 7,2 L1,2 A1,1 0 0 1 1,0 Z', itemWidth: 8, itemHeight: 2 }
  ]
}
```

⚠️ 混合图例顶层 `itemWidth/itemHeight` 是全局值——折柱混合里柱（6×6）与线（8×2）尺寸不同，必须把 `itemWidth/itemHeight` 写到 `data[]` 的每一项里覆盖。

### 陷阱 13：单系列图表漏掉图例

**症状**：基础柱状图、基础折线图等单系列图表没有显示图例。

**修法**：即使只有一条 series，也要给 series 加 `name` 并配置 `legend`：
```js
legend: { left: 'left', itemWidth: 6, itemHeight: 6, icon: 'roundRect' },
series: [{
  name: '归母净利润',  // ⚠️ 必须有 name
  type: 'bar',
  ...
}]
```

### 陷阱 14：Y 轴单位（name）与顶部图例重叠 / 拖宽时延迟跟随

**症状**：
- 双轴图（折柱混合 / 主副图）的 `yAxis.name`（"亿元" / "%"）与顶部 `legend` 文字水平重叠。
- 拖动容器宽度时，图例从 1 行变 2 行（或反之），但 `grid.top` 不跟随，要等下一帧或 `finished` 触发后才调整，视觉上"跳一下"。

**根因**：
1. `yAxis.name` 默认 `nameLocation:'end'` 渲染在轴顶端，与 `legend.top:0` 抢同一行。
2. ECharts 没有把 legend 实际渲染高度自动喂给 grid.top；`chart.resize()` 仅缩放尺寸，不会重排 grid。

**修法（一步到位）**：
1. **预估算图例占用高度**（基于宽度 + 字符宽度），算出 `gridTop = legendH + 4 + 16`。
2. **配 `nameGap: 6`**——name 紧贴 grid 顶端，刚好落在「图例下方、刻度上方」的独立行。
3. **挂 `ResizeObserver`** 监听容器尺寸变化，立刻重估并 `setOption({grid:{top:newGT}}, false)`，与 ECharts 内部 resize 在同一帧合并。

```js
// 估算图例行数（不依赖 DOM，纯计算）
function estimateLegendH(legendData, chartW, padding, fontSize) {
  padding = padding || 32;   // grid left+right 安全间距
  fontSize = fontSize || 12;
  var available = chartW - padding;
  var iconW = 6, iconTextGap = 4, itemGap = 12, lineH = 16;
  var line = 0, totalLines = 1;
  legendData.forEach(function(item) {
    var name = typeof item === 'string' ? item : item.name;
    if (!name) return;
    // 中文 1×fontSize，ASCII 0.55×fontSize
    var w = 0;
    for (var i = 0; i < name.length; i++) {
      var c = name.charCodeAt(i);
      w += (c > 127) ? fontSize : fontSize * 0.55;
    }
    var itemW = iconW + iconTextGap + w + itemGap;
    if (line + itemW > available) { totalLines++; line = itemW; }
    else { line += itemW; }
  });
  return totalLines * lineH;
}

// 自适应包装器：用它代替 chart.setOption(opt)
function renderWithAdaptiveYName(chart, opt, cfg) {
  cfg = cfg || {};
  var gap = cfg.gap || 4;            // 图例底到 name 行顶
  var nameLineH = cfg.nameLineH || 16;
  var nameGap = cfg.nameGap || 6;    // name 与 axis 端距离
  var lastGT = -1;

  var legCfg = opt.legend || {};
  var legendData = legCfg.data || (opt.series||[])
    .map(function(s){return s.name;}).filter(Boolean);

  function computeGT(){
    var w = chart.getWidth();
    var lH = estimateLegendH(legendData, w);
    return Math.round(lH + gap + nameLineH);
  }

  function apply(gt) {
    var grid = Array.isArray(opt.grid)
      ? opt.grid.map(function(g, i){ return i===0 ? Object.assign({}, g, {top:gt}) : g; })
      : Object.assign({}, opt.grid||{}, {top:gt});
    var yAxis = (Array.isArray(opt.yAxis) ? opt.yAxis : [opt.yAxis])
      .map(function(y){ return y && y.name ? Object.assign({}, y, {nameGap:nameGap}) : y; });
    return { grid: grid, yAxis: yAxis };
  }

  // 首次渲染：预估算注入 baseOption
  var gt = computeGT();
  Object.assign(opt, apply(gt));
  lastGT = gt;
  chart.setOption(opt);

  // 宽度变化：立刻重算，与 resize 同帧合并
  var ro = new ResizeObserver(function(){
    var newGT = computeGT();
    if (Math.abs(newGT - lastGT) < 2) return;
    lastGT = newGT;
    chart.setOption(apply(newGT), false);  // merge 模式只改 grid.top
  });
  ro.observe(chart.getDom());
}
```

**易错点**：

1. **不能只在 `finished` 事件里测量**——首次渲染就会"用旧 gridTop 渲染一帧 → 跳到新值"。必须在 `setOption` 之前预估算。
2. **不能用 `chart.on('resize',...)`**——ECharts 没有这个事件。用 `ResizeObserver(chart.getDom())`。
3. **`setOption(patch, false)`** 第二参 `notMerge=false` 是合并模式（默认就是 false），patch 只需带 `grid` / `yAxis`，其他保留。
4. **字符宽度估算**：中文取 1×fontSize、ASCII 取 0.55×fontSize 即可。THSJinRongTi / PingFang 等中文字体宽度基本一致。
5. **iFinD PC 主题**字号变成 14，记得把 `fontSize` 参数传 14。

### 陷阱 15：Tooltip 内的图例标记与图表 legend 不一致 / 主题切换不跟随

**症状**：
- 图表 legend 是 8×2 短横线（折线），但 tooltip 行首是固定圆点；
- 切到 Ainvest 主题，legend 改成圆形了，tooltip 还是默认深色圆点；
- 折柱混合图：tooltip 里所有行都是圆点，看不出哪行是柱、哪行是折线。

**根因**：ECharts `params.marker`（`'<span style="...">⬤</span>'`）是**固定渲染的圆点**，颜色跟随系列、形状不跟随 legend。`formatter` 里直接用 `p.marker` 等于放弃了一致性。

**修法**：自己拼接 marker HTML，与 legend 用同一份"形状映射表"，并按 series 类型 / 主题选形状。

```js
// 1) 形状映射表（与 legend 共用一份；主题里 override）
function markerHtml(seriesType, color, themeMarker) {
  // themeMarker 可以是 'circle' 让某些主题强制全圆形（如 Ainvest）
  var shape = themeMarker || (
    seriesType === 'line'     ? 'pill'   :   // 8×2 胶囊
    seriesType === 'bar'      ? 'square' :   // 6×6 方块
    seriesType === 'pie'      ? 'dot'    :   // 6×6 圆点
    seriesType === 'radar'    ? 'dot'    :
    seriesType === 'scatter'  ? 'dot'    :
    'dot'
  );
  var style = 'display:inline-block;background:' + color + ';margin-right:6px;vertical-align:middle;';
  if (shape === 'pill')   return '<span style="' + style + 'width:8px;height:2px;border-radius:1px;"></span>';
  if (shape === 'square') return '<span style="' + style + 'width:6px;height:6px;border-radius:1px;"></span>';
  if (shape === 'dot' || shape === 'circle')
    return '<span style="' + style + 'width:6px;height:6px;border-radius:50%;"></span>';
  return '<span style="' + style + 'width:6px;height:6px;"></span>';
}

// 2) Tooltip formatter：丢掉 p.marker，用 markerHtml 重拼
tooltip: {
  trigger: 'axis',
  formatter: function(params) {
    var html = '<div>' + params[0].axisValue + '</div>';
    params.forEach(function(p) {
      // p.seriesType 是 'bar' / 'line' / 'pie' 等
      // theme.tooltipMarker 是主题级 override（默认 undefined → 按 series 类型走）
      var marker = markerHtml(p.seriesType, p.color, currentTheme.tooltipMarker);
      html += '<div style="display:flex;justify-content:space-between;align-items:flex-start;">'
            +   '<span style="min-width:0;word-break:break-word;">' + marker + p.seriesName + '</span>'
            +   '<span style="margin-left:24px;flex-shrink:0;white-space:nowrap;">' + p.value + '</span>'
            + '</div>';
    });
    return html;
  }
}

// 3) 主题挂钩：Ainvest 全圆形
var THEME_AINVEST = {
  // ...其他 token
  tooltipMarker: 'circle'   // 让 markerHtml 强制返回圆形
};
var THEME_BASE = {
  tooltipMarker: null       // 按 series 类型走
};
```

**易错点**：

1. **不要直接拼 `p.marker`**——它是 ECharts 渲染的固定圆点 SVG，没法改形状。
2. **折柱混合的 tooltip 必须按 `p.seriesType` 区分**：柱行用方块，线行用胶囊。直接全用圆点违反 SKILL 规范。
3. **主题切换钩子**：themeMarker 写到主题对象上，formatter 闭包持有 currentTheme 引用，切主题后 `chart.setOption({tooltip:{formatter:...}})` 重设。否则 tooltip 还是旧主题形状。
4. **不要硬编码 12×12**——tooltip.md 旧版写的 12×12 已废止，标记尺寸跟 legend 走（6×6 / 8×2）。tooltip 内行高靠 padding / line-height，不靠 marker 撑高。
5. **dataItem 行不要混色**：marker 颜色 = `p.color`（series 配色），不要用 visualization-09 等中性色。
6. **系列名换行须顶对齐第一行**：行容器 `align-items:flex-start` + 数值 span `white-space:nowrap;flex-shrink:0` + 系列名 span `min-width:0`。漏 `align-items` → 数值随多行块**垂直居中**、偏离第一行；漏 `min-width:0` → 长系列名在 flex 里**不换行反而撑破** `max-width:160px`（flex item 默认 `min-width:auto` 不收缩）。库无关规则见 [tooltip.md § 系列名过长换行对齐](components/tooltip.md#系列名过长换行对齐)。

---

### 陷阱 16：Legend 换行时行间距压不到规范 4px

**症状**：图例项多触发换行（折柱混合 / 多系列折线），换行后两行之间间距约 8-10px，不是规范要求的 4px。设 `textStyle.lineHeight` 反而更糟（变 32px）。

**根因**：ECharts 内置 legend 行 stride 公式：
```
stride ≈ max(top-level itemHeight, max(per-data itemHeight), 文字渲染高) + itemHeight + ~1
```
- 没有 `rowGap` 或 `lineGap` 公开配置
- `textStyle.lineHeight` 不控制行 stride，反而被叠加进 text BBox 高度
- 含 6×6 方块 icon（柱图例）的 stride 比纯 8×2 胶囊（折线图例）多 4px——这就是为什么同一份 base 规范在 Multi-Line 上能命中 4px，在 Bar-Line Combo 上必然多 4px

**实测对比**（fontSize 12，textH ~17）：

| 图表 | 顶层 itemHeight | per-data itemHeight | stride | 行间距 |
| --- | --- | --- | --- | --- |
| Multi-Line | 2 (line iconType) | 无 | 20 | 4px ✅ |
| Bar-Line Combo | 6 (默认 markerSize) | 6 (bar) / 2 (line) | 24 | 8px ❌ |

**修法（SVG 后处理，60 行 helper，渲染后压紧）**：

```js
function tightenLegendRows(chart, targetGap){
  targetGap = targetGap == null ? 4 : targetGap;
  function patch(){
    var svg = chart.getDom().querySelector('svg');
    if(!svg) return;
    var opt = chart.getOption();
    var legendCfg = opt.legend && opt.legend[0];
    if(!legendCfg) return;
    var names = (legendCfg.data && legendCfg.data.length
      ? legendCfg.data : (opt.series||[]).map(s=>s.name).filter(Boolean))
      .map(d=>typeof d==='string'?d:d.name).filter(Boolean);

    // 1) 复位：把之前 patch 加的 translateY 还原（保留 ECharts 自己的 transform）
    svg.querySelectorAll('[data-legend-tighten]').forEach(el=>{
      var orig = el.getAttribute('data-orig-transform');
      if(orig==null||orig==='') el.removeAttribute('transform');
      else el.setAttribute('transform', orig);
      el.removeAttribute('data-legend-tighten');
    });

    // 2) 按当前 Y 坐标对 legend text 分行
    var textsByRow = {};
    svg.querySelectorAll('text').forEach(t=>{
      var n = (t.textContent||'').trim();
      if(names.indexOf(n)<0) return;
      var r = t.getBoundingClientRect();
      (textsByRow[Math.round(r.top)] = textsByRow[Math.round(r.top)]||[]).push({el:t, rect:r});
    });
    var rowYs = Object.keys(textsByRow).map(Number).sort((a,b)=>a-b);
    if(rowYs.length<2) return;

    // 3) 按 Y 中心距离把 icon path 关联到所属行
    var rowCenters = rowYs.map(y=>y + textsByRow[y][0].rect.height/2);
    var iconMinY=rowYs[0]-12, iconMaxY=rowYs[rowYs.length-1]+24;
    var pathsByRow = {};
    rowYs.forEach(y=>pathsByRow[y]=[]);
    svg.querySelectorAll('path').forEach(p=>{
      var r = p.getBoundingClientRect();
      if(r.width===0||r.height===0||r.width>14||r.height>14) return;
      var pc=(r.top+r.bottom)/2;
      if(pc<iconMinY||pc>iconMaxY) return;
      var bestIdx=-1, bestDist=Infinity;
      rowCenters.forEach((rc,idx)=>{ var d=Math.abs(pc-rc); if(d<bestDist){bestDist=d;bestIdx=idx;}});
      if(bestIdx>=0 && bestDist<10) pathsByRow[rowYs[bestIdx]].push(p);
    });

    // 4) 计算 delta 并叠加 translate(0,-delta×rowIdx)
    var textH = textsByRow[rowYs[0]][0].rect.height;
    var currentStride = rowYs[1] - rowYs[0];
    var targetStride = Math.round(textH + targetGap);
    var delta = currentStride - targetStride;
    if(delta<=0) return;

    rowYs.forEach((y, idx)=>{
      if(idx===0) return;
      var dy = -delta*idx;
      function shift(el){
        var orig = el.getAttribute('transform')||'';
        el.setAttribute('data-orig-transform', orig);
        el.setAttribute('transform', 'translate(0 '+dy+') ' + orig);
        el.setAttribute('data-legend-tighten','1');
      }
      textsByRow[y].forEach(o=>shift(o.el));
      pathsByRow[y].forEach(p=>shift(p));
    });
  }
  chart.off('finished', patch);
  chart.on('finished', patch);
  setTimeout(patch, 50);  // fallback：finished 可能在 setOption 同步阶段就已经触发完
}

// 用法：每次 chart.setOption() 之后调一次
chart.setOption(option);
tightenLegendRows(chart, 4);
```

**易错点**：

1. **复位时不能用 `removeAttribute('transform')`**——ECharts 用 `transform` 做 legend item 的横向布局和图标垂直对齐。必须把 ECharts 原 transform 存到 `data-orig-transform`，叠加 translate(0,dy) 而不是覆盖。
2. **SVG 是扁平结构**——ZRender SVG 渲染器把所有 legend text / path 放在同一个 `<g>` 下，没有 per-item 分组。识别 icon 属于哪一行靠 **Y 中心距离匹配**最近的 text 行。
3. **必须 SVG 渲染器**（`{renderer:'svg'}`）——Canvas 渲染器没有 DOM，无法后处理。生产端如必须用 Canvas，则只能放弃 4px 改用 HTML 自绘 legend。
4. **resize / 主题切换都要重跑**——helper 内已 off + on 监听 `finished`，且加 setTimeout fallback 兜底 finished 错过的情况。
5. **不能依赖 `params.marker`**（陷阱 15 同源）——同理，legend 内部 DOM 结构是私有实现，本陷阱依赖 SVG 输出格式，ECharts 升级时需复检。

---

### 陷阱 17：水印 / logo / icon 资源被按参数手画成占位符

**症状**：spec 说水印 "96×20 / 5% 黑 / Ainvest logo"（或类似 brand asset），落地后画面上看到一个灰矩形 + "Ainvest" 字样，跟设计稿完全不像。

**根因**：把 spec 表里的「尺寸 / 颜色 / 名称」当成了**绘制依据**，用 `graphic.rect + graphic.text` 自行拼出来，**根本没去加载** `assets/examples/_shared/*.svg` 真实资源。真实 SVG 通常包含多条 `<path>`（字形 / logo 路径），任何参数表都不可能等价于"加载这个文件"。

参见 [SKILL.md § 全局规则 5：资源文件优先于参数描述](../SKILL.md#5-资源文件优先于参数描述asset-over-params) 与 [components/watermark.md § 资源协议](components/watermark.md)。

**修法**：用 `graphic.image` 加载 SVG 文件——

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

**易错点**：

1. **`graphic` 必须用 `{ elements: [...] }` 包裹形式 + `replaceMerge`**——不要直接 `graphic: [...]`。二次 `setOption` 合并时 ECharts 默认按 index 合并 graphic，新元素可能被吃掉或错位。`replaceMerge: ['graphic']` 显式声明用整体替换语义。
2. **`file://` 协议下 SVG 加载可能失败**——本地开发用 `python -m http.server` 起 HTTP 服务，或改用 Data URI（`'data:image/svg+xml;utf8,' + encodeURIComponent(svgText)`）。
3. **`width` / `height` 必须显式写**——`graphic.image` 不会从 SVG 内 viewBox 自动取尺寸；不写会按 0×0 渲染（即"不显示"）。
4. **5% 黑在白底上视觉本来就弱**——这是 spec 故意的"不遮挡数据"设计；不要因为"看不见"就加深透明度，那会破坏数据观感。需要更显眼时可临时在 dev 模式叠一个 1px 边框确认位置，**生产环境必须按 spec 透明度**。
5. **同理适用于其他 asset**：tooltip 指示器图、品牌 logo、icon 资源、自定义 marker 等只要 spec 引用了 `.svg/.png/.webp` 文件，就直接加载，不要按参数重画。

---

### 陷阱 18：Ainvest tooltip 只做了背景色，三角 / 锚定 / 边缘 clamping 全跳过

> 📌 本陷阱 = [tooltip.md § 位置 B 定位算法（库无关）](components/tooltip.md#位置-b-定位算法库无关) 的 **ECharts 落地版**。库无关的 4 步公式 / 两条不变量 / 6 条禁忌 / 自检清单看那里；本节是把它落进 ECharts `tooltip.position` 回调的具体代码。Ainvest 取值（`#383838` / 三角高 6 等）见 [ainvest.md §1.6](themes/ainvest.md#16-tooltip-形态端通用--仅折线柱状)。

**症状**：spec 说 Ainvest tooltip "带下三角 + 锚定 grid 上方 + 边缘自适应"，落地后 hover 折线 / 柱状图看到的是普通深色 tooltip 跟着光标飘——背景色对了，三角没有、位置没锚定、贴边时也没有"三角仍指向光标"的细节。

**适用范围**：本陷阱及修法**仅对折线 / 柱状图族成立**——其他图表（饼 / 环 / 雷达 / 散点 / treemap / sankey 等）回退到 base tooltip 默认行为，**不要套用本修法**。完整白名单见 [ainvest.md § 1.6 适用范围](themes/ainvest.md#16-tooltip-形态端通用--仅折线柱状)。

**根因**：

1. **`extraCssText` 加不了伪元素**——`::before/::after` 必须通过 `<style>` 标签注入到 `<head>`，不能 inline。想加三角只能在 `formatter` 返回的 HTML 里塞 `<span>`，靠 CSS border 三角技巧渲染。
2. **ECharts 默认 `tooltip.position` 是"避让光标偏右上"**——不会锚定到 grid 任何位置。想锚到"grid 上方外侧"必须自己实现 `position` 回调，调 `chart.convertToPixel({gridIndex:0}, [0, yMax])` 拿 grid 顶部 y 像素。
3. **`confine: true` 与自定义 position 冲突**——它只把 tooltip 整体推回画布内，但三角还在 tooltip 中央，违反"贴边时三角仍指向光标"。clamping 必须在 position 回调里手算。

**修法**（最小可工作版本，每个折线 / 柱状图实例都要调一次）：

```js
function ainvestLineBarTooltip(chart) {
  const TRI_H = 6;   // 三角高
  const GAP   = 4;   // 三角尖距 grid 顶端
  return {
    trigger: 'axis',
    backgroundColor: '#383838',
    borderWidth: 0,
    padding: [8, 10, 8, 10],
    hideDelay: 0,
    transitionDuration: 0,
    textStyle: { color: 'rgba(255,255,255,.84)', fontSize: 12, fontFamily: FONT_FAMILY_EN /* Ainvest 字体 = font-family-en token */ },
    extraCssText: 'border-radius:4px;box-shadow:0 2px 6px rgba(0,0,0,.18);',
    // ⚠️ 不要加 confine: true ——会与 position 冲突
    position(pt, params, dom, rect, size) {
      const chartW = chart.getWidth();
      const yOpt   = chart.getOption().yAxis;
      const yMax   = (Array.isArray(yOpt) ? yOpt[0] : yOpt).max;
      const gridTop = chart.convertToPixel({ gridIndex: 0 }, [0, yMax])[1];
      const [tW, tH] = size.contentSize;

      // 1) 垂直：锚定 grid 上方外侧
      const y = Math.max(0, gridTop - tH - TRI_H - GAP);

      // 2) 水平：光标居中 + 边缘 clamping
      let x = pt[0] - tW / 2;
      x = Math.max(0, Math.min(x, chartW - tW));

      // 3) 三角偏移：贴边时让三角仍指向原光标 x
      //    把"光标相对 tooltip 左边"的偏移记到 dom 上，formatter 后用 RAF 同步到 .av-tip-arrow
      dom.dataset.arrowX = String(pt[0] - x);

      return [x, y];
    },
    formatter(params) {
      const rows = params.map(p => `
        <div style="display:flex;justify-content:space-between;line-height:18px">
          <span>
            <i style="display:inline-block;width:10px;height:10px;border-radius:50%;background:${p.color};margin-right:6px;vertical-align:middle"></i>
            ${p.seriesName}
          </span>
          <span style="margin-left:24px;font-weight:500">${p.value}</span>
        </div>`).join('');
      return `
        <div style="position:relative">
          <div style="color:#fff;margin-bottom:4px">${params[0].axisValueLabel ?? params[0].axisValue}</div>
          ${rows}
          <span class="av-tip-arrow" style="
            position:absolute;bottom:-${TRI_H}px;left:0;
            width:0;height:0;
            border-left:6px solid transparent;
            border-right:6px solid transparent;
            border-top:${TRI_H}px solid #383838;
            transform:translateX(-6px);
          "></span>
        </div>`;
    }
  };
}

// 把 dom.dataset.arrowX 同步到 .av-tip-arrow 的 left
chart.on('showTip', () => {
  requestAnimationFrame(() => {
    const root = chart.getDom();
    const arrow = root.querySelector('.av-tip-arrow');
    // ECharts tooltip 容器是 root.firstChild 之后的某个 absolute div；用 closest 找
    const tipDom = arrow?.closest('div[style*="position: absolute"]');
    const arrowX = tipDom?.dataset?.arrowX;
    if (arrow && arrowX != null) {
      arrow.style.left = (Number(arrowX) - 6) + 'px';  // -6 = 半个三角底宽
    }
  });
});
```

**反例**（⚠️ 已踩过的坑）：

```js
// ❌ 只改背景色 + hideDelay 就交差——三角、position、clamping 全跳过
{
  trigger: 'axis',
  backgroundColor: '#383838',
  hideDelay: 0,
  extraCssText: 'border-radius:4px;'   // 三角呢？position 呢？
}

// ❌ 想用 extraCssText 加伪元素 —— 无效，inline style 不支持 ::after
{
  extraCssText: '...&::after{content:"";border-top:6px solid #383838;...}'
}

// ❌ 用 confine:true 当 clamping —— 与自定义 position 冲突，且三角不偏移
{
  confine: true,
  position(pt) { /* ... */ }
}
```

**易错点**：

1. **伪元素无效**——三角只能在 `formatter` 返回的 HTML 里塞 `<span>`，靠 CSS border 技巧（`border-left/right transparent + border-top 实色`）
2. **`confine: true` 必须禁用**——与自定义 position 互斥；clamping 在 position 回调里手算
3. **三角偏移是关键**——贴边时 tooltip 向内收，但三角必须停留在"原光标 x"对应的位置；通过 `dom.dataset.arrowX` 记录、`showTip` 事件 + RAF 同步到 DOM
4. **`chart.convertToPixel('grid', ...)` 在首次 setOption 完成前调会报错**——`position` 回调只在 tooltip 显示时触发，那时 chart 已渲染，安全；但**不要在 chart 初始化时直接调**
5. **不要对无 grid 图表套此 tooltip**——pie / radar / treemap / sankey 等 `convertToPixel({gridIndex:0}, ...)` 返回 `undefined`，整个 position 回调崩；这类图表用 base 默认 tooltip

---

### 陷阱 19：形式 A 数据内缩——`boundaryGap: ['10%','10%']` 在 category 轴上不生效

**症状**：按 [layout.md § Y 轴标签布局 § 形式 A](components/layout.md#y-轴标签布局) 写 `xAxis.boundaryGap: ['10%', '10%']`，柱 / 折线**完全没内缩**，最右柱仍贴 grid 边、与内嵌 Y 标签重叠。

**根因**：ECharts `xAxis.boundaryGap` 接受形态按轴类型分：

| `xAxis.type` | 接受值 | 数组 `['a%','b%']` |
| --- | --- | --- |
| `'category'` | `true` / `false` | **静默忽略** |
| `'value'` / `'time'` | `true` / `false` / `[左, 右]` | 生效 |

**修法**：按轴类型分别下发——

```js
// value / time 轴（scatter / beeswarm 等）：数组形式直接生效
xAxis: { type: 'value', boundaryGap: ['10%', '10%'] }

// category 轴（形式 A 适用的 12 个图表）：boundaryGap: true 半 band 缓冲（仅 N ≤ 5 满足 10%）
xAxis: { type: 'category', data: [...], boundaryGap: true }

// category 轴 N ≥ 6：在 true 基础上首尾补空类目（'' + null）加大内缩
xAxis: { type: 'category', data: ['', 'Q1', 'Q2', 'Q3', 'Q4', ''], boundaryGap: true },
series: [{ data: [null, 120, 180, 95, 220, null] }]
```

**关于 category 轴近似精度**：`boundaryGap: true` 给每侧 `1/(2N)` 内缩——

| N（category 数） | 实际内缩 | 与 10% 偏差 |
| --- | --- | --- |
| 4 | 12.5% | 偏大，视觉良好 |
| 5 | 10.0% | 正中 |
| 6 | ~8.3% | 偏小（开始需要补空类目） |
| 10 | 5.0% | 不足一半，必须补空类目 |

默认内缩 **10%**（2026-06-12 由 5% 上调，规避形式 A 内嵌 Y 标签被数据图形遮挡）——`boundaryGap: true` 仅 **N ≤ 5** 达标；N ≥ 6 首尾补空类目（见易错点 3），每侧实际内缩 = `(0.5 + k) / (N + 2k)`（k = 每侧空类目数）：k=1 覆盖 N ≤ 13、k=2 覆盖 N ≤ 21。

**反例**：

```js
// ❌ category 轴写数组形式 —— 静默失效
xAxis: { type: 'category', data: [...], boundaryGap: ['10%', '10%'] }

// ❌ 用 grid.left/right 加大代替 boundaryGap —— 整个 grid 内移，Y 轴跟着挪，变成形式 B
grid: { left: 40, right: 40 }
```

**易错点**：

1. **折线类（line / area 等默认 `boundaryGap: false`）**：要把 `boundaryGap` 显式改回 `true`，否则首尾数据点仍贴 grid 边
2. **`barCategoryGap` 不能代替**：它只动相邻 band 之间间距，**第一 / 最后 band 仍贴 grid 边**
3. **N ≥ 6**：`true` 给的内缩 < 10%，**首尾补空类目**（categories 两端各加 `''`、series 数据补 `null`；空类目无标签无图形，保持形式 A 不退化）；调大 `grid.right` / `axisLabel.padding` 外推是最后手段（等效退化为形式 B）
4. **stacked / normalized / bar-line-combo / 多系列**：都吃 `boundaryGap: true` + 补空类目方案，**各系列数据都要同步补 `null`**（漏补的系列会整体错位一格）
5. **scatter / beeswarm 用 value 轴**：直接 `boundaryGap: ['10%', '10%']`，不要套 category 方案

---

### 陷阱 20：Legend 圆形 marker 被 series.itemStyle 污染（空心 / 撑大 / 形状错）

**症状**（三类同源 —— 同一条继承链上的三个表型）：

| 症状 | 触发条件 | 视觉表现 |
| --- | --- | --- |
| A. **空心圆** | series.itemStyle.color 设为白色（误把"hover 切白"写到默认态） | Legend 圆变成白底 + 颜色描边，看起来像甜甜圈 |
| B. **尺寸撑大** | series.itemStyle.borderWidth 设了非 0 值（如折线数据点正确的 2px 描边） | `itemWidth/Height: 10` 但视觉直径 12px（描边一半外溢） |
| C. **形状错乱** | 同源的 tooltip 同步问题 | 详见 [陷阱 15](#陷阱-15tooltip-内的图例标记与图表-legend-不一致--主题切换不跟随) |

**根因**：`legend.icon` 为闭合形状（`circle` / `rect` / `roundRect`）时，legend 图标**继承整个 `series.itemStyle`**——不只是 `color`，还包括 `borderColor`、`borderWidth`、`opacity`、`shadowBlur` 等**所有视觉属性**。`itemWidth/Height` 设的是**几何尺寸**，描边是额外加在外圈的——SVG `stroke-alignment: center` 把一半描边推到外侧，于是 10×10 + 2px 描边 = 视觉 12×12。

**触发场景图**（以折线图为例，3 种 itemStyle 写法的 legend 视觉结果）：

| series.itemStyle 写法 | legend icon 渲染 | 视觉 |
| --- | --- | --- |
| `{ color: '#265FFC' }`（柱图常见） | fill `#265FFC`、无描边 | ✅ 实心圆 10×10 |
| `{ color: '#265FFC', borderColor: '#265FFC', borderWidth: 2 }`（折线 spec 正确写法） | fill `#265FFC` + 2px 同色描边 | ⚠️ **视觉 12×12**（撑大） |
| `{ color: '#fff', borderColor: '#265FFC', borderWidth: 2 }`（误把 hover 切白写默认） | fill 白 + 2px 蓝描边 | ❌ **空心圆 12×12**（既空心又撑大） |

**修法**：在 `legend.itemStyle` 顶层显式 override，斩断从 series 的继承。

```js
legend: {
  itemWidth: 10, itemHeight: 10, icon: 'circle',
  itemStyle: {
    borderWidth: 0    // ⚠️ 关键：强制清掉从 series 继承的描边宽度
    // borderColor 不用另设——borderWidth 0 时不渲染描边
  },
  textStyle: { fontSize: 12, fontFamily: FONT_FAMILY_EN /* Ainvest 字体 = font-family-en token */ }
}
```

折柱混合图例需要分别设：

```js
legend: {
  data: [
    { name: '营收', icon: 'roundRect', itemWidth: 6, itemHeight: 6,
      itemStyle: { borderWidth: 0 } },     // 柱图 legend
    { name: '同比', icon: 'circle', itemWidth: 10, itemHeight: 10,
      itemStyle: { borderWidth: 0 } }      // 折线 legend，关键防撑大
  ]
}
```

**易错点**：

1. **不要在 series 上修补**——series.itemStyle 的 borderWidth 是折线数据点 spec 的硬约束（ainvest 2px、base 1.5px），改了会让数据点失去描边。**legend 的修正必须在 legend 层**，不能动 series。
2. **`legend.itemStyle.borderWidth: 0` 不影响数据点**——它只作用于 legend 内部 icon 绘制，series.itemStyle 仍完整应用于数据点本身。
3. **图标为 line / path 时不受影响**——`icon: 'path://...'`（折线 8×2 短横线胶囊）有自己的渲染路径，不会被 series.itemStyle 撑大。这条陷阱专门针对**闭合形状（circle/rect/roundRect）**。
4. **同源陷阱链**——这条 + [陷阱 8](#陷阱-8折线数据点-hover-时描边变白)（默认 vs hover 状态分离）+ [陷阱 15](#陷阱-15tooltip-内的图例标记与图表-legend-不一致--主题切换不跟随)（tooltip 同步）三者全是同一条 `itemStyle` 传播链上的不同症状——**任何一个 series.itemStyle 改动都要全链路 check**（数据点 / legend / tooltip 三处都要测）。
5. **`legend.itemStyle.borderWidth: 0` vs 不写**：ECharts 默认 legend 不显式覆盖 itemStyle，所以默认就吃 series 的继承——**必须显式设 0** 才能斩断。

**与已有陷阱的分工**：

| 陷阱 | 解决的问题 |
| --- | --- |
| [陷阱 7](#陷阱-7图例标记尺寸--8默认而非-6) | legend 自己的 `itemWidth/Height` 设错 |
| [陷阱 12](#陷阱-12图例标记形状全部一样没有区分柱线圆) | 不同 series 类型 icon 形状区分 |
| [陷阱 15](#陷阱-15tooltip-内的图例标记与图表-legend-不一致--主题切换不跟随) | tooltip marker 不跟 legend 同步 |
| **陷阱 20（本条）** | **series.itemStyle 反向污染 legend icon 视觉**——空心 / 撑大 / 形状错的根因 |

前 3 条解决"legend 自己 config 写错"；本条解决"legend config 写对了、被 series 污染了"。

---

### 陷阱 21：形式 A 顶部 Y 轴标签溢出 grid 顶（verticalAlign 全局一刀切）

**症状**：形式 A 下，Y 轴最大值标签（最顶那条）跑到最顶网格线**上方**、探出 grid（甚至顶进 legend / 单位行）；其余标签正常。

**根因**：`yAxis.axisLabel.verticalAlign` 是**全局**设置、无法 per-label。但形式 A 要求「顶标签往下挂、其余标签往上顶」两个**相反**方向（见 [layout.md § 形式 A](components/layout.md#y-轴标签布局)）：

| `verticalAlign` | 顶标签 | 中间 / 0 / 最小标签 | 结果 |
| --- | --- | --- | --- |
| `'bottom'` | 往上顶 → **溢出 grid 顶** | 往上顶 ✅ | 顶标签错（本症状） |
| `'top'` | 往下挂 ✅ | 往下挂 → 落到线**下方**（侧别错） | 中间标签错 |
| `'middle'`（默认） | 上半截出界 | 底标签下半截出界 | 两端都错 |

任何单一取值都不满足；`verticalAlign` 又不支持 callback，所以必须 per-label 落地。

**修法 A（推荐，原生 ECharts）：主轴 + 顶标签辅助轴叠加。** 主轴渲染「除最大值外」的标签、`verticalAlign:'bottom'`；再叠一条**只渲染最大值标签**、`verticalAlign:'top'` 的辅助 value 轴（共享 `min/max/interval/position`，关掉 splitLine/axisLine/axisTick、不重复 name）。两轴同 position + offset 0 像素重合，标签落在同一 x、不同 y，互不遮挡。

```js
// 把一个 value 轴配置拆成 [主轴, 顶标签轴]
function insideYAxis(cfg) {                  // cfg 含 type:'value' / min / max / interval / position / axisLabel...
  var max = cfg.max;
  function make(isTop) {
    var ax = Object.assign({}, cfg);
    ax.axisLabel = Object.assign({}, cfg.axisLabel, {
      inside: true,
      verticalAlign: isTop ? 'top' : 'bottom',      // 顶标签下挂 / 其余上顶
      formatter: function (v) {
        var isMax = Math.abs(v - max) < 1e-6;
        return (isTop ? isMax : !isMax) ? v : '';   // 顶轴只留 max，主轴去掉 max
      }
    });
    if (isTop) { ax.splitLine = { show: false }; ax.axisLine = { show: false };
                 ax.axisTick = { show: false }; ax.name = undefined; }
    return ax;
  }
  return [make(false), make(true)];           // [主轴, 顶标签轴]
}

// 单 Y 轴：series 仍 yAxisIndex 0（主轴），顶标签轴是 index 1（无 series）
option.yAxis = insideYAxis(yCfg);

// 双 Y 轴（折柱组合）：主轴在前、顶标签轴在后，series.yAxisIndex 不变
var L = insideYAxis(leftCfg), R = insideYAxis(rightCfg);
option.yAxis = [L[0], R[0], L[1], R[1]];     // bar→yAxisIndex 0(L主), line→1(R主)
```

**修法 B（控制力最强）**：`axisLabel.show:false`，用 `graphic` / 自定义 series 自绘每条标签、逐条按规则设 verticalAlign。适合 paradigm-chart 这类已大量定制的实现。

**易错点**：

1. 顶标签轴**必须关 splitLine/axisLine/axisTick**，否则重复画网格线 / 多一条轴线。
2. 两轴 `min/max/interval/position` **必须完全一致**，否则像素错位、顶标签与主轴标签不对齐。
3. 浮点比较用 `Math.abs(v-max) < 1e-6`，不要 `v === max`。
4. **双 Y 轴顺序**：主轴在前（index 0/1）、顶标签轴在后（index 2/3），保持 `series.yAxisIndex` 指向主轴。
5. 形式 B **无此问题**（标签在 grid 外、与网格线居中对齐，单一 `verticalAlign:'middle'` 即可）。

---

## 三、文档定位

- **库无关原则不变**：本文档**不是**规范的一部分；规范保持库无关，落地实现可选库。
- 用 AntV G2 / D3 / Recharts 等其他库时，开发者可类比本文档的「设计意图」到对应库的字段。
- 本文档**优先记录高频踩坑**——按反馈持续补充，而非穷举所有 option 字段。
