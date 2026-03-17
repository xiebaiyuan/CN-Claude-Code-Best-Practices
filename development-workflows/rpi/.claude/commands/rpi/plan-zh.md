---
description: 为功能创建完整的规划文档
argument-hint: "<feature-slug>"
---

## 用户输入

```text
$ARGUMENTS
```

你 **必须** 解析用户输入以提取 feature slug（`rpi/` 下的文件夹名）。

## 目的

该命令为功能请求创建完整的规划文档，生成详细规格、技术设计与实施计划，存放于功能的 RPI 目录。

**前置条件**：
- `rpi/{feature-slug}/` 目录存在
- 已完成 Research 且结论为 GO（`rpi/{feature-slug}/research/RESEARCH.md` 存在）

**输出位置**：`rpi/{feature-slug}/plan/`

**这是 RPI 工作流的第 3 步**（Research 通过后进入）。

## 大纲

1. **加载上下文**：读取研究报告与项目宪法（如有）
2. **理解需求**：解析功能范围与需求
3. **技术分析**：审查架构与依赖
4. **架构设计**：输出高层架构与 API 合约
5. **拆解实现**：生成分阶段任务拆解
6. **生成文档**：产出结构化文档文件
7. **质量验证**：确保全部质量门通过
8. **完成报告**：输出总结与下一步

## Phases

### Phase 0：加载上下文

**前置条件**：已提供 feature slug

**流程**：
1. **确认 Research 完成**：
   - 检查 `rpi/{feature-slug}/research/RESEARCH.md` 是否存在
   - 确认 GO 建议（若为 NO-GO 或 CONDITIONAL 则提醒）

2. **读取研究结论**：
   - 提取产品分析
   - 提取技术发现
   - 提取技术可行性评估
   - 记录风险与约束

3. **加载项目宪法**（如有）：
   - 查找仓库中的宪法/原则文档
   - 提取相关约束与偏好

**输出**：
- 研究摘要
- 宪法上下文（如有）
- 规划约束

**验证**：
- [ ] 研究报告存在
- [ ] GO 建议确认
- [ ] 宪法已加载（如存在）

---

### Phase 1：理解功能需求

**前置条件**：Phase 0 完成

**流程**：
1. **解析功能描述**（来自研究报告）：
   - 提取功能名称与主要目标
   - 识别目标组件
   - 区分用户功能与技术功能
   - 判断复杂度

2. **识别受影响组件**：
   - 主组件（功能所在）
   - 次组件（集成点）
   - 共享工具
   - 外部依赖

3. **研究现有模式**：
   - 搜索代码库中的类似功能
   - 复用组件与模式
   - 识别可复用代码

**输出**：
- 功能范围文档（内部）
- 受影响组件清单
- 现有模式清单

**验证**：
- [ ] 功能名称与目标清晰
- [ ] 目标组件已识别
- [ ] 复杂度已评估

---

### Phase 2：技术需求分析

**前置条件**：Phase 1 完成

**流程**：
1. **审查组件架构**：
   - 阅读组件 README 与文档
   - 审查现有代码结构
   - 识别架构模式

2. **识别技术依赖**：
   - 内部依赖（组件、共享工具）
   - 外部依赖（API、服务、库）
   - 数据库/存储需求
   - 认证/鉴权需求

3. **评估集成点**：
   - 需要创建/修改的 API
   - 数据库 schema 变化
   - 事件/消息流
   - 前后端集成

4. **评估技术风险**：
   - 破坏既有功能
   - 性能影响
   - 安全问题
   - 数据迁移需求

**输出**：
- 技术需求文档（内部）
- 依赖图
- 集成点图
- 风险评估

**验证**：
- [ ] 组件架构理解充分
- [ ] 依赖已识别
- [ ] 集成点已映射
- [ ] 风险已评估

---

### Phase 3：功能架构设计

**前置条件**：Phase 1-2 完成

**Agent**：senior-software-engineer

**流程**：
1. **设计高层架构**：
   - 组件/模块结构
   - 数据流图
   - API 接口
   - 数据库 schema 变化

2. **定义实现路径**：
   - 文件结构组织
   - 代码组织模式
   - 测试策略
   - 错误处理策略

3. **规划数据库/存储变更**（如适用）：
   - 新表/集合
   - schema 修改
   - 迁移策略
   - 数据校验规则

4. **设计 API 合约**（如适用）：
   - 请求/响应格式
   - 认证要求
   - 错误响应

5. **规划测试策略**：
   - 单元测试
   - 集成测试
   - 端到端测试

**输出**：
- 架构设计文档（内部）
- API 规格
- 数据库 schema 设计
- 测试策略

**验证**：
- [ ] 高层架构已设计
- [ ] 实施路径已定义
- [ ] 数据库变更已规划（如有）
- [ ] API 合约已明确（如有）
- [ ] 测试策略完整

---

### Phase 4：拆解实施任务

**前置条件**：Phase 1-3 完成

**流程**：
1. **识别实施阶段**：
   - 拆为 3-5 个逻辑阶段
   - 每个阶段可交付、可测试
   - 阶段逐步构建

2. **为每阶段拆解任务**：
   - 列出具体实现任务
   - 估算复杂度（Low/Medium/High）
   - 标注依赖
   - 关联代码区域

3. **定义成功标准**：
   - 阶段验收标准
   - 测试要求
   - 文档要求

4. **识别并行机会**：
   - 可并行任务
   - 前后端并行
   - 独立模块开发

**输出**：
- 分阶段实施计划
- 任务拆解与估算
- 每阶段成功标准
- 依赖图

**验证**：
- [ ] 拆为 3-5 个逻辑阶段
- [ ] 每阶段有具体任务
- [ ] 任务复杂度已估计
- [ ] 依赖清晰
- [ ] 成功标准已定义

---

### Phase 5：生成文档

**前置条件**：Phase 1-4 完成

**Agent**：documentation-analyst-writer（通过 Task 工具）

**流程**：
1. **生成 pm.md**（产品需求）：
   - 功能描述与用户故事
   - 宪法对齐（如适用）
   - 业务价值与成功指标
   - 用户画像与用例
   - 验收标准
   - Out of scope

2. **生成 ux.md**（用户体验设计）：
   - UI 方案（文字描述）
   - 用户流程与交互
   - 无障碍考虑
   - 错误状态与边界条件

3. **生成 eng.md**（技术规格）：
   - 架构设计
   - API 规格
   - 数据库 schema 变更
   - 技术栈
   - 技术风险与缓解

4. **生成 PLAN.md**（实施路线图）：
   - 分阶段拆解
   - 每阶段任务与估算
   - 依赖与顺序
   - 每阶段成功标准
   - 测试要求
   - 验证检查点

**输出文件**（保存至 `rpi/{feature-slug}/plan/`）：
- `pm.md` - 产品需求
- `ux.md` - UX 设计
- `eng.md` - 技术规格
- `PLAN.md` - 详细实施路线图

**验证**：
- [ ] 4 个文件齐全（pm、ux、eng、PLAN）
- [ ] pm.md 覆盖业务需求
- [ ] ux.md 覆盖用户体验
- [ ] eng.md 提供技术规格
- [ ] PLAN.md 有分阶段实施
- [ ] 无占位文本
- [ ] Markdown 格式清晰

---

## 子代理编排

该命令编排以下专业 agents：

| Phase | Agent | Type | Purpose |
|-------|-------|------|---------|
| Phase 3 | senior-software-engineer | Custom | 架构设计 |
| Phase 5 | product-manager | Custom | 产品需求（pm.md） |
| Phase 5 | ux-designer | Custom | 用户体验（ux.md） |
| Phase 5 | senior-software-engineer | Custom | 技术规格（eng.md） |
| Phase 5 | documentation-analyst-writer | Built-in | 文档整合 |

### Agent 调用方式

**自定义 Agents**（product-manager、senior-software-engineer、ux-designer）：
- Claude Code 会自动从 `.claude/agents/` 识别
- 直接自然引用：“Acting as the senior-software-engineer agent...”
- 无需 Task 工具调用

**内置 Agent**（documentation-analyst-writer）：
- 使用 Task 工具，`subagent_type="documentation-analyst-writer"`

---

## 完成报告

完成后请报告：

### 输出内容

**Documentation Folder**：`rpi/{feature-slug}/plan/`

生成文件：
- **pm.md**：产品需求与用户故事（{Y} stories）
- **ux.md**：用户体验设计（{Z} flows）
- **eng.md**：技术规格（{A} APIs, {B} schema changes）
- **PLAN.md**：实施路线图（{C} phases, {D} tasks）

### 功能摘要

- **Feature Name**：{feature-name}
- **Target Component**：{component-name}
- **Complexity**：{Simple/Medium/Complex}
- **Implementation Phases**：{N} phases
- **Total Tasks**：{M} tasks
- **Dependencies**：{Y} internal, {Z} external

### 技术概要

- **Architecture Pattern**：{pattern-name}
- **APIs Added/Modified**：{N} APIs
- **Database Changes**：{Y} collections/tables
- **Testing Requirements**：{Z} test suites
- **Risk Level**：{Low/Medium/High}

### 实施阶段

1. **Phase 1**：{phase-name} - {task-count} tasks
2. **Phase 2**：{phase-name} - {task-count} tasks
3. **Phase 3**：{phase-name} - {task-count} tasks
[根据实际阶段继续...]

---

### 下一步

1. **Review Documentation**：
   - 阅读 `rpi/{feature-slug}/plan/` 下的规划文档
   - 重点查看 `eng.md`
   - 理解 `PLAN.md` 中的阶段拆解

2. **Validate with Stakeholders**：
   - 产品 review pm.md
   - UX review ux.md
   - 技术 review eng.md

3. **Begin Implementation**：
   - 运行 `/rpi:implement "{feature-slug}"` 执行分阶段实现
   - 按 PLAN.md 阶段推进
   - 完成每个阶段的验证门

---

## 错误处理

**研究报告不存在**：
- 行为：停止并告知用户
- 提示："Research report not found. Run `/rpi:research` first."

**研究建议为 NO-GO**：
- 行为：提醒用户但允许继续
- 提示："Research recommended NO-GO. Proceed anyway? (y/n)"

**目标组件不存在**：
- 行为：询问用户是否为新组件
- 提示："Component not found. Is this a new component?"

**文档 agent 失败**：
- 行为：直接生成文档
- 警告："Documentation may not fully adhere to standards"

---

## 备注

- **前置条件**：Research 已完成且为 GO
- **RPI 工作流**：第 3 步（Describe → Research → Plan → Implement）

**最佳实践**：
1. **先读 Research**：理解可行性结论
2. **利用技术发现**：用 Research 的技术发现指导规划
3. **足够具体**：细致规划带来更顺畅的实施
4. **提前验证**：实现前先审阅文档

---

## 完成后的动作

**重要**：完成规划后必须提示用户压缩对话：

> **上下文管理**：该规划流程消耗了大量上下文。为实施阶段释放空间，请运行：
>
> ```
> /compact
> ```
>
> 该命令会总结对话并保留规划决策，减少实施阶段的 token 使用。
