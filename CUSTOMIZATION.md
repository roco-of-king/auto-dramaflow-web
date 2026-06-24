# 项目品牌自定义指南

> 拉取本项目后，按本指南将名称和图标替换为你自己的品牌。

---

## 一、准备工作

| 素材 | 要求 | 说明 |
|------|------|------|
| **favicon.ico** | 内含多尺寸（建议 16/32/48/64/128/256px），可用 [realfavicongenerator.net](https://realfavicongenerator.net) 生成 | 浏览器标签页图标 |
| **logo.png** | 透明背景，建议 512×512 | 应用内 Logo |

---

## 二、替换图标

### 2.1 替换 favicon

```
用你的 favicon.ico 覆盖 public/favicon.ico
```

### 2.2 替换 Logo

```
用你的 logo.png 覆盖 src/assets/logo.png
```

如果 Logo 是 PNG 格式，确保以下 CSS 使用 `background-image`（默认已配置好）：

| 文件 | CSS 类 | 说明 |
|------|--------|------|
| `src/pages/login/index.vue` | `.logoImg` | 登录页 Logo |
| `src/pages/workbench/index.vue` | `.logo` | 工作台左侧栏 Logo |

---

## 三、替换名称

> 以下用 `原名称 → 新名称` 表示。**注意大小写**：`ToonFlow`（标题式）、`Toonflow`（正文式）、`toonflow`（全小写）。

### 3.1 核心配置文件

| 文件 | 行/字段 | 修改内容 |
|------|---------|----------|
| `index.html` | `<title>` | 浏览器标签页标题：`Toonflow` → 新名称 |
| `package.json` | `"name"` | 项目标识：`toonflow_web` → `xxx_web` |

### 3.2 多语言文件（7 个）

目录：`src/locales/language/`

| 文件 | 涉及处数 |
|------|:--:|
| `zh-CN.json` | ~10 |
| `zh-TW.json` | ~7 |
| `en.json` | ~10 |
| `ja_JP.json` | ~7 |
| `ru_RU.json` | ~7 |
| `th_TH.json` | ~8 |
| `vi-VN.json` | ~8 |

**替换命令（一键批量）：**

```bash
# Windows Git Bash / Linux / macOS
cd src/locales/language/
for f in *.json; do
    sed -i 's/ToonFlow/新名称标题式/g; s/Toonflow/新名称正文式/g; s/toonflow/新名称小写/g' "$f"
done
```

### 3.3 Vue 组件硬编码文案

| 文件 | 原文案 | 说明 |
|------|--------|------|
| `src/pages/login/index.vue` | `<span class="logoText">ToonFlow</span>` | 登录页 Logo 旁文字 |
| `src/components/titleBar.vue` | `<span class="titleBar-text">ToonFlow</span>` | 桌面端标题栏 |
| `src/components/hello.vue` | `alt="ToonFlow Logo"` | 欢迎页 Logo 图片替代文本 |
| `src/components/setting/components/about.vue` | `<div class="name">ToonFlow</div>` | 关于页应用名 |
| `src/components/setting/components/about.vue` | `alt="ToonFlow Logo"` | 关于页 Logo 替代文本 |
| `src/components/setting/components/about.vue` | `label: "ToonFlow"` | 更新源下拉选项标签 |
| `src/components/setting/components/about.vue` | `toonflow: "ToonFlow"` | 更新源映射文本 |
| `src/components/setting/components/vendorConfig.vue` | `确认后 Toonflow 会自动加载该代码` | 添加供应商安全提示 |

---

## 四、不需要改的内容

以下位置包含 `toonflow` 字样，但**修改后可能破坏功能**，建议保留：

| 位置 | 原因 |
|------|------|
| `toonflow://` 协议（约 12 处） | Electron 桌面端 IPC 通信协议 |
| `src/components/setting/components/about.vue` 中的 `toonflowLogo` 变量名、`"toonflow"` 类型值 | 代码内部标识，不影响 UI |
| `scripts/license.ts` 中的 `toonflow-serve` | 后端包名排除，与前端无关 |

---

## 五、构建与部署

### 5.1 安装依赖（首次）

```bash
npm install --legacy-peer-deps
```

### 5.2 构建

```bash
# 跳过类型检查，直接打包
npm run build-only
```

产物在 `dist/` 目录，核心文件为 `index.html`（所有资源内联为单文件）及 Worker `.js` 文件。

### 5.3 部署到后端

将 `dist/` 下的文件复制到后端服务的静态文件目录：

```bash
# 如果后端直接读取 data/web/
cp dist/* ../Toonflow-app-master/data/web/

# 如果后端通过 Docker 卷挂载（检查实际路径）
cp dist/* /path/to/docker-volumes/xxx-data/web/
```

> **注意**：不要覆盖后端自带的文件（如 `upload.html`）。

### 5.4 重启服务

```bash
# 直接运行
cd ../Toonflow-app-master && yarn dev

# Docker
docker build -t yourname . && docker run -d -p 10588:10588 --name yourname yourname
```

---

## 六、完整修改清单

| 类别 | 文件数 | 说明 |
|------|:--:|------|
| favicon | 1 | `public/favicon.ico` |
| Logo 图片 | 1 | `src/assets/logo.png` |
| 核心配置 | 2 | `index.html`, `package.json` |
| 多语言 | 7 | `src/locales/language/*.json` |
| Vue 组件 | 6 | login, workbench, titleBar, hello, about, vendorConfig |
| CSS Logo | 2 | login/index.vue, workbench/index.vue（如用 SVG 则需调整） |

---

## 七、检查清单

打包部署后，逐项确认：

- [ ] 浏览器标签页显示新图标和新标题
- [ ] 登录页显示新 Logo 和新名称
- [ ] 工作台左侧栏和标题栏显示新名称
- [ ] 设置 → 关于 显示新 Logo 和新名称
- [ ] 添加供应商的提示文案中没有旧名称
- [ ] 切换不同语言，UI 文案中均无旧名称
