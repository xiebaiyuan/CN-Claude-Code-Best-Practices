# MCP Servers 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2002%2C%202026%2012%3A30%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../.mcp.json)

MCP（Model Context Protocol）服务器让 Claude Code 连接外部工具、数据库与 API。本指南涵盖日常推荐服务器与配置最佳实践。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 日常常用 MCP 服务器

> *“装了 15 个 MCP 服务器，以为越多越好，结果每天只用 4 个。”* — [r/mcp](https://reddit.com/r/mcp/comments/1mj0fxs/)（682 赞）

| MCP 服务器 | 作用 | 资源 |
|------------|-------------|-----------|
| [**Context7**](https://github.com/upstash/context7) | 将最新库文档注入上下文，避免基于过期训练数据的幻觉 API | [Reddit: “目前为止最强编程 MCP”】【https://reddit.com/r/mcp/comments/1qarjqm/】 · [npm](https://www.npmjs.com/package/@upstash/context7-mcp) |
| [**Playwright**](https://github.com/microsoft/playwright-mcp) | 浏览器自动化 —— 自主实现、测试并验证 UI 功能；截图、导航、表单测试 | [Reddit: 前端必备](https://reddit.com/r/mcp/comments/1m59pk0/) · [Docs](https://playwright.dev/) |
| [**Claude in Chrome**](https://github.com/nicobailon/claude-code-in-chrome-mcp) | 连接到真实 Chrome 浏览器 —— 查看控制台、网络、DOM，调试用户看到的真实内容 | [Reddit: “调试神器”](https://reddit.com/r/mcp/comments/1qarjqm/5_mcps_that_have_genuinely_made_me_10x_faster/nza0i7t/) · [对比报告](../reports/claude-in-chrome-v-chrome-devtools-mcp.md) |
| [**DeepWiki**](https://github.com/devanshusemwal/deepwiki-mcp) | 为任意 GitHub 仓库生成结构化 wiki 文档 —— 架构、API 面、关系图 | [Reddit: “与 Context7 组合使用更佳”](https://reddit.com/r/mcp/comments/1qarjqm/) |
| [**Excalidraw**](https://github.com/antonpk1/excalidraw-mcp-app) | 从提示词生成架构图、流程图、系统设计草图 | [GitHub](https://github.com/antonpk1/excalidraw-mcp-app) |

研究（Context7/DeepWiki）-> 调试（Playwright/Chrome）-> 文档（Excalidraw）

---

## 配置

MCP 服务器可在项目根目录的 `.mcp.json`（项目级）或 `~/.claude.json`（用户级）中配置。

### 服务器类型

| 类型 | 传输方式 | 示例 |
|------|-----------|---------|
| **stdio** | 启动本地进程 | `npx`、`python`、可执行文件 |
| **http** | 连接远程 URL | HTTP/SSE 端点 |

### `.mcp.json` 示例

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@upstash/context7-mcp"]
    },
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp"]
    },
    "deepwiki": {
      "command": "npx",
      "args": ["-y", "deepwiki-mcp"]
    },
    "remote-api": {
      "type": "http",
      "url": "https://mcp.example.com/mcp"
    }
  }
}
```

使用环境变量注入 secrets，避免在 `.mcp.json` 中提交 API key：

```json
{
  "mcpServers": {
    "remote-api": {
      "type": "http",
      "url": "https://mcp.example.com/mcp?token=${MCP_API_TOKEN}"
    }
  }
}
```

### MCP 服务器相关设置

`.claude/settings.json` 中以下设置控制 MCP 服务器的批准行为：

| 键 | 类型 | 说明 |
|-----|------|-------------|
| `enableAllProjectMcpServers` | boolean | 自动批准 `.mcp.json` 中的所有服务器 |
| `enabledMcpjsonServers` | array | 自动批准的服务器白名单 |
| `disabledMcpjsonServers` | array | 拒绝的服务器黑名单 |

### MCP 工具的权限规则

MCP 工具在权限规则中使用 `mcp__<server>__<tool>` 命名：

```json
{
  "permissions": {
    "allow": [
      "mcp__*",
      "mcp__context7__*",
      "mcp__playwright__browser_snapshot"
    ],
    "deny": [
      "mcp__dangerous-server__*"
    ]
  }
}
```

---

## MCP 作用域

MCP 服务器可在三层作用域中定义：

| 作用域 | 位置 | 目的 |
|-------|----------|---------|
| **Project** | `.mcp.json`（仓库根目录） | 团队共享服务器，提交到 git |
| **User** | `~/.claude.json`（`mcpServers` 键） | 个人跨项目服务器 |
| **Subagent** | Agent frontmatter（`mcpServers` 字段） | 子代理专属服务器 |

优先级：Subagent > Project > User

---

## Sources

- [MCP Servers — Claude Code Docs](https://code.claude.com/docs/en/mcp)
- [Model Context Protocol Specification](https://modelcontextprotocol.io/)
- [5 MCPs that have genuinely made me 10x faster — r/mcp](https://reddit.com/r/mcp/comments/1qarjqm/)
- [MCP Server Overload Discussion — r/mcp](https://reddit.com/r/mcp/comments/1mj0fxs/)
