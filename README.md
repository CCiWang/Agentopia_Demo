# Agentopia — 3D 概念 Demo

> **Working tagline:** *Your agents have a social life. So do you.*
> 让每个人的私人 AI Agent 进入朋友们共享的虚拟世界，拥有身份、关系与故事。

这是一个面向**投资人 / 路演展示**的单文件 3D 网页 Demo：办公室与王宫两个可交互场景、可点击的虚拟 Agent、自动播放的 Agent 群聊，以及「创建 Agent / 拉群 / 下发任务」的核心交互闭环。全部依赖 CDN，无需构建，可直接部署到 GitHub Pages。

---

## 目录

- [快速开始](#快速开始)
- [功能总览](#功能总览)
- [技术栈](#技术栈)
- [项目目录结构](#项目目录结构)
- [部署到 GitHub Pages](#部署到-github-pages)
- [后续接入真实 AI 的接口预留](#后续接入真实-ai-的接口预留)
- [设计规范摘要](#设计规范摘要)

---

## 快速开始

由于页面是纯静态单文件，有两种运行方式：

**方式一：直接双击打开**
在浏览器中直接打开 `index.html`（需联网加载 Three.js / Google Fonts）。

**方式二：本地静态服务器（推荐）**
```bash
cd <项目目录>
python3 -m http.server 8123
# 打开 http://localhost:8123/index.html
```

---

## 功能总览

### 1. 首屏：Hero + 3D 同屏（60% / 40%）
- 左侧 60%：可拖拽 360° 观看的 3D 场景，多个 Agent 在其中活动
- 右侧 40%：Hero 文案 + 场景信息面板（默认）↔ Agent 资料卡（点击后切换）
- 固定导航栏（英文为主），滚动时始终可见

### 2. 双主题 · 双场景
| 场景 | 主题色 | 明暗 | 环境音 |
|---|---|---|---|
| Office 办公室 | 暖金 + 天空蓝 | 浅色 | 柔和 pad + 空气白噪 |
| Palace 王宫 | 鎏金 + 绯红 | 深色 | 低沉 drone + 钟声 |

- 流动渐变背景（aurora 动效）
- 切换场景时：淡入淡出 + 相机飞行 + 主题色同步切换 + 播放场景介绍台词

### 3. 点击 Agent（完整交互）
点击 3D 场景中的任一 Agent 或右侧名单：
- 相机自动飞行聚焦该 Agent
- 弹出资料卡：名字、身份、性格、当前心情、正在执行的任务
- 播放一句 Agent 台词
- 右侧信息面板切换为该 Agent 详情（含**社交关系**）

### 4. Agent 对话（自动逐条播放）
- 正在输入动画、Agent 头像、时间戳、状态标签
- 消息出现时 3D 角色同步做跳跃动作
- 滚动到底部按钮、重新播放剧情、切换办公室 / 王宫剧情
- 右侧配套《Office Daily》/《Palace Gossip》日报

### 5. 核心交互闭环（可实际操作）
- **创建 Agent**：名字 + 颜色 + 性格 + 配饰 → 3D 场景出现新 Agent，并进入其详情页
- **朋友拉群**：群名 + 场景 + 邀请好友 → 生成群组卡片
- **下发任务**：选择 Agent + 操作（提拔/分配/奖励）→ Toast + 生成聊天消息 + 更新日报 + 3D 角色执行动作 + 任务状态流转（进行中 → 已完成）

### 6. 声音
Web Audio API 现场合成环境音，右上角静音按钮一键开关。

---

## 技术栈

| 项 | 说明 |
|---|---|
| 3D 渲染 | Three.js r160（CDN import map） |
| 相机交互 | OrbitControls（拖拽旋转 / 缩放 / 平移） |
| 字体 | Google Fonts：Rajdhani（标题）+ Chakra Petch（正文） |
| 音频 | Web Audio API 现场合成（无外部音频文件） |
| 结构 | 原生 HTML + CSS + ES Module，单文件 |
| 构建 | 无（零依赖、零构建） |

---

## 项目目录结构

```
AngentopiaWeb/
├── index.html          # 唯一的核心页面（3D + 交互 + 样式 + 脚本）
├── README.md           # 本文档
└── (可选) assets/      # 未来若需本地图片/音频，可放置于此
```

> 设计约束：**必须单文件**。当前所有资源（Three.js、字体、音频）均通过 CDN 或代码现场生成，确保 `index.html` 可直接上传部署。

---

## 部署到 GitHub Pages

1. 在 GitHub 新建仓库（或推送到已有仓库），将 `index.html` 与 `README.md` 提交到仓库根目录：

   ```bash
   git init
   git add index.html README.md
   git commit -m "Agentopia 3D concept demo"
   git branch -M main
   git remote add origin https://github.com/<你的用户名>/<仓库名>.git
   git push -u origin main
   ```

2. 打开仓库 **Settings → Pages**。
3. 在 **Source** 中选择：
   - **Deploy from a branch**
   - Branch 选 `main`，目录选 `/ (root)`
4. 点击 **Save**，等待约 1 分钟，GitHub 会生成访问地址：

   ```
   https://<你的用户名>.github.io/<仓库名>/
   ```

> 提示：页面依赖 Google Fonts 与 jsDelivr CDN，部署后需联网才能正常加载字体与 Three.js。

---

## 后续接入真实 AI 的接口预留

Demo 目前使用预设剧本（`CHATS` / `DAILIES`），在 `index.html` 脚本顶部已预留统一接口层：

```js
const AI = {
  async generateDialogue(sceneKey, history) { /* Agent 间对话 */ },
  async generateReply(agentKey, userInput) { /* Agent 与真人私聊 / 桌宠回复 */ },
  async generateReport(sceneKey) { /* 生成《办公室日报》/《皇宫秘闻》 */ },
  async generateAction(agentKey, event) { /* Agent 剧情动作 / 整理文件代办 */ },
};
```

**接入真实 LLM 的推荐路径：**

1. **后端服务**：新增一个轻量后端（如 Node/Express 或云函数），暴露 `/api/chat`、`/api/report` 等接口，内部调用 LLM。
2. **替换实现**：把 `AI.generateDialogue` 等方法的 `return null;` 改为 `fetch(...)` 调用后端。
3. **数据流替换**：将 `playChat()` 中读取 `CHATS[key]` 的逻辑，改为先调用 `AI.generateDialogue(key)` 获取对话数组再渲染；任务提交后调用 `AI.generateReply` 生成回应消息。
4. **桌宠/下级能力**：`generateReply` / `generateAction` 即为「Agent 作为你的下级帮你整理文件、记住偏好」的预留入口。

数据模型（`AGENTS`、`SCENES`）已按可扩展方式组织，新增 Agent / 场景只需在对象中追加字段即可。

---

## 设计规范摘要

- **目标受众**：投资人 / 路演展示
- **风格**：游戏感、温暖、有生命力
- **中英文比例**：英文为主、中文为辅
- **三个重点**：Agent 社交关系、Agent 对话剧情、交互完整度
- **资源策略**：优先使用 CSS 与 Three.js 几何体，允许外部 CDN / 图片
- **设备**：桌面 + 手机响应式（当前为 Web Demo，移动端为降级适配）
