# Claude Code：Agent Memory Frontmatter

为子代理提供持久记忆 —— 让 agent 在会话之间学习、记忆并积累知识。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 概览

在 **Claude Code v2.1.33**（2026 年 2 月）中引入 `memory` frontmatter 字段，允许每个 subagent 拥有自己的持久化 Markdown 知识库。在此之前，每次 agent 调用都会从零开始。

```yaml
---
name: code-reviewer
description: Reviews code for quality and best practices
tools: Read, Write, Edit, Bash
model: sonnet
memory: user
---

You are a code reviewer. As you review code, update your agent memory with
patterns, conventions, and recurring issues you discover.
```

---

## Memory Scopes

| Scope | 存储位置 | 版本控制 | 共享 | 适用场景 |
|-------|-----------------|-------------------|--------|----------|
| `user` | `~/.claude/agent-memory/<agent-name>/` | 否 | 否 | 跨项目知识（推荐默认） |
| `project` | `.claude/agent-memory/<agent-name>/` | 是 | 是 | 团队共享的项目知识 |
| `local` | `.claude/agent-memory-local/<agent-name>/` | 否（git 忽略） | 否 | 项目相关的个人知识 |

这些作用域与设置层级一致（`~/.claude/settings.json` → `.claude/settings.json` → `.claude/settings.local.json`）。

---

## 工作方式

1. **启动时**：`MEMORY.md` 的前 200 行注入 agent 系统提示
2. **工具权限**：自动启用 `Read`、`Write`、`Edit` 以管理记忆
3. **执行中**：agent 可自由读写记忆目录
4. **整理**：`MEMORY.md` 超过 200 行时，将细节移至主题文件

```
~/.claude/agent-memory/code-reviewer/     # user 作用域示例
├── MEMORY.md                              # 主文件（前 200 行加载）
├── react-patterns.md                      # 主题文件
└── security-checklist.md                  # 主题文件
```

---

## Agent Memory 与其他记忆系统对比

| 系统 | 谁写入 | 谁读取 | 作用域 |
|--------|-----------|-----------|-------|
| **CLAUDE.md** | 你（手动） | 主 Claude + 所有 agents | 项目 |
| **Auto-memory** | 主 Claude（自动） | 仅主 Claude | 每项目、每用户 |
| **`/memory` 命令** | 你（编辑） | 仅主 Claude | 每项目、每用户 |
| **Agent memory** | agent 自己 | 仅该 agent | 可配置（user/project/local） |

这些系统是 **互补** 的 —— agent 同时读取 CLAUDE.md（项目上下文）与自身记忆（agent 专属知识）。

---

## 实际示例

```yaml
---
name: api-developer
description: Implement API endpoints following team conventions
tools: Read, Write, Edit, Bash
model: sonnet
memory: project
skills:
  - api-conventions
  - error-handling-patterns
---

Implement API endpoints. Follow the conventions from your preloaded skills.
As you work, save architectural decisions and patterns to your memory.
```

这结合了 **skills**（启动时静态知识）与 **memory**（逐步积累的动态知识）。

---

## Tips

- **提示使用记忆** —— 在提示词中加入："Before starting, review your memory. After completing, update your memory with what you learned."
- **调用时检查记忆**："Review this PR, and check your memory for patterns you've seen before."
- **选择合适 scope** —— `user` 用于跨项目，`project` 用于团队共享，`local` 用于个人

---

## Sources

- [Create custom subagents — Claude Code Docs](https://code.claude.com/docs/en/sub-agents)
- [Manage Claude's memory — Claude Code Docs](https://code.claude.com/docs/en/memory)
- [Claude Code v2.1.33 Release Notes](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
