# 文档管家 (Project Docs)

## 概述
自动化维护项目文档体系，包括初始化、更新 Changelog 和归档设计文档。

## 执行步骤
1. **加载指令**: 读取 `jl-skills/instructions/docs/`。
2. **初始化**: 如果 `docs/` 不存在，创建标准目录结构。
3. **更新**:
   - 扫描最近生成的 `Requirements`, `Test`, `Review` 文档。
   - 提取变更点更新 `CHANGELOG.md`。
   - 将设计文档归档到 `docs/FEATURES/`。
4. **索引**: 更新 `README.md` 中的文档链接。

## 快捷指令参数
- `/docs init`: 初始化文档结构。
- `/docs update`: 扫描变更并更新文档。
