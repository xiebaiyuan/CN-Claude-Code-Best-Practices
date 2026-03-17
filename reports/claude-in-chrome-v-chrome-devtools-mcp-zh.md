# 浏览器自动化 MCP 全面对比报告

<table width="100%">
<tr>
<td><a href="../">← 返回 Claude Code 最佳实践</a></td>
<td align="right"><img src="../!/claude-jumping.svg" alt="Claude" width="60" /></td>
</tr>
</table>

## 执行摘要

基于深入研究，以下对比了两款截图中的工具以及第三个主要备选方案，帮助你选择自动化测试的最佳方案。

---

## 1. 三个候选方案

### **A. Chrome DevTools MCP**（你的截图 #1）
- **来源**：Google Chrome 官方团队
- **发布时间**：2025 年 9 月公测
- **架构**：基于 Chrome DevTools Protocol（CDP）+ Puppeteer
- **Token 用量**：约 19.0k tokens（9.5% 上下文）
- **工具数量**：26 个工具，6 类别

### **B. Claude in Chrome**（你的截图 #2）
- **来源**：Anthropic 官方扩展
- **发布时间**：Beta，逐步向付费计划（Pro/Max/Team/Enterprise）开放
- **架构**：浏览器扩展 + 计算机使用能力
- **Token 用量**：约 15.4k tokens（7.7% 上下文）
- **工具数量**：16 个工具，含计算机使用能力

### **C. Playwright MCP**（强力备选）
- **来源**：Microsoft（官方 + 社区实现）
- **架构**：基于可访问性树的自动化
- **Token 用量**：约 13.7k tokens（6.8% 上下文）
- **工具数量**：21 个工具

---

## 2. 关键功能对比

| 特性 | Chrome DevTools MCP | Claude in Chrome | Playwright MCP |
|---------|---------------------|------------------|----------------|
| **主要用途** | 调试与性能 | 通用浏览器自动化 | UI 测试与 E2E |
| **浏览器支持** | 仅 Chrome | 仅 Chrome | Chromium、Firefox、WebKit |
| **Token 效率** | 19.0k（9.5%） | 15.4k（7.7%） | 13.7k（6.8%） |
| **元素选择** | CSS/XPath | 视觉 + DOM | 可访问性树（语义） |
| **性能分析** | ✅ 强 | ❌ 无 | ⚠️ 有限 |
| **网络分析** | ✅ 深度 | ⚠️ 基础 | ⚠️ 基础 |
| **控制台日志** | ✅ 完整 | ✅ 完整 | ⚠️ 有限 |
| **跨浏览器** | ❌ | ❌ | ✅ |
| **CI/CD 集成** | ✅ 强 | ❌ 弱（需登录态） | ✅ 强 |
| **无头模式** | ✅ | ❌ | ✅ |
| **认证** | 需要配置 | 复用现有登录态 | 需要配置 |
| **定时任务** | ❌ | ✅ | ❌ |
| **成本** | 免费 | 需付费计划 | 免费 |
| **本地配置** | 需 Node.js | 浏览器扩展 | 需 Node.js |

---

## 3. 工具拆解

### Chrome DevTools MCP（26 工具）

```
INPUT AUTOMATION (8):     click, drag, fill, fill_form, handle_dialog,
                          hover, press_key, upload_file

NAVIGATION (6):           close_page, list_pages, navigate_page,
                          new_page, select_page, wait_for

EMULATION (2):            emulate, resize_page

PERFORMANCE (3):          performance_analyze_insight,
                          performance_start_trace, performance_stop_trace

NETWORK (2):              get_network_request, list_network_requests

DEBUGGING (5):            evaluate_script, get_console_message,
                          list_console_messages, take_screenshot,
                          take_snapshot
```

### Claude in Chrome（16 工具）

```
BROWSER CONTROL:          navigate, read_page, find, computer
                          (click, type, scroll)

FORM INTERACTION:         form_input, javascript_tool

MEDIA:                    upload_image, get_page_text, gif_creator

TAB MANAGEMENT:           tabs_context_mcp, tabs_create_mcp

DEVELOPMENT:              read_console_messages, read_network_requests

UTILITIES:                shortcuts_list, shortcuts_execute,
                          resize_window, update_plan
```

### Playwright MCP（21 工具）

```
NAVIGATION:               navigate, goBack, goForward, reload

INTERACTION:              click, fill, select, hover, press,
                          drag, uploadFile

ELEMENT QUERIES:          getElement, getElements, waitForSelector

ASSERTIONS:               assertVisible, assertText, assertTitle

PAGE STATE:               screenshot, getAccessibilityTree,
                          evaluateScript

BROWSER MGMT:             newPage, closePage
```

---

## 4. 自动化测试场景分析

### **Chrome DevTools MCP 最适合：**

✅ **性能测试**
- 记录 Core Web Vitals
- 定位渲染瓶颈与布局抖动
- 内存泄漏与 CPU 分析

✅ **深度调试**
- 网络请求分析（headers/payload/timing）
- 控制台错误与堆栈
- 实时 DOM 检查

✅ **CI/CD**
- 无头执行
- 稳定脚本化自动化
- 不依赖登录态

**理想用途**：“这个页面为什么慢？”或“调试某个 API 调用”。

---

### **Claude in Chrome 最适合：**

✅ **辅助人工测试**
- 使用你的登录态测试
- 视觉上下文探索
- 记录并重放流程

✅ **快速验证**
- UI 设计对比（如 Figma）
- 功能快速验收
- 读取开发过程控制台错误

✅ **周期性任务**
- 定时检查
- 多 Tab 协作
- 学习你操作的流程

**理想用途**：“看看我改动的页面对不对”或“用我账号测试表单”。

---

### **Playwright MCP 最适合：**

✅ **E2E 自动化**
- 跨浏览器测试（Chrome/Firefox/Safari）
- 生成可复用测试脚本
- Page Object Model

✅ **可靠 UI 测试**
- 可访问性树减少选择器抖动
- 交互更确定
- 更不易因 UI 变动而坏

✅ **CI/CD 集成**
- 无头模式
- 从自然语言生成 Playwright 测试文件
- 可集成测试管理工具

**理想用途**：“为这条用户流程写 E2E 测试”或“跨浏览器测试”。

---

## 5. Token 效率分析

| 工具 | Token 用量 | 上下文占比 | 效率评级 |
|------|-------------|--------------|-------------------|
| Playwright MCP | ~13.7k | 6.8% | ⭐⭐⭐⭐⭐ 最优 |
| Claude in Chrome | ~15.4k | 7.7% | ⭐⭐⭐⭐ 良好 |
| Chrome DevTools MCP | ~19.0k | 9.5% | ⭐⭐⭐ 可接受 |

**影响：** 以 200k 上下文为例：
- Playwright 剩余 186.3k tokens
- Claude in Chrome 剩余 184.6k tokens
- Chrome DevTools 剩余 181k tokens

Playwright 与 DevTools 的差距约 5.3k tokens，对复杂会话有影响。

---

## 6. 安全考量

### Chrome DevTools MCP
- ✅ 默认隔离浏览器 profile
- ✅ 无云依赖
- ✅ 完全本地控制
- ⚠️ 远程调试端口安全（需隔离 profile）

### Claude in Chrome
- ⚠️ **无缓解时 23.6% 攻击成功率**（防护后降至 11.2%）
- ⚠️ 使用真实浏览器会话（cookie 暴露风险）
- ⚠️ 禁止金融/成人/盗版站点
- ⚠️ Beta 阶段仍有已知漏洞

### Playwright MCP
- ✅ 隔离浏览器上下文
- ✅ 无云依赖
- ✅ 安全模型成熟（微软背书）
- ✅ 可安全处理认证

---

## 7. 安装命令

### Chrome DevTools MCP

```bash
claude mcp add chrome-devtools npx chrome-devtools-mcp@latest
```

### Claude in Chrome

```
从 Chrome Web Store 安装（需 Pro/Max/Team/Enterprise 计划）
```

### Playwright MCP（推荐）

```bash
# 先安装浏览器
npx playwright install

# 添加到 Claude Code（用户级 = 所有项目）
claude mcp add playwright -s user -- npx @playwright/mcp@latest
```

---

## 8. 推荐方案

### **你的自动化测试流程：**

#### 🥇 **主力工具：Playwright MCP**

**适用于**：日常 E2E、跨浏览器验证、生成测试脚本

**理由：**
- token 最省，留给代码更多上下文
- 跨浏览器支持
- 可访问性树选择器更稳定
- CI/CD 集成强
- 可生成真实 Playwright 测试文件
- 免费

#### 🥈 **辅工具：Chrome DevTools MCP**

**适用于**：性能调试、网络分析、Core Web Vitals

**理由：**
- 性能分析无可替代
- 深度网络请求分析
- Google 官方支持
- 适合回答“为什么慢？”

#### 🥉 **场景工具：Claude in Chrome**

**适用于**：登录态下的快速人工验证、探索性测试、设计对齐

**理由：**
- 视觉检查快速
- 使用你的登录态
- 适合“看起来对不对？”
- 不适合 CI/CD 或严肃自动化

---

## 9. 推荐安装组合

```bash
# 同时安装 Playwright 与 Chrome DevTools MCP
npx playwright install
claude mcp add playwright -s user -- npx @playwright/mcp@latest
claude mcp add chrome-devtools -s user -- npx chrome-devtools-mcp@latest
```

### 推荐工作流

```
1. DEVELOP      → Claude Code (terminal)
2. TEST         → Playwright MCP (E2E, cross-browser)
3. DEBUG        → Chrome DevTools MCP (performance, network)
4. VERIFY       → Claude in Chrome (quick visual checks)
5. CI/CD        → Playwright MCP (headless, automated)
```

---

## 10. 最终结论

| 需求 | 推荐 |
|----------------|----------|
| 跨浏览器 E2E | **Playwright MCP** |
| 性能分析 | **Chrome DevTools MCP** |
| 网络调试 | **Chrome DevTools MCP** |
| 快速视觉验证 | **Claude in Chrome** |
| CI/CD 自动化 | **Playwright MCP** |
| 测试脚本生成 | **Playwright MCP** |
| 最低 token 消耗 | **Playwright MCP** |
| 登录态测试 | **Claude in Chrome** |
| 控制台调试 | **Chrome DevTools MCP** |

### **TL;DR 推荐：**

**同时安装 Playwright MCP 与 Chrome DevTools MCP。** Playwright 作为主要测试工具（更省 token、跨浏览器、E2E 更优），Chrome DevTools 用于深度性能/网络调试。Claude in Chrome 仅用于登录态下的快速人工验证。

---

## Sources

- [Chrome DevTools MCP - GitHub](https://github.com/ChromeDevTools/chrome-devtools-mcp)
- [Anthropic - Piloting Claude in Chrome](https://claude.com/blog/claude-for-chrome)
- [Claude in Chrome Help Center](https://support.claude.com/en/articles/12012173-getting-started-with-claude-in-chrome)
- [Playwright MCP - GitHub](https://github.com/microsoft/playwright-mcp)
- [Simon Willison - Using Playwright MCP with Claude Code](https://til.simonwillison.net/claude-code/playwright-mcp-claude-code)
- [Testomat.io - Playwright MCP Claude Code](https://testomat.io/blog/playwright-mcp-claude-code/)
- [MCP Integration Guide - Scrapeless](https://www.scrapeless.com/en/blog/mcp-integration-guide)
- [Chrome DevTools MCP Guide - Vladimir Siedykh](https://vladimirsiedykh.com/blog/chrome-devtools-mcp-ai-browser-debugging-complete-guide-2025)
- [Addy Osmani - Give your AI eyes](https://addyosmani.com/blog/devtools-mcp/)

---

*This report was generated by Claude Code using the Opus 4.5 model on December 19, 2025.*
