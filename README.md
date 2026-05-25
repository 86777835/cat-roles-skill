# cat-roles-skill

Claude Code 角色制开发模式 — 5 喵团队协作系统。

自动创建 5 个专业角色 Agent（产品喵 / 开发喵 / 测试喵 / 运维喵 / 运营喵），配合可视化需求看板（Kanban）和自动派单 cron，实现从需求到交付的完整闭环。

## 特性

- **5 角色自动建队** — 产品喵出需求 → 开发喵实现 → 测试喵验证 → 运维喵部署 → 运营喵维护
- **可视化需求看板** — 3 列 Kanban（待开发 / 进行中 / 已完成）+ 验收 + 归档 + 返工
- **自动派单** — 20 分钟 cron 轮询，空闲时自动按优先级抓活
- **角色锁** — 每个 Agent 只做自己职责范围内的事，不抢不代不转发
- **跨项目隔离** — 基于 `$PWD` hash 的 namespace，多项目并行不冲突

## 前置依赖

### 1. Claude Code

确保已安装 [Claude Code CLI](https://docs.anthropic.com/en/docs/claude-code)：

```bash
npm install -g @anthropic-ai/claude-code
```

### 2. tmux

Teams 模式依赖 tmux 管理多 Agent 窗口：

```bash
# macOS
brew install tmux

# Ubuntu / Debian
sudo apt install tmux

# CentOS / RHEL
sudo yum install tmux
```

验证安装：

```bash
tmux -V
```

### 3. Agent Teams 功能

确认 `~/.claude/settings.json` 包含以下配置：

```json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  },
  "teammateMode": "tmux"
}
```

### 4. tmux 窗口编号（推荐）

在 `~/.tmux.conf` 中添加：

```
set -g base-index 1
set -g renumber-windows on
```

加载配置：

```bash
tmux source-file ~/.tmux.conf 2>/dev/null || true
```

## 安装

### 方式一：全局安装（推荐）

所有项目都能使用 `/cat-roles`，skill 文件统一管理。

```bash
# 克隆到 Claude Code 全局 skills 目录
git clone https://github.com/86777835/cat-roles-skill.git ~/.claude/skills/cat-roles

# 验证
ls ~/.claude/skills/cat-roles/SKILL.md
```

### 方式二：项目级安装

只在当前项目生效，适合团队协作或需要定制 skill 的场景。

```bash
# 在项目根目录下
mkdir -p .claude/skills
git clone https://github.com/86777835/cat-roles-skill.git .claude/skills/cat-roles

# 验证
ls .claude/skills/cat-roles/SKILL.md
```

## 使用

在任意项目目录下启动 Claude Code，输入：

```
/cat-roles
```

系统会自动执行：

1. 环境检查（tmux / Agent Teams / tmux 模式）
2. 计算项目隔离 namespace
3. 检测项目状态（全新 / 恢复 / 不完整）
4. 选择模式（Teams 推荐 / 单角色）
5. 创建 5 喵团队 + 注入角色定义
6. 初始化需求看板（`需求池.html`）
7. 启动自动派单 cron（可选）

### 启动后

- **浏览器打开** `需求池.html` 查看可视化看板
- **直接用人话告诉 team-lead**（主 Claude Code 会话）你要做什么
- team-lead 自动拆需求、建任务、派给对应喵角色
- 看板上点「验收」按钮验收已完成的需求

### tmux 操作

| 快捷键 | 功能 |
|--------|------|
| `Ctrl+a 1` | 切换到产品喵窗口 |
| `Ctrl+a 2` | 切换到开发喵窗口 |
| `Ctrl+a 3` | 切换到测试喵窗口 |
| `Ctrl+a 4` | 切换到运维喵窗口 |
| `Ctrl+a 5` | 切换到运营喵窗口 |
| `Ctrl+a d` | 脱离 tmux（后台运行） |
| `tmux attach` | 重新连接 |

## 项目结构

```
cat-roles-skill/
├── SKILL.md              # 核心技能定义（启动流程、角色、规则）
├── roles.json            # 角色配置
├── 需求池.html.template  # 看板 HTML 模板
└── README.md             # 本文件
```

## 工作原理

```
用户 → team-lead（CEO 头脑）→ 路由任务 → 对应喵角色
                                    ↓
                              需求看板（HTML）
                                    ↓
                              cron 自动派单（20min）
```

- **team-lead** 不直接干活，只做路由、协调、与用户沟通
- **喵角色** 只认领自己名下的任务，完成后自动找下一个
- **通信规则**：所有喵只和 team-lead 沟通，不跨角色直接派活

## 卸载

```bash
# 全局安装
rm -rf ~/.claude/skills/cat-roles

# 项目级安装（在项目根目录下）
rm -rf .claude/skills/cat-roles
```

## License

MIT
