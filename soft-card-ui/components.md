# Soft Card UI — Components

组件用「结构 + 变体」描述，框架无关（Vue/React/SwiftUI/Flutter 均可）。

## 1. App header（深色条）

```
[ Logo/Brand ] ........ [ Bell ] [ Avatar ]
```

- 容器：`--surface-inverse`，圆角 `20–24`（可只圆底角）
- 品牌字 bold；图标线型、反白
- 移动可沉浸到顶；桌面可并入侧栏顶

## 2. Search capsule

```
[ 🔍  placeholder.............. ] [ scan ]
```

- 高度 `48–52`；背景 `--bg-muted`；全圆或 `radius-pill`
- 右侧可放扫码 / 筛选实心小方钮（可用次强调色）

## 3. Service tile（服务入口）

```
Title
subtitle / weight
[ 3D or flat illustration ]
```

- 白底卡，`--radius-card`，内边距 `16–20`
- 2×2 网格（移动）或自动横排（web）
- 标题 bold；副文案 secondary
- 插画占卡面 30–40%，勿压过文字

## 4. Inverse CTA card

```
Custom conditions          [ ↗ ]
```

- 背景 `--surface-inverse`，文字 inverse
- 大箭头可用 `--accent` 圆形底
- 整卡可点；桌面加 hover 微抬

## 5. Order row / history chip

**列表行**

```
[flag][flag]  City → City     [Status pill]
              #ORDER-ID
```

**横向历史卡**

```
#ID
From ······→ To
Tariff  Weight
[mini map / accent route]
```

- 行间留白，不用粗分割线；可用极淡分隔
- ID 用等宽或 medium，便于扫读

## 6. Status pill

- `padding: 4px 10px`；`radius-pill`；12px medium
- 颜色见 tokens 状态表
- 同一屏最多 1 个「当前强调」状态用 accent 浅底

## 7. Filter chips

```
[ Overview ] [ Ongoing ] [ Completed ] [ Recent ]
```

- 未选：浅灰底；选中：深底白字或 accent 浅底 + accent 字
- 横向可滚动（移动）

## 8. Map stage + floating detail

**移动**

```
┌──────── map full-bleed ────────┐
│  route accent line             │
│  ┌──────────────────────────┐  │
│  │ grab handle (optional)   │  │
│  │ driver · chat · call     │  │
│  │ address · status pill    │  │
│  │ timeline                 │  │
│  │ menu rows                │  │
│  └──────────────────────────┘  │
└────────────────────────────────┘
```

**Web / Desktop**

- 地图占主列 60–70%
- 详情改为右侧固定卡（同结构，去掉 grab handle）

## 9. Driver / courier strip

```
[Avatar] Name ★4.8          (💬) (📞)
         Courier
```

- 聊/电：圆形 40–44；描边次钮 + 实心主钮（电话常用 accent/黑）

## 10. Progress

**Horizontal（配送中）**

- 轨道灰；已走段 accent；当前位置圆点或包裹图标
- 两端小字时间

**Stepper**

- 完成：accent 实心圆 + 勾；未完成：灰空心
- 连接线实线或短虚线（虚线更「路径感」）

**Bar with hatch**

- 已完成实心 accent；剩余斜线填充灰（可选，blue 主题常见）

## 11. Menu row

```
[icon]  Label                    ›
```

- 行高舒适；图标线型；整行可点
- 用于 Order Details / Payment / Support

## 12. Floating pill nav（移动）

```
  ( home ) ( box ) ( ＋ ) ( bag ) ( user )
```

- 底栏悬浮，左右 margin `16`，底安全区 + `12`
- 深色 pill；选中：accent 圆形衬底
- 中心 `+` 可突出为 FAB

**Web/Desktop**：改为左侧 icon rail 或顶部 tab，保留同套图标与选中语义。

## 13. Twin action bar

```
[ 🗺 Map ]   [ 📄 Inventory ]
```

- 二等分大 pill；描边次级 + 实心主级，或双等权浅底
- 高度 `48–56`

## 14. Bottom sheet handle

- 顶中 `32×4` 圆角条，`--text-secondary` 透明度
- 仅移动；桌面侧栏不需要
