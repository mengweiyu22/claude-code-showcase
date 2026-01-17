# skill-eval.sh - 技能评估包装脚本

## 文件概述

`skill-eval.sh` 是一个 Bash 包装脚本，用于执行技能评估引擎。它的主要作用是作为 Node.js 脚本的桥梁，确保在 Claude Code 环境中正确运行技能评估功能。

## 主要功能

### 1. 脚本定位
```bash
SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
NODE_SCRIPT="$SCRIPT_DIR/skill-eval.js"
```
- 自动获取脚本所在目录
- 定位到 `skill-eval.js` 文件

### 2. 环境检查
```bash
# 检查 Node.js 是否可用
if ! command -v node &>/dev/null; then
  # Fallback: exit silently if Node.js not found
  exit 0
fi
```
- 检查系统是否安装了 Node.js
- 如果没有 Node.js，静默退出（优雅降级）

### 3. 文件存在性检查
```bash
if [[ ! -f "$NODE_SCRIPT" ]]; then
  exit 0
fi
```
- 确保评估脚本存在
- 如果脚本不存在，静默退出

### 4. 数据传输
```bash
# Pipe stdin to the Node.js script (suppress stderr noise from nvm/shell init)
cat | node "$NODE_SCRIPT" 2>/dev/null
```
- 将标准输入传递给 Node.js 脚本
- 抑制 nvm/shell 初始化的 stderr 噪音

### 5. 确保流程继续
```bash
# Always exit 0 to allow the prompt through
exit 0
```
- 始终以退出码 0 结束
- 保证用户提示不会被阻塞

## 使用场景

### 1. Claude Code Hook
作为 UserPromptSubmit hook 运行：
- 在用户提交提示时自动触发
- 分析提示内容并建议相关技能
- 不会影响正常对话流程

### 2. 环境兼容性
- 支持 macOS/Linux 系统
- 兼容不同的 shell 环境（Bash, Zsh 等）
- 处理 nvm 等 Node 版本管理器的噪音

### 3. 错误处理
- 多层检查确保环境兼容性
- 优雅降级机制
- 不会因为缺少依赖而中断工作流程

## 技术特点

### 1. 路径处理
- 使用 `$(dirname "${BASH_SOURCE[0]}")` 获取当前脚本目录
- 使用 `$(cd ... && pwd)` 确保获得绝对路径
- 跨平台路径处理

### 2. 命令检查
- 使用 `command -v` 检查命令是否存在
- 标准的错误输出重定向到 `/dev/null`

### 3. 数据流控制
- 使用 `cat` 将标准输入传递给 Node.js 脚本
- 抑制不需要的输出噪音
- 保持数据流的完整性

### 4. 安全性
- 静默退出避免错误信息干扰
- 确保系统稳定性
- 不会因为环境问题而影响用户体验

## 调用示例

```bash
# 典型的调用流程
echo '{"prompt": "帮我写一个 React 组件测试"}' | ./skill-eval.sh

# 或者在 Claude Code 中的调用
user-prompt -> skill-eval.sh -> skill-eval.js -> 技能建议输出
```

## 依赖关系

- **skill-eval.js**：核心评估引擎（必须）
- **Node.js**：JavaScript 运行时（必需）
- **skill-rules.json**：技能配置文件（必需）

## 注意事项

1. **Node.js 依赖**：脚本需要 Node.js 环境
2. **脚本路径**：必须与 skill-eval.js 在同一目录
3. **权限**：需要执行权限
4. **错误处理**：静默错误处理可能隐藏问题

## 扩展建议

1. **调试模式**：可以添加调试选项来显示详细日志
2. **配置检查**：可以添加更详细的配置验证
3. **超时处理**：为长时间运行的评估添加超时机制
4. **性能监控**：添加执行时间监控

这个脚本虽然简单，但在 Claude Code 的技能评估系统中起到了关键的作用，确保了在不同环境下的稳定运行。