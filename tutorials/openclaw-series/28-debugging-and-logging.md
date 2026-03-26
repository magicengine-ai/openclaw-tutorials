# 第 28 篇：调试与日志 —— 排查问题的技巧

> 💡 **学习时长**：30 分钟 | **难度**：⭐⭐⭐ 中等 | **阶段**：高级应用

---

## 🎯 本篇你将学到

- 日志系统配置
- 日志分析方法
- 常见问题排查
- 调试工具使用
- 问题定位技巧

---

## 1. 调试与日志总览

### 1.1 为什么需要日志？

```
┌─────────────────────────────────────────────────────────┐
│              日志的价值                                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  没有日志的困境：                                       │
│  ❌ 出错了不知道原因                                    │
│  ❌ 问题无法复现                                        │
│  ❌ 排查靠猜                                            │
│  ❌ 浪费大量时间                                        │
│                                                         │
│  有日志的好处：                                         │
│  ✅ 快速定位问题                                        │
│  ✅ 追溯问题根源                                        │
│  ✅ 监控系统状态                                        │
│  ✅ 优化系统性能                                        │
│                                                         │
│  日志的作用：                                           │
│  • 记录系统运行状态                                     │
│  • 记录用户操作                                         │
│  • 记录错误和异常                                       │
│  • 辅助问题排查                                         │
│  • 安全审计                                             │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 1.2 日志级别

| 级别 | 描述 | 使用场景 | 示例 |
|------|------|----------|------|
| **ERROR** | 错误 | 系统错误、操作失败 | API 调用失败、文件不存在 |
| **WARN** | 警告 | 潜在问题、不影响运行 | 配置缺失、性能下降 |
| **INFO** | 信息 | 正常操作、重要事件 | 用户登录、任务完成 |
| **DEBUG** | 调试 | 开发调试、详细信息 | 函数调用、参数值 |
| **TRACE** | 追踪 | 最详细信息 | 每步执行细节 |

### 1.3 日志系统架构

```
┌─────────────────────────────────────────────────────────┐
│              OpenClaw 日志系统架构                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │              日志收集层                          │   │
│  │  • 应用日志                                     │   │
│  │  • 系统日志                                     │   │
│  │  • 审计日志                                     │   │
│  └─────────────────────────────────────────────────┘   │
│                        │                                │
│                        ▼                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │              日志处理层                          │   │
│  │  • 格式化                                       │   │
│  │  • 过滤                                         │   │
│  │  • 脱敏                                         │   │
│  └─────────────────────────────────────────────────┘   │
│                        │                                │
│                        ▼                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │              日志存储层                          │   │
│  │  • 文件存储                                     │   │
│  │  • 日志轮转                                     │   │
│  │  • 压缩归档                                     │   │
│  └─────────────────────────────────────────────────┘   │
│                        │                                │
│                        ▼                                │
│  ┌─────────────────────────────────────────────────┐   │
│  │              日志分析层                          │   │
│  │  • 搜索查询                                     │   │
│  │  • 统计分析                                     │   │
│  │  • 告警通知                                     │   │
│  └─────────────────────────────────────────────────┘   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 2. 日志系统配置

### 2.1 基础配置

```json
// ~/.openclaw/config.json

{
  "logging": {
    "level": "info",
    "path": "~/.openclaw/logs",
    "format": "json",
    "includeTimestamp": true,
    "includeLevel": true,
    "includeSource": true,
    "maxFiles": 30,
    "maxSize": "100MB",
    "compress": true
  }
}
```

### 2.2 日志级别配置

```json
{
  "logging": {
    "level": "info",
    "levels": {
      "default": "info",
      "modules": {
        "gateway": "debug",
        "api": "info",
        "tools": "warn",
        "security": "info"
      }
    },
    "overrides": {
      "verbose": false,
      "quiet": false
    }
  }
}
```

### 2.3 日志轮转配置

```json
{
  "logging": {
    "rotation": {
      "enabled": true,
      "strategy": "size",
      "maxSize": "100MB",
      "maxFiles": 30,
      "compress": true,
      "compressDelay": 86400
    },
    "cleanup": {
      "enabled": true,
      "maxAge": "30d",
      "schedule": "0 2 * * *"
    }
  }
}
```

### 2.4 日志格式配置

```json
{
  "logging": {
    "format": "json",
    "formats": {
      "json": {
        "timestampFormat": "ISO8601",
        "includeStack": true,
        "prettyPrint": false
      },
      "text": {
        "pattern": "%timestamp% [%level%] %message%",
        "dateFormat": "YYYY-MM-DD HH:mm:ss"
      }
    }
  }
}
```

---

## 3. 日志分析方法

### 3.1 查看日志

```bash
# 查看最近的日志
openclaw logs

# 查看指定数量的日志
openclaw logs --tail 100

# 查看特定级别的日志
openclaw logs --level error
openclaw logs --level warn

# 实时查看日志
openclaw logs --follow

# 查看特定时间范围的日志
openclaw logs --since "2026-03-26 10:00"
openclaw logs --until "2026-03-26 12:00"
```

### 3.2 搜索日志

```bash
# 搜索包含关键词的日志
openclaw logs --grep "error"
openclaw logs --grep "timeout"

# 搜索特定模块的日志
openclaw logs --module gateway
openclaw logs --module tools

# 组合搜索
openclaw logs --level error --grep "API" --since "1h"
```

### 3.3 日志分析工具

```bash
# 使用 jq 分析 JSON 日志
cat ~/.openclaw/logs/*.json | jq '.level' | sort | uniq -c

# 统计错误数量
grep -c "ERROR" ~/.openclaw/logs/*.log

# 查看最频繁的错误
grep "ERROR" ~/.openclaw/logs/*.log | cut -d':' -f3 | sort | uniq -c | sort -rn | head -10

# 查看响应时间分布
grep "response_time" ~/.openclaw/logs/*.log | awk '{print $NF}' | sort -n | uniq -c
```

### 3.4 日志可视化

```bash
# 生成日志统计报告
openclaw logs report --output report.html

# 生成错误趋势图
openclaw logs chart --type error --period 7d

# 导出日志数据
openclaw logs export --format csv --output logs.csv
```

---

## 4. 常见问题排查

### 4.1 排查流程

```
┌─────────────────────────────────────────────────────────┐
│              问题排查流程                                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. 重现问题                                            │
│     • 确认问题现象                                     │
│     • 记录复现步骤                                     │
│     • 确定影响范围                                     │
│                                                         │
│  2. 收集信息                                            │
│     • 查看相关日志                                     │
│     • 收集系统信息                                     │
│     • 记录环境配置                                     │
│                                                         │
│  3. 定位原因                                            │
│     • 分析日志线索                                     │
│     • 缩小问题范围                                     │
│     • 确定根本原因                                     │
│                                                         │
│  4. 解决问题                                            │
│     • 制定解决方案                                     │
│     • 实施修复                                         │
│     • 验证效果                                         │
│                                                         │
│  5. 总结预防                                            │
│     • 记录问题报告                                     │
│     • 更新文档                                         │
│     • 添加监控告警                                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 4.2 常见问题及排查

#### 问题 1：Gateway 启动失败

```
症状：
• openclaw gateway start 后无响应
• 端口无法访问

排查步骤：

1. 查看启动日志
   openclaw logs --since "5m" --level error

2. 检查端口占用
   lsof -i :3000
   netstat -tlnp | grep 3000

3. 检查配置文件
   openclaw config validate
   cat ~/.openclaw/config.json | python -m json.tool

4. 检查权限
   ls -la ~/.openclaw/

5. 查看详细错误
   journalctl -u openclaw -n 50
```

#### 问题 2：API 调用失败

```
症状：
• 工具调用超时
• 返回错误信息

排查步骤：

1. 查看 API 相关日志
   openclaw logs --grep "API" --since "1h"

2. 检查 API Key
   echo $OPENCLAW_API_KEY
   openclaw config get model.apiKey

3. 测试网络连接
   curl -I https://api.example.com

4. 检查配额限制
   openclaw usage check

5. 查看错误详情
   openclaw logs --grep "401\|403\|429"
```

#### 问题 3：消息发送失败

```
症状：
• 消息无法发送
• 渠道无响应

排查步骤：

1. 查看渠道状态
   openclaw channels list
   openclaw channels check feishu

2. 查看消息日志
   openclaw logs --grep "message\|channel"

3. 检查渠道配置
   openclaw config get channels

4. 测试渠道连接
   openclaw channels test feishu --message "测试"

5. 查看错误详情
   openclaw logs --level error --grep "send\|deliver"
```

#### 问题 4：性能下降

```
症状：
• 响应变慢
• 资源占用高

排查步骤：

1. 查看性能日志
   openclaw performance report

2. 检查资源使用
   top
   free -h
   df -h

3. 查看慢请求
   openclaw logs --slow --since "1h"

4. 检查并发情况
   openclaw performance concurrency

5. 分析瓶颈
   openclaw performance profile --duration 60
```

### 4.3 问题排查速查表

| 问题类型 | 首先检查 | 关键日志 | 常用命令 |
|----------|----------|----------|----------|
| **启动失败** | 端口、配置 | error 级别 | logs --level error |
| **API 失败** | API Key、网络 | API 相关 | logs --grep API |
| **消息失败** | 渠道状态 | channel 相关 | channels check |
| **性能问题** | 资源使用 | 慢请求 | performance report |
| **权限问题** | 用户权限 | auth 相关 | logs --grep auth |
| **文件问题** | 文件权限 | file 相关 | logs --grep file |

---

## 5. 调试工具使用

### 5.1 调试模式

```bash
# 启用调试模式
openclaw debug enable

# 禁用调试模式
openclaw debug disable

# 查看调试状态
openclaw debug status

# 调试模式下运行
openclaw gateway run --debug
```

### 5.2 调试配置

```json
{
  "debug": {
    "enabled": false,
    "verbose": false,
    "stackTrace": true,
    "slowQueryThreshold": 1000,
    "logRequests": true,
    "logResponses": true
  }
}
```

### 5.3 会话调试

```bash
# 查看会话列表
openclaw sessions list

# 查看会话历史
openclaw sessions history --sessionKey xxx

# 查看会话详情
openclaw sessions get --sessionKey xxx

# 调试特定会话
openclaw sessions debug --sessionKey xxx
```

### 5.4 工具调试

```bash
# 列出可用工具
openclaw tools list

# 查看工具详情
openclaw tools get read

# 测试工具
openclaw tools test read --path ~/test.txt

# 查看工具调用历史
openclaw tools history --tool read
```

---

## 6. 问题定位技巧

### 6.1 日志分析技巧

```
┌─────────────────────────────────────────────────────────┐
│              日志分析技巧                                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. 时间线分析                                          │
│     • 按时间顺序查看事件                               │
│     • 找到问题发生的时间点                             │
│     • 关联前后事件                                     │
│                                                         │
│  2. 错误链追踪                                          │
│     • 从错误向上追踪                                   │
│     • 找到根本原因                                     │
│     • 注意级联错误                                     │
│                                                         │
│  3. 模式识别                                            │
│     • 识别重复出现的错误                               │
│     • 发现周期性规律                                   │
│     • 关联相关事件                                     │
│                                                         │
│  4. 对比分析                                            │
│     • 对比正常和异常日志                               │
│     • 对比不同时间段                                   │
│     • 对比不同环境                                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 6.2 调试技巧

```
┌─────────────────────────────────────────────────────────┐
│              调试技巧                                    │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  1. 分而治之                                            │
│     • 将大问题分解为小问题                             │
│     • 逐个排查                                         │
│     • 缩小问题范围                                     │
│                                                         │
│  2. 控制变量                                            │
│     • 一次只改变一个因素                               │
│     • 观察变化                                         │
│     • 确定因果关系                                     │
│                                                         │
│  3. 假设验证                                            │
│     • 提出可能原因                                     │
│     • 设计验证方法                                     │
│     • 证实或证伪                                       │
│                                                         │
│  4. 复现问题                                            │
│     • 尝试稳定复现                                     │
│     • 记录复现步骤                                     │
│     • 在测试环境验证                                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 6.3 实用命令组合

```bash
# 查找最近的错误
openclaw logs --level error --since "1h" | tail -20

# 统计每小时错误数
openclaw logs --level error --since "24h" | cut -d'T' -f1 | cut -d' ' -f2 | cut -d':' -f1 | sort | uniq -c

# 查找超时请求
openclaw logs --grep "timeout" --since "1h"

# 查找特定用户的操作
openclaw logs --grep "user@example.com" --since "1d"

# 分析响应时间
openclaw logs --grep "response_time" | awk '{sum+=$NF; count++} END {print "平均:", sum/count, "总数:", count}'
```

---

## 7. 调试实战

### 7.1 场景 1：用户无法登录

```
问题描述：
用户报告无法登录系统

排查过程：

1. 确认问题范围
   - 单个用户还是所有用户？
   - 特定渠道还是所有渠道？

2. 查看认证日志
   openclaw logs --grep "auth\|login" --since "1h"

3. 发现错误
   ERROR: Invalid credentials for user@example.com

4. 检查用户配置
   openclaw users get user@example.com

5. 验证凭证
   openclaw auth test --user user@example.com

6. 解决问题
   - 重置密码
   - 更新配置
   - 验证登录

7. 后续跟进
   - 添加登录失败告警
   - 更新用户文档
```

### 7.2 场景 2：定时任务不执行

```
问题描述：
配置的 Cron 任务到时间不执行

排查过程：

1. 检查任务状态
   openclaw cron list
   openclaw cron status --jobId xxx

2. 查看任务日志
   openclaw cron runs --jobId xxx
   openclaw logs --grep "cron\|schedule"

3. 检查配置
   openclaw config get cron

4. 验证 Cron 表达式
   openclaw cron validate --expr "0 8 * * *"

5. 检查时区
   openclaw config get timezone
   date

6. 手动触发测试
   openclaw cron run --jobId xxx

7. 解决问题
   - 修正 Cron 表达式
   - 更新时区配置
   - 重新启用任务
```

### 7.3 场景 3：内存泄漏

```
问题描述：
系统运行几天后内存占用持续增长

排查过程：

1. 确认问题
   free -h
   top -p $(pgrep openclaw)

2. 查看内存相关日志
   openclaw logs --grep "memory\|heap"

3. 分析会话
   openclaw sessions list
   openclaw sessions list --activeMinutes 1440

4. 检查缓存
   openclaw cache status
   openclaw cache stats

5. 生成内存报告
   openclaw performance memory --duration 300

6. 定位泄漏源
   - 长时间会话未清理
   - 缓存未设置上限
   - 文件句柄未释放

7. 解决问题
   - 配置会话超时
   - 限制缓存大小
   - 定期重启服务
```

---

## 8. 日志最佳实践

### 8.1 日志编写规范

```
┌─────────────────────────────────────────────────────────┐
│              日志编写规范                                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ✅ 推荐做法：                                          │
│                                                         │
│  1. 使用合适的级别                                      │
│     ERROR: 需要立即处理的错误                          │
│     WARN: 需要注意的警告                               │
│     INFO: 重要的操作事件                               │
│     DEBUG: 调试信息                                    │
│                                                         │
│  2. 包含上下文信息                                      │
│     • 用户 ID                                          │
│     • 请求 ID                                          │
│     • 时间戳                                           │
│     • 相关参数                                         │
│                                                         │
│  3. 使用结构化格式                                      │
│     • JSON 格式便于分析                                 │
│     • 统一字段名                                       │
│     • 包含必要元数据                                   │
│                                                         │
│  4. 敏感信息脱敏                                        │
│     • 密码不记录                                       │
│     • Key 脱敏显示                                     │
│     • 个人信息加密                                     │
│                                                         │
│  ❌ 避免做法：                                          │
│                                                         │
│  1. 记录过多细节                                        │
│  2. 记录敏感信息                                        │
│  3. 日志级别滥用                                        │
│  4. 无意义的日志                                        │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 8.2 日志管理策略

```json
{
  "logManagement": {
    "retention": {
      "error": "90d",
      "warn": "60d",
      "info": "30d",
      "debug": "7d"
    },
    "archival": {
      "enabled": true,
      "schedule": "0 2 * * 0",
      "compressAfter": "7d",
      "deleteAfter": "90d"
    },
    "alerting": {
      "errorRate": {
        "threshold": 10,
        "window": "5m"
      },
      "patterns": [
        "OutOfMemory",
        "ConnectionTimeout",
        "AuthenticationFailed"
      ]
    }
  }
}
```

---

## 9. 调试命令速查

```
┌─────────────────────────────────────────────────────────┐
│              调试命令速查表                              │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  日志查看                                               │
│  openclaw logs                     # 查看日志           │
│  openclaw logs --level error       # 查看错误日志       │
│  openclaw logs --follow            # 实时查看           │
│  openclaw logs --grep "xxx"        # 搜索日志           │
│                                                         │
│  调试工具                                               │
│  openclaw debug enable             # 启用调试           │
│  openclaw sessions list            # 查看会话           │
│  openclaw tools list               # 查看工具           │
│  openclaw performance report       # 性能报告           │
│                                                         │
│  问题排查                                               │
│  openclaw config validate          # 验证配置           │
│  openclaw channels check           # 检查渠道           │
│  openclaw cron status              # 检查定时任务       │
│  openclaw health check             # 健康检查           │
│                                                         │
│  系统信息                                               │
│  openclaw status                   # 系统状态           │
│  openclaw info                     # 详细信息           │
│  openclaw version                  # 版本信息           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 10. 本篇总结

### 📌 核心要点

1. **日志系统**：配置日志级别、格式、轮转、存储
2. **日志分析**：查看、搜索、统计、可视化
3. **问题排查**：遵循流程、使用工具、定位原因
4. **调试工具**：调试模式、会话调试、工具调试
5. **定位技巧**：时间线分析、错误链追踪、模式识别
6. **最佳实践**：日志规范、管理策略、安全脱敏

### ✅ 学完这篇你应该能：

- [ ] 配置和管理日志系统
- [ ] 使用各种方法分析日志
- [ ] 遵循流程排查常见问题
- [ ] 使用调试工具辅助排查
- [ ] 应用问题定位技巧
- [ ] 遵循日志最佳实践

---

## 📚 下一篇预告

**第 29 篇：最佳实践 —— 来自真实用户的经验**

我们会分享：
- 真实用户的使用经验
- 常见陷阱和避免方法
- 性能优化实战案例
- 安全配置最佳实践
- 团队协作经验

---

## 🤔 思考题

1. 你平时如何查看和分析 OpenClaw 日志？
2. 你遇到过最难排查的问题是什么？如何解决的？
3. 你的日志配置是否合理？需要优化吗？
4. 你打算如何改进你的问题排查流程？

---

## 📋 调试能力自评

```
完成以下任务后打勾：

□ 理解了日志系统架构
□ 配置了日志系统
□ 能使用各种方法分析日志
□ 掌握了问题排查流程
□ 能使用调试工具
□ 理解了定位技巧
□ 能遵循最佳实践

完成度：___/7

7/7：优秀！可以进入第 29 篇
5-6/7：良好，继续练习
4/7 以下：建议重新阅读本篇，多实践排查
```

---

**🎉 恭喜！你已经掌握了 OpenClaw 的调试与日志技巧！**

---

*📅 创建时间：2026-03-26 17:30*  
*📂 文件位置：`tutorials/openclaw-series/28-debugging-and-logging.md`*
