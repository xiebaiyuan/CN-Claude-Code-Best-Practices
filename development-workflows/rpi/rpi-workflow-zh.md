# RPI 工作流

**RPI** = **R**esearch → **P**lan → **I**mplement

一个在每个阶段都设置验证门的系统化开发工作流，防止在不可行的功能上浪费精力，并确保文档完整。

<table width="100%">
<tr>
<td><a href="../../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

---

## 概览

![RPI Workflow](rpi-workflow.svg)

---

## 安装

将包含 `agents/` 与 `commands/rpi/` 的 `.claude` 文件夹复制到你的仓库根目录，然后创建 `rpi/plans` 目录。

---

## 示例工作流

### 功能：用户认证

**Step 1: Describe**
```
User: "Add OAuth2 authentication with Google and GitHub providers"

1. Claude 生成计划
   → 输出：rpi/plans/oauth2-authentication.md
2. 创建功能目录：rpi/oauth2-authentication/
3. 将计划复制到功能目录
4. 重命名为 REQUEST.md
   → 最终：rpi/oauth2-authentication/REQUEST.md
```

**Step 2: Research**
```bash
/rpi:research rpi/oauth2-authentication/REQUEST.md
```
输出：
- `research/RESEARCH.md` 分析报告
- 结论：**GO**（可行且符合策略）

**Step 3: Plan**
```bash
/rpi:plan oauth2-authentication
```
输出：
- `plan/pm.md` - 用户故事与验收标准
- `plan/ux.md` - 登录 UI 流程
- `plan/eng.md` - 技术架构
- `plan/PLAN.md` - 3 个阶段、15 个任务

**Step 4: Implement**
```bash
/rpi:implement oauth2-authentication
```
进度：
- Phase 1: Backend Foundation → PASS
- Phase 2: Frontend Integration → PASS
- Phase 3: Testing & Polish → PASS

结果：功能完成，准备提 PR。

---

## 功能目录结构

所有功能相关工作都在 `rpi/{feature-slug}/` 下：

```
rpi/{feature-slug}/
├── REQUEST.md              # Step 1: 初始需求描述
├── research/
│   └── RESEARCH.md         # Step 2: GO/NO-GO 分析
├── plan/
│   ├── PLAN.md             # Step 3: 实施路线图
│   ├── pm.md               # 产品需求
│   ├── ux.md               # UX 设计
│   └── eng.md              # 技术规格
└── implement/
    └── IMPLEMENT.md        # Step 4: 实施记录
```

---

## Agents 与 Commands

| Command | 使用的 Agents |
|---------|-------------|
| `/rpi:research` | requirement-parser, product-manager, Explore, senior-software-engineer, technical-cto-advisor, documentation-analyst-writer |
| `/rpi:plan` | senior-software-engineer, product-manager, ux-designer, documentation-analyst-writer |
| `/rpi:implement` | Explore, senior-software-engineer, code-reviewer |
