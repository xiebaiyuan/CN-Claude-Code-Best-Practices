# Changelog — README CONCEPTS Section

跟踪 README CONCEPTS 表与官方 Claude Code 文档之间的漂移。

## 状态图例

| 状态 | 含义 |
|--------|---------|
| ✅ `COMPLETE (reason)` | 已完成并成功解决 |
| ❌ `INVALID (reason)` | 发现无效、不适用或为有意设计 |
| ✋ `ON HOLD (reason)` | 暂缓处理（等待外部依赖或用户决策） |

---

## [2026-03-02 11:14 AM PKT] Claude Code v2.1.63

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Broken URL | 修复 Permissions URL `/iam` → `/permissions` | ✅ COMPLETE |
| 2 | HIGH | Missing Concept | 增加 Agent Teams 行 | ✅ COMPLETE |
| 3 | HIGH | Missing Concept | 增加 Keybindings 行 | ✅ COMPLETE |
| 4 | HIGH | Missing Concept | 增加 Model Configuration 行 | ✅ COMPLETE |
| 5 | HIGH | Missing Concept | 增加 Auto Memory 行 | ✅ COMPLETE |
| 6 | HIGH | Stale Anchor | 修复 Rules URL anchor | ✅ COMPLETE |
| 7 | MED | Missing Concept | 增加 Checkpointing 行 | ✅ COMPLETE |
| 8 | MED | Missing Concept | 增加 Status Line 行 | ✅ COMPLETE |
| 9 | MED | Missing Concept | 增加 Remote Control 行 | ✅ COMPLETE |
| 10 | MED | Missing Concept | 增加 Fast Mode 行 | ✅ COMPLETE |
| 11 | MED | Missing Concept | 增加 Headless Mode 行 | ✅ COMPLETE |
| 12 | LOW | Changed Description | Memory 描述补充 auto memory | ✅ COMPLETE |
| 13 | LOW | Changed Location | MCP Servers 位置包含 `.mcp.json` | ✅ COMPLETE |
| 14 | LOW | Missing Badge | Hooks 行添加 Implemented badge | ✅ COMPLETE |

---

## [2026-03-02 11:57 AM PKT] Claude Code v2.1.63

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Table Consolidation | CONCEPTS 表从 22 行合并为 10 行 | ✅ COMPLETE |
| 2 | MED | Merged Concept | Marketplaces 合并进 Plugins | ✅ COMPLETE |
| 3 | MED | Merged Concept | Agent Teams 合并进 Sub-Agents | ✅ COMPLETE |
| 4 | MED | Merged Concept | Permissions/Model Config/Output Styles/Sandboxing/Keybindings/Status Line/Fast Mode 合并进 Settings | ✅ COMPLETE |
| 5 | MED | Merged Concept | Auto Memory 与 Rules 合并进 Memory | ✅ COMPLETE |
| 6 | MED | Merged Concept | Headless Mode 合并进 Remote Control | ✅ COMPLETE |
| 7 | LOW | Reorder | 按逻辑分组重排 | ✅ COMPLETE |

---

## [2026-03-07 08:40 AM PKT] Claude Code v2.1.71

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Broken URL | 修复 TIPS 中 `context-management` → `interactive-mode` | ✅ COMPLETE |
| 2 | HIGH | Broken URL | 修复 TIPS 中 `model-configuration` → `model-config` | ✅ COMPLETE |
| 3 | HIGH | Broken URL | 修复 TIPS 中 `usage-billing` → `costs` | ✅ COMPLETE |
| 4 | HIGH | Broken URL | 移除 STARTUPS 中 `cowork` 链接 | ✅ COMPLETE |
| 5 | HIGH | Missing Concept | 在 CONCEPTS 与 Hot 中添加 Scheduled Tasks | ✅ COMPLETE |
| 6 | MED | Changed Location | Agent Teams 位置改为 built-in (env var) | ✅ COMPLETE |

---

## [2026-03-10 01:18 PM PKT] Claude Code v2.1.72

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Broken URL | Commands URL `/slash-commands` → `/skills` | ❌ INVALID |
| 2 | HIGH | Broken URL | TIPS 中 `/slash-commands` → `/skills` | ❌ INVALID |
| 3 | MED | Missing Inline Link | CLI Startup Flags 行补充 `/interactive-mode` | ✅ COMPLETE |
| 4 | MED | Missing Inline Link | Settings 行补充 `/costs` | ❌ INVALID |
| 5 | LOW | Missing Concept | 新增 IDE Integrations 行 | ❌ INVALID |
| 6 | HIGH | Missing Concept | Hot 表添加 Code Review 行 | ✅ COMPLETE |
| 7 | MED | New Badge | 创建 `!/tags/beta.svg` 并添加到 Code Review 与 Agent Teams | ✅ COMPLETE |
| 8 | MED | Reorder | Hot 表按发布时间排序 | ✅ COMPLETE |

---

## [2026-03-12 12:22 PM PKT] Claude Code v2.1.74

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Broken URL | Commands URL `/slash-commands` → `/skills` | ❌ INVALID（仍可访问） |
| 2 | LOW | Verification | 外链全部有效 | ✅ COMPLETE |
| 3 | LOW | Verification | 本地 badge 路径全部有效 | ✅ COMPLETE |
| 4 | LOW | Verification | Memory anchor 有效 | ✅ COMPLETE |
| 5 | LOW | Verification | CONCEPTS 描述无漂移 | ✅ COMPLETE |

---

## [2026-03-15 12:48 PM PKT] Claude Code v2.1.76

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Stale URL | Commands URL `/slash-commands` 指向 Skills | ❌ INVALID（仍可访问） |
| 2 | MED | Missing Badges | Remote Control 行缺少 badge | ✅ COMPLETE |
| 3 | LOW | Naming | “Sub-Agents” → “Subagents” | ✅ COMPLETE |
| 4 | LOW | Verification | 外链全部有效 | ✅ COMPLETE |
| 5 | LOW | Verification | 本地 badge 路径有效 | ✅ COMPLETE |
| 6 | LOW | Verification | Memory anchor 有效 | ✅ COMPLETE |
| 7 | LOW | Verification | CONCEPTS 描述无漂移 | ✅ COMPLETE |
