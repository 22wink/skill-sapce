---
name: feedback-states
description: >-
  Designs operation-feedback UI patterns: loading skeletons, button busy/lock,
  optimistic updates, success and error toasts/snackbars, empty states, inline
  errors, retry recovery, destructive confirm dialogs, persistent page banners,
  and undo countdown windows. Use when the user asks for loading feedback,
  skeleton screens, toast/snackbar, optimistic UI, empty state, error retry,
  submit locking, confirm before delete, alert banner, undo timer, or any
  post-action user feedback.
---

# Feedback States（操作反馈）

覆盖「用户做了一次操作之后（以及危险操作之前），界面如何说话」的模式库。框架无关；**状态机与时机优先于视觉皮肤**。可与 `soft-card-ui`（外观）或业务页并存，不替代表单校验规则本身。

## Demo ≠ 业务皮肤（重要）

`demo.html`（及同类演示页）中的样式**只为讲清反馈时机与状态**，不代表项目最终 UI。

落地时：

1. **优先用项目组件库已有能力**：如 Message/Toast、Skeleton、Empty、Dialog、Banner、Progress、Button loading。
2. **按本 skill 补齐行为契约**：最短 busy、骨架延迟、乐观回滚、Undo 世代、滑满阈值等——用库的 API 与主题 token 实现。
3. **不要**把 demo 配色/圆角/字体当规范贴进业务；库有主题就走主题，有设计稿就走设计稿。
4. 映射示例可参考同目录 `library-mapping.md`（若存在）；总约定见 [`../README.md`](../README.md)。

## When to use

- 提交、保存、删除、发送、刷新等需要明确反馈
- 首屏 / 列表加载、空数据、失败重试
- 乐观更新、Toast、骨架屏、按钮 loading
- 危险操作确认、页顶持久告警、限时撤销
- 用户说「加个加载态 / 成功提示 / 空状态 / 失败了怎么办 / 删前确认 / 顶栏通知」

## 模式清单

| # | 模式 | 一句话 |
| --- | ------ | -------- |
| 1 | Button busy | 点击后按钮进入忙态并防重复提交 |
| 2 | Skeleton / loading | 用结构占位表达「内容将在此」，而非只转圈 |
| 3 | Optimistic update | 先改 UI，失败再回滚并说明 |
| 4 | Toast / Snackbar | 成功或失败的短暂全局反馈，可带撤销/重试 |
| 5 | Empty state | 无数据时说明原因 + 一个明确下一步 |
| 6 | Error + retry | 失败区块保留上下文，提供重试/换路 |
| 7 | Inline result | 行内/字段旁的即时结果（保存成功勾、校验错） |
| 8 | Long-running progress | 超过数秒的任务用进度或阶段文案，可取消 |
| 9 | Destructive confirm | 危险确认：对话框按钮，或滑块拉满确认（未满回弹 0） |
| 10 | Page banner | 页顶持久告警（维护/欠费/权限），不自动消失 |
| 11 | Undo window | 先改界面，倒计时内可撤销再真正提交 |

细节见 [patterns.md](patterns.md)。

## Agent workflow

```
Feedback States Progress:
- [ ] 1. 列出页面上的关键操作与异步点（提交 / 拉取 / 删除…）
- [ ] 2. 为每个操作选定反馈组合（忙态 + Toast？确认？Undo 窗？）
- [ ] 3. 画状态机：idle → pending → success | error（含回滚 / 确认前）
- [ ] 4. 按 patterns.md 落地文案、时机、可达性
- [ ] 5. 对照 Checklist 自检（含重复点击、弱网、误触删除）
```

### 选型速查

| 场景 | 推荐组合 |
| ------ | ---------- |
| 表单提交 | Button busy + 成功 Toast 或页内成功态；失败 Inline / Toast |
| 列表首屏 | Skeleton；空 → Empty；失败 → Error+retry |
| 点赞/收藏/开关 | Optimistic + 失败 Toast 回滚 |
| 可恢复删除 | **Undo window**（先从列表移除）或 Toast 撤销 |
| 不可恢复 / 高危删除 | **Destructive confirm**（按钮确认或**滑满确认**）→ Button busy → 结果 Toast |
| 导出/上传 | Long-running progress；完成 Toast |
| 系统维护 / 欠费 | **Page banner**（持久）；勿只用一次 Toast |
| 静默刷新 | 局部淡指示，避免全页骨架闪烁 |

### 时机默认值

| Token | Default |
| ------- | --------- |
| 按钮 busy 最短展示 | `300–400ms`（防闪烁） |
| Skeleton 出现延迟 | 超过 `150–200ms` 再显示（即时返回则不闪） |
| Toast 自动消失 | 成功 `2.5–3.5s`；错误 `4–6s` 或手动关 |
| 乐观失败回滚 | 立即回滚 UI + 错误 Toast |
| 进度态出现 | 操作预计 > `2–3s` 才上进度 |
| Undo 窗口 | `5–8s`；到期才发真实删除/提交 |
| Banner | 不自动关；用户关后可按策略再出现（会话内 / 直至恢复） |
| 滑块确认阈值 | ≥ `95–100%` 才触发；松手未达标 → 回弹到 `0` |

## Do / Don't

**Do**

- 每个主操作都有 **pending / success / error** 可感知路径
- 失败保留用户已输入内容；说明「什么失败了」+「下一步」
- 忙态禁用重复提交，但允许取消类操作（若业务支持）
- 成功反馈可短；错误反馈要更稳、可读
- `aria-live` 告知读屏关键结果（Toast / 错误摘要）
- 高危操作先确认；可恢复删除优先 Undo 窗，少打断
- 滑块确认：必须拉满；中途松手回弹，防误触

**Don't**

- 只用颜色表示成功/失败（需文案或图标）
- 全页遮罩转圈挡住一切，却无法取消或重试
- 空状态只写「暂无数据」没有行动点
- 乐观更新失败时静默回滚、不提示
- 成功 Toast 与错误 Toast 样式过于相近
- 用 Toast 代替必须持续可见的系统告警（用 Banner）
- 危险确认默认焦点落在「删除」上
- 滑块未拉满却提交；或松手停在中间态不回弹

## Quality checklist

- [ ] 快速连点不会重复提交或状态错乱
- [ ] 弱网：busy / skeleton 会出现；失败可重试
- [ ] 空 / 错 / 加载三种互斥且不会叠怪（如空+骨架同屏）
- [ ] Toast 不挡主按钮；多条时有队列或替换策略
- [ ] 确认框 Esc / 遮罩可关；焦点陷阱与恢复
- [ ] Undo 到期与「撤销」互斥，不会双删或双提交
- [ ] Banner 不挤压到无法使用主流程（可折叠/关）
- [ ] `prefers-reduced-motion`：取消无意义闪烁，保留状态切换
- [ ] 文案具体（「保存失败：网络超时」优于「出错了」）

## Additional resources

- [patterns.md](patterns.md) — 十一种模式的结构、状态、文案与实现要点
- [demo.html](demo.html) — 可交互演示
