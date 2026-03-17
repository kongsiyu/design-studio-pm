---
stepsCompleted: [1, 2, 3, 4, 5]
inputDocuments: ['_bmad-output/planning-artifacts/prd.md', '_bmad-output/planning-artifacts/ux-design-specification.md', '_bmad-output/planning-artifacts/prd-validation-report.md']
workflowType: 'architecture'
project_name: 'project-manager'
user_name: 'Sue'
date: '2026-03-17'
collaboration_notes: 'Multi-agent Party Mode review completed with architect, analyst, developer, and designer perspectives'
---

# Architecture Decision Document

_This document builds collaboratively through step-by-step discovery. Sections are appended as we work through each architectural decision together._

---

## Project Context Analysis

### Requirements Overview

**功能需求：** 共 34 条 FR，覆盖 7 个功能模块

| 模块 | FR 数量 | 架构复杂度 | 说明 |
|------|---------|-----------|------|
| 客户管理 | FR1–FR4 | 低 | 标准 CRUD + 关联查询 |
| 供应商管理 | FR5–FR7 | 低 | 标准 CRUD + 支付记录汇总 |
| 项目管理 | FR8–FR12 | 中 | 7 阶段状态机 + 多模块关联 |
| 费用管理 | FR13–FR21 | 高 | 三层数据关联（预算→支出→核销）+ 智能预填 + 多路径导入 |
| 收款管理 | FR22–FR26 | 中 | 阶段性计划 + 实收对比 |
| 报表统计 | FR27–FR30 | 中 | 聚合查询 + Excel 导出 |
| 系统功能 | FR31–FR34 | 中 | uni-id 用户体系 + 微信推送 |

**关键数据关系（影响数据模型设计）：**
- 项目 → 客户（多对一）
- 费用支出 → 项目、供应商、费用预算（多对多关联）
- 核销记录 → 银行流水、费用支出（一对一确认）
- 收款记录 → 收款计划、项目（多对一）
- 操作日志 → 所有写操作（粗粒度记录）

**非功能需求（驱动架构决策的关键 NFR）：**

| NFR | 要求 | 架构影响 |
|-----|------|---------|
| 响应时间 | 核心操作 < 2s | 首页聚合函数需优化，索引设计关键 |
| 数据准确性 | 财务数据零错误 | 所有导入操作强制人工确认步骤 |
| 安全 | 加密存储传输 + 权限控制 | uni-id 角色系统，云函数层鉴权 |
| 推送 | 智能触发（非固定时间）| uni-cloud 云函数监听业务状态变化 |
| 平台合规 | 微信小程序（类目/支付 API）| 需预留合规配置点 |

**规模与复杂度评估：**
- 主技术域：uniapp 移动端 + uni-cloud 云后端 + uni-admin PC 端
- 复杂度等级：中等（业务逻辑复杂度为主，非技术规模复杂）
- 预计架构组件数量：约 8–12 个主要组件

### Technical Constraints & Dependencies

| 约束项 | 具体内容 |
|--------|---------|
| 前端框架 | uniapp + Vue 3，微信小程序为主平台 |
| 后端/存储 | uni-cloud（需优化数据结构应对免费版限制） |
| PC 端框架 | uni-admin（DCloud 官方，内置 uni-id/权限/日志/菜单）|
| 用户体系 | uni-id 统一管理，小程序微信登录 + PC 用户名密码登录 |
| OCR 集成 | 腾讯云 OCR API（云函数中转），一期仅增值税发票 |
| Excel 导出 | 前端 SheetJS，无需后端文件服务 |
| 无离线需求 | 全在线，无需离线数据同步架构 |
| 深色模式 | 一期明确关闭（`darkmode: false`）|

### Cross-Cutting Concerns Identified

1. **认证与授权**：uni-id 跨双平台统一，MVP 全权限（所有用户默认主理人权限），Phase 2 细粒度角色配置
2. **数据准确性保护**：所有导入操作（Excel/OCR/手动）必须经「导入→解析→人工确认→提交」四步，云端事务保证原子性
3. **导入流程状态管理**：云端实现导入会话状态机（PENDING → PARSING → PARSED → REVIEWING → APPROVED → COMMITTING → SUCCESS/COMMIT_ERROR），确保中断恢复可靠
4. **操作日志**：粗粒度日志（操作类型+对象+时间），无需详细属性变化记录
5. **缓存策略**：加载数据（首页摘要、列表）可缓存 5-10 分钟；提交操作完全不缓存
6. **报表权限**：用户自主导出或主理人导出分享给他人，系统无需实现复杂的报表权限共享
7. **代码复用与分层**：单仓库管理，小程序页面和 PC 页面独立，云函数和数据模型共用

---

## Architecture Decision Outcomes (From Party Mode Collaboration)

### 1. User Permission Model

**决策：简化权限模型，MVP 全权限**

- **实际角色**：主理人（全权限）和项目经理（受限操作）
- **MVP 实现**：所有登录用户默认为主理人权限
- **Phase 2 演进**：在 `users` 表添加 `role` 字段，云函数层增加权限检查
- **简化理由**：财务人员是"旁观者"，仅查看报表，不进系统操作

### 2. Code Repository & Branching Strategy

**决策：单仓库，分支隔离，云函数共用**

项目结构约定：
```
project-manager/
├── /pages/**              // 小程序页面
├── /admin/**              // PC admin 页面（uni-admin）
├── /cloudfunctions/**     // 云函数（双端共享）
├── /utils/**              // 共用工具和业务逻辑
├── /types/**              // 共用 TypeScript 类型定义
```

**分支管理**：
- 小程序 feat 分支只改 `/pages`，不影响 PC
- PC feat 分支只改 `/admin`，不影响小程序
- 共用模块修改（云函数、类型、工具）影响双端，需要协调

**发布**：各端有独立版本号，同一 `main` 分支管理，根据功能完成情况灵活发布

### 3. Import Flow & State Management

**决策：云端状态机 + 事务保证原子性**

**导入会话状态转移：**
```
PENDING → PARSING → PARSED → REVIEWING → APPROVED → COMMITTING → SUCCESS
           ↓                      ↓                        ↓
        PARSE_ERROR          REVIEW_REJECT          COMMIT_ERROR
           ↓                      ↓                        ↓
        PENDING              PENDING                  PENDING
```

**数据模型（import_sessions 集合）：**
```javascript
{
  _id: 'session_xxx',
  type: 'excel' | 'ocr' | 'manual',
  status: 'PENDING' | 'PARSING' | 'PARSED' | 'REVIEWING' | 'APPROVED' | 'COMMITTING' | 'SUCCESS' | 'COMMIT_ERROR',
  userId: 'user_xxx',
  createTime: timestamp,
  parseResult: {
    records: [ { date, amount, memo, ... } ],
    errors: [ { index, reason } ]
  },
  approvedBy: 'user_xxx',
  approvedTime: timestamp,
  committedTime: timestamp,
  resultRecordIds: [ 'record_xxx', ... ] // 便于撤销或追溯
}
```

**事务性提交（云函数实现）：**
- 使用 MongoDB 事务：`db.runTransaction()`
- 批量插入 reconciliation_records + 更新 import_sessions 状态 → 原子操作
- 任何一步失败，整个事务回滚，数据库状态保持一致

### 4. Caching Strategy

**决策：加载数据缓存，提交数据无缓存**

| 数据类型 | 缓存策略 | TTL | 原因 |
|---------|---------|-----|------|
| 首页财务摘要 | 客户端缓存 + 后台定时刷新 | 5-10 分钟 | 非关键数据，聚合查询消耗，无离线需求 |
| 项目列表 | 客户端缓存 | 10 分钟 | 列表数据变化相对低频 |
| 提交操作（新增费用、核销、导入）| 无缓存，直接写数据库 | - | 金融数据准确性优先 |

### 5. Operation Logging

**决策：粗粒度日志，记录操作类型和对象**

**日志模型（operation_logs 集合）：**
```javascript
{
  _id: 'log_xxx',
  userId: 'user_xxx',
  action: 'CREATE_EXPENSE' | 'UPDATE_EXPENSE' | 'DELETE_EXPENSE' | 'RECONCILE_FLOW' | 'IMPORT_FLOWS' | 'EXPORT_REPORT',
  objectType: 'expense' | 'reconciliation' | 'project' | 'customer' | ...,
  objectId: 'xxx',
  timestamp: Date.now(),
  description: '创建费用记录'  // 可选，人类可读的描述
}
```

**记录原则**：
- 只记录"谁+做了什么+对谁+什么时间"
- 不记录详细属性变化（如"将金额从 1000 改成 1001"）
- 用于审计和故障追溯，不用于显示修改历史

### 6. Report & Export Permissions

**决策：用户自主导出，主理人可外发**

- **用户自己导出**：所有登录用户可导出自己有权限查看的数据
- **兼职财务获取**：主理人导出后，通过 Email/微信 等方式分享给财务人员（系统外处理）
- **系统无需实现**：复杂的报表共享权限、动态权限链接等机制

**实现方式**：
- 前端 SheetJS 生成 Excel 下载
- 或后端生成 Excel 后提供下载链接
- PC 端导出功能优先级：基础报表（应收/实收/应付/实付）> 明细数据导出

---

## Starter Template Decision

### Primary Technology Domain

**双平台全栈应用**：uniapp + Vue 3 微信小程序 + PC Web 管理后台

基于项目需求分析，采用：
- **前端框架**：uniapp 5.03+ (Vue 3 Composition API + TypeScript)
- **后端/存储**：uni-cloud（无服务器）+ MongoDB
- **用户认证**：uni-id（统一跨平台）
- **PC 管理后台**：uni-admin 2.5.13

### Selected Starter Strategy: Hybrid Approach

**分两部分初始化，共享云函数和数据模型：**

#### Part 1: 小程序主体（官方 uniapp starter）

**初始化命令**：
```bash
npx degit dcloudio/uni-preset-vue#vite-ts project-manager
cd project-manager
npm install
```

**版本信息**：
- uniapp 版本：5.03.2026031210+
- Vue 版本：3.x Composition API
- TypeScript：内置支持
- 构建工具：Vite（高效快速）
- Node.js 要求：18.x 或 20.x+

**优势**：
- ✅ DCloud 官方维护，生产级稳定性
- ✅ 条件编译机制内置，轻松处理 MP-WEIXIN vs H5 差异
- ✅ 云函数和 clientDB 集成就绪
- ✅ 项目轻量级，无冗余功能

#### Part 2: PC Web 管理后台（uni-admin 组件集成）

**方式**：在 `/admin` 目录下整合 uni-admin 组件，共享 `/cloudfunctions` 和数据模型

**参考**：uni-admin 2.5.13 官方模板

**关键配置**：
- uni-admin 的认证、权限、菜单系统复用
- 操作日志自动记录（符合你的粗粒度日志需求）
- uni-id 配置（路径：`/uniCloud/cloudfunctions/common/uni-id/config.json`）

### Project Structure After Initialization

```
project-manager/
├── pages/                           # 小程序业务页面
│   ├── index/                       # 首页
│   ├── project/                     # 项目管理
│   ├── expense/                     # 费用录入
│   ├── reconciliation/              # 核销流程
│   └── me/                          # 我的
├── admin/                           # PC Web 管理后台（uni-admin 集成）
│   ├── pages/
│   │   ├── login/                   # 登录页
│   │   ├── dashboard/               # 数据看板
│   │   ├── customer/                # 客户管理
│   │   ├── vendor/                  # 供应商管理
│   │   ├── project/                 # 项目管理
│   │   ├── expense/                 # 费用管理
│   │   ├── collection/              # 收款管理
│   │   ├── reports/                 # 报表统计
│   │   └── settings/                # 系统设置
│   └── utils/
├── components/                      # 共用 Vue 组件
│   ├── business/                    # 业务组件（C01-C10）
│   └── common/                      # 通用组件
├── utils/                           # 共用工具函数
│   ├── api.js                       # 云函数调用封装
│   ├── storage.js                   # 数据缓存工具
│   ├── validators.js                # 业务校验规则
│   └── algorithms.js                # 算法库（置信度匹配等）
├── types/                           # TypeScript 类型定义
│   ├── models.ts                    # 数据模型类型
│   ├── api.ts                       # API 响应类型
│   └── ui.ts                        # UI 组件 Props 类型
├── cloudfunctions/                  # uni-cloud 云函数（小程序+PC 共享）
│   ├── common/
│   │   └── uni-id/                  # 用户认证配置
│   ├── expense-create/              # 新增费用
│   ├── expense-list/                # 费用列表（支持分页）
│   ├── reconcile-import/            # 流水导入和匹配
│   ├── reconcile-commit/            # 核销提交（事务处理）
│   ├── summary-dashboard/           # 首页摘要聚合查询
│   └── export-report/               # 报表导出
├── uniCloud/
│   └── database/                    # 数据库 Schema 定义
│       ├── db_init.json
│       ├── customers.schema.json
│       ├── vendors.schema.json
│       ├── projects.schema.json
│       ├── expenses.schema.json
│       ├── budget_items.schema.json
│       ├── collections.schema.json
│       ├── bank_flows.schema.json
│       ├── reconciliations.schema.json
│       ├── import_sessions.schema.json
│       └── operation_logs.schema.json
├── static/                          # 静态资源
│   ├── mp-weixin/                   # 小程序专用资源
│   └── web/                         # Web 专用资源
├── pages.json                       # 小程序路由和导航配置
├── manifest.json                    # 应用元数据和打包配置
├── uni.scss                         # 全局设计 Token（颜色、间距、字体）
├── main.js                          # 应用入口
├── App.vue                          # 全局配置
└── vite.config.js                   # Vite 构建配置
```

### Architectural Decisions Provided by Starter

**Language & Runtime**：
- TypeScript + ES2020+，全类型检查覆盖
- Vue 3 Composition API + `<script setup>` 推荐语法

**Platform Differentiation**：
- 条件编译（`#ifdef MP-WEIXIN` / `#ifdef H5`）
- 运行时无冗余判断，编译时完全分离

**Development Experience**：
- Vite 热更新（毫秒级反应）
- HBuilderX 集成开发环境
- ESLint + Prettier 开箱即用

**Cloud Integration**：
- uni-cloud clientDB 初始化
- uni-id 认证系统预留配置点
- 云函数目录结构规范

### Project Initialization Steps

**Step 1: 创建项目**
```bash
npx degit dcloudio/uni-preset-vue#vite-ts project-manager
cd project-manager
npm install
```

**Step 2: 初始化云开发空间**
- 在 HBuilderX 中右键 `uniCloud` 目录 → 创建云开发服务空间
- 选择地区和服务商（腾讯云/阿里云）
- 确认并绑定

**Step 3: 上传初始化云函数和数据库**
```bash
# HBuilderX 中右键 cloudfunctions 目录
# 上传并部署 → 初始化 uni-id 相关云函数
```

**Step 4: 本地开发启动**
```bash
# 小程序开发版
npm run dev:mp-weixin

# PC Web 开发版
npm run dev:h5
```

**Step 5: 第一个实现故事**
项目初始化本身应该作为第一个 Story，确保：
- ✅ 小程序端能正常编译和预览
- ✅ PC Web 端能正常启动
- ✅ 云函数部署成功
- ✅ uni-id 认证流程测试通过

### Why This Approach

1. **官方 starter 保证稳定性**：DCloud 原生支持，避免第三方库依赖风险
2. **条件编译天然支持双平台差异**：不需要自建 if/else 判断逻辑
3. **uni-admin 组件可选集成**：PC 端功能完整但不强制依赖，保持小程序端轻量
4. **符合 MVP→Phase2 演进**：权限、日志、菜单等框架预留，后续迭代无需大改
5. **单仓库分支隔离**：小程序 feat 和 PC feat 独立，云函数共享

---

## Core Architectural Decisions

### Decision Priority Analysis

**关键决策（阻塞实现）：**
- 数据库选择与云服务商确定
- 认证流程设计（小程序 + PC 差异化）
- API 设计与错误处理标准
- 前端状态管理方案
- 云函数模块化分组

**重要决策（塑造架构）：**
- Token 生命周期和刷新策略
- 云函数幂等性保证机制
- 前端组件库使用策略
- 云函数性能优化方案

**可推迟决策（MVP 后）：**
- 详细的监控和告警策略
- 自动化 CI/CD 流程
- 数据存储层加密（MVP 不加密）
- 分包策略（小程序一期不分包）

---

### 数据架构决策

#### 1. 数据库与云服务商选择

**决策：使用支付宝云免费版 + MongoDB**

**理由：**
- ✅ 支付宝云免费版额度充足（符合 MVP 小规模需求）
- ✅ uni-cloud 与支付宝云深度集成，官方首选
- ✅ 成本最低，后续付费选项灵活
- ✅ 云函数、数据库、存储一体服务

**MongoDB 特性：**
- 文档型数据库，天然支持 JSON 数据结构
- 支持事务（4.0+），符合原子性要求
- uni-cloud 官方支持的默认数据库
- clientDB 直接查询模式（前端可直接操作）

**数据库连接配置：**
```javascript
// cloudfunctions/common/uni-config-center/uni-id/config.json
{
  "databaseUrl": "aliyun", // 支付宝云
  "service": {
    "aliyun": {
      "endpoint": "***"
    }
  }
}
```

#### 2. 数据模型设计策略

**决策：采用反范式化模式优化查询性能**

**核心原则：**
- ✅ 冗余字段优于多次关联查询（支付宝云免费版并发限制）
- ✅ 首页摘要数据需实时汇总，冗余存储常用聚合结果
- ✅ 费用支出表冗余"关联的预算金额"和"匹配状态"

**数据模型冗余示例：**

```javascript
// expenses 集合（费用支出）
{
  _id: 'expense_xxx',
  projectId: 'project_xxx',
  projectName: '客户A-装修项目',  // 冗余：避免每次查询费用都要 join 项目表
  vendorId: 'vendor_xxx',
  vendorName: '劳务队',          // 冗余
  amount: 10000,
  category: 'labor',
  budgetItemId: 'budget_xxx',
  budgetAmount: 15000,           // 冗余：预算金额，便于快速判断是否超预算
  status: 'approved',            // 冗余：审批状态
  reconcilStatus: 'pending',     // 冗余：核销状态（已核销/待核销/无需核销）
  createdAt: timestamp,
  createdBy: 'user_xxx'
}

// 首页摘要冗余表（每小时更新一次）
{
  _id: 'dashboard_summary_current_month',
  month: '2026-03',
  shouldReceive: 450000,         // 本月应收
  actualReceived: 320000,        // 本月已到账
  pendingReconcile: 12,          // 待核销笔数
  lastUpdated: timestamp
}
```

**更新策略：**
- 费用新增时，事务性更新 expenses 记录和 dashboard_summary
- 核销成功时，更新 expenses 的 reconcilStatus 和摘要表的 pendingReconcile 计数
- 首页查询直接读摘要表（不做聚合），响应时间 < 100ms

#### 3. 数据校验策略

**决策：前端轻校验 + 云端严格校验 + 事务保证**

**分工：**
- **前端校验**：输入格式、必填项、长度限制（UX 友好，快速反馈）
- **云端校验**：业务规则、权限检查、重复性检查、事务一致性（安全关键）

**校验链路示例（新增费用）：**
```javascript
// 前端（pages/expense/create.vue）
async function submitExpense() {
  // 快速格式校验
  if (!form.amount || form.amount <= 0) {
    showError('金额必须大于 0')
    return
  }

  // 提交到云函数
  const result = await uniCloud.callFunction({
    name: 'expense-create',
    data: form
  })
}

// 云函数（cloudfunctions/expense-create/index.js）
async function main(event) {
  const { projectId, amount, category, vendorId } = event

  // 严格校验
  if (!projectId || !amount || !category) {
    throw new Error('缺少必要字段')
  }

  // 权限检查
  if (!await hasPermission(event.userInfo, 'create_expense')) {
    throw new Error('无权限')
  }

  // 业务校验：项目是否存在
  const project = await db.collection('projects').doc(projectId).get()
  if (!project.data) {
    throw new Error('项目不存在')
  }

  // 业务校验：供应商是否存在
  const vendor = await db.collection('vendors').doc(vendorId).get()
  if (!vendor.data) {
    throw new Error('供应商不存在')
  }

  // 事务性写入
  await db.runTransaction(async (transaction) => {
    // 插入费用记录
    await transaction.write.add('expenses', {
      projectId, projectName: project.data.name,
      vendorId, vendorName: vendor.data.name,
      amount, category,
      status: 'pending',
      reconcilStatus: 'pending',
      createdAt: Date.now(),
      createdBy: event.userInfo._id
    })

    // 更新摘要表
    await transaction.write.update('dashboard_summary', {
      // 增加待核销笔数或其他聚合
    })
  })

  return { success: true }
}
```

---

### 认证与安全决策

#### 1. 小程序登录流程

**决策：微信授权登录（一键登录）**

- ✅ 用户体验最优（一键授权，无需记住密码）
- ✅ 微信提供登录状态管理（session）
- ✅ 与 uni-id 深度集成

**流程：**
```
用户点击"微信授权登录"
  → 小程序调用 wx.getUserInfo()
  → 获得 code + encryptedData
  → 发送到云函数 uni-id-login
  → uni-id 解密验证
  → 返回 token 和用户信息
  → 本地存储 token
```

#### 2. PC Web 登录流程

**决策：用户名密码登录 + 微信授权作为备选**

- ✅ PC Web 用户习惯
- ✅ MVP 只需用户名密码
- ✅ 微信授权作为 Phase 2 快捷登录选项

**PC 端登录表单：**
```
用户名（邮箱或手机号）
密码（加密传输）
登录按钮
  ├─ 校验用户名密码 → uni-id-login
  └─ 返回 token
```

#### 3. Token 生命周期

**决策：较长的有效期 + 刷新令牌机制**

- **Access Token TTL**：7 天（相比标准 1-2 小时更长）
  - 理由：内部管理工具，用户相对固定，安全风险低
  - uni-id 默认就是 7 天
- **Refresh Token TTL**：30 天
  - 用户长期不登录超过 30 天，需要重新授权

**配置（uni-id config）：**
```javascript
{
  "tokenSecret": "your-secret-key",
  "tokenExpiresIn": 604800,  // 7 天，单位秒
  "refreshTokenExpiresIn": 2592000  // 30 天
}
```

#### 4. 数据加密策略

**决策：传输层加密（TLS），存储层不加密**

- **传输层**：HTTPS 自动加密（云服务商提供）
- **存储层**：MVP 不加密敏感字段
  - 理由：uni-cloud 服务商已提供访问控制，足够保护内部工具
  - Phase 2 可按需添加字段级加密

---

### API & 通信设计

#### 1. 云函数模块化策略

**决策：按功能模块分组云函数**

**模块划分（与故事卡对应）：**
```
cloudfunctions/
├── common/              # 共用库
│   ├── uni-id/         # 认证系统
│   ├── middleware/     # 权限检查、日志记录
│   └── validators/     # 数据校验规则
├── customer/           # 客户管理模块
│   ├── customer-list
│   ├── customer-create
│   ├── customer-update
│   └── customer-delete
├── expense/            # 费用管理模块
│   ├── expense-list
│   ├── expense-create
│   ├── expense-update
│   └── reconcile-import
├── collection/         # 收款管理模块
│   ├── collection-list
│   └── collection-create
├── report/             # 报表导出模块
│   └── export-report
└── system/             # 系统功能
    ├── dashboard-summary
    └── operation-log
```

**优势：**
- ✅ 模块独立，团队协作无冲突
- ✅ 部署灵活（可单独热更新某个模块）
- ✅ 易于单元测试

#### 2. 错误处理标准

**决策：统一错误码 + 错误消息体**

**统一错误响应格式：**
```javascript
// 成功
{
  success: true,
  data: { ... }
}

// 失败
{
  success: false,
  errorCode: 'EXPENSE_AMOUNT_INVALID',  // 机器可读
  message: '费用金额必须大于 0',         // 人类可读
  statusCode: 400                        // HTTP 状态码
}

// 特殊错误（权限）
{
  success: false,
  errorCode: 'AUTH_PERMISSION_DENIED',
  message: '无权限执行此操作',
  statusCode: 403
}
```

**常用错误码列表：**
- `SUCCESS` (200)
- `INVALID_PARAM` (400) - 参数校验失败
- `AUTH_UNAUTHORIZED` (401) - 未登录
- `AUTH_PERMISSION_DENIED` (403) - 无权限
- `RESOURCE_NOT_FOUND` (404) - 资源不存在
- `CONFLICT` (409) - 数据冲突（如重复核销）
- `INTERNAL_ERROR` (500) - 服务器错误

#### 3. 幂等性保证机制

**决策：使用版本号（乐观锁）防止重复提交**

**场景：** 用户点击"确认核销"后网络波动，再点一次，可能重复核销

**解决方案：**
```javascript
// 核销记录表
{
  _id: 'reconciliation_xxx',
  flowId: 'flow_xxx',
  expenseId: 'expense_xxx',
  version: 1,           // 版本号，初始为 1
  status: 'confirmed',
  confirmedAt: timestamp
}

// 云函数：核销提交
async function reconcileCommit(event) {
  const { reconciliationId, version } = event

  // 乐观锁：带上版本号查询
  const doc = await db.collection('reconciliations')
    .where({
      _id: reconciliationId,
      version: version
    })
    .get()

  if (!doc.data || doc.data.length === 0) {
    // 版本号不匹配，说明已经被修改过
    throw new Error('该核销记录已被处理，请刷新重试')
  }

  // 原子更新：version 递增
  await db.collection('reconciliations')
    .doc(reconciliationId)
    .update({
      status: 'confirmed',
      version: db.command.inc(1),  // 版本号递增
      confirmedAt: Date.now()
    })
}

// 前端：提交时携带版本号
async function confirmReconciliation(reconciliation) {
  await uniCloud.callFunction({
    name: 'reconcile-commit',
    data: {
      reconciliationId: reconciliation._id,
      version: reconciliation.version
    }
  })
}
```

---

### 前端架构决策

#### 1. 状态管理方案

**决策：使用 Pinia（如果与 uniapp 兼容）**

**验证兼容性：** Pinia 4.x 支持 uniapp（通过 uni_modules 安装）

**选择理由：**
- ✅ 比 Vuex 更轻量，API 更直观
- ✅ Vue 3 官方推荐
- ✅ TypeScript 支持更好

**全局状态（stores/index.js）：**
```javascript
import { defineStore } from 'pinia'
import { ref, computed } from 'vue'

// 用户状态
export const useUserStore = defineStore('user', () => {
  const userInfo = ref(null)
  const token = ref(null)

  const isLoggedIn = computed(() => !!token.value)

  function setUser(user) {
    userInfo.value = user
  }

  return { userInfo, token, isLoggedIn, setUser }
})

// 费用列表缓存状态
export const useExpenseStore = defineStore('expense', () => {
  const expenseList = ref([])
  const expenseCache = ref({})  // 按项目 ID 缓存

  function setExpenseList(list) {
    expenseList.value = list
  }

  return { expenseList, expenseCache, setExpenseList }
})
```

**主要状态模块：**
- `userStore` - 用户信息、token、权限
- `expenseStore` - 费用列表、缓存
- `projectStore` - 项目列表
- `dashboardStore` - 首页摘要数据

#### 2. 组件库使用策略

**决策：uni-ui 为主，业务组件自定义**

- ✅ uni-ui 覆盖基础交互（Button、Input、Modal 等）
- ✅ 业务组件（C01-C10）自定义实现，遵循 uni-ui 设计语言
- ✅ 避免过度依赖第三方库，减少包体积

**uni-ui 组件使用清单：**
```
✓ uni-popup（底部 Action Sheet）
✓ uni-easyinput（表单输入）
✓ uni-search-bar（搜索框）
✓ uni-list + uni-list-item（列表）
✓ uni-load-more（上拉加载）
✓ uni-button（按钮）
✓ uni-tag（标签）
✓ uni-icons（图标）
```

**自定义业务组件：**
```
C01 - FinanceSummaryBanner    # 首页财务摘要
C02 - ProjectCard             # 项目卡片
C03 - QuickEntryBar           # 快速录入条
C04 - ReconciliationRow       # 核销流水行
C05 - CategoryQuickPicker     # 费用类别快选
C06 - WarningBanner           # 预警条
C07 - ConfidenceLevelBadge    # 置信度标签
C08 - BatchConfirmSheet       # 批量确认弹层
C09 - AmountInput             # 金额输入增强
C10 - OcrConfirmSheet         # OCR 确认表单
```

#### 3. 路由与分包策略

**决策：不分包（MVP 阶段）**

**理由：**
- ✅ 功能模块数量不大，总包体积预计 < 2MB
- ✅ 分包会增加管理复杂度
- ✅ Phase 2 可按需添加分包（如果包体积超过 2MB）

**路由配置（pages.json）：**
```json
{
  "pages": [
    {"path": "pages/index/index", "style": {"navigationBarTitleText": "首页"}},
    {"path": "pages/project/list", "style": {"navigationBarTitleText": "项目"}},
    {"path": "pages/expense/create", "style": {"navigationBarTitleText": "新增费用"}},
    {"path": "pages/reconciliation/list", "style": {"navigationBarTitleText": "核销"}},
    {"path": "pages/me/index", "style": {"navigationBarTitleText": "我的"}}
  ],
  "tabBar": {
    "color": "#666666",
    "selectedColor": "#1677FF",
    "borderStyle": "black",
    "backgroundColor": "#ffffff",
    "list": [
      {"pagePath": "pages/index/index", "text": "首页", "iconPath": "", "selectedIconPath": ""},
      {"pagePath": "pages/project/list", "text": "项目", "iconPath": "", "selectedIconPath": ""},
      {"pagePath": "pages/reconciliation/list", "text": "核销", "iconPath": "", "selectedIconPath": ""},
      {"pagePath": "pages/me/index", "text": "我的", "iconPath": "", "selectedIconPath": ""}
    ]
  }
}
```

---

### 基础设施与部署决策

#### 1. 云服务商确认

**决策：支付宝云**

**已在数据架构部分确认**

#### 2. CI/CD 流程

**决策：手动部署（MVP 阶段）**

**部署流程：**
```
开发完成 → 本地测试通过 → 提交 PR
  → Code Review 通过 → 合并到 main
  → 手动构建小程序和 Web
  → 手动上传小程序到微信后台
  → 手动上传 Web 到支付宝云
```

**手动构建命令：**
```bash
# 小程序构建
npm run build:mp-weixin
# 输出到 dist/build/mp-weixin

# PC Web 构建
npm run build:h5
# 输出到 dist/build/h5
```

**理由：**
- ✅ MVP 部署频率低（1-2 周一版）
- ✅ 简化配置复杂度
- ✅ Phase 2 可引入 GitHub Actions 自动化

#### 3. 监控与日志

**决策：不引入复杂监控（MVP 阶段）**

- ✅ 支付宝云提供基础监控（云函数执行时间、错误率）
- ✅ 手动检查日志（通过 uni-admin 查看 operation_logs 表）
- ✅ Phase 2 可集成第三方监控（如 Sentry）

---

## 决策影响分析

### 实现顺序

1. **基础设施建立** - 云服务空间初始化、uni-id 配置、数据库 Schema 定义
2. **认证系统** - uni-id 集成、小程序微信登录、PC 用户名密码登录
3. **首页与数据聚合** - dashboard-summary 表、首页摘要查询、缓存策略
4. **费用管理** - expense 表、创建/查询/更新云函数、UI 组件
5. **核销流程** - import_sessions 状态机、reconciliation 表、事务处理
6. **报表导出** - export-report 云函数、SheetJS 集成
7. **PC 管理后台** - uni-admin 集成、客户/供应商/项目管理页面

### 跨组件依赖关系

```
uni-id 认证
  ↓
用户信息 → Pinia userStore
  ↓
首页数据查询 → dashboard-summary（需要 userId 过滤）
  ↓
费用新增 → expense 表、操作日志记录
  ↓
核销导入 → import_sessions 状态机、reconciliation 事务处理
  ↓
报表导出 → 汇总查询、SheetJS 生成
```

### 版本控制策略

- **小程序版本**：从 1.0.0 开始，按功能迭代递增
- **PC Web 版本**：同步小程序版本
- **云函数版本**：不单独版本化，与应用版本绑定
- **数据库 Schema 版本**：在 uniCloud/database/db_init.json 中记录演变

---

## Implementation Patterns & Consistency Rules

### 目的

定义 AI 智能体在实现代码时必须遵循的一致性模式，防止不同智能体之间的冲突和不一致，确保所有代码自动集成。

### 已识别的关键冲突点

10 个最容易产生 AI 实现冲突的地方：

| # | 冲突点 | 影响范围 | 优先级 |
|---|--------|---------|--------|
| 1 | 数据库表命名 | 数据模型 | 🔴 关键 |
| 2 | 云函数命名 | API 接口 | 🔴 关键 |
| 3 | 前端文件结构 | 代码组织 | 🔴 关键 |
| 4 | API 响应格式 | 前后端交互 | 🔴 关键 |
| 5 | 错误处理 | 异常控制流 | 🟡 重要 |
| 6 | 加载状态管理 | 状态管理 | 🟡 重要 |
| 7 | 日期时间格式 | 数据序列化 | 🟡 重要 |
| 8 | HTTP 状态码 | API 语义 | 🟢 次要 |
| 9 | Vue 3 组件风格 | 代码风格 | 🟢 次要 |
| 10 | 事件命名 | 通信约定 | 🟢 次要 |

---

### 1. 数据库表命名约定

**规则：小驼峰 + 复数/特定形式**

✅ **推荐**：
```javascript
// 表名
users, projects, expenses, budgetItems, bankFlows, reconciliations, importSessions, operationLogs

// 列名（全部小驼峰）
{
  _id: "xxx",              // MongoDB 默认 ObjectId
  userId: "xxx",           // 外键用小驼峰，不用 user_id
  createdAt: timestamp,    // 时间戳，单位毫秒
  updatedAt: timestamp,
  isArchived: boolean,     // 布尔值用 is/has 前缀
  status: "pending",
  reconcilStatus: "pending"
}
```

❌ **禁止**：
```javascript
// 混乱命名
user_info, ExpenseRecord, BANK_FLOW_TABLE  // 大小混合、下划线、全大写
{
  user_id: "xxx",         // 下划线
  create_at: "2026-03-17", // 下划线 + 字符串日期
  Archive: true           // 首字母大写
}
```

**AI 一致性检查**：所有表和列名必须符合此格式，代码审查时自动验证。

---

### 2. 云函数命名约定

**规则：`<模块>-<操作>` 格式，全小写，使用连字符**

✅ **推荐**：
```javascript
// 客户管理
customer-list
customer-create
customer-update
customer-delete

// 费用管理
expense-list
expense-create
expense-update
reconcile-import
reconcile-commit

// 系统
dashboard-summary
export-report
operation-log-query
```

❌ **禁止**：
```javascript
// 混乱命名
CustomerList, createExpense, RECONCILE_COMMIT  // 驼峰、大小混合
getExpenseList, expenseCreate                  // 用动词前缀 get/create

// 单个单词
expense, reconcile                             // 太通用，不明确操作
```

**云函数统一入口签名**：
```javascript
// cloudfunctions/{name}/index.js
module.exports = async (event) => {
  const { /* 参数 */ } = event

  // 统一返回格式（见第 4 点）
  return {
    success: true,
    data: { ... }
  }
}
```

---

### 3. 前端文件结构

**规则：按功能模块分组（Feature-first，不是 Type-first）**

✅ **推荐结构**：
```
pages/
├── index/
│   ├── index.vue              # 首页主文件
│   ├── composables/
│   │   └── useDashboard.js    # 首页业务逻辑
│   └── components/
│       ├── SummaryBanner.vue
│       └── ProjectList.vue
├── project/
│   ├── list.vue
│   ├── detail.vue
│   ├── composables/
│   │   └── useProject.js
│   └── components/
│       ├── ProjectCard.vue
│       └── ProjectForm.vue
├── expense/
│   ├── create.vue
│   ├── list.vue
│   ├── composables/
│   │   └── useExpense.js
│   └── components/
└── reconciliation/
    ├── list.vue
    ├── composables/
    │   └── useReconciliation.js
    └── components/

utils/                # 跨模块工具
├── api.js            # 云函数调用包装
├── validators.js     # 业务校验规则
├── format.js         # 格式化工具
└── storage.js        # 本地存储

components/           # 全局共用组件
├── business/         # 业务组件（C01-C10）
│   ├── FinanceSummaryBanner.vue
│   ├── ProjectCard.vue
│   └── ...
└── common/           # 基础组件
    ├── forms/
    └── layout/

stores/               # Pinia 状态管理
├── user.js
├── expense.js
├── project.js
└── dashboard.js
```

❌ **禁止**：
```
pages/
├── components/       # 类型优先，混乱
├── views/
├── containers/
└── ...
```

**文件命名**：
- 页面文件：`list.vue`, `detail.vue`, `create.vue`（全小写，不用 `-`）
- 组件文件：`FinanceSummaryBanner.vue`（PascalCase）
- 工具/Hook：`useDashboard.js`, `validators.js`（驼峰）

---

### 4. API 响应格式

**规则：统一包裹格式，所有响应都返回此结构**

✅ **成功响应**：
```javascript
{
  success: true,
  data: {
    id: "expense_xxx",
    projectId: "project_xxx",
    amount: 10000,
    // ... 业务数据
  }
}
```

✅ **列表响应（带分页）**：
```javascript
{
  success: true,
  data: {
    list: [
      { id: "xxx", name: "项目A" },
      { id: "yyy", name: "项目B" }
    ],
    total: 50,
    pageNo: 1,
    pageSize: 20
  }
}
```

✅ **错误响应**：
```javascript
{
  success: false,
  errorCode: "INVALID_PARAM",        // 机器可读，全大写
  message: "金额必须大于 0",          // 用户可读
  statusCode: 400                    // HTTP 状态码
}
```

**前端调用模式**：
```javascript
// utils/api.js
async function callCloud(name, data) {
  try {
    const response = await uniCloud.callFunction({ name, data })
    const result = response.result

    if (result.success) {
      return result.data  // 直接返回业务数据
    } else {
      throw new Error(result.errorCode)
    }
  } catch (err) {
    return { success: false, error: err.message }
  }
}

// 页面中
const data = await callCloud('expense-create', form)
if (data.success !== false) {
  // 成功处理
  expenses.value.push(data)
} else {
  // 错误处理
  uni.showToast({ title: data.error, icon: 'none' })
}
```

---

### 5. 错误处理模式

**规则：云函数抛异常 → 统一包装器捕获 → 前端统一展示**

✅ **云函数中的错误处理**：
```javascript
async function expenseCreate(event) {
  // 参数校验
  if (!event.amount || event.amount <= 0) {
    throw new Error('INVALID_PARAM: 金额必须大于 0')
  }

  // 权限检查
  if (!await hasPermission(event.userInfo, 'create_expense')) {
    throw new Error('AUTH_PERMISSION_DENIED: 无权限')
  }

  // 业务逻辑
  const project = await db.collection('projects').doc(event.projectId).get()
  if (!project.data) {
    throw new Error('RESOURCE_NOT_FOUND: 项目不存在')
  }

  // 返回成功
  return { success: true, data: result }
}
```

❌ **禁止的错误处理**：
```javascript
// 不要返回错误对象，用异常
if (!event.amount) {
  return { success: false, error: '...' }  // ❌ 不一致
}

// 不要吞掉异常
try {
  // ...
} catch (err) {
  return { success: false }  // ❌ 丢失错误信息
}
```

**错误码标准**：
| 错误码 | HTTP Code | 场景 |
|--------|-----------|------|
| `SUCCESS` | 200 | 成功 |
| `INVALID_PARAM` | 400 | 参数校验失败 |
| `AUTH_UNAUTHORIZED` | 401 | 未登录 |
| `AUTH_PERMISSION_DENIED` | 403 | 无权限 |
| `RESOURCE_NOT_FOUND` | 404 | 资源不存在 |
| `CONFLICT` | 409 | 数据冲突（如重复核销） |
| `INTERNAL_ERROR` | 500 | 服务器错误 |

---

### 6. 加载状态管理

**规则：Pinia 全局 loading + 本地操作态**

✅ **推荐模式**：
```javascript
// stores/ui.js
export const useUIStore = defineStore('ui', () => {
  const globalLoading = ref(false)

  function setLoading(value) {
    globalLoading.value = value
  }

  return { globalLoading, setLoading }
})

// 页面中：本地操作态 + 防重复提交
const form = ref({ amount: 0 })
const submitting = ref(false)  // 本地操作态，防双击

async function submit() {
  if (submitting.value) return  // 已提交，禁用重复点击

  submitting.value = true
  try {
    await callCloud('expense-create', form.value)
    uni.showToast({ title: '成功', icon: 'success' })
    form.value = { amount: 0 }  // 重置表单
  } catch (err) {
    uni.showToast({ title: err.message, icon: 'none' })
  } finally {
    submitting.value = false
  }
}
```

❌ **禁止**：
```javascript
// 不要在多处重复定义 loading
const loading = ref(false)
const isSubmitting = ref(false)
const pending = ref(false)  // 同一概念多个变量

// 不要依赖服务器来做防重复（太慢）
async function submit() {
  await callCloud('create', ...)  // 没有本地防护
}
```

---

### 7. 日期时间格式

**规则：后端统一返回时间戳（毫秒），前端本地格式化**

✅ **推荐**：
```javascript
// 云函数返回
{
  success: true,
  data: {
    createdAt: 1710748800000,  // 时间戳，单位毫秒
    updatedAt: 1710835200000
  }
}

// 前端显示
<template>
  <div>{{ formatDate(expense.createdAt, 'YYYY-MM-DD HH:mm') }}</div>
</template>

// utils/format.js
function formatDate(timestamp, format = 'YYYY-MM-DD') {
  const date = new Date(timestamp)
  const year = date.getFullYear()
  const month = String(date.getMonth() + 1).padStart(2, '0')
  const day = String(date.getDate()).padStart(2, '0')
  const hour = String(date.getHours()).padStart(2, '0')
  const minute = String(date.getMinutes()).padStart(2, '0')

  return format
    .replace('YYYY', year)
    .replace('MM', month)
    .replace('DD', day)
    .replace('HH', hour)
    .replace('mm', minute)
}
```

❌ **禁止**：
```javascript
// ISO 字符串（字符串太大）
{ createdAt: '2026-03-17T12:00:00Z' }

// 秒级时间戳（需要乘以 1000）
{ createdAt: 1710748800 }

// 多种时间格式混用
{ createdAt: timestamp, updatedAt: '2026-03-17', expiredAt: '2026-03-31 23:59:59' }
```

---

### 8. HTTP 状态码使用

**规则：所有响应都返回 200（uni-cloud 限制），通过 errorCode 和 statusCode 字段区分**

✅ **统一做法**：
```javascript
// ✅ 所有响应都是 HTTP 200
{
  success: true,
  statusCode: 200,           // 业务状态码
  data: { ... }
}

{
  success: false,
  statusCode: 400,           // 业务状态码
  errorCode: 'INVALID_PARAM',
  message: '参数错误'
}

{
  success: false,
  statusCode: 403,           // 业务状态码
  errorCode: 'AUTH_PERMISSION_DENIED',
  message: '无权限'
}
```

❌ **禁止**：
```javascript
// 不要依赖 HTTP 状态码区分（uni-cloud 都返回 200）
response.status === 400  // ❌ 永远不会成立

// 不要在不同地方用不同的字段名
{ httpCode: 400 }   // ❌
{ code: 'INVALID' } // ❌
```

---

### 9. Vue 3 组件风格

**规则：全面使用 `<script setup>` + Composition API**

✅ **推荐**：
```vue
<template>
  <div class="expense-form">
    <uni-easyinput
      v-model="form.amount"
      type="digit"
      placeholder="输入金额"
    />
    <uni-button @click="submit" :loading="submitting">
      提交
    </uni-button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useExpenseStore } from '@/stores/expense'
import { callCloud } from '@/utils/api'

const expenseStore = useExpenseStore()
const form = ref({ amount: 0, projectId: '' })
const submitting = ref(false)

async function submit() {
  if (!form.value.amount || !form.value.projectId) {
    uni.showToast({ title: '请填写所有字段', icon: 'none' })
    return
  }

  submitting.value = true
  try {
    const result = await callCloud('expense-create', form.value)
    uni.showToast({ title: '创建成功', icon: 'success' })
    expenseStore.addExpense(result)
  } finally {
    submitting.value = false
  }
}
</script>

<style scoped>
.expense-form {
  padding: 20rpx;
}
</style>
```

❌ **禁止**：
```vue
<script>
export default {
  setup() {
    // 传统 setup() 函数
    return { ... }
  }
}
</script>

<script>
export default {
  data() {
    return { ... }  // Options API，过时
  },
  methods: { ... }
}
</script>
```

---

### 10. 事件命名与状态命名

**规则：Pinia action 和事件都用驼峰，避免混淆**

✅ **推荐**：
```javascript
// stores/expense.js（Pinia）
export const useExpenseStore = defineStore('expense', () => {
  const expenseList = ref([])

  // action：用驼峰
  async function createExpense(data) {
    const result = await callCloud('expense-create', data)
    expenseList.value.push(result)
    return result
  }

  async function deleteExpense(id) {
    await callCloud('expense-delete', { id })
    expenseList.value = expenseList.value.filter(e => e._id !== id)
  }

  async function fetchExpenses(projectId) {
    const { list, total } = await callCloud('expense-list', { projectId })
    expenseList.value = list
    return { list, total }
  }

  return { expenseList, createExpense, deleteExpense, fetchExpenses }
})

// 组件中调用
await expenseStore.createExpense(form)  // 一致的命名
```

❌ **禁止**：
```javascript
// 混乱命名
async create_expense() { }       // 下划线
async addExpense() { }           // add vs create 混用
async getExpenses() { }          // get vs fetch 混用
async ExpenseCreate() { }        // PascalCase

// 状态命名混乱
const expense_list = ref([])     // 下划线
const ExpenseList = ref([])      // PascalCase
const expenseData = ref([])      // data 太通用
```

---

### AI 一致性检查清单

所有 AI 智能体在编写代码前必须确认：

- [ ] 数据库表和列名是否符合小驼峰规范？
- [ ] 云函数是否用 `<模块>-<操作>` 命名？
- [ ] 前端文件是否按功能模块（Feature-first）组织？
- [ ] API 响应是否都用 `{ success, data/errorCode/message, statusCode }` 格式？
- [ ] 错误处理是否通过异常 + 统一包装器实现？
- [ ] 加载状态是否用本地 `ref` + 防重复点击？
- [ ] 日期是否都是时间戳（毫秒）格式？
- [ ] Vue 组件是否全用 `<script setup>`？
- [ ] Pinia action 是否用驼峰命名？

**代码审查时自动检查此清单。**

---

## 下一步

这份架构文档已完整定义：

✅ **项目上下文** - 需求、NFR、技术约束
✅ **核心决策** - 技术栈、权限模型、导入流程、缓存、日志
✅ **实现模式** - 命名、结构、格式、错误处理一致性

**最后步骤：** 需要定义完整的项目目录结构和初始化检查清单。

---
