# Commands Report — 变更历史

## 状态图例

| 状态 | 含义 |
|--------|---------|
| ✅ `COMPLETE (reason)` | 已完成并成功解决 |
| ❌ `INVALID (reason)` | 发现无效、不适用或为有意设计 |
| ✋ `ON HOLD (reason)` | 暂缓处理（等待外部依赖或用户决策） |

---

## [2026-03-13 04:23 PM PKT] Claude Code v2.1.74

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | New Field | 添加 `name` 到 frontmatter 表 | ❌ INVALID（skill 专用字段） |
| 2 | HIGH | New Field | 添加 `disable-model-invocation` | ❌ INVALID（skill 专用字段） |
| 3 | HIGH | New Field | 添加 `user-invocable` | ❌ INVALID（skill 专用字段） |
| 4 | HIGH | New Field | 添加 `context` | ❌ INVALID（skill 专用字段） |
| 5 | HIGH | New Field | 添加 `agent` | ❌ INVALID（skill 专用字段） |
| 6 | HIGH | New Field | 添加 `hooks` | ❌ INVALID（skill 专用字段） |
| 7 | HIGH | New Command | 添加 `/btw <question>` | ✅ COMPLETE（Session tag #53） |
| 8 | HIGH | New Command | 添加 `/hooks` | ✅ COMPLETE（Extensions tag #30） |
| 9 | HIGH | New Command | 添加 `/insights` | ✅ COMPLETE（Context tag #17） |
| 10 | HIGH | New Command | 添加 `/plugin` | ✅ COMPLETE（Extensions tag #33） |
| 11 | HIGH | New Command | 添加 `/skills` | ✅ COMPLETE（Extensions tag #35） |
| 12 | HIGH | New Command | 添加 `/upgrade` | ✅ COMPLETE（Auth tag #3） |
| 13 | HIGH | Removed Command | 删除 `/output-style` | ✅ COMPLETE（Config tag 移除） |
| 14 | HIGH | Removed Command | 删除 `/bug` 行 | ✅ COMPLETE（合并为 /feedback alias） |
| 15 | HIGH | Changed Description | 更新 `/passes` 描述 | ✅ COMPLETE |
| 16 | HIGH | Changed Description | 更新 `/review` 描述 | ✅ COMPLETE |
| 17 | MED | Changed Description | 更新 `/stickers` 描述 | ✅ COMPLETE |

---

## [2026-03-15 12:50 PM PKT] Claude Code v2.1.76

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | New Command | 添加 `/color [color\|default]` | ✅ COMPLETE（Config tag #4） |
| 2 | HIGH | New Command | 添加 `/effort [low\|medium\|high\|max\|auto]` | ✅ COMPLETE（Model tag #38） |
| 3 | MED | Changed Description | 更新 `/status` 描述 | ✅ COMPLETE（Context tag #20） |
| 4 | MED | Changed Description | 更新 `/desktop` 描述 | ✅ COMPLETE（Remote tag #49） |
| 5 | LOW | Changed Argument | `/init` 移除 `[prompt]` | ✅ COMPLETE（Project tag #45） |
