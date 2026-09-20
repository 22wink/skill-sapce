---
name: micro-interactions
description: >-
  Builds gesture-driven UI interaction patterns: overlapping stack fan-out,
  background-fill progress, horizontal accordion, under-tray expand, finger-follow
  icon magnify row, pull-down summary capsule, swipe-to-dismiss with undo,
  shared-element list-to-detail morph, and long-press progress-ring confirm.
  Use when the user asks for overlapping arrangement, fill-width progress,
  horizontal accordion, component tray, finger-follow scale icons, pull-down
  expand summary, swipe delete, shared element transition, hold-to-confirm ring,
  or micro-interaction components of this kind.
---

# Micro Interactions（手势微交互组件）

九个可复用的手势 / 动效组件配方。框架无关（Vue / React / SwiftUI / Flutter 均可）；**结构、时序、交互契约优先于视觉皮肤**。

可与 `feedback-states` 搭配：甩出删除用 Undo window；长按环与滑满确认同属「蓄力确认」家族。

## Demo ≠ 业务皮肤（重要）

`demo.html` 里的颜色、圆角、字体、阴影**仅用于演示手感**，不是设计交付物。

落地到真实项目时：

1. **优先使用当前项目的 UI 组件库 / Design System**（按钮、卡片、列表、Toast、主题变量等）。
2. **只复用本 skill 的交互契约**（跟手、阈值、回弹、stagger、Undo 时机等），把行为挂到项目已有组件上。
3. **不要**把 demo 的 CSS 变量和选择器整段复制进业务，以免和品牌/库风格冲突。
4. 若项目已有设计规范，以项目为准；仅在无规范的新项目中可临时参考 demo 或 `soft-card-ui`。

详见 [`../README.md`](../README.md)。

## When to use

- 用户点名本 skill，或描述下列任一交互
- 需要「重叠散开 / 填充进度 / 横向手风琴 / 底部托盘 / 跟手放大图标 / 下拉摘要 / 甩出删除 / 共享元素过渡 / 长按进度环」
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
| 7 | Swipe dismiss | 做一张可横向甩出的卡片，跟手位移，超过距离阈值或甩出速度足够则飞出删除，并出现限时撤销；未达标则回弹归位 |
| 8 | Shared element | 做列表到详情的共享元素过渡：点击列表项时，封面/标题从列表位置平滑变形到详情位，其余内容延迟淡入；返回时原路收回 |
| 9 | Hold ring | 做一个长按进度环确认，按住时圆环从 0 走向满格，松手未满则回零不触发；走满才执行确认/删除 |

细节配方见 [components.md](components.md)。

## Agent workflow

```
Micro Interactions Progress:
- [ ] 1. 确认要实现哪几个组件（可只做子集）
- [ ] 2. 选定平台输入模型（pointer / touch / 两者）
- [ ] 3. 按 components.md 落地结构、状态机、动效时序
- [ ] 4. 接入真实数据；甩出删除接 Undo；长按环接危险操作回调
- [ ] 5. 对照下方 Checklist 自检
```

### 输入模型

| Platform | Rule |
|----------|------|
| Mobile | 以 touch 为主；Magnify / Pull / Swipe / Hold ring 必须跟手；避免依赖 hover |
| Web | pointer + keyboard；Shared element 可用 View Transitions 或 FLIP |
| Desktop | 同 Web；Swipe 可用拖拽；Hold ring 按住鼠标左键等价长按 |

### 动效默认值（可调，勿删缓动）

| Token | Default |
|-------|---------|
| expand / collapse | `220–320ms`，`ease-out` |
| stagger（依次散开） | 每项间隔 `40–70ms` |
| content fade-in delay | 展开宽度完成后再 `80–120ms` |
| fill progress | `280–400ms`，`ease-in-out` |
| lift / brighten | opacity 或 brightness `+4–8%`，`200ms` |
| follow finger | 跟手无额外 delay；松手 settle `180–240ms` |
| swipe snap / fly | 回弹 `200–280ms`；飞出 `180–240ms` |
| shared morph | `320–420ms`，`ease-in-out`；次级内容 delay `80–120ms` |
| hold ring duration | 按满约 `800–1200ms`；松手回零 `180–240ms` |

优先 CSS / 声明式动画；复杂跟手用 rAF 或手势库，但**视觉结果必须符合规格原文**。

## Do / Don't

**Do**

- 严格实现规格里的比例与时序（压住 1/3、延迟淡入、主卡不动、未满回零等）
- 状态机清晰：`collapsed | expanding | expanded | collapsing`（或等价）
- 给键盘/读屏：展开有 `aria-expanded`；选中有名称可读；Hold 提供替代确认
- 与项目 token 对齐颜色；本 skill 不强制品牌色
- 甩出删除与 `feedback-states` Undo window 对齐世代 token，防双删
- 落地时用项目组件库承载外观；demo 皮肤不可当作业务规范

**Don't**

- 用瞬间切状态代替平滑过渡
- Overlap 散开时忽略「显示名称」
- Tray 展开时把主组件顶走或整体下移
- Magnify 只放大当前项、相邻不让位
- Pull summary 下拉时下方内容不变暗
- Swipe 与纵向滚动抢手势却无方向锁
- Shared element 只做淡入淡出、没有位置/尺寸 morph
- Hold ring 未满却触发确认
- 把 `demo.html` 的 CSS/配色原样贴进已有设计体系的项目

## Quality checklist

- [ ] 所选组件行为与规格原文一致
- [ ] 过渡可感知且可打断（快速连点/松手不崩）
- [ ] 触摸目标 ≥44px；跟手组件不掉帧感明显
- [ ] 完成态 / 选中态有非纯颜色线索（文案或图标）
- [ ] Swipe：阈值与速度二选一可删；Undo 可用
- [ ] Shared：前进/返回对称；减动效时直接切页
- [ ] Hold：未满回零；走满才回调
- [ ] `prefers-reduced-motion`：缩短或取消 stagger / morph，保留最终布局

## Additional resources

- [components.md](components.md) — 九个组件的结构、状态、动效与实现要点
- [demo.html](demo.html) — 可交互演示
