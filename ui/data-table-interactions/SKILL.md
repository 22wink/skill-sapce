---
name: data-table-interactions
description: >-
  Builds satisfying data-table operation patterns: row select with Shift/Ctrl,
  floating batch action bar, column sort and resize, sticky columns, filter chips,
  inline cell edit, density toggle, keyboard navigation (focus host vs scrollport),
  and delete with undo. Use when the user asks for table UX, data grid interactions,
  spreadsheet-like selection, batch toolbar, column freeze, inline edit, arrow-key
  row focus, or table micro-interactions.
---

# Data Table Interactions（表格操作交互）

覆盖「操作数据表时为什么爽」的交互契约：选中、批量、排序筛选、列操作、就地编辑、键盘与 Undo。框架无关；**行为优先于皮肤**。

可与 `feedback-states`（Toast / Undo / 空态 / 骨架）搭配；本 skill 不替代分页协议、权限与接口约定。

## Demo ≠ 业务皮肤（重要）

`demo.html` 的配色与圆角**只为讲清手感**。落地时：

1. **优先用项目表格组件**（Ant Design Table、Element Plus Table、AG Grid、自研 Grid 等）。
2. **只复用本 skill 的交互契约**（连选模型、批量条时机、焦点层/滚动层分离、Undo 世代等）。
3. **不要**把 demo CSS 当品牌规范贴进业务；库有主题走主题，有设计稿走设计稿。
4. 总约定见 [`../README.md`](../README.md)。

## When to use

- 用户要做「好用的表格 / 数据网格 / 批量操作 / 像 Excel」
- 补选中、筛选 chip、列宽、冻结列、就地编辑、删除可撤销
- ↑↓ 与滚动条抢手势、点行后方向键无效等键盘问题
- 提到 table UX、data grid、batch bar、inline edit、column resize、row focus

## 模式清单

| # | 模式 | 一句话 |
|---|------|--------|
| 1 | Row select | 单击选中；Shift 连选；Ctrl/⌘ 点选；勾选列可点 |
| 2 | Batch bar | 有选中时浮现操作条，文案含数量；清空即收起 |
| 3 | Header sort | 表头三态：升 → 降 → 无；与列宽拖拽点击隔离 |
| 4 | Filter chips | 筛选条件可视化，单颗可移除；空结果可「清除筛选」 |
| 5 | Column resize | 表头右缘拖拽改宽；最小宽度有下限 |
| 6 | Sticky cols | 冻结勾选列 + 主标识列；右侧可冻操作列 |
| 7 | Inline edit | 双击编辑；Enter 提交；Esc 取消；Tab 同行下一行；blur 策略明确 |
| 8 | Density | 紧凑 / 默认 / 舒适行高 |
| 9 | Keyboard nav | 点表后 arm；↑↓ 移焦点行；**焦点层与滚动层必须分离** |
| 10 | Delete undo | 先行移除 + 限时撤销（接 `feedback-states` Undo window） |

细节见 [patterns.md](patterns.md)。

## Agent workflow

```
Data Table Progress:
- [ ] 1. 确认要落地哪些模式（可只做子集；键盘与选中建议优先）
- [ ] 2. 对齐项目 Table API（插槽、rowKey、虚拟滚动、受控 selectedRowKeys）
- [ ] 3. 选中模型 + 批量条（含「可见行全选」vs「跨页全选」文案）
- [ ] 4. 排序 / 筛选 chip / 列宽 / 冻结
- [ ] 5. 就地编辑（提交/取消/blur 策略写死）
- [ ] 6. 键盘：焦点宿主 ≠ overflow 容器；document 级 arm 导航
- [ ] 7. 删除 Undo 世代；空/加载接 feedback-states
- [ ] 8. 对照 Checklist 自检
```

### 选型速查

| 场景 | 推荐组合 |
|------|----------|
| 后台订单/用户列表 | Row select + Batch bar + Sort + Filter chips + Sticky |
| 可改单价/备注 | Inline edit + 行闪成功（接 feedback-states Inline） |
| 可恢复删除 | Delete undo（勿先弹确认） |
| 不可恢复 / 高危批量 | Batch → Destructive confirm（feedback-states）→ 再提交 |
| 长表 + 键盘重度 | Keyboard nav（焦点/滚动分离）+ Density |
| 首屏慢 | 表体 Skeleton（feedback-states），勿整页转圈 |

### 时机与尺寸默认值

| Token | Default |
|-------|---------|
| 批量条出现/收起 | `220–280ms`，`ease-out`；有选中才 `show` |
| 行选中底色过渡 | `100–140ms` |
| 删除行离场 | `200–280ms` 后再从数据移除 |
| Undo 窗口 | `5–8s`（与 feedback-states 对齐）；到期才真正提交 |
| 就地编辑成功闪 | `400–600ms` |
| 搜索防抖 | `100–200ms` |
| 列宽最小 | `≥64px`（勾选列可固定 ~44px） |
| 行高密度 | 紧凑 ~36 / 默认 ~48 / 舒适 ~56 |
| 焦点行滚入 | 仅当行越过视口边缘时改 `scrollTop`（禁止整页 `scrollIntoView`） |

## 键盘架构（必读）

↑↓ 被滚动条吃掉，是表格实现里最常见的坑。契约：

```
┌─ focus host（tabindex=0，overflow:hidden）──┐  ← 键盘焦点落在这里
│  ┌─ scrollport（overflow:auto）───────────┐ │  ← 只有这里滚
│  │  <table>…</table>                      │ │
│  └────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

1. **焦点宿主与滚动容器分离** — 可聚焦节点本身不要 `overflow: auto`
2. **点行 / 点表体后 `navArmed = true`** — 在 `document` 捕获阶段处理 ↑↓，并 `preventDefault`
3. **行内 checkbox / 图标按钮 `tabindex="-1"`** — 避免点击后焦点被抢走
4. **需要滚入视野时只改 scrollport 的 `scrollTop`** — 禁用对行调用 `scrollIntoView`（会带动页面滚动条）
5. **搜索框获焦时 `disarm`** — 方向键还给 caret；Esc 可清空选中并 disarm

## Do / Don't

**Do**

- 选中态 = 底色 + checkbox（不只靠颜色）
- 批量条文案写清「已选 N」；操作只作用于选中集合
- 表头全选默认 = **当前可见/当前页**；若支持跨页全选，必须二次文案确认
- 筛选空态提供「清除筛选」，与「从未有数据」文案区分
- 列宽拖拽命中区与排序点击隔离（resize handle 上 `stopPropagation`）
- 删除可恢复 → Undo；不可恢复 → 先确认
- `prefers-reduced-motion`：缩短离场/闪动，保留选中与焦点态

**Don't**

- 把 `tabindex=0` 和 `overflow:auto` 放在同一节点上还指望 ↑↓ 只移行
- 用整页 `scrollIntoView` 跟焦
- 就地编辑 blur 策略含糊（必须写死：提交或取消）
- 横滑时首列身份列跟着消失（应 sticky）
- 批量删除无数量、无 Undo/确认
- 把 demo 皮肤复制进已有设计体系

## Quality checklist

- [ ] Shift 连选、Ctrl/⌘ 点选、单击选中行为符合 patterns
- [ ] 批量条有选中才出现；取消/清空后收起；不误挡横向滚动操作
- [ ] 排序三态正确；拖列宽不触发排序
- [ ] 筛选 chip 与工具栏条件同源；空结果可清除
- [ ] 冻结列在横滑下仍可读
- [ ] 就地编辑 Enter / Esc / Tab / blur 符合约定
- [ ] **点行后 ↑↓ 移焦点行，不滚页面、不空转滚动条**
- [ ] 焦点宿主 ≠ scrollport；checkbox 不抢焦点
- [ ] 删除 Undo 世代互斥；快速连删不双删/错撤
- [ ] 键盘 Space / Delete / ⌘A（可见行）可用；输入框内不误触
- [ ] `prefers-reduced-motion` 降级可读

## Additional resources

- [patterns.md](patterns.md) — 十个模式的结构、状态、实现要点
- [demo.html](demo.html) — 可交互 Playground（含焦点/滚动分离参考实现）
