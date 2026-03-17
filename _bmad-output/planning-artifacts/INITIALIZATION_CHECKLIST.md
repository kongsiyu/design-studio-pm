# Project Manager - 项目初始化检查清单

**项目：** project-manager  
**作者：** Sue  
**日期：** 2026-03-17  
**状态：** 待实施  

---

## 📋 预备检查（开发环境）

### 本地开发环境
- [ ] 安装 Node.js 18.x 或 20.x+
- [ ] 安装 npm 9.x+
- [ ] 安装 HBuilderX 最新版本
- [ ] 配置 git 全局用户名和邮箱
- [ ] 克隆项目仓库到本地

### 账户和权限
- [ ] 注册/登录支付宝云账户
- [ ] 获得微信小程序开发者权限
- [ ] 配置微信小程序开发者工具
- [ ] 获得腾讯云 OCR API 访问权限（暂不需要，二期）

---

## 🚀 Step 1: 项目初始化

### 1.1 创建项目骨架
```bash
npx degit dcloudio/uni-preset-vue#vite-ts project-manager
cd project-manager
npm install
```

**验证**：
- [ ] 项目文件夹已创建
- [ ] npm install 完成无错误
- [ ] `npm run dev:mp-weixin` 能启动小程序开发服务

### 1.2 初始化 Git 仓库
```bash
git init
git add .
git commit -m "Initial commit: uniapp starter project"
git branch -b main
```

**验证**：
- [ ] git 仓库已初始化
- [ ] main 分支已创建
- [ ] 首次提交已完成

---

## ☁️ Step 2: 云开发环境建立

### 2.1 创建支付宝云开发空间
- [ ] 在 HBuilderX 中右键 `uniCloud` 目录
- [ ] 选择"创建云开发服务空间"
- [ ] 选择服务商：**支付宝云**
- [ ] 选择地区并创建服务空间
- [ ] 绑定微信小程序 AppID（在 manifest.json 中）

**验证**：
- [ ] HBuilderX 显示"云开发服务空间已绑定"
- [ ] `uniCloud/cloudfunctions` 可上传到云端

### 2.2 初始化数据库 Schema
- [ ] 在 `uniCloud/database/` 目录下创建所有 schema 文件：
  - `customers.schema.json`
  - `vendors.schema.json`
  - `projects.schema.json`
  - `expenses.schema.json`
  - `budgetItems.schema.json`
  - `collections.schema.json`
  - `bankFlows.schema.json`
  - `reconciliations.schema.json`
  - `importSessions.schema.json`
  - `operationLogs.schema.json`

**参考文档**：见《Core Architectural Decisions》中的数据模型章节

**验证**：
- [ ] 所有 schema 文件已创建
- [ ] HBuilderX 右键 `database/` 可上传云数据库

### 2.3 初始化 uni-id 认证系统
- [ ] 编辑 `cloudfunctions/common/uni-id/config.json`
  ```json
  {
    "passwordSecret": "your-secret-key-here",
    "tokenSecret": "your-token-secret-here",
    "tokenExpiresIn": 604800,
    "refreshTokenExpiresIn": 2592000
  }
  ```
- [ ] 确保 `cloudfunctions/common/uni-id/` 目录完整

**验证**：
- [ ] config.json 已编辑
- [ ] uni-id 云函数可上传并部署

---

## 📦 Step 3: 依赖和工具集成

### 3.1 安装前端依赖
```bash
npm install pinia
npm install pinia-plugin-persistedstate  # 持久化状态
npm install @vueuse/core  # 实用 Vue Hooks
npm install xlsx  # SheetJS for Excel export
```

**验证**：
- [ ] `package.json` 已更新所有依赖
- [ ] `node_modules/` 包含 pinia、xlsx 等库

### 3.2 配置 Pinia
- [ ] 在 `main.js` 中导入和初始化 Pinia
  ```javascript
  import { createPinia } from 'pinia'
  
  const pinia = createPinia()
  app.use(pinia)
  ```

**验证**：
- [ ] `main.js` 已配置 Pinia
- [ ] 小程序能正常启动

### 3.3 创建全局工具函数
- [ ] 创建 `utils/api.js` - 云函数调用包装
- [ ] 创建 `utils/validators.js` - 业务校验规则
- [ ] 创建 `utils/format.js` - 日期格式化工具
- [ ] 创建 `utils/storage.js` - 本地存储管理

**参考**：见《Implementation Patterns》中的示例代码

**验证**：
- [ ] 所有工具文件已创建
- [ ] 工具函数导出正确

---

## 🔐 Step 4: 认证系统实现

### 4.1 小程序登录流程
- [ ] 在 `cloudfunctions/` 目录下创建 `login` 云函数
- [ ] 实现微信授权登录逻辑
- [ ] 返回 token 和用户信息

**参考**：
```javascript
// cloudfunctions/login/index.js
module.exports = async (event) => {
  const { code } = event
  // 调用 uni-id 验证 code
  // 返回 token 和 userInfo
}
```

**验证**：
- [ ] 云函数已创建并部署
- [ ] 小程序能成功通过微信授权并获得 token

### 4.2 PC 端登录认证
- [ ] 在 `cloudfunctions/` 创建 `login-username` 云函数
- [ ] 实现用户名密码登录逻辑
- [ ] 与 uni-id 集成

**验证**：
- [ ] 云函数已创建并部署
- [ ] PC Web 能成功通过用户名密码登录

### 4.3 Token 管理
- [ ] 在 `stores/user.js` 中实现 Pinia store
- [ ] 存储 token 到本地存储（localStorage for Web, uni.storage for App）
- [ ] 实现 token 刷新逻辑

**验证**：
- [ ] userStore 已创建
- [ ] token 能正确存储和读取

---

## 📊 Step 5: 首页与数据聚合

### 5.1 创建首页摘要表
- [ ] 在数据库中创建 `dashboardSummary` 集合（手动或通过 schema）
- [ ] 定义聚合字段：shouldReceive、actualReceived、pendingReconcile 等

**验证**：
- [ ] 数据库集合已创建
- [ ] 可在 HBuilderX 云数据库管理中看到

### 5.2 实现聚合查询云函数
- [ ] 创建 `cloudfunctions/dashboard-summary/index.js`
- [ ] 实现首页数据聚合逻辑（按月统计应收、实收、待核销数）
- [ ] 返回统一格式的响应

**参考**：见《Core Architectural Decisions》中的聚合查询示例

**验证**：
- [ ] 云函数已部署
- [ ] 可从前端成功调用并获得数据

### 5.3 实现首页 UI 组件
- [ ] 创建 `components/business/FinanceSummaryBanner.vue`
- [ ] 实现首页摘要展示
- [ ] 添加缓存和定时刷新逻辑

**参考**：见《UX Design Specification》中的首页设计

**验证**：
- [ ] 组件已创建
- [ ] 小程序首页能正确展示财务摘要数据

---

## 💰 Step 6: 费用管理模块

### 6.1 创建费用数据表
- [ ] 确保 `expenses.schema.json` 已部署
- [ ] 在数据库中初始化 expenses 集合

**验证**：
- [ ] 数据库集合已创建

### 6.2 实现费用 CRUD 云函数
- [ ] 创建 `cloudfunctions/expense/expense-create/index.js`
- [ ] 创建 `cloudfunctions/expense/expense-list/index.js`
- [ ] 创建 `cloudfunctions/expense/expense-update/index.js`
- [ ] 创建 `cloudfunctions/expense/expense-delete/index.js`

**参考**：见《Core Architectural Decisions》中的数据校验示例

**验证**：
- [ ] 所有云函数已部署
- [ ] 前端能成功调用并操作费用数据

### 6.3 创建费用 UI 组件和页面
- [ ] 创建 `pages/expense/create.vue` - 新增费用页
- [ ] 创建 `pages/expense/list.vue` - 费用列表页
- [ ] 创建 `stores/expense.js` - 费用状态管理

**验证**：
- [ ] 页面能正常加载和显示
- [ ] 能成功创建、查看、编辑、删除费用

---

## 🔄 Step 7: 核销流程

### 7.1 创建核销相关数据表
- [ ] 确保 `importSessions.schema.json` 已部署
- [ ] 确保 `reconciliations.schema.json` 已部署
- [ ] 确保 `bankFlows.schema.json` 已部署

**验证**：
- [ ] 数据库集合已创建

### 7.2 实现导入流程云函数
- [ ] 创建 `cloudfunctions/expense/reconcile-import/index.js`
  - 实现文件解析（Excel、CSV）
  - 实现银行流水解析
  - 状态管理（PENDING → PARSING → PARSED）

- [ ] 创建 `cloudfunctions/expense/reconcile-commit/index.js`
  - 实现事务性提交
  - 状态转移（APPROVED → COMMITTING → SUCCESS）
  - 版本号（乐观锁）检查

**参考**：见《Core Architectural Decisions》中的导入流程和事务处理

**验证**：
- [ ] 云函数已部署
- [ ] 能成功上传和解析流水文件

### 7.3 创建核销 UI 和页面
- [ ] 创建 `pages/reconciliation/list.vue` - 核销列表页
- [ ] 创建 `components/business/ReconciliationRow.vue` - 单条流水行组件
- [ ] 创建 `stores/reconciliation.js` - 核销状态管理

**验证**：
- [ ] 页面能正常加载和显示
- [ ] 能成功导入、查看、确认核销流水

---

## 📈 Step 8: 报表导出

### 8.1 实现报表查询云函数
- [ ] 创建 `cloudfunctions/report/export-report/index.js`
- [ ] 实现报表数据聚合
- [ ] 返回结构化数据（支持按月份、项目筛选）

**验证**：
- [ ] 云函数已部署
- [ ] 能成功查询报表数据

### 8.2 实现前端导出功能
- [ ] 在相应页面中集成 SheetJS
- [ ] 实现"导出 Excel"按钮
- [ ] 将报表数据转换为 Excel 格式下载

**验证**：
- [ ] 能成功导出 Excel 文件
- [ ] Excel 文件格式正确、数据完整

---

## 🖥️ Step 9: PC Web 管理后台（uni-admin）

### 9.1 集成 uni-admin
- [ ] 在 `/admin` 目录下初始化 uni-admin 项目
- [ ] 配置 uni-admin 认证和权限系统
- [ ] 同步 uni-id 配置

**参考**：uni-admin 官方文档

**验证**：
- [ ] PC Web 能正常启动
- [ ] 登录功能正常工作

### 9.2 创建 MVP 管理页面
- [ ] 客户管理：列表、CRUD 操作
- [ ] 供应商管理：列表、CRUD 操作
- [ ] 项目管理：列表、CRUD、归档操作
- [ ] 费用管理：列表、查看、删除
- [ ] 收款管理：列表、查看
- [ ] 报表页面：数据展示、导出
- [ ] 系统设置：用户账号、费用类别配置

**验证**：
- [ ] 所有页面能正常加载
- [ ] CRUD 操作正常工作
- [ ] 数据与小程序同步

---

## 📝 Step 10: 操作日志系统

### 10.1 创建操作日志表
- [ ] 确保 `operationLogs.schema.json` 已部署

**验证**：
- [ ] 数据库集合已创建

### 10.2 实现日志记录中间件
- [ ] 创建 `cloudfunctions/common/middleware/log.js`
- [ ] 在关键云函数中调用日志中间件
- [ ] 记录：userId、action、objectType、objectId、timestamp

**参考**：见《Core Architectural Decisions》中的操作日志模型

**验证**：
- [ ] 操作后能在数据库中查看日志记录

---

## ✅ Step 11: 集成验证

### 11.1 功能集成测试
- [ ] 小程序能完整执行用户旅程（登录 → 创建费用 → 导入流水 → 核销 → 导出报表）
- [ ] PC Web 能完整执行管理操作（登录 → CRUD 各模块 → 查看日志）
- [ ] 小程序和 PC Web 数据同步正确

### 11.2 性能验证
- [ ] 首页加载时间 < 1 秒
- [ ] 费用列表首屏加载 < 2 秒
- [ ] 核销导入解析 < 3 秒（100 条流水）

### 11.3 错误处理验证
- [ ] 网络错误时能正确显示错误提示
- [ ] 业务错误时能正确显示错误信息
- [ ] 异常操作时能正确拒绝并提示

**验证清单**：
- [ ] 所有关键用户旅程已测试
- [ ] 性能指标符合要求
- [ ] 错误处理完善

---

## 🚀 Step 12: 部署准备

### 12.1 构建生产版本
```bash
# 小程序
npm run build:mp-weixin

# PC Web
npm run build:h5
```

**验证**：
- [ ] 小程序包体积 < 4MB
- [ ] Web 包体积 < 5MB
- [ ] 构建无错误和警告

### 12.2 提交到小程序后台
- [ ] 登录微信小程序后台
- [ ] 上传最新版本的小程序包
- [ ] 提交审核或直接发布到体验版

### 12.3 部署 PC Web 到支付宝云
- [ ] 上传 PC Web 构建文件到支付宝云
- [ ] 配置域名和 SSL 证书
- [ ] 验证 Web 应用可正常访问

---

## 📋 验收标准

### 功能完整性
- [ ] 所有 MVP 功能都已实现
- [ ] 所有关键用户旅程都能完成
- [ ] 数据流向正确

### 代码质量
- [ ] 代码遵循《Implementation Patterns》规范
- [ ] 所有类、函数、变量命名规范
- [ ] 错误处理完善

### 性能指标
- [ ] 首页加载时间 < 1 秒
- [ ] 核心操作响应时间 < 2 秒
- [ ] 包体积符合要求

### 文档完整性
- [ ] 架构文档完整
- [ ] API 文档完整
- [ ] 部署文档完整

---

## 📅 时间里程碑

| 步骤 | 描述 | 预计耗时 | 实际完成日期 |
|------|------|---------|-----------|
| 1 | 项目初始化 | 1 小时 | |
| 2 | 云开发环境 | 2 小时 | |
| 3 | 依赖集成 | 1 小时 | |
| 4 | 认证系统 | 3 小时 | |
| 5 | 首页聚合 | 2 小时 | |
| 6 | 费用管理 | 4 小时 | |
| 7 | 核销流程 | 5 小时 | |
| 8 | 报表导出 | 2 小时 | |
| 9 | PC 管理后台 | 6 小时 | |
| 10 | 日志系统 | 1 小时 | |
| 11 | 集成验证 | 3 小时 | |
| 12 | 部署准备 | 2 小时 | |
| **总计** | | **32 小时** | |

---

## 🎯 关键联系点

### 架构文档位置
- 完整架构：`_bmad-output/planning-artifacts/architecture.md`
- 实现模式：架构文档 > Implementation Patterns & Consistency Rules
- UX 规范：`_bmad-output/planning-artifacts/ux-design-specification.md`
- PRD：`_bmad-output/planning-artifacts/prd.md`

### 参考代码示例
所有代码示例都在架构文档的相应章节中提供，包括：
- 云函数模板
- Vue 组件示例
- Pinia store 示例
- 数据模型示例

---

## 📞 故障排除

### 常见问题
1. **uni-cloud 连接失败** → 检查服务空间绑定、API Key 配置
2. **小程序登录失败** → 检查微信 AppID、uni-id config.json
3. **数据库查询超时** → 检查索引、查询是否做了反范式化优化
4. **前端报错跨域** → 检查云函数 CORS 配置

### 联系支持
- uni-cloud 官方文档：https://unicloud.dcloud.net.cn/
- WeChat 小程序文档：https://developers.weixin.qq.com/
- Pinia 文档：https://pinia.vuejs.org/

---

**最后检查日期**：2026-03-17  
**检查人**：  
**状态**：待启动  

