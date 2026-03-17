# Code Review 与 Test Time Compute —— Boris Cherny 的建议

本文总结了 Claude Code 创建者 Boris Cherny（[@bcherny](https://x.com/bcherny)）在 2026 年 3 月 10 日分享的要点。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 1/ 引入 Code Review

Claude Code 新功能：**Code Review**。一组 agents 会对每个 PR 进行深入审查。

- 先为 Anthropic 自己的团队构建 —— 今年每位工程师产出 **提升 200%**，审查成了瓶颈
- Boris 使用了数周，发现它能捕捉到许多他自己没注意到的真实 bug
- PR 开启后，Claude 会派出一支 agent 团队寻找 bug

<a href="https://x.com/bcherny/status/2031089411820228645"><img src="assets/boris-10-mar-26/0.png" alt="Boris Cherny announcing Code Review" width="50%" /></a>

---

## 2/ Test Time Compute 与多上下文窗口

大体上，给编码问题投入越多 token，结果越好。Boris 将其称为 **test time compute**。

- 使用 **独立上下文窗口** 让结果更好 —— 这就是 subagents 的核心机制，也是为什么一个 agent 可能引入 bug，而另一个（同模型）却能发现它
- 类似工程团队：Boris 可能引入 bug，但同事评审更可能发现
- 从极限看，agents 可能最终写出零 bug 代码 —— 在此之前，**多个不相关上下文** 是很好的策略

<a href="https://x.com/bcherny/status/2031151689219321886"><img src="assets/boris-10-mar-26/1.png" alt="Boris Cherny on test time compute" width="50%" /></a>

---

## Sources

- [Boris Cherny (@bcherny) on X — March 10, 2026](https://x.com/bcherny)
