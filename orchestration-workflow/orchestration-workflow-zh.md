# 编排工作流

本文档描述 **Command → Agent（带技能）→ Skill** 的编排流程，通过天气数据获取与 SVG 渲染系统进行演示。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 系统概览

天气系统在单一编排流程中演示了两种不同的技能模式：
- **Agent Skills**（预加载）：`weather-fetcher` 在启动时注入 `weather-agent` 作为领域知识
- **Skills**（独立调用）：`weather-svg-creator` 由命令通过 Skill 工具直接调用

这展示了 **Command → Agent → Skill** 架构模式：
- 命令负责编排流程与用户交互
- agent 使用预加载技能获取数据
- skill 独立生成可视化输出

## 组件概览

| 组件 | 角色 | 示例 |
|-----------|------|---------|
| **Command** | 入口、用户交互 | [`/weather-orchestrator`](../.claude/commands/weather-orchestrator.md) |
| **Agent** | 使用预加载技能获取数据（agent skill） | [`weather-agent`](../.claude/agents/weather-agent.md) with [`weather-fetcher`](../.claude/skills/weather-fetcher/SKILL.md) |
| **Skill** | 独立生成输出（skill） | [`weather-svg-creator`](../.claude/skills/weather-svg-creator/SKILL.md) |

## 流程图

```
╔══════════════════════════════════════════════════════════════════╗
║              ORCHESTRATION WORKFLOW                              ║
║           Command  →  Agent  →  Skill                            ║
╚══════════════════════════════════════════════════════════════════╝

                         ┌───────────────────┐
                         │  User Interaction │
                         └─────────┬─────────┘
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  /weather-orchestrator — Command (Entry Point)      │
         └─────────────────────────┬───────────────────────────┘
                                   │
                              Step 1
                                   │
                                   ▼
                      ┌────────────────────────┐
                      │  AskUser — C° or F°?   │
                      └────────────┬───────────┘
                                   │
                         Step 2 — Agent tool
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-agent — Agent ● skill: weather-fetcher     │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          Returns: temp + unit
                                   │
                         Step 3 — Skill tool
                                   │
                                   ▼
         ┌─────────────────────────────────────────────────────┐
         │  weather-svg-creator — Skill ● SVG card + output    │
         └─────────────────────────┬───────────────────────────┘
                                   │
                          ┌────────┴────────┐
                          │                 │
                          ▼                 ▼
                   ┌────────────┐    ┌────────────┐
                   │weather.svg │    │ output.md  │
                   └────────────┘    └────────────┘
```

## 组件细节

### 1. Command

#### `/weather-orchestrator`（Command）
- **Location**: `.claude/commands/weather-orchestrator.md`
- **Purpose**: 入口 —— 编排工作流并处理用户交互
- **Actions**:
  1. 询问温度单位偏好（Celsius/Fahrenheit）
  2. 通过 Agent 工具调用 weather-agent
  3. 通过 Skill 工具调用 weather-svg-creator
- **Model**: haiku

### 2. 带预加载技能的 Agent（Agent Skill）

#### `weather-agent`（Agent）
- **Location**: `.claude/agents/weather-agent.md`
- **Purpose**: 使用预加载技能获取天气数据
- **Skills**: `weather-fetcher`（作为领域知识预加载）
- **Tools Available**: WebFetch, Read
- **Model**: sonnet
- **Color**: green
- **Memory**: project

该 agent 启动时将 `weather-fetcher` 注入上下文，按技能说明获取温度并将结果返回给命令。

### 3. Skill

#### `weather-svg-creator`（Skill）
- **Location**: `.claude/skills/weather-svg-creator/SKILL.md`
- **Purpose**: 生成 SVG 天气卡片并写入输出文件
- **Invocation**: 由命令通过 Skill 工具调用（不预加载到任何 agent）
- **Outputs**:
  - `orchestration-workflow/weather.svg` — SVG 天气卡片
  - `orchestration-workflow/output.md` — 天气摘要

### 4. 预加载技能

#### `weather-fetcher`（Skill）
- **Location**: `.claude/skills/weather-fetcher/SKILL.md`
- **Purpose**: 获取实时温度数据的说明
- **Data Source**: Open-Meteo API（Dubai, UAE）
- **Output**: 温度值与单位（Celsius/Fahrenheit）
- **Note**: 这是 agent skill —— 预加载到 `weather-agent`，不直接调用

## 执行流程

1. **用户调用**：用户运行 `/weather-orchestrator`
2. **用户输入**：命令询问温度单位（Celsius/Fahrenheit）
3. **Agent 调用**：命令通过 Agent 工具调用 `weather-agent`
4. **Skill 执行**（在 agent 上下文中）：
   - agent 按 `weather-fetcher` 指令从 Open-Meteo 获取温度
   - agent 将温度值与单位返回给命令
5. **SVG 生成**：命令通过 Skill 工具调用 `weather-svg-creator`
   - 生成 `orchestration-workflow/weather.svg`
   - 写入 `orchestration-workflow/output.md`
6. **结果展示**：向用户展示摘要：
   - 请求的温度单位
   - 获取到的温度
   - SVG 卡片位置
   - 输出文件位置

## 示例执行

```
Input: /weather-orchestrator
├─ Step 1: Asks: Celsius or Fahrenheit?
│  └─ User: Celsius
├─ Step 2: Agent tool → weather-agent
│  ├─ Preloaded Skill:
│  │  └─ weather-fetcher (domain knowledge)
│  ├─ Fetches from Open-Meteo → 26°C
│  └─ Returns: temperature=26, unit=Celsius
├─ Step 3: Skill tool → /weather-svg-creator
│  ├─ Creates: orchestration-workflow/weather.svg
│  └─ Writes: orchestration-workflow/output.md
└─ Output:
   ├─ Unit: Celsius
   ├─ Temperature: 26°C
   ├─ SVG: orchestration-workflow/weather.svg
   └─ Summary: orchestration-workflow/output.md
```

## 关键设计原则

1. **两种技能模式**：同时展示预加载 agent skill 与直接调用 skill
2. **命令负责编排**：命令处理用户交互并协调工作流
3. **Agent 负责取数**：agent 使用预加载技能获取数据并返回
4. **Skill 负责输出**：SVG 创建器独立运行，从命令上下文接收数据
5. **职责清晰**：获取（agent）→ 渲染（skill），每个组件单一职责

## 架构模式

### Agent Skill（预加载）

```yaml
# In agent definition (.claude/agents/weather-agent.md)
---
name: weather-agent
skills:
  - weather-fetcher    # Preloaded into agent context at startup
---
```

- **技能预加载**：完整技能内容在启动时注入 agent 上下文
- **agent 使用技能知识**：agent 按预加载技能指令执行
- **非动态调用**：技能是参考材料，不单独调用

### Skill（直接调用）

```yaml
# In skill definition (.claude/skills/weather-svg-creator/SKILL.md)
---
name: weather-svg-creator
description: Creates an SVG weather card...
---
```

- **通过 Skill 工具调用**：命令执行 `Skill(skill: "weather-svg-creator")`
- **独立执行**：在命令上下文中运行，不在 agent 内
- **从上下文取数据**：使用对话中已有的温度数据
