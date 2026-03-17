# Verification Checklist — Subagents Report

规则会不断积累。每次 workflow-changelog 运行必须按指定深度执行所有规则。如果发现新的漂移类型，且已有规则本应发现（但不存在或深度不够），需在此追加新规则。

## 深度等级

| Depth | 含义 | 示例 |
|-------|---------|---------|
| `exists` | 检查章节/表/文件是否存在 | “报告是否包含 Memory Scopes 表？” |
| `presence-check` | 检查某个项是否存在或缺失 | “Frontmatter Fields 表里是否有 `color` 字段？” |
| `content-match` | 与来源逐字比对 | “`model` 字段描述是否与官方一致？” |
| `field-level` | 每个字段逐项验证 | “官方字段是否都在表中？” |
| `cross-file` | 跨文件一致性 | “CLAUDE.md 的 agent 字段是否与报告一致？” |

---

## 1. Frontmatter Fields 表

验证 Frontmatter Fields 表与官方一致。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 1A | Field Completeness | 官方 agent frontmatter 字段必须在表中 | field-level | sub-agents reference page | 2026-02-28 | 初始规则 |
| 1B | Field Types | 字段类型与官方一致 | content-match | sub-agents reference page | 2026-02-28 | 初始规则 |
| 1C | Required Status | Required 列与官方一致 | content-match | sub-agents reference page | 2026-02-28 | 初始规则 |
| 1D | Field Descriptions | 描述与官方一致 | content-match | sub-agents reference page | 2026-02-28 | 初始规则 |

---

## 2. Memory Scopes

验证 Memory Scopes 表。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 2A | Scope Completeness | 官方 scopes 必须在表中 | field-level | sub-agents reference page | 2026-02-28 | 初始规则 |
| 2B | Storage Locations | Storage Location 与官方一致 | content-match | sub-agents reference page | 2026-02-28 | 初始规则 |

---

## 3. 示例

验证示例准确性。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 3A | Minimal Example | 最小示例仅含必填字段且语法正确 | content-match | sub-agents reference page | 2026-02-28 | 初始规则 |
| 3B | Full-Featured Example | 完整示例涵盖全部字段 | field-level | sub-agents reference page | 2026-02-28 | 初始规则 |

---

## 4. Scope & Priority

验证范围与优先级信息。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 4A | Priority Order | Scope 与 Priority 表顺序与官方一致 | content-match | sub-agents + CLI reference | 2026-02-28 | 初始规则 |
| 4B | Invocation Methods | 调用方式表必须包含所有官方方法 | field-level | CLI + sub-agents docs | 2026-03-07 | 缺失 `--agent` 触发补充 |

---

## 5. Cross-File 一致性

验证报告与其他文件一致。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 5A | CLAUDE.md Sync | CLAUDE.md 的 Subagent Definition Structure 与报告一致 | cross-file | CLAUDE.md vs report | 2026-02-28 | 初始规则 |

---

## 6. Process

验证 workflow 本身。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 6A | Source Credibility Guard | 仅官方来源确认后才标记漂移 | content-match | official docs | 2026-02-28 | 防止第三方误导 |

---

## 7. Agent Tables

验证官方与仓库 agent 表。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 7A | Built-in Agent Completeness | 官方 agent 表完整且模型/工具/描述正确 | field-level | sub-agents docs + changelog | 2026-02-28 | 初始规则 |
| 7B | Repository Agent Completeness | `.claude/agents/**/*.md` 全部出现在仓库 agents 表 | field-level | agent frontmatter | 2026-02-28 | 初始规则 |
| 7C | Repository Agent Links | 仓库 agents 表链接可解析到正确文件 | exists | local filesystem | 2026-02-28 | 初始规则 |

---

## 8. Hyperlinks

验证链接有效性。

| # | Category | Check | Depth | Compare Against | Added | Origin |
|---|----------|-------|-------|-----------------|-------|--------|
| 8A | Local File Links | 相对链接必须存在 | exists | local filesystem | 2026-02-28 | 初始规则 |
| 8B | External URL Links | 外链必须可访问 | exists | HTTP response | 2026-02-28 | 初始规则 |
| 8C | Cross-File Reference Links | 跨报告链接必须存在 | exists | local filesystem | 2026-02-28 | 初始规则 |
