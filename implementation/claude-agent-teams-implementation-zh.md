# Agent Teams 实现

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_12%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#time-orchestration"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

<p align="center">
  <img src="assets/impl-agent-teams.png" alt="Agent Teams in action — split pane mode with tmux" width="100%">
</p>

Agent Teams 会启动 **多个独立的 Claude Code 会话**，通过共享任务列表进行协作。与 subagents（在一个会话内进行隔离上下文分叉）不同，每个 teammate 都拥有完整的上下文窗口，并自动加载 CLAUDE.md、MCP 服务器与技能。

---

## ![How to Use](../!/tags/how-to-use.svg)

时间编排工作流完全由 agent team 构建。运行成品：

```bash
cd agent-teams
claude
/time-orchestrator
```

该命令触发 **Command → Agent → Skill** 流程：agent 获取迪拜当前时间，skill 渲染 SVG 时间卡片到 `agent-teams/output/dubai-time.svg`。

---

## ![How to Implement](../!/tags/how-to-implement.svg)

你可以使用 agent teams 复刻天气编排流程 —— 本例中的时间编排就是由 agent team 完成的。

### 1. 安装 [iTerm2](https://iterm2.com/) 与 tmux

```bash
brew install --cask iterm2
brew install tmux
```

### 2. 启动 iTerm2 → tmux → Claude

```bash
tmux new -s dev
CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1 claude
```

### 3. 按团队结构提示

<a id="time-orchestration"></a>

将以下提示粘贴给 Claude，使用 agent teams 启动完整的时间编排工作流：

Main prompt: **[agent-teams-prompt.md](../agent-teams/agent-teams-prompt.md)**

### Team Coordination Flow

```
┌──────────────────────────────────────────────────────────────┐
│                         LEAD (You)                           │
│       "Create an agent team to build time orchestration"     │
└──────────────────────────┬───────────────────────────────────┘
                           │ spawns team (all parallel)
              ┌────────────┼────────────┐
              ▼            ▼            ▼
   ┌────────────────┐ ┌──────────┐ ┌──────────────┐
   │ Command        │ │ Agent    │ │ Skill        │
   │ Architect      │ │ Engineer │ │ Designer     │
   │                │ │          │ │              │
   │ agent-teams/   │ │ agent-   │ │ agent-teams/ │
   │ .claude/       │ │ teams/   │ │ .claude/     │
   │ commands/      │ │ .claude/ │ │ skills/      │
   │ time-          │ │ agents/  │ │ time-svg-    │
   │ orchestrator.md│ │ time-    │ │ creator/     │
   │                │ │ agent.md │ │              │
   └───────┬────────┘ └────┬─────┘ └──────┬───────┘
           │               │              │
           ▼               ▼              ▼
   ┌──────────────────────────────────────────────────┐
   │            Shared Task List                      │
   │  ☐ Agree on data contract: {time, tz, formatted} │
   │  ☐ Command uses Agent tool (not bash)            │
   │  ☐ Agent preloads time-fetcher skill             │
   │  ☐ Skill reads time from context (no re-fetch)   │
   │  ☐ All files inside agent-teams/.claude/         │
   └──────────────────────────────────────────────────┘
                       │
                       ▼
          ┌──────────────────────────────┐
          │  cd agent-teams && claude    │
          │    /time-orchestrator        │
          │   Command → Agent → Skill    │
          └──────────────────────────────┘
```
