# 第 07 篇：安装前的准备 —— 系统要求与环境检查

> 💡 **学习时长**：10 分钟 | **难度**：⭐ 入门 | **阶段**：安装与部署

---

## 🎯 本篇你将学到

- OpenClaw 的系统要求
- 如何检查你的设备是否适合
- 安装前需要准备的工具
- 不同部署方案的对比

---

## 1. 系统要求

OpenClaw 的设计目标是**轻量、灵活**，可以在多种设备上运行。

### 🖥️ 最低配置

| 组件 | 要求 | 说明 |
|------|------|------|
| **CPU** | 双核 1.5GHz | 任何现代处理器都可以 |
| **内存** | 2GB RAM | 建议 4GB 以上 |
| **存储** | 1GB 可用空间 | 用于安装和存储数据 |
| **系统** | Linux/macOS/Windows | 支持主流操作系统 |
| **网络** | 稳定的互联网连接 | 用于连接 AI 模型 API |

### ⭐ 推荐配置

| 组件 | 要求 | 说明 |
|------|------|------|
| **CPU** | 四核 2.0GHz+ | 更快的响应速度 |
| **内存** | 4-8GB RAM | 同时处理多个任务 |
| **存储** | 10GB+ SSD | 快速读写，存储更多数据 |
| **系统** | Linux (Ubuntu 20.04+) | 最佳兼容性 |
| **网络** | 宽带连接 | 低延迟 API 访问 |

---

## 2. 操作系统选择

### 🐧 Linux（推荐）

**优点：**
- ✅ 最佳性能和稳定性
- ✅ 社区支持最好
- ✅ 适合 24/7 运行
- ✅ 资源占用低

**推荐发行版：**
- Ubuntu 20.04/22.04 LTS
- Debian 11/12
- CentOS/Rocky Linux 8+
- Raspberry Pi OS（树莓派）

### 🍎 macOS

**优点：**
- ✅ 开发友好
- ✅ 适合学习和测试
- ✅ 命令行工具完善

**注意：**
- ⚠️ 需要安装 Homebrew
- ⚠️ 某些功能可能需要额外配置

### 🪟 Windows

**支持方式：**
- ✅ WSL2（Windows Subsystem for Linux）
- ✅ Docker Desktop
- ⚠️ 原生支持有限（不推荐）

**推荐方案：**
```powershell
# 安装 WSL2
wsl --install

# 安装 Ubuntu
wsl --install -d Ubuntu-22.04
```

---

## 3. 部署方案对比

### 方案 1：家用电脑/笔记本

**适合人群：** 初学者、个人使用

**优点：**
- ✅ 零成本（利用现有设备）
- ✅ 方便调试和学习
- ✅ 性能通常较好

**缺点：**
- ❌ 需要 24 小时开机
- ❌ 耗电量大
- ❌ 网络依赖家庭宽带

**建议：**
- 适合白天使用或测试
- 可以设置定时任务在需要时运行

---

### 方案 2：树莓派（Raspberry Pi）

**适合人群：** 爱好者、轻量使用

**优点：**
- ✅ 低功耗（5-10W）
- ✅ 小巧安静
- ✅ 适合 24/7 运行
- ✅ 成本低（约 300-500 元）

**缺点：**
- ❌ 性能有限
- ❌ 需要额外购买 SD 卡、电源等

**推荐型号：**
- Raspberry Pi 4B（4GB/8GB）
- Raspberry Pi 5（最新款）

---

### 方案 3：VPS 云服务器

**适合人群：** 生产环境、企业用户

**优点：**
- ✅ 24/7 稳定运行
- ✅ 公网 IP，随时访问
- ✅ 专业数据中心
- ✅ 按需付费

**缺点：**
- ❌ 月费成本（50-200 元/月）
- ❌ 需要基本 Linux 技能

**推荐服务商：**
- 阿里云（国内）
- 腾讯云（国内）
- AWS/Azure（国际）
- DigitalOcean/Vultr（性价比）

**配置建议：**
```
CPU: 2 核
内存：4GB
存储：40GB SSD
带宽：3-5Mbps
月费用：约 60-100 元
```

---

### 方案 4：家庭服务器/NAS

**适合人群：** 已有 NAS 用户

**优点：**
- ✅ 利用现有设备
- ✅ 24/7 运行
- ✅ 本地网络，速度快

**支持平台：**
- 群晖（Synology）Docker
- 威联通（QNAP）Container Station
- 自组 NAS + Docker

---

## 4. 安装前检查清单

### ✅ 必检项目

**1. 系统版本检查**

```bash
# Linux
uname -a
cat /etc/os-release

# macOS
sw_vers

# WSL
wsl --status
```

**2. 内存检查**

```bash
free -h
# 或
cat /proc/meminfo
```

**3. 存储空间检查**

```bash
df -h
# 确保至少有 5GB 可用空间
```

**4. 网络连接检查**

```bash
# 测试网络连通性
ping -c 4 www.baidu.com

# 测试 API 连通性
curl -I https://api.baidu.com
```

**5. Node.js 版本检查**

```bash
# OpenClaw 需要 Node.js 18+
node --version
npm --version

# 如果未安装或版本过低：
# Ubuntu/Debian
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt-get install -y nodejs

# macOS
brew install node@20
```

---

### ✅ 可选项目

**6. Docker 安装（可选）**

```bash
# 检查是否已安装
docker --version

# 安装 Docker（Ubuntu）
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

**7. Git 安装**

```bash
# 检查是否已安装
git --version

# 安装
# Ubuntu/Debian
sudo apt-get install -y git

# macOS
brew install git
```

---

## 5. 快速检查脚本

创建一个脚本自动检查所有要求：

```bash
#!/bin/bash
# 保存为 check-requirements.sh

echo "🔍 OpenClaw 安装前检查"
echo "===================="

# 系统信息
echo -e "\n📌 系统信息:"
uname -a

# CPU
echo -e "\n📌 CPU:"
grep "model name" /proc/cpuinfo | head -1

# 内存
echo -e "\n📌 内存:"
free -h

# 存储
echo -e "\n📌 存储:"
df -h / | tail -1

# Node.js
echo -e "\n📌 Node.js:"
if command -v node &> /dev/null; then
    node --version
    npm --version
else
    echo "❌ Node.js 未安装"
fi

# 网络
echo -e "\n📌 网络测试:"
if ping -c 2 www.baidu.com &> /dev/null; then
    echo "✅ 网络连接正常"
else
    echo "❌ 网络连接失败"
fi

echo -e "\n===================="
echo "检查完成！"
```

**使用方法：**
```bash
chmod +x check-requirements.sh
./check-requirements.sh
```

---

## 6. 常见问题

### Q1: 旧电脑可以运行吗？

**A:** 可以！OpenClaw 对硬件要求不高：
- 10 年前的电脑也能运行
- 关键是内存至少 2GB
- 建议使用 Linux 系统提高性能

### Q2: 需要公网 IP 吗？

**A:** 不需要：
- 本地使用：不需要公网 IP
- 远程访问：需要公网 IP 或内网穿透（如 Tailscale）

### Q3: 可以和其他服务共用服务器吗？

**A:** 可以！OpenClaw 资源占用低：
- 可以和 Web 服务器、数据库等共存
- 建议使用 Docker 隔离

### Q4: 需要 GPU 吗？

**A:** 不需要：
- OpenClaw 本身不使用 GPU
- AI 模型在云端 API 运行
- 如果使用本地模型（如 Ollama），建议有 GPU

---

## 7. 本篇总结

### 📌 核心要点

1. **系统要求低** - 任何现代设备都可以运行
2. **Linux 最佳** - 推荐 Ubuntu 20.04+
3. **部署方案多** - 根据需求选择合适方案
4. **检查很重要** - 安装前做好准备工作

### ✅ 安装前你应该：

- [ ] 确认设备满足最低配置
- [ ] 选择合适的部署方案
- [ ] 安装 Node.js 18+
- [ ] 确保网络连接稳定
- [ ] 准备至少 5GB 存储空间

---

## 📚 下一篇预告

**第 08 篇：快速安装 —— 一行命令搞定**

我们将：
- 演示完整的安装过程
- 配置基本信息
- 启动你的第一个 OpenClaw 实例

---

## 🤔 思考题

1. 你打算在什么设备上部署 OpenClaw？
2. 你的使用场景需要 24/7 运行吗？
3. 你更关心性能还是成本？

---

**🎉 准备好了吗？下一篇文章就开始安装！**

---

*📅 创建时间：2026-03-26 20:00*  
*📂 文件位置：`tutorials/openclaw-series/07-installation-preparation.md`*
