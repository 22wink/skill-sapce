# skill-sapce

个人 Agent Skills 仓库。每个 skill 是一个独立文件夹，内含给 Agent 读的 `SKILL.md`。

克隆本仓库**不会**自动生效，需要把 skill 安装到各工具约定的目录。

## 仓库结构

```text
skill-sapce/
├── README.md                 # 给人看的安装说明
└── soft-card-ui/             # Soft Card UI 设计系统
    ├── SKILL.md              # 必需：Agent 指令
    ├── components.md
    ├── platforms.md
    └── tokens.md
```

约定：`skill-name/SKILL.md`。新增 skill 时按同样结构放在仓库根目录下。

## Skill 清单

| Skill | 用途 | 何时触发 |
|-------|------|----------|
| [`soft-card-ui`](./soft-card-ui/) | Soft Card / 物流 App 风跨端 UI | 提到 soft-card、物流追踪风、大圆角卡片、tracking UI |

## 安装到 Cursor

Cursor 会从下列目录加载 skills：

| 范围 | 路径 |
|------|------|
| 个人全局 | `~/.cursor/skills/<skill-name>/` |
| 仅当前项目 | `<project>/.cursor/skills/<skill-name>/` |

兼容目录（可选）：`~/.agents/skills/`、`~/.claude/skills/`、`~/.codex/skills/`，以及项目内对应路径。

**不要**装到 `~/.cursor/skills-cursor/`，那是 Cursor 内置技能目录。

### 方式一：符号链接（推荐，改仓库即生效）

先克隆到固定位置，再为每个 skill 建链接。

**Windows（PowerShell，管理员或已开启开发者模式）：**

```powershell
git clone https://github.com/22wink/skill-sapce.git D:\skill-sapce
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.cursor\skills"

# 示例：安装 soft-card-ui
cmd /c mklink /J "$env:USERPROFILE\.cursor\skills\soft-card-ui" "D:\skill-sapce\soft-card-ui"
```

**macOS / Linux：**

```bash
git clone https://github.com/22wink/skill-sapce.git ~/skill-sapce
mkdir -p ~/.cursor/skills
ln -s ~/skill-sapce/soft-card-ui ~/.cursor/skills/soft-card-ui
```

装完后重启 Cursor，或新开一个 Agent 会话。

### 方式二：复制到个人目录

适合不想用 symlink 的场景（更新后需重新复制）：

```bash
# macOS / Linux
cp -R soft-card-ui ~/.cursor/skills/soft-card-ui
```

```powershell
# Windows
Copy-Item -Recurse soft-card-ui "$env:USERPROFILE\.cursor\skills\soft-card-ui"
```

### 方式三：只给某个项目用

把 skill 文件夹放到该项目的 `.cursor/skills/` 下（可复制或 symlink）。

### 方式四：CLI（若已安装 skills 工具）

```bash
npx skills add 22wink/skill-sapce/soft-card-ui --agent cursor
```

在项目目录内执行 → 项目级；在别处执行 → 通常为个人级。

## 其他工具

| 工具 | 个人目录 | 说明 |
| ------ | ---------- | ------ |
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

1. 在仓库根目录创建 `my-skill/SKILL.md`（含 `name` / `description` frontmatter）
2. 按需补充 `reference.md`、`scripts/` 等
3. 在本 README 的「Skill 清单」里加一行
4. 对新机或新环境执行一次安装命令

## 上游

- Remote: <https://github.com/22wink/skill-sapce.git>
- Branch: `main`
