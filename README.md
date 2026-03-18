# 项目管理小程序 (project-manager)

 uni-app + Vue 3 + TypeScript 项目，用于工程项目款全过程管理。

## 功能

- 客户与供应商管理
- 项目生命周期管理（7个阶段）
- 收款计划与记录
- 费用报销与核销
- 财务报表与导出

## 技术栈

- **前端框架**: uni-app 5.x + Vue 3 + TypeScript
- **构建工具**: Vite
- **样式**: SCSS + uni.scss 设计 Token

## 开发

```bash
# 安装依赖
npm install

# H5 开发预览
npm run dev

# 构建生产版本
npm run build
```

## 目录结构

```
src/
├── pages/          # 页面
├── components/     # 组件
├── stores/         # 状态管理
├── styles/         # 样式
├── types/          # 类型定义
└── cloudfunctions/ # 云函数
```

## 微信开发者工具

1. 打开微信开发者工具
2. 导入项目目录
3. 使用 H5 预览或导入编译
