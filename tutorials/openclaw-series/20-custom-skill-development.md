# 第 20 篇：自定义 Skill —— 编写你的第一个技能

> 💡 **学习时长**：30 分钟 | **难度**：⭐⭐⭐ 中等 | **阶段**：进阶技能

---

## 🎯 本篇你将学到

- Skill 开发基础和前提知识
- Skill 的目录结构和规范
- 编写第一个 Skill 的完整流程
- 测试和调试技巧
- 发布和分享 Skills

---

## 1. Skill 开发基础

### 1.1 什么是自定义 Skill？

```
┌─────────────────────────────────────────────────────────┐
│              自定义 Skill                                │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  自定义 Skill = 你自己开发的能力扩展包                   │
│                                                         │
│  为什么要自定义？                                       │
│  • 现有 Skills 无法满足你的需求                         │
│  • 你有特定的业务流程需要自动化                         │
│  • 你想连接自己的 API 或服务                            │
│  • 你想与团队共享特定能力                               │
│                                                         │
│  开发难度：                                             │
│  • 基础 Skill：⭐⭐（有编程基础即可）                    │
│  • 进阶 Skill：⭐⭐⭐（需要 API 开发经验）                 │
│  • 复杂 Skill：⭐⭐⭐⭐（需要系统设计能力）                │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

### 1.2 开发前提知识

| 知识领域 | 要求 | 重要性 |
|----------|------|--------|
| **JavaScript/TypeScript** | 基础语法 | ⭐⭐⭐⭐⭐ |
| **Node.js** | 基础使用 | ⭐⭐⭐⭐⭐ |
| **API 开发** | 了解 HTTP/REST | ⭐⭐⭐⭐ |
| **Git** | 基础操作 | ⭐⭐⭐ |
| **OpenClaw 工具系统** | 了解工具调用 | ⭐⭐⭐⭐⭐ |

### 1.3 Skill 能做什么？

```
┌─────────────────────────────────────────────────────────┐
│              Skill 能力范围                              │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  ✅ 可以做的：                                          │
│  • 添加新的工具（Tools）                                │
│  • 封装复杂的工作流程                                   │
│  • 连接外部 API 和服务                                  │
│  • 定义专业的提示词模板                                 │
│  • 创建自定义命令                                       │
│                                                         │
│  ⚠️ 有限制的：                                          │
│  • 不能修改 OpenClaw 核心代码                           │
│  • 不能访问受限的系统资源                               │
│  • 需要遵守安全规范                                     │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 2. Skill 结构规范

### 2.1 目录结构

```
my-first-skill/
├── SKILL.md              # Skill 描述文档（必填）
├── package.json          # Node.js 项目配置（必填）
├── index.ts              # Skill 主入口（必填）
├── index.js              # 编译后的 JavaScript（运行时）
├── tools/                # 工具定义目录
│   ├── hello.ts          # 示例工具
│   └── custom.ts         # 自定义工具
├── hooks/                # 钩子函数目录（可选）
│   └── openclaw/
│       └── HOOK.md
├── references/           # 参考资料目录（可选）
│   └── api-docs.md
├── assets/               # 资源文件目录（可选）
│   └── templates/
└── README.md             # 使用说明（推荐）
```

### 2.2 SKILL.md 规范

```markdown
# Skill 名称

Skill 描述，说明这个 Skill 是做什么的。

## 功能

- 功能 1 说明
- 功能 2 说明
- 功能 3 说明

## 使用示例

```
用户：使用示例命令
Skill: 响应内容
```

## 配置要求

- 需要的 API Key
- 环境变量
- 其他依赖

## 版本历史

- v1.0.0 - 初始版本
```

### 2.3 package.json 规范

```json
{
  "name": "my-first-skill",
  "version": "1.0.0",
  "description": "我的第一个 OpenClaw Skill",
  "main": "index.js",
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch"
  },
  "keywords": ["openclaw", "skill"],
  "author": "你的名字",
  "license": "MIT",
  "dependencies": {
    "axios": "^1.6.0"
  },
  "devDependencies": {
    "typescript": "^5.0.0",
    "@types/node": "^20.0.0"
  }
}
```

### 2.4 index.ts 入口文件

```typescript
// index.ts - Skill 主入口

import { SkillDefinition } from '@openclaw/skill-sdk';

// 定义 Skill
const skill: SkillDefinition = {
  id: 'my-first-skill',
  name: '我的第一个 Skill',
  version: '1.0.0',
  description: '这是一个示例 Skill',
  
  // 注册工具
  tools: [
    {
      name: 'hello',
      description: '打招呼工具',
      handler: async (args: any) => {
        return `你好，${args.name || '世界'}!`;
      }
    }
  ],
  
  // 生命周期钩子（可选）
  hooks: {
    onEnable: async () => {
      console.log('Skill 已启用');
    },
    onDisable: async () => {
      console.log('Skill 已禁用');
    }
  }
};

export default skill;
```

---

## 3. 编写第一个 Skill

### 3.1 实战：天气查询 Skill

让我们创建一个完整的天气查询 Skill。

**步骤 1：创建项目目录**

```bash
# 创建 Skill 目录
mkdir -p ~/.openclaw/skills/weather-skill
cd ~/.openclaw/skills/weather-skill

# 初始化项目
npm init -y
```

**步骤 2：创建 SKILL.md**

```markdown
# Weather Skill

天气查询 Skill，可以查询全球城市的当前天气和预报。

## 功能

- 查询当前天气
- 查询 7 天预报
- 设置天气提醒

## 使用示例

```
用户：北京今天天气怎么样？
Skill: 北京今天晴，15-25°C...

用户：设置下雨提醒
Skill: 已设置，下雨时会通知你
```

## 配置要求

- OpenWeatherMap API Key（免费）
- 获取地址：https://openweathermap.org/api

## 版本历史

- v1.0.0 - 初始版本
```

**步骤 3：安装依赖**

```bash
npm install axios
npm install --save-dev typescript @types/node
```

**步骤 4：创建 TypeScript 配置**

```json
// tsconfig.json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "commonjs",
    "outDir": "./",
    "rootDir": "./",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true
  },
  "include": ["*.ts"],
  "exclude": ["node_modules"]
}
```

**步骤 5：编写主入口**

```typescript
// index.ts

import axios from 'axios';

const skill = {
  id: 'weather-skill',
  name: 'Weather Skill',
  version: '1.0.0',
  description: '天气查询 Skill',
  
  tools: [
    {
      name: 'get_weather',
      description: '查询指定城市的当前天气',
      parameters: {
        city: {
          type: 'string',
          description: '城市名称',
          required: true
        }
      },
      handler: async (args: any) => {
        const apiKey = process.env.OPENWEATHER_API_KEY;
        const city = args.city;
        
        if (!apiKey) {
          return '错误：未配置 API Key';
        }
        
        try {
          const response = await axios.get(
            `https://api.openweathermap.org/data/2.5/weather`,
            {
              params: {
                q: city,
                appid: apiKey,
                units: 'metric',
                lang: 'zh_cn'
              }
            }
          );
          
          const data = response.data;
          
          return `
🌤️ ${data.name} 天气

温度：${data.main.temp}°C
体感：${data.main.feels_like}°C
天气：${data.weather[0].description}
湿度：${data.main.humidity}%
风速：${data.wind.speed} m/s
          `.trim();
          
        } catch (error: any) {
          return `查询失败：${error.message}`;
        }
      }
    },
    
    {
      name: 'get_forecast',
      description: '查询指定城市的 7 天天气预报',
      parameters: {
        city: {
          type: 'string',
          description: '城市名称',
          required: true
        }
      },
      handler: async (args: any) => {
        const apiKey = process.env.OPENWEATHER_API_KEY;
        const city = args.city;
        
        // 类似实现...
        return '7 天预报内容';
      }
    }
  ]
};

export default skill;
```

**步骤 6：编译 TypeScript**

```bash
npx tsc
```

**步骤 7：配置 API Key**

```bash
# 添加环境变量
echo 'export OPENWEATHER_API_KEY="your-api-key"' >> ~/.bashrc
source ~/.bashrc
```

**步骤 8：启用 Skill**

```bash
# 在 OpenClaw 中启用
openclaw skills enable weather-skill
```

---

### 3.2 实战：待办事项 Skill

创建另一个实用的 Skill。

```typescript
// todo-skill/index.ts

const skill = {
  id: 'todo-skill',
  name: 'Todo Skill',
  version: '1.0.0',
  description: '待办事项管理 Skill',
  
  tools: [
    {
      name: 'add_todo',
      description: '添加一个新的待办事项',
      parameters: {
        task: {
          type: 'string',
          description: '任务内容',
          required: true
        },
        priority: {
          type: 'string',
          description: '优先级：high/medium/low',
          required: false,
          default: 'medium'
        },
        due: {
          type: 'string',
          description: '截止时间',
          required: false
        }
      },
      handler: async (args: any) => {
        // 读取现有待办
        const todos = await readTodos();
        
        // 添加新待办
        const newTodo = {
          id: Date.now(),
          task: args.task,
          priority: args.priority,
          due: args.due,
          completed: false,
          createdAt: new Date().toISOString()
        };
        
        todos.push(newTodo);
        
        // 保存
        await saveTodos(todos);
        
        return `✅ 待办已添加：
        
任务：${args.task}
优先级：${args.priority}
${args.due ? `截止：${args.due}` : ''}
        
使用 "查看待办" 来查看所有任务。`;
      }
    },
    
    {
      name: 'list_todos',
      description: '查看所有待办事项',
      parameters: {
        status: {
          type: 'string',
          description: '筛选状态：all/pending/completed',
          required: false,
          default: 'all'
        }
      },
      handler: async (args: any) => {
        const todos = await readTodos();
        
        // 筛选
        let filtered = todos;
        if (args.status === 'pending') {
          filtered = todos.filter(t => !t.completed);
        } else if (args.status === 'completed') {
          filtered = todos.filter(t => t.completed);
        }
        
        // 格式化输出
        let output = '📋 待办事项\n\n';
        
        if (filtered.length === 0) {
          output += '暂无待办事项。';
        } else {
          filtered.forEach((todo: any, index: number) => {
            const icon = todo.completed ? '✅' : '⬜';
            const priorityIcon = {
              high: '🔴',
              medium: '🟡',
              low: '🟢'
            }[todo.priority];
            
            output += `${index + 1}. ${icon} ${priorityIcon} ${todo.task}\n`;
            if (todo.due) {
              output += `   截止：${todo.due}\n`;
            }
            output += '\n';
          });
        }
        
        return output;
      }
    },
    
    {
      name: 'complete_todo',
      description: '标记待办事项为已完成',
      parameters: {
        id: {
          type: 'number',
          description: '待办事项 ID',
          required: true
        }
      },
      handler: async (args: any) => {
        const todos = await readTodos();
        const todo = todos.find((t: any) => t.id === args.id);
        
        if (!todo) {
          return '未找到该待办事项。';
        }
        
        todo.completed = true;
        await saveTodos(todos);
        
        return `✅ 已完成：${todo.task}`;
      }
    }
  ]
};

// 辅助函数
async function readTodos(): Promise<any[]> {
  const fs = require('fs');
  const path = '~/.openclaw/skills/todo-skill/todos.json';
  
  try {
    const data = fs.readFileSync(path, 'utf-8');
    return JSON.parse(data);
  } catch {
    return [];
  }
}

async function saveTodos(todos: any[]): Promise<void> {
  const fs = require('fs');
  const path = '~/.openclaw/skills/todo-skill/todos.json';
  fs.writeFileSync(path, JSON.stringify(todos, null, 2));
}

export default skill;
```

---

## 4. 测试和调试

### 4.1 本地测试

```bash
# 开发模式（自动编译）
npm run dev

# 手动测试工具
node -e "
const skill = require('./index.js').default;
skill.tools[0].handler({ city: '北京' })
  .then(console.log)
  .catch(console.error);
"
```

### 4.2 OpenClaw 中测试

```
# 在 OpenClaw 对话中测试

你：使用 weather-skill 查询北京天气

OpenClaw: 好的，正在查询...

🌤️ 北京 天气

温度：20°C
体感：18°C
天气：晴
湿度：45%
风速：3 m/s

✅ Skill 工作正常！
```

### 4.3 调试技巧

```typescript
// 添加日志输出

handler: async (args: any) => {
  console.log('[Weather Skill] 收到请求:', args);
  
  try {
    console.log('[Weather Skill] 调用 API...');
    const response = await axios.get(...);
    console.log('[Weather Skill] API 响应:', response.data);
    
    return formatWeather(response.data);
    
  } catch (error: any) {
    console.error('[Weather Skill] 错误:', error.message);
    throw error;
  }
}
```

### 4.4 常见错误排查

| 错误 | 可能原因 | 解决方案 |
|------|----------|----------|
| Skill 未加载 | 路径错误 | 检查 skills 目录配置 |
| 工具不识别 | 未正确注册 | 检查 tools 数组定义 |
| API 调用失败 | Key 错误或网络 | 检查 API Key 和网络 |
| TypeScript 编译错误 | 类型错误 | 检查类型定义 |
| 权限错误 | 文件访问受限 | 检查文件权限配置 |

---

## 5. 发布和分享

### 5.1 准备发布

```
发布前检查清单：

□ SKILL.md 文档完整
□ package.json 信息准确
□ 代码已编译（index.js 存在）
□ 测试通过
□ README.md 使用说明
□ 许可证文件（LICENSE）
```

### 5.2 发布到 SkillHub

```bash
# 登录 SkillHub
openclaw skills login

# 发布 Skill
openclaw skills publish

# 或手动提交到 GitHub
# 然后提交到 SkillHub 索引
```

### 5.3 分享给团队

```
方式 1：Git 仓库
git clone <你的仓库>
openclaw skills enable <skill-name>

方式 2：npm 包
npm install your-skill
openclaw skills enable your-skill

方式 3：直接复制
复制 Skill 目录到 ~/.openclaw/skills/
openclaw skills enable <skill-name>
```

### 5.4 版本管理

```json
// package.json 中的版本
{
  "version": "1.0.0"  // 主版本。次版本。修订版本
     ↑      ↑    ↑
     |      |    └─  bug 修复
     |      └─ 新功能（向后兼容）
     └─ 重大变更（可能不兼容）
}

// 更新日志
// CHANGELOG.md
## [1.0.0] - 2026-03-26
### Added
- 初始版本
- 天气查询功能
- 天气预报功能
```

---

## 6. Skill 开发最佳实践

### 6.1 代码规范

```typescript
// ✅ 推荐做法

// 1. 使用 TypeScript
const handler = async (args: WeatherArgs): Promise<string> => {
  // 类型安全
};

// 2. 错误处理
try {
  const result = await api.call();
  return formatResult(result);
} catch (error) {
  console.error('Error:', error);
  return '操作失败，请稍后重试';
}

// 3. 日志输出
console.log('[Skill] 操作开始');
console.log('[Skill] 操作完成');

// 4. 配置外部化
const apiKey = process.env.API_KEY;
```

### 6.2 安全规范

```typescript
// ✅ 推荐做法

// 1. 不硬编码敏感信息
const apiKey = process.env.API_KEY;  // ✅
const apiKey = 'sk-xxx';             // ❌

// 2. 验证输入参数
if (!args.city || typeof args.city !== 'string') {
  return '错误：城市名称不能为空';
}

// 3. 限制资源访问
// 只访问允许的文件路径
const safePath = path.resolve(allowedDir, filename);

// 4. 速率限制
// 避免频繁调用 API
```

### 6.3 文档规范

```markdown
# ✅ 好的文档

## 功能说明
清晰说明每个功能的作用

## 使用示例
提供实际可用的对话示例

## 配置要求
列出所有需要的配置项

## 常见问题
解答用户可能遇到的问题

## 版本历史
记录每次变更
```

---

## 7. Skill 模板

### 7.1 基础模板

```typescript
// 复制这个模板开始你的 Skill 开发

import { SkillDefinition, Tool } from '@openclaw/skill-sdk';

interface MySkillArgs {
  // 定义参数类型
}

const skill: SkillDefinition = {
  id: 'my-skill',
  name: 'My Skill',
  version: '1.0.0',
  description: 'Skill 描述',
  
  tools: [
    {
      name: 'my_tool',
      description: '工具描述',
      parameters: {
        param1: {
          type: 'string',
          description: '参数说明',
          required: true
        }
      },
      handler: async (args: any) => {
        // 实现逻辑
        return '结果';
      }
    }
  ]
};

export default skill;
```

### 7.2 API 集成模板

```typescript
// API 集成 Skill 模板

import axios from 'axios';

const skill = {
  id: 'api-skill',
  name: 'API Integration Skill',
  
  tools: [
    {
      name: 'call_api',
      description: '调用外部 API',
      handler: async (args: any) => {
        const apiKey = process.env.API_KEY;
        
        const response = await axios.get('https://api.example.com/data', {
          headers: {
            'Authorization': `Bearer ${apiKey}`
          },
          params: args
        });
        
        return formatResponse(response.data);
      }
    }
  ]
};
```

---

## 8. 学习资源

### 8.1 官方文档

```
• OpenClaw Skill SDK 文档
  https://docs.openclaw.ai/skills

• Skill 开发指南
  https://docs.openclaw.ai/skill-development

• API 参考
  https://docs.openclaw.ai/api-reference
```

### 8.2 示例 Skills

```
• stock-analysis
  ~/.openclaw/skills/stock-analysis/

• xiaohongshu-ops
  ~/.openclaw/skills/xiaohongshu-ops/

• pdf-generator
  ~/.openclaw/skills/pdf-generator/
```

### 8.3 社区资源

```
• GitHub Discussions
• Discord 社区频道
• Skill 开发者微信群
```

---

## 9. 本篇总结

### 📌 核心要点

1. **Skill 结构**：SKILL.md、package.json、index.ts 是必需的
2. **开发流程**：创建目录→编写代码→编译→测试→启用
3. **工具定义**：name、description、parameters、handler
4. **测试调试**：本地测试 + OpenClaw 中测试
5. **发布分享**：SkillHub 发布或团队内分享
6. **最佳实践**：代码规范、安全规范、文档规范

### ✅ 学完这篇你应该能：

- [ ] 理解 Skill 的结构和规范
- [ ] 创建简单的 Skill 项目
- [ ] 编写工具定义和 handler
- [ ] 测试和调试 Skill
- [ ] 发布和分享 Skill
- [ ] 遵循开发最佳实践

---

## 📚 下一篇预告

**第 21 篇：子 Agent 系统 —— 多 Agent 协作**

我们会深入讲解：
- 什么是子 Agent 系统
- 如何 spawn 子 Agent
- 多 Agent 任务分发
- 结果汇总和协调
- 实际应用场景

---

## 🤔 思考题

1. 你想开发什么类型的 Skill？
2. 你的 Skill 会解决什么具体问题？
3. 你觉得 Skill 开发最大的挑战是什么？
4. 你打算如何分享你的 Skill？

---

## 📋 Skill 开发能力自评

```
完成以下任务后打勾：

□ 理解了 Skill 结构和规范
□ 创建了 Skill 项目目录
□ 编写了 SKILL.md 文档
□ 实现了至少一个工具
□ 成功编译了 TypeScript
□ 在 OpenClaw 中测试了 Skill
□ 理解了最佳实践

完成度：___/7

7/7：优秀！可以进入第 21 篇
5-6/7：良好，继续练习
4/7 以下：建议重新阅读本篇，动手实践
```

---

## 🔧 Skill 开发命令速查

```bash
# 创建 Skill
mkdir -p ~/.openclaw/skills/my-skill
cd ~/.openclaw/skills/my-skill
npm init -y

# 安装依赖
npm install axios
npm install --save-dev typescript @types/node

# 编译
npx tsc

# 开发模式
npm run dev

# 启用 Skill
openclaw skills enable my-skill

# 测试
openclaw ask "使用 my-skill 做 xxx"

# 发布
openclaw skills publish
```

---

**🎉 恭喜！你已经掌握了 OpenClaw Skill 的开发方法！**

---

*📅 创建时间：2026-03-26 13:30*  
*📂 文件位置：`tutorials/openclaw-series/20-custom-skill-development.md`*
