# 第 11 篇：部署到服务器 —— VPS/树莓派/家庭服务器

> 💡 **学习时长**：25 分钟 | **难度**：⭐⭐ 中等 | **阶段**：安装与部署

---

## 🎯 本篇你将学到

- 为什么需要部署到服务器
- 三种部署方案的对比和选择
- VPS 部署完整指南（阿里云/腾讯云/AWS）
- 树莓派部署指南
- 家庭服务器部署指南
- 域名和 HTTPS 配置
- 开机自启动配置
- 服务器运维基础

---

## 1. 为什么需要部署到服务器？

### 1.1 本地运行 vs 服务器运行

```
┌─────────────────────────────────────────────────────────┐
│              本地运行 vs 服务器运行                      │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  本地运行（电脑）                                        │
│  ─────────────────                                      │
│  ✅ 优点：配置简单、无需额外费用                         │
│  ❌ 缺点：电脑关机就停了、外网无法访问                   │
│                                                         │
│  服务器运行（VPS/树莓派/家庭服务器）                      │
│  ──────────────────────────────                         │
│  ✅ 优点：24 小时在线、外网可访问、稳定可靠               │
│  ❌ 缺点：需要额外设备/费用、配置稍复杂                  │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 1.2 部署场景对比

| 场景 | 推荐方案 | 费用 | 难度 | 适合人群 |
|------|----------|------|------|----------|
| **个人学习** | 本地电脑 | 🆓 免费 | ⭐ 简单 | 初学者 |
| **24 小时服务** | VPS | 💰¥50-200/月 | ⭐⭐ 中等 | 进阶用户 |
| **低成本方案** | 树莓派 | 💰¥300-500（一次性） | ⭐⭐ 中等 | 爱好者 |
| **已有设备** | 家庭服务器 | 🆓 利用现有设备 | ⭐⭐ 中等 | 家庭用户 |
| **企业应用** | 云服务器 | 💰¥200+/月 | ⭐⭐⭐ 较难 | 企业用户 |

---

## 2. 方案一：VPS 部署（推荐）

### 2.1 什么是 VPS？

```
VPS = Virtual Private Server（虚拟专用服务器）

简单理解：租用云厂商的一台远程电脑，24 小时运行

主流提供商：
• 阿里云（国内推荐）
• 腾讯云（国内推荐）
• AWS（海外）
• DigitalOcean（海外）
• Vultr（海外）
```

### 2.2 VPS 配置选择

| 配置项 | 最低要求 | 推荐配置 | 说明 |
|--------|----------|----------|------|
| **CPU** | 1 核 | 2 核 | OpenClaw 计算需求不高 |
| **内存** | 1GB | 2GB | 建议 2GB 更流畅 |
| **硬盘** | 20GB | 40GB+ | 存储日志和文件 |
| **带宽** | 1Mbps | 3Mbps+ | 影响响应速度 |
| **系统** | Ubuntu 20.04 | Ubuntu 22.04 | 推荐 Ubuntu |

**费用参考（国内）：**
```
阿里云/腾讯云：
• 入门款：¥50-80/月（1 核 1GB）
• 推荐款：¥100-150/月（2 核 2GB）
• 高配款：¥200+/月（4 核 4GB）

💡 提示：新用户通常有优惠，首年可能只需 ¥100-300
```

### 2.3 购买 VPS（以阿里云为例）

```
步骤 1：注册阿里云账号
访问：https://www.aliyun.com
使用手机号注册

步骤 2：实名认证
• 个人用户：上传身份证
• 企业用户：营业执照

步骤 3：选择云服务器 ECS
• 进入控制台 → 云服务器 ECS
• 点击"创建实例"

步骤 4：配置选择
• 地域：选择离你近的（如华北 2-北京）
• 镜像：Ubuntu 22.04 64 位
• 实例规格：2 核 2GB
• 存储：40GB ESSD
• 带宽：3Mbps
• 密码：设置 root 密码（保存好！）

步骤 5：付款并创建
• 确认配置
• 完成付款
• 等待实例创建完成（1-2 分钟）
```

### 2.4 连接服务器

```bash
# macOS/Linux 使用 SSH 连接
ssh root@你的服务器 IP

# 输入密码（输入时不显示）
# 首次连接会提示确认，输入 yes

# Windows 使用 PowerShell 或 PuTTY
# PowerShell:
ssh root@你的服务器 IP

# 成功登录后，你会看到类似提示：
# Welcome to Ubuntu 22.04 LTS
# root@your-server:~#
```

### 2.5 部署 OpenClaw

```bash
# 步骤 1：更新系统
apt update && apt upgrade -y

# 步骤 2：安装 Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt install -y nodejs git

# 步骤 3：安装 OpenClaw
npm install -g openclaw

# 步骤 4：创建配置目录
mkdir -p ~/.openclaw

# 步骤 5：编辑配置
nano ~/.openclaw/config.json

# 填入你的配置（参考第 09 篇）
# 注意：gateway.host 改为 "0.0.0.0"

# 步骤 6：启动 Gateway
openclaw gateway start

# 步骤 7：设置开机自启动（见第 4 节）
```

### 2.6 配置防火墙

```bash
# 阿里云/腾讯云需要在控制台配置安全组

# 步骤 1：登录云控制台
# 步骤 2：找到你的实例
# 步骤 3：配置安全组规则
# 步骤 4：添加入站规则：
#   - 端口：3000（或你配置的端口）
#   - 协议：TCP
#   - 授权对象：0.0.0.0/0（允许所有 IP）
#   - 描述：OpenClaw Gateway

# 服务器内部防火墙（如有）
ufw allow 3000/tcp
ufw enable
```

---

## 3. 方案二：树莓派部署

### 3.1 为什么选择树莓派？

```
✅ 优点：
• 一次性投入（¥300-500）
• 低功耗（5-10W）
• 静音无噪音
• 小巧不占空间
• 24 小时运行成本低

⚠️ 注意：
• 性能有限（适合轻度使用）
• 需要自己配置系统
• 需要 microSD 卡和网络
```

### 3.2 硬件准备

| 物品 | 推荐型号 | 参考价格 |
|------|----------|----------|
| **树莓派** | Raspberry Pi 4B (2GB/4GB) | ¥300-500 |
| ** microSD 卡** | 32GB+ Class 10 | ¥30-50 |
| **电源** | 官方电源（5V 3A） | ¥30 |
| **外壳** | 带散热风扇 | ¥20-40 |
| **网线** | 或使用 WiFi | ¥10 |

### 3.3 安装系统

```
步骤 1：下载 Raspberry Pi Imager
访问：https://www.raspberrypi.com/software/

步骤 2：准备 microSD 卡
• 将卡插入电脑
• 打开 Raspberry Pi Imager

步骤 3：选择系统和卡
• OS：Raspberry Pi OS (64-bit)
• Storage：选择你的 microSD 卡

步骤 4：配置（点击设置图标）
• 启用 SSH
• 设置用户名和密码
• 配置 WiFi（可选）

步骤 5：写入系统
• 点击"Write"
• 等待写入完成（5-10 分钟）

步骤 6：启动树莓派
• 将卡插入树莓派
• 连接电源和网线
• 等待启动（2-3 分钟）
```

### 3.4 连接树莓派

```bash
# 方式 1：SSH 连接（推荐）
ssh pi@树莓派 IP 地址

# 方式 2：直接连接显示器和键盘
• 连接 HDMI 显示器
• 连接 USB 键盘
• 直接操作
```

### 3.5 部署 OpenClaw

```bash
# 步骤 1：更新系统
sudo apt update && sudo apt upgrade -y

# 步骤 2：安装 Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
sudo apt install -y nodejs git

# 步骤 3：安装 OpenClaw
sudo npm install -g openclaw

# 步骤 4：创建配置
mkdir -p ~/.openclaw
nano ~/.openclaw/config.json

# 步骤 5：启动
openclaw gateway start

# 性能优化（树莓派专用）
# 限制内存使用
export NODE_OPTIONS="--max-old-space-size=512"
```

### 3.6 树莓派优化建议

```bash
# 1. 增加 swap 空间（防止内存不足）
sudo dphys-swapfile swapoff
sudo nano /etc/dphys-swapfile
# 修改：CONF_SWAPSIZE=1024
sudo dphys-swapfile setup
sudo dphys-swapfile swapon

# 2. 禁用不必要的服务
sudo systemctl disable bluetooth
sudo systemctl disable cups

# 3. 监控温度
vcgencmd measure_temp

# 4. 使用 SSD 代替 microSD 卡（可选，提升性能）
```

---

## 4. 方案三：家庭服务器部署

### 4.1 什么是家庭服务器？

```
家庭服务器 = 利用家中现有电脑/旧笔记本

✅ 优点：
• 零成本（利用现有设备）
• 性能通常比树莓派好
• 完全控制

⚠️ 注意：
• 需要 24 小时开机
• 耗电比 VPS/树莓派高
• 需要配置网络
```

### 4.2 适用设备

| 设备类型 | 推荐度 | 说明 |
|----------|--------|------|
| **旧笔记本** | ⭐⭐⭐⭐⭐ | 自带电池（UPS）、低功耗 |
| **旧台式机** | ⭐⭐⭐ | 性能好但耗电高 |
| **迷你主机** | ⭐⭐⭐⭐ | 小巧、低功耗 |
| **NAS** | ⭐⭐⭐⭐ | 如群晖，可安装 Docker |

### 4.3 部署步骤

```bash
# 步骤 1：安装 Ubuntu Server（或保留 Windows 用 WSL2）
# 下载地址：https://ubuntu.com/download/server

# 步骤 2：安装 Node.js 和 OpenClaw
# （同 VPS 部署步骤）

# 步骤 3：配置固定 IP（路由器设置）
# 登录路由器 → DHCP 静态分配
# 将服务器 MAC 地址绑定到固定 IP

# 步骤 4：端口转发（如需外网访问）
# 登录路由器 → 端口转发/虚拟服务器
# 添加规则：
#   - 外部端口：3000
#   - 内部 IP：服务器 IP
#   - 内部端口：3000
#   - 协议：TCP
```

### 4.4 外网访问方案

```
方案 1：端口转发（简单）
• 在路由器配置端口转发
• 使用动态 DNS（如花生壳）
• 访问：http://你的域名:3000

方案 2：内网穿透（无需公网 IP）
• 使用 ngrok：ngrok http 3000
• 使用 frp：自建内网穿透
• 使用 Cloudflare Tunnel

方案 3：Tailscale/ZeroTier（推荐）
• 安装 Tailscale：curl -fsSL https://tailscale.com/install.sh | sh
• 登录账号
• 在其他设备也安装 Tailscale
• 通过 Tailscale IP 访问（安全、免费）
```

---

## 5. 开机自启动配置

### 5.1 使用 systemd（推荐）

```bash
# 创建服务文件
sudo nano /etc/systemd/system/openclaw.service

# 填入以下内容：
[Unit]
Description=OpenClaw Gateway
After=network.target

[Service]
Type=simple
User=你的用户名
WorkingDirectory=/home/你的用户名
ExecStart=/usr/bin/openclaw gateway run
Restart=always
RestartSec=10
Environment=NODE_ENV=production

[Install]
WantedBy=multi-user.target

# 保存后，执行以下命令：
# 重载 systemd 配置
sudo systemctl daemon-reload

# 启用服务（开机自启）
sudo systemctl enable openclaw

# 启动服务
sudo systemctl start openclaw

# 查看状态
sudo systemctl status openclaw

# 查看日志
sudo journalctl -u openclaw -f

# 停止服务
sudo systemctl stop openclaw

# 重启服务
sudo systemctl restart openclaw
```

### 5.2 使用 PM2（可选）

```bash
# 安装 PM2
npm install -g pm2

# 启动 OpenClaw
pm2 start openclaw --name "openclaw-gateway" -- gateway run

# 开机自启
pm2 startup
# 按提示执行生成的命令

pm2 save

# 常用命令
pm2 list          # 查看进程
pm2 logs          # 查看日志
pm2 restart       # 重启
pm2 stop          # 停止
pm2 delete        # 删除
```

---

## 6. 域名和 HTTPS 配置（可选）

### 6.1 购买域名

```
推荐注册商：
• 阿里云万网（国内）
• 腾讯云 DNSPod（国内）
• Namecheap（海外）
• Cloudflare（海外）

费用：¥50-100/年（.com/.cn）
```

### 6.2 域名解析

```
步骤 1：登录域名管理后台
步骤 2：添加 DNS 记录
步骤 3：配置 A 记录
  • 主机记录：@ 或 openclaw
  • 记录类型：A
  • 记录值：你的服务器 IP
  • TTL：600

步骤 4：等待生效（5-10 分钟）
步骤 5：测试
  ping 你的域名
  # 应返回服务器 IP
```

### 6.3 配置 HTTPS（使用 Nginx + Let's Encrypt）

```bash
# 步骤 1：安装 Nginx
sudo apt install nginx -y

# 步骤 2：配置 Nginx
sudo nano /etc/nginx/sites-available/openclaw

# 填入以下内容：
server {
    listen 80;
    server_name 你的域名;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# 步骤 3：启用配置
sudo ln -s /etc/nginx/sites-available/openclaw /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

# 步骤 4：安装 Certbot
sudo apt install certbot python3-certbot-nginx -y

# 步骤 5：获取证书
sudo certbot --nginx -d 你的域名

# 步骤 6：自动续期（已自动配置）
# 验证：sudo certbot renew --dry-run
```

---

## 7. 服务器运维基础

### 7.1 安全加固

```bash
# 1. 创建普通用户（不用 root）
adduser openclaw
usermod -aG sudo openclaw

# 2. 禁用 root SSH 登录
sudo nano /etc/ssh/sshd_config
# 修改：PermitRootLogin no
sudo systemctl restart sshd

# 3. 配置 SSH 密钥登录
ssh-keygen -t ed25519
ssh-copy-id openclaw@服务器 IP

# 4. 安装 fail2ban（防暴力破解）
sudo apt install fail2ban -y
sudo systemctl enable fail2ban

# 5. 定期更新系统
sudo apt update && sudo apt upgrade -y
```

### 7.2 监控和日志

```bash
# 查看系统资源
htop           # CPU/内存使用
df -h          # 磁盘使用
free -h        # 内存使用

# 查看 OpenClaw 日志
openclaw logs --tail 100
sudo journalctl -u openclaw -f

# 查看 Nginx 日志（如配置）
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

### 7.3 备份配置

```bash
# 备份配置文件
tar -czf openclaw-backup-$(date +%Y%m%d).tar.gz ~/.openclaw/

# 备份到远程
scp openclaw-backup-*.tar.gz user@backup-server:/backups/

# 定时备份（cron）
crontab -e
# 添加：0 2 * * * tar -czf /backups/openclaw-$(date +\%Y\%m\%d).tar.gz ~/.openclaw/
```

---

## 8. 部署方案对比

```
┌──────────────────────────────────────────────────────────────────┐
│                    部署方案对比表                                 │
├─────────────┬──────────────┬──────────────┬──────────────────────┤
│    方案     │    VPS       │   树莓派     │    家庭服务器        │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  初期成本   │  ¥50-150/月  │  ¥300-500    │  ¥0（利用现有）      │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  年费用     │  ¥600-1800   │  ¥50（电费）  │  ¥200-500（电费）    │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  性能       │  ⭐⭐⭐⭐       │  ⭐⭐          │  ⭐⭐⭐⭐              │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  稳定性     │  ⭐⭐⭐⭐⭐      │  ⭐⭐⭐⭐        │  ⭐⭐⭐                │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  配置难度   │  ⭐⭐          │  ⭐⭐⭐         │  ⭐⭐⭐                │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  维护成本   │  低           │  中           │  中                  │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│  适合人群   │  大多数用户   │  爱好者       │  有闲置设备用户      │
└─────────────┴──────────────┴──────────────┴──────────────────────┘
```

---

## 9. 部署检查清单

```
┌─────────────────────────────────────────────────────────┐
│              服务器部署检查清单 ✅                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  部署前准备                                             │
│  □ 选择部署方案（VPS/树莓派/家庭服务器）                │
│  □ 准备必要硬件/账号                                    │
│  □ 记录服务器 IP 和登录凭证                             │
│                                                         │
│  系统配置                                               │
│  □ 系统已更新                                           │
│  □ Node.js 20.x 已安装                                  │
│  □ OpenClaw 已安装                                      │
│  □ 配置文件已编辑                                       │
│                                                         │
│  网络配置                                               │
│  □ 防火墙已配置（安全组/ufw）                           │
│  □ 端口已开放（3000）                                   │
│  □ （可选）域名已解析                                   │
│  □ （可选）HTTPS 已配置                                 │
│                                                         │
│  服务配置                                               │
│  □ 开机自启动已配置（systemd/PM2）                      │
│  □ 服务已启动                                           │
│  □ 服务状态正常                                         │
│                                                         │
│  测试验证                                               │
│  □ 能从外网访问                                         │
│  □ 消息渠道正常工作                                     │
│  □ 日志正常输出                                         │
│                                                         │
│  安全加固                                               │
│  □ 已创建普通用户                                       │
│  □ SSH 密钥登录已配置                                   │
│  □ 定期备份已配置                                       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 10. 本篇总结

### 📌 核心要点

1. **VPS 部署**：最推荐，稳定可靠，适合大多数用户
2. **树莓派部署**：低成本，适合爱好者和轻度使用
3. **家庭服务器**：零成本，适合有闲置设备的用户
4. **开机自启**：使用 systemd 配置，确保服务持续运行
5. **域名 HTTPS**：可选，提升访问体验和安全性
6. **安全加固**：创建普通用户、SSH 密钥、定期更新

### ✅ 学完这篇你应该能：

- [ ] 选择适合的部署方案
- [ ] 购买和配置 VPS
- [ ] 部署树莓派系统
- [ ] 配置家庭服务器
- [ ] 配置开机自启动
- [ ] 配置域名和 HTTPS（可选）
- [ ] 进行基础安全加固
- [ ] 监控和备份服务器

---

## 📚 下一篇预告

**第 12 篇：第二阶段小结与故障排查**

我们会：
- 回顾第二阶段 6 篇的核心知识点
- 提供自测题检验学习效果
- 常见故障排查指南
- 第三阶段（基础使用）预告

---

## 🤔 思考题

1. 你选择了哪种部署方案？为什么？
2. 部署过程中遇到了什么问题？如何解决的？
3. 你的服务器响应时间是多少？是否满意？
4. 你配置了域名和 HTTPS 吗？体验如何？

---

## 📋 部署完成度自评

```
完成以下任务后打勾：

□ 选择并准备了部署环境
□ 成功安装 OpenClaw
□ 配置了开机自启动
□ 能从外网访问
□ 配置了安全加固
□ 配置了备份方案
□ 完成了功能测试

完成度：___/7

7/7：完美！可以进入第 12 篇
5-6/7：良好，继续完成剩余配置
4/7 以下：建议重新阅读本篇，完成部署
```

---

## 🔧 服务器部署命令速查

```bash
# SSH 连接
ssh user@server-ip

# 安装 Node.js
curl -fsSL https://deb.nodesource.com/setup_20.x | bash -
apt install -y nodejs git

# 安装 OpenClaw
npm install -g openclaw

# 启动服务
openclaw gateway start

# 配置 systemd 自启
sudo systemctl enable openclaw
sudo systemctl start openclaw

# 查看状态
sudo systemctl status openclaw

# 查看日志
sudo journalctl -u openclaw -f

# 配置防火墙
ufw allow 3000/tcp
ufw enable

# 系统监控
htop
df -h
free -h
```

---

**🎉 恭喜！你的 OpenClaw 已经部署到服务器，可以 24 小时运行了！**

---

*📅 创建时间：2026-03-26 09:00*  
*📂 文件位置：`tutorials/openclaw-series/11-deploy-to-server.md`*
