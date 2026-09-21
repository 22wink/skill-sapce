# ui/ — 使用约定

本目录下的 skill 描述的是**交互结构、状态机、时序与可达性契约**，不是可直接拷进业务的最终视觉稿。

## Demo 与落地的关系（必读）

| | Demo（如 `demo.html`） | 实际项目落地 |
|--|------------------------|--------------|
| 目的 | 讲清「手感 / 状态怎么走」 | 融入现有产品 UI |
| 样式 | 仓库内自拟的示意皮肤（颜色、圆角、字体、间距） | **必须**对齐当前项目的设计规范或组件库 |
| 组件 | 原生 HTML / 自写 CSS，便于零依赖演示 | 优先用项目已有组件（如 Element Plus、Ant Design、Vant、内部 Design System） |
| 可改 | 外观可整套替换 | **交互契约尽量保留**（阈值、回弹、忙态、Undo 时机等） |

### Agent / 开发者落地时

1. **先认项目事实**：现有 UI 库、主题 token、圆角/字号阶梯、Toast/Dialog/Empty 是否已有封装。
2. **只搬行为**：按 `SKILL.md` / `components.md` / `patterns.md` 实现状态与手势；视觉走项目变量与组件 API。
3. **禁止**：把 demo 的配色、阴影、字体、圆角数值原样贴进业务，造成「另一套皮肤」。
4. **可以**：在无设计体系的新项目里，临时沿用 demo 皮肤，或改用 `soft-card-ui`——但仍应抽成项目 token，而不是复制 demo 选择器。
5. **已有库时**：例如反馈用 `ElMessage` / `ElSkeleton` / `ElEmpty`；确认用 `ElMessageBox` 或项目 Dialog；微交互包在现有 Card/List 外层，而不是新建一套平行组件皮。

### 什么必须守住 vs 什么必须适配

| 必须守住（交互契约） | 必须适配（视觉与载体） |
|----------------------|------------------------|
| 状态流转、阈值、回弹/飞出、延迟淡入 | 色板、字体、圆角、阴影、间距 |
| 忙态防重、Undo 世代、未满不提交 | Button / Dialog / Toast / Tag 用哪套组件 |
| 读屏角色、`prefers-reduced-motion` 降级 | 是否暗色主题、品牌主色、动效时长微调 |

## 本目录 skill

| Skill | 管什么 | Demo |
|-------|--------|------|
| [`soft-card-ui`](./soft-card-ui/) | 物流/软卡片视觉语言与布局 | 无独立 demo（配方文档） |
| [`micro-interactions`](./micro-interactions/) | 手势微交互 | [`demo.html`](./micro-interactions/demo.html) |
| [`feedback-states`](./feedback-states/) | 操作反馈模式 | [`demo.html`](./feedback-states/demo.html) 等 |
| [`data-table-interactions`](./data-table-interactions/) | 表格操作交互（含键盘焦点/滚动分离） | [`demo.html`](./data-table-interactions/demo.html) |
