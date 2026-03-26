# 第 09 篇：配置详解 —— config.json 每个参数的含义

> 💡 **学习时长**：20 分钟 | **难度**：⭐⭐ 入门 | **阶段**：安装与部署

---

## 🎯 本篇你将学到

- config.json 的完整结构和层次
- 每个配置区块的含义和用法
- 常用配置项的推荐值
- 高级配置选项详解
- 多环境配置管理技巧
- 配置最佳实践和常见错误

---

## 1. 为什么配置很重要？

想象一下这个场景：

```
❌ 配置不当的后果：
你：（随便填了几个配置）
    启动 OpenClaw...
    ❌ API Key 错误，无法调用模型
    ❌ 端口冲突，Gateway 启动失败
    ❌ 工具权限太松，存在安全风险
    ❌ 日志级别太高，磁盘被占满

✅ 正确配置的好处：
你：（仔细阅读本篇，理解每个参数）
    配置合理，运行稳定
    性能优化，响应快速
    安全可控，隐私保护
    易于维护，方便调试
```

**配置是 OpenClaw 的"神经系统"**，决定了它如何工作、如何思考、如何与你交互。

---

## 2. config.json 完整结构

### 2.1 配置文件位置

```bash
# 默认配置文件位置
~/.openclaw/config.json

# 查看配置
cat ~/.openclaw/config.json

# 编辑配置
nano ~/.openclaw/config.json
```

### 2.2 完整配置示例

这是一个完整的配置示例，包含了所有可用选项：

```json
{
  "gateway": {
    "port": 3000,
    "host": "localhost",
    "debug": false,
    "cors": true
  },
  "model": {
    "provider": "bailian",
    "model": "qwen-plus",
    "apiKey": "sk-xxxxxxxxxxxxxxxx",
    "contextLength": 8192,
    "temperature": 0.7,
    "maxTokens": 4096,
    "timeout": 60
  },
  "channels": [
    {
      "type": "feishu",
      "appId": "cli_xxxxxxxxxx",
      "appSecret": "xxxxxxxxxxxxxxxx",
      "enabled": true,
      "name": "飞书主渠道"
    }
  ],
  "workspace": {
    "path": "~/openclaw/workspace",
    "allowWrite": true,
    "maxFileSize": 10485760,
    "allowedPaths": ["~/openclaw/workspace"]
  },
  "tools": {
    "allow": ["read", "write", "web_search", "message", "browser"],
    "deny": ["exec"],
    "execSecurity": "allowlist"
  },
  "memory": {
    "enabled": true,
    "path": "~/openclaw/workspace/memory",
    "maxSnippets": 10,
    "minScore": 0.5
  },
  "logging": {
    "level": "info",
    "path": "~/.openclaw/logs",
    "maxFiles": 7,
    "maxSize": "10MB"
  },
  "security": {
    "requireApproval": ["exec", "browser"],
    "allowlist": ["ls", "cat", "grep"],
    "rateLimit": {
      "enabled": true,
      "maxRequests": 100,
      "windowMs": 60000
    }
  }
}
```

---

## 3. 配置项详解

### 3.1 gateway 区块 —— 网关配置

控制 Gateway 服务器的基本行为。

```json
"gateway": {
  "port": 3000,
  "host": "localhost",
  "debug": false,
  "cors": true
}
```

| 参数 | 类型 | 默认值 | 说明 | 推荐值 |
|------|------|--------|------|--------|
| `port` | number | 3000 | Gateway 监听端口 | 3000（如被占用可改） |
| `host` | string | localhost | 监听地址 | localhost（本地）/ 0.0.0.0（公开） |
| `debug` | boolean | false | 调试模式 | false（生产）/ true（开发） |
| `cors` | boolean | true | 允许跨域请求 | true（需要 Web UI 时） |

**使用场景：**

```json
// 场景 1：本地个人使用（推荐）
"gateway": {
  "port": 3000,
  "host": "localhost",
  "debug": false
}

// 场景 2：开发调试
"gateway": {
  "port": 3000,
  "host": "localhost",
  "debug": true  // 输出详细日志
}

// 场景 3：服务器部署（可被外部访问）
"gateway": {
  "port": 3000,
  "host": "0.0.0.0",  // 监听所有网卡
  "debug": false
}
```

**⚠️ 注意事项：**
- 端口被占用时，修改 `port` 为其他值（如 3001、8080）
- `host: "0.0.0.0"` 会让 Gateway 可被外部访问，注意防火墙设置
- 生产环境不要开启 `debug`，会泄露敏感信息

---

### 3.2 model 区块 —— AI 模型配置

配置使用的 AI 模型和 API 参数。

```json
"model": {
  "provider": "bailian",
  "model": "qwen-plus",
  "apiKey": "sk-xxxxxxxxxxxxxxxx",
  "contextLength": 8192,
  "temperature": 0.7,
  "maxTokens": 4096,
  "timeout": 60
}
```

| 参数 | 类型 | 默认值 | 说明 | 推荐值 |
|------|------|--------|------|--------|
| `provider` | string | - | 模型提供商 | bailian/anthropic/openai/ollama |
| `model` | string | - | 模型名称 | qwen-plus/claude-3/gpt-4 |
| `apiKey` | string | - | API 密钥 | 从对应平台获取 |
| `contextLength` | number | 8192 | 上下文长度（tokens） | 4096-32768 |
| `temperature` | number | 0.7 | 创造性（0-1） | 0.5（严谨）- 0.8（创意） |
| `maxTokens` | number | 4096 | 最大输出长度 | 2048-8192 |
| `timeout` | number | 60 | 请求超时（秒） | 30-120 |

**不同提供商配置示例：**

```json
// 阿里云（通义千问）- 推荐国内用户
"model": {
  "provider": "bailian",
  "model": "qwen-plus",
  "apiKey": "sk-xxxxxxxxxxxxxxxx",
  "contextLength": 8192,
  "temperature": 0.7
}

// Anthropic（Claude）- 需要海外账号
"model": {
  "provider": "anthropic",
  "model": "claude-3-sonnet-20240229",
  "apiKey": "sk-ant-xxxxxxxxxxxxxxxx",
  "contextLength": 16384,
  "temperature": 0.5
}

// OpenAI（GPT）- 需要海外账号
"model": {
  "provider": "openai",
  "model": "gpt-4-turbo-preview",
  "apiKey": "sk-proj-xxxxxxxxxxxxxxxx",
  "contextLength": 8192,
  "temperature": 0.7
}

// 本地模型（Ollama）- 免费但需要硬件
"model": {
  "provider": "ollama",
  "model": "qwen2.5:7b",
  "apiKey": "",  // 本地模型不需要
  "contextLength": 4096,
  "temperature": 0.7
}
```

**temperature 参数详解：**

```
temperature = 0.0 → 最严谨，几乎确定性输出
temperature = 0.5 → 平衡，适合一般任务
temperature = 0.7 → 有创意，适合写作、头脑风暴
temperature = 1.0 → 最随机，可能产生意外结果

推荐：
- 代码生成：0.2-0.4
- 文档写作：0.5-0.7
- 创意写作：0.7-0.9
- 数据分析：0.1-0.3
```

---

### 3.3 channels 区块 —— 消息渠道配置

配置消息收发渠道，可以配置多个。

```json
"channels": [
  {
    "type": "feishu",
    "appId": "cli_xxxxxxxxxx",
    "appSecret": "xxxxxxxxxxxxxxxx",
    "enabled": true,
    "name": "飞书主渠道"
  },
  {
    "type": "telegram",
    "botToken": "123456:ABC-DEF1234...",
    "enabled": false,
    "name": "Telegram 备用"
  }
]
```

**飞书 (Lark) 配置：**

```json
{
  "type": "feishu",
  "appId": "cli_a1b2c3d4e5f6",
  "appSecret": "xxxxxxxxxxxxxxxx",
  "enabled": true,
  "name": "飞书主渠道",
  "encryptKey": "",  // 可选，启用加密时填写
  "verificationToken": ""  // 可选，验证用
}
```

**Telegram 配置：**

```json
{
  "type": "telegram",
  "botToken": "123456:ABC-DEF1234ghijklmnop",
  "enabled": true,
  "name": "Telegram 机器人",
  "pollingInterval": 1000  // 轮询间隔（毫秒）
}
```

**Discord 配置：**

```json
{
  "type": "discord",
  "botToken": "xxxxxxxxxxxxxxxxxxxx",
  "enabled": true,
  "name": "Discord 机器人",
  "guildId": "123456789...",  // 服务器 ID
  "channelId": "987654321..."  // 频道 ID
}
```

| 参数 | 类型 | 说明 | 必填 |
|------|------|------|------|
| `type` | string | 渠道类型 | 是 |
| `enabled` | boolean | 是否启用 | 是 |
| `name` | string | 渠道名称（便于识别） | 否 |
| 其他参数 | varies | 根据 type 不同而不同 | 是 |

---

### 3.4 workspace 区块 —— 工作区配置

配置 Agent 可以访问的文件目录。

```json
"workspace": {
  "path": "~/openclaw/workspace",
  "allowWrite": true,
  "maxFileSize": 10485760,
  "allowedPaths": ["~/openclaw/workspace"]
}
```

| 参数 | 类型 | 默认值 | 说明 | 推荐值 |
|------|------|--------|------|--------|
| `path` | string | ~/openclaw/workspace | 主工作区路径 | 根据实际设置 |
| `allowWrite` | boolean | true | 允许写入文件 | true（需要写文件时） |
| `maxFileSize` | number | 10485760 | 最大文件大小（字节） | 10MB-50MB |
| `allowedPaths` | array | [path] | 允许访问的路径列表 | 明确指定 |

**安全建议：**

```json
// ✅ 推荐：限制访问范围
"workspace": {
  "path": "~/openclaw/workspace",
  "allowWrite": true,
  "maxFileSize": 10485760,
  "allowedPaths": [
    "~/openclaw/workspace",
    "~/openclaw/documents"
  ]
}

// ❌ 不推荐：允许访问整个家目录
"workspace": {
  "path": "~",
  "allowedPaths": ["~"]  // 太宽泛，有风险
}
```

---

### 3.5 tools 区块 —— 工具权限配置

控制 Agent 可以使用哪些工具。

```json
"tools": {
  "allow": ["read", "write", "web_search", "message", "browser"],
  "deny": ["exec"],
  "execSecurity": "allowlist"
}
```

| 参数 | 类型 | 说明 | 推荐值 |
|------|------|------|--------|
| `allow` | array | 允许使用的工具列表 | 根据需求 |
| `deny` | array | 禁止使用的工具列表 | exec（如不需要） |
| `execSecurity` | string | exec 工具安全模式 | allowlist/deny/full |

**可用工具列表：**

```
📁 文件操作：read, write, edit
🔍 信息获取：web_search, web_fetch
🌐 浏览器：browser
💬 消息通信：message
📅 日程任务：calendar, task
🔧 系统控制：exec, process
🧠 记忆系统：memory_search, memory_get
🤖 Agent 系统：sessions_spawn, subagents
```

**安全配置示例：**

```json
// 场景 1：个人使用（较宽松）
"tools": {
  "allow": ["read", "write", "web_search", "message", "exec"],
  "deny": [],
  "execSecurity": "allowlist"
}

// 场景 2：生产环境（严格）
"tools": {
  "allow": ["read", "write", "web_search", "message"],
  "deny": ["exec", "browser"],  // 禁止执行命令和浏览器
  "execSecurity": "deny"
}

// 场景 3：开发调试（完全开放）
"tools": {
  "allow": ["*"],  // 允许所有工具
  "deny": [],
  "execSecurity": "full"  // ⚠️ 仅限可信环境
}
```

**execSecurity 模式说明：**

```
deny → 完全禁止 exec 命令执行
allowlist → 只允许白名单中的命令（推荐）
full → 允许任意命令（危险！）
```

---

### 3.6 memory 区块 —— 记忆系统配置

配置长期记忆功能。

```json
"memory": {
  "enabled": true,
  "path": "~/openclaw/workspace/memory",
  "maxSnippets": 10,
  "minScore": 0.5
}
```

| 参数 | 类型 | 默认值 | 说明 | 推荐值 |
|------|------|--------|------|--------|
| `enabled` | boolean | true | 是否启用记忆 | true |
| `path` | string | ~/openclaw/workspace/memory | 记忆文件路径 | 默认即可 |
| `maxSnippets` | number | 10 | 检索返回的最大片段数 | 5-15 |
| `minScore` | number | 0.5 | 最小相关度分数 | 0.4-0.7 |

---

### 3.7 logging 区块 —— 日志配置

配置日志输出行为。

```json
"logging": {
  "level": "info",
  "path": "~/.openclaw/logs",
  "maxFiles": 7,
  "maxSize": "10MB"
}
```

| 参数 | 类型 | 默认值 | 说明 | 推荐值 |
|------|------|--------|------|--------|
| `level` | string | info | 日志级别 | info/error/debug |
| `path` | string | ~/.openclaw/logs | 日志目录 | 默认即可 |
| `maxFiles` | number | 7 | 保留的最大文件数 | 7-30 |
| `maxSize` | string | 10MB | 单个文件最大大小 | 10MB-100MB |

**日志级别说明：**

```
error → 只记录错误（生产环境推荐）
warn  → 错误 + 警告
info  → 错误 + 警告 + 一般信息（默认推荐）
debug → 所有信息（调试时使用）
```

---

### 3.8 security 区块 —— 安全配置

配置安全相关选项。

```json
"security": {
  "requireApproval": ["exec", "browser"],
  "allowlist": ["ls", "cat", "grep", "git status"],
  "rateLimit": {
    "enabled": true,
    "maxRequests": 100,
    "windowMs": 60000
  }
}
```

| 参数 | 类型 | 说明 | 推荐值 |
|------|------|------|--------|
| `requireApproval` | array | 需要用户确认的工具 | ["exec", "browser"] |
| `allowlist` | array | exec 命令白名单 | 常用安全命令 |
| `rateLimit.enabled` | boolean | 是否启用速率限制 | true |
| `rateLimit.maxRequests` | number | 时间窗口内最大请求数 | 50-200 |
| `rateLimit.windowMs` | number | 时间窗口（毫秒） | 60000（1 分钟） |

---

## 4. 多环境配置管理

### 4.1 为什么需要多环境？

```
开发环境 → 调试模式、详细日志、本地模型
测试环境 → 接近生产、中等日志、测试 API
生产环境 → 严格安全、错误日志、稳定模型
```

### 4.2 配置文件组织

```
~/.openclaw/
├── config.json          # 主配置（默认）
├── config.dev.json      # 开发环境
├── config.test.json     # 测试环境
└── config.prod.json     # 生产环境
```

### 4.3 使用不同配置

```bash
# 使用开发配置启动
openclaw gateway start --config ~/.openclaw/config.dev.json

# 使用生产配置启动
openclaw gateway start --config ~/.openclaw/config.prod.json

# 或者设置环境变量
export OPENCLAW_CONFIG=~/.openclaw/config.prod.json
openclaw gateway start
```

### 4.4 环境配置示例

**开发环境 (config.dev.json)：**

```json
{
  "gateway": {
    "port": 3000,
    "debug": true
  },
  "model": {
    "provider": "ollama",
    "model": "qwen2.5:7b"
  },
  "logging": {
    "level": "debug"
  },
  "tools": {
    "allow": ["*"],
    "execSecurity": "full"
  }
}
```

**生产环境 (config.prod.json)：**

```json
{
  "gateway": {
    "port": 3000,
    "debug": false
  },
  "model": {
    "provider": "bailian",
    "model": "qwen-plus"
  },
  "logging": {
    "level": "error"
  },
  "tools": {
    "allow": ["read", "write", "web_search", "message"],
    "deny": ["exec"],
    "execSecurity": "deny"
  },
  "security": {
    "rateLimit": {
      "enabled": true,
      "maxRequests": 50
    }
  }
}
```

---

## 5. 配置最佳实践

### 5.1 安全实践

```json
// ✅ 推荐做法
{
  "tools": {
    "deny": ["exec"],  // 不需要就禁止
  },
  "workspace": {
    "allowedPaths": ["~/openclaw/workspace"]  // 限制访问范围
  },
  "security": {
    "requireApproval": ["exec", "browser"]  // 敏感操作需确认
  }
}
```

### 5.2 性能优化

```json
// ✅ 推荐做法
{
  "model": {
    "contextLength": 8192,  // 根据需求设置，不要过大
    "maxTokens": 2048,      // 限制输出长度
    "timeout": 30           // 合理超时时间
  },
  "logging": {
    "maxFiles": 7,          // 定期清理日志
    "maxSize": "10MB"
  }
}
```

### 5.3 可维护性

```json
// ✅ 推荐做法
{
  "channels": [
    {
      "type": "feishu",
      "name": "飞书主渠道",  // 有意义的名称
      "enabled": true
    }
  ],
  // 使用注释说明配置用途（JSON 不支持注释，可在旁边创建 README）
}
```

---

## 6. 常见配置错误

### 6.1 JSON 格式错误

```json
// ❌ 错误：缺少逗号
{
  "gateway": {
    "port": 3000
    "host": "localhost"
  }
}

// ✅ 正确
{
  "gateway": {
    "port": 3000,
    "host": "localhost"
  }
}
```

**验证 JSON 格式：**

```bash
# 使用 Python 验证
cat ~/.openclaw/config.json | python -m json.tool

# 使用 jq 验证
jq . ~/.openclaw/config.json
```

### 6.2 路径错误

```json
// ❌ 错误：路径不存在
"workspace": {
  "path": "/nonexistent/path"
}

// ✅ 正确：先创建目录
mkdir -p ~/openclaw/workspace
```

### 6.3 API Key 错误

```json
// ❌ 错误：占位符未替换
"model": {
  "apiKey": "your-api-key-here"
}

// ✅ 正确：使用真实 Key
"model": {
  "apiKey": "sk-xxxxxxxxxxxxxxxx"
}
```

### 6.4 端口冲突

```json
// ❌ 错误：端口被占用
"gateway": {
  "port": 3000  // 如果 3000 已被占用
}

// ✅ 正确：换其他端口
"gateway": {
  "port": 3001
}
```

---

## 7. 配置检查清单

```
┌─────────────────────────────────────────────────────────┐
│              配置检查清单 ✅                             │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  基础配置                                               │
│  □ JSON 格式正确（使用 python/jq 验证）                 │
│  □ gateway.port 未被占用                                │
│  □ model.apiKey 已填写真实值                            │
│  □ model.provider 和 model 匹配                         │
│                                                         │
│  渠道配置                                               │
│  □ 至少配置 1 个渠道                                     │
│  □ 渠道凭证（Token/Secret）正确                         │
│  □ 渠道 enabled: true                                   │
│                                                         │
│  安全配置                                               │
│  □ tools.allow/deny 合理设置                            │
│  □ workspace.allowedPaths 限制范围                      │
│  □ 敏感工具需要审批                                     │
│                                                         │
│  日志配置                                               │
│  □ logging.level 合适（生产用 info/error）              │
│  □ 日志目录有写入权限                                   │
│                                                         │
│  验证测试                                               │
│  □ openclaw gateway start 成功                         │
│  □ 能收到消息                                           │
│  □ 能发送回复                                           │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 8. 本篇总结

### 📌 核心要点

1. **gateway** 控制服务器行为（端口、主机、调试）
2. **model** 配置 AI 模型（提供商、模型名、API Key、参数）
3. **channels** 配置消息渠道（可配置多个）
4. **workspace** 限制文件访问范围（安全重要）
5. **tools** 控制工具权限（allow/deny 列表）
6. **logging** 管理日志输出（级别、大小、数量）
7. **security** 配置安全选项（审批、白名单、限流）

### ✅ 学完这篇你应该能：

- [ ] 理解 config.json 的完整结构
- [ ] 解释每个主要配置区块的作用
- [ ] 根据需求配置合适的参数
- [ ] 管理多环境配置
- [ ] 验证 JSON 格式正确性
- [ ] 排查常见配置错误

---

## 📚 下一篇预告

**第 10 篇：连接你的第一个 Channel —— Telegram/WhatsApp/Discord/飞书**

我们会：
- 详细配置每种消息渠道
- 获取必要的凭证和 Token
- 测试消息收发
- 排查渠道连接问题
- 多渠道管理技巧

---

## 🤔 思考题

1. 你打算使用哪个 AI 模型？为什么？
2. 你的工作区路径设置在哪里？是否安全？
3. 你禁用了哪些工具？为什么？
4. 你的日志级别设置是什么？适合你的场景吗？

---

## 📋 配置完成度自评

```
完成以下任务后打勾：

□ 阅读完整篇教程
□ 理解每个配置区块的作用
□ 编辑了 ~/.openclaw/config.json
□ 填写了真实的 API Key
□ 配置了至少 1 个消息渠道
□ 验证了 JSON 格式正确
□ 成功启动了 Gateway

完成度：___/7

7/7：完美！可以进入第 10 篇
5-6/7：良好，继续完成剩余配置
4/7 以下：建议重新阅读本篇，完成配置
```

---

## 🔧 配置命令速查

```bash
# 编辑配置
nano ~/.openclaw/config.json

# 验证 JSON 格式
cat ~/.openclaw/config.json | python -m json.tool

# 使用指定配置启动
openclaw gateway start --config ~/.openclaw/config.prod.json

# 查看当前配置
openclaw config get

# 重启 Gateway（应用新配置）
openclaw gateway restart
```

---

**🎉 恭喜！你已经掌握了 OpenClaw 的配置方法！**

---

*📅 创建时间：2026-03-26 08:00*  
*📂 文件位置：`tutorials/openclaw-series/09-config-detailed-guide.md`*
