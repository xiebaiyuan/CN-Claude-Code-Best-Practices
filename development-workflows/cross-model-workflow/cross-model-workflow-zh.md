# 跨模型工作流（Claude Code + Codex）

基于 [claude-code-best-practice](https://github.com/shanraisshan/claude-code-best-practice) 与 [codex-cli-best-practice](https://github.com/shanraisshan/codex-cli-best-practice)

## Workflow

```
┌─────────────────────────────────────────────────────────────────────────┐
│              CROSS-MODEL CLAUDE CODE + CODEX WORKFLOW                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  STEP 1: PLAN                                          Claude Code      │
│  ─────────────                                         Opus 4.6         │
│  在终端 1 以 Plan 模式打开 Claude Code。                     Plan Mode        │
│  Claude 通过 AskUserQuestion 访谈你。                           │
│  产出带测试门的分阶段计划。                                │
│                                                                         │
│  输出：plans/{feature-name}.md                                        │
│                                                                         │
│                              ▼                                          │
│                                                                         │
│  STEP 2: QA REVIEW                                     Codex CLI        │
│  ──────────────────                                    GPT-5.4          │
│  在另一终端（终端 2）打开 Codex CLI。                         │
│  Codex 对照实际代码库评审计划。                        │
│  插入中间阶段（"Phase 2.5"）                            │
│  并以 “Codex Finding” 标题标注。                         │
│  只补充计划，不重写原阶段。                              │
│                                                                         │
│  输出：plans/{feature-name}.md（更新）                              │
│                                                                         │
│                              ▼                                          │
│                                                                         │
│  STEP 3: IMPLEMENT                                     Claude Code      │
│  ──────────────────                                    Opus 4.6         │
│  在终端 1 开一个新 Claude Code 会话。                          │
│  逐阶段实现，并在每个阶段设置测试门。                          │
│                                                                         │
│                              ▼                                          │
│                                                                         │
│  STEP 4: VERIFY                                        Codex CLI        │
│  ────────────────                                      GPT-5.4          │
│  在终端 2 启动新的 Codex CLI 会话。                            │
│  Codex 对照计划验证实现。                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

## 生产中跨模型工作流的真实样子

![Cross-Model Workflow](assets/cross-model-workflow.png)

*Last Updated: 2026-03-06*
