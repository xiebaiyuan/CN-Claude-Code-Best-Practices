---
name: time-svg-creator
description: Creates an SVG time card showing the current time for Dubai. Writes the SVG to agent-teams/output/dubai-time.svg and updates agent-teams/output/output.md.
allowed-tools: Write, Read
---

# Time SVG Creator Skill

创建迪拜时间 SVG 卡片并写入输出文件。

## Task

你将从调用上下文接收 `time`、`timezone`、`formatted` 三个字段。请创建 SVG 时间卡片，并写入 SVG 与 markdown 摘要。

## Instructions

1. **创建 SVG** — 使用 [reference.md](reference.md) 中的 SVG 模板并替换占位符
2. **写入 SVG 文件** — 写入 `agent-teams/output/dubai-time.svg`
3. **写入摘要** — 使用 [reference.md](reference.md) 中的 markdown 模板写入 `agent-teams/output/output.md`

## Rules

- 使用提供的精确时间值 —— 不要重新获取或重算
- SVG 必须自包含且有效
- 两个输出文件均写入 `agent-teams/output/` 目录

## Additional resources

- SVG 模板、输出模板与设计规范见 [reference.md](reference.md)
- 输入输出示例见 [examples.md](examples.md)
