---
description: 端到端处理 JIRA/Linear 工单
allowed-tools: Read, Write, Edit, Glob, Grep, Bash(git:*), Bash(gh:*), Bash(npm:*), mcp__jira__*, mcp__github__*, mcp__linear__*
---

# 工单工作流

处理工单：$ARGUMENTS

## 指令

### 1. 阅读工单

首先，获取并理解工单：

```
使用 JIRA/Linear MCP 工具来：
- 获取工单详情（标题、描述、验收标准）
- 检查关联的工单或史诗
- 查看任何评论或附件
```

总结：
- 需要做什么
- 验收标准
- 任何障碍或依赖项

### 2. 探索代码库

在编码之前：
- 搜索相关代码
- 理解当前实现
- 识别需要更改的文件

### 3. 创建分支

```bash
git checkout -b {initials}/{工单ID}-简短描述
```

### 4. 实现更改

- 遵循项目模式（检查相关技能）
- 先写测试（TDD）
- 进行增量提交

### 5. 更新工单

工作时：
- 添加带有进度更新的评论
- 更新状态（进行中 → 审查中）
- 记录任何障碍或问题

### 6. 创建 PR 并链接

当准备好时：
- 使用 `gh pr create` 创建 PR
- 将 PR 链接到工单
- 将工单 ID 添加到 PR 标题：`feat(PROJ-123): 描述`

### 7. 如果你发现 Bug

如果在工作过程中发现无关的 Bug：
1. 使用详情创建新的工单
2. 如果相关，将其链接到当前工单
3. 在 PR 描述中注明
4. 继续原始任务

## 示例工作流

```
我：/ticket PROJ-123

Claude：
1. 从 JIRA 获取 PROJ-123...
   标题：添加用户资料头像上传
   描述：用户应该能够上传个人资料图片...
   验收标准：
   - [ ] 资料页面有上传按钮
   - [ ] 支持 JPG/PNG，最大 5MB
   - [ ] 上传时显示加载状态

2. 在代码库中搜索与资料相关的代码...
   找到：src/screens/Profile/ProfileScreen.tsx
   找到：src/components/Avatar/Avatar.tsx

3. 创建分支：cw/PROJ-123-avatar-upload

4. [使用 TDD 方法实现功能]

5. 将 JIRA 状态更新为"审查中"...
   添加评论："实现完成，PR 等待审查"

6. 创建 PR 并链接到 PROJ-123...
   PR #456 已创建：feat(PROJ-123): 在资料中添加头像上传
```