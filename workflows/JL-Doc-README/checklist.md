# 文档完整性验证清单 (Doc Completeness Checklist)

此清单用于确保 `JL-Doc-README` 工作流生成的文档体系符合标准。

## 1. 目录结构 (Directory Structure)
- [ ] **README.md** 存在于项目根目录。
- [ ] **docs/CHANGELOG/** 目录存在，且包含至少一个版本日志。
- [ ] **docs/INTEGRATION.md** 存在。
- [ ] **docs/ARCHITECTURE.md** 存在。
- [ ] **docs/FEATURES/** 目录存在，且按模块分类。

## 2. 核心内容 (Core Content)
- [ ] **README.md**:
    - [ ] 包含正确的“项目名称”和“核心目标”。
    - [ ] 包含“文档导航”链接，且链接有效。
- [ ] **CHANGELOG**:
    - [ ] 版本号符合 Semantic Versioning (vX.Y.Z)。
    - [ ] 包含“核心变更”和“兼容性影响”分析。
- [ ] **ARCHITECTURE.md**:
    - [ ] 包含 C4 Context 图。
    - [ ] 包含关键架构决策 (ADR)。
- [ ] **FEATURES/{Module}/**:
    - [ ] **SPEC.md** 存在（链接自产研设计文档）。
    - [ ] **TEST_CASES.md** 存在（链接自场景测试报告）。

## 3. 一致性检查 (Consistency)
- [ ] CHANGELOG 中的变更内容与 FEATURES 目录下的新功能一致。
- [ ] 集成指南中的接口描述与最新的 API 契约一致。
