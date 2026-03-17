# Sub-agents 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2015%2C%202026%2012%3A50%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-subagents-implementation.md)

Claude Code 子代理 —— frontmatter 字段与官方内置代理类型。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段（14）

| 字段 | 类型 | 必填 | 说明 |
|-------|------|------|-------------|
| `name` | string | 是 | 唯一标识符，使用小写字母与连字符 |
| `description` | string | 是 | 何时调用。使用 `"PROACTIVELY"` 表示由 Claude 自动调用 |
| `tools` | string/list | 否 | 工具白名单（如 `Read, Write, Edit, Bash`）。不填则继承所有工具。支持 `Agent(agent_type)` 语法限制可派生的子代理；旧的 `Task(agent_type)` 别名仍可用 |
| `disallowedTools` | string/list | 否 | 禁用的工具，从继承或指定列表中移除 |
| `model` | string | 否 | 模型别名：`haiku`、`sonnet`、`opus` 或 `inherit`（默认 `inherit`） |
| `permissionMode` | string | 否 | 权限模式：`default`、`acceptEdits`、`dontAsk`、`bypassPermissions`、`plan` |
| `maxTurns` | integer | 否 | 子代理在停止前的最大 agentic 回合数 |
| `skills` | list | 否 | 启动时预加载到 agent 上下文的技能（完整内容注入） |
| `mcpServers` | list | 否 | 子代理专属 MCP 服务器 —— 服务器名称字符串或 `{name: config}` 内联对象 |
| `hooks` | object | 否 | 子代理作用域的生命周期 hooks。支持所有事件；常用的是 `PreToolUse`、`PostToolUse`、`Stop` |
| `memory` | string | 否 | 持久化记忆范围：`user`、`project`、`local` |
| `background` | boolean | 否 | 设为 `true` 作为后台任务运行（默认 `false`） |
| `isolation` | string | 否 | 设为 `"worktree"` 以在临时 git worktree 中运行（无变更会自动清理） |
| `color` | string | 否 | CLI 输出颜色（如 `green`、`magenta`）。功能存在但不在官方 frontmatter 表中，只在交互式快速入门中提及 |

---

## ![Official](../!/tags/official.svg) **（6）**

| # | Agent | Model | Tools | Description |
|---|-------|-------|-------|-------------|
| 1 | `general-purpose` | inherit | All | 复杂多步骤任务的默认代理类型 —— 研究、代码搜索与自主工作 |
| 2 | `Explore` | haiku | 只读（无 Write、Edit） | 快速代码库搜索与探索 —— 适用于查找文件、搜索代码、回答代码库问题 |
| 3 | `Plan` | inherit | 只读（无 Write、Edit） | 计划模式下的前期研究 —— 探索代码库并设计实现方案后再动手 |
| 4 | `Bash` | inherit | Bash | 在独立上下文中运行终端命令 |
| 5 | `statusline-setup` | sonnet | Read, Edit | 配置用户的 Claude Code 状态栏设置 |
| 6 | `claude-code-guide` | haiku | Glob, Grep, Read, WebFetch, WebSearch | 解答 Claude Code 功能、Agent SDK 与 Claude API 相关问题 |

---

## Sources

- [Create custom subagents — Claude Code Docs](https://code.claude.com/docs/en/sub-agents)
- [CLI reference — Claude Code Docs](https://code.claude.com/docs/en/cli-reference)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
