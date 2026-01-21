# 系统迁移 (System Migration)

## 概述
引导遗留系统现代化迁移的全流程。

## 执行步骤
1. **加载指令**: 读取 `jl-skills/instructions/migration/`。
2. **执行流程**:
   - 阶段 1: 范围分析 (Scope)
   - 阶段 2: 黄金测试集 (Golden Master)
   - 阶段 3: 数据库迁移 (DB Migration)
   - 阶段 4: 分层重构 (Refactoring)
   - 阶段 5: E2E 验证 (Verification)

## 快捷指令参数
- `/migration scope`: 仅执行范围分析。
- `/migration golden`: 生成黄金测试集。
- `/migration refactor`: 辅助代码重构。
