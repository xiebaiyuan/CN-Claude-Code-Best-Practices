# claude-code-best-practice

> 语言版本：[中文（Main README）](README.md) | [English Original](README-en.md)
>
> 说明：此文件为中文镜像版本；仓库默认展示的主 README 为 [README.md](README.md)。

practice makes claude perfect

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2016%2C%202026%2007%3A36%20PM%20PKT-white?style=flat&labelColor=555) <a href="https://github.com/shanraisshan/claude-code-best-practice/stargazers"><img src="https://img.shields.io/github/stars/shanraisshan/claude-code-best-practice?style=flat&label=%E2%98%85&labelColor=555&color=white" alt="GitHub Stars"></a>

[![Best Practice](!/tags/best-practice.svg)](best-practice/) *点击徽章查看最新最佳实践*<br>
[![Implemented](!/tags/implemented.svg)](implementation/) *点击徽章查看本仓库中的实现*<br>
[![Orchestration Workflow](!/tags/orchestration-workflow.svg)](orchestration-workflow/orchestration-workflow.md) *点击徽章查看 Command → Agent → Skill 编排工作流*

<p align="center">
  <img src="!/claude-jumping.svg" alt="Claude Code mascot jumping" width="120" height="100">
</p>

<p align="center">
  <img src="!/root/boris-slider.gif" alt="Boris Cherny on Claude Code" width="600"><br>
  Boris Cherny on X（<a href="https://x.com/bcherny/status/2007179832300581177">tweet 1</a> · <a href="https://x.com/bcherny/status/2017742741636321619">tweet 2</a> · <a href="https://x.com/bcherny/status/2021699851499798911">tweet 3</a>）
</p>

## 🧠 CONCEPTS

| Feature | Location | Description |
|---------|----------|-------------|
| [**Commands**](https://code.claude.com/docs/en/slash-commands) | `.claude/commands/<name>.md` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-commands.md) [![Implemented](!/tags/implemented.svg)](implementation/claude-commands-implementation.md) 向现有上下文注入知识；由用户触发的简单提示模板，用于工作流编排 |
| [**Subagents**](https://code.claude.com/docs/en/sub-agents) | `.claude/agents/<name>.md` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-subagents.md) [![Implemented](!/tags/implemented.svg)](implementation/claude-subagents-implementation.md) 在全新隔离上下文中运行的自治执行体；可自定义工具、权限、模型、记忆与持久身份 |
| [**Skills**](https://code.claude.com/docs/en/skills) | `.claude/skills/<name>/SKILL.md` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-skills.md) [![Implemented](!/tags/implemented.svg)](implementation/claude-skills-implementation.md) 向现有上下文注入知识；可配置、可预加载、可自动发现，支持上下文分叉与渐进披露 · [Official Skills](https://github.com/anthropics/skills/tree/main/skills) |
| [**Workflows**](https://code.claude.com/docs/en/common-workflows) | [`.claude/commands/weather-orchestrator.md`](.claude/commands/weather-orchestrator.md) | [![Orchestration Workflow](!/tags/orchestration-workflow.svg)](orchestration-workflow/orchestration-workflow.md) |
| [**Hooks**](https://code.claude.com/docs/en/hooks) | `.claude/hooks/` | [![Best Practice](!/tags/best-practice.svg)](https://github.com/shanraisshan/claude-code-voice-hooks) [![Implemented](!/tags/implemented.svg)](https://github.com/shanraisshan/claude-code-voice-hooks) 在特定事件上于 agentic 循环之外执行的确定性脚本 |
| [**MCP Servers**](https://code.claude.com/docs/en/mcp) | `.claude/settings.json`, `.mcp.json` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-mcp.md) [![Implemented](!/tags/implemented.svg)](.mcp.json) 通过 Model Context Protocol 连接外部工具、数据库与 API |
| [**Plugins**](https://code.claude.com/docs/en/plugins) | distributable packages | skills、subagents、hooks、MCP servers 的打包分发形式 · [Marketplaces](https://code.claude.com/docs/en/discover-plugins) |
| [**Settings**](https://code.claude.com/docs/en/settings) | `.claude/settings.json` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-settings.md) [![Implemented](!/tags/implemented.svg)](.claude/settings.json) 分层配置系统 · [Permissions](https://code.claude.com/docs/en/permissions) · [Model Config](https://code.claude.com/docs/en/model-config) · [Output Styles](https://code.claude.com/docs/en/output-styles) · [Sandboxing](https://code.claude.com/docs/en/sandboxing) · [Keybindings](https://code.claude.com/docs/en/keybindings) · [Fast Mode](https://code.claude.com/docs/en/fast-mode) |
| [**Status Line**](https://code.claude.com/docs/en/statusline) | `.claude/settings.json` | [![Best Practice](!/tags/best-practice.svg)](https://github.com/shanraisshan/claude-code-status-line) [![Implemented](!/tags/implemented.svg)](.claude/settings.json) 可定制状态栏，显示上下文占用、模型、成本与会话信息 |
| [**Memory**](https://code.claude.com/docs/en/memory) | `CLAUDE.md`, `.claude/rules/`, `~/.claude/rules/`, `~/.claude/projects/<project>/memory/` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-memory.md) [![Implemented](!/tags/implemented.svg)](CLAUDE.md) 通过 CLAUDE.md 文件与 `@path` 导入实现持久上下文 · [Auto Memory](https://code.claude.com/docs/en/memory) · [Rules](https://code.claude.com/docs/en/memory#organize-rules-with-clauderules) |
| [**Checkpointing**](https://code.claude.com/docs/en/checkpointing) | automatic (git-based) | 自动跟踪文件修改，支持回退（`Esc Esc` 或 `/rewind`）与定向摘要 |
| [**CLI Startup Flags**](https://code.claude.com/docs/en/cli-reference) | `claude [flags]` | [![Best Practice](!/tags/best-practice.svg)](best-practice/claude-cli-startup-flags.md) 启动 Claude Code 的命令行参数、子命令和环境变量 · [Interactive Mode](https://code.claude.com/docs/en/interactive-mode) |
| **AI Terms** | | [![Best Practice](!/tags/best-practice.svg)](https://github.com/shanraisshan/claude-code-codex-cursor-gemini/blob/main/reports/ai-terms.md) Agentic Engineering · Context Engineering · Vibe Coding |
| [**Best Practices**](https://code.claude.com/docs/en/best-practices) | | 官方最佳实践 · [Prompt Engineering](https://github.com/anthropics/prompt-eng-interactive-tutorial) · [Extend Claude Code](https://code.claude.com/docs/en/features-overview) |

### 🔥 Hot

| Feature | Location | Description |
|---------|----------|-------------|
| [**/btw**](https://x.com/trq212/status/2031506296697131352) | `/btw` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/trq212/status/2031506296697131352) Claude 工作时可并行进行侧链对话 |
| [**Code Review**](https://code.claude.com/docs/en/code-review) ![beta](!/tags/beta.svg) | GitHub App (managed) | [![Best Practice](!/tags/best-practice.svg)](https://x.com/claudeai/status/2031088171262554195) 多代理 PR 分析，可捕获 bug、安全漏洞与回归 · [Blog](https://claude.com/blog/code-review) |
| [**Scheduled Tasks**](https://code.claude.com/docs/en/scheduled-tasks) | `/loop`, cron tools | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2030193932404150413) [![Implemented](!/tags/implemented.svg)](implementation/claude-scheduled-tasks-implementation.md) 按周期运行提示（最长 3 天）、设置一次性提醒、轮询部署和构建状态 |
| [**Voice Mode**](https://x.com/trq212/status/2028628570692890800) ![beta](!/tags/beta.svg) | `/voice` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/trq212/status/2028628570692890800) 语音交互模式，使用 `/voice` 启用 |
| [**Simplify & Batch**](https://x.com/bcherny/status/2027534984534544489) | `/simplify`, `/batch` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2027534984534544489) 内置技能用于代码质量和批量操作：将重构简化为可复用高效步骤，并跨文件批量执行命令 |
| [**Agent Teams**](https://code.claude.com/docs/en/agent-teams) ![beta](!/tags/beta.svg) | built-in (env var) | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2019472394696683904) [![Implemented](!/tags/implemented.svg)](implementation/claude-agent-teams-implementation.md) 多个 agent 在同一代码库并行协作并共享任务协调 |
| [**Remote Control**](https://code.claude.com/docs/en/remote-control) | `/remote-control`, `/rc` | [![Best Practice](!/tags/best-practice.svg)](https://x.com/noahzweben/status/2032533699116355819) 可从任意设备（手机、平板、浏览器）继续本地会话 · [Headless Mode](https://code.claude.com/docs/en/headless) |
| [**Git Worktrees**](https://code.claude.com/docs/en/common-workflows) | built-in | [![Best Practice](!/tags/best-practice.svg)](https://x.com/bcherny/status/2025007393290272904) 并行开发的隔离 git 分支机制，每个 agent 拥有独立工作副本 |
| [**Ralph Wiggum Loop**](https://github.com/anthropics/claude-code/tree/main/plugins/ralph-wiggum) | plugin | [![Best Practice](!/tags/best-practice.svg)](https://github.com/ghuntley/how-to-ralph-wiggum) [![Implemented](!/tags/implemented.svg)](https://github.com/shanraisshan/novel-llm-26) 面向长时任务的自治开发循环，持续迭代直到完成 |

<a id="orchestration-workflow"></a>

## <a href="orchestration-workflow/orchestration-workflow.md"><img src="!/tags/orchestration-workflow-hd.svg" alt="Orchestration Workflow"></a>

查看 [orchestration-workflow](orchestration-workflow/orchestration-workflow.md) 了解 **Command → Agent → Skill** 模式的实现细节。

<p align="center">
  <img src="orchestration-workflow/orchestration-workflow.svg" alt="Command Skill Agent Architecture Flow" width="100%">
</p>

<p align="center">
  <img src="orchestration-workflow/orchestration-workflow.gif" alt="Orchestration Workflow Demo" width="600">
</p>

![How to Use](!/tags/how-to-use.svg)

```bash
claude
/weather-orchestrator
```

## ⚙️ DEVELOPMENT WORKFLOWS

### 🔥 Hot
- [Cross-Model (Claude Code + Codex) Workflow](development-workflows/cross-model-workflow/cross-model-workflow.md) [![Implemented](!/tags/implemented.svg)](development-workflows/cross-model-workflow/cross-model-workflow.md)
- [RPI](development-workflows/rpi/rpi-workflow.md) [![Implemented](!/tags/implemented.svg)](development-workflows/rpi/rpi-workflow.md)
- [Ralph Wiggum Loop](https://www.youtube.com/watch?v=eAtvoGlpeRU) [![Implemented](!/tags/implemented.svg)](https://github.com/shanraisshan/novel-llm-26)
- [Garry Tan (CEO of Y Combinator) - gstack](https://github.com/garrytan/gstack) · ★ 15k

### Others
- [obra/superpowers](https://github.com/obra/superpowers) · ★ 87k
- [Github Speckit](https://github.com/github/spec-kit) · ★ 77k
- [get-shit-done (GSD)](https://github.com/gsd-build/get-shit-done) · ★ 31k
- [OpenSpec OPSX](https://github.com/Fission-AI/OpenSpec/blob/main/docs/opsx.md) · ★ 31k
- [Brian Casel (Creator of Agent OS) - 2026 Workflow](https://github.com/buildermethods/agent-os) · ★ 4k - [it's overkill in 2026](https://www.youtube.com/watch?v=0hdFJA-ho3c)
- [Human Layer RPI - Research Plan Implement](https://github.com/humanlayer/advanced-context-engineering-for-coding-agents/blob/main/ace-fca.md) · ★ 1.5k
- [Andrej Karpathy (Founding Member, OpenAI) Workflow](https://x.com/karpathy/status/2015883857489522876)
- [Boris Cherny (Creator of Claude Code) - Feb 2026 Workflow](https://x.com/bcherny/status/2017742741636321619)
- [Peter Steinberger (Creator of OpenClaw) Workflow](https://youtu.be/8lF7HmQ_RgY?t=2582)

## 💡 TIPS AND TRICKS

![Community](!/tags/community.svg)

■ **Prompting (4)**
- challenge Claude — “grill me on these changes and don't make a PR until I pass your test.” 或 “prove to me this works”，并让 Claude 对比 main 与当前分支 diff [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742752566632544)
- 当修复效果一般时，使用 “knowing everything you know now, scrap this and implement the elegant solution” [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742752566632544)
- 大多数 bug Claude 可以自修：粘贴 bug 后直接说 “fix”，不要微观指挥 👶 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742750473720121)
- 说 “use subagents” 以投入更多计算资源，将任务卸载出去，保持主上下文干净聚焦 👶 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742755737555434)

■ **Planning/Specs (5)**
- 始终从 [plan mode](https://code.claude.com/docs/en/common-workflows) 开始 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179845336527000)
- 从最小化 spec/prompt 起步，让 Claude 通过 [AskUserQuestion](https://code.claude.com/docs/en/cli-reference) 工具采访你，再开新会话执行 spec [![Thariq](!/tags/thariq.svg)](https://x.com/trq212/status/2005315275026260309)
- 制定分阶段且可门控的计划，每阶段配置多类测试（单测、自动化、集成）
- 启动第二个 Claude 以资深工程师视角审阅你的计划，或用 [cross-model](development-workflows/cross-model-workflow/cross-model-workflow.md) 做审阅 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742745365057733)
- 先写细规格、先降歧义再交付，越具体输出越好 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742752566632544)

■ **Workflows (12)**
- [CLAUDE.md](https://code.claude.com/docs/en/memory) 每个文件建议控制在 [200 行以内](https://code.claude.com/docs/en/memory#write-effective-instructions)。[humanlayer 示例为 60 行](https://www.humanlayer.dev/blog/writing-a-good-claude-md)（[仍非 100% 保证](https://www.reddit.com/r/ClaudeCode/comments/1qn9pb9/claudemd_says_must_use_agent_claude_ignores_it_80/)）[![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179840848597422)
- 在 monorepo 使用 [multiple CLAUDE.md](best-practice/claude-memory.md)（祖先 + 后代加载）
- 使用 [.claude/rules/](https://code.claude.com/docs/en/memory#organize-rules-with-clauderules) 拆分大指令
- 对你的工作流优先使用 [commands](https://code.claude.com/docs/en/slash-commands)，而不是 [sub-agents](https://code.claude.com/docs/en/sub-agents) [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179847949500714)
- 优先做“功能专用 [sub-agents](https://code.claude.com/docs/en/sub-agents)（额外上下文）+ [skills](https://code.claude.com/docs/en/skills)（渐进披露）”，而不是通用 QA/backend engineer [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179850139000872)
- [memory.md](https://code.claude.com/docs/en/memory)、constitution.md 并不能保证一定生效
- 避免 agent dumb zone：在 50% 时手动执行 [/compact](https://code.claude.com/docs/en/interactive-mode)；切新任务可用 [/clear](https://code.claude.com/docs/en/cli-reference) 重置上下文
- 小任务场景下，原生 Claude Code 往往比复杂工作流更好
- monorepo 中把 [skills 放在子目录](reports/claude-skills-for-larger-mono-repos.md)
- 用 [/model](https://code.claude.com/docs/en/model-config) 选模型与推理、[/context](https://code.claude.com/docs/en/interactive-mode) 看上下文占用、[/usage](https://code.claude.com/docs/en/costs) 看套餐限制、[/extra-usage](https://code.claude.com/docs/en/interactive-mode) 配置超额计费、[/config](https://code.claude.com/docs/en/settings) 管理设置
- 在 [/config](https://code.claude.com/docs/en/settings) 中建议开启 [thinking mode](https://code.claude.com/docs/en/model-config) true 与 [Output Style](https://code.claude.com/docs/en/output-styles) Explanatory，以更好理解 Claude 决策 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179838864666847)
- 在提示中使用 ultrathink 关键词触发 [高强度推理](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking#tips-and-best-practices)
- 用 [/rename](https://code.claude.com/docs/en/cli-reference) 命名重要会话（例如 [TODO - refactor task]），并通过 [/resume](https://code.claude.com/docs/en/cli-reference) 后续继续
- Claude 跑偏时，用 [Esc Esc 或 /rewind](https://code.claude.com/docs/en/checkpointing) 回退，比在同一上下文硬修更有效
- 高频提交：至少每小时提交一次；任务完成立即提交

■ **Workflows Advanced (6)**
- 大量使用 ASCII 图帮助理解架构 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742759218794768)
- 用 [agent teams with tmux](https://code.claude.com/docs/en/agent-teams) + [git worktrees](https://x.com/bcherny/status/2025007393290272904) 做并行开发
- 用 [/loop](https://code.claude.com/docs/en/scheduled-tasks) 做周期监控：轮询部署、守护 PR、检查构建（最长 3 天）
- 长时间自治任务可用 [Ralph Wiggum plugin](https://github.com/shanraisshan/novel-llm-26) [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179858435281082)
- 使用 [/permissions](https://code.claude.com/docs/en/permissions) 的通配语法（Bash(npm run *), Edit(/docs/**)）替代危险的 dangerously-skip-permissions [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2007179854077407667)
- 使用 [/sandbox](https://code.claude.com/docs/en/sandboxing) 通过文件与网络隔离减少权限提示 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021700506465579443)

■ **Debugging (5)**
- 遇到问题时养成截图并分享给 Claude 的习惯
- 用 mcp（[Claude in Chrome](https://code.claude.com/docs/en/chrome)、[Playwright](https://github.com/microsoft/playwright-mcp)、[Chrome DevTools](https://developer.chrome.com/blog/chrome-devtools-mcp)）让 Claude 自行查看 Chrome 控制台日志
- 要求 Claude 把需观察日志的终端命令以后台任务运行，更利于调试
- 用 [/doctor](https://code.claude.com/docs/en/cli-reference) 诊断安装、认证与配置问题
- compact 报错时，可先用 [/model](https://code.claude.com/docs/en/model-config) 切到 1M token 模型，再执行 [/compact](https://code.claude.com/docs/en/interactive-mode)
- 用 [cross-model](development-workflows/cross-model-workflow/cross-model-workflow.md) 做 QA，例如用 [Codex](https://github.com/shanraisshan/codex-cli-best-practice) 做计划与实现审查

■ **Utilities (5)**
- 使用 [iTerm](https://iterm2.com/)/[Ghostty](https://ghostty.org/) [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2017742753971769626)/[tmux](https://github.com/tmux/tmux) 终端，而非 IDE（[VS Code](https://code.visualstudio.com/)/[Cursor](https://www.cursor.com/)）
- 用 [Wispr Flow](https://wisprflow.ai) 做语音提示（10x 生产力）
- 用 [claude-code-voice-hooks](https://github.com/shanraisshan/claude-code-voice-hooks) 获取 Claude 反馈
- 用 [status line](https://github.com/shanraisshan/claude-code-status-line) 提升上下文感知与快速 compact 能力 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021700784019452195)
- 探索 [settings.json](best-practice/claude-settings.md) 能力，如 [Plans Directory](best-practice/claude-settings.md#plans-directory)、[Spinner Verbs](best-practice/claude-settings.md#display--ux) 做个性化体验 [![Boris](!/tags/boris-cherny.svg)](https://x.com/bcherny/status/2021701145023197516)

■ **Daily (3)**
- 每天 [update](https://code.claude.com/docs/en/setup) Claude Code，并从阅读 [changelog](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md) 开始一天
- 关注 [r/ClaudeAI](https://www.reddit.com/r/ClaudeAI/)、[r/ClaudeCode](https://www.reddit.com/r/ClaudeCode/) ![Reddit](https://img.shields.io/badge/-FF4500?style=flat&logo=reddit&logoColor=white)
- 关注 [Boris](https://x.com/bcherny)、[Thariq](https://x.com/trq212)、[Cat](https://x.com/_catwu)、[Lydia](https://x.com/lydiahallie)、[Noah](https://x.com/noahzweben)、[Anthony](https://x.com/amorriscode)、[Claude](https://x.com/claudeai)、[Alex](https://x.com/alexalbert__) ![X](https://img.shields.io/badge/-000?style=flat&logo=x&logoColor=white)

![Boris Cherny + Team](!/tags/boris-team.svg)

- Always use plan mode, give Claude a way to verify, use /code-review (Boris) | 27/Dec/25 ● [Tweet](https://x.com/bcherny/status/2004711722926616680)
- Ask Claude to interview you using AskUserQuestion tool (Thariq) | 28/Dec/25 ● [Tweet](https://x.com/trq212/status/2005315275026260309)
- [How I use Claude Code — 13 tips from my surprisingly vanilla setup (Boris) | 03/Jan/26](tips/claude-boris-13-tips-03-jan-26.md) ● [Tweet](https://x.com/bcherny/status/2007179832300581177)
- [10 tips for using Claude Code from the team (Boris) | 01/Feb/26](tips/claude-boris-10-tips-01-feb-26.md) ● [Tweet](https://x.com/bcherny/status/2017742741636321619)
- [12 ways how people are customizing their claudes (Boris) | 12/Feb/26](tips/claude-boris-12-tips-12-feb-26.md) ● [Tweet](https://x.com/bcherny/status/2021699851499798911)
- Git Worktrees - 5 ways how boris is using | 21 Feb 2026 ● [Tweet](https://x.com/bcherny/status/2025007393290272904)
- Seeing like an Agent - lessons from building Claude Code (Thariq) | 28 Feb 2026 ● [Tweet](https://x.com/trq212/status/2027463795355095314)
- AskUserQuestion + ASCII Markdowns (Thariq) | 28 Feb 2026 ● [Tweet](https://x.com/trq212/status/2027543858289250472)
- /loop — schedule recurring tasks for up to 3 days (Boris) | 07 Mar 2026 ● [Tweet](https://x.com/bcherny/status/2030193932404150413)
- [Code Review & Test Time Compute (Boris) | 10/Mar/26](tips/claude-boris-2-tips-10-mar-26.md) ● [Tweet](https://x.com/bcherny/status/2031089411820228645)
- /btw — side chain conversations while Claude works (Thariq) | 10 Mar 2026 ● [Tweet](https://x.com/trq212/status/2031506296697131352)

## ☠️ STARTUPS / BUSINESSES

| Claude | Replaced |
|-|-|
|[**Code Review**](https://code.claude.com/docs/en/code-review)|[Greptile](https://greptile.com), [CodeRabbit](https://coderabbit.ai), [Devin Review](https://devin.ai), [OpenDiff](https://opendiff.com), [Cursor BugBot](https://bugbot.dev)|
|[**Voice Mode**](https://x.com/trq212/status/2028628570692890800)|[Wispr Flow](https://wisprflow.ai), [SuperWhisper](https://superwhisper.com/)|
|[**Remote Control**](https://code.claude.com/docs/en/remote-control)|[OpenClaw](https://openclaw.ai/)
|[**Cowork**](https://claude.com/blog/cowork-research-preview)|[OpenAI Operator](https://openai.com/operator), [AgentShadow](https://agentshadow.ai)
|[**Tasks**](https://x.com/trq212/status/2014480496013803643)|[Beads](https://github.com/steveyegge/beads)
|[**Plan Mode**](https://code.claude.com/docs/en/common-workflows)|[Agent OS](https://github.com/buildermethods/agent-os)|
|[**Skills / Plugins**](https://code.claude.com/docs/en/plugins)|YC AI wrapper startups ([reddit](https://reddit.com/r/ClaudeAI/comments/1r6bh4d/claude_code_skills_are_basically_yc_ai_startup/))|

<a id="billion-dollar-questions"></a>
![Billion-Dollar Questions](!/tags/billion-dollar-questions.svg)

*如果你有答案，欢迎联系：shanraisshan@gmail.com*

**Memory & Instructions (4)**

1. CLAUDE.md 里到底该放什么，不该放什么？
2. 已经有 CLAUDE.md 之后，还需要独立的 constitution.md 或 rules.md 吗？
3. CLAUDE.md 应该多久更新一次？如何判断它已经过时？
4. 为什么即使写了 MUST 大写，Claude 仍可能忽略 CLAUDE.md 指令？（[reddit](https://reddit.com/r/ClaudeCode/comments/1qn9pb9/claudemd_says_must_use_agent_claude_ignores_it_80/)）

**Agents, Skills & Workflows (6)**

1. 什么时候该用 command、agent、skill？什么时候原生 Claude Code 反而更好？
2. 随着模型能力提升，agents、commands、workflows 应该多久更新一次？
3. 给 subagent 写详细人格设定会提升质量吗？研究/QA subagent 的“最佳 persona/prompt”长什么样？
4. 应该依赖 Claude Code 内置 plan mode，还是自己构建能强制团队流程的 planning command/agent？
5. 如果你有个人 skill（例如符合你编码风格的 /implement），如何与社区 skill（例如 /simplify）并存且不冲突？冲突时谁优先？
6. 我们到了那一步吗？能否把现有代码库转成 specs，删除代码，再让 AI 仅凭 specs 还原出完全一致的代码？

**Specs & Documentation (3)**

1. 仓库里的每个功能都应该有对应 markdown 规格文档吗？
2. 新功能落地后，为避免规格过时，文档更新频率该怎么定？
3. 实现新功能时，如何处理它对其他功能规格文档的连带影响？

## REPORTS

<p align="center">
  <a href="reports/claude-agent-sdk-vs-cli-system-prompts.md"><img src="https://img.shields.io/badge/Agent_SDK_vs_CLI-555?style=for-the-badge" alt="Agent SDK vs CLI"></a>
  <a href="reports/claude-in-chrome-v-chrome-devtools-mcp.md"><img src="https://img.shields.io/badge/Browser_Automation_MCP-555?style=for-the-badge" alt="Browser Automation MCP"></a>
  <a href="reports/claude-global-vs-project-settings.md"><img src="https://img.shields.io/badge/Global_vs_Project_Settings-555?style=for-the-badge" alt="Global vs Project Settings"></a>
  <a href="reports/claude-skills-for-larger-mono-repos.md"><img src="https://img.shields.io/badge/Skills_in_Monorepos-555?style=for-the-badge" alt="Skills in Monorepos"></a>
  <br>
  <a href="reports/claude-agent-memory.md"><img src="https://img.shields.io/badge/Agent_Memory-555?style=for-the-badge" alt="Agent Memory"></a>
  <a href="reports/claude-advanced-tool-use.md"><img src="https://img.shields.io/badge/Advanced_Tool_Use-555?style=for-the-badge" alt="Advanced Tool Use"></a>
  <a href="reports/claude-usage-and-rate-limits.md"><img src="https://img.shields.io/badge/Usage_&_Rate_Limits-555?style=for-the-badge" alt="Usage & Rate Limits"></a>
  <a href="reports/claude-agent-command-skill.md"><img src="https://img.shields.io/badge/Agents_vs_Commands_vs_Skills-555?style=for-the-badge" alt="Agents vs Commands vs Skills"></a>
  <br>
  <a href="reports/llm-day-to-day-degradation.md"><img src="https://img.shields.io/badge/LLM_Degradation-555?style=for-the-badge" alt="LLM Degradation"></a>
</p>

<p align="center">
  <a href="https://github.com/trending?since=monthly"><img src="!/root/github-trending.png" alt="GitHub Trending" width="1200"></a><br>
  ✨2026 年 3 月 GitHub Trending✨
</p>

![How to Use](!/tags/how-to-use.svg)

```
1. 把这个仓库当课程来读：先理解 commands、agents、skills、hooks，再开始使用。
2. 克隆仓库并亲自跑示例：试试 /weather-orchestrator，听 hook 音效，运行 agent teams，理解它们如何实际工作。
3. 回到你的项目里，让 Claude 参考本仓库，建议你应引入哪些最佳实践。
```

## Developed by

![Developed by](!/tags/developed-by.svg)

> | Workflow | Description |
> |----------|-------------|
> | /workflows:best-practice:workflow-concepts | 更新 README CONCEPTS 区域，跟进最新 Claude Code 功能与概念 |
> | /workflows:best-practice:workflow-claude-settings | 跟踪 Claude Code settings 报告变更并找出需更新项 |
> | /workflows:best-practice:workflow-claude-subagents | 跟踪 Claude Code subagents 报告变更并找出需更新项 |
> | /workflows:best-practice:workflow-claude-commands | 跟踪 Claude Code commands 报告变更并找出需更新项 |
> | /workflows:best-practice:workflow-claude-skills | 跟踪 Claude Code skills 报告变更并找出需更新项 |

[![Claude for OSS](!/tags/claude-for-oss.svg)](https://claude.com/contact-sales/claude-for-oss)
[![Claude Community Ambassador](!/tags/claude-community-ambassador.svg)](https://claude.com/community/ambassadors)
[![Claude Certified Architect](!/tags/claude-certified-architect.svg)](https://anthropic.skilljar.com/claude-certified-architect-foundations-access-request)
[![Anthropic Academy](!/tags/anthropic-academy.svg)](https://anthropic.skilljar.com/)
