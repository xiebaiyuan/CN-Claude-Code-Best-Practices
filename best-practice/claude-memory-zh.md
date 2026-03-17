# Claude Memory

通过 CLAUDE.md 文件提供持久上下文 —— 如何编写、以及在单仓库/大仓库中的加载方式。

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 1. 编写高质量的 CLAUDE.md

结构清晰的 CLAUDE.md 是提升 Claude Code 输出质量的最有效方式。Humanlayer 提供了很好的指南，涵盖应包含的内容、结构方式与常见陷阱。

- [Humanlayer - Writing a good Claude.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md)

---

## 2. 大型 Monorepo 中的 CLAUDE.md

在 monorepo 中使用 Claude Code 时，理解 CLAUDE.md 的加载方式对组织项目指令至关重要。

<p align="center">
  <a href="https://x.com/bcherny/status/2016339448863355206"><img src="assets/claude-memory/claude-memory-monorepo.jpg" alt="CLAUDE.md loading in monorepos" width="600"></a>
</p>

### 两种加载机制

Claude Code 通过两种机制加载 CLAUDE.md：

#### 祖先加载（向上遍历目录）

启动 Claude Code 时，会从当前工作目录 **向上** 逐级遍历到文件系统根目录，沿途找到的 CLAUDE.md 都会在 **启动时立即加载**。

#### 后代加载（向下遍历目录）

当前工作目录下的子目录中的 CLAUDE.md **不会在启动时加载**，只会在会话中读取这些子目录文件时 **按需加载**（lazy loading）。

### Monorepo 结构示例

典型 monorepo 结构示例：

```
/mymonorepo/
├── CLAUDE.md          # 根目录指令（全局共享）
├── frontend/
│   └── CLAUDE.md      # 前端专属指令
├── backend/
│   └── CLAUDE.md      # 后端专属指令
└── api/
    └── CLAUDE.md      # API 专属指令
```

### 场景 1：从根目录启动 Claude Code

在 `/mymonorepo/` 运行 Claude Code：

```bash
cd /mymonorepo
claude
```

| 文件 | 启动时加载？ | 原因 |
|------|-------------------|--------|
| `/mymonorepo/CLAUDE.md` | 是 | 当前工作目录 |
| `/mymonorepo/frontend/CLAUDE.md` | 否 | 仅在读取/编辑 `frontend/` 文件时加载 |
| `/mymonorepo/backend/CLAUDE.md` | 否 | 仅在读取/编辑 `backend/` 文件时加载 |
| `/mymonorepo/api/CLAUDE.md` | 否 | 仅在读取/编辑 `api/` 文件时加载 |

### 场景 2：从组件目录启动 Claude Code

在 `/mymonorepo/frontend/` 运行 Claude Code：

```bash
cd /mymonorepo/frontend
claude
```

| 文件 | 启动时加载？ | 原因 |
|------|-------------------|--------|
| `/mymonorepo/CLAUDE.md` | 是 | 祖先目录 |
| `/mymonorepo/frontend/CLAUDE.md` | 是 | 当前工作目录 |
| `/mymonorepo/backend/CLAUDE.md` | 否 | 不在同一目录分支 |
| `/mymonorepo/api/CLAUDE.md` | 否 | 不在同一目录分支 |

### 关键要点

1. **祖先目录总是在启动时加载** —— Claude 会向上遍历并加载所有祖先目录中的 CLAUDE.md，保证能获取仓库级别的通用约定。

2. **子目录按需加载** —— 仅在访问子目录文件时加载，避免无关上下文膨胀。

3. **兄弟目录不会加载** —— 若在 `frontend/` 中工作，不会加载 `backend/` 或 `api/` 的 CLAUDE.md。

4. **全局 CLAUDE.md** —— 也可在 `~/.claude/CLAUDE.md` 中放置全局指令，适用于所有 Claude Code 会话。

### 这种设计为何适合 Monorepo

- **共享指令自上而下传递** —— 根目录 CLAUDE.md 存放全局规范与约定，所有子目录都会受益。

- **组件指令保持隔离** —— 前端无需加载后端指令，避免上下文污染。

- **上下文更高效** —— 子目录 CLAUDE.md 延迟加载，防止启动时引入大量无关信息。

### 最佳实践

1. **全局约定放在根目录 CLAUDE.md** —— 编码规范、提交消息格式、PR 模板等跨项目约定。

2. **组件专属指令放在组件目录 CLAUDE.md** —— 框架模式、架构约定、该组件的测试规范。

3. **使用 CLAUDE.local.md 记录个人偏好** —— 并加入 `.gitignore`，避免与团队共享。

---

## Sources

- [Claude Code Documentation - How Claude Looks Up Memories](https://code.claude.com/docs/en/memory#how-claude-looks-up-memories)
- [Boris Cherny on X - Clarification on CLAUDE.md Loading](https://x.com/bcherny/status/2016339448863355206)
- [Humanlayer - Writing a good Claude.md](https://www.humanlayer.dev/blog/writing-a-good-claude-md)
