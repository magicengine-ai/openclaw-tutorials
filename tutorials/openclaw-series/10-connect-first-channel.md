# 第 10 篇：连接你的第一个 Channel —— Telegram/WhatsApp/Discord/飞书

> 💡 **学习时长**：20 分钟 | **难度**：⭐⭐ 入门 | **阶段**：安装与部署

---

## 🎯 本篇你将学到

- 什么是 Channel 以及为什么需要它
- 四种主流消息渠道的详细配置方法
- 获取每种渠道所需的凭证和 Token
- 测试消息收发功能
- 排查渠道连接问题
- 多渠道管理技巧

---

## 1. 什么是 Channel？

### 1.1 Channel 的作用

```
┌─────────────────────────────────────────────────────────┐
│                  Channel 是什么？                        │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Channel = 消息渠道 / 通信渠道                          │
│                                                         │
│  它是你和 OpenClaw 之间的"电话线"：                      │
│                                                         │
│     你 ←─── Channel ───→ OpenClaw Gateway              │
│   （飞书/Telegram）        （AI 助手）                   │
│                                                         │
│  没有 Channel，OpenClaw 就无法和你通信！                │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 1.2 支持的 Channel 类型

| 渠道 | 类型 | 难度 | 国内访问 | 推荐度 |
|------|------|------|----------|--------|
| **飞书 (Lark)** | 企业 IM | ⭐⭐ | ✅ 优秀 | ⭐⭐⭐⭐⭐ |
| **Telegram** | 即时通讯 | ⭐ | ⚠️ 需要网络 | ⭐⭐⭐⭐ |
| **Discord** | 社区聊天 | ⭐ | ⚠️ 需要网络 | ⭐⭐⭐⭐ |
| **WhatsApp** | 即时通讯 | ⭐⭐ | ⚠️ 需要网络 | ⭐⭐⭐ |
| **Slack** | 企业 IM | ⭐⭐ | ⚠️ 需要网络 | ⭐⭐⭐ |
| **微信** | 即时通讯 | ⭐⭐⭐ | ✅ 优秀 | ⭐⭐ |

**推荐选择：**
```
国内用户 → 飞书（首选）
海外用户 → Telegram/Discord
企业用户 → 飞书/Slack
```

---

## 2. 飞书 (Lark) 配置指南

### 2.1 为什么选择飞书？

```
✅ 优点：
• 国内访问速度快
• 完全免费（个人版够用）
• API 功能强大
• 支持消息/日历/任务/文档
• 企业级稳定性

⚠️ 注意：
• 需要注册飞书账号
• 需要创建企业（一人企业即可）
```

### 2.2 完整配置步骤

#### 步骤 1：注册飞书账号

```
1. 访问：https://www.feishu.cn
2. 点击"免费注册"
3. 使用手机号注册
4. 完成验证
```

#### 步骤 2：创建企业

```
1. 登录飞书管理后台：https://www.feishu.cn/admin
2. 点击"创建企业"
3. 填写企业信息（个人使用可随便填）
4. 完成创建

💡 提示：一人企业完全免费，功能足够使用
```

#### 步骤 3：创建应用

```
1. 访问飞书开放平台：https://open.feishu.cn/app
2. 点击"创建企业自建应用"
3. 填写应用信息：
   - 应用名称：My OpenClaw
   - 应用图标：可选
   - 应用描述：我的 AI 助手
4. 点击"创建"
```

#### 步骤 4：获取凭证

```
创建应用后，在"凭证与基础信息"页面：
• App ID：cli_a1b2c3d4e5f6...
• App Secret：xxxxxxxxxxxxxxxx

⚠️ 重要：复制并保存这两个值，后续配置需要！
```

#### 步骤 5：配置权限

在"权限管理"页面，添加以下权限：

```
必选权限：
□ 发送消息 (im:message)
□ 读取用户信息 (contact:user:readonly)

可选权限（按需添加）：
□ 日历管理 (calendar:calendar)
□ 任务管理 (task:task)
□ 读取群组信息 (contact:group:readonly)
□ 订阅消息事件 (im:message:receive)

完成后点击"发布应用"
```

#### 步骤 6：配置事件订阅

```
1. 进入"事件订阅"页面
2. 开启"启用事件订阅"
3. 配置订阅地址：
   - 公网地址：https://your-domain.com/webhook/feishu
   - 本地测试：使用 ngrok 等工具暴露本地服务
4. 订阅以下事件：
   □ 接收消息 (im.message.receive_v1)
   □ 用户发送/取消快捷菜单 (im.message.menu_action)
5. 保存并等待验证完成
```

#### 步骤 7：添加机器人到聊天

```
方式 1：私聊
1. 在飞书搜索你的应用名称
2. 点击进入聊天
3. 发送消息测试

方式 2：群聊
1. 创建或选择一个群
2. 群设置 → 添加成员 → 添加机器人
3. 选择你的应用
4. 在群里 @机器人 测试
```

### 2.3 配置 OpenClaw

编辑 `~/.openclaw/config.json`：

```json
{
  "channels": [
    {
      "type": "feishu",
      "appId": "cli_a1b2c3d4e5f6",
      "appSecret": "xxxxxxxxxxxxxxxx",
      "encryptKey": "",
      "verificationToken": "",
      "enabled": true,
      "name": "飞书主渠道"
    }
  ]
}
```

### 2.4 测试连接

```bash
# 重启 Gateway 应用配置
openclaw gateway restart

# 查看日志
openclaw logs --tail 50

# 在飞书中发送消息测试
# 期望：1-3 秒内收到回复
```

---

## 3. Telegram 配置指南

### 3.1 为什么选择 Telegram？

```
✅ 优点：
• 配置简单，5 分钟搞定
• 完全免费
• API 友好，文档完善
• 支持机器人功能丰富
• 全球可用

⚠️ 注意：
• 国内需要网络环境
• 需要手机号注册
```

### 3.2 完整配置步骤

#### 步骤 1：注册 Telegram 账号

```
1. 下载 Telegram 应用
   - 手机：App Store / Google Play
   - 电脑：https://desktop.telegram.org
2. 使用手机号注册
3. 完成验证
```

#### 步骤 2：创建 Bot

```
1. 在 Telegram 搜索 @BotFather（官方机器人）
2. 发送 /start 开始对话
3. 发送 /newbot 创建新机器人
4. 按提示输入：
   - 机器人名称：My OpenClaw Bot
   - 机器人用户名：my_openclaw_bot（必须以 bot 结尾）
5. 保存返回的 Token

💡 Token 格式：123456789:ABCdefGHIjklMNOpqrsTUVwxyz
⚠️ 重要：Token 相当于密码，不要泄露！
```

#### 步骤 3：获取 Chat ID

```
方法 1：通过 API 获取
1. 在 Telegram 搜索你的机器人
2. 发送任意消息（如 /start）
3. 访问：https://api.telegram.org/bot<YOUR_TOKEN>/getUpdates
4. 在返回的 JSON 中找到 "chat":{"id": 123456789}
5. 保存 Chat ID

方法 2：使用 @userinfobot
1. 搜索 @userinfobot
2. 发送 /start
3. 它会返回你的 User ID
```

#### 步骤 4：测试机器人

```
在 Telegram 中：
1. 搜索你的机器人用户名
2. 发送 /start
3. 发送测试消息
4. 应该收到回复（配置完成后）
```

### 3.3 配置 OpenClaw

编辑 `~/.openclaw/config.json`：

```json
{
  "channels": [
    {
      "type": "telegram",
      "botToken": "123456789:ABCdefGHIjklMNOpqrsTUVwxyz",
      "enabled": true,
      "name": "Telegram 机器人",
      "pollingInterval": 1000
    }
  ]
}
```

| 参数 | 说明 | 必填 |
|------|------|------|
| `botToken` | BotFather 返回的 Token | 是 |
| `enabled` | 是否启用 | 是 |
| `name` | 渠道名称 | 否 |
| `pollingInterval` | 轮询间隔（毫秒） | 否，默认 1000 |

### 3.4 测试连接

```bash
# 重启 Gateway
openclaw gateway restart

# 查看日志
openclaw logs

# 在 Telegram 发送消息测试
# 期望：1-3 秒内收到回复
```

---

## 4. Discord 配置指南

### 4.1 为什么选择 Discord？

```
✅ 优点：
• 社区功能强大
• 支持服务器/频道管理
• 机器人生态丰富
• 完全免费
• 支持富媒体消息

⚠️ 注意：
• 国内需要网络环境
• 配置稍复杂
```

### 4.2 完整配置步骤

#### 步骤 1：注册 Discord 账号

```
1. 访问：https://discord.com
2. 点击"Register"注册
3. 填写邮箱、用户名、密码
4. 完成邮箱验证
```

#### 步骤 2：创建服务器（可选）

```
1. 登录后，点击左侧边栏的"+"
2. 选择"Create My Own"
3. 填写服务器名称：My OpenClaw Server
4. 点击"Create"

💡 如果已有服务器，可以跳过此步骤
```

#### 步骤 3：创建应用

```
1. 访问 Discord 开发者平台：https://discord.com/developers/applications
2. 点击"New Application"
3. 填写应用名称：My OpenClaw
4. 点击"Create"
```

#### 步骤 4：创建 Bot

```
1. 在应用页面，点击左侧"Bot"
2. 点击"Add Bot" → "Yes, do it!"
3. 在"Token"部分，点击"Reset Token"
4. 复制 Token（只显示一次！）

⚠️ 重要：保存 Token，后续配置需要！
```

#### 步骤 5：配置 Bot 权限

```
在 Bot 页面：
1. 关闭"Public Bot"（保持私有）
2. 开启"Message Content Intent"（读取消息内容）
3. 保存更改
```

#### 步骤 6：邀请 Bot 到服务器

```
1. 在应用页面，点击"OAuth2" → "URL Generator"
2. 选择 Scopes:
   □ bot
   □ applications.commands
3. 选择 Bot Permissions:
   □ Send Messages
   □ Read Messages/View Channels
   □ Embed Links
4. 复制生成的 URL
5. 在浏览器打开 URL
6. 选择服务器，点击"Authorize"
```

#### 步骤 7：获取服务器和频道 ID

```
方法 1：启用开发者模式
1. Discord 设置 → 高级 → 开启"开发者模式"
2. 右键点击服务器图标 → "Copy Server ID"
3. 右键点击频道 → "Copy Channel ID"

方法 2：通过 API 获取
访问：https://discord.com/api/users/@me/guilds
使用 Bot Token 授权后查看返回的服务器列表
```

### 4.3 配置 OpenClaw

编辑 `~/.openclaw/config.json`：

```json
{
  "channels": [
    {
      "type": "discord",
      "botToken": "xxxxxxxxxxxxxxxxxxxx.xxxxxx.xxxxxxxxxxxxxxxxxxxxx",
      "enabled": true,
      "name": "Discord 机器人",
      "guildId": "123456789012345678",
      "channelId": "987654321098765432"
    }
  ]
}
```

| 参数 | 说明 | 必填 |
|------|------|------|
| `botToken` | Bot Token | 是 |
| `enabled` | 是否启用 | 是 |
| `name` | 渠道名称 | 否 |
| `guildId` | 服务器 ID | 推荐 |
| `channelId` | 频道 ID | 推荐 |

### 4.4 测试连接

```bash
# 重启 Gateway
openclaw gateway restart

# 查看日志
openclaw logs

# 在 Discord 频道发送消息测试
# 期望：1-3 秒内收到回复
```

---

## 5. WhatsApp 配置指南

### 5.1 为什么选择 WhatsApp？

```
✅ 优点：
• 全球用户最多
• 移动端体验好
• 支持富媒体消息

⚠️ 注意：
• 配置较复杂
• 需要 Meta 开发者账号
• 国内需要网络环境
• 需要验证业务流程
```

### 5.2 配置步骤（简化版）

```
步骤 1：注册 Meta 开发者账号
访问：https://developers.facebook.com

步骤 2：创建应用
选择"Business"类型，创建应用

步骤 3：添加 WhatsApp 产品
在应用 dashboard 添加 WhatsApp 产品

步骤 4：获取凭证
- Phone Number ID
- Access Token
- Business Account ID

步骤 5：配置 Webhook
设置回调 URL 接收消息

步骤 6：验证号码
添加测试号码或提交业务验证
```

### 5.3 配置 OpenClaw

```json
{
  "channels": [
    {
      "type": "whatsapp",
      "phoneNumberId": "123456789012345",
      "accessToken": "EAABxxxxxxxxxxxxxxxxxxxx",
      "businessAccountId": "123456789012345",
      "enabled": true,
      "name": "WhatsApp 机器人"
    }
  ]
}
```

---

## 6. 多渠道配置

### 6.1 配置多个 Channel

你可以在 config.json 中配置多个渠道：

```json
{
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
      "enabled": true,
      "name": "Telegram 备用"
    },
    {
      "type": "discord",
      "botToken": "xxxxxxxxxxxxxxxxxxxx",
      "enabled": false,
      "name": "Discord（暂时禁用）"
    }
  ]
}
```

### 6.2 渠道管理技巧

```
✅ 推荐做法：
• 设置 1 个主渠道（最常用）
• 设置 1-2 个备用渠道
• 暂时不用的渠道设置 enabled: false
• 给每个渠道起有意义的名称

❌ 不推荐：
• 配置太多渠道（管理复杂）
• 所有渠道都启用（资源浪费）
• 渠道名称随意（难以区分）
```

---

## 7. 常见问题排查

### 7.1 通用问题

**Q1: Gateway 启动后收不到消息**

```
排查步骤：
1. 检查渠道 enabled: true
2. 检查凭证（Token/Secret）是否正确
3. 查看日志是否有错误
4. 确认消息发送到了正确的渠道

命令：
openclaw logs --level error
```

**Q2: 能收到消息但不回复**

```
排查步骤：
1. 检查 AI 模型配置（API Key 是否正确）
2. 查看日志是否有 API 调用错误
3. 测试模型连接：openclaw ask "你好"
4. 检查工具权限配置
```

**Q3: 回复很慢（超过 10 秒）**

```
可能原因：
• 网络延迟（特别是国外 API）
• 模型响应慢
• 服务器性能不足

解决方案：
• 使用国内模型（阿里云）
• 降低 contextLength
• 检查网络连接
```

### 7.2 飞书特定问题

**Q4: 飞书机器人收不到消息**

```
排查清单：
□ 应用是否已发布
□ 权限是否已配置
□ 事件订阅是否已启用
□ 订阅地址是否可访问
□ 机器人是否已添加到聊天

调试命令：
# 查看飞书插件日志
openclaw logs | grep feishu
```

**Q5: 飞书事件订阅验证失败**

```
解决方案：
1. 确保订阅地址可公网访问
2. 使用 ngrok 暴露本地服务：
   ngrok http 3000
3. 将 ngrok 地址填入订阅地址
4. 等待验证完成
```

### 7.3 Telegram 特定问题

**Q6: Telegram 机器人无响应**

```
排查步骤：
1. 检查 Bot Token 是否正确
2. 确认机器人已启动（@BotFather → /startbot）
3. 检查 Chat ID 是否正确
4. 查看网络是否可访问 Telegram API

测试 API：
curl https://api.telegram.org/bot<TOKEN>/getMe
```

### 7.4 Discord 特定问题

**Q7: Discord 机器人无法读取消息**

```
排查步骤：
1. 检查"Message Content Intent"是否开启
2. 确认 Bot 权限包含"Read Messages"
3. 检查频道权限设置
4. 确认 Bot 已添加到正确服务器
```

---

## 8. 渠道配置对比表

```
┌──────────────────────────────────────────────────────────────────┐
│                    渠道配置对比表                                 │
├─────────────┬──────────────┬──────────────┬──────────────────────┤
│    渠道     │   需要凭证   │   配置难度   │      适用场景        │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│ 飞书        │ App ID       │ ⭐⭐          │ 国内用户、企业用户   │
│             │ App Secret   │              │                      │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│ Telegram    │ Bot Token    │ ⭐            │ 个人用户、海外用户   │
│             │ Chat ID      │              │                      │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│ Discord     │ Bot Token    │ ⭐⭐          │ 社区用户、游戏玩家   │
│             │ Guild ID     │              │                      │
│             │ Channel ID   │              │                      │
├─────────────┼──────────────┼──────────────┼──────────────────────┤
│ WhatsApp    │ Phone ID     │ ⭐⭐⭐         │ 海外业务、客服场景   │
│             │ Access Token │              │                      │
│             │ Business ID  │              │                      │
└─────────────┴──────────────┴──────────────┴──────────────────────┘
```

---

## 9. 渠道测试清单

```
┌─────────────────────────────────────────────────────────┐
│              渠道连接测试清单 ✅                         │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  基础配置                                               │
│  □ 渠道已添加到 config.json                            │
│  □ enabled: true                                        │
│  □ 凭证（Token/Secret）已填写                          │
│  □ Gateway 已重启                                       │
│                                                         │
│  连接测试                                               │
│  □ 能发送消息到渠道                                     │
│  □ 渠道能收到消息                                       │
│  □ OpenClaw 能回复消息                                  │
│  □ 回复时间在 5 秒以内                                   │
│                                                         │
│  功能测试                                               │
│  □ 简单对话："你好"                                    │
│  □ 文件操作："帮我创建一个文件"                        │
│  □ 网络搜索："查一下今天的新闻"                        │
│                                                         │
│  日志检查                                               │
│  □ 无错误日志                                           │
│  □ 能看到消息接收日志                                   │
│  □ 能看到消息发送日志                                   │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 10. 本篇总结

### 📌 核心要点

1. **Channel 是通信桥梁**，没有它 OpenClaw 无法和你交流
2. **飞书最适合国内用户**，配置简单、访问快速
3. **Telegram 最适合个人用户**，5 分钟即可配置完成
4. **Discord 适合社区场景**，支持服务器/频道管理
5. **多渠道可以同时配置**，但建议 1 个主用 +1-2 个备用
6. **遇到问题先查日志**，大部分问题都有明确错误信息

### ✅ 学完这篇你应该能：

- [ ] 解释 Channel 的作用
- [ ] 配置飞书渠道（国内推荐）
- [ ] 配置 Telegram 渠道（海外推荐）
- [ ] 配置 Discord 渠道（社区场景）
- [ ] 配置 WhatsApp 渠道（可选）
- [ ] 配置多个渠道
- [ ] 排查渠道连接问题
- [ ] 完成渠道功能测试

---

## 📚 下一篇预告

**第 11 篇：部署到服务器 —— VPS/树莓派/家庭服务器**

我们会讲解：
- 为什么需要部署到服务器
- VPS 选择和配置（阿里云/腾讯云/AWS）
- 树莓派部署指南
- 家庭服务器部署
- 域名和 HTTPS 配置
- 开机自启动配置

---

## 🤔 思考题

1. 你选择了哪个消息渠道？为什么？
2. 配置过程中遇到了什么问题？如何解决的？
3. 你打算只使用一个渠道还是配置多个？
4. 你的渠道响应时间是多少？是否满意？

---

## 📋 渠道配置完成度自评

```
完成以下任务后打勾：

□ 选择并注册了消息渠道账号
□ 获取了必要的凭证（Token/Secret 等）
□ 在 config.json 中配置了渠道
□ 重启了 Gateway
□ 成功发送测试消息
□ 收到了 OpenClaw 的回复
□ 完成了功能测试（对话/搜索等）

完成度：___/7

7/7：完美！可以进入第 11 篇
5-6/7：良好，继续完成剩余测试
4/7 以下：建议重新阅读本篇，完成配置
```

---

## 🔧 渠道配置命令速查

```bash
# 编辑配置
nano ~/.openclaw/config.json

# 重启 Gateway（应用新配置）
openclaw gateway restart

# 查看日志
openclaw logs --tail 100

# 查看错误日志
openclaw logs --level error

# 测试对话
openclaw ask "你好"

# 检查 Gateway 状态
openclaw gateway status
```

---

**🎉 恭喜！你的 OpenClaw 已经可以和你通信了！**

---

*📅 创建时间：2026-03-26 08:30*  
*📂 文件位置：`tutorials/openclaw-series/10-connect-first-channel.md`*
