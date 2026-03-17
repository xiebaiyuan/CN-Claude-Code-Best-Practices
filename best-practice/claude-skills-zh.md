# Skills 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2015%2C%202026%2012%3A49%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-skills-implementation.md)

Claude Code 技能 —— frontmatter 字段与官方内置技能。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段（10）

| 字段 | 类型 | 必填 | 说明 |
|-------|------|------|-------------|
| `name` | string | 否 | 显示名称与 `/slash-command` 标识符。未填写则默认使用目录名 |
| `description` | string | 推荐 | 技能用途说明。展示在自动补全中，Claude 用于自动发现 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`、`[filename]`） |
| `disable-model-invocation` | boolean | 否 | 设为 `true` 可禁止 Claude 自动调用此技能 |
| `user-invocable` | boolean | 否 | 设为 `false` 将其隐藏在 `/` 菜单中 —— 仅作为背景知识，供 agent 预加载 |
| `allowed-tools` | string | 否 | 技能激活时可免提示使用的工具 |
| `model` | string | 否 | 技能运行时使用的模型（如 `haiku`、`sonnet`、`opus`） |
| `context` | string | 否 | 设为 `fork` 可在隔离的子代理上下文中运行 |
| `agent` | string | 否 | 当 `context: fork` 时的子代理类型（默认 `general-purpose`） |
| `hooks` | object | 否 | 该技能作用域内的生命周期 hooks |

---

## ![Official](../!/tags/official.svg) **（5）**

| # | Skill | Description |
|---|-------|-------------|
| 1 | `simplify` | 对变更代码进行复用、质量与效率审查 —— 通过重构消除重复 |
| 2 | `batch` | 批量跨文件执行命令 |
| 3 | `debug` | 调试失败的命令或代码问题 |
| 4 | `loop` | 以固定间隔运行提示或斜杠命令（最长 3 天） |
| 5 | `claude-api` | 使用 Claude API 或 Anthropic SDK 构建应用 —— 当检测到 `anthropic` / `@anthropic-ai/sdk` 导入时触发 |

另见：[Official Skills Repository](https://github.com/anthropics/skills/tree/main/skills) 了解社区维护的可安装技能。

---

## Sources

- [Claude Code Skills — Docs](https://code.claude.com/docs/en/skills)
- [Skills Discovery in Monorepos](../reports/claude-skills-for-larger-mono-repos.md)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
