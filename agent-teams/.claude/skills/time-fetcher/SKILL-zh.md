---
name: time-fetcher
description: Instructions for fetching current Dubai time via bash command
user-invocable: false
---

## 迪拜时间获取器

### 命令

```bash
TZ='Asia/Dubai' date '+%Y-%m-%d %H:%M:%S %Z'
```

### 预期输出格式

`YYYY-MM-DD HH:MM:SS +04`（Gulf Standard Time）

### 时区详情

- 时区：Asia/Dubai
- 偏移：UTC+4
- 缩写：GST（Gulf Standard Time）
- 迪拜不使用夏令时

### 返回格式

请输出以下字段：
- `time`: 仅时间部分（HH:MM:SS）
- `timezone`: "GST (UTC+4)"
- `formatted`: 命令的完整输出字符串
