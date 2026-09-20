# skill-sapce

个人 Agent Skills 仓库。按领域分类存放；每个 skill 是独立文件夹，内含给 Agent 读的 `SKILL.md`。

克隆本仓库**不会**自动生效，需要把 skill 安装到各工具约定的目录。

## 分类约定

| 目录 | 放什么 |
|------|--------|
| `ui/` | 设计系统、视觉语言、布局模式、动效规范 |
| `frontend/` | 前端工程：框架约定、组件写法、样式工程 |
| `backend/` | 后端 / API / 数据模型 |
| `devops/` | CI、部署、环境、运维脚本 |
| `workflow/` | 通用开发流程：PR、commit、review、会话交接 |
| `product/` | 产品 / 需求 / 文档协作 |
| `tools/` | 外部工具与 CLI（禅道、Figma、MCP 等） |

空分类目录用 `.gitkeep` 占位，保证能推到远程。有 skill 后可删掉该目录下的 `.gitkeep`。

## 仓库结构

```text
skill-sapce/
├── README.md
├── ui/
│   ├── .gitkeep
│   ├── soft-card-ui/         # Soft Card UI 设计系统
│   │   ├── SKILL.md
│   │   ├── components.md
│   │   ├── platforms.md
│   │   └── tokens.md
│   ├── micro-interactions/   # 手势微交互组件
│   │   ├── SKILL.md
│   │   ├── components.md
│   │   └── demo.html
│   └── feedback-states/      # 操作反馈模式
│       ├── SKILL.md
│       ├── patterns.md
│       └── demo.html
├── frontend/
│   └── .gitkeep
├── backend/
│   └── .gitkeep
├── devops/
│   └── .gitkeep
├── workflow/
│   └── .gitkeep
├── product/
│   └── .gitkeep
└── tools/
    └── .gitkeep
```

约定：`<category>/<skill-name>/SKILL.md`。只做一层分类，不要再嵌套。

## Skill 清单

| 分类 | Skill | 用途 | 何时触发 |
|------|-------|------|----------|
| `ui/` | [`soft-card-ui`](./ui/soft-card-ui/) | Soft Card / 物流 App 风跨端 UI | 提到 soft-card、物流追踪风、大圆角卡片、tracking UI |
| `ui/` | [`micro-interactions`](./ui/micro-interactions/) | 九个手势微交互组件 | 重叠散开、填充进度、横向手风琴、组件托盘、跟手放大、下拉摘要、甩出删除、共享元素、长按进度环 |
| `ui/` | [`feedback-states`](./ui/feedback-states/) | 操作反馈模式库 | 加载骨架、Toast、乐观更新、空状态、错误重试、按钮忙态、危险确认/滑满确认、页顶 Banner、Undo 倒计时 |

## 安装到 Cursor

Cursor 会从下列目录加载 skills：

| 范围 | 路径 |
|------|------|
| 个人全局 | `~/.cursor/skills/<skill-name>/` |
| 仅当前项目 | `<project>/.cursor/skills/<skill-name>/` |

兼容目录（可选）：`~/.agents/skills/`、`~/.claude/skills/`、`~/.codex/skills/`，以及项目内对应路径。

**不要**装到 `~/.cursor/skills-cursor/`，那是 Cursor 内置技能目录。

安装时链接到**叶子 skill 文件夹**（含 `SKILL.md` 的那一层），分类目录只留在本仓库。

### 方式一：符号链接（推荐，改仓库即生效）

先克隆到固定位置，再为每个 skill 建链接。

**Windows（PowerShell，管理员或已开启开发者模式）：**

```powershell
git clone git@github.com:22wink/skill-sapce.git D:\skill-sapce
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\skills"

# 示例：安装 soft-card-ui
cmd /c mklink /J "$env:USERPROFILE\.cursor\skills\soft-card-ui" "D:\skill-sapce\ui\soft-card-ui"
```

**macOS / Linux：**

```bash
git clone git@github.com:22wink/skill-sapce.git ~/skill-sapce
mkdir -p ~/.cursor/skills
ln -s ~/skill-sapce/ui/soft-card-ui ~/.cursor/skills/soft-card-ui
```

装完后重启 Cursor，或新开一个 Agent 会话。

### 方式二：复制到个人目录

适合不想用 symlink 的场景（更新后需重新复制）：

```bash
# macOS / Linux
cp -R ui/soft-card-ui ~/.cursor/skills/soft-card-ui
```

```powershell
# Windows
Copy-Item -Recurse ui\soft-card-ui "$env:USERPROFILE\.cursor\skills\soft-card-ui"
```

### 方式三：只给某个项目用

把 skill 文件夹放到该项目的 `.cursor/skills/` 下（可复制或 symlink）。

### 方式四：CLI（若已安装 skills 工具）

```bash
npx skills add 22wink/skill-sapce/ui/soft-card-ui --agent cursor
```

在项目目录内执行 → 项目级；在别处执行 → 通常为个人级。

## 其他工具

| 工具 | 个人目录 | 说明 |
|------|----------|------|
| Cursor | `~/.cursor/skills/` | 见上文 |
| Claude Code | `~/.claude/skills/` | 同样需要每个 skill 一文件夹 + `SKILL.md` |
| Codex | `~/.codex/skills/` | 同上 |
| 通用 agents | `~/.agents/skills/` | Cursor 等也会兼容读取 |

同一套 `SKILL.md` 可 symlink 到多个目录，避免多份拷贝。

## 校验安装

确认路径类似：

```text
~/.cursor/skills/soft-card-ui/SKILL.md
```

在 Cursor Agent 里提到相关场景（例如「用 soft-card-ui 做一版追踪页」），Agent 应能发现并加载该 skill。

## 新增 skill

1. 选对分类目录，创建 `<category>/my-skill/SKILL.md`（含 `name` / `description` frontmatter）
2. 按需补充 `reference.md`、`scripts/` 等；该分类若已有 skill，可删除多余的 `.gitkeep`
3. 在本 README 的「Skill 清单」里加一行
4. 对新机或新环境执行一次安装命令（链接到叶子目录）

## 上游

- Remote: git@github.com:22wink/skill-sapce.git
- Branch: `main`
