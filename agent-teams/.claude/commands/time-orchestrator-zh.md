---
model: haiku
---

# Time Orchestrator Command

获取迪拜当前时间（Asia/Dubai，UTC+4）并创建 SVG 时间卡片。

## Workflow

### Step 1: 获取迪拜当前时间

使用 Agent 工具调用 time-agent：
- subagent_type: time-agent
- description: Fetch current Dubai time
- prompt: Fetch the current time for Dubai (Asia/Dubai, UTC+4). Return exactly three fields: `time` (the time portion, e.g. "14:30:45"), `timezone` ("GST (UTC+4)"), and `formatted` (full formatted string, e.g. "2026-03-12 14:30:45 +04"). The agent has a preloaded skill (time-fetcher) that provides the detailed instructions.
- model: haiku

等待 agent 完成并获取返回的时间数据。

### Data Contract

time-agent 必须返回以下三个字段：
- **time**: 时间部分（例如 "14:30:45"）
- **timezone**: "GST (UTC+4)"
- **formatted**: 完整格式字符串（例如 "2026-03-12 14:30:45 +04"）

### Step 2: 创建 SVG 时间卡片

使用 Skill 工具调用 time-svg-creator：
- skill: time-svg-creator
- args: 传入 Step 1 的时间数据 —— 包含 `time`、`timezone`、`formatted`

该技能会使用 Step 1 的时间数据（当前上下文中可用）创建 SVG 卡片并写入输出文件。

## Critical Requirements

1. **对 time-agent 使用 Agent 工具**：不得用 bash 调用 agent，必须使用 Agent 工具，`subagent_type: "time-agent"`。
2. **对 SVG Creator 使用 Skill 工具**：必须通过 Skill 工具调用 `skill: "time-svg-creator"`，不可用 Agent 工具。
3. **顺序流程**：必须先等待 agent 完成并返回时间数据，再调用 skill，禁止并行。
4. **数据传递**：调用 skill 时确保上下文包含 time、timezone、formatted 三个字段。

## Output Summary

两步完成后向用户输出清晰摘要：
- 获取到的迪拜当前时间
- Timezone: GST (UTC+4)
- 完整时间戳
- SVG 卡片位置：`agent-teams/output/dubai-time.svg`
- 摘要写入：`agent-teams/output/output.md`
