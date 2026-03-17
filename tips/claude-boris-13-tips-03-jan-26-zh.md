# 我如何使用 Claude Code —— Boris Cherny 的 13 条建议

本文总结了 Claude Code 创建者 Boris Cherny（[@bcherny](https://x.com/bcherny)）在 2026 年 1 月 3 日分享的设置与使用技巧。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 背景

Boris 分享了他的个人 Claude Code 配置，并表示它“出奇地朴素”——Claude Code 开箱即用已经很好，所以他并不做太多定制。没有唯一正确的使用方式：团队刻意将它设计为可使用、可定制、可改造的工具，团队成员的用法都很不同。

<a href="https://x.com/bcherny/status/2007179832300581177"><img src="assets/boris-3-jan-26/0.png" alt="Boris Cherny intro tweet" width="50%" /></a>

---

## 1/ 并行运行 5 个 Claude

在终端同时跑 5 个 Claude。把标签页编号 1–5，用系统通知判断何时需要输入。

参见：[Terminal Setup Docs](https://code.claude.com/docs/en/terminal)

<a href="https://x.com/bcherny/status/2007179833990885678"><img src="assets/boris-3-jan-26/1.png" alt="Run 5 Claudes in parallel" width="50%" /></a>

---

## 2/ 用 claude.ai/code 获得更多并行度

在 claude.ai/code 上并行跑 5–10 个 Claude，与本地 Claude 同时使用。用 `claude.ai/code` 把本地会话交给网页会话，在 Chrome 中启动，再来回 teleport。

<a href="https://x.com/bcherny/status/2007179836704600237"><img src="assets/boris-3-jan-26/2.png" alt="claude.ai/code parallelism" width="50%" /></a>

---

## 3/ 所有任务都用 Opus + Thinking

所有任务都使用带 thinking 的 Opus 4.5。这是 Boris 用过最好的编码模型——虽然比 Sonnet 更大更慢，但由于需要更少的引导、工具使用更好，最终通常更快。

<a href="https://x.com/bcherny/status/2007179838864666847"><img src="assets/boris-3-jan-26/3.png" alt="Opus with thinking" width="50%" /></a>

---

## 4/ 与团队共享一个 CLAUDE.md

为仓库维护一个共享 `CLAUDE.md` 并提交到 git，让团队每周多次共同维护。每次 Claude 做错事就加入 `CLAUDE.md`，让它下次避免。

<a href="https://x.com/bcherny/status/2007179840848597422"><img src="assets/boris-3-jan-26/4.png" alt="Shared CLAUDE.md" width="50%" /></a>

---

## 5/ 在 PR 中 @claude 更新 CLAUDE.md

代码评审时在同事 PR 上 @claude，让它在 PR 中补充 `CLAUDE.md`。使用 Claude Code GitHub action（[install-@hub-action](https://github.com/apps/claude)）。这是 Boris 版的“工程复利”。

<a href="https://x.com/bcherny/status/2007179842928947333"><img src="assets/boris-3-jan-26/5.png" alt="Tag @claude on PRs" width="50%" /></a>

---

## 6/ 大多数会话先进入 Plan 模式

大多数会话先进入 Plan 模式（shift+tab 两次）。如果目标是写 PR，就用 Plan 模式与 Claude 反复完善计划，满意后切换到自动接受编辑模式，通常可以一次搞定。好计划非常关键。

<a href="https://x.com/bcherny/status/2007179845336527000"><img src="assets/boris-3-jan-26/6.png" alt="Plan mode" width="50%" /></a>

---

## 7/ 用斜杠命令做高频内循环

把每天重复多次的“内循环”工作流做成斜杠命令，这能减少重复提示，也让 Claude 能复用这些流程。命令提交到 git，位于 `.claude/commands/`。

例：`/commit-push-pr` —— 提交、推送并打开 PR。

<a href="https://x.com/bcherny/status/2007179847949500714"><img src="assets/boris-3-jan-26/7.png" alt="Slash commands" width="50%" /></a>

---

## 8/ 用 Subagents 自动化常用流程

常用几个 subagents：`code-simplifier` 在 Claude 完成后简化代码，`verify-app` 用于端到端测试等。把 subagents 视为常用流程的自动化 —— 类似斜杠命令。

Subagents 位于 `.claude/agents/`。

<a href="https://x.com/bcherny/status/2007179850139000872"><img src="assets/boris-3-jan-26/8.png" alt="Subagents" width="50%" /></a>

---

## 9/ 用 PostToolUse Hook 自动格式化代码

用 `PostToolUse` hook 格式化 Claude 生成的代码。Claude 输出通常已经不错，hook 处理最后 10%，避免 CI 格式化报错。

```json
"PostToolUse": [
  {
    "matcher": "Write|Edit",
    "hooks": [
      {
        "type": "command",
        "command": "bun run format || true"
      }
    ]
  }
]
```

<a href="https://x.com/bcherny/status/2007179852047335529"><img src="assets/boris-3-jan-26/9.png" alt="PostToolUse hook for formatting" width="50%" /></a>

---

## 10/ 预先允许权限，不要用 --dangerously-skip-permissions

不要用 `--dangerously-skip-permissions`。改用 `/permissions` 预先允许你环境里安全的常用 bash 命令，以减少权限提示。多数规则提交到 `.claude/settings.json` 并共享给团队。

<a href="https://x.com/bcherny/status/2007179854077407667"><img src="assets/boris-3-jan-26/10.png" alt="Pre-allow permissions" width="50%" /></a>

---

## 11/ 用 MCP 让 Claude 使用你的全部工具

Claude Code 会使用你的工具：通过 MCP 搜索并发布到 Slack，运行 BigQuery 查询分析指标，从 Sentry 拉取错误日志等。Slack MCP 配置提交在 `.mcp.json` 中并与团队共享。

<a href="https://x.com/bcherny/status/2007179856266789204"><img src="assets/boris-3-jan-26/11.png" alt="MCP tools" width="50%" /></a>

---

## 12/ 用后台 agents 验证长任务

对超长任务，可选择：（a）让 Claude 在完成后用后台 agent 验证；（b）使用 agent Stop hook 更确定地触发；（c）使用 ralph-wiggum 插件（最初由 @GeoffreyHuntley 提出）。

<a href="https://x.com/bcherny/status/2007179858435281082"><img src="assets/boris-3-jan-26/12.png" alt="Long-running tasks verification" width="50%" /></a>

---

## 13/ 给 Claude 一个验证方式

要获得高质量结果，最重要的是让 Claude 有验证方式。如果它有反馈闭环，最终质量会提高 2–3 倍。

Boris 提交的每个改动都会跑测试。

<a href="https://x.com/bcherny/status/2007179861115511237"><img src="assets/boris-3-jan-26/13.png" alt="Give Claude a way to verify" width="50%" /></a>

---

## Sources

- [Boris Cherny (@bcherny) on X — January 3, 2026](https://x.com/bcherny/status/2007179832300581177)
