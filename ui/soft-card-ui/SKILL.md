---
name: soft-card-ui
description: >-
  Applies a cross-platform Soft Card UI system (large-radius cards, high-contrast
  action surfaces, pastel status pills, map+floating detail sheets, floating pill
  nav) derived from modern logistics/tracking app design language. Use when the
  user asks to build or restyle UI with soft-card / logistics-app aesthetics,
  tracking dashboards, delivery-style screens, or wants this look on mobile, web,
  or desktop.
---

# Soft Card UI（跨端设计系统）

将「物流/配送类现代 App」设计稿提炼为可复用的 Soft Card 设计系统。默认主题可换（黄/黑、紫、蓝），**结构与组件语言保持一致**。

## 与项目 UI 的关系（重要）

本 skill 是**一套可选的视觉/布局语言**，不是强制覆盖层。

落地时：

1. 若项目**已有** Design System / 组件库 / 品牌色：**优先沿用项目 token 与组件**；只借用本 skill 的布局模式与组件配方（卡片栈、地图+浮层、pill 状态等），不要强行换成 demo 式配色。
2. 若项目**没有**统一规范、且产品明确要 soft-card / 物流风：再按本 skill 的 DNA 与 `tokens.md` 落地，并抽成项目级变量。
3. 与 `micro-interactions` / `feedback-states` 合用时：交互走那两份契约，皮肤仍跟当前项目（或本 skill DNA），**不以任何 demo.html 皮肤为准**。

总约定见 [`../README.md`](../README.md)。

## When to use

- 用户提到 soft-card-ui、软卡片、物流 App 风、tracking UI、大圆角卡片
- 需要把同一套视觉语言落到移动端 / Web / 桌面端
- 新建或改版：首页仪表盘、地图追踪、订单列表、详情底栏

若项目已有 Design System，**优先沿用项目 token**；本 skill 只补布局模式与组件配方，不要强行覆盖品牌色。

## Design DNA（必须守住）

1. **Card-first**：几乎一切信息住在圆角卡片里；卡片之间靠留白分离，少用硬分割线。
2. **大圆角**：卡片 / 底栏 / 主按钮 radius ≈ `20–32px`；胶囊控件 `999px`。
3. **高对比操作面**：主 CTA 用纯黑或品牌色实心块 + 粗图标（箭头/加号），一眼可点。
4. **柔和状态色**：状态用 pastel 底 + 深色字的 pill，不用高饱和实心块刷整行。
5. **地图 + 浮层**：追踪页 = 全幅地图/插画背景 + 底部（或侧栏）大圆角详情卡。
6. **呼吸感**：卡片内 padding `16–24px`；区块间距 `12–20px`；忌堆砌。
7. **线型图标 + 可选 3D 插画**：功能用细线图标；分类/空态可用 matte 3D 或扁平插画点缀，勿滥。

## Agent workflow

复制并跟踪：

```
Soft Card UI Progress:
- [ ] 1. 选定主题变体（yellow-black / purple / blue）或项目品牌色
- [ ] 2. 选定平台布局（mobile stack / web split / desktop dashboard）
- [ ] 3. 落地 token（见 tokens.md）
- [ ] 4. 用组件配方搭屏（见 components.md）
- [ ] 5. 按平台规则适配（见 platforms.md）
- [ ] 6. 对照下方 Checklist 自检
```

### Step 1 — Theme

| Variant | Accent | Primary surface | Typical mood |
| --------- | -------- | ----------------- | -------------- |
| `yellow-black` | `#FFC837` / `#FFD000` | `#000000` | 高级、硬朗、Move. 风 |
| `purple` | `#6C5CE7` / `#7B61FF` | `#111827` | 活力、消费级 |
| `blue` | `#3B82F6` | `#111827` | 可信、企业级 |

用户未指定时：默认 `yellow-black`。可用 CSS 变量换肤，勿改结构。

### Step 2 — Platform layout

| Platform | Layout rule |
| ---------- | ------------- |
| Mobile | 单列卡片栈；追踪用全幅地图 + 底部 sheet；底栏 floating pill |
| Web | 左列表 / 中地图 / 右详情；服务入口横排；底栏改顶栏或侧栏 |
| Desktop | 仪表盘多列；地图作主舞台；详情固定侧栏；hover 替代部分 tap |

细节见 [platforms.md](platforms.md)。

### Step 3–4 — Tokens & components

- Token：[tokens.md](tokens.md)
- 组件配方：[components.md](components.md)

实现时优先 CSS 变量 / design tokens，避免硬编码散落。

## Screen recipes（高频页面）

### A. Home / Dashboard

1. **顶栏**：深色圆角条或浅底 + 头像；品牌 / 通知 / 头像
2. **当前运单卡**：缩略地图或插画 + ID + 展开箭头
3. **服务入口**：2×2（移动）或横排（Web）圆角卡 + 插画 + 标题 + 副文案
4. **主 CTA 卡**：黑/品牌色 + 大箭头或「自定义条件」
5. **订单列表**：起终点 + ID + 状态 pill；可横向历史卡

### B. Tracking / Map

1. 全幅浅色/单色地图，路线用 **粗实线 accent**
2. 起终点用 pin / 载具图标
3. 底部（移动）或侧栏（桌面）详情卡：骑手信息 + 聊/电圆钮 + 地址 + 状态 pill + 进度条/时间轴 + 菜单行

### C. Delivery detail

1. 顶栏返回 + 标题 + more
2. 筛选 chip 行（Overview / Ongoing / …）
3. 可选英雄插画（车/箱）
4. 信息卡：Tracking ID、From/To、进度步进
5. 底栏双 pill 按钮（Map / Inventory）或单主按钮

## Do / Don't

**Do**

- 用大圆角卡片承载交互
- 状态用 pastel pill
- 追踪页保持「地图舞台 + 浮层信息」
- 主操作高对比、可触摸（≥44px）
- 跨端只改布局密度，不改 DNA

**Don't**

- 小圆角（≤8px）密表格后台风
- 紫雾玻璃 + 多层霓虹光效堆砌（除非用户指定 glass）
- 首屏塞满统计条、促销贴、浮动徽章
- 实线密分割替代留白
- 把移动底栏原样钉在桌面底部

## Quality checklist

- [ ] 圆角层级一致（卡 24–32 / 钮与 pill 全圆）
- [ ] 主 CTA 高对比且唯一视觉焦点
- [ ] 状态色可读（浅底深字）
- [ ] 移动：单列 + 浮层；Web/桌面：分栏且地图可扩展
- [ ] 图标线宽统一；插画不抢正文
- [ ] 间距节奏稳定（8 的倍数）
- [ ] 浅色为主；深色仅用于顶栏/主 CTA/强调卡

## Additional resources

- [tokens.md](tokens.md) — 颜色、字号、间距、阴影、运动
- [components.md](components.md) — 组件结构与状态
- [platforms.md](platforms.md) — 移动 / Web / 桌面适配
