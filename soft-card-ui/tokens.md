# Soft Card UI — Design Tokens

实现时映射到 CSS variables / Tailwind theme / 原生 token。数值可按平台缩放，比例勿破。

## Color

### Neutrals（全主题共用）

| Token | Value | Usage |
| ------- | ------- | ------- |
| `--bg-page` | `#F5F5F7` / `#F7F7F7` | 页面底 |
| `--bg-card` | `#FFFFFF` | 卡片 |
| `--bg-muted` | `#EEEEF0` | 输入框、次级容器 |
| `--text-primary` | `#0A0A0A` | 标题、关键数字 |
| `--text-secondary` | `#6B7280` | 标签、元数据 |
| `--text-inverse` | `#FFFFFF` | 深色底上的字 |
| `--border-subtle` | `rgba(0,0,0,0.06)` | 可选细边；优先阴影/留白 |

### Accent variants

**yellow-black（默认）**

- `--accent`: `#FFC837`
- `--accent-strong`: `#FFD000`
- `--surface-inverse`: `#000000`
- `--route-line`: `--accent`

**purple**

- `--accent`: `#6C5CE7`
- `--accent-soft`: `#EDE9FE`
- `--surface-inverse`: `#111827`
- `--route-line`: `--accent`

**blue**

- `--accent`: `#3B82F6`
- `--accent-soft`: `#DBEAFE`
- `--surface-inverse`: `#111827`
- `--route-line`: `--accent`

### Status（pastel pill）

| Status | Background | Text |
| -------- | ------------ | ------ |
| In Transit / Ongoing | `#FFF3C4` | `#8A6D00` |
| Delivered / Received | `#D1FAE5` | `#065F46` |
| Pending | `#E5E7EB` | `#374151` |
| Progress (blue theme) | `#DBEAFE` | `#1D4ED8` |

状态色可随主题微调，但必须保持「浅底 + 深字」。

## Typography

- 字体：几何无衬线（优先项目已有字体；新建可用 `Plus Jakarta Sans` / `SF Pro` / `Geist`；避免随意混用 3+ 字体）
- 字重：标题 `700`，正文 `500/400`，元数据 `400`
- 层级建议：

| Role | Size (mobile) | Size (web/desktop) |
| ------ | --------------- | -------------------- |
| Display / Tracking ID | 22–28px | 28–36px |
| Section title | 18–20px | 20–24px |
| Body | 14–16px | 14–16px |
| Meta / caption | 12–13px | 12–13px |
| Pill label | 12px medium | 12px medium |

## Shape

| Token | Value |
| ------- | ------- |
| `--radius-card` | `24px`（范围 20–32） |
| `--radius-sheet` | `28px` 顶角 |
| `--radius-control` | `16px` |
| `--radius-pill` | `999px` |
| `--radius-avatar` | `999px` |

## Spacing（8pt）

- 页边：mobile `16–20`；web `24–32`
- 卡内 padding：`16–24`
- 卡间距：`12–16`
- 列表行高：内容区约 `56–72` 触控友好
- 区块标题与列表：`8–12`

## Elevation

- 默认卡：`0 4px 24px rgba(0,0,0,0.06)` 或极淡边框
- 浮层 sheet：`0 -8px 40px rgba(0,0,0,0.10)`
- 浮动底栏：`0 8px 32px rgba(0,0,0,0.18)`
- 避免多层彩色 glow

## Motion

至少保留 2–3 个有意动效：

1. Sheet / 侧栏：`transform + opacity`，160–240ms，ease-out
2. 路线进度点：轻微 scale / 位置过渡
3. 卡片按压：`scale(0.98)` 或阴影收敛（移动）；web 用 hover 上浮 2–4px

不要：无限闪烁、强弹跳、满屏粒子。

## Map styling

- 底图：浅灰 / 银白 / 弱对比单色；去掉噪音 POI
- 路线：`6–8px` 圆角实线，颜色 = `--route-line`
- 当前位置：实心圆 + 可选软脉冲环
- 起终点：简洁 pin 或载具图标，勿大贴纸
