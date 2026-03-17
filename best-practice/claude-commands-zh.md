# Commands 最佳实践

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar%2015%2C%202026%2012%3A50%20PM%20PKT-white?style=flat&labelColor=555)<br>
[![Implemented](https://img.shields.io/badge/Implemented-2ea44f?style=flat)](../implementation/claude-commands-implementation.md)

Claude Code 命令 —— frontmatter 字段与官方内置斜杠命令。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## Frontmatter 字段（4）

| 字段 | 类型 | 必填 | 说明 |
|-------|------|------|-------------|
| `description` | string | 推荐 | 命令用途说明。展示在自动补全中，Claude 用于自动发现 |
| `argument-hint` | string | 否 | 自动补全时显示的提示（如 `[issue-number]`、`[filename]`） |
| `allowed-tools` | string | 否 | 命令运行时可免提示使用的工具 |
| `model` | string | 否 | 命令运行时使用的模型（如 `haiku`、`sonnet`、`opus`） |

---

## ![Official](../!/tags/official.svg) **（62）**

| # | Command | Tag | Description |
|---|---------|-----|-------------|
| 1 | `/login` | ![Auth](https://img.shields.io/badge/Auth-2980B9?style=flat) | 通过 OAuth 认证 Claude Code |
| 2 | `/logout` | ![Auth](https://img.shields.io/badge/Auth-2980B9?style=flat) | 退出 Claude Code |
| 3 | `/upgrade` | ![Auth](https://img.shields.io/badge/Auth-2980B9?style=flat) | 打开升级页面切换更高套餐 |
| 4 | `/color [color\|default]` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 设置当前会话的提示栏颜色 |
| 5 | `/config` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 打开带搜索的交互式设置界面 |
| 6 | `/keybindings` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 按上下文自定义快捷键与组合键 |
| 7 | `/permissions` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 查看或更新工具权限 |
| 8 | `/privacy-settings` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 管理隐私与遥测偏好 |
| 9 | `/sandbox` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 配置沙盒与依赖状态 |
| 10 | `/statusline` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 配置 Claude Code 状态栏 UI |
| 11 | `/stickers` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 订购 Claude Code 贴纸 |
| 12 | `/terminal-setup` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 在 IDE 终端启用 shift+enter 换行 |
| 13 | `/theme` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 切换配色主题 |
| 14 | `/vim` | ![Config](https://img.shields.io/badge/Config-F39C12?style=flat) | 启用 vim 风格编辑 |
| 15 | `/context` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 可视化当前上下文使用（彩色网格+Token 统计） |
| 16 | `/cost` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 显示当前会话 Token 使用统计 |
| 17 | `/extra-usage` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 为订阅计划配置超额计费 |
| 18 | `/insights` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 生成 Claude Code 会话报告（项目区域、交互模式、摩擦点） |
| 19 | `/stats` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 可视化日常使用、会话历史、连续天数与模型偏好 |
| 20 | `/status` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 打开设置界面（状态页）显示版本、模型、账号、连接状态 |
| 21 | `/usage` | ![Context](https://img.shields.io/badge/Context-8E44AD?style=flat) | 显示套餐用量上限与限流状态（订阅计划） |
| 22 | `/doctor` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 检查 Claude Code 安装健康度 |
| 23 | `/feedback [description]` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 生成用于反馈的 GitHub Issue URL。别名：`/bug` |
| 24 | `/help` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 显示斜杠命令帮助 |
| 25 | `/release-notes` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 显示最近发布说明 |
| 26 | `/tasks` | ![Debug](https://img.shields.io/badge/Debug-E74C3C?style=flat) | 列出并管理后台任务 |
| 27 | `/copy` | ![Export](https://img.shields.io/badge/Export-7F8C8D?style=flat) | 复制上一条助手回复到剪贴板 |
| 28 | `/export [filename]` | ![Export](https://img.shields.io/badge/Export-7F8C8D?style=flat) | 导出当前对话到文件或剪贴板 |
| 29 | `/agents` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理自定义子代理：查看、创建、编辑、删除 |
| 30 | `/chrome` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理 Claude in Chrome 浏览器集成 |
| 31 | `/hooks` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理工具事件 Hook 配置 |
| 32 | `/ide` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 连接 IDE 集成 |
| 33 | `/mcp` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理 MCP 服务器连接 |
| 34 | `/plugin` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 管理 Claude Code 插件 |
| 35 | `/reload-plugins` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 无需重启即可重载已安装插件 |
| 36 | `/skills` | ![Extensions](https://img.shields.io/badge/Extensions-16A085?style=flat) | 列出可用技能 |
| 37 | `/memory` | ![Memory](https://img.shields.io/badge/Memory-3498DB?style=flat) | 查看并编辑记忆文件 |
| 38 | `/effort [low\|medium\|high\|max\|auto]` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 设置模型努力程度 |
| 39 | `/fast` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 切换快速模式 —— 同 Opus 4.6 模型但更快输出 |
| 40 | `/model` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 切换模型（haiku、sonnet、opus）并调整努力程度 |
| 41 | `/passes [number]` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 向朋友分享 Claude Code 免费周（仅在账号符合条件时可见） |
| 42 | `/plan` | ![Model](https://img.shields.io/badge/Model-E67E22?style=flat) | 进入只读计划模式 —— 仅提供方案不做修改 |
| 43 | `/add-dir` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 为当前会话添加额外工作目录 |
| 44 | `/diff` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 查看当前仓库 git diff |
| 45 | `/init` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 用 CLAUDE.md 指引初始化新项目 |
| 46 | `/pr-comments` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 查看或回复 PR 评论 |
| 47 | `/review` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 已弃用 —— 请安装 `code-review` 插件 |
| 48 | `/security-review` | ![Project](https://img.shields.io/badge/Project-27AE60?style=flat) | 对当前变更进行安全审查 |
| 49 | `/desktop` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 在 Claude Code 桌面应用继续当前会话（仅 macOS/Windows） |
| 50 | `/install-github-app` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 安装用于 PR 工作流的 GitHub App |
| 51 | `/install-slack-app` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 安装 Slack App 以接收通知与分享 |
| 52 | `/mobile` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 连接或管理移动端伴侣应用 |
| 53 | `/remote-control` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 从其他设备继续当前会话 |
| 54 | `/remote-env` | ![Remote](https://img.shields.io/badge/Remote-5D6D7E?style=flat) | 查看或复制 remote-control 环境设置 |
| 55 | `/btw <question>` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 侧边提问，不加入主对话上下文 |
| 56 | `/clear` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 清空对话并重新开始 |
| 57 | `/compact [prompt]` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 压缩对话以释放上下文窗口，可选 prompt 指定压缩重点 |
| 58 | `/exit` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 退出 REPL |
| 59 | `/fork` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 将当前对话分叉为新会话 |
| 60 | `/rename <name>` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 重命名当前会话便于识别 |
| 61 | `/resume [session]` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 按 ID/名称恢复会话，或打开选择器 |
| 62 | `/rewind` | ![Session](https://img.shields.io/badge/Session-4A90D9?style=flat) | 回退对话和/或代码到更早状态 |

打包技能（如 `/debug`）也可能出现在斜杠命令菜单中，但它们不是内置命令。

---

## Sources

- [Claude Code Slash Commands](https://code.claude.com/docs/en/slash-commands)
- [Claude Code Interactive Mode](https://code.claude.com/docs/en/interactive-mode)
- [Claude Code CHANGELOG](https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md)
