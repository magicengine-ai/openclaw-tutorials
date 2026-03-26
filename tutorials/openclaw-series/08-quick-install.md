# 第 08 篇：快速安装 —— 一行命令搞定

> 💡 **学习时长**：10 分钟 | **难度**：⭐ 入门 | **阶段**：安装与部署

---

## 🎯 本篇你将学到

- 使用官方脚本快速安装
- 手动安装的完整步骤
- 验证安装是否成功
- 启动你的第一个 OpenClaw 实例

---

## 1. 快速安装（推荐）

### 🚀 一行命令安装

OpenClaw 提供了自动化安装脚本，适用于大多数 Linux 系统：

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

**安装过程：**
```
🦞 OpenClaw 安装脚本

✓ 检查系统要求
✓ 安装 Node.js 20
✓ 安装 OpenClaw
✓ 创建配置文件
✓ 设置系统服务

安装完成！运行以下命令启动：
  openclaw gateway start
```

**就这么简单！** 整个安装过程通常只需要 2-3 分钟。

---

### 📋 安装后检查

安装完成后，验证是否成功：

```bash
# 检查版本
openclaw --version

# 输出示例：
# OpenClaw 2026.3.13 (61d171a)
```

---

## 2. 手动安装（进阶）

如果自动脚本失败，或者你想完全控制安装过程，可以手动安装。

### 步骤 1：安装 Node.js

OpenClaw 需要 Node.js 18 或更高版本。

**Ubuntu/Debian:**
```bash
# 使用 NodeSource 仓库
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# 验证安装
node --version  # 应该显示 v20.x.x
npm --version   # 应该显示 10.x.x
```

**macOS:**
```bash
# 使用 Homebrew
brew install node@20

# 验证
node --version
```

**CentOS/RHEL:**
```bash
curl -fsSL https://rpm.nodesource.com/setup_20.x | sudo bash -
sudo yum install -y nodejs
```

---

### 步骤 2：安装 OpenClaw

```bash
# 使用 npm 全局安装
npm install -g openclaw

# 验证安装
openclaw --version
```

---

### 步骤 3：初始化配置

```bash
# 创建配置目录
mkdir -p ~/.openclaw

# 生成默认配置文件
openclaw init
```

这会创建以下文件结构：
```
~/.openclaw/
├── openclaw.json      # 主配置文件
├── workspace/         # 工作目录
└── logs/             # 日志目录
```

---

## 3. 配置 AI 模型

OpenClaw 需要连接 AI 模型 API 才能工作。

### 方案 1：使用云端 API（推荐）

**配置百度千帆（Qwen）:**

编辑 `~/.openclaw/openclaw.json`：

```json
{
  "model": {
    "provider": "bailian",
    "model": "qwen3.5-plus",
    "apiKey": "你的 API Key"
  }
}
```

**获取 API Key:**
1. 访问 https://cloud.baidu.com/product/wenxinworkshop
2. 注册/登录账号
3. 创建应用获取 API Key
4. 复制到配置文件中

**其他支持的模型:**
- OpenAI GPT-4
- Anthropic Claude
- 智谱 GLM
- 月之暗面 Kimi

---

### 方案 2：使用本地模型（可选）

如果你关心数据隐私，可以使用本地模型：

```bash
# 安装 Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# 下载模型
ollama pull qwen2.5:7b

# 配置 OpenClaw 使用本地模型
# 在 openclaw.json 中配置：
{
  "model": {
    "provider": "ollama",
    "model": "qwen2.5:7b",
    "endpoint": "http://localhost:11434"
  }
}
```

**优点：**
- ✅ 数据完全本地
- ✅ 无需 API 费用
- ✅ 离线可用

**缺点：**
- ❌ 需要更好的硬件
- ❌ 模型能力相对较弱

---

## 4. 启动 OpenClaw

### 启动 Gateway

```bash
# 启动服务
openclaw gateway start

# 查看状态
openclaw gateway status

# 停止服务
openclaw gateway stop

# 重启服务
openclaw gateway restart
```

**输出示例：**
```
🦞 OpenClaw Gateway 已启动

状态：运行中
端口：18789
日志：~/.openclaw/logs/gateway.log
```

---

### 查看日志

```bash
# 实时查看日志
tail -f ~/.openclaw/logs/gateway.log

# 查看最近 100 行
tail -100 ~/.openclaw/logs/gateway.log
```

---

## 5. 验证安装

### 测试 1：检查服务状态

```bash
openclaw status
```

**成功输出：**
```
🦞 OpenClaw 状态

Gateway: ✅ 运行中
模型：✅ 已连接
Channel: ⚠️ 未配置
```

---

### 测试 2：发送测试消息

```bash
# 发送测试消息到当前会话
openclaw agent --message "你好，测试"
```

**预期响应：**
```
你好！我是你的 OpenClaw 助手。有什么可以帮你的吗？
```

---

### 测试 3：检查工具可用性

```bash
# 测试网络搜索
openclaw agent --message "今天北京天气怎么样？"

# 测试文件操作
openclaw agent --message "帮我看看当前目录有哪些文件"
```

---

## 6. 常见问题

### Q1: 安装时提示权限错误

**错误信息：**
```
npm ERR! Error: EACCES: permission denied
```

**解决方案：**
```bash
# 方案 1：使用 sudo
sudo npm install -g openclaw

# 方案 2：修复 npm 权限（推荐）
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
npm install -g openclaw
```

---

### Q2: Gateway 启动失败

**错误信息：**
```
Error: Port 18789 is already in use
```

**解决方案：**
```bash
# 查找占用端口的进程
lsof -i :18789

# 杀死进程
kill -9 <PID>

# 或者修改配置使用其他端口
# 编辑 ~/.openclaw/openclaw.json
{
  "gateway": {
    "port": 18790  # 使用其他端口
  }
}
```

---

### Q3: 模型连接失败

**错误信息：**
```
Error: API key is invalid
```

**解决方案：**
1. 检查 API Key 是否正确
2. 确认账户有足够余额
3. 检查网络连接
4. 查看日志获取详细错误信息

---

### Q4: 命令找不到

**错误信息：**
```
command not found: openclaw
```

**解决方案：**
```bash
# 检查 npm 全局路径
npm config get prefix

# 添加路径到环境变量
echo 'export PATH=$(npm config get prefix)/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# 验证
openclaw --version
```

---

## 7. 安装检查清单

完成安装后，确认以下项目：

- [ ] ✅ Node.js 18+ 已安装
- [ ] ✅ OpenClaw 已安装（`openclaw --version`）
- [ ] ✅ 配置文件已创建（`~/.openclaw/openclaw.json`）
- [ ] ✅ AI 模型 API Key 已配置
- [ ] ✅ Gateway 可以启动（`openclaw gateway start`）
- [ ] ✅ 测试消息可以发送

---

## 8. 本篇总结

### 📌 核心要点

1. **快速安装** - 一行命令搞定（推荐）
2. **手动安装** - 完全控制安装过程
3. **配置模型** - 选择云端或本地模型
4. **启动服务** - `openclaw gateway start`
5. **验证安装** - 发送测试消息

### ✅ 学完这篇你应该能：

- [ ] 使用自动脚本安装 OpenClaw
- [ ] 手动安装和配置 OpenClaw
- [ ] 配置 AI 模型 API
- [ ] 启动和停止 Gateway 服务
- [ ] 验证安装是否成功

---

## 📚 下一篇预告

**第 09 篇：配置详解 —— config.json 每个参数的含义**

我们将深入讲解：
- 配置文件的完整结构
- 每个参数的作用和用法
- 常用配置示例
- 高级配置技巧

---

## 🤔 思考题

1. 你选择云端 API 还是本地模型？为什么？
2. 你打算在什么设备上运行 OpenClaw？
3. 安装过程中遇到了什么问题？如何解决的？

---

**🎉 恭喜！OpenClaw 已经安装完成！**

---

*📅 创建时间：2026-03-26 20:40*  
*📂 文件位置：`tutorials/openclaw-series/08-quick-install.md`*
