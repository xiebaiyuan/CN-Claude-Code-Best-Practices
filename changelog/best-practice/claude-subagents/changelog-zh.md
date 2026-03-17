# Subagents Report — 变更历史

## 状态图例

| 状态 | 含义 |
|--------|---------|
| ✅ `COMPLETE (reason)` | 已完成并成功解决 |
| ❌ `INVALID (reason)` | 发现无效、不适用或为有意设计 |
| ✋ `ON HOLD (reason)` | 暂缓处理（等待外部依赖或用户决策） |

---

## [2026-02-28 03:22 PM PKT] Claude Code v2.1.63

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Agents Table | 将 `workflow-changelog-claude-agents-frontmatter-agent` 加入 Agents in This Repository 表 | ✅ COMPLETE（已添加，model: opus，继承全部工具，无 skills/memory） |
| 2 | HIGH | Agents Table | 修正 presentation-curator 的 skills 列，添加 `presentation/` 前缀 | ✅ COMPLETE |
| 3 | MED | Field Documentation | 添加 `color` 字段说明：功能存在但官方表未列出 | ✅ COMPLETE |
| 4 | MED | Invocation Section | 扩充调用方式：--agents CLI、/agents、claude agents CLI、agent resumption | ✅ COMPLETE |

---

## [2026-03-07 08:35 AM PKT] Claude Code v2.1.71

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Broken Link | 修复 agent memory 链接至 `reports/claude-agent-memory.md` | ✅ COMPLETE |
| 2 | HIGH | Changed Behavior | `tools` 描述从 `Task(agent_type)` → `Agent(agent_type)` | ✅ COMPLETE |
| 3 | HIGH | Changed Behavior | 调用段落：Task 工具 → Agent 工具 | ✅ COMPLETE |
| 4 | HIGH | Example Update | 示例 `Task(monitor, rollback)` → `Agent(monitor, rollback)` | ✅ COMPLETE |
| 5 | HIGH | Built-in Agent | 添加 `Bash` agent 到 Official Claude Agents 表 | ✅ COMPLETE |
| 6 | HIGH | Agents Table | 添加 `workflow-concepts-agent` | ✅ COMPLETE |
| 7 | HIGH | Agents Table | 添加 `workflow-claude-settings-agent` | ✅ COMPLETE |
| 8 | MED | Built-in Agent | 修正 `statusline-setup` model：inherit → Sonnet | ✅ COMPLETE |
| 9 | MED | Built-in Agent | 修正 `claude-code-guide` model：inherit → Haiku | ❌ NOT APPLICABLE（已从表中移除） |
| 10 | MED | Agents Table | 修正 `weather-agent` color：teal → green | ✅ COMPLETE |
| 11 | MED | Invocation | 添加 `--agent <name>` 到调用方式表 | ✅ COMPLETE |
| 12 | MED | Changed Behavior | Official Claude Agents 表头中 “Task tool” → “Agent tool” | ✅ COMPLETE |
| 13 | MED | Cross-File | 更新 CLAUDE.md 中 `Task(...)` → `Agent(...)` | ✅ COMPLETE |

---

## [2026-03-12 12:17 PM PKT] Claude Code v2.1.74

未检测到漂移 —— 报告与官方文档完全一致。所有 13 个 frontmatter 字段与 6 个内置 agents 匹配。

---

## [2026-03-13 04:21 PM PKT] Claude Code v2.1.74

未检测到漂移 —— 报告与官方文档完全一致。所有 13 个 frontmatter 字段与 6 个内置 agents 匹配。

---

## [2026-03-15 12:50 PM PKT] Claude Code v2.1.76

未检测到漂移 —— 报告与官方文档完全一致。所有 13 个 frontmatter 字段与 6 个内置 agents 匹配。
