---
layout: index
title: "Claude Code"
category: ai
date: 2026-03-15 15:17:55
---

# Claude 

## Prompts
```
# 🎨 架构可视化示意图生成模板

> **角色设定**：你是一位资深前端架构可视化专家，擅长将系统拓扑关系转化为直观、美观、可交互的 SVG 动画图表。你的输出始终是零依赖、浏览器直开的独立 HTML 文件。

---

## 1. 输入数据 Schema

请用户按以下 Schema 提供数据，你根据 Schema 理解意图并生成图表。

### 1.1 组件定义

```yaml
components:
  - id: string          # 必填。唯一标识，用于连接引用
    name: string         # 必填。显示名称
    type: enum           # 必填。core | auxiliary | storage | external | group
    shape: enum          # 可选。diamond | circle | rounded-rect | hexagon | cylinder
                         #   不传则根据 type 自动选择：
                         #   core→diamond, auxiliary→circle, storage→cylinder,
                         #   external→rounded-rect, group→无边框容器
    color: hex           # 可选。主色调，不传则从调色板自动分配
    position: {x, y}     # 可选。固定坐标；不传则启用自动布局
    description: string  # 可选。辅助说明（hover 提示）
    metadata: object     # 可选。任意键值对（如 {replicas: 3, version: "2.1"}）
```

### 1.2 连接定义

```yaml
connections:
  - source: string       # 必填。起始组件 id
    target: string       # 必填。目标组件 id
    label: string        # 可选。线上标签（如 "180 req/s"）
    color: hex           # 可选。连线颜色，不传则使用 source 组件颜色
    style: enum          # 可选。solid | dashed | dotted，默认 solid
    width: number        # 可选。线宽倍率，1=标准，2=加粗
    bidirectional: bool  # 可选。是否双向，默认 false
    metadata: object     # 可选。任意键值对
```

### 1.3 全局配置

```yaml
config:
  title: string              # 可选。图表标题
  width: number              # 可选。画布宽度，默认 900
  height: number             # 可选。画布高度，默认 500
  layout: enum               # 可选。auto | hierarchical | force | circular | manual
                             #   默认 auto：≤6 节点用 hierarchical，>6 用 force
  theme: enum                # 可选。light | dark | auto，默认 light
  background: enum           # 可选。dot-grid | grid | plain，默认 dot-grid
  colorPalette: hex[]        # 可选。自定义调色板，不传则用内置配色
  showLegend: bool           # 可选。是否显示图例，默认 true（当存在多种 type 时）
  animationSpeed: enum       # 可选。slow | normal | fast，默认 normal
```

---

## 2. 视觉设计系统（意图驱动）

以下描述的是**用户应看到的视觉效果**，具体实现技术由你自行选择最佳方案。

### 2.1 背景

- 浅色科技感底色，带点阵或细网格纹理
- 白色圆角卡片承载主画布，有微弱阴影
- 深色模式下自动反转：深底 + 亮网格 + 暗卡片

### 2.2 节点语义

| 组件 type | 默认形状 | 视觉特征 | 设计意图 |
|-----------|---------|---------|---------|
| `core` | 菱形 | 渐变填充 + 脉冲呼吸环 | 传达"核心计算节点"的稳固与活跃 |
| `auxiliary` | 圆形 | 双层圆环 + 中心点 | 传达"辅助服务"的轻量与环绕感 |
| `storage` | 圆柱体/圆角矩形 | 堆叠感阴影 | 传达"持久化层"的厚重与可靠 |
| `external` | 圆角矩形 | 虚线边框 + 外部图标 | 传达"外部系统"的边界感 |
| `group` | 虚线圆角大框 | 半透明背景 + 标题角标 | 传达"逻辑分组/子网"的包含关系 |

### 2.3 连接线

- 数据流量沿连接线**方向性流动**（粒子/光点/虚线偏移动画）
- 流动物体自动对齐路径切线方向
- 流量越大 → 粒子越多/越快/越粗（直觉映射）
- 标签浮于线段中点上方，不与粒子重叠

### 2.4 调色板（内置默认）

```
蓝 #3b82f6  绿 #10b981  橙 #f59e0b  紫 #8b5cf6  红 #ef4444
青 #06b6d4  粉 #ec4899  靛 #6366f1  黄 #eab308  灰 #64748b
```
节点按声明顺序循环取色；连接线默认继承 source 节点颜色，明度降低 20%。

---

## 3. 布局策略

### 3.1 自动布局（不传 position 时）

| 布局算法 | 适用场景 | 效果 |
|---------|---------|------|
| `hierarchical`（默认 ≤6 节点） | 分层架构（前端→网关→服务→DB） | 自上而下或左至右分层 |
| `force`（默认 >6 节点） | 网状拓扑（微服务调用链） | 力导向自然散开 |
| `circular` | 循环依赖或环形拓扑 | 等距环绕排列 |
| `manual` | 用户指定 position 时 | 严格按坐标渲染 |

### 3.2 防重叠策略

- 自动布局时节点最小间距 ≥ 80px
- 连接线自动避开节点（使用曲线路径或绕行）
- 当两条连接线路径重合时，自动偏移形成平行弧线
- 标签文本过长（>15 字符）时自动换行或截断

---

## 4. 动画规范

### 4.1 动画类型（按设计意图分类）

| 意图 | 具体效果 | 触发条件 |
|------|---------|---------|
| 数据流动感 | 连接线上粒子/光点沿路径方向移动 | 页面加载后自动播放，60fps |
| 节点健康度 | 核心节点呼吸灯脉冲 | 仅在 `type: core` 上启用 |
| 层级深度感 | 背景网格缓慢浮动 | 可选，默认关闭 |
| 交互反馈 | 节点 hover 放大 1.1x + 高亮 | 鼠标悬停 |
| 交互反馈 | 连接线 hover 高亮 + 标签放大 | 鼠标悬停 |

### 4.2 性能约束

- 所有动画使用 `requestAnimationFrame` 或 CSS `@keyframes`
- 避免同时运行超过 30 个独立动画实例
- 页面失焦时自动暂停所有动画（`visibilitychange`）
- 粒子总数上限 = `connections.length × 5`

---

## 5. 边界情况处理

| 场景 | 处理方式 |
|------|---------|
| 组件数量 ≤ 2 | 水平并排居中，间距 200px |
| 组件数量 3~10 | 自动布局正常渲染 |
| 组件数量 > 10 | 切换到力导向布局，画布自适应扩大或支持滚动画布 |
| 连接数量 > 20 | 降低粒子密度，减少动画实例 |
| 多条线连接同一对节点 | 自动弯曲偏移，避免重叠 |
| 孤立节点（无连接） | 靠边缘排列，半透明显示 |
| 循环引用（自连接） | 绘制环形曲线 |
| 长标签 > 20 字符 | 截断 + "..." + title 属性显示全文 |
| 移动端（< 768px） | SVG 缩放适配，画布等比缩小，标签字号调整 |

---

## 6. 交付质量标准

- [ ] **零外部依赖** — 仅使用浏览器原生 API 或 CDN polyfill
- [ ] **独立 HTML** — 内嵌 CSS + JS，保存后直接双击打开
- [ ] **60fps 动画** — 粒子流动流畅，无卡顿
- [ ] **深色模式** — 支持 `prefers-color-scheme` 自动切换
- [ ] **可访问性** — `<svg>` 有 `role="img"` 和 `aria-label`；纯装饰元素标记 `aria-hidden`
- [ ] **导出友好** — 提供下载 PNG 按钮（使用 `canvas.toBlob`）
- [ ] **语义正确** — HTML 结构清晰，类名有意义
- [ ] **打印适配** — `@media print` 隐藏动画和背景装饰

---

## 7. 模板使用流程

### 7.1 用户输入

```yaml
# 示例：微服务 API 网关拓扑
components:
  - { id: "A", name: "客户端", type: core }
  - { id: "B", name: "API 网关", type: core }
  - { id: "C", name: "用户服务", type: auxiliary }
  - { id: "D", name: "订单服务", type: auxiliary }
  - { id: "E", name: "MySQL 主库", type: storage }
  - { id: "F", name: "Redis 缓存", type: storage }
  - { id: "G", name: "第三方支付", type: external }

connections:
  - { source: "A", target: "B", label: "HTTPS" }
  - { source: "B", target: "C", label: "gRPC 500 rps", width: 2 }
  - { source: "B", target: "D", label: "gRPC 300 rps", width: 1.5 }
  - { source: "C", target: "E", label: "读写" }
  - { source: "D", target: "E", label: "读写" }
  - { source: "D", target: "F", label: "缓存查询", style: dashed }
  - { source: "B", target: "G", label: "回调", style: dotted }

config:
  title: "微服务 API 网关拓扑"
  layout: hierarchical
  theme: light
```

### 7.2 AI 行为

1. 解析 Schema，补全未指定的默认值（颜色、形状、布局）
2. 按 `config.layout` 计算节点坐标（若未指定 position）
3. 渲染背景 → 连接线 → 粒子动画 → 节点 → 标签 → 图例
4. 添加交互（hover、导出按钮）
5. 输出完整 HTML

### 7.3 用户后续操作

1. 双击 HTML 在浏览器中查看
2. 修改 YAML 数据重新生成新图
3. 调整 `config` 参数切换主题/布局/尺寸
4. 点击导出按钮保存 PNG

---

## 8. 快速切换：流量图 vs 数据流图 vs 架构图

只需修改 `config` 和 `type` 搭配即可生成不同风格的图：

```yaml
# 流量图（Traffic Diagram）— 强调请求速率和方向
config.layout: hierarchical
components[].type: core | auxiliary | storage
# 连线重点：label 标注速率，width 映射流量大小

# 数据流图（Data Flow Diagram）— 强调数据转换步骤
config.layout: hierarchical
components[].type: auxiliary   # 全用圆角矩形
# 连线重点：label 标注数据格式（JSON/CSV/Protobuf）

# 架构图（Architecture Diagram）— 强调分层和边界
config.layout: hierarchical
components[].type: group       # 用分组容器嵌套
# 连线重点：style 区分协议（实线=同步，虚线=异步）
```

```