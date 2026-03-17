# Verification Checklist — Settings Report

规则会不断积累。每次 workflow-changelog 运行必须按指定深度执行所有规则。如果发现新的漂移类型，且已有规则本应发现（但不存在或深度不够），需在此追加新规则。

## 深度等级

| Depth | 含义 | 示例 |
|-------|---------|---------|
| `exists` | 检查章节/表/文件是否存在 | “报告是否包含 Sandbox Settings 表？” |
| `presence-check` | 检查某个项是否存在或缺失 | “Hook Events 表里是否有 `ConfigChange` 事件？” |
| `content-match` | 与来源逐字比对 | “`model` 设置描述是否与官方一致？” |
| `field-level` | 每个字段逐项验证 | “官方所有 settings key 是否在表中？” |
| `cross-file` | 跨文件一致性 | “CLAUDE.md 的 hooks 是否与报告一致？” |

---

## 1. Settings Keys 表

验证 settings key 表是否与官方一致。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 1A | Key Completeness | 官方所有 settings key 必须出现在正确章节表中 | field-level | settings documentation page | 2026-03-05 | 初始规则 — 防止漏项 |
| 1B | Key Types | 表中每个 key 的 Type 与官方一致 | content-match | settings documentation page | 2026-03-05 | 初始规则 — 防止类型错误 |
| 1C | Key Defaults | 有默认值的 key 必须与官方一致 | content-match | settings documentation page | 2026-03-05 | 初始规则 — 防止默认值错误 |
| 1D | Key Descriptions | 描述需准确反映官方行为 | content-match | settings documentation page | 2026-03-05 | 初始规则 — 防止描述过期 |
| 1E | Scope Column | 有 Scope 列的表（MCP/Plugin/Permission）需与官方一致 | content-match | settings documentation page | 2026-03-15 | v2.1.71 发现 scope 错误；需系统性校验 |
| 1F | Inverse Completeness | 报告中的 key 必须存在于官方文档，或标记为 “not in official docs — unverified” | field-level | settings documentation page + JSON schema | 2026-03-15 | 反向校验避免无依据条目 |
| 1G | Edge-Case Semantics | 边界值行为必须与官方一致并文档化 | content-match | settings documentation page | 2026-03-15 | v2.1.75/76 捕获边界行为缺失 |

---

## 2. Settings Hierarchy

验证设置层级表。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 2A | Priority Levels | 层级顺序与官方一致（5 层 + managed） | field-level | settings documentation page | 2026-03-05 | 初始规则 |
| 2B | File Locations | 各层级路径与官方一致 | content-match | settings documentation page | 2026-03-05 | 初始规则 |
| 2C | Merge Semantics | 数组合并描述必须与官方一致 | content-match | settings documentation page | 2026-03-15 | v2.1.75 捕获描述变化 |
| 2D | Managed Internals | 管理层交付方式/优先级/路径与官方一致 | field-level | settings documentation page | 2026-03-15 | v2.1.75 细节补充 | 

---

## 3. Permissions

验证权限配置准确性。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 3A | Permission Modes | 权限模式与官方一致 | field-level | settings documentation page | 2026-03-05 | 初始规则 |
| 3B | Tool Syntax Patterns | 工具语法与示例与官方一致 | content-match | settings documentation page | 2026-03-05 | 初始规则 |
| 3C | Bidirectional Mode Check | 双向校验：报告与官方模式需一致 | field-level | settings + permissions docs | 2026-03-15 | v2.1.74 捕获 unverified 模式 |
| 3D | Evaluation Semantics | 评估顺序、Remote 限制、路径前缀语义与官方一致 | content-match | permissions docs | 2026-03-15 | v2.1.75/76 捕获语义缺失 |

---

## 4. Hooks（重定向）

hook 分析不在此 workflow 中，已外置到 [claude-code-voice-hooks](https://github.com/shanraisshan/claude-code-voice-hooks)。只验证跳转链接有效。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 4A | Hooks Redirect | hooks section 必须包含有效跳转链接 | exists | report file | 2026-03-05 | hooks 外置后只验证链接 |

---

## 5. 环境变量

验证环境变量完整性与归属。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 5A | Env Var Completeness | 官方可配置 env vars 必须出现在报告 | field-level | settings documentation page | 2026-03-05 | 初始规则 |
| 5B | Ownership Boundary | CLI 启动 env vars 不应出现在 settings 报告（反之亦然） | cross-file | claude-cli-startup-flags.md vs settings report | 2026-03-05 | 防止重复 |
| 5C | Env Var Descriptions | 描述必须与官方 env-vars 页面一致 | content-match | env-vars docs | 2026-03-15 | v2.1.74 捕获描述错误 |
| 5D | Inverse Env Var Check | 报告中的 env var 必须在官方页面或标注 unverified | field-level | env-vars docs | 2026-03-15 | 防止无依据条目 |

---

## 6. Examples

验证示例准确性。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 6A | Quick Reference Example | Quick Reference 示例必须合法且为当前最佳实践 | content-match | settings docs | 2026-03-05 | 初始规则 |
| 6B | Example URL Validation | JSON 示例里的 URL 应有效并使用正确域名 | exists | HTTP response | 2026-03-15 | v2.1.74 捕获 schema URL 错误 |

---

## 7. Cross-File 一致性

验证报告与其他文件一致。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 7A | CLAUDE.md Sync | CLAUDE.md 的配置层级与 hooks 与报告一致 | cross-file | CLAUDE.md vs report | 2026-03-05 | 初始规则 |

---

## 8. Process

验证 workflow 本身。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 8A | Source Credibility Guard | 仅在官方来源确认后标记漂移 | content-match | official docs | 2026-03-05 | 防止第三方误导 |

---

## 10. 版本元数据与 Suspect Key 生命周期

验证报告元数据准确性，防止疑似项长期积压。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 10A | Version Metadata | 版本徽章、header 计数与表格一致 | content-match | report internal consistency | 2026-03-15 | v2.1.71/69 捕获不一致 |
| 10B | Suspect Key Escalation | 连续 5 次 ON HOLD 必须解决或标注 | exists | changelog history | 2026-03-15 | 防止疑似项长期积压 |
| 10C | Bidirectional Completeness | 报告中的 key/mode/env var 必须可追溯或标注 unverified | field-level | official docs vs report | 2026-03-15 | 反向校验，防止孤儿条目 |

---

## 9. Hyperlinks

验证链接有效性。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 9A | Local File Links | 相对链接必须存在 | exists | local filesystem | 2026-03-05 | 初始规则 |
| 9B | External URL Links | 外链必须可访问 | exists | HTTP response | 2026-03-05 | 初始规则 |
| 9C | Anchor Links | 锚点必须存在 | exists | file headings | 2026-03-05 | 初始规则 |
