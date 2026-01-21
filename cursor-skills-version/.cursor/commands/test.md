# 场景测试生成 (Scenario Test)

## 概述
分析业务逻辑，生成覆盖 Happy/Sad Path 的测试用例及自动化脚本。参考场景测试模板，逐步生成测试内容。

## 执行步骤
1. **初始化检查**: ⚠️ 必须先询问测试目标，禁止直接开始。
2. **测试范围确认**: 确认补充哪部分测试（正常流程、异常流程、边界测试、并发测试等）。
3. **场景概览**: 生成测试场景概览表，列出所有测试场景。
4. **流程介绍**: 介绍业务逻辑与流程，包含 Mermaid 时序图。
5. **脚本生成**: 生成 cURL 和 SQL 准备脚本（在对话框显示，最终写入报告）。
6. **报告写入**: 使用模板整合所有内容，写入完整的测试报告（包含 cURL 和 SQL 脚本）。
7. **Python 测试脚本**: 新建 Python 文件，写入端到端测试脚本。

## 生成产物
- **测试报告**: `jl-skills/generated/test/{date}/Scenario_Test_Case.md`（包含 cURL 和 SQL 脚本）
- **Python 测试脚本**: `jl-skills/generated/test/{date}/scripts/e2e_test.py`

## 快捷指令参数
- `/test [文件路径]`: 为指定文件生成测试。
- `/test api`: 侧重于接口契约测试。
- `/test integration`: 生成端到端集成测试场景。
