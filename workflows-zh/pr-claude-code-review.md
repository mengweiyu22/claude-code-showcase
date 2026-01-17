# PR Claude Code Review

## 文件概述

`pr-claude-code-review.yml` 是一个 GitHub Actions 工作流文件，用于在拉取请求（PR）触发时自动使用 Claude 进行代码审查。

## 触发条件

- **PR 事件**：当 PR 被创建（`opened`）、更新（`synchronize`）或重新打开（`reopened`）时
- **评论事件**：当有人在 PR 或 Issue 中提到 `@claude` 时

## 主要功能

### 1. 并发控制
- 使用并发组确保同一时间只有一个审查任务在运行
- 自动取消正在进行的审查任务

### 2. 权限设置
- `contents: read` - 读取仓库内容
- `pull-requests: write` - 写入 PR 信息
- `issues: read` - 读取 Issue 信息

### 3. 仓库检出逻辑
- **PR 事件**：检出 PR 分支的最新提交
- **评论事件**：
  1. 先检出主分支
  2. 然后检出对应的 PR 分支
  3. 获取 PR 的基础分支信息

### 4. Claude 代码审查
使用 Claude Code Action 进行深度代码审查，具体流程如下：

1. 读取 `.claude/agents/code-reviewer.md` 中的审查标准
2. 使用 `git diff` 查看 PR 的所有变更
3. 应用审查检查清单到修改的文件
4. 按严重程度组织反馈：Critical（关键）、Warning（警告）、Suggestion（建议）

## 技术细节

### 模型选择
- 使用 `claude-opus-4.5-20251101` 模型
- 设置 30 分钟超时
- 启用进度跟踪

### 限制的工具
- 只允许使用 Claude Code 的安全工具集
- 包括文件读取、代码搜索、Git 操作等
- 禁用了可能产生破坏性操作的工具

## 安全考虑

- 权限最小化原则，只授予必要的权限
- 并发控制防止多个审查同时进行
- 通过限制工具集确保审查过程安全

## 使用场景

这个工作流特别适合：
- 自动化的代码质量检查
- 减少人工审查负担
- 确保代码符合团队标准
- 快速识别潜在问题和改进点

## 配置建议

1. 确保 `ANTHROPIC_API_KEY` secret 已正确配置
2. 根据团队需求调整 `.claude/agents/code-reviewer.md` 中的审查标准
3. 可以考虑添加额外的触发条件，如特定标签的 PR