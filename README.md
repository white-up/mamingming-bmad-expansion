# mamingming-bmad-expansion
BMAD6 拓展模块

这个项目是为 BMAD6 开发的一个拓展模块，旨在集成常用的提示词和工作流程，提升开发和测试效率。

## 快速开始

1. **安装模块**: 将此模块加载到您的 BMAD6 环境中
2. **选择功能**: 根据您的需求选择对应的快捷指令
3. **执行操作**: 在代码编辑器中选中相关内容，输入对应指令即可

## 功能模块 (Modules)

### 快捷指令汇总

| 模块名称 | 快捷指令 | 功能说明 | 适用场景 |
|---------|---------|---------|---------|
| **测试规格生成器** | `JL_gen-spec` | 生成包含测试场景、逻辑图解、可执行脚本的全栈测试规格说明书 | 开发/测试阶段，基于代码生成测试文档 |
| **代码逆向分析师** | `JL_analyze-code` | 对遗留代码进行深度剖析，挖掘业务规则并可视化数据流 | 代码审查、重构，理解复杂业务逻辑 |
| **代码守卫** | `JL_review-code` | 基于技术规范进行代码合规性审查，提供重构建议 | 代码提交前审查，确保规范合规 |
| **业务支持知识库结构化专家** | `JL_gen-knowledge` | 将问题和解决方案转换为标准化的知识库条目 | 客服支持，构建可搜索的知识库 |
| **产研设计文档智能专家** | `JL_gen-design` | 生成结构化、无歧义、可落地的产研通用设计文档 | 需求分析、技术方案设计阶段 |

### 1. 测试规格生成器 (Test Spec Generator)

基于代码或日志上下文，自动生成包含测试场景、逻辑图解及可执行脚本的全栈测试规格说明书。

- **功能**: 输出结构化的测试文档，包含：
    - **测试场景 (Test Scenarios)**: 覆盖 Happy Path 和异常/边界情况。
    - **逻辑可视化 (Logic Visualization)**: 使用 Mermaid 流程图展示数据流转。
    - **可执行脚本 (Execution Scripts)**: 提供 cURL、SQL 数据准备/校验、Python 自动化验证脚本。
- **调用指令**: `JL_gen-spec`
- **相关文件**:
    - Agent 定义: `agents/spec-expert.yaml` (ID: `spec-expert`)
    - Workflow 定义: `workflows/gen-spec.yaml`
- **使用方式**:
    在 BMAD 环境中，选中代码片段或提供日志上下文，输入指令 `JL_gen-spec`。系统会确认测试覆盖范围（正常流程/异常场景/边界条件），确认后生成对应的测试规格书。

### 2. 代码逆向分析师 (Code Reverse Analyst)

像 Java 资深架构师一样，对遗留代码进行深度剖析，挖掘业务规则并可视化数据流。

- **功能**: 输出深度代码分析报告，包含：
    - **全景扫描**: 识别输入输出及非功能性注解 (@Transactional 等)。
    - **依赖探测**: 自动识别数据库交互、外部服务 (Feign/Dubbo) 和中间件 (Redis/MQ)。
    - **逻辑与规则**: 像调试器一样推演逻辑，并提取自然语言的业务规则。
    - **可视化**: 自动生成 Mermaid 流程图和推断的 ER 图。
- **调用指令**: `JL_analyze-code`
- **相关文件**:
    - Agent 定义: `agents/code-analyst.yaml` (ID: `code-analyst`)
    - Workflow 定义: `workflows/analyze-code.yaml`
- **使用方式**:
    选中一段 Java 代码 (Controller/Service)，输入 `JL_analyze-code`。系统会询问重点关注方向（业务逻辑/数据流/全景扫描），确认后生成深度分析报告。

### 3. 代码守卫 (Code Guardian)

严格的 Java 代码审查专家，基于 Jlpay 技术规范进行合规性审查。

- **功能**: 自动审查代码规范、架构分层、技术栈合规性及潜在风险。
    - **规范验收**: 强制执行 COLA 分层、MapStruct 转换、MyBatis-Plus 使用等规则。
    - **异味扫描**: 识别大类/长方法、参数过多、空指针风险、事务失效等问题。
    - **重构建议**: 提供 Before/After 代码对比，指导优化。
- **调用指令**: `JL_review-code`
- **相关文件**:
    - Agent 定义: `agents/code-guardian.yaml` (ID: `code-guardian`)
    - Workflow 定义: `workflows/review-code.yaml`
- **使用方式**:
    选中待提交的 Java 代码，输入 `JL_review-code`。系统会确认审查重点（架构合规/安全风险/通用规范），确认后生成合规性审查报告。

### 4. 业务支持知识库结构化专家 (Business Knowledge Architect)

将一线支持人员的问题和解决流程转化为高质量的知识库条目。

- **功能**: 将原始问题和琐碎解决流程转化为结构化知识库条目，包含：
    - **精准标题生成**: 使用【业务模块】标注，包含动作、对象和同义词扩展。
    - **标准QA格式**: 包含问题背景、解决方案步骤和注意事项。
    - **语义搜索优化**: 标题设计确保在语义搜索系统中高匹配度。
- **调用指令**: `JL_gen-knowledge`
- **相关文件**:
    - Agent 定义: `agents/knowledge-expert.yaml` (ID: `knowledge-expert`)
    - Workflow 定义: `workflows/gen-knowledge.yaml`
- **使用方式**:
    输入 `JL_gen-knowledge`，系统将提示准备就绪，然后您提供问题描述和解决方案，系统会自动生成标准化的知识库条目。

### 5. 产研设计文档智能专家 (Product-Research Design Expert)

具备顶尖产品架构和资深研发经验的智能专家，输出结构化、无歧义、可直接落地的产研通用设计文档。

- **功能**: 生成标准化的产研设计文档，连接产品与研发：
    - **双视角覆盖**: 既包含产品关注的业务边界、验收标准，也包含研发关注的技术约束、数据模型。
    - **交互式澄清**: 自动识别模糊需求，引导用户补全关键信息。
    - **标准化输出**: 包含业务流程图、时序图、C4架构图和数据字典。
    - **代码一致性**: 分析现有代码对需求的覆盖情况及扩展性建议。
- **调用指令**: `JL_gen-design`
- **相关文件**:
    - Agent 定义: `agents/design-expert.yaml` (ID: `design-expert`)
    - Workflow 定义: `workflows/gen-design.yaml`
- **使用方式**:
    选中相关代码或提供需求描述，输入 `JL_gen-design`。系统会首先评估信息完整性，必要时请求补充，随后生成完整文档。

## 项目结构

```
mamingming-bmad-expansion/
├── agents/                 # 智能体 (Agent) 定义文件
│   ├── spec-expert.yaml    # 测试规格专家
│   ├── code-analyst.yaml   # 代码逆向分析师
│   ├── code-guardian.yaml  # 代码守卫
│   ├── knowledge-expert.yaml # 业务知识架构师
│   └── design-expert.yaml  # 产研设计专家
├── workflows/              # 工作流 (Workflow) 定义文件
│   ├── gen-spec.yaml       # 测试规格生成工作流
│   ├── analyze-code.yaml   # 代码分析工作流
│   ├── review-code.yaml    # 代码审查工作流
│   ├── gen-knowledge.yaml  # 知识库生成工作流
│   └── gen-design.yaml     # 设计文档生成工作流
├── docs/                   # 生成的文档输出目录
│   ├── specs/             # 测试规格文档
│   ├── knowledge/         # 知识库条目
│   └── designs/           # 产研设计文档
├── module.yaml            # 模块元数据定义
└── README.md              # 项目说明文档
```

### 文件说明

- **`agents/`**: 存放各功能模块的核心AI智能体配置，定义了AI的角色、目标和行为准则
- **`workflows/`**: 定义完整的工作流程，包括触发条件、执行步骤和输出处理
- **`docs/`**: 存放各模块生成的结果文档，支持版本管理和历史追溯
- **`module.yaml`**: BMAD6模块的元数据配置，包含模块信息和依赖声明

## 注意事项

- **环境要求**: 需要 BMAD6 运行环境支持
- **文件权限**: 确保对 `docs/` 目录有写入权限，用于保存生成的结果
- **AI模型**: 各功能模块对AI模型能力有一定要求，建议使用 GPT-4 或同等能力模型
- **自定义配置**: 可根据团队需求修改 `agents/` 和 `workflows/` 中的配置参数

## 更新日志

- **v1.0.0**: 初始版本发布，包含4个核心功能模块
  - 测试规格生成器
  - 代码逆向分析师
  - 代码守卫
  - 业务支持知识库结构化专家
- **v1.1.0**: 新增产研设计文档生成模块
  - 产研设计文档智能专家
- **v1.2.0**: 全面升级交互式工作流
  - 所有工作流增加"需求澄清"步骤
  - 优化指令执行体验，确保任务目标明确
