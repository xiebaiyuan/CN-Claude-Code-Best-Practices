# Claude Code 团队的 10 条使用技巧

本文总结了 Claude Code 创建者 Boris Cherny（[@bcherny](https://x.com/bcherny)）在 2026 年 2 月 1 日分享的团队使用技巧。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 背景

Boris 分享了 Claude Code 团队内部的使用技巧。团队的用法与他个人习惯不同。记住：Claude Code 没有唯一正确的使用方式 —— 每个人的配置都不同。请多尝试找到最适合自己的方法。

<a href="https://x.com/bcherny/status/2017742741636321619"><img src="assets/boris-1-feb-26/0.png" alt="Boris Cherny intro tweet" width="50%" /></a>

---

## 1/ 并行做更多

一次启动 3–5 个 git worktree，每个 worktree 运行独立 Claude 会话。这是最大的效率提升，也是团队的第一条建议。Boris 个人会用多个 git checkout，但团队多数人更偏好 worktree —— 这也是 `@amorisscode` 把原生支持集成到 Claude Desktop 的原因。

有人会给 worktree 命名并配置 shell alias（`2a`、`2b`、`2c`）一键切换，也有人保留一个专门的 “analysis” worktree 用于读日志与跑 BigQuery。

参见：[Worktrees Docs](https://code.claude.com/docs/en/common...)

<a href="https://x.com/bcherny/status/2017742743125299476"><img src="assets/boris-1-feb-26/1.png" alt="Do more in parallel" width="50%" /></a>

---

## 2/ 复杂任务先进入 Plan 模式

把精力投入到计划，让 Claude 一次成型。

有人让一个 Claude 写计划，再开第二个 Claude 以 Staff 工程师身份审查。也有人在事情开始跑偏时立刻回到 Plan 模式重新规划，不再硬推进。他们还会明确要求 Claude 在验证步骤也进入 Plan 模式，而不仅仅是 build。

<a href="https://x.com/bcherny/status/2017742745365057733"><img src="assets/boris-1-feb-26/2.png" alt="Start every complex task in plan mode" width="50%" /></a>

---

## 3/ 投资你的 CLAUDE.md

每次纠正后都加一句：“Update your CLAUDE.md so you don't make that mistake again.” Claude 非常擅长给自己写规则。

持续打磨 `CLAUDE.md`，直到 Claude 的错误率明显下降。

有位工程师会让 Claude 为每个任务/项目维护笔记目录，每次 PR 后更新，再在 `CLAUDE.md` 中指向它。

<a href="https://x.com/bcherny/status/2017742747067945390"><img src="assets/boris-1-feb-26/3.png" alt="Invest in your CLAUDE.md" width="50%" /></a>

---

## 4/ 自建技能并提交到 Git

跨项目复用。团队建议：

- 每天做超过一次的事就写成 skill 或 command
- 做一个 `/techdebt` 命令，在每次会话结束时找出并清理重复代码
- 建一个命令把 7 天的 Slack、GDrive、Asana、GitHub 同步到一个上下文
- 构建 analytics 工程师风格的 agents：写 dbt 模型、评审代码、在 dev 环境测试

参见：[Extend Claude with Skills — Claude Code Docs](https://code.claude.com/docs/en/skills)

<a href="https://x.com/bcherny/status/2017742748984742078"><img src="assets/boris-1-feb-26/4.png" alt="Create your own skills" width="50%" /></a>

---

## 5/ Claude 能自己修大多数 Bug

团队的做法：

启用 Slack MCP，把 Slack bug 线程粘贴给 Claude 直接说 “fix”，无需切换上下文。

或者直接说 “去修复失败的 CI 测试”，不要微操。

把 Docker 日志给 Claude 排查分布式系统问题也很有效。

<a href="https://x.com/bcherny/status/2017742750473720121"><img src="assets/boris-1-feb-26/5.png" alt="Claude fixes most bugs by itself" width="50%" /></a>

---

## 6/ 提升提示词

a. **挑战 Claude。** 说 “Grill me on these changes and don't make a PR until I pass your test.” 让 Claude 当你的审阅者。或者说 “Prove to me this works”，让 Claude 对比主分支与功能分支行为差异。

b. **修得一般时，** 说：“Knowing everything you know now, scrap this and implement the elegant solution.”

c. **写详细规格，** 降低歧义。越具体，产出越好。

<a href="https://x.com/bcherny/status/2017742752566632544"><img src="assets/boris-1-feb-26/6.png" alt="Level up your prompting" width="50%" /></a>

---

## 7/ 终端与环境配置

团队很喜欢 Ghostty！多人认可它的同步渲染、24-bit 色彩与良好 unicode 支持。

为便于切换多个 Claude，会用 `/statusline` 自定义状态栏，常显示上下文使用量与当前 git 分支。很多人还会给终端标签上色并命名，有的使用 tmux —— 一任务/一 worktree 一个 tab。

语音输入也值得用：说话比打字快 3 倍，提示词会更详细。（macOS 上按 fn 两次）

参见：[Terminal Setup Docs](https://code.claude.com/docs/en/termin...)

<a href="https://x.com/bcherny/status/2017742753971769626"><img src="assets/boris-1-feb-26/7.png" alt="Terminal and environment setup" width="50%" /></a>

---

## 8/ 使用 Subagents

a. 在任何需要更多算力的请求末尾加一句 “use subagents”。

b. 把任务拆给 subagents，保持主上下文干净。

c. 用 hook 把权限请求转给 Opus 4.5，让它扫描攻击并自动批准安全请求。参见：[Hooks Docs](https://code.claude.com/docs/en/hooks#...)

<a href="https://x.com/bcherny/status/2017742755737555434"><img src="assets/boris-1-feb-26/8.png" alt="Use subagents" width="50%" /></a>

---

## 9/ 用 Claude 做数据与分析

让 Claude Code 调用 `bq` CLI 即时拉取并分析指标。团队把 BigQuery skill 提交在代码库里，所有人直接在 Claude Code 里做分析查询。Boris 个人已经 6+ 个月没写过 SQL。

对任何有 CLI、MCP 或 API 的数据库都适用。

<a href="https://x.com/bcherny/status/2017742757666902374"><img src="assets/boris-1-feb-26/9.png" alt="Use Claude for data and analytics" width="50%" /></a>

---

## 10/ 用 Claude 学习

团队的一些学习建议：

a. 在 `/config` 中启用 “Explanatory” 或 “Learning” 输出风格，让 Claude 解释“为什么”。

b. 让 Claude 生成 HTML 可视化演示，用来讲解陌生代码。

c. 让 Claude 画 ASCII 图帮助理解协议与代码库。

d. 构建间隔复习技能：你先讲理解，Claude 追问补缺并保存结果。

<a href="https://x.com/bcherny/status/2017742759218794768"><img src="assets/boris-1-feb-26/10.png" alt="Learning with Claude" width="50%" /></a>

---

## Sources

- [Boris Cherny (@bcherny) on X — February 1, 2026](https://x.com/bcherny/status/2017742741636321619)
