# mamingming-bmad-expansion
BMAD6 拓展模块

这个项目是为 BMAD6 开发的一个拓展模块，旨在集成常用的提示词和工作流程，提升开发和测试效率。

## 功能模块 (Modules)

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
    在 BMAD 环境中，选中代码片段或提供日志上下文，输入指令 `JL_gen-spec`，即可生成对应的测试规格书。

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
    选中一段 Java 代码 (Controller/Service)，输入 `JL_analyze-code`，获取深度分析报告。

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
    选中待提交的 Java 代码，输入 `JL_review-code`，获取合规性审查报告。

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

## 项目结构

- `agents/`: 存放智能体 (Agent) 的定义文件。
- `workflows/`: 存放工作流 (Workflow) 的定义文件。
- `module.yaml`: 模块元数据定义。
