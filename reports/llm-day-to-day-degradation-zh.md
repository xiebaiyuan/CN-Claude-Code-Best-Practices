# LLM 日常性能波动：神话 vs 现实

已部署 LLM 的性能会在日常波动吗？模型权重冻结后还能变吗？本报告深入分析真实原因、基础设施问题与心理因素。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<table width="100%">
<tr>
<td width="50%"><a href="https://x.com/nicksdot/status/2029520949176049704"><img src="assets/llm-degradation.png" alt="Twitter users reporting day-to-day Claude quality degradation" width="100%" /></a></td>
<td width="50%"><a href="https://x.com/levelsio/status/2029369159893569680"><img src="assets/llm-degradation-2.png" alt="Twitter users reporting day-to-day Claude quality degradation" width="100%" /></a></td>
</tr>
</table>

---
---

# 🔥 Claude Code Ops 4.6 分析：高推理

当 Anthropic 发布 Opus 4.6 这类模型时，**模型权重**（数十亿参数）是冻结的。训练极其昂贵（数百万美元与数周算力），不会隔夜重训。

但权重只是系统的一层。研究表明，至少有 **7 类机制** 会导致真实或感知到的质量波动，即使权重冻结。

| 问题 | 答案 |
|----------|--------|
| 发布后权重会变吗？ | **不会** —— 所有提供商确认 |
| 日常行为会变化吗？ | **会** —— 证明存在 ±8–14% 波动 |
| 是故意“降智”？ | **否** —— 无证据 |
| 基础设施 Bug 存在吗？ | **是** —— Anthropic 确认 3 个 bug，影响最高 16% 请求 |
| 心理因素影响吗？ | **是** —— 确认偏差与蜜月效应 |
| 系统提示/后训练会变吗？ | **会** —— 多家提供商证实 |
| 用户感知可信么？ | **部分可信** —— 原因真实，但感知会放大 |

---

## 完整推理栈

权重冻结，但 **其上 9 层** 都会影响行为：

```
┌──────────────────────────────────────────────┐
│  YOUR SESSION CONTEXT                        │  ← 会话内劣化
│  (accumulated errors, long conversations)    │
├──────────────────────────────────────────────┤
│  SYSTEM PROMPT                               │  ← 会更新
│  (safety rules, behavior instructions)       │
├──────────────────────────────────────────────┤
│  POST-TRAINING (RLHF / Fine-tuning)         │  ← 可悄然更新
│  (instruction following, safety alignment)   │
├──────────────────────────────────────────────┤
│  SAMPLING PARAMETERS                         │  ← 可服务端调整
│  (temperature, top-p, top-k)                 │
├──────────────────────────────────────────────┤
│  SPECULATIVE DECODING                        │  ← 草稿模型质量变化
│  (draft model predictions + verification)    │
├──────────────────────────────────────────────┤
│  MoE ROUTING / BATCH COMPOSITION             │  ← ±8–14% 已验证波动
│  (which experts activate per request)        │
├──────────────────────────────────────────────┤
│  HARDWARE ROUTING                            │  ← TPU vs GPU vs Trainium
│  (which cluster serves your request)         │
├──────────────────────────────────────────────┤
│  QUANTIZATION LEVEL                          │  ← 负载下可能变化
│  (FP16 vs INT8 vs INT4 precision)            │
├──────────────────────────────────────────────┤
│  COMPILER & RUNTIME                          │  ← XLA bug 已验证
│  (XLA:TPU, CUDA, hardware-specific code)     │
├──────────────────────────────────────────────┤
│  MODEL WEIGHTS (FROZEN)                      │  ← 权重不变
│  (billions of learned parameters)            │
└──────────────────────────────────────────────┘
```

关键认知：**权重冻结 ≠ 行为冻结**。这类似“发动机不变”却忽略了轮胎、路况、燃油质量与驾驶员状态。

---

## 已证实原因：基础设施 Bug

### Anthropic 2025 年 9 月事故复盘

Anthropic 公开复盘了 3 个基础设施 bug，导致 2025 年 8–9 月质量下降。官方声明：

> "We never reduce model quality due to demand, time of day, or server load. The problems our users reported were due to infrastructure bugs alone."

### Bug #1 — Context Window 路由错误

Sonnet 4 请求误路由到 1M 上下文服务器。

- **时间线**：8 月 5 日引入，8 月 29 日负载均衡调整后加剧
- **峰值影响**：最高 16% Sonnet 4 请求
- **用户影响**：约 30% Claude Code 用户至少遇到一次
- **隐蔽性**：路由“粘性”，一旦命中坏服务器会持续
- **修复**：9 月 4–18 日逐步修复

### Bug #2 — TPU 输出损坏

TPU 服务器配置错误导致 token 生成异常。

- **症状**：英文中出现泰文/中文字符，明显语法错误
- **影响**：Opus 4.1/4（8 月 25–28），Sonnet 4（8 月 25–9 月 2）
- **范围**：仅 Claude API；第三方平台未受影响
- **修复**：9 月 2 日回滚

### Bug #3 — XLA:TPU 编译器误编译（最隐蔽）

为修复精度问题的改动暴露了 **潜在编译器 bug**。

- **根因**：近似 top-k 操作在特定 batch 和配置下返回错误
- **难以发现**：行为取决于前后操作与调试工具
- **隐藏数月**：此前 workaround 被意外移除
- **影响**：Haiku 3.5 确认，Sonnet 4/Opus 3 部分
- **修复**：改用精确 top-k，接受性能损失

### 为什么难以检测

自动评估未能捕捉用户感知下降，因为模型常能从错误中恢复。不同平台、不同错误率让问题难以定位。

Claude 运行在 AWS Trainium、NVIDIA GPU、Google TPU 三种硬件上，硬件差异导致不同故障模式。

---

## 已证实原因：MoE 路由波动

MoE 架构仅激活部分“专家”，路由决定哪些专家参与推理。

Scale AI 研究指出：

> "Sparse MoE + batched inference 会产生不可预测结果，因为 batch 组合会影响路由，且 batch 内其他用户请求是不确定的。"

### 日常波动测量

| Provider | 日常波动 |
|----------|----------------|
| OpenAI (GPT-4) | ±10–12% |
| Anthropic (Claude) | ±8–11% |
| Google (Gemini) | ±9–14% |

示例：同一模型一天 jailbreak 抵抗 77%，另一天 63%，单日波动 14%。

这意味着即便无任何 bug，也会出现显著日常波动。A/B 测试很难检测 5% 的真实变化。

---

## 已证实原因：系统提示与后训练更新

### 系统提示变化

权重不变，但 **系统提示** 可随时更新。Claude 的系统提示历经多次变更，包含“热修复”指令。

Claude 3.7 系统提示含多个 hot-fix；Claude 4.0 删除这些指令，改由后训练强化学习解决。

### 后训练理论

公司可以更新 **微调与 RLHF** 而不改变权重。这会导致“模型没变”的说法成立，但行为仍改变。

---

## 已证实原因：静默模型切换

OpenAI 多次被记录发生模型切换：

- 移除模型选择器，用户从 GPT-4o 被切到 GPT-5
- GPT-4o 变成隐藏“legacy”，需手动打开
- 自动切换 bug 导致错误路由
- Plus 用户反映被切到“受限版本”

Sam Altman 承认 rollout “有点坎坷”。Reddit 上有大量抱怨。

这说明行业内静默模型切换 **确实发生**。

---

## 其他影响因素

### 负载下量化

为节省成本，可能在负载下使用量化模型（FP16 → INT8/INT4）。虽然效率提升，但质量会下降。

### Speculative Decoding

使用草稿模型预生成 token，再由主模型验证。理论上不影响分布，但实际接受率随领域变化，长上下文效果可能下降。

### 上下文污染

长会话中错误积累，模型看到自己的错误后会延续。这是“会话内变笨”的最常见原因。

**建议**：使用 `/compact` 或新会话重置上下文。

---

## 斯坦福研究与复杂性

2023 Stanford/UC Berkeley 研究（Chen, Zaharia, Zou）显示 GPT-4 在 3–6 月数学准确率 97.6% → 2.4%。

### 研究证实

- 同一 LLM 行为会随时间变化
- 不同能力可能反向变化
- 代码生成质量下降
- 提出 “LLM drift” 概念

### 方法学批评

- 3 月使用 temperature 0.0，6 月使用 1.0 —— 重大混杂因素
- 每任务仅 500 查询，样本小
- 数学问题本质为 yes/no 猜测模式变化
- 更可能是安全后训练更新导致

---

## 心理因素

### 确认偏差

一旦有人说“今天 Claude 变笨”，你会更关注错误。社交媒体放大这种效应。

### 蜜月效应

新模型有蜜月期，随后用户逐渐发现限制。模型未变，期待变了。

### 任务难度波动

任务本身难度不同，容易误以为模型变差。

### “周末 Claude” 迷思

缺乏证据支持日历模式。分析显示没有一致的周末效应。

### 随机性

LLM 是概率系统，同一提示可能得到不同结果。连续差输出可能只是运气。

---

## 结论

用户体验下降 **真实存在但常被误归因**：

- **正确**：某些天质量确实下降
- **错误**：模型被故意“降智”

主要原因包括：

1. **基础设施 Bug**（Anthropic 复盘，影响最高 16%）
2. **MoE 路由波动**（±8–14% 已测）
3. **系统提示与后训练更新**
4. **硬件差异**（TPU/GPU/Trainium）
5. **上下文污染**（会话内质量下降）
6. **确认偏差**
7. **随机波动**

测量问题严重：日常噪声 8–14%，难以区分 5% 真实变化。这就是“全是心理”与“被降智”两派都觉得自己对的原因。

---

## Sources

- [Anthropic: A Postmortem of Three Recent Issues](https://www.anthropic.com/engineering/a-postmortem-of-three-recent-issues)
- [Anthropic Reveals Three Infrastructure Bugs — InfoQ](https://www.infoq.com/news/2025/10/anthropic-infrastructure-bugs/)
- [How is ChatGPT's Behavior Changing Over Time? — Stanford/UC Berkeley](https://arxiv.org/abs/2307.09009)
- [The Truth About ChatGPT's Degrading Capabilities — TechTalks](https://bdtechtalks.com/2023/07/24/chatgpt-capabilities-degrading-study/)
- [LLMs Are Getting Dumber and We Have No Idea Why — Ignorance.ai](https://www.ignorance.ai/p/llms-are-getting-dumber-and-we-have)
- [When Claude Forgets How to Code — Robert Matsuoka](https://hyperdev.matsuoka.com/p/when-claude-forgets-how-to-code)
- [Smoothing Out LLM Variance — Scale AI](https://scale.com/blog/smoothing-out-llm-variance)
- [What We Can Learn from Anthropic's System Prompt Updates — PromptLayer](https://blog.promptlayer.com/what-we-can-learn-from-anthropics-system-prompt-updates/)
- [Claude's System Prompt Changes Reveal Anthropic's Priorities — Drew Breunig](https://www.dbreunig.com/2025/06/03/comparing-system-prompts-across-claude-versions.html)
- [Complaints About Secretly Switching Models — OpenAI Forum](https://community.openai.com/t/complaints-about-secretly-switching-models/1360150)
- [Speculative Decoding — BentoML LLM Inference Handbook](https://bentoml.com/llm/inference-optimization/speculative-decoding)
- [A Visual Guide to Mixture of Experts — Maarten Grootendorst](https://newsletter.maartengrootendorst.com/p/a-visual-guide-to-mixture-of-experts)

---
---

# 🔥 Codex 5.3 高推理结论

### 报告范围

解释为什么用户会在短时间窗口内感到 Claude 质量下降，而 Codex 5.3 在编码任务上更稳或更强。重点是短期生产行为，而非永久模型排名。

报告日期：2026-03-05。

### 观察到的模式

常见模式：

1. 一段时间质量正常
2. 质量似乎下降数天
3. 质量回到接近原基线

这通常是服务栈或 rollout 现象，而非模型能力永久下降。永久下降不会在短期内恢复。

### 高推理：为何 Codex 5.3 在坏窗口里更强

Codex 5.3 在另一提供商质量下降时显得更强的原因可能叠加：

1. 产品目标适配：Codex 5.3 针对编码与 agentic 工作流优化，同样的基础能力可获得更好的编码效果。
2. 推理策略差异：不同提供商的延迟/推理深度/采样参数不同，保守策略可能显得更“聪明”。
3. 服务路径隔离：不同提供商的路由、编译、部署管线不同，一方事故不影响另一方。
4. Rollout 时序：一方 rollout 时另一方稳定，短期质量差异会很大。
5. 会话污染效应：长对话错误积累放大问题，另一助手可能因会话重置或工具循环更快而显得更好。

### 详细结论

对于“Claude 变弱 4 天后恢复”的反馈，最可能解释：

1. 服务端事故、路由错误、解码/运行时 bug 或 rollout 回归影响部分请求
2. 持续时间足以被用户重复感知
3. 事故被修复或回滚
4. 质量快速回到原基线

在此期间 Codex 5.3 会显著更好，因为它未命中该事故路径，并且编码优化放大了实际效果差距。

### 假设排序

| 假设 | 概率 | 理由 |
|------------|------------|-----------|
| 供应商事故 + 回滚 | 高 | 最符合多日下降后快速恢复 |
| 服务配置变更（采样/延迟/推理预算） | 高 | 常见、可导致突变行为 |
| 静默 alias/snapshot 切换 | 中高 | 可无提示改变行为 |
| 仅 prompt 漂移 + 上下文污染 | 中 | 可影响会话，但难解释广泛多日 |
| 永久基础模型退化 | 低 | 不符合快速恢复 |

### 如何验证或证伪

要从推断到证明，可收集同任务跨天的请求遥测：

1. 请求时模型 ID 与 alias/snapshot
2. 后端指纹或版本标记
3. 解码参数（temperature/top_p/top_k/max tokens）
4. 延迟、超时、错误率
5. 固定 benchmark 质量分数
6. 会话长度与上下文深度

若质量下降与事故/配置/指纹变更一致，则可证实事故假设。若仅长会话下降，则上下文污染为主因。

### 工程实践建议

降低日常波动：

1. 尽量固定模型快照而非浮动别名
2. 记录请求元数据（model ID、参数、延迟、错误、质量标签）
3. 跑固定 daily canary 并监控回归
4. 多轮失败后重置/压缩会话
5. 准备备用模型/供应商路径
6. 内部监控区分“模型质量”与“服务可靠性”

### 最终结论

在 Claude 短期下降窗口中 Codex 5.3 显得更好，是技术上合理且常见的结果。最强解释不是模型永久退化，而是某一方短期服务路径问题叠加编码优化差异。
