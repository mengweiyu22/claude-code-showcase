# 项目名称

> 这是一个示例 CLAUDE.md 文件，展示如何为你的项目配置 Claude Code。

## 快速事实

- **栈**: React, TypeScript, Node.js
- **测试命令**: `npm test`
- **Lint 命令**: `npm run lint`
- **构建命令**: `npm run build`

## 关键目录

- `src/components/` - React 组件
- `src/hooks/` - 自定义 React hooks
- `src/utils/` - 工具函数
- `src/api/` - API 客户端代码
- `tests/` - 测试文件

## 代码风格

- 启用 TypeScript 严格模式
- 优先使用 `interface` 而非 `type`（联合类型/交叉类型除外）
- 没有 `any` - 使用 `unknown` 代替
- 使用早期返回，避免嵌套条件
- 优先使用组合而非继承

## Git 约定

- **分支命名**: `{initials}/{description}`（例如 `jd/fix-login`）
- **提交格式**: 常规提交（`feat:`、`fix:`、`docs:` 等）
- **PR 标题**: 与提交格式相同

## 关键规则

### 错误处理
- 绝不要静默吞咽错误
- 总是为错误显示用户反馈
- 记录错误用于调试

### UI 状态
- 总是处理：加载、错误、空、成功状态
- 仅在没有数据时显示加载
- 每个列表都需要空状态

### 变更操作
- 在异步操作期间禁用按钮
- 在按钮上显示加载指示器
- 总是有带用户反馈的 onError 处理器

## 测试

- 首先编写失败的测试（TDD）
- 使用工厂模式：`getMockX(overrides)`
- 测试行为，而非实现
- 提交前运行测试

## 技能激活

在实现任何任务之前，检查是否有相关技能适用：

- 创建测试 → `testing-patterns` 技能
- 构建表单 → `formik-patterns` 技能
- GraphQL 操作 → `graphql-schema` 技能
- 调试问题 → `systematic-debugging` 技能
- UI 组件 → `react-ui-patterns` 技能

## 常用命令

```bash
# 开发
npm run dev          # 启动开发服务器
npm test             # 运行测试
npm run lint         # 运行 linter
npm run typecheck    # 检查类型

# Git
npm run commit       # 交互式提交
gh pr create         # 创建 PR
```
