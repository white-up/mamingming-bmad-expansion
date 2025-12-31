# mamingming-bmad-expansion
BMAD6 拓展模块 - 分布式系统研发与质量保障工具集

这个项目是为 BMAD6 开发的一个拓展模块，通过集成两个核心 AI 专家（ARCH & QA），提供从需求分析到代码审查的全流程智能化解决方案。

## 核心能力

### 🎯 双专家架构
- **JL-ARCH (架构专家)**: 专注于系统架构设计、DDD 领域建模与逆向工程分析
- **JL-QA (质量专家)**: 专注于代码审查、测试生成与质量保障

### 🔄 全流程覆盖
- **设计阶段 (Design)**: DDD 领域建模、产研设计文档生成
- **构建阶段 (Build)**: 场景测试用例生成、代码审查
- **发布阶段 (Ship)**: 代码逆向分析、知识库条目生成

## 快速开始

1. **安装模块**: 将此模块加载到您的 BMAD6 环境中
2. **选择场景**: 根据您的研发阶段选择对应的快捷指令
3. **执行操作**: 在代码编辑器中选中相关内容，输入对应指令即可

## 功能模块 (Modules)

### 快捷指令汇总

| 模块名称 | 快捷指令 | 功能说明 | 适用场景 | 执行Agent |
|---------|---------|---------|---------|---------|
| **DDD系统设计** | `JL-Design-DDD` | 基于需求文档，进行DDD领域建模并输出详细设计文档 | 需求分析、技术方案设计阶段 | JL-ARCH |
| **代码逆向分析** | `JL-Ship-AnalyzeCode` | 对Java代码进行深度逆向分析，生成功能分析报告 | 代码理解、重构规划、文档编写 | JL-ARCH |
| **知识库条目生成** | `JL-Ship-GenKnowledge` | 将问题和解决方案转换为标准化的知识库条目 | 客服支持，构建可搜索的知识库 | JL-ARCH |
| **场景测试用例生成** | `JL-Build-Scenario-Test-Case` | 基于代码和日志生成完整的场景测试报告 | 测试设计、质量保障阶段 | JL-QA |
| **代码审查** | `JL-Build-ReviewCode` | 基于技术规范进行深度代码合规性审查 | 代码提交前审查、质量把控 | JL-QA |

---

### 1. DDD系统设计 (JL-Design-DDD)

基于需求文档，进行DDD领域建模并输出详细设计文档。

- **功能**: 完整的DDD设计流程，包含：
    - **产研需求设计**: 结构化、无歧义、可直接落地的产研通用设计文档
    - **事件风暴建模**: 与用户多轮对话绘制事件风暴图，确保领域模型准确性
    - **DDD建模报告**: 严格遵循DDD规范的完整建模报告
- **工作流模式**:
    - `full_design` - 完整设计流程（产研设计 → 事件风暴 → DDD建模）
    - `requirements_only` - 仅产研设计
    - `ddd_only` - 仅DDD建模
    - `resume` - 恢复中断的设计
- **调用指令**: `JL-Design-DDD`
- **相关文件**:
    - Agent: `agents/JL-ARCH.agent.yaml`
    - Workflow: `workflows/JL-Design-DDD/`
    - Templates: `templates/JL-Template-Requirements-Design.md`, `templates/JL-Template-DDD-Design.md`
- **使用方式**:
    选中需求文档或相关代码，输入 `JL-Design-DDD`。系统会分阶段确认需求边界、生成设计文档、进行事件风暴建模，最终输出完整的DDD建模报告。

---

### 2. 代码逆向分析 (JL-Ship-AnalyzeCode)

对Java代码进行深度逆向分析，生成功能分析报告。

- **功能**: 资深架构师级别的代码分析，包含：
    - **业务背景分析**: User Story、核心价值识别
    - **业务流程分析**: 流程图、步骤详解、分支路径
    - **业务规则提取**: 校验规则、处理逻辑、状态流转
    - **数据接口分析**: 数据模型、API定义、外部依赖
    - **异常边界分析**: 异常场景、边界条件、风险识别
- **工作流模式**:
    - `full_analysis` - 完整分析
    - `quick_analysis` - 快速分析
    - `focused_analysis` - 聚焦分析（按角色定向）
    - `resume` - 恢复中断的分析
- **分析视角**:
    - 开发人员：代码逻辑推演、数据流转、性能优化点
    - 架构师：架构设计模式、模块职责、系统集成点
    - 测试人员：业务边界、异常场景、测试覆盖点
    - 业务人员：业务规则、状态流转、验收标准
- **调用指令**: `JL-Ship-AnalyzeCode`
- **相关文件**:
    - Agent: `agents/JL-ARCH.agent.yaml`
    - Workflow: `workflows/JL-Ship-AnalyzeCode/`
    - Template: `templates/JL-Template-Analyze-Code.md`
- **使用方式**:
    选中Java代码片段，输入 `JL-Ship-AnalyzeCode`。系统会确认您的角色和分析重点，结合相关文档进行深度分析，生成结构化报告。

---

### 3. 知识库条目生成 (JL-Ship-GenKnowledge)

将问题和解决方案转换为标准化的知识库条目。

- **功能**: 结构化知识库建设，包含：
    - **智能标题生成**: 关键词前置、拒绝口语化、包含特征词、同义词扩展
    - **结构化内容**: 问题背景、解决方案步骤、注意事项
    - **去冗余化**: 剔除客套话和无关废话，动作导向
    - **语义搜索优化**: 标题设计确保在语义搜索系统中高匹配度
- **工作流模式**:
    - `single_entry` - 单条目生成
    - `batch_entry` - 批量生成
    - `refine_entry` - 优化已有条目
    - `resume` - 恢复中断的生成
- **标题生成规则**:
    - 关键词前置：【业务模块】动作 + 对象
    - 拒绝口语化：删除"请问"、"帮我"等
    - 包含特征词：错误码、状态、系统名称
    - 覆盖同义词：（退款失败/拦截发货）
- **调用指令**: `JL-Ship-GenKnowledge`
- **相关文件**:
    - Agent: `agents/JL-ARCH.agent.yaml`
    - Workflow: `workflows/JL-Ship-GenKnowledge/`
    - Template: `templates/JL-Template-Knowledge-Entry.md`
- **使用方式**:
    输入 `JL-Ship-GenKnowledge`，提供问题描述和解决方案，系统自动生成标准化的知识库条目。

---

### 4. 场景测试用例生成 (JL-Build-Scenario-Test-Case)

基于代码和日志生成完整的场景测试报告。

- **功能**: 全栈测试规格生成，包含：
    - **上下文分析**: 识别服务组件、数据库表、外部API交互
    - **场景识别**: Happy Path、边界值、异常场景、安全测试
    - **增量生成**: 支持分批次生成测试用例
    - **可执行脚本**: 提供cURL、SQL、Python自动化验证脚本
- **工作流模式**:
    - `full_generation` - 完整生成
    - `incremental` - 增量生成
    - `resume` - 恢复中断的生成
- **调用指令**: `JL-Build-Scenario-Test-Case`
- **相关文件**:
    - Agent: `agents/JL-QA.agent.yaml`
    - Workflow: `workflows/JL-Build-Scenario-Test-Case/`
    - Template: `templates/JL-Template-Scenario-Test-Case.md`
- **使用方式**:
    选中代码片段或提供日志上下文，输入 `JL-Build-Scenario-Test-Case`。系统会确认测试覆盖范围，基于代码逆向工程生成完整的测试规格书。

---

### 5. 代码审查 (JL-Build-ReviewCode)

基于技术规范进行深度代码合规性审查。

- **功能**: 严格的质量把控，包含：
    - **代码规约检查**: Java命名规范、异常处理、日志规范、注释规范
    - **架构设计检查**: 封装性、聚合边界、领域逻辑、值对象使用
    - **安全性检查**: OWASP Top 10、输入验证、敏感数据处理
    - **代码质量检查**: 单元测试、复杂度、重复代码、性能隐患
- **工作流模式**:
    - `full_review` - 完整审查（四维度全覆盖）
    - `quick_review` - 快速审查（关键问题检查）
    - `focused_review` - 聚焦审查（特定领域深度审查）
    - `resume` - 恢复中断的审查
- **问题严重程度**:
    - 🔴 严重问题 (Critical): 必须修复，阻塞合并
    - 🟠 主要问题 (Major): 应该修复，影响质量
    - 🟡 次要问题 (Minor): 建议修复，代码改进
    - 🔵 提示信息 (Info): 可选优化，最佳实践
- **调用指令**: `JL-Build-ReviewCode`
- **相关文件**:
    - Agent: `agents/JL-QA.agent.yaml`
    - Workflow: `workflows/JL-Build-ReviewCode/`
    - Template: `templates/JL-Template-CR.md`
- **使用方式**:
    选中待审查的Java代码，输入 `JL-Build-ReviewCode`。系统会确认审查范围和模式，基于规范进行深度审查，生成详细的审查报告（含评分）。

---

## 项目结构

```
mamingming-bmad-expansion/
├── agents/                              # AI专家定义
│   ├── JL-ARCH.agent.yaml              # 架构专家 (JL-AG-ARCH)
│   ├── JL-QA.agent.yaml                # 质量专家 (JL-AG-QA)
│   └── sidecar/                        # 专家共享知识库
│       ├── DDD与可视化规范.md
│       ├── Java编码规范.md
│       └── 架构设计规范.md
├── templates/                           # 输出模板
│   ├── JL-Template-Analyze-Code.md     # 功能分析报告模板
│   ├── JL-Template-CR.md               # 代码审查报告模板
│   ├── JL-Template-DDD-Design.md       # DDD建模报告模板
│   ├── JL-Template-Knowledge-Entry.md  # 知识库条目模板
│   ├── JL-Template-Requirements-Design.md  # 产研设计文档模板
│   └── JL-Template-Scenario-Test-Case.md   # 场景测试用例模板
├── workflows/                           # 工作流定义 (模块化目录结构)
│   ├── JL-Design-DDD/                  # DDD系统设计工作流
│   │   ├── workflow.yaml               # 主配置
│   │   ├── instructions.md             # 路由器指令
│   │   ├── checklist.md                # 验证清单
│   │   └── workflows/                  # 子工作流
│   │       ├── requirements-design-instructions.md
│   │       ├── event-storming-instructions.md
│   │       └── ddd-modeling-instructions.md
│   ├── JL-Build-ReviewCode/            # 代码审查工作流
│   │   ├── workflow.yaml
│   │   ├── instructions.md
│   │   ├── checklist.md
│   │   └── workflows/
│   │       ├── code-compliance-instructions.md
│   │       ├── architecture-review-instructions.md
│   │       ├── security-review-instructions.md
│   │       └── report-generation-instructions.md
│   ├── JL-Build-Scenario-Test-Case/    # 场景测试用例工作流
│   │   ├── workflow.yaml
│   │   ├── instructions.md
│   │   ├── checklist.md
│   │   └── workflows/
│   │       ├── context-analysis-instructions.md
│   │       ├── scenario-identification-instructions.md
│   │       └── incremental-generation-instructions.md
│   ├── JL-Ship-AnalyzeCode/            # 代码逆向分析工作流
│   │   ├── workflow.yaml
│   │   ├── instructions.md
│   │   ├── checklist.md
│   │   └── workflows/
│   │       ├── context-analysis-instructions.md
│   │       └── code-analysis-instructions.md
│   └── JL-Ship-GenKnowledge/           # 知识库条目生成工作流
│       ├── workflow.yaml
│       ├── instructions.md
│       ├── checklist.md
│       └── workflows/
│           └── knowledge-generation-instructions.md
├── example/                             # 示例文件
│   └── document-project/               # 文档项目示例
├── module.yaml                          # 模块元数据定义
└── README.md                            # 项目说明文档
```

### 工作流目录结构说明

每个工作流采用统一的模块化目录结构：

```
JL-{Phase}-{Name}/
├── workflow.yaml           # 主工作流配置（元数据、输入输出、模式定义）
├── instructions.md         # 路由器指令（模式选择、状态恢复、流程编排）
├── checklist.md            # 验证清单（质量检查、完成标准）
└── workflows/              # 子工作流目录
    ├── {phase1}-instructions.md
    ├── {phase1}.yaml
    ├── {phase2}-instructions.md
    └── {phase2}.yaml
```

### 文件说明

- **`agents/`**: 两个核心AI专家配置
  - `JL-ARCH.agent.yaml`: 架构专家，负责设计、分析、建模等架构相关任务
  - `JL-QA.agent.yaml`: 质量专家，负责测试、审查、质量保障等任务
  - `sidecar/`: 专家的共享知识库，包含各种规范文档

- **`templates/`**: 结构化的输出模板，确保输出一致性

- **`workflows/`**: 模块化工作流目录
  - 每个工作流独立目录，包含完整的配置、指令和验证
  - 支持状态恢复、多模式选择、增量执行

- **`module.yaml`**: BMAD6模块的元数据配置

## 技术特性

### 🤖 双专家架构
- **JL-ARCH**: 崇尚DDD的系统架构师，擅长可视化建模与逆向工程
- **JL-QA**: 严谨的质量保障工程师，基于"Trust, but Verify"原则

### 📋 标准化输出
- **模板化**: 所有输出严格遵循预定义模板，确保一致性
- **可视化**: 自动生成Mermaid图表，提升文档可读性
- **结构化**: 清晰的文档层次，便于团队协作

### 🔄 交互式工作流
- **需求澄清**: 每个工作流第一步都进行上下文评估和需求确认
- **多轮对话**: 复杂任务支持与用户的多轮交互（如事件风暴建模）
- **上下文感知**: 自动关联相关文档，提高分析准确性

### 💾 状态管理
- **可恢复**: 所有工作流支持中断恢复，通过状态文件保存进度
- **多模式**: 支持完整/快速/聚焦等多种执行模式
- **增量执行**: 支持分阶段、分批次执行

## 注意事项

- **环境要求**: 需要 BMAD6 运行环境支持，支持sidecar外部知识库功能
- **文件权限**: 确保对输出目录有写入权限
- **AI模型**: 建议使用 GPT-4 或同等能力模型，特别是对于复杂的设计和分析任务
- **知识库**: sidecar目录下的规范文档可根据团队实际情况调整
- **自定义配置**: 可修改agents、workflows、templates以适应团队特定需求

## 更新日志

- **v3.0.0**: 工作流模块化重构
  - 采用模块化目录结构，每个工作流独立目录
  - 新增状态管理和恢复机制
  - 新增多模式支持（完整/快速/聚焦/恢复）
  - 新增验证清单（checklist.md）确保输出质量
  - 支持子工作流编排和增量执行
  - 代码逆向分析支持角色视角（开发/架构/测试/业务）
  - 知识库条目生成支持批量处理和条目优化
  - 代码审查支持四维度评分（规约/架构/安全/质量）

- **v2.0.0**: 全新架构重构，采用双专家模式
  - 重构为JL-ARCH（架构专家）和JL-QA（质量专家）双核心架构
  - 重新组织目录结构，按领域分类
  - 标准化模板和输出格式
  - 增强交互式工作流，支持多轮对话和上下文关联

- **v1.3.0**: 新增 DDD 架构审查模块
- **v1.2.0**: 全面升级交互式工作流
- **v1.1.0**: 新增产研设计文档生成模块
- **v1.0.0**: 初始版本发布
