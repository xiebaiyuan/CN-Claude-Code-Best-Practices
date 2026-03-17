# Claude Agent SDK vs Claude CLI：系统提示词与输出一致性

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

![SDK vs CLI System Prompts Diagram](assets/sdk-vs-cli-diagram.svg)

---

## Executive Summary

将同一条消息（如“挪威的首都是哪？”）通过 **Claude Agent SDK** 与 **Claude CLI（Claude Code）** 发送时，两者携带的系统提示词不同。CLI 使用 **模块化系统提示架构**（基础约 269 tokens，按功能条件加载更多上下文），而 SDK 默认是最小提示。**即便配置相同，也无法保证输出一致**，因为没有 seed 参数，且 Claude 本身具有非确定性。

---

## 1. 系统提示对比

### Claude CLI（Claude Code）

CLI 采用 **模块化系统提示架构**，基础约 269 tokens，并按需加载：

| 组件 | 描述 | 加载方式 |
|-----------|-------------|---------|
| **Base System Prompt** | 核心行为指令 | 总是（约 269 tokens） |
| **Tool Instructions** | 18+ 内置工具（Write/Read/Edit/Bash/TodoWrite 等） | 总是 |
| **Coding Guidelines** | 代码风格/格式/安全规范 | 总是 |
| **Safety Rules** | 拒绝规则、注入防护、风险规避 | 总是 |
| **Response Style** | 语气、冗长度、解释深度、emoji 使用 | 总是 |
| **Environment Context** | 工作目录、git 状态、平台信息 | 总是 |
| **Project Context** | CLAUDE.md、settings、hooks 配置 | 条件加载 |
| **Subagent Prompts** | Plan/Explore/Task prompts | 条件加载 |
| **Security Review** | 扩展安全说明（约 2,610 tokens） | 条件加载 |

**关键特征：**
- **模块化架构**，110+ 条提示词按需加载
- 基础提示约 269 tokens，总量随功能变化
- 内置安全与注入防护层
- 自动加载当前目录 CLAUDE.md
- 交互模式保留会话上下文

### Claude Agent SDK

SDK 默认 **最小系统提示**：

| 组件 | 描述 | Token 影响 |
|-----------|-------------|--------------|
| **Essential Tool Instructions** | 仅提供的工具说明 | 最少 |
| **Basic Safety** | 基础安全提示 | 最少 |

**关键特征：**
- 默认无编码指南或风格偏好
- 无项目上下文，除非显式配置
- 无完整工具说明
- 需显式配置才能接近 CLI 行为

---

## 2. 不同接口发送内容

### 示例：“挪威的首都是哪？”

#### 通过 Claude CLI

```
System Prompt: [modular, ~269+ base tokens]
├── Base system prompt (~269 tokens)
├── Tool instructions (Write, Read, Edit, Bash, Grep, Glob, etc.)
├── Git safety protocols
├── Code reference guidelines
├── Professional objectivity instructions
├── Security and injection defense rules
├── Environment context (OS, directory, date)
├── CLAUDE.md content (if present) [conditional]
├── MCP tool descriptions (if configured) [conditional]
├── Plan/Explore mode prompts [conditional]
└── Session/conversation context

User Message: "What is the capital of Norway?"
```

#### 通过 Agent SDK（默认）

```
System Prompt: [minimal]
├── Essential tool instructions (if any tools provided)
└── Basic operational context

User Message: "What is the capital of Norway?"
```

#### 通过 Agent SDK（使用 `claude_code` preset）

```typescript
const response = await query({
  prompt: "What is the capital of Norway?",
  options: {
    systemPrompt: {
      type: "preset",
      preset: "claude_code"
    }
  }
});
```

```
System Prompt: [modular, matches CLI]
├── Full Claude Code system prompt
├── Tool instructions
├── Coding guidelines
└── Safety rules

// NOTE: Still does NOT include CLAUDE.md unless settingSources is configured
```

---

## 3. 自定义方式

### Claude CLI 自定义

| 方法 | 命令 | 效果 |
|--------|---------|--------|
| **追加提示** | `claude -p "..." --append-system-prompt "..."` | 保留默认提示并追加指令 |
| **替换提示** | `claude -p "..." --system-prompt "..."` | 完全替换系统提示 |
| **项目上下文** | CLAUDE.md | 自动加载、持久 |
| **输出风格** | `/output-style [name]` | 应用输出风格 |

### Agent SDK 自定义

| 方法 | 配置 | 效果 |
|--------|---------------|--------|
| **自定义提示** | `systemPrompt: "..."` | 完全替换（丢失工具说明） |
| **preset + append** | `systemPrompt: { type: "preset", preset: "claude_code", append: "..." }` | 保留 CLI 功能并追加 |
| **CLAUDE.md 加载** | `settingSources: ["project"]` | 加载项目级指令 |
| **输出风格** | `settingSources: ["user"]` 或 `settingSources: ["project"]` | 加载输出风格 |

### 配置对比表

| 特性 | CLI 默认 | SDK 默认 | SDK + preset |
|---------|-------------|-------------|-----------------|
| 工具说明 | ✅ 完整 | ❌ 最小 | ✅ 完整 |
| 编码指南 | ✅ | ❌ | ✅ |
| 安全规则 | ✅ | ❌ 基础 | ✅ |
| CLAUDE.md 自动加载 | ✅ | ❌ | ❌* |
| 项目上下文 | ✅ 自动 | ❌ | ❌* |

*需要显式 `settingSources: ["project"]`

---

## 4. 输出一致性保证

### 关键结论：无法保证确定性

**Claude Messages API 没有 seed 参数**，这是架构层面限制。

### 影响一致性的因素

| 因素 | 描述 | 可控？ |
|--------|-------------|-----------|
| **系统提示不同** | CLI vs SDK 默认不同 | ✅（通过配置） |
| **浮点计算** | 并行硬件差异 | ❌ |
| **MoE 路由** | 混合专家架构变化 | ❌ |
| **批处理/调度** | 云基础设施差异 | ❌ |
| **数值精度** | 推理引擎差异 | ❌ |
| **模型快照** | 版本更新变动 | ❌ |

### Temperature 与采样

即便 `temperature=0.0`（贪心解码）：
- **仍无确定性保证**
- 仍可能出现微小差异
- 已知问题：[Claude CLI 相同输入非确定输出](https://github.com/anthropics/claude-code/issues/3370)

---

## 5. 最大化一致性

要让 SDK 与 CLI 输出 **尽可能一致**：

### Agent SDK 配置

```typescript
import Anthropic from "@anthropic-ai/sdk";

const client = new Anthropic();

// Option 1: Use claude_code preset
const response = await client.messages.create({
  model: "claude-sonnet-4-20250514",
  max_tokens: 1024,
  // Match CLI system prompt as closely as possible
  system: "Your exact system prompt matching CLI",
  messages: [
    { role: "user", content: "What is the capital of Norway?" }
  ],
  // Use greedy decoding for maximum consistency
  temperature: 0
});

// Option 2: With Agent SDK query function
import { query } from "@anthropic-ai/agent-sdk";

for await (const message of query({
  prompt: "What is the capital of Norway?",
  options: {
    systemPrompt: {
      type: "preset",
      preset: "claude_code"
    },
    temperature: 0,
    model: "claude-sonnet-4-20250514",
    // Load project context like CLI does
    settingSources: ["project"]
  }
})) {
  // Process response
}
```

### CLI 配置

```bash
# Match the SDK configuration as closely as possible
claude -p "What is the capital of Norway?" \
  --model claude-sonnet-4-20250514 \
  --temperature 0
```

### 仍无法保证

即使配置完全一致：
- 仍可能每次输出不同
- SDK 与 CLI 之间仍可能不一致
- 没有 seed 无法强制复现

---

## 6. 实际影响

### 何时用哪个接口

| 使用场景 | 推荐接口 | 理由 |
|----------|----------------------|--------|
| 交互式开发 | Claude CLI | 全套工具、项目上下文 |
| 程序化集成 | Agent SDK | 更细粒度控制 |
| 一致性 API 输出 | Agent SDK + 自定义 prompt | 更可控系统提示 |
| 批处理 | Agent SDK | 更适合自动化 |
| 临时任务 | Claude CLI | 上手快、上下文就绪 |

### 设计建议

1. **不要依赖完全可复现**
   - 系统应能容忍小幅输出差异
   - 使用结构化输出与校验

2. **对一致性要求高的生产流水线：**
   - 能缓存就缓存
   - 使用 JSON schema 验证
   - 结合确定性逻辑与验证
   - 必要时多次生成取一致性

3. **在 SDK 中匹配 CLI 行为：**
   ```typescript
   systemPrompt: {
     type: "preset",
     preset: "claude_code",
     append: "Your additional instructions"
   },
   settingSources: ["project", "user"]
   ```

---

## 7. 系统提示 token 影响

| 配置 | 架构 | 备注 |
|---------------|-------------|-------|
| SDK（最小） | 最小默认 | 仅必要工具说明 |
| SDK（claude_code preset） | 模块化（~269+ base） | 接近 CLI，随功能变化 |
| CLI（默认） | 模块化（~269+ base） | 条件加载额外上下文 |
| CLI（含 MCP） | 模块化 + MCP | MCP 工具描述会显著增加 tokens |

**说明：** Claude Code 采用模块化架构，110+ 系统提示字符串。基础提示约 269 tokens，组件在 18–2,610 tokens 不等。

**影响：** SDK 的最小默认为任务留出更多上下文，但也失去 Claude Code 的完整能力。

---

## 8. 总结表

| 维度 | Claude CLI | Agent SDK（默认） | Agent SDK（Preset） |
|--------|------------|--------------------|--------------------|
| **系统提示** | 模块化（~269+ base） | 最小 | 模块化（接近 CLI） |
| **工具说明** | 18+ 内置 | 仅提供 | 18+ 内置 |
| **CLAUDE.md 自动加载** | 是 | 否 | 否（需配置） |
| **编码指南** | 是 | 否 | 是 |
| **安全规则** | 完整 | 基础 | 完整 |
| **温度控制** | 支持 | 支持 | 支持 |
| **确定性保证** | 无 | 无 | 无 |
| **输出一致** | N/A | 否（vs CLI） | 更接近，但不保证 |

---

## 9. 结论

**问：SDK 与 CLI 的系统提示有何不同？**

CLI 使用 **模块化系统提示架构**（基础 ~269 tokens，110+ 可选组件：工具说明、编码指南、安全规则、项目上下文等）。SDK 默认 **最小提示**，但可通过 `claude_code` preset 近似 CLI 行为。

**问：能保证输出一致吗？**

**不能。** 即便系统提示一致、输入一致、`temperature=0`，仍不保证输出一致，原因包括：
- API 无 seed 参数
- 浮点运算差异
- 基础设施非确定性
- MoE 路由差异

**建议：** 系统设计应容忍输出差异，必要时使用结构化输出、缓存与验证层。

---

## Sources

- [Modifying System Prompts - Agent SDK](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/sdk#modifying-system-prompts)
- [Claude Code CLI Reference](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/cli)
- [Claude Code Headless Mode](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/headless)
- [Claude Code Best Practices - Anthropic Engineering](https://www.anthropic.com/engineering/claude-code-best-practices)
- [Claude Messages API Reference](https://docs.anthropic.com/en/api/messages)
- [GitHub Issue #3370: Non-deterministic output](https://github.com/anthropics/claude-code/issues/3370)
- [Claude Code System Prompts Repository](https://github.com/Piebald-AI/claude-code-system-prompts)
- [Why Deterministic Output from LLMs is Nearly Impossible](https://unstract.com/blog/understanding-why-deterministic-output-from-llms-is-nearly-impossible/)

---

*This report was generated by Claude Code using the Opus 4.5 model on February 3, 2026.*
