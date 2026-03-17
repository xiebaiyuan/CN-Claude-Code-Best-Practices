# Claude Code：用量、限流与 Extra Usage

理解 Claude Code 的用量限制，以及触发限制后如何继续工作。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 概览

订阅计划（Pro、Max 5x、Max 20x）的 Claude Code 有滚动窗口用量限制。以下三个内置命令用于监控与管理用量：

| 命令 | 说明 | 适用对象 |
|---------|-------------|--------------|
| `/usage` | 查看套餐额度与限流状态 | Pro、Max 5x、Max 20x |
| `/extra-usage` | 配置超额付费以突破限流 | Pro、Max 5x、Max 20x |
| `/cost` | 查看当前会话 token 用量与费用 | API key 用户 |

---

## `/usage` — 查看额度

显示当前套餐的用量限制与限流状态。适合在触发限制前查看剩余额度。

---

## `/extra-usage` — 超额继续

`/extra-usage` 可配置 **按量付费溢出**，当触发套餐限流时继续工作，而不是被阻断。

### 工作方式

1. 命中套餐限流（每 5 小时重置）
2. 若开启 extra usage 且有余额，则不中断继续
3. 溢出 token 按 **标准 API 计费**，与订阅费分开

### 配置方式

CLI 的 `/extra-usage` 会引导配置，也可在 claude.ai 的 **Settings > Usage** 配置：

1. 启用 extra usage
2. 添加支付方式
3. 设置 **月度上限**（或无限制）
4. 可选：预存余额并自动补充

### 关键细节

| 细节 | 数值 |
|--------|-------|
| 每日兑换上限 | $2,000/天 |
| 计费 | 与订阅分离，按 API 标准费率 |
| 限流重置窗口 | 每 5 小时 |

### 已知问题

截至 2026 年 2 月，CLI 中的 `/extra-usage` [未文档化](https://github.com/anthropics/claude-code/issues/12396)，可能弹出登录窗口但配置不清晰。当前更可靠的方式是通过 **claude.ai Web 界面** 进行配置。

---

## `/cost` — 会话费用（API 用户）

对 API key 用户（非订阅计划），`/cost` 显示：

- 当前会话总费用
- API 时长与墙钟时间
- Token 用量拆分
- 代码改动

对 Pro/Max 订阅用户不适用。

---

## Fast Mode 与 Extra Usage

Fast mode（`/fast`）使用 Claude Opus 4.6 的更快输出。它与 extra usage 计费关系特殊：

- **所有 fast mode 用量都会计入 extra usage**，从第一个 token 开始
- 即便订阅额度仍有剩余也如此
- fast mode 不消耗订阅内含额度

这意味着使用 `/fast` 必须启用并有余额的 extra usage。

---

## CLI 启动参数

以下启动参数与用量预算相关（仅 API key 用户，且为 print mode）：

| 参数 | 说明 |
|------|-------------|
| `--max-budget-usd <AMOUNT>` | API 调用的美元预算上限 |
| `--max-turns <NUMBER>` | 限制 agentic 回合数 |

完整列表见 [CLI Startup Flags Reference](claude-cli-startup-flags.md)。

---

## Sources

- [Extra usage for paid Claude plans — Claude Help Center](https://support.claude.com/en/articles/12429409-extra-usage-for-paid-claude-plans)
- [Using Claude Code with your Pro or Max plan — Claude Help Center](https://support.claude.com/en/articles/11145838-using-claude-code-with-your-pro-or-max-plan)
- [/extra-usage slash command is undocumented — GitHub Issue #12396](https://github.com/anthropics/claude-code/issues/12396)
- [Claude Code CLI Reference](https://code.claude.com/docs/en/cli-reference)
