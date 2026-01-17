---
description: 在目录上运行代码质量检查
allowed-tools: Read, Glob, Grep, Bash(npm:*), Bash(npx:*)
---

# 代码质量审查

审查位置：$ARGUMENTS

## 指令

1. **识别要审查的文件**：
   - 查找目录中所有 `.ts` 和 `.tsx` 文件
   - 排除测试文件和生成的文件

2. **运行自动化检查**：
   ```bash
   npm run lint -- $ARGUMENTS
   npm run typecheck
   ```

3. **手动审查清单**：
   - [ ] 无 TypeScript `any` 类型
   - [ ] 正确的错误处理
   - [ ] 加载状态处理正确
   - [ ] 列表有空状态
   - [ ] 突变有 onError 处理器
   - [ ] 异步操作期间按钮禁用

4. **按严重性组织报告结果**：
   - 严重（必须修复）
   - 警告（应该修复）
   - 建议（可以改进）