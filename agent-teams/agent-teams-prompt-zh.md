# Agent Teams 提示词

创建一个 agent team，用于构建一个时间编排工作流，以 SVG 卡片展示迪拜当前时间。该工作流遵循
Command → Agent → Skill 架构模式：

- 命令负责编排流程并处理用户交互
- agent 使用预加载技能获取迪拜实时当前时间
- skill 使用获取到的数据创建 SVG 时间卡片

**重要**：所有文件必须创建在 `agent-teams/.claude/` 中 ——
不要放到仓库根目录的 `.claude/`。这样 agent team 的产物即可自包含，
并可通过 `cd agent-teams && claude` 运行。
不要引用或复制已有的天气流程 —— 必须从零搭建。

指派以下队友：

1. **Command Architect** —— 设计并实现 `/time-orchestrator`
   命令，文件为 `agent-teams/.claude/commands/time-orchestrator.md`。命令要求：
    - 通过 Agent 工具调用 time-agent（不是 bash）获取迪拜当前时间
      （Asia/Dubai 时区，UTC+4）
    - 通过 Skill 工具调用 time-svg-creator 渲染 SVG 卡片
    - frontmatter 使用 model: haiku
    - 包含关键要求：顺序流程、正确工具使用
      （Agent 用于 agents，Skill 用于 skills）、输出总结
    通过共享任务列表与其他队友协调数据契约（{time, timezone, formatted}）。

2. **Agent Engineer** —— 设计并实现 `time-agent`
   （`agent-teams/.claude/agents/time-agent.md`）及其预加载的 `time-fetcher`
   技能（`agent-teams/.claude/skills/time-fetcher/SKILL.md`）。agent 要求：
    - 使用 Bash 获取迪拜当前时间（Asia/Dubai，UTC+4）：
      `TZ='Asia/Dubai' date '+%Y-%m-%d %H:%M:%S %Z'`
    - 返回 time、timezone、formatted 三个字段给命令
    - frontmatter：tools（Bash）、model: haiku、color: blue、maxTurns: 3
    - 通过 `skills:` 预加载 time-fetcher
   time-fetcher 技能需要包含获取时间的 bash 命令、预期输出格式，
   并设置 user-invocable: false（仅 agent 内部知识）。
   将协商好的数据契约写入共享任务列表，方便 Command Architect
   与 Skill Designer 对齐接口。

3. **Skill Designer** —— 设计并实现 `time-svg-creator`
   技能（`agent-teams/.claude/skills/time-svg-creator/SKILL.md`），并提供
   `reference.md`（SVG 模板 + 输出模板）与 `examples.md`
   （输入/输出示例）。技能要求：
    - 从上下文接收 time、timezone、formatted
    - 创建自包含 SVG 时间卡片（展示迪拜当前时间）
    - 将 SVG 写入 `agent-teams/output/dubai-time.svg`
    - 将摘要写入 `agent-teams/output/output.md`
    - 使用提供的精确时间 —— 不要重新获取
    - 模板放在 reference.md，示例放在 examples.md
   同时创建 `agent-teams/output/` 目录用于输出文件。

所有三位队友需要创建共享任务列表并统一数据契约：agent 返回
{time, timezone, formatted}，命令透传，skill 消费。
三人可并行开始，因为组件彼此独立 —— 只需对齐数据接口，无需等待实现。
