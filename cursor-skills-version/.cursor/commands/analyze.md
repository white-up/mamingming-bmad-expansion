# 代码深度解析 (Code Analysis)

## 概述
帮助开发者快速理解陌生代码库，生成调用链视图和影响面分析。

## 执行步骤
1. **加载指令**: 读取 `jl-skills/instructions/analyze/`。
2. **静态分析**: 识别技术栈、核心实体 (ER图) 和模块结构。
3. **动态追踪**: 绘制核心链路的时序图 (Sequence Diagram)。
4. **影响评估**: (可选) 分析代码变更可能波及的范围。

## 快捷指令参数
- `/analyze flow`: 生成业务流程图。
- `/analyze impact`: 分析变更影响面。
- `/analyze explain`: 用自然语言解释复杂逻辑。
