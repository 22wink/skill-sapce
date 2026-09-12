# Soft Card UI — Platform Adaptation

同一套 DNA，按平台改**布局与密度**，不改圆角语言、状态 pill、高对比 CTA。

## Mobile

| Pattern | Rule |
| --------- | ------ |
| Structure | 单列纵向栈 |
| Nav | 底部 floating pill；中心可 FAB |
| Tracking | 全幅地图 + 底部 sheet（可拖） |
| Services | 2×2 网格 |
| Orders | 纵向列表；历史可横向 scroll |
| Touch | 主控件 ≥44px；卡间距 ≥12 |

优先拇指区：主 CTA、底栏、sheet 内主按钮放中下部。

## Web（≥768 / ≥1024）

| Pattern | Rule |
| --------- | ------ |
| Structure | 分栏：左列表 280–360 · 中地图 flex · 右详情 320–400 |
| Nav | 顶栏或左侧 rail；去掉贴底 pill |
| Tracking | 地图固定主舞台；详情常驻右栏（非 modal） |
| Services | 单行 4–5 tile |
| Orders | 表格感仍用卡片行，增加 hover |
| Pointer | hover 上浮/描边；focus ring 可见 |

窄 web（平板竖屏）可回退「上地图 + 下详情」类移动布局。

## Desktop（大屏 / Electron 等）

| Pattern | Rule |
| --------- | ------ |
| Structure | Dashboard：顶栏信息密度略增；多列卡片 |
| Map | 更大主舞台；路线与 hub 可同时标注 |
| Detail | 固定侧栏或可钉选第二屏 |
| Density | 字号可同 web；边距加大到 24–40 |
| Window | 支持侧栏折叠；折叠后保留 icon rail |

桌面勿简单放大手机稿；信息架构改为「总览 + 主舞台 + 详情」。

## Responsive breakpoints（建议）

```
mobile:  default
tablet:  ≥768px   → 双列服务；追踪可上下分
desktop: ≥1024px  → 三区 split
wide:    ≥1440px  → 更宽地图；右栏可 400px
```

## Mapping cheatsheet

| Mobile | Web / Desktop |
| -------- | ---------------- |
| Bottom sheet | Right panel |
| Floating pill nav | Left rail / top tabs |
| 2×2 service grid | Horizontal tiles |
| Full-bleed map | Center stage map |
| Horizontal history scroll | Grid or denser list |
| Grab handle | Panel header / collapse |

## Framework hints（非强制）

- **CSS**：用变量实现主题；`clamp()` 处理字号与间距
- **React/Vue**：卡片与 pill 做成原子组件；布局用组合而非复制三套样式
- **Flutter**：`BorderRadius.circular(24)` + `BoxShadow` 轻阴影；`BottomSheet` / `NavigationRail` 切换
- **SwiftUI**：`RoundedRectangle(cornerRadius: 24)`；`safeAreaInset` 放 sheet

## Accessibility

- 状态不只靠颜色：pill 含文字
- 深色底上的字对比 ≥ WCAG AA
- 键盘可到达所有主操作（web/desktop）
- 减动效：尊重 `prefers-reduced-motion`
