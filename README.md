<p align="center">
  <strong>简体中文</strong>
</p>

<div align="center">

<img src="./docs/logo.png" alt="Auto-Dramaflow Web Logo" height="120"/>

# Auto-Dramaflow Web

  <p align="center">
    <b>
      Auto-Dramaflow 前端应用
      <br />
      基于 Vue 3 + TypeScript + Vite 构建的现代化 Web 界面
      <br />
      AI短剧工厂的用户操作端 &#x1F3A8;
    </b>
  </p>
  <p align="center">
    <a href="https://www.apache.org/licenses/LICENSE-2.0" target="_blank">
      <img src="https://img.shields.io/badge/License-Apache%202.0-blue.svg?style=for-the-badge" alt="License Badge" />
    </a>
  </p>
  <p align="center">
    <img src="https://ziadoua.github.io/m3-Markdown-Badges/badges/Vue/vue3.svg" alt="Vue 3" />&nbsp;
    <img src="https://ziadoua.github.io/m3-Markdown-Badges/badges/TypeScript/typescript2.svg" alt="TypeScript" />&nbsp;
    <img src="https://ziadoua.github.io/m3-Markdown-Badges/badges/Vite/vite2.svg" alt="Vite" />
  </p>

  > &#x1F3AF; **现代化前端架构**：采用 Vue 3 组合式 API、TypeScript 类型安全、Vite 极速构建，打造流畅的 AI 短剧创作体验！
  >
  > 本项目基于 [Toonflow-web](https://github.com/HBAI-Ltd/Toonflow-web) 进行二次开发，新增了**首尾帧双卡片 UI**、**卡片级联视频工作台**、**模式级联选择器**等前端交互功能。
</div>

---

# &#x26A0;&#xFE0F; 重要提示

> **本仓库仅包含前端源代码，适用于开发者进行二次开发或定制。**
>
> &#x1F389; **配合后端使用**：前端需对接 [Auto-Dramaflow](https://github.com/roco-of-king/auto-dramaflow) 后端服务。

---

# &#x1F31F; 技术栈

- **框架**：Vue 3.5+ (组合式 API)
- **构建工具**：Vite 5.4+
- **语言**：TypeScript 5.6+
- **状态管理**：Pinia 2.2+ (支持持久化)
- **路由**：Vue Router 4.4+
- **UI 组件库**：
  - Ant Design Vue 4.2+
  - Element Plus 2.13+
  - VXE Table 4.17+
- **工具库**：
  - Axios - HTTP 请求
  - VueUse - Vue 组合式工具集
  - Day.js - 日期处理
  - Mammoth - Word 文档解析

---

# &#x1F3A8; 主要功能模块

Auto-Dramaflow Web 提供了完整的短剧创作前端界面，包含以下核心模块：

- &#x2705; **项目管理**  
   创建、编辑和管理短剧项目，支持项目状态追踪和多项目并行开发。
- &#x2705; **原始文本编辑**  
   导入和编辑小说原文，支持 Word 文档解析，智能文本清洗和章节分割。
- &#x2705; **角色素材库**  
   管理角色设定、角色图片等素材，支持批量生成、手动上传和在线编辑。
- &#x2705; **大纲管理**  
   可视化编辑故事大纲和事件线，支持拖拽排序和智能生成。
- &#x2705; **剧本编辑器**  
   结构化剧本编辑界面，支持对话、场景、情绪等多维度标注。
- &#x2705; **分镜设计**  
   可视化分镜画布，支持拖拽布局、图像检测和 AI 对话式分镜生成。
- &#x2705; **视频配置**  
   配置视频生成参数，支持多家 AI 视频服务商切换和视频下载。
- &#x2705; **任务监控**  
   实时查看 AI 生成任务进度，支持任务队列管理和历史记录查询。
- &#x2705; **系统设置**  
   配置 AI 服务商、提示词模板、用户权限等系统级参数。

### &#x1F195; 本仓库新增功能

- &#x2705; **首尾帧双卡片 UI**  
  分镜画布中每个分镜卡片支持首帧图 + 尾帧图双卡片预览，独立编辑首尾帧图片路径 (`firstFramePath` / `lastFramePath`)，点击保存调用 `editStoryboardInfo` 持久化到后端。
- &#x2705; **卡片级联视频工作台**  
  视频轨道按 track 分组级联排列，支持多模态视频模型（文生/图生/首尾帧/多参考）在同一工作台混排显示，模式切换后自动调用 `getFlowData` 强制刷新分镜面板。
- &#x2705; **模式级联选择器**  
  旧模式名（`startEndRequired` 等）自动映射为新模式名，兜底标签显示，确保历史数据兼容。
- &#x2705; **独立素材上传页面 (upload.html)**  
  内置在 `dist/` 构建产物中，与后端 `uploadAssetImage` API 对接，提供角色/场景/道具图片的批量上传功能。
- &#x2705; **品牌自定义体系**  
  提供完整的 `CUSTOMIZATION.md` 指南，覆盖 Logo、favicon、多语言文案、Vue 组件硬编码等品牌替换全流程。

---

# &#x1F4E6; 应用场景

- 短剧内容创作的前端操作界面
- AI 辅助编剧工具的可视化平台
- 分镜设计与视频生成的工作台
- 多人协作的剧本管理系统

---

# &#x1F680; 快速开始

## &#x1F4A1; 您是哪类用户？

| 用户类型 | 推荐方案 | 链接 |
| :--- | :--- | :--- |
| &#x1F3AC; **普通用户** - 想直接使用 | 下载完整后端 | [Auto-Dramaflow](https://github.com/roco-of-king/auto-dramaflow) |
| &#x1F468;&#x200D;&#x1F4BB; **开发者** - 修改前端代码 | 继续阅读本文档 | 本仓库 |

---

## 前置条件

- &#x2705; **Node.js**：23.11.1 或更高版本
- &#x2705; **Yarn**：1.22.0 或更高版本
- &#x2705; **后端服务**：确保 Auto-Dramaflow 后端已启动（端口 10588）

## 本地开发

### 1. 克隆项目

```bash
git clone https://github.com/roco-of-king/auto-dramaflow-web.git
cd auto-dramaflow-web
```

### 2. 安装依赖

```bash
yarn install
```

### 3. 启动开发服务器

```bash
yarn dev
```

开发服务器默认运行在 `http://localhost:5173`，支持 HMR。

### 4. 构建生产版本

```bash
yarn build:prod
```

### 5. 部署到后端

将 `dist/` 目录内容复制到后端 `data/web/` 目录：

```bash
cp -r dist/* ../auto-dramaflow/data/web/
```

---

# &#x1F528; 开发指南

## 常用命令

```bash
yarn install        # 安装依赖
yarn dev            # 启动开发服务器（HMR）
yarn type-check     # TypeScript 类型检查
yarn lint           # 代码检查和自动修复
yarn build:dev      # 构建开发版本
yarn build:prod     # 构建生产版本
yarn preview        # 预览生产构建
```

## 项目结构

```
&#x1F4C2; auto-dramaflow-web/
&#x251C;&#x2500; &#x1F4C2; public/                # 静态资源（favicon 等）
&#x251C;&#x2500; &#x1F4C2; scripts/               # 构建脚本
&#x251C;&#x2500; &#x1F4C2; src/
&#x2502;  &#x251C;&#x2500; &#x1F4C2; assets/             # 静态资源（样式、图片、Logo）
&#x2502;  &#x251C;&#x2500; &#x1F4C2; components/         # 公共组件
&#x2502;  &#x2502;  &#x251C;&#x2500; &#x1F4C2; storyboardEditor/ # &#x1F195; 分镜编辑器（首尾帧双卡片）
&#x2502;  &#x2502;  &#x251C;&#x2500; &#x1F4C2; videoConfig/     # &#x1F195; 视频配置（卡片级联工作台）
&#x2502;  &#x2502;  &#x251C;&#x2500; &#x1F4C2; chat/            # 聊天/AI 对话组件
&#x2502;  &#x2502;  &#x2514;&#x2500; &#x1F4C2; setting/         # 设置组件
&#x2502;  &#x251C;&#x2500; &#x1F4C2; config/             # 配置文件
&#x2502;  &#x251C;&#x2500; &#x1F4C2; locales/           # 多语言文件（7 种语言）
&#x2502;  &#x251C;&#x2500; &#x1F4C2; pages/             # 页面组件
&#x2502;  &#x251C;&#x2500; &#x1F4C2; router/            # 路由配置
&#x2502;  &#x251C;&#x2500; &#x1F4C2; stores/            # Pinia 状态管理
&#x2502;  &#x251C;&#x2500; &#x1F4C2; types/             # TypeScript 类型定义
&#x2502;  &#x251C;&#x2500; &#x1F4C2; utils/             # 工具函数
&#x2502;  &#x251C;&#x2500; &#x1F4C2; views/             # 视图页面
&#x2502;  &#x2502;  &#x251C;&#x2500; &#x1F4C2; project/         # 项目管理
&#x2502;  &#x2502;  &#x251C;&#x2500; &#x1F4C2; projectDetail/   # 项目详情（素材/剧本/分镜/视频）
&#x2502;  &#x2502;  &#x251C;&#x2500; &#x1F4C2; setting/         # 系统设置
&#x2502;  &#x2502;  &#x2514;&#x2500; &#x1F4C2; taskList/        # 任务列表
&#x2502;  &#x251C;&#x2500; &#x1F4C4; App.vue             # 根组件
&#x2502;  &#x2514;&#x2500; &#x1F4C4; main.ts             # 应用入口
&#x251C;&#x2500; &#x1F4C2; dist/                 # 构建产物
&#x251C;&#x2500; &#x1F4C4; index.html            # HTML 模板
&#x251C;&#x2500; &#x1F4C4; package.json          # 项目配置
&#x251C;&#x2500; &#x1F4C4; vite.config.ts        # Vite 配置
&#x251C;&#x2500; &#x1F4C4; tsconfig.json         # TypeScript 配置
&#x251C;&#x2500; &#x1F4C4; CUSTOMIZATION.md       # &#x1F195; 品牌自定义指南
&#x251C;&#x2500; &#x1F4C4; LICENSE               # 许可证
&#x2514;&#x2500; &#x1F4C4; README.md             # 项目说明
```

---

# &#x1F4DD; 品牌自定义

本项目提供了完整的品牌自定义指南，详见 [CUSTOMIZATION.md](./CUSTOMIZATION.md)。涵盖：

| 类别 | 文件数 | 说明 |
| :--- | :--: | :--- |
| favicon | 1 | `public/favicon.ico` |
| Logo 图片 | 1 | `src/assets/logo.png` |
| 核心配置 | 2 | `index.html`, `package.json` |
| 多语言 | 7 | `src/locales/language/*.json` |
| Vue 组件 | 6 | login, workbench, titleBar, hello, about, vendorConfig |

---

# &#x1F517; 相关仓库

| 仓库 | 说明 | 地址 |
| :--- | :--- | :--- |
| **Auto-Dramaflow** | 完整后端（推荐普通用户） | [GitHub](https://github.com/roco-of-king/auto-dramaflow) |
| **Auto-Dramaflow-Web** | 前端源代码（本仓库，适合开发者） | [GitHub](https://github.com/roco-of-king/auto-dramaflow-web) |
| **Toonflow-web** | 上游原项目 | [GitHub](https://github.com/HBAI-Ltd/Toonflow-web) |

---

# &#x1F64F; 致谢与上游

本项目基于 [Toonflow-web](https://github.com/HBAI-Ltd/Toonflow-web) 进行二次开发，感谢原作者的杰出工作。

感谢以下开源项目：

- [Vue.js](https://vuejs.org/) - 渐进式 JavaScript 框架
- [Vite](https://vitejs.dev/) - 下一代前端构建工具
- [Ant Design Vue](https://antdv.com/) - 企业级 UI 组件库
- [Element Plus](https://element-plus.org/) - 基于 Vue 3 的组件库
- [Pinia](https://pinia.vuejs.org/) - Vue 的直观状态管理库

---

# &#x1F4DC; 许可证

本项目基于 [Apache-2.0](https://www.apache.org/licenses/LICENSE-2.0) 协议开源发布。

完整协议详见 [LICENSE](./LICENSE) 文件。

> &#x26A0;&#xFE0F; 上游 Toonflow 项目有其独立的商业授权补充协议，如涉及商业分发请参考[上游仓库](https://github.com/HBAI-Ltd/Toonflow-web)的完整许可条款。

##### copyright &#xA9; 基于 Toonflow-web 二次开发
