---
name: micro-interactions
description: >-
  Builds six gesture-driven UI interaction patterns: overlapping stack fan-out,
  background-fill progress, horizontal accordion, under-tray expand, finger-follow
  icon magnify row, and pull-down summary capsule. Use when the user asks for
  overlapping arrangement, fill-width progress, horizontal accordion, component
  tray, finger-follow scale icons, pull-down expand summary, or micro-interaction
  components of this kind.
---

# Micro Interactions（手势微交互组件）

六个可复用的手势 / 动效组件配方。框架无关（Vue / React / SwiftUI / Flutter 均可）；**结构、时序、交互契约优先于视觉皮肤**。

## When to use

- 用户点名本 skill，或描述下列任一交互
- 需要「重叠散开 / 填充进度 / 横向手风琴 / 底部托盘 / 跟手放大图标 / 下拉摘要」
- 在现有页面上补微交互，而非整套设计系统（整套视觉仍可配合 `soft-card-ui`）

## 组件清单（原文规格）

| # | 组件 | 规格（必须守住） |
|---|------|------------------|
| 1 | Overlap stack | 做一个重叠排列组件，元素互相压住三分之一，末尾显示剩余数量，点击后依次散开并显示名称，再次点击收回 |
| 2 | Fill progress | 用组件背景的填充宽度表示完成进度，勾选一项时填充平滑前推，完成时整个组件轻微提亮 |
| 3 | H-accordion | 做一个横向手风琴组件。默认等宽窄条，点击某条展开变宽显示详细，其余同步收窄，内容延迟淡入 |
| 4 | Under tray | 在组件下方垫一层深灰（或者较组件背景淡一点的颜色）托盘，默认只露一行，点击时托盘向下展开显示明细，主组件位置保持不动 |
| 5 | Magnify dock | 做一排跟随手指放大的图标，按距离手指远近平滑缩放，相邻图标同步让位，松手选中当前图标 |
| 6 | Pull summary | 做一个可下拉展开的顶部摘要，收起时为一行胶囊，下拉时跟手展开成完整统计面板，下方内容下一并变暗 |

细节配方见 [components.md](components.md)。

## Agent workflow

```
Micro Interactions Progress:
- [ ] 1. 确认要实现哪几个组件（可只做子集）
- [ ] 2. 选定平台输入模型（pointer / touch / 两者）
- [ ] 3. 按 components.md 落地结构、状态机、动效时序
- [ ] 4. 接入真实数据（名称、进度项、明细、图标、统计）
- [ ] 5. 对照下方 Checklist 自检
```

### 输入模型

| Platform | Rule |
|----------|------|
| Mobile | 以 touch 为主；Magnify dock / Pull summary 必须跟手；避免依赖 hover |
| Web | pointer + keyboard：Accordion / Tray / Overlap 支持 click；Dock 支持 pointermove |
| Desktop | 同 Web；Dock 可用 cursor 距离缩放；Pull summary 可用拖拽手柄 |

### 动效默认值（可调，勿删缓动）

| Token | Default |
|-------|---------|
| expand / collapse | `220–320ms`，`ease-out` |
| stagger（依次散开） | 每项间隔 `40–70ms` |
| content fade-in delay | 展开宽度完成后再 `80–120ms` |
| fill progress | `280–400ms`，`ease-in-out` |
| lift / brighten | opacity 或 brightness `+4–8%`，`200ms` |
| follow finger | 跟手无额外 delay；松手 settle `180–240ms` |

优先 CSS / 声明式动画；复杂跟手用 rAF 或手势库，但**视觉结果必须符合规格原文**。

## Do / Don't

**Do**

- 严格实现规格里的比例与时序（压住 1/3、延迟淡入、主卡不动等）
- 状态机清晰：`collapsed | expanding | expanded | collapsing`（或等价）
- 给键盘/读屏：展开有 `aria-expanded`；选中有名称可读
- 与项目 token 对齐颜色；本 skill 不强制品牌色

**Don't**

- 用瞬间切状态代替平滑过渡
- Overlap 散开时忽略「显示名称」
- Tray 展开时把主组件顶走或整体下移
- Magnify 只放大当前项、相邻不让位
- Pull summary 下拉时下方内容不变暗

## Quality checklist

- [ ] 六个（或所选子集）行为与规格原文一致
- [ ] 过渡可感知且可打断（快速连点不崩）
- [ ] 触摸目标 ≥44px；跟手组件不掉帧感明显
- [ ] 完成态 / 选中态有非纯颜色线索（文案或图标）
- [ ] `prefers-reduced-motion`：缩短或取消 stagger，保留最终布局

## Additional resources

- [components.md](components.md) — 六个组件的结构、状态、动效与实现要点
