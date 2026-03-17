# CLI 启动参数最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2002%2C%202026-white?style=flat&labelColor=555)

在终端启动 Claude Code 时的启动参数、顶级子命令与启动环境变量参考。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 目录

1. [Session Management](#session-management)
2. [Model & Configuration](#model--configuration)
3. [Permissions & Security](#permissions--security)
4. [Output & Format](#output--format)
5. [System Prompt](#system-prompt)
6. [Agent & Subagent](#agent--subagent)
7. [MCP & Plugins](#mcp--plugins)
8. [Directory & Workspace](#directory--workspace)
9. [Budget & Limits](#budget--limits)
10. [Integration](#integration)
11. [Initialization & Maintenance](#initialization--maintenance)
12. [Debug & Diagnostics](#debug--diagnostics)
13. [Settings Override](#settings-override)
14. [Version & Help](#version--help)
15. [Subcommands](#subcommands)
16. [Environment Variables](#environment-variables)

---

## Session Management

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--continue` | `-c` | 继续当前目录最近一次会话 |
| `--resume` | `-r` | 按 ID 或名称恢复指定会话，或打开交互式选择器 |
| `--from-pr <NUMBER\|URL>` | | 恢复与指定 GitHub PR 关联的会话 |
| `--fork-session` | | 恢复时创建新的会话 ID（与 `--resume` 或 `--continue` 搭配） |
| `--session-id <UUID>` | | 使用指定会话 ID（必须是有效 UUID） |
| `--no-session-persistence` | | 禁用会话持久化（仅打印模式） |
| `--remote` | | 在 claude.ai 创建新的 Web 会话 |
| `--teleport` | | 在本地终端恢复 Web 会话 |

---

## Model & Configuration

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--model <NAME>` | | 设置模型别名（`sonnet`、`opus`、`haiku`）或完整模型 ID |
| `--fallback-model <NAME>` | | 默认模型拥塞时自动回退（仅打印模式） |
| `--betas <LIST>` | | API 请求携带的 Beta 头（仅 API key 用户） |

---

## Permissions & Security

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--dangerously-skip-permissions` | | 跳过全部权限提示（极度谨慎使用） |
| `--allow-dangerously-skip-permissions` | | 允许启用跳过权限（不主动开启） |
| `--permission-mode <MODE>` | | 以指定权限模式启动：`default`、`plan`、`acceptEdits`、`bypassPermissions` |
| `--allowedTools <TOOLS>` | | 无需提示即可执行的工具（权限规则语法） |
| `--disallowedTools <TOOLS>` | | 从模型上下文中移除的工具 |
| `--tools <TOOLS>` | | 限制可用内置工具（使用 `""` 禁用全部） |
| `--permission-prompt-tool <TOOL>` | | 非交互模式下使用指定 MCP 工具处理权限提示 |

---

## Output & Format

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--print` | `-p` | 不进入交互模式，直接打印响应（headless/SDK 模式） |
| `--output-format <FORMAT>` | | 输出格式：`text`、`json`、`stream-json` |
| `--input-format <FORMAT>` | | 输入格式：`text`、`stream-json` |
| `--json-schema <SCHEMA>` | | 输出校验 JSON（仅打印模式） |
| `--include-partial-messages` | | 包含流式部分消息（需 `--print` 且 `--output-format=stream-json`） |
| `--verbose` | | 启用详细日志（逐轮输出） |

---

## System Prompt

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--system-prompt <TEXT>` | | 用自定义文本替换完整系统提示词 |
| `--system-prompt-file <PATH>` | | 从文件加载系统提示词并替换默认值（仅打印模式） |
| `--append-system-prompt <TEXT>` | | 在默认提示词后追加自定义文本 |
| `--append-system-prompt-file <PATH>` | | 在默认提示词后追加文件内容（仅打印模式） |

---

## Agent & Subagent

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--agent <NAME>` | | 为当前会话指定 Agent |
| `--agents <JSON>` | | 通过 JSON 动态定义子代理 |
| `--teammate-mode <MODE>` | | 设定 agent team 展示：`auto`、`in-process`、`tmux` |

---

## MCP & Plugins

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--mcp-config <PATH\|JSON>` | | 从 JSON 文件或字符串加载 MCP 服务器 |
| `--strict-mcp-config` | | 只使用 `--mcp-config` 中的 MCP 服务器，忽略其他来源 |
| `--plugin-dir <PATH>` | | 仅在本会话中从目录加载插件（可重复指定） |

---

## Directory & Workspace

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--add-dir <PATH>` | | 追加 Claude 可访问的工作目录 |
| `--worktree` | `-w` | 在隔离的 git worktree 中启动 Claude（基于 HEAD 分支） |

---

## Budget & Limits

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--max-budget-usd <AMOUNT>` | | API 调用累计费用上限（仅打印模式） |
| `--max-turns <NUMBER>` | | 限制 agentic 回合数（仅打印模式） |

---

## Integration

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--chrome` | | 启用 Chrome 浏览器集成（自动化） |
| `--no-chrome` | | 禁用 Chrome 浏览器集成 |
| `--ide` | | 启动时自动连接 IDE（仅在检测到唯一可用 IDE 时） |

---

## Initialization & Maintenance

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--init` | | 运行初始化钩子并进入交互模式 |
| `--init-only` | | 仅运行初始化钩子后退出（不进入交互会话） |
| `--maintenance` | | 运行维护钩子后退出 |

---

## Debug & Diagnostics

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--debug <CATEGORIES>` | | 启用 Debug 模式并可指定过滤分类（如 `"api,hooks"`） |

---

## Settings Override

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--settings <PATH\|JSON>` | | 加载指定 settings JSON 文件或 JSON 字符串 |
| `--setting-sources <LIST>` | | 逗号分隔的来源列表：`user`、`project`、`local` |
| `--disable-slash-commands` | | 本会话禁用所有技能与斜杠命令 |

---

## Version & Help

| 参数 | 短参数 | 说明 |
|------|-------|------|
| `--version` | `-v` | 输出版本号 |
| `--help` | `-h` | 显示帮助信息 |

---

## Subcommands

这些是通过 `claude <subcommand>` 运行的顶级命令：

| 子命令 | 说明 |
|------------|-------------|
| `claude` | 启动交互式 REPL |
| `claude "query"` | 启动 REPL 并带入初始提示词 |
| `claude agents` | 列出已配置的 agents |
| `claude auth` | 管理 Claude Code 认证 |
| `claude doctor` | 在命令行运行诊断 |
| `claude install` | 安装或切换 Claude Code 原生构建版本 |
| `claude mcp` | 配置 MCP 服务器（`add`、`remove`、`list`、`get`、`enable`） |
| `claude plugin` | 管理 Claude Code 插件 |
| `claude remote-control` | 管理远程控制会话 |
| `claude setup-token` | 创建订阅使用的长期 token |
| `claude update` / `claude upgrade` | 更新到最新版本 |

---

## Environment Variables

这些仅在启动前于 shell 中设置的环境变量（不能在 `settings.json` 中配置）：

| 变量 | 说明 |
|----------|-------------|
| `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` | 启用实验性的 agent teams |
| `CLAUDE_CODE_TMPDIR` | 覆盖内部临时目录 |
| `CLAUDE_CODE_ADDITIONAL_DIRECTORIES_CLAUDE_MD=1` | 启用额外目录的 CLAUDE.md 加载 |
| `DISABLE_AUTOUPDATER=1` | 禁用自动更新 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 控制思考深度 — 参见 [Settings Reference](./claude-settings.md#environment-variables-via-env) |
| `USE_BUILTIN_RIPGREP=0` | 使用系统 ripgrep 代替内置版本（Alpine Linux） |
| `CLAUDE_CODE_SIMPLE` | 启用简化模式（仅 Bash + Edit 工具） |
| `CLAUDE_BASH_NO_LOGIN=1` | BashTool 跳过 login shell |

有关可通过 `settings.json` 中 `"env"` 键配置的环境变量（包括 `MAX_THINKING_TOKENS`、`CLAUDE_CODE_SHELL`、`CLAUDE_CODE_ENABLE_TASKS`、`CLAUDE_CODE_DISABLE_BACKGROUND_TASKS`、`CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` 等），请参见 [Claude Settings Reference](./claude-settings.md#environment-variables-via-env)。

---

## Sources

- [Claude Code CLI Reference](https://code.claude.com/docs/en/cli-reference)
- [Claude Code Headless Mode](https://code.claude.com/docs/en/headless)
- [Claude Code Setup](https://code.claude.com/docs/en/setup)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
- [Claude Code Common Workflows](https://code.claude.com/docs/en/common-workflows)
