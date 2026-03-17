---
description: 研究并分析功能可行性 - GO/NO-GO 决策门
argument-hint: "<feature-slug>"
---

## 用户输入

```text
$ARGUMENTS
```

你 **必须** 解析用户输入以提取 feature slug（`rpi/` 下的文件夹名）。

**期望输入格式**：`rpi/{feature-slug}/REQUEST.md`

## 目的

该命令在规划阶段之前，对功能请求进行全面研究与分析。它是关键的 GO/NO-GO 门，用于判断该功能是否值得进入详细规划。

**关键目标**：
- 评估产品-市场匹配与用户价值
- 评估技术可行性与复杂度
- 识别风险与潜在阻塞
- 决定策略（自建/购买/合作/拒绝）
- 给出清晰的 go/no-go 建议与理由

**前置条件**：
- `rpi/{feature-slug}/` 目录存在
- `rpi/{feature-slug}/REQUEST.md` 存在

**输出位置**：`rpi/{feature-slug}/research/RESEARCH.md`

**这是 RPI 工作流的第 2 步**（第 1 步为功能描述）。

## 大纲

1. **加载上下文**：读取 `rpi/{feature-slug}/` 的功能描述与项目宪法（如有）
2. **解析功能请求**：使用 requirement-parser agent 提取结构化需求
3. **多阶段研究**：
   - Phase 1：解析功能请求（requirement-parser agent）
   - Phase 2：产品分析与宪法对齐（product-manager agent）
   - Phase 2.5：技术发现（Explore agent）— **关键：深度代码探索**
   - Phase 3：技术可行性（senior-software-engineer agent）
   - Phase 4：战略评估（technical-cto-advisor agent）
   - Phase 5：生成研究报告（documentation-analyst-writer agent）
4. **综合结论**：汇总分析并给出清晰的 go/no-go 结论
5. **质量验证**：对照质量门检查
6. **完成报告**：输出建议、下一步与报告位置

## Phases

### Phase 0：加载上下文

**前置条件**：提供 feature slug，`rpi/{feature-slug}/REQUEST.md` 存在

**流程**：
1. **读取功能描述**：
   - 读取 `rpi/{feature-slug}/REQUEST.md`（必需）
   - 提取需求与目标

2. **检查项目宪法**（可选）：
   - 仓库中查找宪法/原则文档
   - 常见位置：`constitution.md`、`PRINCIPLES.md`、`.project/constitution.md`
   - 若存在，提取核心原则、约束与目标

3. **建立研究上下文**：
   - 汇总为简洁摘要供 agents 使用
   - 识别对齐标准

**输出**：
- 功能描述摘要
- 宪法原则（如有）
- 评估对齐标准

**验证**：
- [ ] `rpi/{feature-slug}/` 目录存在
- [ ] 已提取功能描述
- [ ] 已检查并加载宪法（如存在）

---

### Phase 1：解析功能请求

**前置条件**：Phase 0 完成

**Agent**：requirement-parser（规划域）

**流程**：
1. **启动 requirement-parser** 并提供功能描述
2. **Agent 提取**：
   - 功能名称与类型
   - 目标组件
   - 目标与目的
   - 功能性/非功能性需求
   - 约束与假设
   - 复杂度估计
   - 澄清问题（如有）

3. **复核解析结果**：
   - 如有澄清问题，**停止并询问用户**

**输出**：
- 结构化需求文档
- 功能元信息（名称、类型、组件、复杂度）
- 澄清问题（如有）

---

### Phase 2：产品分析与宪法对齐

**前置条件**：Phase 1 完成，需求明确

**Agent**：product-manager

**流程**：
1. **启动 product-manager** 并提供：
   - Phase 1 解析结果
   - Phase 0 的宪法上下文

2. **Agent 分析**：
   - **用户价值**：谁受益？影响多大？
   - **市场匹配**：是否符合市场需求？
   - **产品愿景**：是否与产品战略一致？
   - **宪法对齐**：是否符合项目原则？
   - **约束检查**：是否违反宪法约束？

3. **Agent 输出**：
   - 产品可行性评分（High/Medium/Low）
   - 用户价值评估
   - 战略对齐评估
   - 优先级建议
   - 产品疑虑或红旗

**输出**：
- 产品可行性评估
- 用户价值分析
- 战略对齐评分
- 宪法对齐摘要（如适用）

---

### Phase 2.5：技术发现（代码探索）

**前置条件**：Phase 1-2 完成，产品可行性确认

**Agent**：Explore（通过 Task 工具，subagent_type="Explore"）

**目的**：**关键阶段** —— 在技术可行性判断前深度分析现有代码库。

**流程**：
1. **启动 Explore agent** 并提供目标组件
2. **Agent 调研**：
   - **现有实现**：是否已有类似功能？
   - **集成点**：该功能会触达哪些系统/模块？
   - **当前架构**：系统结构如何？
   - **数据模型**：已有的数据库/数据结构是什么？
   - **依赖**：现有库/服务依赖情况
   - **模式**：现有编码模式与约定

3. **Agent 输出**：
   - **当前状态摘要**：已存在的实现
   - **集成分析**：功能应如何融入
   - **冲突点**：可能破坏/冲突之处
   - **复用机会**：哪些可复用
   - **技术约束**：来自代码的真实约束

**输出**：
- 当前实现摘要
- 集成点地图
- 冲突点识别
- 可复用组件
- 技术约束清单

**关键**：此阶段确保 Phase 3 基于真实代码，而非假设。

---

### Phase 3：技术可行性评估

**前置条件**：Phase 1-2.5 完成，代码已探索

**Agent**：senior-software-engineer

**流程**：
1. **启动 senior-software-engineer** 并提供：
   - Phase 1 需求
   - Phase 2 产品上下文
   - **Phase 2.5 技术发现结果**

2. **Agent 分析**（基于真实代码）：
   - **技术方案**：可选实现路径
   - **复杂度**：实现难度
   - **依赖**：所需系统/服务
   - **技术债**：是否增加或减少
   - **风险**：技术风险与缓解

3. **Agent 输出**：
   - 技术可行性评分（High/Medium/Low）
   - 推荐方案（含替代方案）
   - 复杂度估计（Simple/Medium/Complex）
   - 技术风险与缓解

**输出**：
- 技术可行性评分
- 推荐实现方案
- 复杂度与工作量估计
- 技术风险与缓解

---

### Phase 4：战略评估

**前置条件**：Phase 1-3 完成

**Agent**：technical-cto-advisor

**流程**：
1. **启动 technical-cto-advisor** 并提供全部阶段输出

2. **Agent 综合**：
   - **总体评估**：结合产品与技术视角
   - **战略对齐**：是否符合工程原则与宪法
   - **风险与收益**：价值是否匹配成本与风险
   - **替代路径**：自建/购买/合作/延期/拒绝

3. **Agent 输出**：
   - **Go/No-Go 建议**：清晰决策与信心
   - **理由**：详细解释
   - **推荐路径**：如 Go，最佳方案
   - **条件**：继续前的前置条件
   - **风险**：关键风险

**输出**：
- Go/No-Go 建议
- 战略理由
- 推荐路径
- 风险摘要

---

### Phase 5：生成研究报告

**前置条件**：Phase 1-4 完成

**Agent**：documentation-analyst-writer（通过 Task 工具）

**流程**：
1. **启动 documentation-analyst-writer** 并提供全部阶段输出

2. **生成报告**，包含：
   - **Executive Summary**：一段概述与建议
   - **Feature Overview**：名称、类型、组件、目标
   - **Requirements Summary**：关键功能/非功能需求
   - **Product Analysis**：用户价值、市场匹配、战略对齐
   - **Technical Discovery**：当前状态、集成点、约束
   - **Technical Analysis**：可行性、方案、复杂度、风险
   - **Strategic Recommendation**：Go/No-Go 及理由
   - **Next Steps**：下一步行动

3. **生成文件**：`rpi/{feature-slug}/research/RESEARCH.md`

**输出**：
- 完整研究报告 `rpi/{feature-slug}/research/RESEARCH.md`

---

## 子代理编排

此命令编排 6 个专业 agent：

| Phase | Agent | Type | Location |
|-------|-------|------|----------|
| Phase 1 | requirement-parser | Custom | .claude/agents/requirement-parser.md |
| Phase 2 | product-manager | Custom | .claude/agents/product-manager.md |
| Phase 2.5 | Explore | Built-in | Task tool with subagent_type="Explore" |
| Phase 3 | senior-software-engineer | Custom | .claude/agents/senior-software-engineer.md |
| Phase 4 | technical-cto-advisor | Custom | .claude/agents/technical-cto-advisor.md |
| Phase 5 | documentation-analyst-writer | Built-in | Task tool with subagent_type="documentation-analyst-writer" |

---

## 完成报告

完成后需报告以下内容：

### 研究结论

**Decision**：`[GO | NO-GO | CONDITIONAL GO | DEFER]`

**Confidence**：`[High | Medium | Low]`

**Rationale**（1-2 句）：
[建议的关键原因]

---

### 研究摘要

**Feature**：{feature-name}
**Type**：{feature-type}
**Component**：{target-component}
**Complexity**：{Simple | Medium | Complex}

**Scores**：
- Product Viability：`[High/Medium/Low]`
- Technical Feasibility：`[High/Medium/Low]`
- Overall Assessment：`[High/Medium/Low]`

**Key Risks**：
1. {risk-1}
2. {risk-2}
3. {risk-3}

---

### 报告位置

**Full Research Report**：`rpi/{feature-slug}/research/RESEARCH.md`

---

### 下一步

基于 **[GO/NO-GO]**：

**若 GO**：
1. 查看报告：`rpi/{feature-slug}/research/RESEARCH.md`
2. 进入规划：`/rpi:plan "{feature-slug}"`

**若 CONDITIONAL GO**：
1. 查看报告中的条件
2. 先解决条件再推进
3. 必要时重新 research

**若 DEFER**：
1. 查看报告中的时间建议
2. 适当时机再评估

**若 NO-GO**：
1. 查看理由
2. 评估替代方案
3. 归档以备后续参考

---

## 错误处理

**REQUEST.md 不存在**：
- 行为：停止并告知用户
- 提示："Feature request file `rpi/{feature-slug}/REQUEST.md` not found. Create the feature folder and REQUEST.md first (Step 1: Describe in Plan Mode)."

**需求过于模糊**：
- 行为：requirement-parser 会提出澄清问题
- 提示："Need more information. Please answer:"
- 下一步：等待答案后继续

**agent 失败或超时**：
- 行为：重试一次
- 若仍失败：询问用户是否在缺失研究的情况下继续

---

## 备注

- **使用时机**：完成 Step 1（Describe）创建功能目录后
- **关键门**：避免在不可行功能上浪费精力
- **RPI 工作流**：第 2 步（Describe → Research → Plan → Implement）

---

## 完成后的动作

**重要**：完成 research 后，始终提示用户压缩对话：

> **上下文管理**：该研究流程消耗了大量上下文。为后续步骤释放空间，请运行：
>
> ```
> /compact
> ```
>
> 该命令会总结对话并保留关键结论，减少后续 token 使用。
