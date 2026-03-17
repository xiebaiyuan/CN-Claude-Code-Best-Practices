# Commands 实现

![Last Updated](https://img.shields.io/badge/Last_Updated-Mar_02%2C_2026-white?style=flat&labelColor=555)

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

<a href="#weather-orchestrator"><img src="../!/tags/implemented-hd.svg" alt="Implemented"></a>

天气编排命令作为 **Command → Agent → Skill** 架构模式的入口点在本仓库实现，用于演示命令如何编排多步骤工作流。

---

## Weather Orchestrator

**File**: [`.claude/commands/weather-orchestrator.md`](../.claude/commands/weather-orchestrator.md)

```yaml
---
description: Fetch weather data for Dubai and create an SVG weather card
model: haiku
---

# Weather Orchestrator Command

Fetch the current temperature for Dubai, UAE and create a visual SVG weather card.

## Workflow

### Step 1: Ask User Preference
Use the AskUserQuestion tool to ask the user whether they want the temperature
in Celsius or Fahrenheit.

### Step 2: Fetch Weather Data
Use the Agent tool to invoke the weather agent:
- subagent_type: weather-agent
- prompt: Fetch the current temperature for Dubai, UAE in [unit]...

### Step 3: Create SVG Weather Card
Use the Skill tool to invoke the weather-svg-creator skill:
- skill: weather-svg-creator

...
```

该命令编排完整流程：询问用户温度单位偏好，通过 Agent 工具调用 `weather-agent`，再通过 Skill 工具调用 `weather-svg-creator`。

---

## ![How to Use](../!/tags/how-to-use.svg)

```bash
$ claude
> /weather-orchestrator
```

---

## ![How to Implement](../!/tags/how-to-implement.svg)

可以让 Claude 为你创建命令 —— 它会在 `.claude/commands/<name>.md` 生成带 YAML frontmatter 的 markdown 文件。

---

<a href="https://github.com/shanraisshan/claude-code-best-practice#orchestration-workflow"><img src="../!/tags/orchestration-workflow-hd.svg" alt="Orchestration Workflow"></a>

天气编排命令是 Command → Agent → Skill 模式中的 **Command**。它负责入口与用户交互（温度单位），将数据获取委托给 `weather-agent`，再调用 `weather-svg-creator` 产出可视化。

<p align="center">
  <img src="../orchestration-workflow/orchestration-workflow.svg" alt="Command Skill Agent Architecture Flow" width="100%">
</p>

| 组件 | 角色 | 本仓库 |
|-----------|------|-----------|
| **Command** | 入口、用户交互 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **Agent** | 使用预加载技能获取数据（agent skill） | [`weather-agent`](../.claude/agents/weather-agent.md) with [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **Skill** | 独立创建输出（skill） | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |
