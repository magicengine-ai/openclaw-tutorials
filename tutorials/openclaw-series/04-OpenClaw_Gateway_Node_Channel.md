# 第 04 篇：OpenClaw 的架构组成 —— Gateway、Node、Channel

> 💡 **学习时长**：10 分钟 | **难度**：⭐⭐ 入门 | **阶段**：基础篇

---

Config warnings:\n- plugins.entries.feishu: plugin feishu: duplicate plugin id detected; later plugin may be overridden (/home/jason/.openclaw/extensions/feishu/index.ts)
[plugins] plugins.allow is empty; discovered non-bundled plugins may auto-load: feishu (/home/jason/.openclaw/extensions/feishu/index.ts), openclaw-lark (/home/jason/.openclaw/extensions/openclaw-lark/index.js). Set plugins.allow to explicit trusted ids.
[plugins] feishu_doc: Registered feishu_doc, feishu_app_scopes
[plugins] feishu_chat: Registered feishu_chat tool
[plugins] feishu_wiki: Registered feishu_wiki tool
[plugins] feishu_drive: Registered feishu_drive tool
[plugins] feishu_bitable: Registered bitable tools
[plugins] feishu_chat: Registered feishu_chat, feishu_chat_members
[plugins] feishu_im: Registered feishu_im_user_message, feishu_im_user_fetch_resource, feishu_im_user_get_messages, feishu_im_user_get_thread_messages, feishu_im_user_search_messages
[plugins] feishu_im: Registered feishu_im_bot_image
[plugins] Registered all OAPI tools (calendar, task, bitable, search, drive, wiki, sheets, im)
[plugins] feishu_oauth: Registered feishu_oauth tool
[plugins] feishu_oauth_batch_auth: Registered feishu_oauth_batch_auth tool
[plugins] openclaw-lark: loaded without install/load-path provenance; treat as untracked local code and pin trust via plugins.allow or install records (/home/jason/.openclaw/extensions/openclaw-lark/index.js)
error: unknown command 'ask'

---

*📅 创建时间：2026-03-26 04:39*  
*📂 文件位置：`tutorials/openclaw-series/04-OpenClaw_Gateway_Node_Channel.md`*
