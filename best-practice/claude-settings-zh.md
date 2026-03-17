# Claude Code 设置参考

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2015%2C%202026%201%3A10%20PM%20PKT-white?style=flat&labelColor=555) ![Version](https://img.shields.io/badge/Claude_Code-v2.1.76-blue?style=flat&labelColor=555)

Claude Code 的 `settings.json` 可用配置项完整指南。截至 v2.1.76，Claude Code 暴露 **60+ 设置项** 与 **160+ 环境变量**（建议通过 `settings.json` 的 `"env"` 字段配置，避免包装脚本）。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 目录

1. [Settings Hierarchy](#settings-hierarchy)
2. [Core Configuration](#core-configuration)
3. [Permissions](#permissions)
4. [Hooks](#hooks)
5. [MCP Servers](#mcp-servers)
6. [Sandbox](#sandbox)
7. [Plugins](#plugins)
8. [Model Configuration](#model-configuration)
9. [Display & UX](#display--ux)
10. [AWS & Cloud Credentials](#aws--cloud-credentials)
11. [Environment Variables](#environment-variables-via-env)
12. [Useful Commands](#useful-commands)

---

## Settings Hierarchy

设置按优先级从高到低生效：

| 优先级 | 位置 | 作用域 | 共享？ | 目的 |
|----------|----------|-------|---------|---------|
| 1 | 管理设置 | 组织 | 是（由 IT 部署） | 不可被覆盖的安全策略 |
| 2 | 命令行参数 | 会话 | N/A | 临时单会话覆盖 |
| 3 | `.claude/settings.local.json` | 项目 | 否（git 忽略） | 个人项目级设置 |
| 4 | `.claude/settings.json` | 项目 | 是（提交） | 团队共享设置 |
| 5 | `~/.claude/settings.json` | 用户 | N/A | 全局个人默认值 |

**管理设置** 为组织强制策略，不能被任何其他层覆盖（包括命令行参数）。交付方式：
- **服务端下发**（远程交付）
- **MDM profiles** —— macOS plist 位于 `com.anthropic.claudecode`
- **Registry policies** —— Windows HKLM/HKCU 位于 `Software\Anthropic\ClaudeCode`
- **文件** —— `managed-settings.json`（macOS：`/Library/Application Support/ClaudeCode/`，Linux/WSL：`/etc/claude-code/`，Windows：`C:\Program Files\ClaudeCode\`）

在管理层内，优先级为：服务端下发 > MDM/OS 级策略 > `managed-settings.json` > HKCU 注册表（仅 Windows）。只会使用一个管理来源，且不会合并。

> **注意：** 自 v2.1.75 起，已移除 Windows 的旧回退路径 `C:\ProgramData\ClaudeCode\managed-settings.json`，请改用 `C:\Program Files\ClaudeCode\managed-settings.json`。

**重要：**
- `deny` 规则拥有最高安全优先级，无法被低优先级的 allow/ask 规则覆盖。
- 管理设置即便与本地配置冲突，也可能锁定或覆盖本地行为。
- 数组型设置（如 `permissions.allow`）会在各层之间 **拼接并去重** —— 各层条目会合并，而不是替换。

---

## Core Configuration

### General Settings

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `$schema` | string | - | IDE 校验与自动补全用 JSON Schema URL（如 `"https://json.schemastore.org/claude-code-settings.json"`） |
| `model` | string | `"default"` | 覆盖默认模型。可用别名（`sonnet`、`opus`、`haiku`）或完整模型 ID |
| `agent` | string | - | 设置主对话默认 agent（来自 `.claude/agents/` 的 agent 名称）。CLI 也可用 `--agent` |
| `language` | string | `"english"` | Claude 的首选回复语言 |
| `cleanupPeriodDays` | number | `30` | 超过该天数未活跃的会话会在启动时删除。设为 `0` 会删除所有既有记录并禁用会话持久化（不写 `.jsonl`，`/resume` 不显示对话，hooks 接收空 `transcript_path`） |
| `autoUpdatesChannel` | string | `"latest"` | 更新通道：`"stable"` 或 `"latest"` |
| `alwaysThinkingEnabled` | boolean | `false` | 默认启用扩展思考 |
| `skipWebFetchPreflight` | boolean | `false` | WebFetch 前跳过阻止列表检查 |
| `availableModels` | array | - | 限制 `/model`、`--model` 或 `ANTHROPIC_MODEL` 可选模型。不影响 Default 选项。示例：`["sonnet", "haiku"]` |
| `fastModePerSessionOptIn` | boolean | `false` | 每个会话都需显式选择 fast mode |
| `teammateMode` | string | `"auto"` | agent team 展示模式：`"auto"`（tmux/iTerm2 分屏，其他环境 in-process）、`"in-process"` 或 `"tmux"` |
| `includeGitInstructions` | boolean | `true` | 在系统提示中包含 git 相关指令 |
| `feedbackSurveyRate` | number | - | 会话质量调研出现概率（0–1）。企业管理员可控制出现频率。示例：`0.05` = 5% |

**示例：**
```json
{
  "model": "opus",
  "agent": "code-reviewer",
  "language": "japanese",
  "cleanupPeriodDays": 60,
  "autoUpdatesChannel": "stable",
  "alwaysThinkingEnabled": true
}
```

### Plans & Memory Directories

自定义 plan 与 auto-memory 文件的存放位置。

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `plansDirectory` | string | `~/.claude/plans` | `/plan` 输出的存放目录 |
| `autoMemoryDirectory` | string | - | auto-memory 自定义目录。支持 `~/` 展开路径。不能在项目设置（`.claude/settings.json`）中使用，以避免重定向到敏感位置；可在 policy、local 与 user 设置中使用 |

**示例：**
```json
{
  "plansDirectory": "./my-plans"
}
```

**用途：** 便于将计划产物与 Claude 内部文件分离，或放到团队共享位置。

### Worktree Settings

配置 `--worktree` 的创建与管理方式，适用于大型 monorepo 以节省磁盘和启动时间。

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `worktree.symlinkDirectories` | array | `[]` | 在每个 worktree 中从主仓库软链的目录，避免复制大目录 |
| `worktree.sparsePaths` | array | `[]` | 使用 git sparse-checkout（cone 模式）只检出的目录 |

**示例：**
```json
{
  "worktree": {
    "symlinkDirectories": ["node_modules", ".cache"],
    "sparsePaths": ["packages/my-app", "shared/utils"]
  }
}
```

### Attribution Settings

定制 git 提交与 PR 的署名信息。

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `attribution.commit` | string | Co-authored-by | Git 提交署名（支持 trailer） |
| `attribution.pr` | string | 生成的消息 | PR 描述署名 |
| `includeCoAuthoredBy` | boolean | `true` | **已弃用** —— 请改用 `attribution` |

**示例：**
```json
{
  "attribution": {
    "commit": "Generated with AI\n\nCo-Authored-By: Claude <noreply@anthropic.com>",
    "pr": "Generated with Claude Code"
  }
}
```

**注意：** 设为空字符串（`""`）可隐藏署名。

### Authentication Helpers

动态生成认证 token 的脚本。

| 键 | 类型 | 说明 |
|-----|------|-------------|
| `apiKeyHelper` | string | 输出认证 token 的 shell 脚本路径（以 `X-Api-Key` header 发送） |
| `forceLoginMethod` | string | 限制登录方式为 `"claudeai"` 或 `"console"` |
| `forceLoginOrgUUID` | string | 登录时自动选择组织的 UUID |

**示例：**
```json
{
  "apiKeyHelper": "/bin/generate_temp_api_key.sh",
  "forceLoginMethod": "console",
  "forceLoginOrgUUID": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
}
```

### Company Announcements

启动时显示的自定义公告（随机轮播）。

| 键 | 类型 | 说明 |
|-----|------|-------------|
| `companyAnnouncements` | array | 启动时展示的字符串数组 |

**示例：**
```json
{
  "companyAnnouncements": [
    "Welcome to Acme Corp!",
    "Remember to run tests before committing!",
    "Check the wiki for coding standards"
  ]
}
```

---

## Permissions

控制 Claude 可执行的工具与操作。

### Permission Structure

```json
{
  "permissions": {
    "allow": [],
    "ask": [],
    "deny": [],
    "additionalDirectories": [],
    "defaultMode": "acceptEdits",
    "disableBypassPermissionsMode": "disable"
  }
}
```

### Permission Keys

| 键 | 类型 | 说明 |
|-----|------|-------------|
| `permissions.allow` | array | 允许免提示的规则 |
| `permissions.ask` | array | 需要用户确认的规则 |
| `permissions.deny` | array | 阻止的规则（最高优先级） |
| `permissions.additionalDirectories` | array | 允许 Claude 访问的额外目录 |
| `permissions.defaultMode` | string | 默认权限模式。在 Remote 环境仅 `acceptEdits` 与 `plan` 生效（v2.1.70+） |
| `permissions.disableBypassPermissionsMode` | string | 禁止启用 bypass 模式 |
| `allowManagedPermissionRulesOnly` | boolean | **（仅管理设置）** 只允许管理层规则，忽略用户/项目层 allow/ask/deny |
| `allow_remote_sessions` | boolean | **（仅管理设置）** 是否允许远程控制与 Web 会话。默认 `true`，设为 `false` 会阻止远程会话 |

### Permission Modes

| 模式 | 行为 |
|------|----------|
| `"default"` | 标准权限检查并提示 |
| `"acceptEdits"` | 自动接受文件编辑 |
| `"askEdits"` | 每次操作都询问 *(非官方文档，未验证)* |
| `"dontAsk"` | 未预先允许的工具会自动拒绝 |
| `"viewOnly"` | 只读模式，不允许修改 *(非官方文档，未验证)* |
| `"bypassPermissions"` | 跳过所有权限检查（危险） |
| `"plan"` | 只读探索模式 |

### Tool Permission Syntax

| 工具 | 语法 | 示例 |
|------|--------|----------|
| `Bash` | `Bash(command pattern)` | `Bash(npm run *)`, `Bash(* install)`, `Bash(git * main)` |
| `Read` | `Read(path pattern)` | `Read(.env)`, `Read(./secrets/**)` |
| `Edit` | `Edit(path pattern)` | `Edit(src/**)`, `Edit(*.ts)` |
| `Write` | `Write(path pattern)` | `Write(*.md)`, `Write(./docs/**)` |
| `NotebookEdit` | `NotebookEdit(pattern)` | `NotebookEdit(*)` |
| `WebFetch` | `WebFetch(domain:pattern)` | `WebFetch(domain:example.com)` |
| `WebSearch` | `WebSearch` | 全局 Web 搜索 |
| `Task` | `Task(agent-name)` | `Task(Explore)`, `Task(my-agent)` |
| `Agent` | `Agent(name)` | `Agent(researcher)`, `Agent(*)` —— 权限范围是子代理派生 |
| `Skill` | `Skill(skill-name)` | `Skill(weather-fetcher)` |
| `MCP` | `mcp__server__tool` 或 `MCP(server:tool)` | `mcp__memory__*`, `MCP(github:*)` |

**规则评估顺序：** 先 deny，再 ask，再 allow，首个匹配规则生效。

**Read/Edit 路径规则：** `Read`、`Edit`、`Write` 使用 gitignore 风格模式，支持四种前缀：

| 前缀 | 含义 | 示例 |
|--------|---------|---------|
| `//` | 从文件系统根目录开始的绝对路径 | `Read(//Users/alice/file)` |
| `~/` | 相对用户主目录 | `Read(~/.zshrc)` |
| `/` | 相对项目根目录 | `Edit(/src/**)` |
| `./` 或无 | 相对当前目录 | `Read(.env)`, `Read(*.ts)` |

**Bash 通配符说明：**
- `*` 可出现在任意位置：前缀（`Bash(* install)`）、后缀（`Bash(npm *)`）、中间（`Bash(git * main)`）
- **词边界：** `Bash(ls *)`（空格后 `*`）匹配 `ls -la` 但不匹配 `lsof`；`Bash(ls*)`（无空格）两者都匹配
- `Bash(*)` 等同于 `Bash`（匹配所有 bash 命令）
- 权限规则支持重定向：`Bash(python:*)` 可匹配 `python script.py > output.txt`
- 旧的 `:*` 后缀语法（如 `Bash(npm:*)`）等同于 ` *` 但已弃用

**示例：**
```json
{
  "permissions": {
    "allow": [
      "Edit(*)",
      "Write(*)",
      "Bash(npm run *)",
      "Bash(git *)",
      "WebFetch(domain:*)",
      "mcp__*"
    ],
    "ask": [
      "Bash(rm *)",
      "Bash(git push *)"
    ],
    "deny": [
      "Read(.env)",
      "Read(./secrets/**)",
      "Bash(curl *)"
    ],
    "additionalDirectories": ["../shared-libs/"]
  }
}
```

---

## Hooks

Hook 配置（事件、属性、匹配器、退出码、环境变量、HTTP hooks）维护在独立仓库：

> **[claude-code-voice-hooks](https://github.com/shanraisshan/claude-code-voice-hooks)** — 完整 Hook 参考：声音通知系统、19 个 hook 事件、HTTP hooks、匹配模式、退出码与环境变量。

Hook 相关设置键（`hooks`、`disableAllHooks`、`allowManagedHooksOnly`、`allowedHttpHookUrls`、`httpHookAllowedEnvVars`）也在该仓库中说明。

官方 hooks 参考见 [Claude Code Hooks Documentation](https://code.claude.com/docs/en/hooks)。

---

## MCP Servers

配置 MCP 服务器以扩展能力。

### MCP Settings

| 键 | 类型 | 作用域 | 说明 |
|-----|------|-------|-------------|
| `enableAllProjectMcpServers` | boolean | 任意 | 自动批准 `.mcp.json` 的全部服务器 |
| `enabledMcpjsonServers` | array | 任意 | 允许的服务器白名单 |
| `disabledMcpjsonServers` | array | 任意 | 禁止的服务器黑名单 |
| `allowedMcpServers` | array | 仅管理 | 允许列表（按名称/命令/URL 匹配） |
| `deniedMcpServers` | array | 仅管理 | 禁止列表（按匹配） |
| `allowManagedMcpServersOnly` | boolean | 仅管理 | 仅允许管理层允许的 MCP 服务器 |

### MCP Server Matching（管理设置）

```json
{
  "allowedMcpServers": [
    { "serverName": "github" },
    { "serverCommand": "npx @modelcontextprotocol/*" },
    { "serverUrl": "https://mcp.company.com/*" }
  ],
  "deniedMcpServers": [
    { "serverName": "dangerous-server" }
  ]
}
```

**示例：**
```json
{
  "enableAllProjectMcpServers": true,
  "enabledMcpjsonServers": ["memory", "github", "filesystem"],
  "disabledMcpjsonServers": ["experimental-server"]
}
```

---

## Sandbox

配置 bash 命令沙盒以提升安全。

### Sandbox Settings

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `sandbox.enabled` | boolean | `false` | 启用 bash 沙盒 |
| `sandbox.autoAllowBashIfSandboxed` | boolean | `true` | 在沙盒内自动批准 bash |
| `sandbox.excludedCommands` | array | `[]` | 在沙盒外执行的命令 |
| `sandbox.allowUnsandboxedCommands` | boolean | `true` | 允许 `dangerouslyDisableSandbox` |
| `sandbox.ignoreViolations` | object | `{}` | 命令模式到路径数组的映射，抑制违规警告 |
| `sandbox.enableWeakerNestedSandbox` | boolean | `false` | Docker 内使用较弱沙盒（降低安全性） |
| `sandbox.network.allowUnixSockets` | array | `[]` | 沙盒可访问的 Unix socket 路径 |
| `sandbox.network.allowAllUnixSockets` | boolean | `false` | 允许所有 Unix socket（覆盖 allowUnixSockets） |
| `sandbox.network.allowLocalBinding` | boolean | `false` | 允许绑定 localhost 端口（macOS） |
| `sandbox.network.allowedDomains` | array | `[]` | 沙盒网络域名白名单 |
| `sandbox.network.deniedDomains` | array | `[]` | 沙盒网络域名黑名单 |
| `sandbox.network.httpProxyPort` | number | - | HTTP 代理端口 1-65535（自定义代理） |
| `sandbox.network.socksProxyPort` | number | - | SOCKS5 代理端口 1-65535（自定义代理） |
| `sandbox.network.allowManagedDomainsOnly` | boolean | `false` | 仅允许管理层域名白名单（管理设置） |
| `sandbox.filesystem.allowWrite` | array | `[]` | 允许写入的路径前缀（`//` 绝对、`~/` 主目录、`/` 项目根、`./` 当前目录） |
| `sandbox.filesystem.denyWrite` | array | `[]` | 禁止写入的路径前缀 |
| `sandbox.filesystem.denyRead` | array | `[]` | 禁止读取的路径前缀 |
| `sandbox.enableWeakerNetworkIsolation` | boolean | `false` | （仅 macOS）允许访问系统 TLS 信任（`com.apple.trustd.agent`），降低安全性 |

**示例：**
```json
{
  "sandbox": {
    "enabled": true,
    "autoAllowBashIfSandboxed": true,
    "excludedCommands": ["git", "docker", "gh"],
    "allowUnsandboxedCommands": false,
    "network": {
      "allowUnixSockets": ["/var/run/docker.sock"],
      "allowLocalBinding": true
    }
  }
}
```

---

## Plugins

配置 Claude Code 插件与插件市场。

### Plugin Settings

| 键 | 类型 | 作用域 | 说明 |
|-----|------|-------|-------------|
| `enabledPlugins` | object | 任意 | 启用/禁用特定插件 |
| `extraKnownMarketplaces` | object | 项目 | 添加自定义插件市场（通过 `.claude/settings.json` 团队共享） |
| `strictKnownMarketplaces` | array | 仅管理 | 允许的市场白名单 |
| `skippedMarketplaces` | array | 任意 | 用户拒绝安装的市场 |
| `skippedPlugins` | array | 任意 | 用户拒绝安装的插件 |
| `pluginConfigs` | object | 任意 | 每个插件的 MCP 配置（键为 `plugin@marketplace`） |
| `blockedMarketplaces` | array | 仅管理 | 禁止的插件市场 |
| `pluginTrustMessage` | string | 仅管理 | 提示用户信任插件时的自定义文案 |

**示例：**
```json
{
  "enabledPlugins": {
    "formatter@acme-tools": true,
    "deployer@acme-tools": true,
    "experimental@acme-tools": false
  },
  "extraKnownMarketplaces": {
    "acme-tools": {
      "source": {
        "source": "github",
        "repo": "acme-corp/claude-plugins"
      }
    }
  }
}
```

---

## Model Configuration

### Model Aliases

| 别名 | 说明 |
|-------|-------------|
| `"default"` | 推荐用于你的账号类型 |
| `"sonnet"` | 最新 Sonnet 模型（Claude Sonnet 4.6） |
| `"opus"` | 最新 Opus 模型（Claude Opus 4.6） |
| `"haiku"` | 快速 Haiku 模型 |
| `"sonnet[1m]"` | 1M 上下文的 Sonnet |
| `"opusplan"` | 规划用 Opus、执行用 Sonnet |

**示例：**
```json
{
  "model": "opus"
}
```

### Model Overrides

将 Anthropic 模型 ID 映射到 Bedrock、Vertex、Foundry 等供应商模型 ID。

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `effortLevel` | string | - | 跨会话持久化努力程度。可为 `"low"`、`"medium"`、`"high"`。运行 `/effort low|medium|high` 时自动写入。仅 Opus 4.6 与 Sonnet 4.6 支持 |
| `modelOverrides` | object | - | 将模型选择项映射为供应商模型 ID（如 Bedrock inference profile ARN）。键为模型选择项名称，值为供应商模型 ID |

**示例：**
```json
{
  "modelOverrides": {
    "claude-opus-4-6": "arn:aws:bedrock:us-east-1:123456789:inference-profile/anthropic.claude-opus-4-6-v1:0",
    "claude-sonnet-4-6": "arn:aws:bedrock:us-east-1:123456789:inference-profile/anthropic.claude-sonnet-4-6-v1:0"
  }
}
```

### Effort Level

`/model` 命令提供 **effort level** 控制，决定每次响应的推理力度。可在 `/model` UI 中使用 **← →** 调整。

| Effort Level | 说明 |
|-------------|-------------|
| High | 全量推理，适合复杂任务 |
| Medium（默认） | 平衡推理，适合日常任务 |
| Low | 最小推理，速度最快 |

**使用方式：**
1. 运行 `/effort low`、`/effort medium`、`/effort high` 直接设置（v2.1.76+）
2. 或运行 `/model` → 选择模型 → 使用 **← →** 调整
3. 设置会写入 `settings.json` 中的 `effortLevel`

**说明：** Effort level 仅在 Max 和 Team 计划的 Opus 4.6 与 Sonnet 4.6 上可用。默认值在 v2.1.68 从 High 改为 Medium。自 v2.1.75 起，Opus 4.6 的 1M 上下文默认在 Max、Team、Enterprise 可用。

### Model Environment Variables

通过 `env` 键配置：

```json
{
  "env": {
    "ANTHROPIC_MODEL": "sonnet",
    "ANTHROPIC_DEFAULT_HAIKU_MODEL": "custom-haiku-model",
    "ANTHROPIC_DEFAULT_SONNET_MODEL": "custom-sonnet-model",
    "ANTHROPIC_DEFAULT_OPUS_MODEL": "custom-opus-model",
    "CLAUDE_CODE_SUBAGENT_MODEL": "haiku",
    "MAX_THINKING_TOKENS": "10000"
  }
}
```

---

## Display & UX

### Display Settings

| 键 | 类型 | 默认值 | 说明 |
|-----|------|---------|-------------|
| `statusLine` | object | - | 自定义状态栏配置 |
| `outputStyle` | string | `"default"` | 输出风格（如 `"Explanatory"`） |
| `spinnerTipsEnabled` | boolean | `true` | 等待时显示提示 |
| `spinnerVerbs` | object | - | 自定义 spinner 动词（`mode` 为 `"append"` 或 `"replace"`，`verbs` 数组） |
| `spinnerTipsOverride` | object | - | 自定义 spinner 提示（`tips` 字符串数组，`excludeDefault` 可选） |
| `terminalProgressBarEnabled` | boolean | `true` | 显示终端进度条 |
| `showTurnDuration` | boolean | `true` | 显示回合耗时信息 |
| `respectGitignore` | boolean | `true` | 文件选择器尊重 .gitignore |
| `prefersReducedMotion` | boolean | `false` | 减少 UI 动画与动效 |
| `fileSuggestion` | object | - | 自定义文件推荐命令（见下方配置） |

### Status Line Configuration

```json
{
  "statusLine": {
    "type": "command",
    "command": "~/.claude/statusline.sh",
    "padding": 0
  }
}
```

**状态栏输入字段：**

状态栏命令会从 stdin 接收 JSON 对象，常用字段：

| 字段 | 说明 |
|-------|-------------|
| `workspace.added_dirs` | 通过 `/add-dir` 添加的目录 |
| `context_window.used_percentage` | 已用上下文百分比 |
| `context_window.remaining_percentage` | 剩余上下文百分比 |
| `current_usage` | 当前上下文 token 数 |
| `exceeds_200k_tokens` | 上下文是否超过 200k tokens |

### File Suggestion Configuration

文件推荐脚本会从 stdin 接收 JSON（如 `{"query": "src/comp"}`），并输出最多 15 条文件路径（每行一个）。

```json
{
  "fileSuggestion": {
    "type": "command",
    "command": "~/.claude/file-suggestion.sh"
  },
  "respectGitignore": true
}
```

**示例：**
```json
{
  "statusLine": {
    "type": "command",
    "command": "git branch --show-current 2>/dev/null || echo 'no-branch'"
  },
  "spinnerTipsEnabled": true,
  "spinnerVerbs": {
    "mode": "replace",
    "verbs": ["Cooking", "Brewing", "Crafting", "Conjuring"]
  },
  "spinnerTipsOverride": {
    "tips": ["Use /compact at ~50% context", "Start with plan mode for complex tasks"],
    "excludeDefault": true
  },
  "terminalProgressBarEnabled": true,
  "showTurnDuration": false
}
```

---

## AWS & Cloud Credentials

### AWS Settings

| 键 | 类型 | 说明 |
|-----|------|-------------|
| `awsAuthRefresh` | string | 刷新 AWS 认证脚本（会修改 `.aws` 目录） |
| `awsCredentialExport` | string | 输出 AWS 凭证 JSON 的脚本 |

**示例：**
```json
{
  "awsAuthRefresh": "aws sso login --profile myprofile",
  "awsCredentialExport": "/bin/generate_aws_grant.sh"
}
```

### OpenTelemetry

| 键 | 类型 | 说明 |
|-----|------|-------------|
| `otelHeadersHelper` | string | 动态生成 OpenTelemetry headers 的脚本 |

**示例：**
```json
{
  "otelHeadersHelper": "/bin/generate_otel_headers.sh"
}
```

---

## Environment Variables（通过 `env`）

为所有 Claude Code 会话设置环境变量。

```json
{
  "env": {
    "ANTHROPIC_API_KEY": "...",
    "NODE_ENV": "development",
    "DEBUG": "true"
  }
}
```

### 常见环境变量

| 变量 | 说明 |
|----------|-------------|
| `ANTHROPIC_API_KEY` | API 认证 key |
| `ANTHROPIC_AUTH_TOKEN` | OAuth token |
| `ANTHROPIC_BASE_URL` | 自定义 API 端点 |
| `CLAUDE_CODE_USE_BEDROCK` | 使用 AWS Bedrock（`1` 启用） |
| `CLAUDE_CODE_USE_VERTEX` | 使用 Google Vertex AI（`1` 启用） |
| `CLAUDE_CODE_USE_FOUNDRY` | 使用 Microsoft Foundry（`1` 启用） |
| `CLAUDE_CODE_ENABLE_TELEMETRY` | 启用/禁用遥测（`0` 或 `1`） |
| `DISABLE_ERROR_REPORTING` | 禁用错误上报（`1` 禁用） |
| `DISABLE_TELEMETRY` | 禁用遥测（`1` 禁用） |
| `MCP_TIMEOUT` | MCP 启动超时（ms，默认 10000） |
| `MAX_MCP_OUTPUT_TOKENS` | MCP 输出 token 上限（默认 50000） |
| `BASH_MAX_TIMEOUT_MS` | Bash 命令超时 |
| `BASH_MAX_OUTPUT_LENGTH` | Bash 输出长度上限 |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | 自动 compact 阈值（1–100）。默认 ~95%。设为 50 等更低以提前压缩。超过 95% 无效。可用 `/context` 观察。示例：`CLAUDE_AUTOCOMPACT_PCT_OVERRIDE=50 claude` |
| `CLAUDE_BASH_MAINTAIN_PROJECT_WORKING_DIR` | Bash 调用间保持 cwd（`1` 启用） |
| `CLAUDE_CODE_DISABLE_BACKGROUND_TASKS` | 禁用后台任务（`1` 禁用） |
| `ENABLE_TOOL_SEARCH` | MCP 工具搜索阈值（如 `auto:5`） |
| `DISABLE_PROMPT_CACHING` | 禁用所有提示缓存（`1` 禁用） |
| `DISABLE_PROMPT_CACHING_HAIKU` | 禁用 Haiku 提示缓存 |
| `DISABLE_PROMPT_CACHING_SONNET` | 禁用 Sonnet 提示缓存 |
| `DISABLE_PROMPT_CACHING_OPUS` | 禁用 Opus 提示缓存 |
| `CLAUDE_CODE_DISABLE_EXPERIMENTAL_BETAS` | 禁用实验性 Beta 功能（`1` 禁用） |
| `CLAUDE_CODE_SHELL` | 覆盖自动 Shell 检测 |
| `CLAUDE_CODE_FILE_READ_MAX_OUTPUT_TOKENS` | 覆盖默认文件读取 token 上限 |
| `CLAUDE_CODE_ENABLE_TASKS` | 设为 `false` 禁用新任务系统 |
| `CLAUDE_CODE_EXIT_AFTER_STOP_DELAY` | SDK 模式空闲后自动退出（ms） |
| `CLAUDE_CODE_DISABLE_ADAPTIVE_THINKING` | 禁用自适应思考（`1` 禁用） |
| `CLAUDE_CODE_DISABLE_1M_CONTEXT` | 禁用 1M 上下文（`1` 禁用） |
| `CLAUDE_CODE_ACCOUNT_UUID` | 覆盖认证用账号 UUID |
| `CLAUDE_CODE_DISABLE_GIT_INSTRUCTIONS` | 禁用 git 相关系统提示指令 |
| `ENABLE_CLAUDEAI_MCP_SERVERS` | 启用 Claude.ai MCP 服务器 |
| `CLAUDE_CODE_EFFORT_LEVEL` | 设置 effort level：`high`、`medium`、`low` |
| `CLAUDE_CODE_MAX_TURNS` | 最大 agentic 回合数 *(非官方文档，未验证)* |
| `CLAUDE_CODE_DISABLE_NONESSENTIAL_TRAFFIC` | 禁用非必要网络流量 |
| `CLAUDE_CODE_SKIP_SETTINGS_SETUP` | 跳过首次运行设置向导 *(非官方文档，未验证)* |
| `CLAUDE_CODE_PROMPT_CACHING_ENABLED` | 覆盖提示缓存行为 *(非官方文档，未验证)* |
| `CLAUDE_CODE_DISABLE_TOOLS` | 禁用指定工具（逗号分隔） *(非官方文档，未验证)* |
| `CLAUDE_CODE_DISABLE_MCP` | 禁用所有 MCP 服务器（`1` 禁用） *(非官方文档，未验证)* |
| `CLAUDE_CODE_MAX_OUTPUT_TOKENS` | 每次响应输出 token 上限（默认 32000，最大 64000） |
| `CLAUDE_CODE_DISABLE_FAST_MODE` | 完全禁用 fast mode（`1` 禁用） |
| `CLAUDE_CODE_DISABLE_AUTO_MEMORY` | 禁用 auto memory（`1` 禁用） |
| `CLAUDE_CODE_USER_EMAIL` | 同步提供用户邮箱用于认证 |
| `CLAUDE_CODE_ORGANIZATION_UUID` | 同步提供组织 UUID 用于认证 |
| `CLAUDE_CONFIG_DIR` | 自定义配置目录（覆盖默认 `~/.claude`） |
| `ANTHROPIC_CUSTOM_HEADERS` | 自定义 API headers（`Name: Value` 格式，多条用换行分隔） |
| `ANTHROPIC_FOUNDRY_API_KEY` | Microsoft Foundry 认证 API key |
| `ANTHROPIC_FOUNDRY_BASE_URL` | Foundry 资源 base URL |
| `ANTHROPIC_FOUNDRY_RESOURCE` | Foundry 资源名 |
| `AWS_BEARER_TOKEN_BEDROCK` | Bedrock 认证 API key |
| `ANTHROPIC_SMALL_FAST_MODEL` | **已弃用** —— 改用 `ANTHROPIC_DEFAULT_HAIKU_MODEL` |
| `ANTHROPIC_SMALL_FAST_MODEL_AWS_REGION` | 弃用 Haiku 类模型 override 的 AWS 区域 |
| `CLAUDE_CODE_SHELL_PREFIX` | 在 bash 命令前追加前缀 |
| `BASH_DEFAULT_TIMEOUT_MS` | Bash 默认超时（ms） |
| `CLAUDE_CODE_SKIP_BEDROCK_AUTH` | 跳过 Bedrock 认证（`1` 跳过） |
| `CLAUDE_CODE_SKIP_FOUNDRY_AUTH` | 跳过 Foundry 认证（`1` 跳过） |
| `CLAUDE_CODE_SKIP_VERTEX_AUTH` | 跳过 Vertex 认证（`1` 跳过） |
| `CLAUDE_CODE_PROXY_RESOLVES_HOSTS` | 允许代理做 DNS 解析 |
| `CLAUDE_CODE_API_KEY_HELPER_TTL_MS` | `apiKeyHelper` 凭证刷新间隔（ms） |
| `CLAUDE_CODE_CLIENT_CERT` | mTLS 客户端证书路径 |
| `CLAUDE_CODE_CLIENT_KEY` | mTLS 客户端私钥路径 |
| `CLAUDE_CODE_CLIENT_KEY_PASSPHRASE` | mTLS 加密私钥口令 |
| `CLAUDE_CODE_PLUGIN_GIT_TIMEOUT_MS` | 插件市场 git clone 超时（ms，默认 120000） |
| `CLAUDE_CODE_HIDE_ACCOUNT_INFO` | 隐藏 UI 中的邮箱/组织信息 *(非官方文档，未验证)* |
| `CLAUDE_CODE_DISABLE_CRON` | 禁用计划/cron 任务（`1` 禁用） |
| `DISABLE_INSTALLATION_CHECKS` | 禁用安装检查警告 |
| `DISABLE_BUG_COMMAND` | 禁用 `/bug` 命令 |
| `DISABLE_NON_ESSENTIAL_MODEL_CALLS` | 禁用风味文本与非必要模型调用 *(非官方文档，未验证)* |
| `DISABLE_COST_WARNINGS` | 禁用费用警告 |
| `CLAUDE_CODE_SUBAGENT_MODEL` | 覆盖子代理模型（如 `haiku`、`sonnet`） |
| `CLAUDE_CODE_SESSIONEND_HOOKS_TIMEOUT_MS` | SessionEnd hook 超时（ms），替代硬性 1.5s 限制 |
| `CLAUDE_CODE_DISABLE_FEEDBACK_SURVEY` | 禁用反馈调研提示（`1` 禁用） |
| `CLAUDE_CODE_DISABLE_TERMINAL_TITLE` | 禁用终端标题更新（`1` 禁用） |
| `CLAUDE_CODE_IDE_SKIP_AUTO_INSTALL` | 跳过 IDE 扩展自动安装（`1` 跳过） |
| `CLAUDE_CODE_OTEL_HEADERS_HELPER_DEBOUNCE_MS` | OTel headers helper 脚本 debounce（ms） |
| `ANTHROPIC_DEFAULT_OPUS_MODEL` | 覆盖 Opus 模型别名（如 `claude-opus-4-6[1m]`） |
| `ANTHROPIC_DEFAULT_SONNET_MODEL` | 覆盖 Sonnet 模型别名（如 `claude-sonnet-4-6`） |
| `MAX_THINKING_TOKENS` | 每次响应的扩展思考 token 上限 |
| `CLAUDE_CODE_AUTO_COMPACT_WINDOW` | 自动 compact 窗口行为配置 |
| `CLAUDE_CODE_ENABLE_PROMPT_SUGGESTION` | 启用提示词建议 |
| `CLAUDE_CODE_PLAN_MODE_REQUIRED` | 强制 plan mode |
| `CLAUDE_CODE_TEAM_NAME` | agent teams 团队名 |
| `CLAUDE_CODE_TASK_LIST_ID` | 任务集成的 task list ID |
| `CLAUDE_ENV_FILE` | 自定义环境文件路径 |
| `FORCE_AUTOUPDATE_PLUGINS` | 强制插件自动更新（`1` 启用） |
| `HTTP_PROXY` | HTTP 代理 URL |
| `HTTPS_PROXY` | HTTPS 代理 URL |
| `NO_PROXY` | 直连白名单（逗号分隔） |
| `MCP_TOOL_TIMEOUT` | MCP 工具执行超时（ms） |
| `MCP_CLIENT_SECRET` | MCP OAuth 客户端密钥 |
| `MCP_OAUTH_CALLBACK_PORT` | MCP OAuth 回调端口 |
| `IS_DEMO` | 启用演示模式 |
| `SLASH_COMMAND_TOOL_CHAR_BUDGET` | 斜杠命令工具输出字符预算 |
| `VERTEX_REGION_CLAUDE_3_5_HAIKU` | Vertex AI 中 Claude 3.5 Haiku 区域覆盖 |
| `VERTEX_REGION_CLAUDE_3_7_SONNET` | Vertex AI 中 Claude 3.7 Sonnet 区域覆盖 |
| `VERTEX_REGION_CLAUDE_4_0_OPUS` | Vertex AI 中 Claude 4.0 Opus 区域覆盖 |
| `VERTEX_REGION_CLAUDE_4_0_SONNET` | Vertex AI 中 Claude 4.0 Sonnet 区域覆盖 |
| `VERTEX_REGION_CLAUDE_4_1_OPUS` | Vertex AI 中 Claude 4.1 Opus 区域覆盖 |

---

## Useful Commands

| 命令 | 说明 |
|---------|-------------|
| `/model` | 切换模型并调整 Opus 4.6 effort level |
| `/effort` | 直接设置 effort level：`low`、`medium`、`high`（v2.1.76+） |
| `/config` | 交互式配置 UI |
| `/memory` | 查看/编辑所有 memory 文件 |
| `/agents` | 管理子代理 |
| `/mcp` | 管理 MCP 服务器 |
| `/hooks` | 查看 hooks 配置 |
| `/plugin` | 管理插件 |
| `/keybindings` | 配置自定义快捷键 |
| `/skills` | 查看与管理技能 |
| `/permissions` | 查看与管理权限规则 |
| `--doctor` | 诊断配置问题 |
| `--debug` | Debug 模式（含 hook 执行细节） |

---

## Quick Reference: Complete Example

```json
{
  "$schema": "https://json.schemastore.org/claude-code-settings.json",
  "model": "sonnet",
  "agent": "code-reviewer",
  "language": "english",
  "cleanupPeriodDays": 30,
  "autoUpdatesChannel": "stable",
  "alwaysThinkingEnabled": true,
  "includeGitInstructions": true,
  "plansDirectory": "./plans",
  "effortLevel": "medium",

  "worktree": {
    "symlinkDirectories": ["node_modules"],
    "sparsePaths": ["packages/my-app", "shared/utils"]
  },

  "modelOverrides": {
    "claude-opus-4-6": "arn:aws:bedrock:us-east-1:123456789:inference-profile/anthropic.claude-opus-4-6-v1:0"
  },

  "permissions": {
    "allow": [
      "Edit(*)",
      "Write(*)",
      "Bash(npm run *)",
      "Bash(git *)",
      "WebFetch(domain:*)",
      "mcp__*",
      "Agent(*)"
    ],
    "deny": [
      "Read(.env)",
      "Read(./secrets/**)"
    ],
    "additionalDirectories": ["../shared/"],
    "defaultMode": "acceptEdits"
  },

  "enableAllProjectMcpServers": true,

  "sandbox": {
    "enabled": true,
    "excludedCommands": ["git", "docker"],
    "filesystem": {
      "denyRead": ["./secrets/"],
      "denyWrite": ["./.env"]
    }
  },

  "attribution": {
    "commit": "Generated with Claude Code",
    "pr": ""
  },

  "statusLine": {
    "type": "command",
    "command": "git branch --show-current"
  },

  "spinnerTipsEnabled": true,
  "spinnerTipsOverride": {
    "tips": ["Custom tip 1", "Custom tip 2"],
    "excludeDefault": false
  },
  "showTurnDuration": false,
  "prefersReducedMotion": false,

  "env": {
    "NODE_ENV": "development",
    "CLAUDE_CODE_EFFORT_LEVEL": "medium"
  }
}
```

---

## Sources

- [Claude Code Settings Documentation](https://code.claude.com/docs/en/settings)
- [Claude Code Settings JSON Schema](https://json.schemastore.org/claude-code-settings.json)
- [Claude Code Configuration Guide](https://claudelog.com/configuration/)
- [Claude Code GitHub Settings Examples](https://github.com/feiskyer/claude-code-settings)
- [Eesel AI - Developer's Guide to settings.json](https://www.eesel.ai/blog/settings-json-claude-code)
- [Shipyard - Claude Code CLI Cheatsheet](https://shipyard.build/blog/claude-code-cheat-sheet/)
- [Claude Code Environment Variables Reference](https://code.claude.com/docs/en/env-vars)
- [Claude Code Permissions Reference](https://code.claude.com/docs/en/permissions)
