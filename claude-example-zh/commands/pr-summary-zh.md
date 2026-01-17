---
description: 为当前分支更改生成摘要
allowed-tools: Bash(git:*)
---

# PR 摘要

为当前分支生成拉取请求摘要。

## 指令

1. **分析更改**：
   ```bash
   git log main..HEAD --oneline
   git diff main...HEAD --stat
   ```

2. **生成摘要**，包含：
   - 更改的简要描述
   - 修改的文件列表
   - 任何破坏性更改
   - 测试说明

3. **格式化为 PR 正文**：
   ```markdown
   ## 概述
   [1-3 个要点描述更改]

   ## 更改
   - [重要更改列表]

   ## 测试计划
   - [ ] [测试清单项目]
   ```