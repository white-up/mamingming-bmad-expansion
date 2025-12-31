# mamingming-bmad-expansion
BMAD6 拓展模块 - 分布式系统研发与质量保障工具集

这个项目是为 BMAD6 开发的一个拓展模块，通过集成两个核心 AI 专家（ARCH & QA），提供从需求分析到代码审查的全流程智能化解决方案。

## 核心能力

### 🎯 双专家架构
- **JL-ARCH (架构专家)**: 专注于系统架构设计、DDD 领域建模与逆向工程分析
- **JL-QA (质量专家)**: 专注于代码审查、测试生成与质量保障

### 🔄 全流程覆盖
- **设计阶段**: DDD 领域建模、产研设计文档生成
- **构建阶段**: 代码分析、测试规格生成、代码审查
- **发布阶段**: 知识库条目生成、功能分析报告

## 快速开始

1. **安装模块**: 将此模块加载到您的 BMAD6 环境中
2. **选择场景**: 根据您的研发阶段选择对应的快捷指令
3. **执行操作**: 在代码编辑器中选中相关内容，输入对应指令即可

## 功能模块 (Modules)

### 快捷指令汇总

| 模块名称 | 快捷指令 | 功能说明 | 适用场景 | 执行Agent |
|---------|---------|---------|---------|---------|
| **DDD系统设计** | `JL-Design-DDD` | 基于需求文档，进行DDD领域建模并输出详细设计文档 | 需求分析、技术方案设计阶段 | JL-ARCH |
| **功能分析报告** | `JL-Ship-AnalyzeCode` | 对Java代码进行深度逆向分析，挖掘业务规则并可视化 | 代码理解、重构规划、文档编写 | JL-ARCH |
| **知识库条目生成** | `JL-Ship-GenKnowledge` | 将问题和解决方案转换为标准化的知识库条目 | 客服支持，构建可搜索的知识库 | JL-ARCH |
| **场景测试用例生成** | `JL-Build-Scenario-Test-Case` | 基于代码和日志生成完整的场景测试报告 | 测试设计、质量保障阶段 | JL-QA |
| **代码审查** | `JL-Build-ReviewCode` | 基于技术规范进行深度代码合规性审查 | 代码提交前审查、质量把控 | JL-QA |

### 1. DDD系统设计 (DDD System Design)

基于需求文档，进行DDD领域建模并输出详细设计文档。

- **功能**: 完整的DDD设计流程，包含：
    - **产研需求设计**: 结构化、无歧义、可直接落地的产研通用设计文档
    - **事件风暴建模**: 与用户多轮对话绘制事件风暴图，确保领域模型准确性
    - **DDD建模报告**: 严格遵循DDD规范的完整建模报告
- **调用指令**: `JL-Design-DDD`
- **相关文件**:
    - Agent: `agents/JL-ARCH.agent.yaml`
    - Workflow: `workflows/arch/JL-Workflow-Design-DDD.yaml`
    - Templates: `templates/arch/JL-Template-Requirements-Design.md`, `templates/arch/JL-Template-DDD-Design.md`
- **使用方式**:
    选中需求文档或相关代码，输入 `JL-Design-DDD`。系统会分阶段确认需求边界、生成设计文档、进行事件风暴建模，最终输出完整的DDD建模报告。

### 2. 功能分析报告 (Function Analysis Report)

对Java代码进行深度逆向分析，挖掘业务规则并可视化数据流。

- **功能**: 资深架构师级别的代码分析，包含：
    - **全景业务扫描**: 识别业务逻辑、数据流向、依赖关系
    - **规则提取**: 将代码逻辑转换为自然语言的业务规则
    - **可视化图表**: 自动生成Mermaid流程图和时序图
- **调用指令**: `JL-Ship-AnalyzeCode`
- **相关文件**:
    - Agent: `agents/JL-ARCH.agent.yaml`
    - Workflow: `workflows/arch/JL-Workflow-Ship-AnalyzeCode.yaml`
    - Template: `templates/arch/JL-Template-Analyze-Code.md`
- **使用方式**:
    选中Java代码片段，输入 `JL-Ship-AnalyzeCode`。系统会确认分析重点（业务逻辑/数据流/全景扫描），结合相关文档进行深度分析，生成结构化报告。

### 3. 知识库条目生成 (Knowledge Base Entry Generation)

将问题和解决方案转换为标准化的知识库条目。

- **功能**: 结构化知识库建设，包含：
    - **智能标题生成**: 使用业务模块标注，包含动作、对象和同义词扩展
    - **标准QA格式**: 包含问题背景、解决方案步骤和注意事项
    - **语义搜索优化**: 标题设计确保在语义搜索系统中高匹配度
- **调用指令**: `JL-Ship-GenKnowledge`
- **相关文件**:
    - Agent: `agents/JL-ARCH.agent.yaml`
    - Workflow: `workflows/arch/JL-Workflow-Ship-GenKnowledge.yaml`
    - Template: `templates/arch/JL-Template-Knowledge-Entry.md`
- **使用方式**:
    输入 `JL-Ship-GenKnowledge`，系统会提示准备就绪，然后您提供问题描述和解决方案，系统自动生成标准化的知识库条目。

### 4. 场景测试用例生成 (Scenario Test Case Generation)

基于代码和日志生成完整的场景测试报告。

- **功能**: 全栈测试规格生成，包含：
    - **拓扑还原**: 识别服务组件、数据库表、外部API交互
    - **业务逻辑对齐**: 对比业务描述与日志，找出状态机变更
    - **测试场景覆盖**: Happy Path、边界值、异常场景、安全测试
    - **可执行脚本**: 提供cURL、SQL、Python自动化验证脚本
- **调用指令**: `JL-Build-Scenario-Test-Case`
- **相关文件**:
    - Agent: `agents/JL-QA.agent.yaml`
    - Workflow: `workflows/quality/JL-Workflow-Build-GenTestSpec.yaml`
    - Template: `templates/quality/JL-Template-Scenario-Test-Case.md`
- **使用方式**:
    选中代码片段或提供日志上下文，输入 `JL-Build-Scenario-Test-Case`。系统会确认测试覆盖范围，基于代码逆向工程生成完整的测试规格书。

### 5. 代码审查 (Code Review)

基于技术规范进行深度代码合规性审查。

- **功能**: 严格的质量把控，包含：
    - **规范检查**: 基于Java编码规范和架构设计规范进行审查
    - **异味识别**: 识别代码异味、安全风险、性能问题
    - **重构建议**: 提供具体的改进方案和Before/After对比
- **调用指令**: `JL-Build-ReviewCode`
- **相关文件**:
    - Agent: `agents/JL-QA.agent.yaml`
    - Workflow: `workflows/quality/JL-Workflow-Build-ReviewCode.yaml`
    - Template: `templates/quality/JL-Template-CR.md`
- **使用方式**:
    选中待审查的Java代码，输入 `JL-Build-ReviewCode`。系统会确认审查范围，基于规范进行深度审查，生成详细的审查报告。

## 项目结构

```
mamingming-bmad-expansion/
├── agents/                         # AI专家定义
│   ├── JL-ARCH.agent.yaml         # 架构专家 (JL-AG-ARCH)
│   ├── JL-QA.agent.yaml           # 质量专家 (JL-AG-QA)
│   └── sidecars/                  # 专家知识库
│       ├── arch/                  # 架构相关规范文档
│       │   ├── DDD与可视化规范.md
│       │   └── 架构设计规范.md
│       └── Java编码规范.md        # Java编码规范
├── templates/                      # 输出模板 (按领域分类)
│   ├── arch/                      # 架构领域模板
│   │   ├── JL-Template-Analyze-Code.md      # 代码分析报告模板
│   │   ├── JL-Template-DDD-Design.md        # DDD建模报告模板
│   │   ├── JL-Template-Knowledge-Entry.md   # 知识库条目模板
│   │   └── JL-Template-Requirements-Design.md # 需求设计文档模板
│   └── quality/                   # 质量领域模板
│       ├── JL-Template-CR.md                 # 代码审查报告模板
│       └── JL-Template-Scenario-Test-Case.md # 场景测试用例模板
├── workflows/                      # 工作流定义 (按领域分类)
│   ├── arch/                      # 架构领域工作流
│   │   ├── JL-Workflow-Design-DDD.yaml         # DDD系统设计
│   │   ├── JL-Workflow-Ship-AnalyzeCode.yaml   # 功能分析报告
│   │   └── JL-Workflow-Ship-GenKnowledge.yaml  # 知识库条目生成
│   └── quality/                   # 质量领域工作流
│       ├── JL-Workflow-Build-GenTestSpec.yaml  # 场景测试用例生成
│       └── JL-Workflow-Build-ReviewCode.yaml   # 代码审查
├── docs/                          # 生成文档输出目录
│   ├── designs/                   # 设计文档
│   ├── knowledge/                 # 知识库条目
│   └── specs/                     # 测试规格文档
├── module.yaml                    # 模块元数据定义
└── README.md                      # 项目说明文档
```

### 文件说明

- **`agents/`**: 两个核心AI专家配置
  - `JL-ARCH.agent.yaml`: 架构专家，负责设计、分析、建模等架构相关任务
  - `JL-QA.agent.yaml`: 质量专家，负责测试、审查、质量保障等任务
  - `sidecars/`: 专家的外部知识库，包含各种规范文档

- **`templates/`**: 结构化的输出模板，按领域分类
  - `arch/`: 架构领域模板（设计文档、分析报告、建模报告等）
  - `quality/`: 质量领域模板（测试用例、审查报告等）

- **`workflows/`**: 工作流编排，按领域分类
  - `arch/`: 架构领域工作流（设计、分析、建模等）
  - `quality/`: 质量领域工作流（测试、审查等）

- **`docs/`**: 自动生成的文档输出目录，包含所有产出物

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

## 注意事项

- **环境要求**: 需要 BMAD6 运行环境支持，支持sidecar外部知识库功能
- **文件权限**: 确保对输出目录有写入权限
- **AI模型**: 建议使用 GPT-4 或同等能力模型，特别是对于复杂的设计和分析任务
- **知识库**: sidecars目录下的规范文档可根据团队实际情况调整
- **自定义配置**: 可修改agents、workflows、templates以适应团队特定需求

## 更新日志

- **v2.0.0**: 全新架构重构，采用双专家模式
  - 重构为JL-ARCH（架构专家）和JL-QA（质量专家）双核心架构
  - 重新组织目录结构，按领域分类（arch/quality）
  - 标准化模板和输出格式
  - 增强交互式工作流，支持多轮对话和上下文关联

- **v1.3.0**: 新增 DDD 架构审查模块
  - DDD 领域建模专家
- **v1.2.0**: 全面升级交互式工作流
  - 所有工作流增加"需求澄清"步骤
- **v1.1.0**: 新增产研设计文档生成模块
- **v1.0.0**: 初始版本发布
