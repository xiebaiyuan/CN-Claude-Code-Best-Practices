# CLAUDE.md

本文件为 Claude Code（claude.ai/code）在本仓库中工作时提供指导。

## 仓库概览

这是一个用于 Claude Code 配置最佳实践的仓库，演示了 skills、subagents、hooks 和 commands 的模式。它是一个参考实现仓库，而不是业务应用代码仓库。

## 核心组件

### Weather 系统（示例工作流）
通过 **Command → Agent → Skill** 架构演示两种不同的 skill 模式：
- `/weather-orchestrator` command（`.claude/commands/weather-orchestrator.md`）：入口点，询问用户使用 C/F，然后调用 agent，再调用 SVG skill
- `weather-agent` agent（`.claude/agents/weather-agent.md`）：使用其预加载的 `weather-fetcher` skill 获取温度（agent skill 模式）
- `weather-fetcher` skill（`.claude/skills/weather-fetcher/SKILL.md`）：预加载到 agent 中，用于从 Open-Meteo 获取温度
- `weather-svg-creator` skill（`.claude/skills/weather-svg-creator/SKILL.md`）：生成天气 SVG 卡片，并写入 `orchestration-workflow/weather.svg` 和 `orchestration-workflow/output.md`

两种 skill 模式：agent skills（通过 `skills:` 字段预加载）与 skills（通过 `Skill` 工具调用）。完整流程图见 `orchestration-workflow/orchestration-workflow.md`。

### Skill 定义结构
`.claude/skills/<name>/SKILL.md` 中的 skill 使用 YAML frontmatter：
- `name`：显示名称与 `/slash-command`（默认使用目录名）
- `description`：触发时机说明（推荐用于自动发现）
- `argument-hint`：自动补全提示（例如 `[issue-number]`）
- `disable-model-invocation`：设为 `true` 可阻止自动调用
- `user-invocable`：设为 `false` 可从 `/` 菜单隐藏（仅后台知识）
- `allowed-tools`：skill 激活时可免权限提示使用的工具
- `model`：skill 激活时使用的模型
- `context`：设为 `fork` 可在隔离子代理上下文中运行
- `agent`：`context: fork` 时使用的子代理类型（默认：`general-purpose`）
- `hooks`：该 skill 作用域内的生命周期 hooks

### Presentation 系统
见 `.claude/rules/presentation.md`：所有 presentation 相关工作都要委托给 `presentation-curator` agent。

### Hooks 系统
位于 `.claude/hooks/` 的跨平台声音通知系统：
- `scripts/hooks.py`：Claude Code hook 事件主处理器
- `config/hooks-config.json`：团队共享配置
- `config/hooks-config.local.json`：个人覆盖配置（被 git 忽略）
- `sounds/`：按 hook 事件组织的音频文件（由 ElevenLabs TTS 生成）

`.claude/settings.json` 中配置的 hook 事件包括：PreToolUse、PostToolUse、UserPromptSubmit、Notification、Stop、SubagentStart、SubagentStop、PreCompact、SessionStart、SessionEnd、Setup、PermissionRequest、TeammateIdle、TaskCompleted、ConfigChange。

特殊处理：git commit 会触发 `pretooluse-git-committing` 音效。

## 关键模式

### Subagent 编排
Subagent **不能** 通过 bash 命令调用其他 subagent。请使用 Agent 工具（在 v2.1.63 中由 Task 重命名而来；`Task(...)` 仍可作为别名）：
```
Agent(subagent_type="agent-name", description="...", prompt="...", model="haiku")
```

在 subagent 定义中请明确工具使用方式。避免使用可能被误解为 bash 命令的模糊词（如“launch”）。

### Subagent 定义结构
`.claude/agents/*.md` 中的 subagent 使用 YAML frontmatter：
- `name`：subagent 标识
- `description`：触发时机说明（使用“PROACTIVELY”可启用自动调用）
- `tools`：逗号分隔的工具白名单（省略时继承全部）。支持 `Agent(agent_type)` 语法
- `disallowedTools`：禁止工具，会从继承或显式列表中移除
- `model`：模型别名：`haiku`、`sonnet`、`opus` 或 `inherit`（默认：`inherit`）
- `permissionMode`：权限模式（如：`"acceptEdits"`、`"plan"`、`"bypassPermissions"`）
- `maxTurns`：subagent 停止前的最大 agentic 回合数
- `skills`：预加载到 agent 上下文中的 skill 名称列表
- `mcpServers`：该 subagent 使用的 MCP 服务器（服务器名或内联配置）
- `hooks`：该 subagent 作用域内的生命周期 hooks（支持全部 hook 事件；常用为 `PreToolUse`、`PostToolUse`、`Stop`）
- `memory`：持久记忆作用域：`user`、`project` 或 `local`（见 `reports/claude-agent-memory.md`）
- `background`：设为 `true` 表示始终后台运行
- `isolation`：设为 `"worktree"` 表示在临时 git worktree 中运行
- `color`：CLI 输出颜色，用于视觉区分

### 配置层级
1. **Managed**（`managed-settings.json` / MDM plist / Registry）：组织强制配置，不可覆盖
2. 命令行参数：单次会话覆盖
3. `.claude/settings.local.json`：个人项目配置（被 git 忽略）
4. `.claude/settings.json`：团队共享配置
5. `~/.claude/settings.json`：全局个人默认配置
6. `hooks-config.local.json` 会覆盖 `hooks-config.json`

### 禁用 Hooks
在 `.claude/settings.local.json` 中设置 `"disableAllHooks": true`，或在 `hooks-config.json` 中禁用单个 hook。

## 工作流最佳实践

来自本仓库实践经验：

- 每个文件将 CLAUDE.md 控制在 200 行以内，以提高遵循稳定性
- 工作流优先用 commands，而非独立 agents
- 优先创建“功能专用 subagent + skills（渐进披露）”，而非通用 agent
- 在上下文使用约 50% 时手动执行 `/compact`
- 复杂任务先进入 plan mode
- 多步骤任务采用“人工门控”的任务清单工作流
- 将子任务拆到足够小，确保在 50% 上下文内完成

### 调试建议

- 使用 `/doctor` 做诊断
- 长时间运行的终端命令使用后台任务，便于查看日志
- 使用浏览器自动化 MCP（Claude in Chrome、Playwright、Chrome DevTools）让 Claude 自行查看控制台日志
- 报告可视化问题时提供截图

## 文档

文档规范见 `.claude/rules/markdown-docs.md`。关键文档：
- `best-practice/claude-subagents.md`：subagent frontmatter、hooks 和仓库 agents
- `best-practice/claude-commands.md`：slash command 模式与内置命令参考
- `orchestration-workflow/orchestration-workflow.md`：Weather 系统流程图
