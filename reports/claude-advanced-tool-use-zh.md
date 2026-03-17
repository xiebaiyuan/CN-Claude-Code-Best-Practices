# Claude 高级工具使用模式

API 级特性（已 GA），用于减少 token 消耗、降低延迟、提升工具调用准确性。随 Opus/Sonnet 4.6 发布。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 目录

1. [Overview](#overview)
2. [Programmatic Tool Calling (PTC)](#programmatic-tool-calling-ptc)
3. [Dynamic Filtering for Web Search/Fetch](#dynamic-filtering-for-web-searchfetch)
4. [Tool Search Tool](#tool-search-tool)
5. [Tool Use Examples](#tool-use-examples)
6. [Claude Code Relevance](#claude-code-relevance)

---

## Overview

| 特性 | 解决问题 | Token 节省 | 可用性 |
|---------|---------------|---------------|--------------|
| Programmatic Tool Calling | 多步工具循环消耗大量 tokens | ~37% 降幅 | API、Foundry（GA） |
| Dynamic Filtering | Web 搜索/抓取内容冗余 | ~24% 输入 token 降幅 | API、Foundry（GA） |
| Tool Search Tool | 工具定义过多导致上下文膨胀 | ~85% 降幅 | API、Foundry（GA） |
| Tool Use Examples | 仅 schema 无法表达使用模式 | 72% → 90% 准确率 | API、Foundry（GA） |

所有特性自 2026-02-18 起 **正式可用**。

**策略性分层** —— 从最大瓶颈开始：
- 工具定义膨胀 → Tool Search Tool
- 中间结果过大 → Programmatic Tool Calling
- Web 搜索噪声 → Dynamic Filtering
- 参数错误 → Tool Use Examples

---

## Programmatic Tool Calling (PTC)

<img src="assets/programmatic-tool-calling-diagram.svg" alt="PTC Diagram — Traditional vs Programmatic Tool Calling" width="100%" />

### 范式转变

**之前（传统调用）：**
```
User prompt → Claude → Tool call 1 → Response 1 → Claude → Tool call 2 → Response 2 → Claude → Tool call 3 → Response 3 → Claude → Final answer
```
每次工具调用都需要完整的模型回合，3 个工具 = 3 次推理。

**之后（PTC）：**
```
User prompt → Claude → writes Python script → Script calls Tool 1, Tool 2, Tool 3 internally → stdout → Claude → Final answer
```
Claude 编写代码并在沙盒中编排工具。仅最终 `stdout` 进入上下文，3 个工具 = 1 次推理。

### 工作方式

1. 工具声明 `allowed_callers: ["code_execution_20250825"]`
2. Claude 在沙盒中写 Python 调用工具（async）
3. 工具调用时沙盒暂停，API 返回 `tool_use`
4. 工具结果返回给运行中的代码，而不是 Claude 上下文
5. 代码继续执行并可调用更多工具
6. 仅最终 `stdout` 进入 Claude 上下文

### 关键配置

```json
{
  "tools": [
    {
      "type": "code_execution_20250825",
      "name": "code_execution"
    },
    {
      "name": "query_database",
      "description": "Execute a SQL query. Returns rows as JSON objects with fields: id (str), name (str), revenue (float).",
      "input_schema": {
        "type": "object",
        "properties": {
          "sql": { "type": "string", "description": "SQL query to execute" }
        },
        "required": ["sql"]
      },
      "allowed_callers": ["code_execution_20250825"]
    }
  ]
}
```

### `allowed_callers` 字段

| 值 | 行为 |
|-------|----------|
| `["direct"]` | 仅传统调用（默认） |
| `["code_execution_20250825"]` | 仅允许在 Python 沙盒中调用 |
| `["direct", "code_execution_20250825"]` | 两种方式均可 |

**建议**：每个工具只选一种调用模式，减少歧义。

### 响应中的 `caller` 字段

每个 tool_use 块包含 `caller`：

```json
// Direct (traditional)
{ "caller": { "type": "direct" } }

// Programmatic (from code execution)
{ "caller": { "type": "code_execution_20250825", "tool_id": "srvtoolu_abc123" } }
```

### 高级模式

**批处理** —— 1 次推理处理 N 项：
```python
regions = ["West", "East", "Central", "North", "South"]
results = {}
for region in regions:
    data = await query_database(f"SELECT SUM(revenue) FROM sales WHERE region='{region}'")
    results[region] = data[0]["revenue"]

top = max(results.items(), key=lambda x: x[1])
print(f"Top region: {top[0]} with ${top[1]:,}")
```

**提前终止** —— 满足条件即停止：
```python
endpoints = ["us-east", "eu-west", "apac"]
for endpoint in endpoints:
    status = await check_health(endpoint)
    if status == "healthy":
        print(f"Found healthy endpoint: {endpoint}")
        break
```

**条件性工具选择：**
```python
file_info = await get_file_info(path)
if file_info["size"] < 10000:
    content = await read_full_file(path)
else:
    content = await read_file_summary(path)
print(content)
```

**数据过滤** —— 减少进入上下文的数据：
```python
logs = await fetch_logs(server_id)
errors = [log for log in logs if "ERROR" in log]
print(f"Found {len(errors)} errors")
for error in errors[-10:]:
    print(error)
```

### 模型兼容性

| 模型 | 支持 |
|-------|-----------|
| Claude Opus 4.6 | Yes |
| Claude Sonnet 4.6 | Yes |
| Claude Sonnet 4.5 | Yes |
| Claude Opus 4.5 | Yes |

### 限制

| 限制 | 说明 |
|-----------|--------|
| **不支持 Bedrock/Vertex** | 仅 API 与 Foundry |
| **不支持 MCP 工具** | MCP 连接器不可程序化调用 |
| **不支持 Web search/fetch** | Web 工具不可用 |
| **不支持结构化输出** | `strict: true` 工具不兼容 |
| **不能强制 tool_choice** | 不能强制 PTC |
| **容器生命周期** | 约 4.5 分钟 |
| **ZDR** | 不覆盖 Zero Data Retention |
| **工具结果为字符串** | 需防止代码注入 |

### 适用场景

| 适合 | 不太适合 |
|----------------|------------|
| 大数据集聚合处理 | 单次简单工具调用 |
| 3+ 连续依赖工具调用 | 需要即时用户反馈的工具 |
| 过滤/转换中间结果 | 极快操作（开销大于收益） |
| 并行处理大量项 | |
| 基于中间结果的条件逻辑 | |

### Token 效率

- 程序化工具结果 **不进入 Claude 上下文** —— 只有最终 `stdout`
- 中间处理在代码层完成
- 10 个程序化调用 ≈ 传统 10 调用的 1/10 tokens

---

## Dynamic Filtering for Web Search/Fetch

### 问题

Web 搜索/抓取工具会把完整 HTML 放进上下文，大量内容无关，浪费 tokens 且降低准确率。

### 解决方案

Claude **编写并执行 Python 代码过滤结果**，在进入上下文前提取相关内容。

### 工作方式

**之前：**
```
Query → Search results → Fetch full HTML × N pages → All content enters context → Claude reasons over everything
```

**之后：**
```
Query → Search results → Claude writes filtering code → Code extracts relevant content only → Filtered results enter context
```

### API 配置

使用新版工具类型并添加 beta header：

```json
{
  "model": "claude-opus-4-6",
  "max_tokens": 4096,
  "tools": [
    {
      "type": "web_search_20260209",
      "name": "web_search"
    },
    {
      "type": "web_fetch_20260209",
      "name": "web_fetch"
    }
  ]
}
```

**Header required**：`anthropic-beta: code-execution-web-tools-2026-02-09`

**默认启用**（Sonnet 4.6 / Opus 4.6 + 新工具类型）。

### Benchmark 结果

**BrowseComp**（网页查找）：

| 模型 | 无过滤 | 有过滤 | 提升 |
|-------|-------------------|----------------|-------------|
| Sonnet 4.6 | 33.3% | **46.6%** | +13.3 pp |
| Opus 4.6 | 45.3% | **61.6%** | +16.3 pp |

**DeepsearchQA**（多步研究，F1）：

| 模型 | 无过滤 | 有过滤 | 提升 |
|-------|-------------------|----------------|-------------|
| Sonnet 4.6 | 52.6% | **59.4%** | +6.8 pp |
| Opus 4.6 | 69.8% | **77.3%** | +7.5 pp |

**Token 效率**：平均输入 token 减少 24%。Sonnet 4.6 成本下降，Opus 4.6 因过滤代码复杂可能略升。

### 使用场景

- 过滤技术文档
- 验证多来源引用
- 交叉检索搜索结果
- 多步骤研究问题
- 从大页面中找特定数据

---

## Tool Search Tool

### 问题

加载全部工具定义会占用上下文。例如 50 个 MCP 工具，每个 1.5K tokens，总计 75K tokens。

### 解决方案

对低频工具设置 `defer_loading: true`，它们不进入初始上下文，Claude 通过 Tool Search 需要时发现。

### 配置

```json
{
  "tools": [
    {
      "type": "mcp_toolset",
      "mcp_server_name": "google-drive",
      "default_config": { "defer_loading": true },
      "configs": {
        "search_files": { "defer_loading": false }
      }
    }
  ]
}
```

### 最佳实践

- 保留 3-5 个常用工具常驻，其他延迟加载
- 工具名称与描述清晰，便于搜索
- 在系统提示中注明能力

### 适用场景

- 工具定义 > 10K tokens
- 10+ 个工具
- 多个 MCP 服务器
- 工具选择准确率下降

### Token 节省

约 85% 降幅（77K → 8.7K）。

### Claude Code 对应能力

Claude Code 自 v2.1.7 起内置 **MCP tool search auto mode**。当 MCP 工具描述超过上下文 10% 时，自动延迟加载并通过 `MCPSearch` 发现。可用 `ENABLE_TOOL_SEARCH=auto:N` 调整阈值。

---

## Tool Use Examples

### 问题

JSON schema 不能表达：
- 何时使用可选参数
- 参数组合是否合理
- 格式约定（日期、ID 模式）
- 嵌套结构使用方式

### 解决方案

在工具定义中加入 `input_examples`，提供真实用法示例。

### 配置

```json
{
  "name": "create_ticket",
  "description": "Create a support ticket",
  "input_schema": {
    "type": "object",
    "properties": {
      "title": { "type": "string" },
      "priority": { "type": "string", "enum": ["low", "medium", "high", "critical"] },
      "assignee": { "type": "string" },
      "labels": { "type": "array", "items": { "type": "string" } }
    },
    "required": ["title"]
  },
  "input_examples": [
    {
      "title": "Login page returns 500 error",
      "priority": "critical",
      "assignee": "oncall-team",
      "labels": ["bug", "auth", "production"]
    },
    {
      "title": "Add dark mode support",
      "priority": "low",
      "labels": ["feature-request", "ui"]
    },
    {
      "title": "Update API docs for v2 endpoints"
    }
  ]
}
```

### 最佳实践

- 使用真实数据，避免 “example_value”
- 展示多样性：最小/部分/完整示例
- 保持精简：每个工具 1-5 个示例
- 解决歧义：聚焦行为清晰度
- 展示参数关联（如 critical 通常带 assignee）

### 结果

复杂参数准确率 72% → 90%。

---

## Claude Code Relevance

### Claude Code 用户直接可用

| 特性 | Claude Code 状态 | 行动 |
|---------|-------------------|--------|
| Tool Search | v2.1.7 起内置 MCPSearch auto | 如 MCP 多可调 `ENABLE_TOOL_SEARCH=auto:N` |
| Dynamic Filtering | CLI 不支持（API web 工具） | Agent SDK 用户可用 |
| PTC | CLI 不支持 | Agent SDK 可用 |
| Tool Use Examples | CLI 不可配置 | MCP server 作者可用 |

### 对 Agent SDK 开发者

若使用 `@anthropic-ai/claude-agent-sdk`，PTC 可立即应用：

1. 在 tools 中加入 `code_execution_20250825`
2. 在适合的工具上设置 `allowed_callers`
3. 实现 tool 结果循环（暂停 → 回传 → 继续）
4. 工具返回结构化数据（JSON），便于解析

### 对 MCP server 作者

添加 `input_examples` 可提高 Claude 使用工具的准确性：
- 在 schema 中加入示例
- 在 description 中清晰说明返回格式（PTC 需要解析）

---

## Sources

- [Anthropic Engineering: Advanced Tool Use](https://www.anthropic.com/engineering/advanced-tool-use)
- [Programmatic Tool Calling Documentation](https://platform.claude.com/docs/en/agents-and-tools/tool-use/programmatic-tool-calling)
- [Code Execution Tool Documentation](https://platform.claude.com/docs/en/agents-and-tools/tool-use/code-execution-tool)
- [Improved Web Search with Dynamic Filtering](https://claude.com/blog/improved-web-search-with-dynamic-filtering)
