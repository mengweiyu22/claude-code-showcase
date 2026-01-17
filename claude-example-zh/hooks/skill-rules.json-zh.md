# skill-rules.json - 技能规则配置

## 文件概述

`skill-rules.json` 是 Claude Code 技能评估系统的核心配置文件，定义了如何根据用户提示自动激活相关技能。它包含了技能定义、触发条件、评分规则和目录映射等信息。

## 文件结构

### 1. 根配置
```json
{
  "$schema": "./skill-rules.schema.json",
  "version": "2.0",
  "config": {
    "minConfidenceScore": 3,
    "showMatchReasons": true,
    "maxSkillsToShow": 5
  },
  "scoring": { ... },
  "directoryMappings": { ... },
  "skills": { ... }
}
```

### 2. 全局配置 (config)
- **minConfidenceScore**: 3 - 最低置信度分数，低于此分数的技能不会被显示
- **showMatchReasons**: true - 是否显示匹配原因
- **maxSkillsToShow**: 5 - 最多显示的技能数量

### 3. 评分规则 (scoring)
定义每种匹配类型的分值：
- **keyword**: 2 - 简单关键词匹配
- **keywordPattern**: 3 - 关键字正则模式匹配
- **pathPattern**: 4 - 文件路径 glob 模式匹配
- **directoryMatch**: 5 - 目录映射匹配
- **intentPattern**: 4 - 用户意图检测
- **contentPattern**: 3 - 代码内容模式匹配
- **contextPattern**: 2 - 上下文模式匹配

### 4. 目录映射 (directoryMappings)
将特定目录自动映射到相关技能：
```json
{
  "src/components/core": "core-components",
  "src/components": "core-components",
  "src/screens": "navigation-architecture",
  "src/navigation": "navigation-architecture",
  "src/hooks": "react-ui-patterns",
  "src/graphql": "graphql-schema",
  ...
}
```

## 技能定义 (skills)

### 基本结构
```json
{
  "skill-name": {
    "description": "技能描述",
    "priority": 8,
    "triggers": {
      "keywords": ["关键词1", "关键词2"],
      "keywordPatterns": ["正则表达式"],
      "pathPatterns": ["glob模式"],
      "intentPatterns": ["意图模式"],
      "contentPatterns": ["代码模式"],
      "contextPatterns": ["上下文模式"]
    },
    "excludePatterns": ["排除模式"],
    "relatedSkills": ["相关技能"]
  }
}
```

### 触发条件类型

#### 1. keywords (关键词)
简单字符串匹配，不区分大小写：
```json
"keywords": ["storybook", "stories", "story"]
```

#### 2. keywordPatterns (关键字模式)
正则表达式匹配：
```json
"keywordPatterns": ["\\bstory\\b", "\\.stories\\b"]
```

#### 3. pathPatterns (路径模式)
文件路径 glob 模式匹配：
```json
"pathPatterns": ["**/*.stories.tsx", "**/stories/**"]
```

#### 4. intentPatterns (意图模式)
检测用户意图的正则表达式：
```json
"intentPatterns": [
  "(?:create|add|write|build|make).*(?:stor(?:y|ies))",
  "(?:stor(?:y|ies)).*(?:for|of)"
]
```

#### 5. contentPatterns (内容模式)
匹配代码片段中的模式：
```json
"contentPatterns": ["ComponentMeta", "StoryObj", "@storybook"]
```

#### 6. contextPatterns (上下文模式)
匹配对话上下文：
```json
"contextPatterns": ["from the reviewer", "in the pr"]
```

#### 7. excludePatterns (排除模式)
排除特定条件：
```json
"excludePatterns": ["e2e", "maestro", "end-to-end"]
```

#### 8. relatedSkills (相关技能)
推荐的相关技能：
```json
"relatedSkills": ["core-components"]
```

### 技能优先级 (priority)
- 范围：1-10
- 相同分数时，优先级高的技能优先显示
- 不同技能有不同的默认优先级

## 主要技能定义

### 1. storybook
- 描述：Storybook 故事创建模式
- 优先级：8
- 触发条件：storybook、stories、story 相关关键词和路径

### 2. testing-patterns
- 描述：Jest 测试模式和 TDD 工作流
- 优先级：9
- 触发条件：test、jest、spec、tdd、mock、factory
- 排除：e2e、maestro、end-to-end

### 3. formik-patterns
- 描述：Formik 表单处理和验证
- 优先级：7
- 触发条件：form、formik、validation、yup

### 4. graphql-schema
- 描述：GraphQL 查询、变异和代码生成
- 优先级：8
- 相关技能：react-ui-patterns

### 5. navigation-architecture
- 描述：React Navigation 模式和深度链接
- 优先级：7
- 触发条件：navigation、navigate、screen、route

### 6. i18n-translations
- 描述：国际化翻译模式
- 优先级：6
- 触发条件：translation、i18n、localization、translate

### 7. core-components
- 描述：核心组件库和设计系统
- 优先级：7
- 排除：storybook、test

### 8. systematic-debugging
- 描述：四阶段调试和根本原因分析
- 优先级：9
- 触发条件：bug、debug、fix、error、issue

### 9. state-management
- 描述：Redux Toolkit、Jotai、Apollo Client 状态模式
- 优先级：6
- 排除：state of、current state、error state

### 10. github-actions
- 描述：GitHub Actions CI/CD 工作流模式
- 优先级：6
- 触发条件：github action、workflow、ci/cd

### 11. analytics-tracking
- 描述：使用 Amplitude、FullStory、Datadog、Sentry 的分析
- 优先级：5
- 触发条件：analytics、tracking、amplitude、fullstory

### 12. list-pagination
- 描述：FlashList、分页、无限滚动模式
- 优先级：5
- 排除：todo list、checklist、list of

### 13. modal-actionsheet
- 描述：Modal 和 Actionsheet 覆盖层模式
- 优先级：5
- 触发条件：modal、actionsheet、bottom sheet

### 14. maestro-e2e
- 描述：React Native 的 Maestro 端到端测试
- 优先级：6
- 触发条件：e2e、maestro、end-to-end

### 15. react-ui-patterns
- 描述：React 模式、hooks、加载状态、错误处理
- 优先级：6
- 排除：test、jest

### 16. receiving-code-review
- 描述：处理代码审查反馈
- 优先级：4
- 触发条件：code review、pr feedback

### 17. documentation
- 描述：文档标准和 TSDoc
- 优先级：3
- 触发条件：documentation、tsdoc、jsdoc

### 18. typescript-conventions
- 描述：TypeScript 严格模式和约定
- 优先级：5
- 排除：issue type、type of bug

### 19. verification-before-completion
- 描述：基于证据的完成声明协议
- 优先级：8
- 触发条件：verify、check、confirm、done

### 20. defense-in-depth
- 描述：多层数据验证防止 Bug
- 优先级：6
- 相关技能：systematic-debugging

## 配置建议

### 1. 分值调整
- 根据项目重要性调整分值
- 目录匹配应获得最高分值
- 意图检测应获得较高分值

### 2. 优先级设置
- 高优先级技能：testing-patterns (9)、systematic-debugging (9)
- 中优先级技能：storybook (8)、graphql-schema (8)
- 低优先级技能：documentation (3)、receiving-code-review (4)

### 3. 排除规则
- 避免技能误触发
- 确保精确匹配
- 定期审查排除规则

### 4. 相关技能
- 建立技能之间的关联
- 提供更全面的工作流建议
- 避免循环依赖

这个配置文件是 Claude Code 技能系统的核心，通过精细的配置可以实现智能化的技能激活，提高开发效率。