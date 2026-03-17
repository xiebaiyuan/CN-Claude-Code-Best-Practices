# 大型 Monorepo 中的 Claude Skills 发现机制

在 monorepo 中使用 Claude Code 时，理解技能如何被发现并加载到上下文，对组织项目能力非常关键。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 与 CLAUDE.md 的重要区别

**Skills 的加载行为不同于 CLAUDE.md。** CLAUDE.md 会向上遍历（祖先加载），而 skills 使用基于嵌套目录的不同发现机制。

## Skills 的发现方式

### 1. 标准技能位置

按作用域，skills 从以下固定位置加载：

| 位置 | 路径 | 适用范围 |
|----------|------|------------|
| Enterprise | Managed settings | 组织内所有用户 |
| Personal | `~/.claude/skills/<skill-name>/SKILL.md` | 你的所有项目 |
| Project | `.claude/skills/<skill-name>/SKILL.md` | 仅该项目 |
| Plugin | `<plugin>/skills/<skill-name>/SKILL.md` | 启用该插件时 |

### 2. 从嵌套目录自动发现

当你在子目录处理文件时，Claude Code 会自动发现该目录下的 `.claude/skills/`。例如编辑 `packages/frontend/` 下的文件时，也会查找 `packages/frontend/.claude/skills/`。

这非常适合 monorepo 中各包独立维护技能。

## Monorepo 结构示例

假设 monorepo 有多个包：

```
/mymonorepo/
├── .claude/
│   └── skills/
│       └── shared-conventions/SKILL.md    # 项目级 skill
├── packages/
│   ├── frontend/
│   │   ├── .claude/
│   │   │   └── skills/
│   │   │       └── react-patterns/SKILL.md  # 前端专属 skill
│   │   └── src/
│   │       └── App.tsx
│   ├── backend/
│   │   ├── .claude/
│   │   │   └── skills/
│   │   │       └── api-design/SKILL.md      # 后端专属 skill
│   │   └── src/
│   └── shared/
│       ├── .claude/
│       │   └── skills/
│       │       └── utils-patterns/SKILL.md  # 共享工具 skill
│       └── src/
```

## 场景 1：从根目录启动（尚未编辑文件）

在 `/mymonorepo/` 启动 Claude Code 且尚未编辑任何文件：

```bash
cd /mymonorepo
claude
# 刚启动 - 尚未编辑文件
```

| Skill | 是否在上下文中？ | 原因 |
|-------|-------------|--------|
| `shared-conventions` | **是** | 根目录 `.claude/skills/` 的项目级 skill |
| `react-patterns` | **否** | 尚未处理 `packages/frontend/` 的文件，未发现 |
| `api-design` | **否** | 尚未处理 `packages/backend/` 的文件，未发现 |
| `utils-patterns` | **否** | 尚未处理 `packages/shared/` 的文件，未发现 |

## 场景 2：编辑某个包内文件之后

当你让 Claude 编辑 `packages/frontend/src/App.tsx` 之后：

| Skill | 是否在上下文中？ | 原因 |
|-------|-------------|--------|
| `shared-conventions` | **是** | 根目录 skill 始终可用 |
| `react-patterns` | **是** | 编辑 `packages/frontend/` 文件时被发现 |
| `api-design` | **否** | 仍未处理 `packages/backend/` 文件 |
| `utils-patterns` | **否** | 仍未处理 `packages/shared/` 文件 |

**关键点**：嵌套 skills 是 **按需发现**，不是启动时预加载。

## 关键行为：描述 vs 全文

Skill 描述会加载到上下文（在字符预算内），但 **完整内容仅在调用时加载**。这是重要优化：

- **描述**：始终在上下文（受字符预算限制）
- **完整内容**：仅在调用时按需加载

> 注意：预加载到 subagent 的 skills 不同 —— 完整内容会在启动时注入。

## 优先级（同名 skill）

当不同层级出现同名 skill 时，高优先级覆盖低优先级：

| 优先级 | 位置 | 作用域 |
|----------|----------|-------|
| 1（最高） | Enterprise | 组织级 |
| 2 | Personal（`~/.claude/skills/`） | 所有项目 |
| 3（最低） | Project（`.claude/skills/`） | 当前项目 |

Plugin skills 使用 `plugin-name:skill-name` 命名空间，因此不会冲突。

## 为什么适合 Monorepo

- **包级技能隔离**：前端开发者只会看到前端 skill，不会被后端污染
- **自动发现减少配置**：无需显式注册包级技能
- **上下文优化**：描述先加载，内容按需加载
- **团队自治**：各包团队可维护自己的技能

## 字符预算注意事项

Skill 描述会加载到上下文（默认上限 15,000 字符）。在大型 monorepo 中可能触发上限。

- 运行 `/context` 查看是否有 skills 被排除的提示
- 设置 `SLASH_COMMAND_TOOL_CHAR_BUDGET` 环境变量以提高上限

## 最佳实践

1. **共享流程放根目录 `.claude/skills/`** —— 全局约定、提交流程、共享模式

2. **包级技能放包目录 `.claude/skills/`** —— 框架模式、组件约定、测试工具等

3. **危险技能使用 `disable-model-invocation: true`** —— 部署或破坏性操作必须显式调用

4. **描述简洁** —— 描述会常驻上下文，避免浪费字符预算

5. **命名空间** —— 适当用包名作为前缀（如 `frontend-review`、`backend-deploy`）

## 对比：Skills vs CLAUDE.md 加载

| 行为 | CLAUDE.md | Skills |
|----------|-----------|--------|
| 祖先加载（向上） | 是 | 否 |
| 后代发现（向下） | 是（lazy） | 是（自动发现） |
| 全局位置 | `~/.claude/CLAUDE.md` | `~/.claude/skills/` |
| 项目位置 | `.claude/` 或仓库根 | `.claude/skills/` |
| 内容加载 | 完整内容 | 仅描述（调用时加载全文） |

---

## Sources

- [Claude Code Documentation - Extend Claude with Skills](https://code.claude.com/docs/en/skills)
- [Claude Code Documentation - Automatic Discovery from Nested Directories](https://code.claude.com/docs/en/skills#automatic-discovery-from-nested-directories)
