---
title: 开发者快速启动指南
date: 2026-03-17
audience: "Development Team"
---

# 开发者快速启动指南

## 📋 项目概览

**项目名称**：project-manager（项目-经理系统）
**平台**：WeChat 小程序 + PC Web 后台管理
**总计划**：10 个 Epic，73 个 Story

**最新更新**：Party Mode 多代理评审完成，新增 Story 1.9 和 1.10（全局数据模型 + 设计评审门控）

---

## 🚀 启动前检查

在开始开发前，确保你已完成以下事项：

- [ ] 读过 `/docs/consistency-rules.md`（Story 1.8 的输出）
- [ ] 访问过 `/docs/global-data-model.md`（Story 1.9 的输出） — **关键！**
- [ ] 访问过 `/docs/api-contracts.md`（Story 1.9 的输出） — **关键！**
- [ ] 看过设计评审记录 `/docs/design-review-record.md`（Story 1.10 的输出）— **关键！**
- [ ] 理解你所在 Epic 的前置依赖

---

## 📊 Epic 并行开发时间表

### 第 1 阶段：Epic 1 基础设施（必须先完成）

```
Timeline: Week 1-4
Story 1.1 - 初始化小程序项目结构
Story 1.2 - 配置设计 Token 与全局样式
Story 1.3 - 集成 uni-ui 组件库
Story 1.4 - 配置 uniCloud 云环境
Story 1.5 - 配置 uni-id 认证系统（小程序）
Story 1.6 - 初始化 PC Web 后台
Story 1.7 - 配置 uni-id 认证系统（PC）
Story 1.8 - 建立一致性规则文档
║
╠═══ Story 1.9 - 全局数据模型设计 [CRITICAL GATE]
║
╚═══ Story 1.10 - 设计评审与批准 [GATING POINT]
       ↓
    其他 Epic 解冻
```

**关键检查点**：Story 1.10 的批准是其他所有 Epic 开始的必要条件。

---

### 第 2 阶段：并行开发（Story 1.10 批准后）

```
Timeline: Week 5-10 (Parallel)

📍 Group A: 数据基础（并行，无冲突）
  ├─ Epic 2: 客户与供应商管理 (Week 5-9)
  │   └─ Story 2.1-2.5: CRUD 及分析
  │
  └─ Epic 3: 项目生命周期管理 (Week 5-9)
      └─ Story 3.1-3.6: CRUD、状态流转、进度

📍 Group B: 财务核心（并行，无冲突）
  ├─ Epic 5: 收款计划与记录 (Week 5-10)
  │   └─ Story 5.1-5.3: 计划、记录、对账
  │
  └─ Epic 6: 快速费用录入 (Week 5-11)
      └─ Story 6.1-6.7: 预算、支出、推荐、核销

📍 Group C: 配置系统（并行）
  └─ Epic 9: 用户权限与系统设置 (Week 5-8)
      └─ Story 9.1-9.3: 用户管理、权限、配置

📍 Group D: 设计系统（并行）
  └─ Epic 10: 设计系统与一致性保障 (Week 5-9)
      └─ Story 10.1-10.4: Token、组件、规范

📍 Timeline: 特殊依赖
  ├─ Epic 7: 银行流水核销 (Week 10+)
  │   └─ 需要等待 Epic 6 完成
  │
  ├─ Epic 4: 首页摘要 (Week 10+)
  │   └─ 需要等待 Story 2.1, 3.1, 5.1, 6.1 完成
  │
  └─ Epic 8: 报表统计 (Week 12+)
      └─ 需要等待所有核心 Epic 完成
```

---

## 🔗 你的 Epic 依赖关系速查表

### 如果你在做 Epic 2（客户与供应商）

**前置条件**：
- ✅ Story 1.10 批准（设计评审门控）
- ✅ `/docs/global-data-model.md` 已定义 customers 表

**可以立即做**：
- 在 Story 1.10 批准后立即启动
- 与 Epic 3 并行开发（无冲突）
- 与 Epic 9、10 并行开发

**你提供给谁**：
- Epic 3：客户表定义 + customerId 外键
- Epic 4：客户查询接口
- Epic 5-8：客户相关数据

---

### 如果你在做 Epic 3（项目管理）

**前置条件**：
- ✅ Story 1.10 批准
- ✅ `/docs/global-data-model.md` 已定义 projects 表
- ✅ 确保 projects.customerId 外键指向 customers 表

**可以立即做**：
- 在 Story 1.10 批准后立即启动
- 与 Epic 2 并行开发（无冲突）

**你需要从谁获得数据**：
- Epic 2：customers 表（通过外键引用）

**你提供给谁**：
- Epic 4-5-6-7-8：项目数据和状态

---

### 如果你在做 Epic 5（收款管理）

**前置条件**：
- ✅ Story 1.10 批准
- ✅ Story 3.1 完成（项目表存在）
- ✅ `/docs/global-data-model.md` 已定义 receipt_plans 表

**可以立即做**：
- 在 Story 3.1 完成后立即启动
- 与 Epic 6 并行开发（无冲突）

**你需要从谁获得数据**：
- Epic 3：projects 表（通过外键引用）

**你提供给谁**：
- Epic 4：收款数据查询接口
- Epic 8：收款数据汇总

---

### 如果你在做 Epic 6（费用管理）

**前置条件**：
- ✅ Story 1.10 批准
- ✅ Story 3.1 完成（项目表存在）
- ✅ Story 9.2 完成（费用类别配置）
- ✅ `/docs/global-data-model.md` 已定义 expenses 表

**可以立即做**：
- 在 Story 3.1 和 Story 9.2 完成后立即启动
- 与 Epic 5 并行开发（无冲突）

**你需要从谁获得数据**：
- Epic 3：projects 表
- Epic 9：expense_categories 表（用于推荐）

**你提供给谁**：
- Epic 7：费用记录（用于银行流水匹配）
- Epic 4-8：费用数据查询接口

---

### 如果你在做 Epic 4（首页摘要）

**前置条件**：
- ✅ Story 2.1 完成（customers 表 + API）
- ✅ Story 3.1 完成（projects 表 + API）
- ✅ Story 5.1 完成（receipt_plans 表 + API）
- ✅ Story 6.1 完成（expenses 表 + API）

**不能早于这些条件启动**。一旦上述 4 个 Story 完成，立即启动 Epic 4。

**你需要从谁获得数据**：
- Epic 2-3-5-6：所有数据查询接口

---

### 如果你在做 Epic 8（报表统计）

**前置条件**：
- ✅ 所有 Epic 2-7 的核心 Story 完成

**这是最后的汇总 Epic**。在其他所有 Epic 完成后启动。

---

## 📌 最关键的规则

### 1. 遵守全局数据模型 ⚠️

**所有开发者必须遵守** `/docs/global-data-model.md` 中定义的：
- ✅ 表名和字段名（使用小驼峰）
- ✅ 数据类型和约束
- ✅ 表间关系（外键）
- ✅ 枚举类型定义

**如果你发现模型定义不清晰**：
- 不要自己修改数据模型
- 立即报告给架构师
- 通过 Change Request 流程更新模型

### 2. 遵守 API 契约 ⚠️

**所有云函数接口必须遵守** `/docs/api-contracts.md` 中定义的：
- ✅ 函数命名：`<模块>-<操作>` 格式
  - 例：`customer-list`, `expense-create`, `receipt-record-add`
- ✅ 请求/响应数据结构
- ✅ 错误码定义
- ✅ 参数验证

### 3. 云函数设计原则 ⚠️

**每个云函数应该**：
- ✅ 单一职责：一个函数做一件事
- ✅ 数据验证：在函数入口验证所有参数
- ✅ 一致的错误处理：使用统一的错误码
- ✅ 事务一致性：如果涉及多表更新，使用数据库事务

**不应该**：
- ❌ 一个函数处理多个不相关的业务逻辑
- ❌ 分散实现相同的业务逻辑（如果已有相同功能，重用现有函数）
- ❌ 绕过数据验证
- ❌ 忽视错误处理

### 4. 外键关联原则 ⚠️

**数据关联在数据层通过外键完成**：
- ✅ `projects.customerId` → `customers.id`
- ✅ `receipt_plans.projectId` → `projects.id`
- ✅ `expenses.projectId` → `projects.id`

**不要**：
- ❌ 在应用层硬编码关联逻辑
- ❌ 手动维护关联关系

---

## 🧪 测试与验证

### 单元测试

每个 Story 完成后：
- [ ] 所有相关的云函数都有单元测试
- [ ] 所有关键路径都被测试（成功路径 + 错误路径）
- [ ] 边界条件被测试（空值、超长字符串等）

### 集成测试

当你的 Epic 所有 Story 完成后：
- [ ] 进行 Epic 内部集成测试（Story 间的数据流）
- [ ] 验证所有前置 Epic 的数据可正确传入
- [ ] 验证输出数据的格式和准确性

**不同 Epic 的集成测试时机**：
- **独立交付的 Epic**（2, 3, 5, 6, 7, 9, 10）：每个 Epic 完成后独立进行集成测试
- **依赖前置的 Epic**（4, 8）：在前置 Epic 数据源就绪后进行集成测试

---

## 📞 当遇到问题时

### 如果数据模型有问题

1. 不要修改自己的代码绕过问题
2. 报告给架构师
3. 通过 Change Request 流程更新全局数据模型
4. 等待更新后继续开发

### 如果 API 契约不清晰

1. 查看 `/docs/api-contracts.md`
2. 如果仍不清晰，询问产品经理或架构师
3. 不要自己定义 API 契约

### 如果发现前置依赖尚未完成

1. 检查 `dependency-audit.md` 中的依赖矩阵
2. 确认前置 Story 的完成状态
3. 告知项目经理，调整开发顺序
4. 不要跳过前置依赖继续开发

### 如果有其他 Epic 的问题

- **不是你的 Epic**：不要自己尝试修改，报告给相应 Epic 的负责人

---

## 📚 重要文件位置

| 文件 | 位置 | 用途 |
|-----|------|------|
| **Epics & Stories** | `_bmad-output/planning-artifacts/epics.md` | 所有 Epic 和 Story 定义 |
| **依赖关系审计** | `_bmad-output/planning-artifacts/dependency-audit.md` | 详细的依赖关系验证 |
| **一致性规则** | `/docs/consistency-rules.md` | 编码规范和约定 |
| **全局数据模型** | `/docs/global-data-model.md` | 数据库表结构定义 |
| **API 契约** | `/docs/api-contracts.md` | 云函数接口规范 |
| **设计评审记录** | `/docs/design-review-record.md` | 设计评审的批准记录 |

---

## ✅ 启动前最终检查

在你开始开发你的 Epic 前：

- [ ] 我已读过一致性规则 (`/docs/consistency-rules.md`)
- [ ] 我已读过全局数据模型 (`/docs/global-data-model.md`)
- [ ] 我已读过 API 契约 (`/docs/api-contracts.md`)
- [ ] 我理解我的 Epic 的前置依赖
- [ ] 我理解我的 Epic 需要提供给其他 Epic 的数据
- [ ] 我知道遇到问题时应该联系谁
- [ ] 我已将 `dependency-audit.md` 中的依赖矩阵加入书签

**如果所有项都打勾✅，你就可以开始开发了！**

---

**最后更新**：2026-03-17
**下一步**：等待 Sprint 规划，按时间表启动你的 Epic
**问题**：联系项目经理或架构师
