# Claude Code 技能

此目录包含项目特定的技能，为 Claude 提供此代码库的领域知识和最佳实践。

## 按类别划分的技能

### 代码质量与模式
| 技能 | 描述 |
|-------|-------------|
| [testing-patterns](./testing-patterns/SKILL-zh.md) | Jest 测试、工厂函数、模拟策略、TDD 工作流 |
| [systematic-debugging](./systematic-debugging/SKILL-zh.md) | 四阶段调试方法论、根本原因分析 |

### React 与 UI
| 技能 | 描述 |
|-------|-------------|
| [react-ui-patterns](./react-ui-patterns/SKILL-zh.md) | React 模式、加载状态、错误处理、GraphQL hooks |
| [core-components](./core-components/SKILL-zh.md) | 设计系统组件、令牌、组件库 |
| [formik-patterns](./formik-patterns/SKILL-zh.md) | 表单处理、验证、提交模式 |

### 数据与 API
| 技能 | 描述 |
|-------|-------------|
| [graphql-schema](./graphql-schema/SKILL-zh.md) | GraphQL 查询、变异、代码生成 |

## 常见任务的技能组合

### 构建新功能
1. **react-ui-patterns** - 加载/错误/空状态
2. **graphql-schema** - 创建查询/变异
3. **core-components** - UI 实现
4. **testing-patterns** - 编写测试（TDD）

### 构建表单
1. **formik-patterns** - 表单结构和验证
2. **graphql-schema** - 提交的变异
3. **react-ui-patterns** - 加载/错误处理

### 调试问题
1. **systematic-debugging** - 根本原因分析
2. **testing-patterns** - 先编写失败的测试

## 技能工作原理

当 Claude 识别到相关上下文时，技能会自动调用。每个技能提供：

- **何时使用** - 触发条件
- **核心模式** - 最佳实践和示例
- **反模式** - 需要避免的内容
- **集成** - 技能如何连接

## 添加新技能

1. 创建目录：`.claude/skills/skill-name/`
2. 添加 `SKILL.md`（区分大小写）和 YAML frontmatter：
   ```yaml
   ---
   # 必需字段
   name: skill-name              # 小写，连字符，最多 64 个字符
   description: 功能描述和使用时机。包含触发关键词。  # 最多 1024 个字符

   # 可选字段
   allowed-tools: Read, Grep, Glob    # 限制可用工具
   model: claude-sonnet-4-20250514    # 使用的特定模型
   ---
   ```
3. 包含标准部分：何时使用、核心模式、反模式、集成
4. 添加到此 README
5. 将触发器添加到 `.claude/hooks/skill-rules.json`

**重要**：`description` 字段很关键——Claude 使用语义匹配来决定何时应用技能。包含用户自然会提到的关键词。

## 维护

- 当模式更改时更新技能
- 删除过时的信息
- 添加新出现的新模式
- 保持示例与代码库同步