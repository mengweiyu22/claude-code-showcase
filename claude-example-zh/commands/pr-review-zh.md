---
description: 使用项目标准审查拉取请求
allowed-tools: Read, Glob, Grep, Bash(git:*), Bash(gh:*)
---

# PR 审查

审查拉取请求：$ARGUMENTS

## 指令

1. **获取 PR 信息**：
   - 运行 `gh pr view $ARGUMENTS` 获取 PR 详情
   - 运行 `gh pr diff $ARGUMENTS` 查看更改

2. **阅读审查标准**：
   - 阅读 `.claude/agents/code-reviewer.md` 获取审查清单

3. **将清单应用于所有更改的文件**：
   - TypeScript 严格模式合规性
   - 错误处理模式
   - 加载/错误/空状态
   - 测试覆盖率
   - 文档更新

4. **提供结构化反馈**：
   - **严重**：合并前必须修复
   - **警告**：应该修复
   - **建议**：有更好

5. **使用 `gh pr comment` 发布审查评论**