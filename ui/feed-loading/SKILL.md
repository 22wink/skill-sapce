---
name: feed-loading
description: >-
  Builds feed/product-list loading choreography: shape-matched skeleton shimmer
  reveal, one-viewport prefetch infinite scroll with bouncing dots and conveyor
  card append, pull-to-refresh, masonry shortest-column insert, dual-column
  staggered enter, filter reload via skeleton, tab keep-alive scroll restore,
  footer error retry without clearing list, bottom-only skeleton for new pages,
  sticky category headers, back-to-top FAB, and hover prefetch for detail.
  Use when the user asks for feed loading, infinite product list, pull refresh,
  waterfall insert, filter reload, tab keep-alive, load-more error bar, sticky
  section headers, back to top, or hover prefetch.
---

# Feed Loading（信息流 / 商品列表加载）

列表与信息流的**出现 / 分页 / 刷新 / 筛选 / 导航**编排。框架无关；交互契约优先于视觉皮肤。

与 `feedback-states`：Toast / Empty / 通用 Error 可复用；本 skill 管列表编排。与 `photo-wall`：单图 blur-up 用那边；本 skill 管列表级加载。

## Demo ≠ 业务皮肤（重要）

`demo.html` **仅示意手感**。落地必须适配项目组件库与设计规范，禁止复制 demo CSS。总约定见 [`../README.md`](../README.md)。

## When to use

- 信息流骨架显影、商品列表预加载、下拉刷新、瀑布流、筛选重载
- Tab 保活、加载失败条、底屏骨架、粘性分类、回顶、悬停预取
- 用户点名本 skill 或描述上述任一

## 组件清单（规格必须守住）

| # | 模式 | 规格 |
|---|------|------|
| 1 | Feed skeleton reveal | 做一个信息流加载：先显示和真实内容形状一样的骨架屏（例如描述部分样式：圆头像、标题条、图片块，尺寸和位置跟真实卡片一致），所有骨架屏共用一道柔和的斜向光带，每 1.5s 从左往右扫过；数据到了以后，按头像、标题、图片这些块从上到下每块错开 60ms 依次浮现（淡入+上移 8px），骨架同步淡出，整页不跳动、不闪白 |
| 2 | Product list prefetch | 做一个商品列表，滑到离底部还剩约一屏时就提前加载下一页；底部显示 3 个依次跳到的小圆点；新商品卡片每张错开 50ms，从上方 24px 处滑上来并淡入，约 0.45s，像传送带接上；加载期间滚动不卡、位置不跳；最后一页显示“没有更多了”。 |
| 3 | Pull to refresh | 做一个列表下拉刷新：在顶部下拉跟手，超过约 56–64px 阈值松手后触发刷新；刷新中顶部显示进度/三点；新数据就绪后从顶部接入（可短 fade），**不整页闪白**；刷新期间尽量保持下方已读内容稳定 |
| 4 | Masonry fill | 做一个瀑布流列表：多列高度不齐时，新卡片插入**当前最短列**；插入用短淡入（约 200–280ms），**禁止**为接新卡而重排整墙已有卡片位置 |
| 5 | Dual-column stagger | 做一个双列商品入场：同一批新卡按左→右或之字形顺序，列间错开 30–50ms 依次浮现（可叠传送带位移），形成错峰而非两列同时闪现 |
| 6 | Filter reload | 做一个筛选/排序后的列表重载：变更筛选项后列表先短淡出，再换上**同构骨架**（复用 #1 光带契约），数据到后分块或整卡浮现；需要回顶时平滑滚到顶部，**不要**无动画瞬跳 + 白屏 |
| 7 | Tab keep-alive | 做一个多 Tab 列表：切换 Tab 时用短淡切或局部骨架，**返回曾打开的 Tab 时恢复滚动位置与已加载页**，不重新从第一页清空重拉（除非业务强制刷新） |
| 8 | Footer error bar | 做一个分页加载失败态：预加载失败时在列表**底部**显示「加载失败 · 重试」，**不清空、不卸载**已成功渲染的商品；点重试仅重打失败页 |
| 9 | Bottom-only skeleton | 做一个分页骨架：加载下一页时**仅在底部约一屏**铺同构骨架（可带 #1 光带），上方已出现的真卡保持不动；数据到后底部骨架换成新卡（可用 #2 传送带） |
| 10 | Sticky category | 做一个带分类的长列表：分类标题滚动时**吸顶**；点击顶部分类条时平滑滚动到对应锚点，吸顶态与当前分类高亮同步 |
| 11 | Back-to-top FAB | 做一个长列表回顶：滚动超过约两屏后显示回顶按钮；点击后平滑滚到顶部，**不触发**列表重载或清空 |
| 12 | Hover prefetch | 做一个列表悬停预取：指针在卡片上停留约 200ms 后预取详情数据/路由；点击进入时应尽量秒开（已有缓存则直接用），移动端用 touch 长按或跳过预取 |

细节见 [patterns.md](patterns.md)。

## Agent workflow

```
Feed Loading Progress:
- [ ] 1. 勾选需要的模式（可子集）
- [ ] 2. 理清 scroll root（window vs 容器）与分页 API
- [ ] 3. 按 patterns 落地状态机；组合时避免互相抢滚动
- [ ] 4. 接项目 Card/List/Tab/FAB；皮肤走设计 token
- [ ] 5. Checklist + reduced-motion
```

### 时机默认值

| Token | Default |
|-------|---------|
| #1 光带 / 块错开 | `1.5s` 左→右；块 `+60ms`；↑`8px` |
| #2 预加载 / 接卡 | 距底 ≈1 屏；点跳动；卡 `+50ms`、↑来自 `-24px`、`≈0.45s` |
| #3 下拉阈值 | `56–64px`；跟手阻尼可选 |
| #4 插入动画 | `200–280ms` fade |
| #5 列错峰 | `30–50ms` |
| #6 筛选切换 | 淡出 `150–200ms` → 骨架 → 浮现 |
| #7 Tab | 保活缓存实例；恢复 `scrollTop` |
| #8 失败 | 底部条；保留列表 |
| #9 底骨架 | 高度约 `1×` 视口内 2–4 张卡 |
| #10 吸顶 | `position: sticky`；点击平滑滚 |
| #11 回顶出现 | 滚过 ≈`2×` 视口 |
| #12 预取 | 悬停 ≈`200ms` |

## Do / Don't

**Do**

- 追加/插入优先，避免整表销毁重挂
- 失败与加载态放脚注区，保住已渲染内容
- 多模式组合时统一一个 scroll root 与 in-flight 锁
- 落地用项目组件库

**Don't**

- 刷新/筛选取消整个 DOM 造成闪白
- 瀑布流为对齐而重排旧卡
- Tab 返回时丢掉滚动与分页进度
- 回顶时顺手 `location.reload` 或清空 store
- 复制 demo 皮肤进业务

## Quality checklist

- [ ] 所选模式行为符合上表规格
- [ ] 滚动不卡、位置不无故跳动
- [ ] 末页 / 失败 / 空态互斥清晰
- [ ] `prefers-reduced-motion` 降级后仍可用

## Additional resources

- [patterns.md](patterns.md)
- [demo.html](demo.html) — 示意，非业务最终样式
