# Story 1.1: 初始化小程序项目结构与 Starter Template

Status: ready-for-dev

<!-- Note: Validation is optional. Run validate-create-story for quality check before dev-story. -->

## Story

As a **开发者**,
I want **使用官方 uniapp starter 模板初始化项目**,
so that **项目结构规范，能正常启动小程序**.

## Acceptance Criteria (BDD)

### AC1: 项目初始化成功

**Given** 项目仓库中无 uniapp 项目结构
**When** 使用官方 starter 模板初始化项目
**Then**
- 小程序项目结构完成（pages/components/stores/static/types 等目录存在）
- Vue 3 + TypeScript 框架就绪且能正确识别
- `package.json` 中项目名称设置为 `"project-manager"`

### AC2: 开发环境可用

**Given** 项目已初始化且依赖安装完成
**When** 执行 `npm run dev:mp-weixin`
**Then**
- 成功启动小程序开发服务
- 输出到 `dist/dev/mp-weixin` 目录
- 基础页面结构正确生成

### AC3: 生产构建成功

**Given** 项目已初始化
**When** 执行 `npm run build:mp-weixin`
**Then**
- 成功打包到 `dist/build/mp-weixin` 目录
- 生成的包体积 < 1MB（初始化后）

### AC4: 示例代码清理 & 目录结构就绪

**Given** starter 模板初始化完成
**When** 检查项目结构
**Then**
- 已删除所有示例/演示代码
- 保留基础框架结构（App.vue, main.ts, pages.json, manifest.json, uni.scss）
- 已创建项目约定的目录结构（见下方 Tasks）

## Tasks / Subtasks

- [ ] **Task 1: 清理现有错误脚手架** (AC: #1)
  - [ ] 1.1 删除当前项目中的 Electron 相关文件（`src/main.js`, `src/renderer.js`, `src/preload.js`, `forge.config.js`, `src/index.html`, `src/index.css`）
  - [ ] 1.2 删除现有的 `package.json`（将被 starter 模板覆盖）
  - [ ] 1.3 删除 `node_modules/` 和 `package-lock.json`（如存在）

- [ ] **Task 2: 初始化 uniapp 项目** (AC: #1)
  - [ ] 2.1 执行 `npx degit dcloudio/uni-preset-vue#vite-ts .` 在当前目录初始化（注意：保留 `_bmad/`、`_bmad-output/`、`.claude/`、`CLAUDE.md`、`.git/` 等非代码文件）
  - [ ] 2.2 执行 `npm install` 安装依赖
  - [ ] 2.3 修改 `package.json` 中 `name` 字段为 `"project-manager"`

- [ ] **Task 3: 清理示例代码** (AC: #4)
  - [ ] 3.1 清理 `pages/index/index.vue` 中的示例内容，替换为简单的空白首页占位
  - [ ] 3.2 清理 `App.vue` 中的示例样式，保留基本框架结构

- [ ] **Task 4: 创建项目约定目录结构** (AC: #4)
  - [ ] 4.1 创建 `pages/index/` 目录（首页，starter 已有）
  - [ ] 4.2 创建 `pages/project/` 目录（项目管理）
  - [ ] 4.3 创建 `pages/expense/` 目录（费用录入）
  - [ ] 4.4 创建 `pages/reconciliation/` 目录（核销流程）
  - [ ] 4.5 创建 `pages/me/` 目录（我的）
  - [ ] 4.6 创建 `components/business/` 目录（业务组件 C01-C10）
  - [ ] 4.7 创建 `components/common/` 目录（通用组件）
  - [ ] 4.8 创建 `stores/` 目录（Pinia 状态管理）
  - [ ] 4.9 创建 `utils/` 目录（工具函数）
  - [ ] 4.10 创建 `types/` 目录（TypeScript 类型定义）
  - [ ] 4.11 创建 `static/mp-weixin/` 和 `static/web/` 目录（静态资源）
  - [ ] 4.12 在每个新建目录中放置 `.gitkeep` 文件以确保目录被 Git 追踪

- [ ] **Task 5: 配置基础文件** (AC: #1, #2)
  - [ ] 5.1 配置 `pages.json` — 设置首页路由 `pages/index/index`，配置基本的 `globalStyle`（导航栏标题、背景色等）
  - [ ] 5.2 配置 `manifest.json` — 设置 `appid`（可暂用测试 ID）、`darkmode: false`、微信小程序相关配置
  - [ ] 5.3 确认 `vite.config.ts` 包含 `@dcloudio/vite-plugin-uni` 插件
  - [ ] 5.4 确认 `tsconfig.json` 正确配置 TypeScript（ES2020+, Vue 3 类型支持）

- [ ] **Task 6: 验证构建** (AC: #2, #3)
  - [ ] 6.1 运行 `npm run dev:mp-weixin` 确认开发服务正常启动
  - [ ] 6.2 运行 `npm run build:mp-weixin` 确认生产构建成功
  - [ ] 6.3 检查构建输出体积 < 1MB

## Dev Notes

### 关键发现：现有项目含 Electron 脚手架

**当前项目目录中存在 Electron.js 脚手架文件**，必须先清理：
- `src/main.js` — Electron 主进程
- `src/renderer.js` — Electron 渲染进程
- `src/preload.js` — Electron 预加载脚本
- `forge.config.js` — Electron Forge 配置
- `src/index.html`, `src/index.css` — Electron 页面

这些文件与 uniapp 项目完全无关，必须在初始化前删除。

**注意保留以下文件/目录：**
- `_bmad/` — BMAD 方法配置
- `_bmad-output/` — 规划和实施产物
- `.claude/` — Claude 技能配置
- `CLAUDE.md` — 项目指令文件
- `.git/` — Git 仓库
- `.gitignore` — Git 忽略规则
- `README.md` — 项目说明

### 初始化命令

```bash
# 在项目根目录执行（先清理 Electron 文件后）
npx degit dcloudio/uni-preset-vue#vite-ts .
# 如果 degit 报目录非空，可先将模板下载到临时目录再移动：
# npx degit dcloudio/uni-preset-vue#vite-ts /tmp/uniapp-starter
# 然后将内容复制到项目根目录

npm install
```

### 开发/构建命令

```bash
# 开发（微信小程序）
npm run dev:mp-weixin

# 构建（微信小程序）
npm run build:mp-weixin

# 开发（H5/PC Web）— 后续 Story 使用
npm run dev:h5

# 构建（H5/PC Web）
npm run build:h5
```

### 技术栈版本要求

| 技术 | 版本 | 说明 |
|------|------|------|
| uniapp | 5.03+ | DCloud 官方 starter |
| Vue | 3.x | Composition API + `<script setup>` |
| TypeScript | 内置 | ES2020+ |
| Vite | 内置 | 毫秒级热更新 |
| Node.js | 18.x 或 20.x+ | 运行时环境 |

### Project Structure Notes

初始化完成后，项目结构应如下（仅列出 Story 1.1 需创建的部分）：

```
project-manager/
├── pages/
│   ├── index/index.vue          # 首页（starter 自带，需清理示例）
│   ├── project/                 # 项目管理（空目录 + .gitkeep）
│   ├── expense/                 # 费用录入（空目录 + .gitkeep）
│   ├── reconciliation/          # 核销流程（空目录 + .gitkeep）
│   └── me/                      # 我的（空目录 + .gitkeep）
├── components/
│   ├── business/                # 业务组件（空目录 + .gitkeep）
│   └── common/                  # 通用组件（空目录 + .gitkeep）
├── stores/                      # Pinia 状态管理（空目录 + .gitkeep）
├── utils/                       # 工具函数（空目录 + .gitkeep）
├── types/                       # TypeScript 类型（空目录 + .gitkeep）
├── static/
│   ├── mp-weixin/               # 小程序资源（空目录 + .gitkeep）
│   └── web/                     # Web 资源（空目录 + .gitkeep）
├── pages.json                   # 路由配置
├── manifest.json                # 应用配置
├── uni.scss                     # 全局样式变量（Story 1.2 填充）
├── App.vue                      # 全局配置
├── main.ts                      # 应用入口
├── vite.config.ts               # Vite 配置
├── tsconfig.json                # TypeScript 配置
└── package.json                 # name: "project-manager"
```

### Architecture Compliance

**必须遵循的架构规则：**
- **Feature-first 目录组织**：按功能模块分组（`pages/project/`, `pages/expense/`），不按类型分组 [Source: architecture.md#前端文件结构]
- **Vue 3 Composition API**：全部使用 `<script setup>` 语法 [Source: architecture.md#AR34]
- **TypeScript**：所有 `.vue` 文件使用 `<script setup lang="ts">` [Source: architecture.md#Starter Template Decision]
- **暗色模式禁用**：`manifest.json` 中 `darkmode: false` [Source: architecture.md#Technical Constraints]

### pages.json 基础配置参考

```json
{
  "pages": [
    {
      "path": "pages/index/index",
      "style": {
        "navigationBarTitleText": "首页"
      }
    }
  ],
  "globalStyle": {
    "navigationBarTextStyle": "black",
    "navigationBarTitleText": "设计工作室",
    "navigationBarBackgroundColor": "#FFFFFF",
    "backgroundColor": "#F7F8FA"
  }
}
```

> 注意：TabBar 配置（4 个 Tab）将在后续 Story 中添加（需要图标资源）。Story 1.1 仅需首页路由。

### manifest.json 关键配置

```json
{
  "mp-weixin": {
    "appid": "",
    "setting": {
      "urlCheck": false
    },
    "usingComponents": true
  },
  "darkmode": false
}
```

### 本 Story 不涉及的内容（后续 Story 负责）

- **Story 1.2**: 设计 Token 与全局样式（`uni.scss` 填充）
- **Story 1.3**: uni-ui 组件库集成
- **Story 1.4**: uniCloud 云环境与数据库连接
- **Story 1.5**: uni-id 认证系统（小程序微信登录）
- **Story 1.6**: PC Web 管理后台（`/admin` 目录）
- **Story 1.7**: uni-id 认证系统（PC 登录）

### References

- [Source: architecture.md#Starter Template Decision] — 初始化方案选择
- [Source: architecture.md#Project Structure After Initialization] — 项目目录结构
- [Source: architecture.md#Implementation Patterns & Consistency Rules] — 一致性规则
- [Source: architecture.md#前端文件结构] — Feature-first 组织模式
- [Source: epics.md#Story 1.1] — Story 原始定义
- [Source: ux-design-specification.md#Design System Foundation] — 设计系统基础
- [Source: prd.md#Mobile App Specific Requirements] — 平台要求

## Dev Agent Record

### Agent Model Used

### Debug Log References

### Completion Notes List

### Change Log

### File List
