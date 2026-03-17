# Verification Checklist — README CONCEPTS Section

用于验证 CONCEPTS 表准确性的规则。每次 workflow 运行都必须检查。

## 规则

### 1. 外部 URL 可用性
- **Category**：URL Accuracy
- **检查内容**：CONCEPTS 表中所有外部链接有效
- **Depth**：请求 URL，确认页面正确（不跳转到错误页面）
- **对照来源**：`https://code.claude.com/docs/llms.txt`
- **添加日期**：2026-03-02
- **起因**：`/iam` 权限链接跳到 Authentication 页面

### 2. Anchor 片段有效性
- **Category**：URL Accuracy
- **检查内容**：所有带 `#anchor` 的链接是否指向真实标题
- **Depth**：抓取页面并验证 anchor 存在
- **对照来源**：目标页面内容
- **添加日期**：2026-03-02
- **起因**：`#modular-rules-with-clauderules` 变更为 `#organize-rules-with-clauderules`

### 3. 缺失文档页面
- **Category**：Missing Concepts
- **检查内容**：官方 docs 索引中的用户功能页都应在 CONCEPTS 表有对应行
- **Depth**：对比 docs 索引与 CONCEPTS 表
- **对照来源**：`https://code.claude.com/docs/llms.txt`
- **添加日期**：2026-03-02
- **起因**：发现多个缺失概念（Agent Teams、Keybindings、Model Config 等）

### 4. 本地 Badge 链接有效性
- **Category**：Badge Accuracy
- **检查内容**：CONCEPTS 表 badge 路径必须存在
- **Depth**：检查文件是否存在
- **对照来源**：本地文件系统
- **添加日期**：2026-03-02
- **起因**：初始清单建立

### 5. 描述准确性
- **Category**：Description Accuracy
- **检查内容**：CONCEPTS 描述与官方页面描述一致
- **Depth**：对比 README 描述与官方页面 meta/首段
- **对照来源**：官方 docs 页面
- **添加日期**：2026-03-02
- **起因**：Memory 描述缺少 auto memory；MCP Servers 未包含 `.mcp.json`
