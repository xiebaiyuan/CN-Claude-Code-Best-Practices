# Claude Code：全局 vs 项目级特性

全面对比 Claude Code 哪些特性仅存在于全局（`~/.claude/`），哪些同时支持全局与项目级（`.claude/`）。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 目录

1. [Overview](#overview)
2. [Global-Only Features](#global-only-features)
3. [Dual-Scope Features](#dual-scope-features)
4. [Settings Precedence](#settings-precedence)
5. [Directory Structure Comparison](#directory-structure-comparison)
6. [Tasks System](#tasks-system)
7. [Agent Teams](#agent-teams)
8. [Design Principles](#design-principles)
9. [Sources](#sources)

---

## Overview

Claude Code 使用 **作用域层级**：部分特性在全局（`~/.claude/`）与项目（`.claude/`）都存在，而另一些仅全局。设计原则是：*个人状态* 或 *跨项目协作* 放在全局；*可团队共享的项目配置* 可放在项目级。

- `~/.claude/` 是 **用户级**（全局、适用于所有项目）
- `.claude/` 是 **项目级**（仅当前项目）

---

## Global-Only Features

以下只存在于 `~/.claude/`，不可项目级：

| 特性 | 位置 | 目的 |
|---------|----------|---------|
| **Tasks** | `~/.claude/tasks/` | 跨会话与跨 agent 的持久任务列表 |
| **Agent Teams** | `~/.claude/teams/` | 多 agent 协作配置（实验性，2026 年 2 月） |
| **Auto Memory** | `~/.claude/projects/<hash>/memory/` | Claude 自动记录的项目学习（个人，不共享） |
| **Credentials & OAuth** | 系统钥匙串 + `~/.claude.json` | API key、OAuth token（不进入项目文件） |
| **Keybindings** | `~/.claude/keybindings.json` | 自定义快捷键 |
| **MCP User Servers** | `~/.claude.json`（`mcpServers`） | 跨项目个人 MCP 服务器 |
| **Preferences/Cache** | `~/.claude.json` | 主题、模型、输出风格、会话状态 |

---

## Dual-Scope Features

以下在全局与项目都存在，**项目级优先**：

| 特性 | 全局（`~/.claude/`） | 项目（`.claude/`） | 优先级 |
|---------|----------------------|---------------------|------------|
| **CLAUDE.md** | `~/.claude/CLAUDE.md` | `./CLAUDE.md` 或 `.claude/CLAUDE.md` | 项目覆盖全局 |
| **Settings** | `~/.claude/settings.json` | `.claude/settings.json` + `.claude/settings.local.json` | 项目 > 全局 |
| **Rules** | `~/.claude/rules/*.md` | `.claude/rules/*.md` | 项目覆盖 |
| **Agents/Subagents** | `~/.claude/agents/*.md` | `.claude/agents/*.md` | 项目覆盖 |
| **Commands** | `~/.claude/commands/*.md` | `.claude/commands/*.md` | 均可用 |
| **Skills** | `~/.claude/skills/` | `.claude/skills/` | 均可用 |
| **Hooks** | `~/.claude/hooks/` | `.claude/hooks/` | 均执行 |
| **MCP Servers** | `~/.claude.json`（用户级） | `.mcp.json`（项目级） | 三层：local > project > user |

---

## Settings Precedence

用户可写设置的优先级（高 → 低）：

| 优先级 | 位置 | 作用域 | 版本控制 | 目的 |
|----------|----------|-------|-----------------|---------|
| 1 | 命令行参数 | 会话 | N/A | 单次会话覆盖 |
| 2 | `.claude/settings.local.json` | 项目 | 否（git 忽略） | 个人项目设置 |
| 3 | `.claude/settings.json` | 项目 | 是（提交） | 团队共享设置 |
| 4 | `~/.claude/settings.local.json` | 用户 | N/A | 全局个人覆盖 |
| 5 | `~/.claude/settings.json` | 用户 | N/A | 全局个人设置 |

政策层：`managed-settings.json` 为组织强制策略，不能被本地覆盖。

**重要**：`deny` 规则拥有最高安全优先级，不能被低优先级的 allow/ask 规则覆盖。

---

## Directory Structure Comparison

### 全局作用域（`~/.claude/`）

```
~/.claude/
├── settings.json              # 用户级设置（所有项目）
├── settings.local.json        # 个人覆盖
├── CLAUDE.md                  # 用户记忆（所有项目）
├── agents/                    # 用户 subagents（所有项目可用）
│   └── *.md
├── rules/                     # 用户级规则
│   └── *.md
├── commands/                  # 用户级命令
│   └── *.md
├── skills/                    # 用户级技能
│   └── */SKILL.md
├── tasks/                     # 仅全局：任务列表
│   └── {task-list-id}/
├── teams/                     # 仅全局：agent teams 配置
│   └── {team-name}/
│       └── config.json
├── projects/                  # 仅全局：项目 auto-memory
│   └── {project-hash}/
│       └── memory/
│           ├── MEMORY.md
│           └── *.md
├── keybindings.json           # 仅全局：快捷键
└── hooks/                     # 用户级 hooks
    ├── scripts/
    └── config/

~/.claude.json                 # 仅全局：MCP 服务器、OAuth、偏好、缓存
```

### 项目作用域（`.claude/`）

```
.claude/
├── settings.json              # 团队共享设置
├── settings.local.json        # 个人项目覆盖（git 忽略）
├── CLAUDE.md                  # 项目记忆（也可用 ./CLAUDE.md）
├── agents/                    # 项目 subagents
│   └── *.md
├── rules/                     # 项目级规则
│   └── *.md
├── commands/                  # 自定义斜杠命令
│   └── *.md
├── skills/                    # 自定义技能
│   └── {skill-name}/
│       ├── SKILL.md
│       └── supporting-files/
├── hooks/                     # 项目级 hooks
│   ├── scripts/
│   └── config/
└── plugins/                   # 已安装插件

.mcp.json                      # 项目级 MCP 服务器（仓库根目录）
```

---

## Tasks System

在 **Claude Code v2.1.16**（2026-01-22）引入，替代旧 TodoWrite。

### 存储

Tasks 存储于本地文件系统 `~/.claude/tasks/`（非云端），可审计、可版本管理、可崩溃恢复。

### 工具

| 工具 | 目的 |
|------|---------|
| **TaskCreate** | 创建任务（含 subject/description/activeForm） |
| **TaskGet** | 获取指定任务完整信息 |
| **TaskUpdate** | 修改状态、设定负责人、依赖或删除 |
| **TaskList** | 列出任务及状态 |

### 生命周期

```
pending  →  in_progress  →  completed
```

### 依赖管理

任务可通过 `addBlockedBy`/`addBlocks` 建立依赖图，避免提前执行。

### 多会话协作

```bash
CLAUDE_CODE_TASK_LIST_ID=my-project-tasks claude
```

共享 ID 的会话可实时同步任务，用于并行协作与会话恢复。

### 与旧 Todos 的区别

| 特性 | 旧 Todos | 新 Tasks |
|---------|-----------|-----------|
| 作用域 | 单会话 | 跨会话、跨 agent |
| 依赖 | 无 | 有依赖图 |
| 存储 | 内存 | 文件系统（`~/.claude/tasks/`） |
| 持久性 | 会话结束丢失 | 重启/崩溃后保留 |
| 多会话 | 不可用 | 通过 `CLAUDE_CODE_TASK_LIST_ID` |

---

## Agent Teams

**2026 年 2 月 5 日**宣布的实验功能。Agent Teams 允许多个 Claude Code 会话协同工作。

### 启用方式

```json
// In ~/.claude/settings.json
{
  "env": {
    "CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS": "1"
  }
}
```

### 配置

团队配置位于 `~/.claude/teams/{team-name}/`，支持以下模式：

| 模式 | 描述 | 要求 |
|------|-------------|--------------|
| **In-process**（默认） | 所有队友在当前终端运行 | 无 |
| **Split panes** | 每个队友独立 pane | tmux 或 iTerm2（不支持 VS Code 终端） |

---

## Design Principles

全局 vs 双作用域遵循清晰模式：

| 类别 | 作用域 | 理由 |
|----------|-------|-----------|
| **协作状态**（tasks、teams） | 仅全局 | 需跨项目持久化 |
| **安全状态**（凭证、OAuth） | 仅全局 | 防止误提交到仓库 |
| **个人学习**（auto-memory） | 仅全局 | 用户个人，非团队共享 |
| **输入偏好**（快捷键） | 仅全局 | 用户肌肉记忆 |
| **配置**（settings、rules、agents） | 双层 | 团队需共享项目行为 |
| **工作流定义**（commands、skills） | 双层 | 可个人或团队共享 |

Auto-memory（`~/.claude/projects/<hash>/memory/`）是一个混合案例：内容针对具体项目，但属于个人学习，因此存储在全局。

---

## Sources

- [Claude Code Settings Documentation](https://code.claude.com/docs/en/settings)
- [Orchestrate Teams of Claude Code Sessions](https://code.claude.com/docs/en/agent-teams)
- [What are Tasks in Claude Code - ClaudeLog](https://claudelog.com/faqs/what-are-tasks-in-claude-code/)
- [Claude Code Task Management - ClaudeFast](https://claudefa.st/blog/guide/development/task-management)
- [Claude Code Tasks Update - VentureBeat](https://venturebeat.com/orchestration/claude-codes-tasks-update-lets-agents-work-longer-and-coordinate-across)
- [Where Are Claude Code Global Settings - ClaudeLog](https://claudelog.com/faqs/where-are-claude-code-global-settings/)
- [Claude Opus 4.6 Agent Teams - VentureBeat](https://venturebeat.com/technology/anthropics-claude-opus-4-6-brings-1m-token-context-and-agent-teams-to-take)
- [How to Set Up Claude Code Agent Teams (Full Walkthrough) - r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/comments/1qz8tyy/how_to_set_up_claude_code_agent_teams_full/)
- [Anthropic replaced Claude Code's old 'Todos' with Tasks - r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/comments/1qkjznp/anthropic_replaced_claude_codes_old_todos_with/)
