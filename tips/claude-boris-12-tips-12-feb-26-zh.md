# 12 种自定义 Claude Code 的方式 —— Boris Cherny 的建议

本文总结了 Claude Code 创建者 Boris Cherny（[@bcherny](https://x.com/bcherny)）在 2026 年 2 月 12 日分享的自定义技巧。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 背景

Boris 强调，可定制性是工程师最喜欢 Claude Code 的点之一——hooks、plugins、LSP、MCP、skills、effort、自定义 agents、状态栏、输出风格等。他分享了 12 个实用的自定义方式。

<a href="https://x.com/bcherny/status/2021699851499798911"><img src="assets/boris-12-feb-26/0.webp" alt="Boris Cherny intro tweet" width="50%" /></a>

---

## 1/ 配置你的终端

为了最佳体验，先配置终端：

- **主题**：运行 `/config` 设置浅色/深色模式
- **通知**：启用 iTerm2 通知，或使用自定义通知 hook
- **换行**：在 IDE 终端、Apple Terminal、Warp 或 Alacritty 中运行 `/terminal-setup` 以启用 shift+enter 换行（无需手动输入 `\`）
- **Vim 模式**：运行 `/vim`

<a href="https://x.com/bcherny/status/2021699859359883608"><img src="assets/boris-12-feb-26/1.webp" alt="Configure your terminal" width="50%" /></a>

---

## 2/ 调整 Effort Level

运行 `/model` 选择 effort level：

- **Low** — 更少 token、更快响应
- **Medium** — 平衡
- **High** — 更多 token、更高智能

Boris 偏好：全部用 High。

<a href="https://x.com/bcherny/status/2021699860869902424"><img src="assets/boris-12-feb-26/2.webp" alt="Adjust effort level" width="50%" /></a>

---

## 3/ 安装 Plugins、MCP 与 Skills

Plugins 可以安装 LSP（主流语言都有）、MCP、skills、agents 与自定义 hooks。

可从 Anthropic 官方插件市场安装，也可以为公司自建市场。将 `settings.json` 提交到代码库，团队成员自动获得市场配置。

运行 `/plugin` 开始。

<a href="https://x.com/bcherny/status/2021699862522364149"><img src="assets/boris-12-feb-26/3.webp" alt="Install Plugins, MCPs, and Skills" width="50%" /></a>

---

## 4/ 创建自定义 Agents

在 `.claude/agents` 中放置 `.md` 文件即可创建自定义 agents。每个 agent 可设置名称、颜色、工具集、允许/禁止工具、权限模式与模型。

也可在 `settings.json` 用 `"agent"` 字段或 `--agent` 参数设置主对话默认 agent。

运行 `/agents` 开始。

<a href="https://x.com/bcherny/status/2021700144039903699"><img src="assets/boris-12-feb-26/4.webp" alt="Create custom agents" width="50%" /></a>

---

## 5/ 预先批准常用权限

Claude Code 权限系统结合了提示注入检测、静态分析、沙盒与人工确认。

默认已预批准少量安全命令。若需更多，运行 `/permissions` 将规则加入 allow/block 列表，并提交到团队的 `settings.json`。

支持通配符语法，如 `Bash(bun run *)` 或 `Edit(/docs/**)`。

<a href="https://x.com/bcherny/status/2021700332292911228"><img src="assets/boris-12-feb-26/5.webp" alt="Pre-approve common permissions" width="50%" /></a>

---

## 6/ 启用沙盒

启用 Claude Code 开源沙盒运行时，以提升安全性并减少权限提示。

运行 `/sandbox`。沙盒在本地运行，支持文件与网络隔离。

<a href="https://x.com/bcherny/status/2021700506465579443"><img src="assets/boris-12-feb-26/6.webp" alt="Enable sandboxing" width="50%" /></a>

---

## 7/ 添加状态栏

自定义状态栏显示在编辑框下方，展示模型、目录、剩余上下文、成本等你关心的信息。

每个成员都可以有不同的状态栏。运行 `/statusline`，Claude 会基于 `.bashrc`/`.zshrc` 生成配置。

<a href="https://x.com/bcherny/status/2021700784019452195"><img src="assets/boris-12-feb-26/7.webp" alt="Add a status line" width="50%" /></a>

---

## 8/ 自定义快捷键

Claude Code 的每个快捷键都可自定义。运行 `/keybindings` 重映射，设置会热加载，立即体验。

<a href="https://x.com/bcherny/status/2021700883873165435"><img src="assets/boris-12-feb-26/8.webp" alt="Customize your keybindings" width="50%" /></a>

---

## 9/ 设置 Hooks

Hooks 允许确定性接入 Claude 生命周期：

- 将权限请求自动转发到 Slack 或 Opus
- 在回合结束时提示 Claude 继续（还可用 prompt 决定是否继续）
- 预处理/后处理工具调用，例如加入日志

让 Claude 帮你添加 hook 即可开始。

<a href="https://x.com/bcherny/status/2021701059253874861"><img src="assets/boris-12-feb-26/9.webp" alt="Set up hooks" width="50%" /></a>

---

## 10/ 自定义 Spinner 动词

替换或扩展默认 spinner 动词列表，将 `settings.json` 提交到 git 共享给团队。

<a href="https://x.com/bcherny/status/2021701145023197516"><img src="assets/boris-12-feb-26/10.webp" alt="Customize your spinner verbs" width="50%" /></a>

---

## 11/ 使用输出风格

运行 `/config` 设置输出风格，让 Claude 用不同语气或格式回复：

- **Explanatory** —— 推荐用于熟悉新代码库，Claude 会解释框架与模式
- **Learning** —— 让 Claude 像教练一样指导修改
- **Custom** —— 自定义输出风格调整语气

<a href="https://x.com/bcherny/status/2021701379409273093"><img src="assets/boris-12-feb-26/11.webp" alt="Use output styles" width="50%" /></a>

---

## 12/ 所有东西都可以自定义！

Claude Code 开箱即用已经很好，但一旦自定义，建议将 `settings.json` 提交到 git，让团队共享。配置支持多级别：

- 代码库级
- 子目录级
- 个人级
- 企业策略级

截至该分享时有 37 个设置项和 84 个环境变量（用 `settings.json` 的 `"env"` 字段避免 wrapper 脚本）。大概率你想要的行为都能配置。

<a href="https://x.com/bcherny/status/2021701636075458648"><img src="assets/boris-12-feb-26/12.webp" alt="Customize all the things" width="50%" /></a>

---

## Sources

- [Boris Cherny (@bcherny) on X — February 12, 2026](https://x.com/bcherny)
- [Claude Code Terminal Setup Docs](https://code.claude.com/docs/en/terminal)
- [Claude Code Plugins & Discovery Docs](https://code.claude.com/docs/en/discover-plugins)
- [Claude Code Sub-agents Docs](https://code.claude.com/docs/en/sub-agents)
- [Claude Code Permissions Docs](https://code.claude.com/docs/en/permissions)
- [Claude Code Sandbox Docs](https://code.claude.com/docs/en/sandbox)
- [Claude Code Status Line Docs](https://code.claude.com/docs/en/statusline)
- [Claude Code Keyboard Shortcuts Docs](https://code.claude.com/docs/en/keybindings)
- [Claude Code Hooks Reference](https://code.claude.com/docs/en/hooks)
- [Claude Code Output Styles Docs](https://code.claude.com/docs/en/output-styles)
- [Claude Code Settings Docs](https://code.claude.com/docs/en/settings)
