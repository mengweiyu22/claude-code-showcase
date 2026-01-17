# skill-rules.schema.json - 技能规则 JSON Schema

## 文件概述

`skill-rules.schema.json` 是 `skill-rules.json` 配置文件的 JSON Schema 定义。它提供了验证配置文件格式、结构和数据类型的标准，确保配置文件的正确性和一致性。

## Schema 结构

### 1. 基础信息
```json
{
  "$schema": "http://json-schema.org/draft-07/schema#",
  "title": "Skill Rules Configuration",
  "description": "Configuration for automatic skill activation based on prompt analysis",
  "type": "object",
  "required": ["version", "config", "scoring", "skills"]
}
```

### 2. 根对象属性
#### 必需字段
- **version**: 字符串，符合 `^\d+\.\d+$` 模式的版本号
- **config**: 全局配置对象
- **scoring**: 评分规则对象
- **skills**: 技能定义对象

#### 可选字段
- **$schema**: JSON Schema 引用字符串

## 详细属性定义

### 1. config (全局配置)
```json
{
  "type": "object",
  "required": ["minConfidenceScore", "showMatchReasons", "maxSkillsToShow"],
  "properties": {
    "minConfidenceScore": {
      "type": "integer",
      "minimum": 1,
      "description": "Minimum score required to suggest a skill"
    },
    "showMatchReasons": {
      "type": "boolean",
      "description": "Whether to show why each skill was matched"
    },
    "maxSkillsToShow": {
      "type": "integer",
      "minimum": 1,
      "maximum": 10,
      "description": "Maximum number of skills to show in output"
    }
  }
}
```

**字段说明**：
- **minConfidenceScore**: 1-10 的整数，建议技能的最低分数
- **showMatchReasons**: 布尔值，是否显示匹配原因
- **maxSkillsToShow**: 1-10 的整数，最多显示的技能数量

### 2. scoring (评分规则)
```json
{
  "type": "object",
  "required": ["keyword", "keywordPattern", "pathPattern", "directoryMatch", "intentPattern", "contentPattern", "contextPattern"],
  "properties": {
    "keyword": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for simple keyword match"
    },
    "keywordPattern": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for regex pattern match"
    },
    "pathPattern": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for file path glob match"
    },
    "directoryMatch": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for directory mapping match"
    },
    "intentPattern": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for user intent detection"
    },
    "contentPattern": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for code content pattern match"
    },
    "contextPattern": {
      "type": "integer",
      "minimum": 1,
      "description": "Points for conversation context match"
    }
  }
}
```

**字段说明**：
- 所有分值必须是 ≥1 的整数
- 不同匹配类型的分值反映了其重要性
- 目录匹配通常获得最高分值

### 3. directoryMappings (目录映射)
```json
{
  "type": "object",
  "description": "Map directory paths to skill names",
  "additionalProperties": {
    "type": "string",
    "description": "Skill name to suggest for this directory"
  }
}
```

**说明**：
- 动态属性，键为目录路径，值为技能名称
- 支持任意数量的目录映射
- 路径匹配是精确匹配或前缀匹配

### 4. skills (技能定义)
```json
{
  "type": "object",
  "description": "Skill definitions and their triggers",
  "additionalProperties": {
    "type": "object",
    "required": ["description", "triggers"],
    "properties": {
      "description": {
        "type": "string",
        "description": "Human-readable description of what triggers this skill"
      },
      "priority": {
        "type": "integer",
        "minimum": 1,
        "maximum": 10,
        "default": 5,
        "description": "Skill priority (higher = suggested first when scores are equal)"
      },
      "triggers": {
        "type": "object",
        "description": "Conditions that trigger this skill",
        "properties": {
          "keywords": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Simple keywords to match (case-insensitive)"
          },
          "keywordPatterns": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Regex patterns to match in prompt"
          },
          "pathPatterns": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Glob patterns for file paths mentioned in prompt"
          },
          "intentPatterns": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Regex patterns to detect user intent (e.g., 'create.*test')"
          },
          "contentPatterns": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Patterns to match in code snippets"
          },
          "contextPatterns": {
            "type": "array",
            "items": { "type": "string" },
            "description": "Phrases indicating conversation context"
          }
        }
      },
      "excludePatterns": {
        "type": "array",
        "items": { "type": "string" },
        "description": "Patterns that exclude this skill from matching"
      },
      "relatedSkills": {
        "type": "array",
        "items": { "type": "string" },
        "description": "Skills to suggest alongside this one"
      }
    }
  }
}
```

#### 技能属性详解

##### 基本属性
- **description**: 字符串，技能的人类可读描述
- **priority**: 整数，1-10，默认为 5，技能优先级

##### 触发条件 (triggers)
- **keywords**: 字符串数组，简单关键词匹配（不区分大小写）
- **keywordPatterns**: 字符串数组，正则表达式模式
- **pathPatterns**: 字符串数组，文件路径 glob 模式
- **intentPatterns**: 字符串数组，用户意图检测正则表达式
- **contentPatterns**: 字符串数组，代码内容模式
- **contextPatterns**: 字符串数组，上下文模式

##### 可选属性
- **excludePatterns**: 字符串数组，排除模式
- **relatedSkills**: 字符串数组，相关技能列表

#### 数组元素约束
- 所有数组都包含字符串类型元素
- 可以包含空数组（表示无限制）

## Schema 使用场景

### 1. 配置验证
- 在加载配置文件时验证其结构
- 确保所有必需字段都存在
- 验证数据类型和值范围

### 2. IDE 支持
- 为编辑器提供自动补全
- 显示字段说明和类型提示
- 帮助开发者正确编辑配置

### 3. 文档生成
- 自动生成配置文档
- 提供字段说明和示例
- 帮助新用户理解配置结构

### 4. 测试框架
- 编写配置验证测试
- 确保配置的正确性
- 验证配置的边界条件

## Schema 版本兼容性

- **JSON Schema 版本**: Draft 07
- **兼容性**: 兼容大多数现代 JSON Schema 验证器
- **扩展性**: 可以轻松扩展以支持更多字段

## 实际应用示例

### 1. 验证配置文件
```javascript
const Ajv = require('ajv');
const ajv = new Ajv();
const validate = ajv.compile(require('./skill-rules.schema.json'));
const valid = validate(config);
if (!valid) console.log(validate.errors);
```

### 2. IDE 提示
当编辑配置文件时，IDE 会根据 Schema 提供：
- 字段自动补全
- 类型检查
- 文档提示

### 3. 配置生成
根据 Schema 自动生成配置模板：
```javascript
const defaultConfig = {
  version: "2.0",
  config: {
    minConfidenceScore: 3,
    showMatchReasons: true,
    maxSkillsToShow: 5
  },
  // ... 其他默认值
};
```

## Schema 的优势

### 1. 类型安全
- 严格的类型定义
- 值范围限制
- 必需字段验证

### 2. 文档化
- 自带描述和说明
- 清晰的结构定义
- 易于理解和维护

### 3. 验证友好
- 支持标准 JSON Schema 验证器
- 提供详细的错误信息
- 便于自动化测试

### 4. 扩展性
- 支持动态属性
- 可以轻松添加新字段
- 向后兼容

## 注意事项

1. **版本兼容性**: 使用 Draft 07 确保广泛的兼容性
2. **性能考虑**: 复杂的 Schema 可能影响验证性能
3. **文档更新**: Schema 修改时需要同步更新文档
4. **测试覆盖**: 需要充分的测试覆盖所有 Schema 规则

这个 JSON Schema 为技能规则配置提供了强大的类型约束和文档支持，是确保 Claude Code 技能系统稳定运行的重要保障。