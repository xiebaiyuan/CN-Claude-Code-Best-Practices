---
description: 执行分阶段实现并设置验证门
argument-hint: "<feature-slug> [--phase N] [--validate-only]"
---

## 用户输入

```text
$ARGUMENTS
```

你 **必须** 解析用户输入以提取 feature slug（`rpi/` 下的文件夹名）。

## 目的

该命令基于规划文档执行分阶段实现，编排专业 agents、强制验证门，并在实现过程中确保符合项目宪法。

**前置条件**：
- `rpi/{feature-slug}/` 目录存在
- 规划完成（`rpi/{feature-slug}/plan/PLAN.md` 存在）

**输出位置**：`rpi/{feature-slug}/implement/`

**这是 RPI 工作流的第 4 步**（最终实现阶段）。

## Flags

- `--phase N`：执行指定阶段（1-8），不传则从 Phase 1 开始
- `--validate-only`：仅验证当前阶段，不执行实现
- `--skip-validation`：跳过验证门继续（谨慎使用）

## 可用 Agents

所有 agents 使用 **Opus 模型**以获得最高质量。

### Implementation Agent

| Agent | Type | When to Use |
|-------|------|-------------|
| `senior-software-engineer` | Custom | 所有实现任务 |

### Support Agents

| Agent | Type | Purpose |
|-------|------|---------|
| `Explore` | Built-in | 实现前代码探索 |
| `code-reviewer` | Custom | 代码评审与质量验证 |
| `constitutional-validator` | Custom | 宪法对齐验证 |
| `documentation-analyst-writer` | Built-in | 文档生成 |

### Agent Routing

所有实现任务由 `senior-software-engineer` 处理。

---

## Phase 0：加载上下文与规则

**前置条件**：从用户输入中解析出 feature slug

**流程**：

### 0.1 加载项目宪法

1. 查找仓库内宪法/原则文档
2. 若存在，提取：
   - 技术约束（类型安全、测试、组件隔离）
   - 业务原则（质量标准、流程）
   - 架构边界
3. 在实现过程中强制遵守

### 0.2 加载领域规则

根据待修改文件加载相关指引：
- 检查组件 README
- 检查代码风格指南
- 检查测试要求文档

### 0.3 分析实现范围

1. 读取 `rpi/{feature-slug}/plan/PLAN.md`
2. 识别所有需要修改的文件
3. 将文件映射给实现 agent

**输出**：
- 宪法上下文摘要
- 已加载的领域规则
- 文件到 agent 的映射
- 阶段执行计划

**验证**：
- [ ] 宪法已加载（如存在）
- [ ] 影响文件的领域规则已加载
- [ ] 所有文件已映射至 agent
- [ ] 执行计划已理解

---

## 分阶段实施流程

### 阶段实施循环

对 PLAN.md 中每个阶段：

```
┌─────────────────────────────────────────────────────────────────┐
│ Phase N: [Phase Name]                                            │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  1. Code Discovery (Explore Agent)                              │
│     └─→ 变更前理解现有代码                                     │
│                                                                  │
│  2. Implementation (senior-software-engineer)                   │
│     └─→ 实现阶段交付物                                          │
│                                                                  │
│  3. Self-Validation                                             │
│     └─→ 工程师按阶段清单自检                                  │
│                                                                  │
│  4. Code Review (code-reviewer Agent)                           │
│     └─→ 安全、正确性、可维护性                                  │
│                                                                  │
│  5. User Validation Gate                                        │
│     └─→ 停止并请求用户确认                                     │
│         ├─→ PASS: 进入下一阶段                                 │
│         ├─→ CONDITIONAL PASS: 记录问题后继续                    │
│         └─→ FAIL: 修复后重新验证                               │
│                                                                  │
│  6. Documentation Update                                        │
│     └─→ 更新 PLAN.md 阶段状态                                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Step 1：代码探索（每阶段）

**Agent**：Explore（内置，通过 Task 工具）

**目的**：在修改前让实现基于真实代码。

**流程**：
1. 使用 Task 工具启动 Explore，`subagent_type="Explore"`
2. 分析当前阶段涉及文件
3. 理解现有模式、集成点与约束

**Explore Agent Prompt**：
```
Analyze the codebase to prepare for implementing Phase N of [feature-name].

Files to be modified in this phase:
[List files from PLAN.md]

Investigate and document:

1. **Current Implementation**
   - How do these files currently work?
   - What patterns are used?
   - What functions/classes will be affected?

2. **Integration Points**
   - What other files import or use these modules?
   - What APIs or interfaces will change?
   - What tests cover this code?

3. **Dependencies**
   - What libraries are used?
   - What internal utilities are available?
   - What constraints exist from current code?

4. **Patterns to Follow**
   - What coding style is used in these files?
   - What naming conventions are followed?
   - What error handling patterns exist?

5. **Risks and Considerations**
   - What could break if we change this?
   - What edge cases exist?
   - What backward compatibility concerns?

Provide a discovery summary to inform implementation.
```

**输出**：实现所需的探索摘要

---

## Step 2：实施（每阶段）

**Agent**：senior-software-engineer

**流程**：
1. 使用 senior-software-engineer
2. 提供 Step 1 的探索上下文
3. 完成阶段交付物
4. 遵守宪法约束与项目规则

**Implementation Agent Prompt Template**：
```
Acting as the [agent-name] agent, implement Phase N deliverables for [feature-name].

## Context
- Constitutional Constraints: [from Phase 0]
- Domain Rules: [from Phase 0]
- Discovery Summary: [from Step 1]

## Phase N Deliverables
[List from PLAN.md]

## Files to Modify
[List files with specific changes from PLAN.md]

## Implementation Requirements
1. Follow existing code patterns identified in discovery
2. Honor constitutional constraints (type safety, testing, etc.)
3. Follow project-specific rules (if applicable)
4. Write tests for new functionality
5. Include appropriate logging
6. Handle errors gracefully

## Quality Checklist
- [ ] Code follows existing patterns
- [ ] Type annotations present where applicable
- [ ] Tests written and passing
- [ ] No breaking changes to existing functionality
- [ ] Logging added for observability
- [ ] Error handling comprehensive

Implement all deliverables and report what was done.
```

---

## Step 3：自检验证

**Agent**：senior-software-engineer（同 Step 2）

**流程**：
1. 按阶段清单自检
2. 运行 lint（用项目配置的 linter）
3. 运行相关测试
4. 确认构建成功

**验证命令**（根据项目调整）：

```bash
# Run linter
[your-linter-command]

# Run tests
[your-test-command]

# Build/compile
[your-build-command]
```

**自检清单**：
- [ ] 所有交付物已实现
- [ ] Lint 通过
- [ ] 测试通过
- [ ] 构建成功
- [ ] 现有测试无回归
- [ ] 宪法约束遵守
- [ ] 领域规则遵守

---

## Step 4：代码评审

**Agent**：code-reviewer（Custom，自动调用）

**流程**：
1. 调用 code-reviewer 评审变更
2. 关注正确性、安全性、可维护性
3. 解决阻塞问题后再继续

**Code Review Agent Prompt**：
```
Acting as the code-reviewer agent, review the Phase N implementation for [feature-name].

## Files Changed
[List modified files]

## Changes Made
[Summary of implementation]

## Review Focus
- Correctness & tests
- Security & dependency hygiene
- Architectural boundaries
- Clarity over cleverness

## Constitutional Constraints
[From Phase 0]

Provide review using standard output format.
```

**Review Verdicts**：
- **APPROVED**：进入用户验证
- **APPROVED WITH SUGGESTIONS**：记录建议后继续
- **NEEDS REVISION**：修复后重审

---

## Step 5：用户验证门

**关键**：必须与用户交互，禁止自动继续。

**流程**：
1. 展示阶段交付清单
2. 展示已完成内容（文件与功能）
3. 展示 PLAN.md 的验证标准
4. 展示代码评审结果
5. **停止并等待用户决定**

**Validation Request Format**：
```
## Phase N Validation Request

### Deliverables Completed
- [x] [Deliverable 1] - [implementation summary]
- [x] [Deliverable 2] - [implementation summary]
- ...

### Files Changed
| File | Change Type | Lines |
|------|-------------|-------|
| [file] | [add/modify] | [±N] |

### Tests
- [x] Unit tests: PASS
- [x] Integration tests: PASS
- [x] Build: SUCCESS

### Code Review
- Verdict: [APPROVED / APPROVED WITH SUGGESTIONS]
- Issues: [None / List]

### Validation Criteria (from PLAN.md)
- [ ] [Criterion 1]
- [ ] [Criterion 2]
- ...

---

**Please validate Phase N:**
- **PASS**: Phase complete, proceed to Phase N+1
- **CONDITIONAL PASS**: Note issues below, proceed with caution
- **FAIL**: Specify issues to fix before proceeding
```

**User Decisions**：
- **PASS**：进入下一阶段
- **CONDITIONAL PASS**：记录问题并继续
- **FAIL**：修复问题后重做 Step 2-5

---

## Step 6：文档更新

**流程**：
1. 更新 `rpi/{feature-slug}/plan/PLAN.md` 的阶段状态
2. 更新 `rpi/{feature-slug}/implement/IMPLEMENT.md` 的验证结果
3. 将每阶段验证追加到 IMPLEMENT.md

### 阶段状态跟踪

更新 PLAN.md 的复选框：
```markdown
- [ ] Phase N: Not Started
- [~] Phase N: In Progress
- [x] Phase N: Validated (PASS)
- [!] Phase N: Conditional Pass (with notes)
- [-] Phase N: Failed Validation (needs rework)
```

### IMPLEMENT.md 模板

```markdown
# Implementation Record

**Feature**: [feature-slug]
**Started**: [Date]
**Status**: [IN_PROGRESS / COMPLETED]

---

## Phase 1: [Phase Name]

**Date**: [Date]
**Verdict**: [PASS / CONDITIONAL PASS / FAIL]

### Deliverables
- [x] [Deliverable 1]
- [x] [Deliverable 2]

### Files Changed
[List with line counts]

### Test Results
[Test output summary]

### Code Review
[Review verdict and notes]

### Notes
[Any additional notes]

---

## Phase 2: [Phase Name]
[Same structure as Phase 1...]

---

## Summary

**Phases Completed**: [N] of [N]
**Final Status**: [COMPLETED / IN_PROGRESS]
```

---

## 错误处理

### 实现失败

**若实现失败**：
1. 记录具体失败
2. 分析根因
3. 尝试替代方案（最多 2 次）
4. 仍失败则停止并请求用户指导
5. 不要带着失败实现进入下一阶段

**Message**："Implementation failed: [error]. Attempted [N] approaches. User guidance needed."

### 测试失败

**若测试失败**：
1. 分析失败原因（代码 bug 或测试问题）
2. 修复问题
3. 重新运行测试
4. 如仍失败，记录并询问用户
5. 测试未通过不得标记阶段完成

**Message**："Tests failing: [failures]. Fix attempted but unsuccessful. User review needed."

### 构建失败

**若构建失败**：
1. 检查类型错误
2. 检查缺失导入
3. 检查语法错误
4. 修复并重建
5. 如仍失败，升级到用户

**Message**："Build failing: [error]. Unable to resolve automatically."

### Agent 失败

**若 agent 失败或超时**：
1. 用相同输入重试一次
2. 仍失败则继续但标记缺失
3. 在验证请求中记录缺失

**Message**："Agent [name] failed. Proceeding without contribution."

---

## 完成报告

所有阶段完成后输出：

```markdown
## Implementation Complete

### Feature Summary
- **Feature**: [feature-name]
- **Phases Completed**: [N] of [N]

### Phases Executed
| Phase | Status | Notes |
|-------|--------|-------|
| Phase 1 | PASS | [summary] |
| Phase 2 | PASS | [summary] |
| ... | ... | ... |

### Files Modified
| File | Change Type | Lines |
|------|-------------|-------|
| [file] | [type] | [±N] |

### Tests Added
- [test files]

### Code Review Summary
- Blockers Fixed: [N]
- Suggestions Addressed: [N]

### Constitutional Compliance
- [ ] Type safety maintained
- [ ] Tests written
- [ ] Component isolation respected
- [ ] No breaking changes

### Artifacts Created
- `rpi/{feature-slug}/plan/PLAN.md` (updated with phase status)
- `rpi/{feature-slug}/implement/IMPLEMENT.md` (all phase validations)

### Next Steps
1. Create PR with changes
2. Request final human review
3. Deploy to staging
4. Verify in staging environment
5. Deploy to production

### PR Notes

**Title**: [{feature-slug}] [Brief description]

**Summary**:
[What was implemented]

**Changes**:
- [List key changes]

**Testing**:
- [How tested]

**Rollout**:
- [Deployment steps]

**Rollback**:
- [Rollback procedure if issues]
```

---

## 质量门

### 每阶段质量门

在标记阶段完成前：

- [ ] 所有交付物已实现
- [ ] Lint 通过
- [ ] 测试通过
- [ ] 构建成功
- [ ] 代码评审通过
- [ ] 用户验证已完成
- [ ] 文档已更新

### 最终质量门

在标记实现完成前：

- [ ] 所有阶段已验证
- [ ] 无失败测试
- [ ] 全量构建通过
- [ ] 宪法合规性已验证
- [ ] 领域规则已遵守
- [ ] PR notes 已生成

---

## 备注

### 何时使用该命令

- `/rpi:plan` 生成 PLAN.md 后
- 需要分阶段实现与验证门时
- 复杂功能需要结构化实施

### 何时不使用该命令

- Bug 修复（过重，直接修即可）
- 非常简单的变更（<30 分钟）
- 探索性原型
- 仅文档变更

### 最佳实践

1. **先读 PLAN.md**：理解要实现的内容
2. **信任代码探索**：让 Explore agent 指导实现
3. **遵循现有模式**：根据探索结果实施
4. **不要跳过验证**：门是为了早发现问题
5. **边做边记录**：每阶段更新状态
6. **卡住就问**：不要带着错误继续

### RPI 工作流

第 4 步（Describe → Research → Plan → **Implement**）

---

## Command 示例

### 执行全部阶段

```bash
/rpi:implement "my-feature"
```

### 执行指定阶段

```bash
/rpi:implement "my-feature" --phase 3
```

### 仅验证（不实现）

```bash
/rpi:implement "my-feature" --phase 2 --validate-only
```

---

## 完成后的动作

**重要**：完成实现（全部阶段或重大进度）后必须提示用户压缩对话：

> **上下文管理**：该实现流程消耗了大量上下文。为保留进度并释放空间，请运行：
>
> ```
> /compact
> ```
>
> 该命令会总结对话并保留实现状态，降低后续 token 使用。

**何时提示 compact**：
- 完成全部阶段后
- 每个重要阶段完成后（若跨会话实现）
- 实现中上下文接近上限时
