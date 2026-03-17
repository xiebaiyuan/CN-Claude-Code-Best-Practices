# Agents vs Commands vs Skills —— 何时用哪个

Claude Code 三种扩展机制：subagents、commands、skills 的对比。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

![Slash menu showing time-skill, time-command, and time-agent](assets/agent-command-skill-1.jpg)

---

## 一览

| | Agent | Command | Skill |
|---|---|---|---|
| **位置** | `.claude/agents/<name>.md` | `.claude/commands/<name>.md` | `.claude/skills/<name>/SKILL.md` |
| **上下文** | 独立子代理进程 | 内联（主对话） | 内联（主对话） |
| **用户可调用** | 不在 `/` 菜单，仅 Claude 或 Agent 工具调用 | 是 — `/command-name` | 是 — `/skill-name`（除非 `user-invocable: false`） |
| **Claude 自动调用** | 是 — 通过 `description` | 否 | 是 — 通过 `description`（除非 `disable-model-invocation: true`） |
| **接受参数** | 通过 `prompt` 参数 | `$ARGUMENTS`、`$0`、`$1` | `$ARGUMENTS`、`$0`、`$1` |
| **动态上下文注入** | 否 | 是 — `` !`command` `` | 是 — `` !`command` `` |
| **独立上下文窗口** | 是（隔离） | 否（共享） | 否（共享，除非 `context: fork`） |
| **模型覆盖** | `model:` frontmatter | `model:` frontmatter | `model:` frontmatter |
| **工具限制** | `tools:` / `disallowedTools:` | `allowed-tools:` | `allowed-tools:` |
| **Hooks** | `hooks:` frontmatter | — | `hooks:` frontmatter |
| **Memory** | `memory:` frontmatter（user/project/local） | — | — |
| **可预加载技能** | 是 — `skills:` frontmatter | — | — |
| **MCP 服务器** | `mcpServers:` frontmatter | — | — |

---

## 何时使用

### 适合用 Agent：

- 任务 **自主且多步骤** —— 需要探索、决策与执行
- 需要 **上下文隔离** —— 避免污染主对话
- 需要 **跨会话持久记忆**（例如会学习的代码审阅）
- 需要 **预加载领域知识** 而不污染主上下文
- 需要 **后台运行** 或 **git worktree**
- 需要 **工具限制** 或不同权限模式（如 `acceptEdits`、`plan`）

**示例**：`weather-agent` —— 自主获取天气，使用预加载 `weather-fetcher` 技能。

### 适合用 Command：

- 需要 **用户显式触发的入口**
- 工作流需要 **编排** 其他 agents 或 skills
- 想保持 **上下文精简** —— 命令内容仅在触发时注入

**示例**：`weather-orchestrator` —— 用户触发后询问单位、调用 agent、再调用 SVG skill。

### 适合用 Skill：

- 希望 **Claude 自动调用**（基于语义匹配）
- 任务是 **可复用的过程**（可被命令/agent/Claude 调用）
- 需要 **预加载到 agent** 作为领域知识

**示例**：`weather-svg-creator` —— Claude 自动调用或由命令调用。

---

## Command → Agent → Skill 架构

本仓库展示了分层编排模式：

```
User triggers /command
    ↓
Command orchestrates the workflow
    ↓
Command invokes Agent (separate context, autonomous)
    ↓
Agent uses preloaded Skill (domain knowledge)
    ↓
Command invokes Skill (inline, for output generation)
```

**具体示例** —— 天气系统：

```
/weather-orchestrator (command — entry point, asks C/F)
    ↓
weather-agent (agent — fetches temperature autonomously)
    ├── weather-fetcher (agent skill — preloaded API instructions)
    ↓
weather-svg-creator (skill — creates SVG inline)
```

---

## Frontmatter 对比

### Agent Frontmatter

```yaml
---
name: my-agent
description: Use this agent PROACTIVELY when...
tools: Read, Write, Edit, Bash
model: sonnet
maxTurns: 10
permissionMode: acceptEdits
memory: user
skills:
  - my-skill
---
```

### Command Frontmatter

```yaml
---
description: Do something useful
argument-hint: [issue-number]
allowed-tools: Read, Edit, Bash(gh *)
model: sonnet
---
```

### Skill Frontmatter

```yaml
---
name: my-skill
description: Do something when the user asks for...
argument-hint: [file-path]
disable-model-invocation: false
user-invocable: true
allowed-tools: Read, Grep, Glob
model: sonnet
context: fork
agent: general-purpose
---
```

---

## 关键区别

### 自动调用

| 机制 | Claude 可自动调用？ | 如何禁止 |
|-----------|------------------------|----------------|
| Agent | 是 — 通过 `description`（使用 “PROACTIVELY” 可增强） | 移除或弱化描述 |
| Command | 否 — 始终由用户 `/` 触发 | N/A |
| Skill | 是 — 通过 `description` | `disable-model-invocation: true` |

### `/` 菜单可见性

| 机制 | 是否出现在 `/` 菜单 | 隐藏方式 |
|-----------|---------------------|-------------|
| Agent | 否 | N/A |
| Command | 是（始终） | 无法隐藏 |
| Skill | 默认是 | `user-invocable: false` |

### 上下文隔离

| 机制 | 是否独立上下文 | 配置方式 |
|-----------|---------------------|-----------------|
| Agent | 始终 | 内建行为 |
| Command | 否 | N/A |
| Skill | 可选 | `context: fork` |

---

## 示例：“现在几点？”

本仓库为同一任务定义了三种机制 —— 显示 PKT 时间。当用户输入 **“What is the current time?”** 且未显式调用 `/` 命令：

| 机制 | 会触发？ | 原因 |
|-----------|--------------|---------------|
| `time-command` | 否 | Command 不会自动调用，必须显式 `/time-command` |
| `time-agent` | **可能会** | `description` 匹配意图，可被 Agent 工具触发，但会启用独立上下文 |
| `time-skill` | **最可能** | `description` 明确匹配，Skill 内联执行更轻量 |

### 解析优先级

当多个机制匹配同一意图时，Claude 倾向选择 **最轻量** 的方案：

```
1. Skill（内联，无上下文开销）     ← 优先
2. Agent（独立上下文，适合复杂任务）
3. Command（不会自动触发）
```

### 如果 skill 设置 `disable-model-invocation: true`？

Claude 将无法自动调用 skill，可能改为启动 `time-agent` —— 代价是开启独立上下文来完成一个简单 bash 命令。

### 如果 skill 和 agent 都禁用自动调用？

则不会自动触发任何机制。Claude 可能直接使用通用知识执行 `TZ='Asia/Karachi' date`，用户需显式输入 `/time-command` 或 `/time-skill`。

![Claude auto-invoking time-skill when user asks "What is the current time?"](assets/agent-command-skill-2.png)

---

## Sources

- [Claude Code Skills — Docs](https://code.claude.com/docs/en/skills)
- [Claude Code Sub-agents — Docs](https://code.claude.com/docs/en/sub-agents)
- [Claude Code Slash Commands — Docs](https://code.claude.com/docs/en/slash-commands)
- [Skills Best Practice](../best-practice/claude-skills.md)
- [Commands Best Practice](../best-practice/claude-commands.md)
- [Sub-agents Best Practice](../best-practice/claude-subagents.md)
