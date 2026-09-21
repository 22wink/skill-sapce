# Data Table Interactions — Patterns

模式用「结构 + 状态 + 实现要点」描述，框架无关。规格见 `SKILL.md` 清单；键盘焦点/滚动分离见 SKILL「键盘架构」。

---

## 1. Row select（行选中）

**用途：** 单行操作入口 + 批量操作的选中集合。

### 结构

```
[☐]  客户    单号    …
[☑]  林晓    SO-…   …   ← selected：底色 + checkbox
[☐]  周然    SO-…   …
     ↑ focus-row：左侧强调条（可与 selected 叠加）
```

- 每行稳定 `rowKey`（id）；选中集合为 `Set<rowKey>`
- 记录 `lastClickedIndex`（相对**当前可见列表**）供 Shift 连选
- `focusId`：键盘焦点行，可与 selected 独立（未 Space 时只搬家不选中）

### 状态 / 手势

| 手势 | 行为 |
|------|------|
| 单击行（非控件） | 清空后只选该行；再点唯一已选行可取消（可选） |
| Shift+单击 | 从 `lastClickedIndex` 到当前行的闭区间全选；无 additive 时先清空再填区间 |
| Ctrl/⌘+单击 | 切换该行选中，不清空其它 |
| 点 checkbox | 等同 toggle；Shift+checkbox 走连选 |
| 表头 checkbox | 勾选/取消**当前可见行**；半选 = 部分可见行已选 |

### 实现要点

- 选中态至少两通道：背景 + checkbox（或勾图标）
- 行内 checkbox、`打开/删除` 等按钮建议 `tabindex="-1"`，点击后把导航权交回表格（`armNav`）
- 虚拟列表：连选按「可见数据索引」或「已加载区间」定义清楚，并在 UI 说明

---

## 2. Batch bar（批量操作条）

**用途：** 选中后不打断视线地露出批量动作。

### 结构

```
表区域底部居中浮层：
[ 已选 N ] | [复制] [标为完成] [删除] | [取消]
```

- `selected.size === 0` → 隐藏（可位移+淡出）
- `size > 0` → 显示；`N` 用等宽数字
- 危险动作（删除）视觉加重；「取消」只清选中，不改数据

### 状态

| State | 行为 |
|-------|------|
| `hidden` | 无选中 |
| `shown` | 有选中；操作作用于当前 `selected` 快照 |
| `working` | 可选：条上按钮 busy（接 feedback-states Button busy） |

### 实现要点

- 定位在表格卡片内 `absolute`，避免 `position:fixed` 盖住整站底栏（除非产品要求全局条）
- 底边留白，减少挡横向滚动条 / 最后一行点击
- 跨页选中时文案写「已选 N 条（含其它页）」以免误解

---

## 3. Header sort（表头排序）

**用途：** 快速重排；支持回到「未排序」。

### 结构

```
客户 ↑     单号 ↕     金额 ↓
 ← sorted   ← none    ← sorted desc
```

### 状态

| State | 行为 |
|-------|------|
| `none` | 无主排序（或后端默认序）；图标 ↕ / 淡显 |
| `asc` | 升序；图标 ↑ |
| `desc` | 降序；图标 ↓ |

同一列点击循环：**asc → desc → none**。换列时从 `asc` 开始。

### 实现要点

- 前端排序仅用于当前已加载数据；服务端排序要带上 filter 参数一并请求
- 与 Column resize：只在非 handle 区域触发 sort；handle 上 `mousedown` 需 `stopPropagation`
- 有选中时排序不清空选中（按 rowKey 保留）；`lastClickedIndex` 按新可见序失效时可重置

---

## 4. Filter chips（筛选条件芯片）

**用途：** 让「当前为什么是这些行」可见、可拆。

### 结构

```
工具栏：[搜索] [状态 ▾] [清除筛选]
芯片行： [搜索：林] ×   [状态：处理中] ×
```

- 每个有效条件一颗 chip；点 × 只移除该条件
- 「清除筛选」移除全部条件；无条件时隐藏

### 空结果

| 类型 | 文案方向 |
|------|----------|
| `empty-filter` | 没有符合条件的结果 + **清除筛选** |
| `empty-data` | 还没有数据 + 主行动（新建等） |

二者互斥；有 filter 且 length=0 优先 `empty-filter`。

### 实现要点

- chip 与工具栏控件**同一状态源**，禁止两套状态
- 搜索输入防抖 `100–200ms`；IME 组合输入中勿打断（可选）

---

## 5. Column resize（列宽拖拽）

**用途：** 用户按内容调整扫描节奏。

### 结构

```
| 客户          |║| 单号     |
              ↑ 6–8px 命中区，cursor: col-resize
```

### 状态

| State | 行为 |
|-------|------|
| `idle` | handle 默认透明，hover 显色 |
| `dragging` | 跟手改 `width`；可显示竖线导引 |
| `settled` | 松开；可选写入 localStorage |

### 实现要点

- `mousemove` / `mouseup` 绑在 `window`，避免拖出表头丢失
- `minWidth`：内容列 ≥64px；勾选列建议固定
- 拖拽中 `user-select: none`；结束后恢复
- 与 sticky 列：改宽后更新 sticky 的 `left` 偏移（勾选宽+主列宽）

---

## 6. Sticky cols（冻结列）

**用途：** 横滑时仍能识别行、点到操作。

### 结构

```
冻左： [☐] [客户…] | 单号 | 城市 | … | [操作] 冻右
         sticky      中间横滑        sticky
```

- 左：勾选列 + 主标识列（名 / 标题）
- 右：行操作（可选）；背景勿全透明，避免下层字透出
- 选中行 sticky 单元格背景与行选中色一致

### 实现要点

- `position: sticky` 加在 `th/td`；表头 `z-index` > 表体 sticky
- 滚动在 **scrollport** 上，不是 focus host 上
- 多列左冻时，后列 `left = 前列宽度之和`

---

## 7. Inline edit（就地编辑）

**用途：** 改单个字段不弹窗。

### 结构

```
展示：  ¥1,280
编辑：  [ 1280| ]  ← 边框+焦点环
```

### 状态

| State | 行为 |
|-------|------|
| `display` | 双击（或 F2）进入编辑 |
| `editing` | 输入中；行手势不抢（忽略单击选中） |
| `saving` | 可选 busy；成功 → display + 短闪 |
| `error` | 留在 editing 或回滚 display + Toast |

### 键位

| 键 | 行为 |
|----|------|
| Enter | **提交** |
| Esc | **取消**，还原旧值 |
| Tab / Shift+Tab | 提交当前，打开上/下一行同列（无则停） |
| blur | **必须写死**：默认 = 提交（demo 如此）；表单严格场景可 = 取消 |

### 实现要点

- 校验失败：不关编辑或还原并 Toast，勿静默
- 数字/日期用对应控件；提交前规范化（圆整、时区）
- 与乐观更新：低风险字段可先闪成功再请求，失败回滚（feedback-states）

---

## 8. Density（行高密度）

**用途：** 扫数 vs 阅读切换。

| 档位 | 行高指导 |
|------|----------|
| compact | ~36px，字号可略降 |
| default | ~48px |
| comfortable | ~56px |

- 三段切换器；当前档 `aria-pressed` / `active`
- 只改行高与内边距，不改列宽偏好
- 可选记住到 localStorage

---

## 9. Keyboard nav（键盘导航）

**用途：** 重度操作不离键盘。点行后 ↑↓ 必须移**焦点行**，不得只滚滚动条。

### 结构（强制）

```
focus host   tabindex=0    overflow: hidden   ← 焦点
  scrollport               overflow: auto     ← 滚动
    table
```

### 导航武装

| 事件 | 行为 |
|------|------|
| 在表体 / 批量条 mousedown | `navArmed = true`，焦点回到 focus host |
| 点搜索框 / 表外 | `navArmed = false` |
| Esc | 清选中并 disarm（可保留 focusId） |

`navArmed` 时在 **document 捕获阶段**处理快捷键，并 `preventDefault`，这样即使焦点偶发丢失仍可工作。

### 键位

| 键 | 行为 |
|----|------|
| ↑ / ↓ | 移动 `focusId`；Shift+↑↓ 同时加入选中 |
| Space | 切换焦点行选中 |
| Enter | 打开/详情（业务回调） |
| Delete / Backspace | 删选中；无选中则删焦点行（走 Undo 或确认） |
| Ctrl/⌘+A | 全选**当前可见行** |
| Esc | 清选中；退出武装或退出编辑 |

输入框、`contenteditable`、正在 inline edit 时：**不拦截**方向键与 Backspace。

### 滚入视野

- 仅当焦点行上边缘高于表头下沿、或下边缘低于 scrollport 底时，调整 `scrollport.scrollTop`
- **禁止** `tr.scrollIntoView()`（易带动窗口滚动条）

### 实现要点

- 行内 checkbox / 操作按钮 `tabindex="-1"`
- focus host 用 `:focus-visible` 或 `.nav-armed` 描边，提示键盘已接管
- `prefers-reduced-motion`：跟焦滚动可瞬间到位

---

## 10. Delete undo（删除 + 撤销）

**用途：** 可恢复删除——先爽后审，少弹窗。

### 流程

```
触发删除
  → 行离场动画（可选）
  → 从列表移除（UI 已变）
  → Toast/Undo 窗倒计时 5–8s
       ├─ 点撤销 → 插回行（按稳定序）+ 轻提示
       └─ 到期 → 才发真实删除请求（或确认此前乐观请求）
```

### 世代 token

- 每次删除 `undoGen++`；撤销回调校验 `gen === current`，防止连删后撤错批
- 与 `feedback-states` Undo window 同一套世代语义

### 与危险确认的分工

| 删除类型 | 模式 |
|----------|------|
| 可恢复、低危 | **Delete undo** |
| 不可恢复、合规、批量大额 | 先 Destructive confirm，再提交；可不提供 Undo |

### 实现要点

- 撤销插回时保留 rowKey，避免 React/Vue 列表闪错
- 离场动画未结束不要重复点删同一行；动画中可先锁该 rowKey
- 读屏：`aria-live` 宣告「已删除 N 条，可撤销」

---

## 与项目表格库落地

| 契约 | Ant Design Table | Element Plus Table | 自研 / AG Grid |
|------|------------------|--------------------|----------------|
| 选中 | `rowSelection` + 受控 `selectedRowKeys` | `selection` + `@selection-change` | 自管 Set |
| 批量条 | 表外包一层相对定位容器自绘 | 同左 | 同左 |
| 排序 | `sorter` / 受控 `sortOrder` 三态 | `sortable` + 自定义切换 none | 自管 |
| 列宽 | `resizable` 或标题槽加 handle | `border` + 自定义 | 列 `resizable` |
| 冻结 | `fixed: 'left'/'right'` | `fixed` | pinned columns |
| 行内编辑 | 单元格自定义渲染 | 同左 | editing API |
| 键盘 | 库多不管 ↑↓ 与滚动抢占：**外层仍按本 skill 做 focus host / scrollport / arm** | 同左 | AG Grid 自带导航时可少包一层，但仍需避免外层页面滚动抢键 |

加载骨架、空态、Toast、Undo 视觉与文案 → 交给 `feedback-states`，本 skill 只规定表格侧何时触发。
