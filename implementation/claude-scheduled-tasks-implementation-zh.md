# 定时任务实现

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_10%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#loop-demo"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

`/loop` 技能用于以 cron 间隔调度循环任务。下面演示 `/loop 1m "tell current time"` —— 一个每分钟触发的简单循环任务。

---

## Loop Demo

### 1. 任务调度

<p align="center">
  <img src="assets/impl-loop-1.png" alt="/loop 1m tell current time — scheduling and cron setup" width="100%">
</p>

`/loop 1m "tell current time"` 解析时间间隔（`1m` → 每 1 分钟），创建 cron 任务并确认计划。要点：

- Cron 最小粒度为 **1 分钟** —— `1m` 映射为 `*/1 * * * *`
- 循环任务 **3 天后自动过期**
- 任务 **会话级** —— 仅存在于内存中，Claude 退出即停止
- 可随时用 `cron cancel <job-id>` 取消

---

### 2. 循环执行

<p align="center">
  <img src="assets/impl-loop-2.png" alt="Recurring task firing every minute" width="100%">
</p>

任务每分钟触发一次，执行 `date` 并汇报当前时间。每次迭代都会触发异步 **UserPromptSubmit** 与 **Stop** hooks —— 即本仓库用于声音通知的同一套 hook 系统。

---

## ![How to Use](../!/tags/how-to-use.svg)

```bash
$ claude
> /loop 1m "tell current time"
> /loop 5m /simplify
> /loop 10m "check deploy status"
```

---

## ![How to Implement](../!/tags/how-to-implement.svg)

`/loop` 是 Claude Code 内置技能，无需额外配置。它在底层使用 cron 工具（`CronCreate`、`CronList`、`CronDelete`）管理循环计划。
