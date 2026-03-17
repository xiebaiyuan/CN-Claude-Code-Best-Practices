# Sub-agents 实现

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_02%2C_2026_07%3A59_PM_PKT-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-agent"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

天气 agent 在本仓库中作为 **Command → Agent → Skill** 架构模式的示例，实现了两种不同的技能模式。

---

## Weather Agent

**File**: [`.claude/agents/weather-agent.md`](../.claude/agents/weather-agent.md)

```yaml
---
name: weather-agent
description: Use this agent PROACTIVELY when you need to fetch weather data for
  Dubai, UAE. This agent fetches real-time temperature from Open-Meteo
  using its preloaded weather-fetcher skill.
tools: WebFetch, Read, Write, Edit
model: sonnet
color: green
maxTurns: 5
permissionMode: acceptEdits
memory: project
skills:
  - weather-fetcher
---

# Weather Agent

You are a specialized weather agent that fetches weather data for Dubai,
UAE.

## Your Task

Execute the weather workflow by following the instructions from your preloaded
skill:

1. **Fetch**: Follow the `weather-fetcher` skill instructions to fetch the
   current temperature
2. **Report**: Return the temperature value and unit to the caller
3. **Memory**: Update your agent memory with the reading details for
   historical tracking

...
```

该 agent 预加载了一个技能（`weather-fetcher`），用于从 Open-Meteo 获取数据，并将温度值与单位返回给调用命令。

---

## ![How to Use](../!/tags/how-to-use.svg)

```bash
$ claude
> what is the weather in dubai?
```

---

## ![How to Implement](../!/tags/how-to-implement.svg)

可以使用 `/agents` 创建 agent：
```bash
$ claude
> /agents
```

或让 Claude 为你创建 —— 它会在 `.claude/agents/<name>.md` 生成带 YAML frontmatter 的 markdown 文件。

---

<a href="https://github.com/shanraisshan/claude-code-best-practice#orchestration-workflow"><img src="../!/tags/orchestration-workflow-hd.svg" alt="Orchestration Workflow"></a>

天气 agent 是 Command → Agent → Skill 模式中的 **Agent**。它接收 `/weather-orchestrator` 的工作流，通过预加载技能 `weather-fetcher` 获取温度，然后由命令调用独立 `weather-svg-creator` 生成可视化。

<p align="center">
  <img src="../orchestration-workflow/orchestration-workflow.svg" alt="Command Skill Agent Architecture Flow" width="100%">
</p>

| 组件 | 角色 | 本仓库 |
|-----------|------|-----------|
| **Command** | 入口、用户交互 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **Agent** | 使用预加载技能获取数据（agent skill） | [`weather-agent`](../.claude/agents/weather-agent.md) with [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **Skill** | 独立创建输出（skill） | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |
