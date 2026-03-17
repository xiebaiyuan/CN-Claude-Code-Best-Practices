# Settings Report — 变更历史

## 状态图例

| 状态 | 含义 |
|--------|---------|
| ✅ `COMPLETE (reason)` | 已完成并成功解决 |
| ❌ `INVALID (reason)` | 发现无效、不适用或为有意设计 |
| ✋ `ON HOLD (reason)` | 暂缓处理（等待外部依赖或用户决策） |

---

## [2026-03-05 06:18 AM PKT] Claude Code v2.1.69

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Missing Settings | 添加 13 个非 hook 的缺失设置项（`$schema`、`availableModels`、`fastModePerSessionOptIn`、`teammateMode`、`prefersReducedMotion`、`sandbox.filesystem.*`、`sandbox.network.allowManagedDomainsOnly`、`sandbox.enableWeakerNetworkIsolation`、`allowManagedMcpServersOnly`、`blockedMarketplaces`、`includeGitInstructions`、`pluginTrustMessage`、`fileSuggestion` 表项） | ✅ COMPLETE（已加入报告） |
| 2 | HIGH | Missing Env Vars | 添加缺失环境变量：`CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING`、`CLAUDE_CODE_DISABLE_1M_CONTEXT`、`CLAUDE_CODE_ACCOUNT_UUID`、`CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS`、`ENABLE_CLAUDEAI_MCP_SERVERS` 等 | ✅ COMPLETE（新增 13 项） |
| 3 | HIGH | Effort Default | effort 默认值从 High 改为 Medium（Max/Team），添加 Sonnet 4.6 支持（v2.1.68） | ✅ COMPLETE（已更新默认值与说明） |
| 4 | MED | Settings Hierarchy | 添加管理设置（macOS plist/Windows Registry，v2.1.61/v2.1.69）；记录数组跨作用域合并行为 | ✅ COMPLETE（已补充） |
| 5 | MED | Sandbox Filesystem | 添加 `sandbox.filesystem.allowWrite/denyWrite/denyRead` 及路径前缀语义 | ✅ COMPLETE（已加入表） |
| 6 | MED | Permission Syntax | 添加 `Agent(name)` 权限模式与 `MCP(server:tool)` 语法 | ✅ COMPLETE（已加入工具语法表） |
| 7 | MED | Plugin Gaps | 添加 `blockedMarketplaces`、`pluginTrustMessage` | ✅ COMPLETE（已加入插件表） |
| 8 | MED | Model Config | 添加 `availableModels` 设置 | ✅ COMPLETE（已加入通用设置表） |
| 9 | MED | Suspect Keys | 验证 `sandbox.network.deniedDomains`、`sandbox.ignoreViolations`、`pluginConfigs` —— 仍非官方文档 | ✋ ON HOLD（保留待验证） |
| 10 | LOW | Header Counts | 更新 header 的设置/环境变量数量 | ✅ COMPLETE（已更新） |
| 11 | LOW | CLAUDE.md Sync | 更新 CLAUDE.md 配置层级（加 managed/CLI/user） | ✋ ON HOLD（等待用户批准） |
| 12 | LOW | Example Update | 更新 Quick Reference 示例（添加 `$schema`、sandbox filesystem、`Agent(*)`、移除 hooks 示例） | ✅ COMPLETE（已更新） |
| 13 | MED | Hooks Redirect | hooks 部分改为指向 claude-code-voice-hooks | ✅ COMPLETE（hooks 外置） |

---

## [2026-03-07 02:17 PM PKT] Claude Code v2.1.71

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Changed Behavior | 修正 `teammateMode`：类型 boolean → string，默认 false → "auto"，描述改为显示模式 | ✅ COMPLETE |
| 2 | HIGH | New Setting | 添加 `allowManagedPermissionRulesOnly` 到权限表 | ✅ COMPLETE |
| 3 | HIGH | Missing Env Vars | 新增约 31 个缺失 env vars（含 Foundry/Bedrock/mTLS 等） | ✅ COMPLETE |
| 4 | MED | Changed Default | 修正 `plansDirectory` 默认值 `.claude/plans/` → `~/.claude/plans` | ✅ COMPLETE |
| 5 | MED | Changed Description | 修正 `sandbox.enableWeakerNetworkIsolation` 描述 | ✅ COMPLETE |
| 6 | MED | Scope Fix | 修正 `extraKnownMarketplaces` scope：Any → Project | ✅ COMPLETE |
| 7 | MED | Boundary Violation | 在 CLI flags 文档中替换 `CLAUDE_CODE_EFFORT_LEVEL` 为设置引用 | ✅ COMPLETE |
| 8 | MED | Version Badge | 报告版本从 v2.1.69 更新到 v2.1.71 | ✅ COMPLETE |
| 9 | LOW | Suspect Keys | 再次验证疑似项 | ✋ ON HOLD |
| 10 | LOW | CLAUDE.md Sync | 更新 CLAUDE.md 配置层级（3 → 5+） | ✅ COMPLETE |

---

## [2026-03-12 12:23 PM PKT] Claude Code v2.1.74

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Changed Behavior | 修正 `dontAsk` 权限描述 | ✅ COMPLETE |
| 2 | HIGH | New Setting | 添加 `modelOverrides` | ✅ COMPLETE |
| 3 | HIGH | New Setting | 添加 `allow_remote_sessions`（managed-only） | ✅ COMPLETE |
| 4 | HIGH | Changed Default | 修正 `$schema` URL | ✅ COMPLETE |
| 5 | MED | Changed Description | 修正 `ANTHROPIC_CUSTOM_HEADERS` 格式描述 | ✅ COMPLETE |
| 6 | MED | Unverified Modes | 标注 `askEdits`/`viewOnly` 非官方 | ✅ COMPLETE |
| 7 | MED | Missing Env Vars | 添加 5 个缺失 env vars | ✅ COMPLETE |
| 8 | MED | New Setting | 添加 `autoMemoryDirectory` | ✅ COMPLETE（版本未定） |
| 9 | LOW | Suspect Keys | 仍未在官方文档中 | ✋ ON HOLD |
| 10 | LOW | Missing Env Var | 添加 `CLAUDE_CODE_SUBAGENT_MODEL` | ✅ COMPLETE |
| 11 | LOW | Example Update | 更新示例包含 `modelOverrides` 与正确 `$schema` | ✅ COMPLETE |

---

## [2026-03-14 01:35 AM PKT] Claude Code v2.1.75

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | Settings Hierarchy | 重构为官方 5 层：Managed > CLI > Local > Project > User；补充管理层细节 | ✅ COMPLETE |
| 2 | HIGH | Changed Behavior | `availableModels` 描述改为字符串数组 | ✅ COMPLETE |
| 3 | HIGH | Changed Behavior | 添加 `cleanupPeriodDays=0` 行为描述 | ✅ COMPLETE |
| 4 | HIGH | Permission Syntax | 添加权限评估顺序说明 | ✅ COMPLETE |
| 5 | MED | Changed Description | `autoMemoryDirectory` 作用域限制 | ✅ COMPLETE |
| 6 | MED | Changed Description | Remote 环境限制 `permissions.defaultMode` | ✅ COMPLETE |
| 7 | MED | Model Config | Opus 4.6 1M context 默认说明 | ✅ COMPLETE |
| 8 | MED | Settings Hierarchy | Windows managed 路径变更说明 | ✅ COMPLETE |
| 9 | MED | Display & UX | 添加 `fileSuggestion` stdin JSON 与输出限制 | ✅ COMPLETE |
| 10 | MED | Settings Hierarchy | 数组合并语义改为 “concatenated and deduplicated” | ✅ COMPLETE |
| 11 | LOW | Suspect Keys | `sandbox.ignoreViolations` 等仍非官方 | ✋ ON HOLD |
| 12 | LOW | Suspect Keys | `skipWebFetchPreflight` 等仍非官方 | ✋ ON HOLD |

---

## [2026-03-15 12:52 PM PKT] Claude Code v2.1.76

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | New Setting | 添加 `effortLevel` | ✋ ON HOLD（等待用户批准） |
| 2 | HIGH | New Settings | 添加 `worktree.sparsePaths` 与 `worktree.symlinkDirectories` | ✋ ON HOLD（等待用户批准） |
| 3 | HIGH | New Setting | 添加 `feedbackSurveyRate` | ✋ ON HOLD（等待用户批准） |
| 4 | HIGH | Missing Env Vars | 添加 20 个缺失 env vars | ✋ ON HOLD（等待用户批准） |
| 5 | HIGH | Missing Env Vars | 将 3 个 env vars 移入表格 | ✋ ON HOLD（等待用户批准） |
| 6 | HIGH | Broken Link | 修复 `https://claudelog.com/configuration/` | ✋ ON HOLD（等待用户批准） |
| 7 | MED | Changed Description | 更新 `cleanupPeriodDays` 描述补充空 `transcript_path` | ✋ ON HOLD |
| 8 | MED | Unverified Env Vars | 标注 7 个 env vars 为 unverified | ✋ ON HOLD |
| 9 | MED | New Source | 添加官方 env-vars 来源 | ✋ ON HOLD |
| 10 | MED | Example Update | 示例加入 `effortLevel` 与 worktree | ✋ ON HOLD |
| 11 | LOW | Suspect Keys | `sandbox.ignoreViolations` 等仍非官方 | ✋ ON HOLD |
| 12 | LOW | Suspect Keys | `skipWebFetchPreflight` 等仍非官方 | ✋ ON HOLD |

---

## [2026-03-15 01:10 PM PKT] Claude Code v2.1.76

| # | Priority | Type | Action | Status |
|---|----------|------|--------|--------|
| 1 | HIGH | New Setting | 添加 `effortLevel` 并更新 /effort 与 Effort Level 说明 | ✅ COMPLETE |
| 2 | HIGH | New Settings | 添加 Worktree Settings（`worktree.sparsePaths`、`worktree.symlinkDirectories`） | ✅ COMPLETE |
| 3 | HIGH | New Setting | 添加 `feedbackSurveyRate` | ✅ COMPLETE |
| 4 | HIGH | Missing Env Vars | 添加 23 个缺失 env vars | ✅ COMPLETE |
| 5 | HIGH | Broken Link | `https://claudelog.com/configuration/` 已恢复 | ✅ COMPLETE |
| 6 | MED | Permission Syntax | 增加路径前缀、word-boundary 与 `:*` 弃用说明 | ✅ COMPLETE |
| 7 | MED | Changed Description | `cleanupPeriodDays` 补充空 `transcript_path` | ✅ COMPLETE |
| 8 | MED | Unverified Env Vars | 标注 7 个 env vars 为 unverified | ✅ COMPLETE |
| 9 | MED | New Source | 添加 env-vars 与 permissions 官方来源 | ✅ COMPLETE |
| 10 | MED | Example Update | 示例加入 `effortLevel` 与 worktree | ✅ COMPLETE |
| 11 | LOW | Suspect Keys | `sandbox.ignoreViolations` 等仍非官方 | ✋ ON HOLD |
| 12 | LOW | Suspect Keys | `skipWebFetchPreflight` 等仍非官方 | ✋ ON HOLD |
