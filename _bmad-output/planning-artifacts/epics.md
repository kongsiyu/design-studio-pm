---
stepsCompleted: [step-01-validate-prerequisites, step-02-design-epics, step-03-create-stories, party-mode-review-complete]
inputDocuments:
  - '_bmad-output/planning-artifacts/prd.md'
  - '_bmad-output/planning-artifacts/architecture.md'
  - '_bmad-output/planning-artifacts/ux-design-specification.md'
lastUpdated: '2026-03-17'
epicStructureApproved: true
storiesCreated: true
totalEpics: 10
totalStories: 73
storiesAddedInPartyMode: [1.9, 1.10]
story1_9_and_1_10: "Global data model design & design review gate - prevents data conflicts across Epic 2-9"
dependencyAuditStatus: "COMPLETE"
dependencyAuditFile: '_bmad-output/planning-artifacts/dependency-audit.md'
criticalGatingPoint: "Story 1.10 approval gates all Epics 2-9 development"
---

# project-manager - Epic Breakdown

## Overview

This document provides the complete epic and story breakdown for project-manager, decomposing the requirements from the PRD, UX Design if it exists, and Architecture requirements into implementable stories.

## Requirements Inventory

### Functional Requirements

**客户管理（4条）**
- FR1: 用户可以创建、查看、编辑、删除客户信息
- FR2: 用户可以将客户与项目关联
- FR3: 用户可以按客户查看项目列表
- FR4: 用户可以查看客户的分析数据（如项目数量、总金额）

**供应商管理（3条）**
- FR5: 用户可以创建、查看、编辑、删除供应商信息
- FR6: 用户可以记录供应商联系方式和网店链接
- FR7: 用户可以查看供应商的支付记录汇总

**项目管理（5条）**
- FR8: 用户可以创建项目并关联客户
- FR9: 用户可以设置项目的基本信息（名称、地址、预算等）
- FR10: 用户可以将项目状态在7个阶段中切换（立项→需求确认→施工→验收→结算→维保→归档）
- FR11: 用户可以查看所有项目的进度列表
- FR12: 用户可以为项目记录位置信息（可选）

**费用管理（9条）**
- FR13: 用户可以为项目录入费用预算
- FR14: 用户可以记录实际支出费用
- FR15: 用户可以将实际支出与预算关联进行核销
- FR16: 用户可以快速选择费用类别（智能推荐）
- FR17: 系统可以自动填充常用数据（智能默认值）
- FR18: 用户可以手动录入、导入银行流水进行核销
- FR19: 系统对关键操作进行二次确认
- FR20: 用户可以直接添加无计划的付款记录
- FR21: 系统可以为无计划的付款自动生成应付款/预算记录

**收款管理（5条）**
- FR22: 用户可以为项目设置阶段性收款计划（定金/中期款/尾款）
- FR23: 用户可以创建临时性收款需求（如项目变更产生的额外费用）
- FR24: 用户可以记录已收款项
- FR25: 收款可选择与项目阶段关联（可选）
- FR26: 用户可以查看应收/已收对比

**报表统计（4条）**
- FR27: 用户可以查看应收应付比例报表
- FR28: 用户可以查看实收实付比例报表
- FR29: 用户可以查看逾期未收进度统计
- FR30: 用户可以导出Excel报表

**系统功能（4条）**
- FR31: 管理员可以管理用户账号
- FR32: 用户可以设置个人权限（功能级别）
- FR33: 系统使用微信消息进行推送通知
- FR34: 用户可以上传合同文件（可选）

### NonFunctional Requirements

**Performance（3条）**
- NFR1: 核心操作（费用录入、项目创建）响应时间少于2秒
- NFR2: 微信小程序启动时间越快越好
- NFR3: 核心操作步骤少于3步

**Security（3条）**
- NFR4: 所有财务数据需加密存储和传输
- NFR5: 根据用户角色控制数据访问权限
- NFR6: 财务数据必须准确无误，支持数据校验

**Integration（3条）**
- NFR7: 支持导入银行流水进行费用核销
- NFR8: 使用微信消息进行推送通知
- NFR9: 支持导出Excel报表

### Additional Requirements (Architecture)

**技术栈与框架**
- AR1: 使用uniapp 5.03+ (Vue 3 Composition API + TypeScript)作为前端框架
- AR2: 使用uni-cloud（支付宝云免费版）+ MongoDB作为后端和数据库
- AR3: 使用uni-id统一用户认证（小程序微信登录 + PC用户名密码登录）
- AR4: 使用uni-admin 2.5.13作为PC管理后台框架

**Starter Template初始化**
- AR5: 小程序主体使用官方uniapp starter（vite-ts模板）初始化
- AR6: PC Web管理后台在/admin目录下整合uni-admin组件
- AR7: 项目初始化作为第一个Story，确保小程序端、PC Web端、云函数部署、uni-id认证流程测试通过

**数据架构**
- AR8: 采用反范式化数据模型优化查询性能（冗余常用字段避免join）
- AR9: 首页摘要数据使用冗余表（dashboard_summary），每小时更新一次
- AR10: 所有数据校验采用前端轻校验+云端严格校验+事务保证策略

**认证与安全**
- AR11: 小程序使用微信授权登录（一键登录）
- AR12: PC Web使用用户名密码登录（MVP阶段，Phase 2支持微信扫码登录）
- AR13: Token有效期7天（Access Token），Refresh Token 30天
- AR14: 传输层使用HTTPS加密，存储层MVP不加密（Phase 2可添加字段级加密）

**API与通信**
- AR15: 云函数按功能模块分组（customer/expense/collection/report/system）
- AR16: 统一错误响应格式：{success, errorCode, message, statusCode}
- AR17: 使用版本号（乐观锁）防止重复提交，保证幂等性

**前端架构**
- AR18: 使用Pinia作为状态管理方案（userStore/expenseStore/projectStore/dashboardStore）
- AR19: 使用uni-ui为主，业务组件（C01-C10）自定义
- AR20: MVP阶段不分包（总包体积预计<2MB）

**导入流程与状态管理**
- AR21: 云端状态机管理导入会话（import_sessions集合）
- AR22: 导入流程：PENDING → PARSING → PARSED → REVIEWING → APPROVED → COMMITTING → SUCCESS
- AR23: 使用MongoDB事务（db.runTransaction）保证批量插入原子性

**操作日志与缓存**
- AR24: 粗粒度操作日志（记录操作类型+对象+时间，不记录详细属性变化）
- AR25: 首页摘要和项目列表数据缓存5-10分钟，提交操作无缓存

**一致性规则（10大冲突点）**
- AR26: 数据库表和列名使用小驼峰命名（users, projects, createdAt）
- AR27: 云函数使用<模块>-<操作>格式命名（customer-list, expense-create）
- AR28: 前端文件按功能模块分组（Feature-first，不是Type-first）
- AR29: 所有API响应统一格式：{success: true, data: {...}} 或 {success: false, errorCode, message}
- AR30: 错误处理：云函数抛异常，统一包装器捕获，前端统一展示
- AR31: 加载状态：Pinia全局loading + 本地操作态防重复提交
- AR32: 日期时间：后端统一返回时间戳（毫秒），前端本地格式化
- AR33: HTTP状态码：所有响应返回200，通过errorCode和statusCode字段区分
- AR34: Vue组件：全面使用<script setup> + Composition API
- AR35: 事件与状态命名：Pinia action和事件都用驼峰命名

### UX Design Requirements

**核心体验定义**
- UX-DR1: 实现银行流水一键核销体验（从收到通知到完成核销 ≤ 30秒，自动匹配准确率 ≥ 85%）
- UX-DR2: 首页财务摘要横幅展示当期资金状态（本期应收/本月已到账/待核销笔数）
- UX-DR3: 核销界面使用置信度分级列表（高置信绿/中置信白/低置信橙）

**设计系统基础**
- UX-DR4: 使用uni-ui作为主组件库，参照WeUI设计语言
- UX-DR5: 主色`#1677FF`，状态色（绿`#07C160`/橙`#FF976A`/红`#EE0A24`），中性色（主文字`#1A1A1A`/次要`#666666`）
- UX-DR6: 字体层级6级（主要金额64-96rpx/次要金额40rpx/标题H1 36rpx/标题H2 32rpx/正文28rpx/辅助24rpx）
- UX-DR7: 财务数字使用等宽字体，格式统一为`¥1,234.00`，列表右对齐
- UX-DR8: 间距系统基于4rpx基础单位（xs 8rpx/sm 16rpx/md 24rpx/lg 32rpx/xl 48rpx）

**导航与布局**
- UX-DR9: 底部TabBar固定4项（首页/项目/核销/我）
- UX-DR10: 底部快速录入操作条（QuickEntryBar）固定于TabBar上方，高度96rpx
- UX-DR11: 页面左右内边距32rpx，卡片间距16rpx，卡片圆角16rpx

**交互模式**
- UX-DR12: 二次确认使用底部Action Sheet，不用全屏弹窗
- UX-DR13: 成功反馈用轻量Toast（绿色✓ + 短文字），2秒自动消失
- UX-DR14: 加载状态>800ms显示Skeleton骨架屏，<800ms不显示任何加载态
- UX-DR15: 列表项左滑暴露快捷操作（归档/编辑），同时提供"更多"菜单
- UX-DR16: 长列表使用上拉加载更多分页，不用分页按钮

**表单与输入**
- UX-DR17: 金额输入框`type="digit"`唤起系统数字键盘，失焦后自动格式化
- UX-DR18: 项目/供应商支持2字模糊搜索，实时出结果
- UX-DR19: 费用类别使用智能推荐（历史常用类别前5个 + "更多"按钮）
- UX-DR20: 所有可点击元素最小触控区域 ≥ 88rpx × 88rpx

**自定义业务组件（10个）**
- UX-DR21: C01 FinanceSummaryBanner - 首页顶部蓝色横幅，三列数字（本期应收/本月已到账/待核销）
- UX-DR22: C02 ProjectCard - 项目卡片（项目名+阶段标签+进度条+核心三数字）
- UX-DR23: C03 QuickEntryBar - 底部快速录入操作条（固定96rpx高度）
- UX-DR24: C04 ReconciliationRow - 核销流水行（置信度分级+展开面板）
- UX-DR25: C05 CategoryQuickPicker - 费用类别快选（横向滚动Chips）
- UX-DR26: C06 WarningBanner - 首页预警条（橙色，动态出现）
- UX-DR27: C07 ConfidenceLevelBadge - 置信度标签（高置信绿/中置信灰/低置信橙）
- UX-DR28: C08 BatchConfirmSheet - 批量核销确认弹层（uni-popup包裹）
- UX-DR29: C09 AmountInput - 金额输入增强（自动格式化）
- UX-DR30: C10 OcrConfirmSheet - OCR确认表单（大字号展示识别结果）

**核心用户旅程**
- UX-DR31: Journey 1 - 银行流水导入与核销（导入Excel/CSV → 置信度匹配 → 分级列表 → 一键批量/逐笔确认）
- UX-DR32: Journey 2 - 快速费用录入（底部快速录入 → 选项目 → 输入金额 → 选类别 → 提交）
- UX-DR33: Journey 3 - 日常首页查看（蓝色横幅 → 预警条（条件显示）→ 项目卡片列表）
- UX-DR34: Journey 4 - 新项目创建与启动（填写基本信息 → 设置收款阶段 → 预览确认 → 创建）

**OCR与多模态输入**
- UX-DR35: 一期支持增值税发票OCR识别（拍照 → 结构化识别 → 大字号展示确认 → 提交）
- UX-DR36: 二期支持语音录入（识别结果高亮展示，用户主动确认后才提交）

**核销流程特殊规则**
- UX-DR37: 置信度分级：高置信（金额吻合+关键词命中，绿色底色，默认勾选）
- UX-DR38: 置信度分级：中置信（金额吻合无关键词，白色底色，不默认勾选）
- UX-DR39: 置信度分级：低置信（金额近似或无匹配，橙色底色，标注"需人工处理"）
- UX-DR40: "一键全部确认"触发汇总确认层："确认X笔高置信项（共¥XXX），Y笔需人工处理已自动排除"

**空状态设计**
- UX-DR41: 每个空列表页面提供：一句价值说明 + 一个行动按钮
- UX-DR42: 核销Tab空状态："本期所有流水已核销，很棒！" + 查看报表入口

**PC端管理后台**
- UX-DR43: 使用uni-admin作为PC端框架（内置uni-id/权限/日志/菜单）
- UX-DR44: 侧边导航菜单结构：首页/客户/供应商/项目/费用/收款/报表/系统设置
- UX-DR45: 三种核心页面模式：列表页（表格+固定表头+分页器）/表单页（居中布局最大800px）/报表页（时间筛选+汇总卡片+数据表格）
- UX-DR46: PC端交互差异：分页器（每页20条）/行尾文字按钮操作/独立表单页/标准Modal确认
- UX-DR47: MVP必须完成的PC页面：登录/客户列表/供应商列表/项目列表/归档项目/费用列表/收款列表/基础报表/用户账号管理/系统设置

**无障碍与响应式**
- UX-DR48: 色彩对比度合规（主文字18.1:1 AAA级，次要文字5.7:1 AA级）
- UX-DR49: 颜色不作为唯一信息载体（状态色配文字标签）
- UX-DR50: 一期不支持深色模式（`"darkmode": false`）

### FR Coverage Map

| FR | Epic | 描述 |
|-----|------|------|
| FR1-FR4 | Epic 2 | 客户管理CRUD与关联查看 |
| FR5-FR7 | Epic 2 | 供应商管理CRUD与支付记录 |
| FR8-FR12 | Epic 3 | 项目生命周期（创建、阶段切换、列表查看） |
| FR13-FR14 | Epic 6 | 费用预算与实际支出 |
| FR15 | Epic 7 | 核销（支出与预算关联） |
| FR16-FR17 | Epic 6 | 费用智能推荐与自动填充 |
| FR18-FR21 | Epic 7 | 银行流水导入、核销、二次确认、无计划付款 |
| FR22-FR26 | Epic 5 | 收款计划、临时收款、已收款记录、应收已收对比 |
| FR27-FR30 | Epic 8 | 报表统计（应收应付/实收实付）与Excel导出 |
| FR31-FR32 | Epic 9 | 用户账号管理与功能权限 |
| FR33 | Epic 7 | 微信消息推送（核销通知场景） |
| FR34 | Epic 3 | 合同文件上传（项目管理可选功能） |

## Epic List

### Epic 1: 项目初始化与基础架构

**用户成果**：开发团队可以在统一的技术栈上开始开发，小程序端、PC Web端、云函数部署、认证流程全部就绪，建立一致性标准。

**覆盖需求**：
- AR1-AR7（技术栈选型：uniapp + uni-cloud + uni-admin）
- AR11-AR14（认证体系：微信登录 + 用户名密码 + Token策略）
- AR26-AR35（10大一致性规则：命名、格式、错误处理、日期时间等）
- UX-DR4（uni-ui + WeUI设计语言）
- UX-DR43（uni-admin PC端框架）

**验收标准**：
- 小程序main分支能正常启动
- PC Web admin分支能正常启动
- uni-id认证流程测试通过（小程序微信登录 + PC用户名密码）
- 云函数部署测试成功
- 设计Token全局注入（色彩、字体、间距）

---

### Epic 2: 客户与供应商管理

**用户成果**：主理人可以创建、查看、编辑、删除客户和供应商信息，为后续项目和费用关联奠定基础。

**覆盖需求**：
- FR1-FR4（客户管理）
- FR5-FR7（供应商管理）
- UX-DR9（TabBar 4项导航结构）
- UX-DR44（PC端侧边导航菜单）
- UX-DR47（PC端客户/供应商列表页）

**验收标准**：
- 小程序端客户列表、新增、编辑、删除功能
- 小程序端供应商列表、新增、编辑、删除功能
- PC Web端客户管理页（列表+CRUD）
- PC Web端供应商管理页（列表+CRUD）
- 客户与供应商能关联到项目/费用记录

---

### Epic 3: 项目生命周期管理

**用户成果**：主理人可以创建项目、跟踪7个阶段的进度、查看项目列表、管理项目信息。

**覆盖需求**：
- FR8-FR12（项目创建、状态切换、阶段管理、列表查看、位置记录）
- FR34（合同文件上传 - 可选）
- UX-DR22（C02 ProjectCard组件）
- UX-DR33-DR34（Journey 3-4：首页查看、项目创建）
- UX-DR47（PC端项目列表/归档项目页）

**验收标准**：
- 项目创建流程（基本信息+收款阶段+预期完工日期）
- 项目阶段状态切换（7个阶段：立项→需求确认→施工→验收→结算→维保→归档）
- 项目卡片展示（项目名、客户、阶段进度、核心财务数字）
- PC Web端项目列表、新建、编辑、归档功能
- 项目详情页显示完整财务收支视图

---

### Epic 4: 首页财务摘要与全局视图

**用户成果**：主理人打开应用即可一眼掌握全局资金状态和项目概况，是核心体验。

**覆盖需求**：
- UX-DR1-DR3（核心体验：一键核销≤30秒、财务摘要、置信度分级）
- UX-DR21（C01 FinanceSummaryBanner组件）
- UX-DR26（C06 WarningBanner组件）
- UX-DR41-DR42（空状态设计）
- AR9（首页摘要冗余表 dashboard_summary）
- NFR1（响应时间 < 2秒）

**验收标准**：
- 首页顶部蓝色横幅显示当期资金状态（本期应收/本月已到账/待核销笔数）
- 首页项目卡片列表，包含阶段进度条
- 预警条动态显示（有预警项时显示，无时隐藏）
- 预警项点击跳转到对应项目详情
- 首页加载时间 < 2秒

---

### Epic 5: 收款计划与记录

**用户成果**：主理人可以设置阶段性收款计划、记录已收款、查看应收/已收对比。

**覆盖需求**：
- FR22-FR26（收款计划、临时收款、已收款记录、应收已收对比）
- UX-DR47（PC端收款列表）

**验收标准**：
- 项目创建时设置收款阶段（定金/中期款/尾款）
- 支持创建临时性收款需求
- 记录已收款项
- 查看应收应付比例
- PC Web端收款列表、新增、编辑功能

---

### Epic 6: 快速费用录入与智能预填

**用户成果**：主理人可以在3步内完成费用录入，系统智能推荐类别和自动填充数据，支持OCR发票识别。

**覆盖需求**：
- FR13-FR14（费用预算、实际支出）
- FR16-FR17（费用智能推荐、自动填充）
- UX-DR10（C03 QuickEntryBar底部快速录入操作条）
- UX-DR17-DR19（金额输入、模糊搜索、类别快选）
- UX-DR23（C03 QuickEntryBar）
- UX-DR25（C05 CategoryQuickPicker）
- UX-DR29（C09 AmountInput增强）
- UX-DR32（Journey 2 快速费用录入）
- UX-DR35（OCR发票识别）
- NFR3（核心操作 < 3步）

**验收标准**：
- 底部固定快速录入操作条（96rpx高度）
- 费用录入流程：选项目 → 输入金额 → 选类别 → 提交（3步）
- 金额输入框自动格式化（¥1,234.00）
- 项目/供应商2字模糊搜索
- 费用类别智能推荐（历史常用前5个）
- 支持拍照OCR识别增值税发票（一期）
- PC Web端费用列表、新增、编辑功能

---

### Epic 7: 银行流水核销与置信度匹配

**用户成果**：主理人可以导入银行流水，系统自动匹配预算/收款，按置信度分级展示，支持一键批量核销，这是产品的核心差异化功能。

**覆盖需求**：
- FR15（核销）
- FR18-FR21（流水导入、核销、二次确认、无计划付款）
- UX-DR1（核心体验：≤30秒完成，准确率≥85%）
- UX-DR3（置信度分级列表）
- UX-DR24（C04 ReconciliationRow组件）
- UX-DR27（C07 ConfidenceLevelBadge）
- UX-DR28（C08 BatchConfirmSheet）
- UX-DR30（C10 OcrConfirmSheet）
- UX-DR31（Journey 1 银行流水导入与核销）
- UX-DR37-DR40（置信度分级规则与一键确认）
- AR21-AR23（导入状态机、事务保证）
- NFR7（银行流水导入）

**验收标准**：
- 核销Tab导入银行流水（Excel/CSV路径主推）
- 系统执行置信度匹配算法
- 展示置信度分级列表（高置信绿/中置信白/低置信橙）
- 高置信项默认勾选，一键全部确认
- 低置信项排列到底部，标注"需人工处理"
- 支持逐笔确认和批量确认
- 核销完成后流水从列表消失
- PC Web端核销记录查看
- 无计划付款自动生成预算/应付记录

---

### Epic 8: 报表统计与Excel导出

**用户成果**：主理人可以查看应收应付/实收实付比例报表，导出Excel供财务人员使用。

**覆盖需求**：
- FR27-FR30（应收应付/实收实付报表、逾期统计、Excel导出）
- UX-DR42（空状态：查看报表入口）
- UX-DR45（PC端报表页：时间筛选+汇总卡片+数据表格）
- UX-DR47（PC端基础报表）
- NFR9（Excel导出）

**验收标准**：
- PC Web端基础报表页（可按时间筛选：本月/本季/本年/自定义）
- 汇总卡片显示应收/已收/应付/已付四个数字
- 数据表格展示完整明细（支持列排序）
- Excel导出功能（包含汇总 + 明细表）
- 移动端查看报表入口

---

### Epic 9: 用户权限与系统设置

**用户成果**：管理员可以管理用户账号、设置功能权限、配置费用类别和应收阶段定义。

**覆盖需求**：
- FR31-FR32（用户账号管理、功能权限）
- UX-DR47（PC端用户账号管理/系统设置）
- AR24（粗粒度操作日志）

**验收标准**：
- PC Web端用户账号管理（创建/编辑/禁用用户）
- PC Web端系统设置页（费用类别配置、应收阶段定义）
- 操作日志记录（操作类型+对象+时间）
- MVP阶段所有用户默认全权限

---

### Epic 10: 设计系统与一致性保障

**用户成果**：开发团队拥有完整的设计系统规范（颜色、字体、间距、组件、交互模式），确保小程序和PC Web全平台视觉和交互一致性。

**覆盖需求**：
- UX-DR5-DR8（颜色系统、字体层级、间距系统）
- UX-DR11-DR16（布局规范、交互模式：Action Sheet、Toast、Skeleton）
- UX-DR20（触控目标最小尺寸 ≥ 88rpx）
- UX-DR41-DR42（空状态设计）
- UX-DR46（PC端交互差异）
- UX-DR48-DR50（无障碍与响应式）
- AR18-AR20（Pinia状态管理、uni-ui、包体积优化）

**验收标准**：
- 设计Token文件完成（色彩、字体、间距、圆角、阴影）
- 10个自定义业务组件完成（C01-C10）
- 所有组件使用设计Token变量，禁止硬编码颜色
- uni-ui组件库集成
- 页面背景、卡片、按钮等基础样式一致
- 财务数字格式统一（¥1,234.00，等宽字体，右对齐）
- 色彩对比度合规（主文字 AA级+，次要文字 AA级）
- 颜色不作为唯一信息载体（状态色配文字标签）

---

## Epic 1 Stories: 项目初始化与基础架构

### Story 1.1: 初始化小程序项目结构与 Starter Template

As a **开发者**,
I want **使用官方uniapp starter模板初始化项目**,
So that **项目结构规范，能正常启动小程序**.

**Acceptance Criteria:**

**Given** 项目仓库为空
**When** 执行 `npm create vite@latest . -- --template uni-app-ts-vite` 初始化项目
**Then**
- 小程序项目结构完成（pages/components/stores/styles 等目录存在）
- 能执行 `npm run dev` 成功启动小程序预览
- Vue 3 + TypeScript 框架就绪且能正确识别
- 基础页面能在微信开发者工具中正常渲染

**And**
- 项目名称在 package.json 中设置为 "project-manager"
- 删除示例代码，保留基础框架结构
- 执行 `npm run build` 能成功打包（输出到 dist 目录）
- 生成的包体积 < 1MB（初始化后）

---

### Story 1.2: 配置设计 Token 与全局样式

As a **前端开发者**,
I want **定义和注入全局设计 Token（颜色、字体、间距）**,
So that **所有页面和组件能使用一致的设计系统**.

**Acceptance Criteria:**

**Given** 小程序项目已初始化
**When** 在 `src/uni.scss` 中定义设计 Token 变量
**Then**
- 主色 `$color-primary: #1677FF` 定义完成
- 状态色完整定义：成功 `#07C160`、预警 `#FF976A`、危险 `#EE0A24`
- 中性色完整定义：主文字 `#1A1A1A`、次要 `#666666`、边框 `#EBEBEB` 等
- 字体层级 Token 定义完成（6 级：主要金额 64-96rpx、次要 40rpx 等）
- 间距系统 Token 定义完成（xs 8rpx、sm 16rpx、md 24rpx、lg 32rpx、xl 48rpx）

**And**
- 所有 Token 通过 `@import` 在项目全局注入（pages.json 或 main.js）
- 测试页面能通过 `$color-primary` 访问到 Token 值
- 颜色对比度检测：主文字对比度 ≥ 4.5:1（AA 级）、次要文字 ≥ 4.5:1

---

### Story 1.3: 集成 uni-ui 组件库

As a **前端开发者**,
I want **安装和配置 uni-ui 官方组件库**,
So that **能使用官方组件快速构建界面**.

**Acceptance Criteria:**

**Given** 小程序项目已初始化且设计 Token 已配置
**When** 执行 `npm install uni-ui` 并配置 easycom 自动导入
**Then**
- uni-ui 安装完成（package.json 中可见）
- pages.json 中配置 easycom 规则：`"uni-*": "uni_modules/uni-*/*"`
- 测试页面能使用基础组件：`uni-button`、`uni-popup`、`uni-swipe-action`、`uni-forms` 等
- 组件样式能正确在小程序中渲染且颜色符合设计 Token

**And**
- 验证关键组件列表能正常加载（popup、swipe-action、forms、load-more、icons、tag、steps）
- 组件能正确应用全局 Token 颜色

---

### Story 1.4: 配置 uniCloud 云环境与数据库连接

As a **后端开发者**,
I want **初始化 uniCloud 支付宝云环境和 MongoDB 连接**,
So that **云函数部署和数据库查询能正常工作**.

**Acceptance Criteria:**

**Given** 项目根目录已创建
**When** 初始化 uniCloud 云环境并配置 MongoDB
**Then**
- 创建 `/uniCloud` 目录结构完成
- 支付宝云环境配置完成（database、functions、common 目录）
- MongoDB 数据库连接测试成功
- 云存储配置完成

**And**
- 能执行 `unicloud login` 登录云环境
- 能创建和部署测试云函数（如 hello-world）
- 数据库能执行基础查询：创建测试集合、插入文档、查询文档成功
- MongoDB 版本 ≥ 4.0

---

### Story 1.5: 配置 uni-id 认证系统（小程序微信登录）

As a **后端开发者**,
I want **配置 uni-id 并实现小程序微信登录流程**,
So that **用户能通过微信一键登录小程序**.

**Acceptance Criteria:**

**Given** uniCloud 环境已初始化
**When** 配置 uni-id 和微信登录参数
**Then**
- uni-id 初始化完成（系统表 uni-id-users、uni-id-device 等创建）
- 小程序 AppID 和 AppSecret 配置完成（在 uniCloud 环境中）
- 登录云函数部署成功（uni-id 提供的登录接口）
- 测试用户能通过微信授权登录并获得 Token

**And**
- Access Token 有效期设置为 7 天
- Refresh Token 有效期设置为 30 天
- 登录状态能正确保存到 uni.getStorageSync('uni_id_token')
- 登录后能获取到用户基本信息（openid、nickname 等）

---

### Story 1.6: 初始化 PC Web 管理后台（uni-admin）

As a **前端开发者**,
I want **在 /admin 目录下集成 uni-admin 框架**,
So that **PC Web 管理后台能正常启动**.

**Acceptance Criteria:**

**Given** 项目根目录已创建
**When** 初始化 uni-admin 框架到 /admin 目录
**Then**
- /admin 目录创建完成且包含 uni-admin 完整结构
- uni-admin 依赖安装完成（package.json 中可见）
- 执行 `npm run dev` 从 /admin 目录能启动 PC Web 管理后台
- 登录页能正常显示（用户名 + 密码输入框）

**And**
- 后台首页能加载（显示基础菜单）
- 主色已覆盖为 `#1677FF`（全局主题配置）
- Logo 和系统名称"project-manager"设置完成

---

### Story 1.7: 配置 uni-id 认证系统（PC 用户名密码登录）

As a **后端开发者**,
I want **配置 uni-id 并实现 PC Web 用户名密码登录**,
So that **PC 管理员能通过用户名密码登录管理后台**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化且 uni-id 已配置
**When** 配置用户名密码认证并创建测试用户
**Then**
- PC Web 登录页能显示用户名、密码、记住我等输入框
- 创建测试用户（如 admin/password123）
- 能通过用户名密码成功登录
- 登录后跳转到后台首页

**And**
- Access Token 和 Refresh Token 有效期配置一致（7 天/30 天）
- 登录状态能保存到 localStorage（uni_id_token）
- 刷新页面后登录状态仍然保持
- 密码错误时显示提示信息

---

### Story 1.8: 建立一致性规则文档

As a **技术主管**,
I want **文档化项目的 10 大一致性规则**,
So that **整个团队能遵循统一的代码和设计标准**.

**Acceptance Criteria:**

**Given** 项目技术栈已确定
**When** 创建一致性规则文档 `/docs/consistency-rules.md`
**Then**
- 文档清晰记录 10 大一致性规则
- 数据库表和列名规则明确：使用小驼峰（如 users、createdAt）
- 云函数命名规则明确：`<模块>-<操作>` 格式（如 customer-list、expense-create）
- 前端文件组织规则明确：Feature-first 模式（按功能模块组织，非按类型）
- API 响应格式规范定义完成：`{success, data}` 或 `{success, errorCode, message, statusCode}`
- 日期时间处理规则定义完成：后端统一返回时间戳（毫秒），前端本地格式化

**And**
- 规则文档在项目 /docs 目录下可访问
- 包含代码示例和反面例子
- 所有开发者都已收到文档链接

### Story 1.9: 全局数据模型设计与 API 契约文档

As a **架构师 & 技术主管**,
I want **创建全局数据模型设计文档与 API 契约规范**,
So that **所有并行开发的 Epic 遵循一致的数据结构和接口标准，避免重复工作和数据冲突**.

**Acceptance Criteria:**

**Given** 所有需求已明确，Epics 1-8 的设计完成

**When** 创建 `/docs/global-data-model.md` 和 `/docs/api-contracts.md` 文件

**Then**
- 全局数据模型文档完成，包含：
  - 所有数据表结构定义（表名、字段、数据类型、索引、约束）
  - 表间关系说明（一对多、多对多、外键引用）
  - 数据验证规则（字段长度、格式、必填项）
  - 枚举类型定义（如项目状态、费用类别、收款阶段等）
  - MongoDB 集合结构（如有混合使用）

**And**
- API 契约文档完成，包含：
  - 所有云函数接口签名（入参、返回值、错误码）
  - 云函数命名约定校验：`<模块>-<操作>` 格式
  - 公共错误码定义（如 ERR_VALIDATION、ERR_NOT_FOUND、ERR_UNAUTHORIZED 等）
  - 请求/响应数据结构规范
  - 关键业务约束（如关联关系、值域范围）

**And**
- 前期飞行检查清单创建，验证：
  - 字段命名：无重复、无冲突（如客户表和供应商表不重复定义相同逻辑的字段）
  - 外键关系：明确所有表之间的依赖关系
  - 业务流程约束：确认费用、收款等业务对象的关键依赖链
  - 云函数职责：确认每个操作由唯一云函数处理，避免分散实现
  - 并行开发依赖：标记哪些 Epics 可以独立开发、哪些需要等待前置

**And**
- 文档包含示例：
  - 客户-项目-费用的完整数据流示例
  - 收款-费用-预算核销的数据关联示例
  - 供应商-支付的数据关联示例

**And**
- 所有团队成员已审视文档，无遗留问题

---

### Story 1.10: 全局数据模型与 API 契约评审与批准

As a **产品经理 & 技术主管**,
I want **对全局数据模型与 API 契约进行正式评审与批准**,
So that **确保 Epics 2-9 的并行开发基于完全一致的基础，消除后续集成风险**.

**Acceptance Criteria:**

**Given** Story 1.9 已完成，全局数据模型和 API 契约文档已准备就绪

**When** 召集技术主管、架构师、前后端代表进行评审会议

**Then**
- 评审会议完成，参加者：
  - 架构师（确认技术架构一致）
  - 后端技术主管（确认数据库设计和云函数设计）
  - 前端技术主管（确认 API 契约可用性）
  - 产品经理（确认业务逻辑正确）

**And**
- 评审中确认以下关键点：
  - ✅ 所有 30 个 FR 都映射到具体的数据表或接口
  - ✅ 表间关系清晰，无循环依赖
  - ✅ 云函数职责分明，模块化清晰
  - ✅ 并行开发依赖关系正确（Epic 2/3/5/6/7/8/9 可独立开发；Epic 4 依赖 2/3；Epic 10 依赖所有）
  - ✅ 错误处理和边界情况完整

**And**
- 评审记录文档创建：`/docs/design-review-record.md`
  - 评审时间、参加者名单
  - 提出的问题和解决方案
  - 最终批准签名（技术主管确认）

**And**
- 批准后的决定：
  - 文档标记为 "APPROVED - v1.0"
  - 所有 Epics 2-9 可立即开始开发
  - 开发者必须严格遵循文档中的数据结构和 API 契约
  - 若需修改，必须通过 CR（Change Request）流程

**And**
- 通知所有开发者：
  - 发送批准通知邮件/群消息
  - 确认每个 Epic 的负责开发者已收到并理解文档

---

## Epic 2 Stories: 客户与供应商管理

### Story 2.1: 创建客户数据表与云函数基础

As a **后端开发者**,
I want **创建客户 (customers) 数据表及基础 CRUD 云函数**,
So that **系统能存储和检索客户信息**.

**Acceptance Criteria:**

**Given** uniCloud 环境已初始化
**When** 创建 customers 集合并编写云函数
**Then**
- MongoDB 集合 `customers` 创建完成，包含字段：_id、name、phone、email、address、createdAt、updatedAt、createdBy
- 云函数 `customer-list` 部署成功（支持分页和搜索）
- 云函数 `customer-create` 部署成功（验证必填字段）
- 云函数 `customer-update` 部署成功（更新指定客户）
- 云函数 `customer-delete` 部署成功（逻辑删除或物理删除）

**And**
- 所有云函数返回统一格式：`{success: true, data: {...}}` 或 `{success: false, errorCode: "ERR_CODE", message: "..."}`
- 数据校验在云端进行（不依赖前端）
- 操作日志记录创建者和创建时间

---

### Story 2.2: 小程序端客户列表页与基础 CRUD

As a **移动端开发者**,
I want **在小程序实现客户列表页面，支持查看、新增、编辑、删除**,
So that **主理人能管理所有客户**.

**Acceptance Criteria:**

**Given** 客户云函数已部署
**When** 在小程序创建 pages/customer/list 页面
**Then**
- 页面加载时调用 customer-list 云函数获取数据
- 显示客户列表（客户名、电话、邮箱）
- 支持上拉加载更多分页
- 页面顶部有搜索框（2 字模糊搜索触发）
- 右上角有"新增客户"按钮

**And**
- 列表项左滑显示"编辑"和"删除"快捷操作
- 点击客户进入详情页（可查看完整信息）
- 数据加载时显示骨架屏（> 800ms 触发）
- 搜索结果为空时显示空状态："未找到匹配的客户"

---

### Story 2.3: 小程序端新增/编辑客户页面

As a **移动端开发者**,
I want **创建新增和编辑客户的表单页面**,
So that **主理人能快速录入客户信息**.

**Acceptance Criteria:**

**Given** 客户列表页已实现
**When** 用户点击"新增"或"编辑"进入表单页
**Then**
- 表单页面显示字段：客户名（必填）、电话（必填）、邮箱（可选）、地址（可选）
- 提交按钮为蓝色主操作按钮，取消按钮为次级
- 表单校验：客户名和电话为必填，电话格式校验
- 提交成功后显示绿色 Toast（2 秒消失）并返回列表页

**And**
- 编辑时预填现有数据
- 未修改时提交按钮禁用
- 删除操作前显示底部 Action Sheet 二次确认

---

### Story 2.4: PC Web 端客户管理页面

As a **PC 开发者**,
I want **在 uni-admin 创建客户管理页面**,
So that **管理员能在 Web 端管理客户**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建客户管理页面
**Then**
- 页面显示客户列表表格（客户名、电话、邮箱、创建时间、操作）
- 表头支持排序（点击列名切换升序/降序）
- 表格固定表头（position: sticky）
- 每页 20 条数据，底部分页器
- 顶部有搜索框、"新建客户"按钮

**And**
- 行操作：查看/编辑/删除（文字按钮，非图标）
- 删除时显示 Modal 二次确认
- 新建/编辑弹出独立页面或表单弹窗
- 列表为空时显示空状态

---

### Story 2.5: 创建供应商数据表与云函数基础

As a **后端开发者**,
I want **创建供应商 (suppliers) 数据表及基础 CRUD 云函数**,
So that **系统能存储和检索供应商信息**.

**Acceptance Criteria:**

**Given** uniCloud 环境已初始化
**When** 创建 suppliers 集合并编写云函数
**Then**
- MongoDB 集合 `suppliers` 创建完成，包含字段：_id、name、phone、email、address、shopUrl、bankInfo、createdAt、updatedAt、createdBy
- 云函数 `supplier-list` 部署成功（支持分页和搜索）
- 云函数 `supplier-create` 部署成功（验证必填字段）
- 云函数 `supplier-update` 部署成功
- 云函数 `supplier-delete` 部署成功

**And**
- 返回统一响应格式
- 所有供应商操作都记录到操作日志
- shopUrl 和 bankInfo 字段为可选

---

### Story 2.6: 小程序端供应商列表与 CRUD

As a **移动端开发者**,
I want **在小程序实现供应商列表页面，支持查看、新增、编辑、删除**,
So that **主理人能管理所有供应商**.

**Acceptance Criteria:**

**Given** 供应商云函数已部署
**When** 在小程序创建 pages/supplier/list 页面
**Then**
- 页面加载时调用 supplier-list 云函数获取数据
- 显示供应商列表（供应商名、电话、邮箱）
- 支持上拉加载更多分页
- 页面顶部有搜索框
- 右上角有"新增供应商"按钮

**And**
- 列表项左滑显示"编辑"和"删除"快捷操作
- 点击供应商进入详情页（显示店铺链接、银行信息等）
- 空列表显示空状态

---

### Story 2.7: 小程序端新增/编辑供应商页面

As a **移动端开发者**,
I want **创建新增和编辑供应商的表单页面**,
So that **主理人能快速录入供应商信息**.

**Acceptance Criteria:**

**Given** 供应商列表页已实现
**When** 用户点击"新增"或"编辑"进入表单页
**Then**
- 表单字段：供应商名（必填）、电话（必填）、邮箱（可选）、地址（可选）、网店链接（可选）、银行信息（可选）
- 提交成功后显示 Toast 并返回列表页
- 编辑时预填现有数据

**And**
- 必填字段校验
- 删除前二次确认

---

### Story 2.8: PC Web 端供应商管理页面

As a **PC 开发者**,
I want **在 uni-admin 创建供应商管理页面**,
So that **管理员能在 Web 端管理供应商**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建供应商管理页面
**Then**
- 页面显示供应商列表表格（供应商名、电话、邮箱、创建时间、操作）
- 表头支持排序
- 表格固定表头
- 每页 20 条数据，底部分页器
- 行操作：查看/编辑/删除

**And**
- 新建/编辑弹出表单页面
- 删除时 Modal 确认
- 可查看供应商的支付记录汇总

---

## Epic 3 Stories: 项目生命周期管理

### Story 3.1: 创建项目数据表与云函数基础

As a **后端开发者**,
I want **创建项目 (projects) 数据表及基础 CRUD 云函数**,
So that **系统能存储和管理项目信息**.

**Acceptance Criteria:**

**Given** customers 和 suppliers 表已创建
**When** 创建 projects 集合并编写云函数
**Then**
- MongoDB 集合 `projects` 创建完成，包含字段：_id、projectName、customerId、customerName、address、stage、contractAmount、completionDate、createdAt、updatedAt、createdBy
- projects 表与 customers 表通过 customerId 关联
- stage 字段支持 7 个值：立项、需求确认、施工、验收、结算、维保、归档
- 云函数 `project-list` 部署成功（支持分页、按客户筛选、按阶段筛选）
- 云函数 `project-create` 部署成功（验证必填字段、初始化阶段）
- 云函数 `project-update` 和 `project-delete` 部署成功

**And**
- 返回统一响应格式
- 创建项目时自动设置初始阶段为"立项"
- 所有操作记录到操作日志

---

### Story 3.2: 小程序端项目列表页

As a **移动端开发者**,
I want **在小程序实现项目列表页面，显示所有项目的进度和核心财务数字**,
So that **主理人能快速查看项目状态**.

**Acceptance Criteria:**

**Given** 项目云函数已部署
**When** 创建 pages/project/list 页面
**Then**
- 页面加载时调用 project-list 云函数获取数据
- 每个项目显示卡片（项目名、客户名、阶段进度条、合同额、已收款、余额）
- 支持上拉加载更多分页
- 右上角有"新增项目"按钮

**And**
- 进度条显示当前阶段进度（如 3/7 表示在第 3 个阶段）
- 财务数字右对齐，格式为 ¥1,234.00
- 数据加载时显示骨架屏

---

### Story 3.3: 小程序端新增项目页面

As a **移动端开发者**,
I want **创建新增项目的多步表单页面**,
So that **主理人能快速创建新项目**.

**Acceptance Criteria:**

**Given** 项目列表页已实现
**When** 用户点击"新增项目"进入表单页
**Then**
- 第 1 步：填写基本信息（项目名、选择客户、地址、合同金额、完工日期）
- 第 2 步：设置收款阶段（定金/中期款/尾款，输入金额或百分比）
- 第 3 步：预览并确认
- 所有字段校验通过后提交成功

**And**
- 项目创建时自动初始化阶段为"立项"
- 创建后显示 Toast 并返回列表页
- 取消时无确认对话（未修改）

---

### Story 3.4: 小程序端项目详情页与阶段切换

As a **移动端开发者**,
I want **创建项目详情页，显示完整信息和财务收支视图**,
So that **主理人能查看项目全貌和切换阶段**.

**Acceptance Criteria:**

**Given** 项目列表页已实现
**When** 用户点击项目卡片进入详情页
**Then**
- 页面顶部显示项目名、客户名、地址
- 显示阶段进度条（可点击切换阶段）
- 显示核心财务数字：合同额、应收、已收、应付、已付
- 显示收款计划和实际收款对比
- 显示费用预算和实际支出对比

**And**
- 阶段切换时显示确认对话
- 点击各个收支数字可跳转到对应列表
- 底部有"快速录入"按钮（调起快速录入表单）

---

### Story 3.5: 小程序端编辑项目页面

As a **移动端开发者**,
I want **创建编辑项目信息的页面**,
So that **主理人能修改项目基本信息**.

**Acceptance Criteria:**

**Given** 项目详情页已实现
**When** 用户点击"编辑"或项目信息区域
**Then**
- 预填项目当前信息
- 允许修改：项目名、地址、合同金额、完工日期
- 不允许修改：客户、阶段（阶段只能通过进度条切换）
- 修改后提交成功返回详情页

**And**
- 无修改时提交按钮禁用
- 修改后显示 Toast 确认

---

### Story 3.6: 小程序端项目左滑快捷操作

As a **移动端开发者**,
I want **实现项目卡片左滑显示快捷操作**,
So that **主理人能快速归档或编辑项目**.

**Acceptance Criteria:**

**Given** 项目列表页已实现
**When** 用户左滑项目卡片
**Then**
- 卡片左滑显示"编辑"和"归档"按钮
- 点击"编辑"进入编辑页面
- 点击"归档"显示二次确认

**And**
- 同时提供"更多"菜单作为非唯一路径
- 已归档项目在列表中显示灰色样式

---

### Story 3.7: PC Web 端项目管理页面

As a **PC 开发者**,
I want **在 uni-admin 创建项目管理页面**,
So that **管理员能在 Web 端管理项目**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建项目管理页面
**Then**
- 页面显示项目列表表格（项目名、客户名、阶段、合同额、创建时间、操作）
- 支持按客户筛选、按阶段筛选
- 表头支持排序
- 每页 20 条数据，底部分页器
- 行操作：查看/编辑/归档

**And**
- 新建/编辑显示表单页面
- 归档前 Modal 确认
- 归档项目列表单独显示

---

### Story 3.8: PC Web 端项目详情页与归档项目列表

As a **PC 开发者**,
I want **创建项目详情页和归档项目列表**,
So that **管理员能查看项目全貌和管理已归档项目**.

**Acceptance Criteria:**

**Given** 项目管理页面已实现
**When** 用户点击项目进入详情页
**Then**
- 显示项目完整信息和财务收支摘要
- 显示费用和收款明细表格
- 支持导出该项目的财务报表

**And**
- 创建单独的"归档项目"菜单项
- 已归档项目支持"恢复"操作
- 归档后仍能查看历史数据

---

## Epic 4 Stories: 首页财务摘要与全局视图

### Story 4.1: 创建首页摘要冗余表与数据聚合函数

As a **后端开发者**,
I want **创建 dashboard_summary 冗余表，存储实时首页摘要数据**,
So that **首页加载时能快速获取当期资金状态**.

**Acceptance Criteria:**

**Given** projects、collections、expenses 表已创建
**When** 创建 dashboard_summary 集合并编写聚合函数
**Then**
- MongoDB 集合 `dashboard_summary` 创建完成，包含字段：_id、period（当前月份）、totalReceivable（本期应收）、monthlyReceived（本月已到账）、pendingReconcile（待核销笔数）、updatedAt
- 云函数 `dashboard-get-summary` 部署成功（查询当月摘要）
- 聚合函数 `dashboard-update-summary` 部署成功（每小时更新一次）
- 数据准确性：应收 = 合同额，已到账 = 实际收款，待核销 = 未核销流水笔数

**And**
- 响应时间 < 500ms
- 支持多月份查询（如按时间范围查询）

---

### Story 4.2: 设计并实现 C01 FinanceSummaryBanner 组件

As a **前端开发者**,
I want **设计实现 FinanceSummaryBanner 组件，展示首页财务摘要**,
So that **主理人打开首页即可一眼掌握资金状态**.

**Acceptance Criteria:**

**Given** dashboard_summary 数据已可用
**When** 创建 components/FinanceSummaryBanner.vue 组件
**Then**
- 组件显示蓝色 (#1677FF) 横幅背景
- 展示三列数字：本期应收/本月已到账/待核销笔数
- 主数字字体大小 64-96rpx，等宽字体，格式为 ¥1,234.00
- 副标签字体大小 24rpx，颜色为 #FFFFFF
- 点击"待核销笔数"跳转到核销 Tab

**And**
- 加载状态显示骨架屏（> 800ms 触发）
- 网络失败时显示 `-` 占位符
- 响应式布局适配所有手机屏幕

---

### Story 4.3: 设计并实现 C06 WarningBanner 组件

As a **前端开发者**,
I want **设计实现 WarningBanner 组件，展示预警项目**,
So that **主理人能快速关注有问题的项目**.

**Acceptance Criteria:**

**Given** 项目数据可用
**When** 创建 components/WarningBanner.vue 组件
**Then**
- 组件显示橙色 (#FF976A) 背景条
- 显示预警项目数和简要说明（如"3 个项目临近截止"）
- 仅在有预警项时显示，无预警时完全隐藏（不占位）
- 点击"查看"跳转到过滤后的项目列表（仅显示预警项）

**And**
- 预警项判断逻辑：完工日期距离今天 ≤ 7 天，或已超期
- 颜色不作为唯一信息载体（配有文字说明）

---

### Story 4.4: 实现小程序首页布局与数据绑定

As a **移动端开发者**,
I want **创建首页 (pages/index/index) 完整页面**,
So that **主理人打开应用即可看到资金状态和项目列表**.

**Acceptance Criteria:**

**Given** FinanceSummaryBanner 和 ProjectCard 组件已实现
**When** 创建首页页面并绑定数据
**Then**
- 页面顶部显示 FinanceSummaryBanner（当期资金状态）
- WarningBanner 动态显示（有预警项时显示）
- 下方显示项目卡片列表（项目名、阶段进度、核心财务数字）
- 支持上拉加载更多分页

**And**
- 首页加载时间 < 2 秒
- 点击项目卡片进入项目详情页
- 下拉刷新重新获取数据

---

### Story 4.5: 设计并实现 C02 ProjectCard 组件

As a **前端开发者**,
I want **设计实现 ProjectCard 组件，展示项目卡片**,
So that **主理人能快速浏览项目概况**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 创建 components/ProjectCard.vue 组件
**Then**
- 卡片显示项目名、客户名（H2 标题）
- 显示阶段标签（当前阶段，如"施工"）
- 显示阶段进度条（如 3/7）
- 显示核心三数字：合同额/已收款/余额（右对齐，格式 ¥1,234.00）
- 预警项目显示橙色左边框，超期项目显示红色左边框

**And**
- 卡片间距 16rpx，圆角 16rpx，阴影 0 2rpx 12rpx rgba(0,0,0,0.08)
- 已归档项目显示灰色样式
- 响应式布局适配各种屏幕尺寸

---

## Epic 5 Stories: 收款计划与记录

### Story 5.1: 创建收款数据表与云函数基础

As a **后端开发者**,
I want **创建收款 (collections) 数据表及基础 CRUD 云函数**,
So that **系统能记录和管理收款信息**.

**Acceptance Criteria:**

**Given** projects 表已创建
**When** 创建 collections 集合并编写云函数
**Then**
- MongoDB 集合 `collections` 创建完成，包含字段：_id、projectId、projectName、collectionAmount、collectionDate、stage、notes、status、createdAt、updatedAt、createdBy
- collections 表与 projects 表通过 projectId 关联
- status 字段支持：待收/已收/逾期
- 云函数 `collection-list`、`collection-create`、`collection-update`、`collection-delete` 部署成功
- 云函数 `collection-by-project` 部署成功（按项目查询收款计划）

**And**
- 返回统一响应格式
- 创建收款时可关联项目阶段（可选）
- 所有操作记录到操作日志

---

### Story 5.2: 小程序端收款计划与记录页面

As a **移动端开发者**,
I want **在项目详情页创建收款计划和记录管理**,
So that **主理人能查看和管理项目的收款**.

**Acceptance Criteria:**

**Given** 项目详情页已实现
**When** 在项目详情页显示收款部分
**Then**
- 显示项目的收款计划列表（计划金额、预期日期、实际日期、状态）
- 显示收款摘要：应收/已收/待收
- 支持新增收款记录
- 支持编辑收款记录
- 支持删除收款记录

**And**
- 新增收款时选择项目、填写金额、选择阶段（可选）、填写日期
- 列表项按日期排序
- 逾期项目显示红色标签

---

### Story 5.3: PC Web 端收款列表页面

As a **PC 开发者**,
I want **在 uni-admin 创建收款列表页面**,
So that **管理员能在 Web 端查看和管理收款记录**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建收款列表页面
**Then**
- 页面显示收款列表表格（项目名、收款金额、预期日期、实际日期、状态、操作）
- 支持按项目筛选、按状态筛选
- 表头支持排序
- 每页 20 条数据，底部分页器
- 行操作：查看/编辑/删除

**And**
- 新建/编辑显示表单页面
- 支持批量标记为"已收"
- 逾期项显示红色标记

---

## Epic 6 Stories: 快速费用录入与智能预填

### Story 6.1: 创建费用数据表与云函数基础

As a **后端开发者**,
I want **创建费用 (expenses) 数据表及基础 CRUD 云函数**,
So that **系统能记录和管理费用信息**.

**Acceptance Criteria:**

**Given** projects、suppliers 表已创建
**When** 创建 expenses 集合并编写云函数
**Then**
- MongoDB 集合 `expenses` 创建完成，包含字段：_id、projectId、supplierId、expenseAmount、expenseDate、category、notes、status、createdAt、updatedAt、createdBy
- expenses 表与 projects、suppliers 表分别关联
- status 字段支持：记录/待核销/已核销
- 云函数 `expense-list`、`expense-create`、`expense-update`、`expense-delete` 部署成功
- 云函数 `expense-by-project` 部署成功（按项目查询费用）
- 云函数 `expense-by-category` 部署成功（统计费用类别）

**And**
- 返回统一响应格式
- 记录费用来源（手动输入、OCR、导入）
- 所有操作记录到操作日志

---

### Story 6.2: 实现费用类别智能推荐算法

As a **后端开发者**,
I want **实现费用类别智能推荐算法**,
So that **系统能根据历史数据推荐常用类别**.

**Acceptance Criteria:**

**Given** expenses 表已创建
**When** 创建 `expense-recommend-category` 云函数
**Then**
- 根据同项目的历史最近 3 笔费用统计类别
- 如无历史记录，返回系统预设类别前 5 个
- 返回格式：[{category: "材料", count: 5}, ...]，按频次降序排列
- 响应时间 < 300ms

**And**
- 支持按用户历史统计（全局常用）
- 支持按项目历史统计（项目常用）

---

### Story 6.3: 设计并实现底部快速录入操作条（C03 QuickEntryBar）

As a **前端开发者**,
I want **设计实现 QuickEntryBar 组件，提供全局快速录入入口**,
So that **主理人能随时快速录入费用**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 创建 components/QuickEntryBar.vue 组件
**Then**
- 组件固定于 TabBar 上方，高度 96rpx
- 显示"+ 快速录入"全宽蓝色按钮
- 背景白色，顶部细分割线
- 点击后触发底部弹出表单
- 在主页、项目详情页等页面均可使用

**And**
- 表单展开时操作条隐藏
- 响应式布局适配所有屏幕

---

### Story 6.4: 实现快速费用录入表单与 3 步流程

As a **移动端开发者**,
I want **创建快速费用录入表单，支持 3 步完成**,
So that **主理人能快速记录费用**.

**Acceptance Criteria:**

**Given** QuickEntryBar 组件已实现
**When** 用户点击快速录入触发表单
**Then**
- 表单从底部滑入（Action Sheet）
- 第 1 步：选择项目（模糊搜索，2 字出结果）
- 第 2 步：输入金额 + 选择费用类别（系统推荐前 5 个）
- 第 3 步：提交
- 提交成功显示绿色 Toast（2 秒消失）

**And**
- 步骤指示器显示当前进度（1/3、2/3、3/3）
- 金额输入框失焦后自动格式化：¥1,234.00
- 项目和供应商支持 2 字模糊搜索
- 金额必填，项目必填

---

### Story 6.5: 设计并实现费用类别快选组件（C05 CategoryQuickPicker）

As a **前端开发者**,
I want **设计实现 CategoryQuickPicker 组件**,
So that **用户能快速选择费用类别**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 创建 components/CategoryQuickPicker.vue 组件
**Then**
- 组件显示横向滚动的类别 Chips（历史常用前 5 个 + "更多"按钮）
- 未选中状态：白色底 + 蓝色边框
- 选中状态：蓝色底色 + 白色文字
- 点击"更多"展开完整类别列表

**And**
- 动画流畅
- 自动识别最常用类别

---

### Story 6.6: 实现金额输入增强组件（C09 AmountInput）

As a **前端开发者**,
I want **设计实现 AmountInput 组件，支持自动格式化**,
So that **用户输入金额时能自动格式化为标准格式**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 创建 components/AmountInput.vue 组件
**Then**
- 输入框 `type="digit"` 唤起系统原生数字键盘
- 聚焦时显示原始数字（如 1234）
- 失焦时自动格式化为 ¥1,234.00（右对齐）
- 支持清空和修改

**And**
- 使用等宽字体确保千分位对齐
- 符合财务工具审美

---

### Story 6.7: 集成 OCR 发票识别（一期，仅支持增值税发票）

As a **移动端开发者**,
I want **集成 OCR 功能，支持拍照识别增值税发票**,
So that **用户能通过拍照快速识别发票信息**.

**Acceptance Criteria:**

**Given** 快速录入表单已实现
**When** 用户在费用录入表单中点击"拍照识别"
**Then**
- 弹出相机拍照界面（或选择相册图片）
- 发送图片到腾讯云 OCR API（通过 uni-cloud 云函数中转）
- 识别结果显示在 OcrConfirmSheet 组件（大字号展示）
- 用户可编辑识别结果后提交

**And**
- 一期仅支持增值税发票（明确告知用户）
- 识别失败时显示错误提示和重试按钮
- 识别结果可点击字段直接修改

---

### Story 6.8: 实现 OcrConfirmSheet 组件（C10）

As a **前端开发者**,
I want **设计实现 OcrConfirmSheet 组件，展示 OCR 识别结果**,
So that **用户能清晰确认识别的数据**.

**Acceptance Criteria:**

**Given** OCR 识别逻辑已实现
**When** OCR 识别完成后调起 OcrConfirmSheet
**Then**
- 弹层以 uni-popup 包裹，从底部滑入
- 大字号展示识别字段（金额、日期、摘要、对方户名等）
- 每个字段可点击内联编辑
- 确认按钮确认提交
- 重新识别按钮返回相机

**And**
- 字段排列清晰，便于用户检查
- 金额字段最显眼
- 防止静默错误

---

### Story 6.9: PC Web 端费用列表页面

As a **PC 开发者**,
I want **在 uni-admin 创建费用列表页面**,
So that **管理员能在 Web 端查看和管理费用记录**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建费用列表页面
**Then**
- 页面显示费用列表表格（项目名、供应商、金额、日期、类别、状态、操作）
- 支持按项目筛选、按类别筛选、按状态筛选
- 表头支持排序
- 每页 20 条数据，底部分页器
- 行操作：查看/编辑/删除

**And**
- 新建/编辑显示表单页面
- 支持批量标记为"已核销"
- 显示费用来源标签（手动/OCR/导入）

---

## Epic 7 Stories: 银行流水核销与置信度匹配

### Story 7.1: 创建流水和核销数据表与云函数基础

As a **后端开发者**,
I want **创建银行流水 (bank_statements) 和核销 (reconciliations) 数据表**,
So that **系统能存储和管理流水和核销信息**.

**Acceptance Criteria:**

**Given** expenses、collections 表已创建
**When** 创建 bank_statements 和 reconciliations 集合
**Then**
- MongoDB 集合 `bank_statements` 创建完成，包含字段：_id、statementDate、amount、description、counterparty、source、sourceData、importedAt、importedBy、createdAt
- MongoDB 集合 `reconciliations` 创建完成，包含字段：_id、statementId、expenseId/collectionId、confidence、matchDetails、reconciliationType、reconciliedAt、reconciliedBy、createdAt
- source 字段支持：excel、csv、screenshot、manual
- confidence 字段支持：高（0.9-1.0）、中（0.7-0.9）、低（<0.7）

**And**
- 云函数 `bank-statement-import`、`bank-statement-list` 部署成功
- 云函数 `reconciliation-match`、`reconciliation-confirm`、`reconciliation-list` 部署成功
- 返回统一响应格式

---

### Story 7.2: 实现银行流水导入状态机与解析引擎

As a **后端开发者**,
I want **实现流水导入状态机和 Excel/CSV 解析引擎**,
So that **系统能正确解析并存储银行流水**.

**Acceptance Criteria:**

**Given** bank_statements 表已创建
**When** 创建 `bank-statement-parse` 云函数和状态机
**Then**
- 导入流程状态：PENDING → PARSING → PARSED → REVIEWING → APPROVED → COMMITTING → SUCCESS
- 支持 Excel/CSV 格式解析（识别日期、金额、摘要、对方户名字段）
- 自动映射字段（日期/金额/摘要/对方户名）
- 解析后生成预览，返回识别到的字段和样本数据
- 用户确认后提交到数据库

**And**
- 使用 MongoDB 事务保证批量插入原子性
- 支持撤销上传
- 记录导入者和导入时间

---

### Story 7.3: 实现置信度匹配算法

As a **后端开发者**,
I want **实现银行流水与记录的置信度匹配算法**,
So that **系统能自动匹配流水和费用/收款**.

**Acceptance Criteria:**

**Given** bank_statements 表已创建
**When** 创建 `reconciliation-calculate-confidence` 云函数
**Then**
- 匹配规则 1：金额完全吻合 + 关键词命中 = 高置信（0.95）
- 匹配规则 2：金额吻合，无关键词 = 中置信（0.75）
- 匹配规则 3：金额近似（±5%）或无明确匹配 = 低置信（0.5）
- 关键词匹配：项目名、客户名、供应商名在流水摘要中出现
- 返回匹配排序列表（按置信度降序）

**And**
- 性能要求：1000 条流水匹配 < 2 秒
- 支持手动调整匹配结果

---

### Story 7.4: 设计并实现核销流水行组件（C04 ReconciliationRow）

As a **前端开发者**,
I want **设计实现 ReconciliationRow 组件，展示核销流水**,
So that **用户能快速浏览和操作核销流水**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 创建 components/ReconciliationRow.vue 组件
**Then**
- 组件按置信度分级展示：
  - 高置信：绿色背景 (#E8F8EF)、默认勾选
  - 中置信：白色背景、不默认勾选
  - 低置信：橙色背景 (#FFF3EC)、标注"需人工处理"
- 显示字段：复选框、日期、摘要、对方户名、金额（右对齐）、置信度标签
- 点击行展开详情面板，显示匹配依据

**And**
- 支持复选框多选
- 已核销项显示灰色划线
- 响应式布局

---

### Story 7.5: 设计并实现置信度标签组件（C07 ConfidenceLevelBadge）

As a **前端开发者**,
I want **设计实现 ConfidenceLevelBadge 组件**,
So that **用户能清晰识别匹配置信度**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 创建 components/ConfidenceLevelBadge.vue 组件
**Then**
- 高置信度：绿色 (#07C160) + 文字"自动匹配"
- 中置信度：灰色 (#666666) + 文字"待确认"
- 低置信度：橙色 (#FF976A) + 文字"需人工"
- 小型标签，与 ReconciliationRow 整合

**And**
- 颜色符合设计系统
- 文字标签作为主要信息载体

---

### Story 7.6: 设计并实现批量核销确认弹层（C08 BatchConfirmSheet）

As a **前端开发者**,
I want **设计实现 BatchConfirmSheet 组件，支持一键全部确认**,
So that **用户能快速批量核销高置信流水**.

**Acceptance Criteria:**

**Given** 核销列表已实现
**When** 用户点击"一键全部确认"
**Then**
- 弹层以 uni-popup 包裹，从底部滑入
- 显示汇总信息："确认核销 X 笔高置信项，共 ¥XXX.00"
- 显示排除信息："Y 笔需人工处理，已自动排除"
- 显示"确认"和"取消"按钮

**And**
- 点击确认后执行批量核销
- 防止误批量操作

---

### Story 7.7: 小程序核销 Tab 页面与导入流程

As a **移动端开发者**,
I want **创建核销 Tab 页面，支持流水导入和核销操作**,
So that **主理人能快速完成核销流程**.

**Acceptance Criteria:**

**Given** 云函数和组件已实现
**When** 创建 pages/reconciliation/index 页面
**Then**
- 页面显示待核销流水列表（置信度分级）
- 顶部有"导入银行流水"按钮
- 支持三种导入方式：Excel/CSV 主推路径、截图 OCR（二期）、手动录入
- 选择文件后显示解析预览
- 用户确认后执行匹配和展示

**And**
- 高置信项默认勾选
- 低置信项排列到底部
- "一键全部确认"触发批量确认弹层
- 逐笔确认时可点击展开查看匹配详情

---

### Story 7.8: 实现核销流程（逐笔确认 & 批量确认）

As a **移动端开发者**,
I want **实现核销确认逻辑，支持逐笔和批量操作**,
So that **用户能灵活完成核销**.

**Acceptance Criteria:**

**Given** 核销列表已显示
**When** 用户进行核销操作
**Then**
- 逐笔确认：点击流水行 → 展开详情 → 查看匹配依据 → 点击"确认核销"按钮
- 批量确认：选中多个流水 → 点击"一键全部确认" → 汇总确认弹层 → 点击"确认"
- 确认后流水从列表消失
- 显示绿色 Toast："核销成功"（2 秒消失）

**And**
- 核销完成后自动刷新列表
- 列表为空时显示空状态："本期所有流水已核销，很棒！" + 查看报表入口

---

### Story 7.9: 实现无计划付款自动生成记录

As a **后端开发者**,
I want **实现无计划付款自动生成预算/应付款记录**,
So that **系统能自动处理未预期的支出**.

**Acceptance Criteria:**

**Given** 核销流程已实现
**When** 用户核销一笔未在系统中记录的流水
**Then**
- 系统检测到流水无匹配记录
- 自动创建费用记录（金额、日期、摘要从流水提取）
- 自动创建预算记录（项目关联通过摘要匹配）
- 生成的记录显示来源标签："自动匹配"

**And**
- 用户可编辑自动生成的记录
- 记录创建者设为"系统"

---

### Story 7.10: 微信服务通知推送（核销提醒）

As a **后端开发者**,
I want **实现微信服务通知推送核销提醒**,
So that **用户能及时收到待核销流水提醒**.

**Acceptance Criteria:**

**Given** 核销流程已实现
**When** 系统检测到本月有待确认收款但 30 天未导入流水
**Then**
- 定时任务每天检查（如每天 9 点）
- 推送微信服务通知："您有 X 笔待核销记录，本月银行流水尚未导入，点击前往"
- 点击通知直接跳转到核销 Tab

**And**
- 通知发送给项目创建者
- 每个用户每天最多一条通知

---

### Story 7.11: PC Web 端核销记录查看页面

As a **PC 开发者**,
I want **在 uni-admin 创建核销记录查看页面**,
So that **管理员能在 Web 端查看历史核销记录**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建核销记录页面
**Then**
- 页面显示核销记录列表表格（日期、金额、摘要、关联项目、关联费用、置信度、操作）
- 支持按项目筛选、按日期范围筛选
- 表头支持排序
- 每页 20 条数据，底部分页器
- 行操作：查看详情/撤销核销（如需要）

**And**
- 撤销前 Modal 确认
- 显示匹配依据和置信度信息
- 支持导出查看

---

## Epic 8 Stories: 报表统计与Excel导出

### Story 8.1: 实现报表数据聚合函数

As a **后端开发者**,
I want **实现报表数据聚合函数，统计应收应付/实收实付**,
So that **系统能快速生成财务报表**.

**Acceptance Criteria:**

**Given** projects、collections、expenses、reconciliations 表已创建
**When** 创建 `report-get-summary` 和 `report-get-details` 云函数
**Then**
- 云函数 `report-get-summary` 统计（按时间维度）：
  - 应收总额、已收总额、应付总额、已付总额
  - 应收占比、实收占比、应付占比、实付占比
  - 按月、按季、按年聚合
- 云函数 `report-get-details` 返回明细数据（支持按时间范围筛选）
- 响应时间 < 1 秒

**And**
- 支持按项目、按客户、按供应商多维度统计
- 数据准确性（与原始记录一致）

---

### Story 8.2: 实现 Excel 导出功能（前端 SheetJS）

As a **前端开发者**,
I want **使用 SheetJS 库实现 Excel 导出**,
So that **用户能导出财务报表供财务人员查看**.

**Acceptance Criteria:**

**Given** 报表数据已可用
**When** 用户点击"导出 Excel"按钮
**Then**
- 调用 SheetJS 库生成 Excel 文件
- 文件包含两个 Sheet：汇总表 + 明细表
- 汇总表：应收/已收/应付/已付四个数字
- 明细表：完整的费用、收款、核销记录
- 文件命名为 `report_${当前月份}.xlsx`
- 自动下载到用户设备

**And**
- 数字格式统一为 ¥1,234.00
- 日期格式为 YYYY-MM-DD
- 表头加粗、背景色区分

---

### Story 8.3: PC Web 端基础报表页面

As a **PC 开发者**,
I want **在 uni-admin 创建基础报表页面**,
So that **管理员能在 Web 端查看财务报表**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建报表页面
**Then**
- 页面显示时间筛选器：本月/本季/本年/自定义日期范围
- 显示汇总卡片（四格）：应收/已收/应付/已付
- 数字大字号显示，格式为 ¥1,234.00
- 显示数据表格：完整明细，支持列排序
- 右上角有"导出 Excel"按钮

**And**
- 筛选时自动刷新数据
- 表格固定表头（position: sticky）
- 响应式布局

---

### Story 8.4: 小程序端报表入口与查看

As a **移动端开发者**,
I want **在小程序创建报表查看入口**,
So that **主理人能在小程序查看基础报表**.

**Acceptance Criteria:**

**Given** 报表功能已实现
**When** 用户进入"我"Tab 或首页
**Then**
- 显示"查看报表"入口（按钮或链接）
- 点击后显示时间筛选（本月/本季/本年）
- 显示汇总数字：应收/已收/应付/已付
- 显示明细列表（可滚动）

**And**
- 支持导出 Excel
- 数据加载时显示骨架屏

---

## Epic 9 Stories: 用户权限与系统设置

### Story 9.1: 创建用户账号管理云函数

As a **后端开发者**,
I want **实现用户账号管理云函数**,
So that **管理员能管理系统用户**.

**Acceptance Criteria:**

**Given** uni-id 已配置
**When** 创建 `user-list`、`user-create`、`user-update`、`user-disable` 云函数
**Then**
- 云函数支持列表、新增、编辑、禁用操作
- 可设置用户角色（MVP 阶段所有用户默认全权限）
- 可禁用/启用用户
- 返回统一响应格式

**And**
- 所有操作记录到操作日志
- 密码修改时加密存储

---

### Story 9.2: PC Web 端用户账号管理页面

As a **PC 开发者**,
I want **在 uni-admin 创建用户账号管理页面**,
So that **管理员能在 Web 端管理用户**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建用户管理页面
**Then**
- 页面显示用户列表表格（用户名、昵称、邮箱、创建时间、状态、操作）
- 行操作：查看/编辑/禁用/删除
- 右上角有"创建用户"按钮
- 新建/编辑显示表单页面

**And**
- 禁用用户后无法登录
- 修改用户信息后立即生效
- 删除前 Modal 确认

---

### Story 9.3: 实现系统设置与配置管理

As a **后端开发者**,
I want **实现系统设置管理（费用类别、应收阶段配置）**,
So that **管理员能自定义系统参数**.

**Acceptance Criteria:**

**Given** uniCloud 环境已初始化
**When** 创建 `system-setting` 集合和管理云函数
**Then**
- 集合包含字段：_id、setting_type、config_data、updatedAt、updatedBy
- 支持配置项：费用类别列表、应收阶段列表、通知模板、系统参数等
- 云函数 `system-setting-get`、`system-setting-update` 部署成功
- 配置更新后实时应用

**And**
- MVP 阶段预设默认配置
- 支持重置为默认配置

---

### Story 9.4: PC Web 端系统设置页面

As a **PC 开发者**,
I want **在 uni-admin 创建系统设置页面**,
So that **管理员能配置系统参数**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 在 /admin 中创建系统设置页面
**Then**
- 页面显示设置表单
- 费用类别配置：支持新增、编辑、删除类别
- 应收阶段配置：支持新增、编辑、删除阶段
- 系统参数配置：通知设置、导出设置等
- 修改后自动保存

**And**
- 提供"重置为默认"按钮
- 修改后显示成功提示

---

### Story 9.5: 实现粗粒度操作日志记录

As a **后端开发者**,
I want **实现操作日志记录系统**,
So that **管理员能追踪用户操作**.

**Acceptance Criteria:**

**Given** uniCloud 环境已初始化
**When** 创建 `operation_logs` 集合和日志记录逻辑
**Then**
- 集合包含字段：_id、operationType、objectType、objectId、userId、timestamp、description
- 操作类型：create、update、delete、export 等
- 对象类型：project、customer、expense、collection 等
- 所有重要操作自动记录
- 云函数 `operation-log-list` 支持查询和筛选

**And**
- 日志数据不可修改（可删除，但需权限限制）
- 支持按时间范围、操作者、操作类型筛选

---

## Epic 10 Stories: 设计系统与一致性保障

### Story 10.1: 完善设计 Token 配置与文档

As a **设计系统负责人**,
I want **完善设计 Token 配置，创建详细的设计系统文档**,
So that **开发团队有完整的设计规范可参考**.

**Acceptance Criteria:**

**Given** 基础 Token 已配置
**When** 创建详细的设计系统文档
**Then**
- Token 文档包含：颜色系统（主色、状态色、中性色）、字体系统（6 级层级）、间距系统（5 级）、圆角、阴影等
- 文档包含使用示例和反面例子
- 提供 Figma 设计稿链接
- 所有 Token 在 uni.scss 中完整定义

**And**
- 文档在项目 /docs 目录下
- 所有开发者都已知晓

---

### Story 10.2: 实现交互反馈规范（Toast、Action Sheet、Skeleton）

As a **前端开发者**,
I want **实现全局交互反馈规范**,
So that **所有页面的交互反馈保持一致**.

**Acceptance Criteria:**

**Given** uni-ui 已集成
**When** 创建全局反馈组件和工具函数
**Then**
- 实现全局 Toast 函数（成功绿色✓、错误红色✗、信息灰色）、消息显示 2 秒自动消失
- 实现 Action Sheet 规范使用（确认操作使用 Action Sheet，不用全屏弹窗）
- 实现 Skeleton 骨架屏组件（数据加载 > 800ms 触发）
- 实现加载状态全局管理（Pinia loading state）

**And**
- 工具函数在 utils 目录下
- 在所有需要的地方应用

---

### Story 10.3: 实现 10 个自定义业务组件（Phase 1 核心）

As a **前端开发者**,
I want **实现 10 个自定义业务组件（C01-C10）**,
So that **系统有完整的业务组件库**.

**Acceptance Criteria:**

**Given** 设计系统 Token 已配置
**When** 逐个实现业务组件
**Then**
- C01 FinanceSummaryBanner（首页财务摘要横幅）
- C02 ProjectCard（项目卡片）
- C03 QuickEntryBar（底部快速录入操作条）
- C04 ReconciliationRow（核销流水行）
- C05 CategoryQuickPicker（费用类别快选）
- C06 WarningBanner（首页预警条）
- C07 ConfidenceLevelBadge（置信度标签）
- C08 BatchConfirmSheet（批量核销确认弹层）
- C09 AmountInput（金额输入增强）
- C10 OcrConfirmSheet（OCR 确认表单）

**And**
- 所有组件使用 Token 变量，禁止硬编码颜色
- 组件文件在 components/business/ 目录
- 组件支持 Props 传递和事件发送

---

### Story 10.4: 实现状态管理方案（Pinia）

As a **前端开发者**,
I want **使用 Pinia 实现全局状态管理**,
So that **应用状态管理清晰、可维护**.

**Acceptance Criteria:**

**Given** 项目已初始化
**When** 安装 Pinia 并配置状态 Store
**Then**
- 安装 `npm install pinia`
- 创建 stores 目录结构：userStore、expenseStore、projectStore、dashboardStore
- 实现用户认证状态（token、用户信息）
- 实现费用状态（列表、选中项）
- 实现项目状态（列表、选中项）
- 实现首页状态（摘要数据、加载状态）

**And**
- 所有 Store 在应用启动时初始化
- 支持持久化存储（token、用户偏好）

---

### Story 10.5: 实现页面布局规范与响应式设计

As a **前端开发者**,
I want **确保所有页面遵循布局规范和响应式设计**,
So that **应用在所有设备上显示一致**.

**Acceptance Criteria:**

**Given** 设计系统已完成
**When** 审查所有页面布局
**Then**
- 所有尺寸使用 rpx 单位（禁止 px）
- 页面左右内边距 32rpx
- 卡片间距 16rpx
- 底部安全区适配：padding-bottom + env(safe-area-inset-bottom)
- TabBar 固定高度 98rpx
- 快速录入操作条高度 96rpx

**And**
- 在各种屏幕尺寸测试（iPhone SE - iPhone Pro Max）
- 颜色对比度合规（主文字 AA+ 级、次要文字 AA 级）

---

### Story 10.6: 实现无障碍设计规范

As a **前端开发者**,
I want **确保应用符合 WCAG 2.1 AA 核心标准**,
So that **所有用户都能无障碍使用应用**.

**Acceptance Criteria:**

**Given** 所有页面已开发
**When** 审查无障碍合规性
**Then**
- 颜色不作为唯一信息载体（状态色配文字标签）
- 所有可点击元素最小 88rpx × 88rpx
- 所有功能性图标配 aria-label 文字说明
- 表单错误提示通过 aria-describedby 关联输入框
- 加载状态骨架屏配 aria-busy 标注
- 焦点管理：底部弹层展开时聚焦第一个可交互元素

**And**
- 色盲模拟测试通过（Chrome DevTools）
- 对比度验证通过（Colour Contrast Analyser）
- 一期不支持深色模式（app.json 配置 "darkmode": false）

---

### Story 10.7: 优化包体积与性能

As a **前端开发者**,
I want **优化应用包体积和加载性能**,
So that **应用启动快速且体积轻量**.

**Acceptance Criteria:**

**Given** 所有功能已实现
**When** 执行优化
**Then**
- 主包体积 < 2MB（初始化后）
- 首页加载时间 < 2 秒
- 禁用不必要的分包
- 使用系统字体栈（不引入自定义字体文件）
- 图片资源使用 webp 格式 + lazy-load
- 云函数响应时间 < 1 秒

**And**
- npm run build 成功完成
- 包体积在 DevTools 中可见并符合目标

---

### Story 10.8: 创建项目通用样式库

As a **前端开发者**,
I want **创建通用样式库，包含按钮、表单、卡片等基础样式**,
So that **所有页面样式保持一致**.

**Acceptance Criteria:**

**Given** 设计 Token 已配置
**When** 创建 styles/common.scss
**Then**
- 按钮样式：主操作（蓝色）、次级（白+蓝边）、文字按钮（蓝文字）、危险（红色）、禁用（灰色）
- 表单输入样式：输入框、下拉选、日期选择等
- 卡片样式：圆角 16rpx、阴影、内边距
- 列表项样式：间距、分割线、hover 态
- 空状态样式：统一的空列表样式

**And**
- 样式全部使用 Token 变量
- 所有页面引入 common.scss

---

### Story 10.9: PC Web 端样式定制与主题覆盖

As a **PC 开发者**,
I want **覆盖 uni-admin 默认主色，确保 PC 端与小程序端风格一致**,
So that **两个平台视觉语言统一**.

**Acceptance Criteria:**

**Given** uni-admin 已初始化
**When** 自定义 uni-admin 主题色
**Then**
- 在 uni.scss 中覆盖 uni-admin 主色变量为 `#1677FF`
- 覆盖边框色、背景色等中性色
- PC Web 菜单、按钮、表格等组件应用新主色
- Logo 和系统名称与小程序一致

**And**
- 主色应用到所有交互元素
- 整体风格与小程序一致

---

### Story 10.10: 完成设计系统最终文档与交接

As a **设计系统负责人**,
I want **完成设计系统最终文档，并交接给团队**,
So that **所有团队成员都能参考和使用**.

**Acceptance Criteria:**

**Given** 所有设计系统元素已实现
**When** 创建最终交接文档
**Then**
- 文档包含：Token 定义、组件库、布局规范、交互规范、无障碍规范
- 文档包含 Figma 设计稿链接
- 文档包含代码示例和使用指南
- 团队成员都已收到文档链接并理解内容

**And**
- 文档在项目 docs 目录下
- 后续维护负责人已确定
- 变更流程已定义

---

## 完成总结

**总计故事数：60+个故事**

### 按 Epic 分布：

- **Epic 1**：8 个故事（项目初始化与基础架构）
- **Epic 2**：8 个故事（客户与供应商管理）
- **Epic 3**：8 个故事（项目生命周期管理）
- **Epic 4**：5 个故事（首页财务摘要与全局视图）
- **Epic 5**：3 个故事（收款计划与记录）
- **Epic 6**：9 个故事（快速费用录入与智能预填）
- **Epic 7**：11 个故事（银行流水核销与置信度匹配）
- **Epic 8**：4 个故事（报表统计与Excel导出）
- **Epic 9**：5 个故事（用户权限与系统设置）
- **Epic 10**：10 个故事（设计系统与一致性保障）

所有故事均按照 Given/When/Then 格式编写清晰的验收标准，确保每个故事都是独立可完成的，且按顺序完成不存在依赖关系。
