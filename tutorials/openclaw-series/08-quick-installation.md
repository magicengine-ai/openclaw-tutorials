# 第 08 篇：快速安装 —— 一行命令搞定

> 💡 **学习时长**：15 分钟 | **难度**：⭐ 入门 | **阶段**：安装与部署

---

## 🎯 本篇你将学到

- OpenClaw 的三种安装方式
- 一键安装脚本的使用方法
- 手动安装的详细步骤
- Docker 安装方法（可选）
- 验证安装是否成功
- 启动 Gateway 并进行第一次对话

---

## 1. 安装方式总览

OpenClaw 提供 **3 种安装方式**，你可以根据需求选择：

```
┌─────────────────────────────────────────────────────────┐
│              OpenClaw 安装方式对比                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  方式 1：一键安装脚本（推荐新手）                        │
│  ──────────────────────────────                         │
│  • 命令：一行搞定                                       │
│  • 难度：⭐ 简单                                        │
│  • 时间：5-10 分钟                                       │
│  • 适合：大多数用户                                     │
│                                                         │
│  方式 2：手动安装（推荐开发者）                          │
│  ──────────────────────────────                         │
│  • 命令：多步操作                                       │
│  • 难度：⭐⭐ 中等                                       │
│  • 时间：10-20 分钟                                     │
│  • 适合：想深入了解、自定义配置的用户                   │
│                                                         │
│  方式 3：Docker 安装（推荐高级用户）                     │
│  ──────────────────────────────                         │
│  • 命令：docker compose up                              │
│  • 难度：⭐⭐⭐ 较难                                      │
│  • 时间：10-15 分钟                                     │
│  • 适合：需要隔离环境、多实例部署的用户                 │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

**推荐选择：**
```
新手用户 → 方式 1（一键安装）
开发者 → 方式 2（手动安装）
运维/高级用户 → 方式 3（Docker）
```

---

## 2. 方式 1：一键安装脚本（推荐）

### 2.1 运行安装命令

打开终端，运行以下命令：

```bash
# 一键安装 OpenClaw
curl -fsSL https://openclaw.ai/install.sh | bash
```

**或者使用国内镜像（推荐国内用户）：**

```bash
# 使用国内镜像源
curl -fsSL https://clawhub.cn/install.sh | bash
```

### 2.2 安装过程详解

运行命令后，你会看到以下输出：

```
🦞 OpenClaw 安装脚本
====================

📦 检查系统环境...
   ✅ Node.js v20.11.0
   ✅ npm 10.2.4
   ✅ Git 2.34.1

📥 下载 OpenClaw...
   正在从 npm 安装 openclaw...
   [====================] 100%

🔧 配置初始化...
   创建配置目录：~/.openclaw
   创建默认配置：config.json
   创建工作区目录：~/openclaw/workspace

🎉 安装完成！

====================
下一步：
1. 编辑配置文件：nano ~/.openclaw/config.json
2. 启动 OpenClaw：openclaw gateway start
3. 查看状态：openclaw gateway status

文档：https://docs.openclaw.ai
```

### 2.3 验证安装

安装完成后，验证是否成功：

```bash
# 检查 OpenClaw 版本
openclaw --version

# 期望输出：openclaw v1.x.x

# 查看帮助
openclaw --help

# 期望输出：显示所有可用命令
```

### 2.4 安装目录结构

安装完成后，会在你的家目录创建以下结构：

```
~/.openclaw/                    # OpenClaw 主目录
├── config.json                # 主配置文件
├── logs/                      # 日志目录
│   ├── gateway.log           # Gateway 日志
│   └── sessions/             # 会话日志
├── workspace/                 # 默认工作区
│   ├── AGENTS.md            # Agent 配置
│   ├── SOUL.md              # 人格设定
│   ├── USER.md              # 用户信息
│   └── memory/              # 记忆目录
└── skills/                    # 技能目录
```

---

## 3. 方式 2：手动安装

如果你喜欢手动控制每一步，可以按照以下步骤：

### 3.1 克隆仓库

```bash
# 创建安装目录
mkdir -p ~/openclaw-src
cd ~/openclaw-src

# 克隆 OpenClaw 仓库
git clone https://github.com/openclaw/openclaw.git
cd openclaw

# 或者使用国内镜像（更快）
git clone https://gitee.com/openclaw/openclaw.git
cd openclaw
```

### 3.2 安装依赖

```bash
# 使用 npm 安装
npm install

# 或者使用 yarn
yarn install

# 或者使用 pnpm
pnpm install
```

### 3.3 全局安装

```bash
# 全局安装 openclaw 命令
npm install -g .

# 验证安装
openclaw --version
```

### 3.4 初始化配置

```bash
# 创建配置目录
mkdir -p ~/.openclaw

# 复制默认配置
cp config.example.json ~/.openclaw/config.json

# 创建工作区
mkdir -p ~/openclaw/workspace
```

---

## 4. 方式 3：Docker 安装

### 4.1 准备 Docker 环境

```bash
# 检查 Docker 是否安装
docker --version
docker compose --version

# 如果未安装，访问 https://docker.com 安装
```

### 4.2 创建 docker-compose.yml

```yaml
version: '3.8'

services:
  openclaw:
    image: openclaw/gateway:latest
    container_name: openclaw-gateway
    restart: unless-stopped
    volumes:
      - ./config:/app/config
      - ./workspace:/app/workspace
      - ./logs:/app/logs
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - OPENCLAW_CONFIG_PATH=/app/config/config.json
    networks:
      - openclaw-net

networks:
  openclaw-net:
    driver: bridge
```

### 4.3 启动容器

```bash
# 启动 OpenClaw
docker compose up -d

# 查看日志
docker compose logs -f

# 停止
docker compose down

# 重启
docker compose restart
```

---

## 5. 配置基础设置

安装完成后，需要编辑配置文件：

### 5.1 打开配置文件

```bash
# 使用 nano 编辑（推荐新手）
nano ~/.openclaw/config.json

# 或使用 vim
vim ~/.openclaw/config.json

# 或使用 VS Code
code ~/.openclaw/config.json
```

### 5.2 最小可用配置

```json
{
  "gateway": {
    "port": 3000,
    "host": "localhost"
  },
  "model": {
    "provider": "bailian",
    "model": "qwen-plus",
    "apiKey": "你的 API Key"
  },
  "channels": [
    {
      "type": "feishu",
      "appId": "你的 App ID",
      "appSecret": "你的 App Secret"
    }
  ],
  "workspace": {
    "path": "~/openclaw/workspace"
  }
}
```

### 5.3 配置项说明

| 配置项 | 含义 | 必填 | 示例 |
|--------|------|------|------|
| `gateway.port` | Gateway 监听端口 | 否 | 3000 |
| `model.provider` | AI 模型提供商 | 是 | bailian/anthropic/openai |
| `model.model` | 模型名称 | 是 | qwen-plus/claude-3/gpt-4 |
| `model.apiKey` | API 密钥 | 是 | sk-xxx/cli_xxx |
| `channels[].type` | 消息渠道类型 | 是 | feishu/telegram/discord |
| `workspace.path` | 工作区路径 | 否 | ~/openclaw/workspace |

### 5.4 完整配置示例

```json
{
  "gateway": {
    "port": 3000,
    "host": "0.0.0.0",
    "debug": false
  },
  "model": {
    "provider": "bailian",
    "model": "qwen-plus",
    "apiKey": "sk-xxxxxxxxxxxxxxxx",
    "contextLength": 8192,
    "temperature": 0.7
  },
  "channels": [
    {
      "type": "feishu",
      "appId": "cli_a1b2c3d4e5f6",
      "appSecret": "xxxxxxxxxxxxxxxx",
      "enabled": true
    },
    {
      "type": "telegram",
      "botToken": "123456:ABC-DEF1234...",
      "enabled": false
    }
  ],
  "workspace": {
    "path": "~/openclaw/workspace",
    "allowWrite": true,
    "maxFileSize": 10485760
  },
  "tools": {
    "allow": ["read", "write", "web_search", "message"],
    "deny": ["exec"]
  },
  "memory": {
    "enabled": true,
    "path": "~/openclaw/workspace/memory"
  },
  "logging": {
    "level": "info",
    "path": "~/.openclaw/logs"
  }
}
```

---

## 6. 启动 OpenClaw

### 6.1 启动 Gateway

```bash
# 启动 Gateway（后台运行）
openclaw gateway start

# 前台运行（可以看到实时日志）
openclaw gateway run

# 重启
openclaw gateway restart

# 停止
openclaw gateway stop
```

### 6.2 查看状态

```bash
# 查看 Gateway 状态
openclaw gateway status

# 期望输出：
# 🦞 OpenClaw Gateway
# Status: Running ✅
# PID: 12345
# Port: 3000
# Uptime: 2h 30m
```

### 6.3 查看日志

```bash
# 查看实时日志
openclaw logs

# 查看最近 100 行
openclaw logs --tail 100

# 查看错误日志
openclaw logs --level error
```

---

## 7. 第一次对话测试

### 7.1 通过命令行测试

```bash
# 发送测试消息
openclaw ask "你好，OpenClaw！"

# 期望输出：
# 🦞 OpenClaw: 你好！我是你的 AI 助手，有什么可以帮你的吗？
```

### 7.2 通过消息渠道测试

如果你配置了飞书/Telegram 等渠道：

```
步骤：
1. 在飞书/Telegram 中找到你的机器人
2. 发送消息："你好"
3. 等待回复（通常 1-3 秒）
4. 如果收到回复，说明配置成功！
```

**期望的对话：**

```
你：你好
OpenClaw：你好！我是你的 AI 助手 OpenClaw 🦞
         我已经准备好帮你了，有什么可以做的吗？

你：今天天气怎么样？
OpenClaw：让我帮你查一下...
         [搜索后]
         北京今天晴，15-25°C，适合户外活动！
```

### 7.3 测试基本功能

```bash
# 测试文件操作
openclaw ask "帮我创建一个测试文件，内容是'Hello OpenClaw'"

# 测试网络搜索
openclaw ask "帮我查一下最新的 AI 新闻"

# 测试系统信息
openclaw ask "你现在运行在哪里？系统信息是什么？"
```

---

## 8. 常见问题排查

### 8.1 安装问题

**Q1: 安装脚本报错 "Permission denied"**

```bash
# 解决方案：添加执行权限
chmod +x install.sh
./install.sh

# 或者使用 sudo
curl -fsSL https://openclaw.ai/install.sh | sudo bash
```

**Q2: npm 安装速度慢**

```bash
# 使用国内镜像
npm config set registry https://registry.npmmirror.com

# 然后重新安装
npm install -g openclaw
```

**Q3: 提示 "command not found: openclaw"**

```bash
# 检查 npm 全局路径
npm config get prefix

# 将路径添加到 PATH（添加到 ~/.bashrc 或 ~/.zshrc）
export PATH=$(npm config get prefix)/bin:$PATH

# 重新加载配置
source ~/.bashrc  # 或 source ~/.zshrc
```

### 8.2 启动问题

**Q4: Gateway 启动失败**

```bash
# 查看错误日志
openclaw logs --level error

# 常见原因：
# 1. 端口被占用 → 修改 config.json 中的 port
# 2. 配置文件错误 → 检查 JSON 格式
# 3. API Key 无效 → 重新获取 API Key

# 检查端口占用
lsof -i :3000

# 杀死占用端口的进程
kill -9 <PID>
```

**Q5: 配置保存后不生效**

```bash
# 重启 Gateway
openclaw gateway restart

# 检查配置文件路径
ls -la ~/.openclaw/config.json

# 验证 JSON 格式
cat ~/.openclaw/config.json | python -m json.tool
```

### 8.3 消息渠道问题

**Q6: 飞书机器人不回复**

```
检查清单：
□ App ID 和 Secret 是否正确
□ 机器人是否已发布
□ 权限是否已配置
□ 机器人是否已添加到群聊/私聊

调试步骤：
1. 查看 Gateway 日志
2. 检查飞书开放平台事件订阅
3. 确认 webhook URL 是否正确
```

**Q7: Telegram 机器人不回复**

```
检查清单：
□ Bot Token 是否正确
□ Chat ID 是否正确
□ 机器人是否已启动

调试步骤：
1. 在 Telegram 搜索机器人，发送 /start
2. 访问：https://api.telegram.org/bot<TOKEN>/getUpdates
3. 确认能收到消息更新
```

---

## 9. 安装验证清单

```
┌─────────────────────────────────────────────────────────┐
│              安装验证清单 ✅                             │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  基础安装                                               │
│  □ openclaw 命令可用                                    │
│  □ openclaw --version 显示版本号                        │
│  □ ~/.openclaw 目录已创建                              │
│  □ config.json 配置文件存在                             │
│                                                         │
│  Gateway 运行                                           │
│  □ Gateway 已启动                                       │
│  □ 端口 3000 可访问                                      │
│  □ 日志正常输出                                         │
│                                                         │
│  消息渠道                                               │
│  □ 至少配置 1 个消息渠道                                 │
│  □ 能收到消息                                           │
│  □ 能发送回复                                           │
│                                                         │
│  功能测试                                               │
│  □ 能进行简单对话                                       │
│  □ 能执行基本命令（如搜索）                             │
│  □ 能访问工作区文件                                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 10. 本篇总结

### 📌 核心要点

1. **三种安装方式**：一键安装（推荐）、手动安装、Docker 安装
2. **配置是关键**：正确配置 API Key 和消息渠道
3. **验证很重要**：安装后务必进行测试
4. **日志是朋友**：遇到问题先查日志

### ✅ 学完这篇你应该能：

- [ ] 使用一键脚本安装 OpenClaw
- [ ] 手动安装 OpenClaw（可选）
- [ ] 使用 Docker 安装 OpenClaw（可选）
- [ ] 编辑和配置 config.json
- [ ] 启动和停止 Gateway
- [ ] 进行第一次对话测试
- [ ] 排查常见安装问题

---

## 📚 下一篇预告

**第 09 篇：配置详解 —— config.json 每个参数的含义**

我们会深入讲解：
- config.json 的完整结构
- 每个配置项的含义和用法
- 高级配置选项
- 多环境配置管理
- 配置最佳实践

---

## 🤔 思考题

1. 你选择了哪种安装方式？为什么？
2. 安装过程中遇到了什么问题？如何解决的？
3. 你配置了哪个消息渠道？测试成功了吗？
4. 你打算使用哪个 AI 模型？为什么？

---

## 📋 安装完成度自评

```
完成以下任务后打勾：

□ 选择并执行安装方式
□ openclaw 命令可用
□ 配置文件已编辑
□ Gateway 已启动
□ 通过命令行测试对话
□ 通过消息渠道测试对话

完成度：___/6

6/6：完美！可以进入第 09 篇
4-5/6：良好，继续完成剩余测试
3/6 以下：建议重新阅读本篇，完成安装
```

---

## 🔧 快速命令参考

```bash
# 安装
curl -fsSL https://openclaw.ai/install.sh | bash

# 启动
openclaw gateway start

# 状态
openclaw gateway status

# 日志
openclaw logs

# 测试对话
openclaw ask "你好"

# 重启
openclaw gateway restart

# 停止
openclaw gateway stop
```

---

**🎉 恭喜！OpenClaw 已成功安装并运行！**

---

*📅 创建时间：2026-03-26 07:30*  
*📂 文件位置：`tutorials/openclaw-series/08-quick-installation.md`*
