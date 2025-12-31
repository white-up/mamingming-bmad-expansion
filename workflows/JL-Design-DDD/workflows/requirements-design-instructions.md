# 产研统一设计子工作流

<workflow>

<critical>此工作流执行产研统一设计文档生成（阶段 1）</critical>
<critical>调用者: ../JL-Design-DDD/instructions.md 路由器</critical>
<critical>处理: requirements_design 阶段</critical>

<step n="1.1" goal="加载上下文资源">

<action>加载以下资源：
- 当前的上下文
- 用户的补充输入
- {inputs.req_path} 需求描述文档
- {templates.requirements_template} 产研通用需求设计文档模板
</action>

<check if="resume_mode == true">
  <action>加载之前的设计进度</action>
  <action>显示: "检测到之前的设计进度，将从上次中断处继续..."</action>
  <action>跳转到相应的步骤</action>
</check>

<action>评估资源内容是否足够，可以明确需求的边界和范围</action>

<check if="包含足够的需求信息">
  <output>**资源加载完成 ✓**

我已阅读并理解以下内容：
- 需求文档: {{req_doc_summary}}
- 业务背景: {{business_context_summary}}
- 核心功能: {{core_features_summary}}

我已准备好开始产研设计。请确认是否继续？[y/n]</output>
</check>

<check if="需求信息不足">
  <output>**需求信息不足 ⚠️**

当前可用信息：
{{available_info_summary}}

缺失的关键信息：
{{missing_info_list}}

请提供以下内容之一：
1. 完整的 PRD 文档路径
2. 需求描述文本
3. 相关代码路径（用于分析现有实现）

或者使用 BMAD 流程先完成 PRD 文档。</output>
  <action>等待用户输入</action>
</check>

</step>

<step n="1.2" goal="等待用户确认需求">

<action>等待用户确认或补充信息</action>

<check if="用户提供了额外信息">
  <action>整合新信息到上下文中</action>
  <action>更新 {{context_summary}}</action>
</check>

<check if="用户确认继续">
  <action>继续到步骤 1.3</action>
</check>

</step>

<step n="1.3" goal="生成数据字典和业务规则">
<critical>优先提取数据字典和业务规则，这是后续所有内容的基础</critical>

<action>从需求文档中提取所有业务和技术术语</action>
<action>创建数据字典：
| 术语 | 含义 | 属性名称 |
|-----|------|---------|
</action>

<action>提取所有业务规则并分类汇总：
| 相关功能 | 业务规则 | 描述 |
|---------|---------|-----|
</action>

<output>**数据字典和业务规则草稿 ✓**

**数据字典** ({{term_count}} 个术语):
{{data_dictionary_preview}}

**业务规则** ({{rule_count}} 条规则):
{{business_rules_preview}}

请审核并确认，或提出修改意见：</output>

<action>等待用户确认</action>

<check if="用户有修改意见">
  <action>根据反馈更新数据字典和业务规则</action>
  <action>重新展示更新后的内容</action>
</check>

</step>

<step n="1.4" goal="生成基本信息和需求细化">

<action>基于数据字典和业务规则，生成产研设计文档的以下部分：

## 基本信息（快速对齐核心）
- 业务背景
- 核心诉求（使用「动词+对象+目标」格式）
- 边界定义（In-Scope / Out-of-Scope）
- 依赖条件
- 利益攸关者分析
- 数据字典（来自步骤 1.3）

## 需求细化
### 业务流程细化
- 主业务流程（使用 Mermaid 绘制流程图和时序图）
- 分支业务流程

### 功能清单
- 功能点描述表格

### 用户故事梳理
- 用户故事列表

### 业务信息汇总
- 业务规则汇总（来自步骤 1.3）
- 关键业务信息
</action>

<critical>所有后续内容必须引用数据字典中的术语，保持一致性</critical>

<output>**需求细化文档草稿 ✓**

已生成以下内容：
- 基本信息部分
- 主业务流程图
- 主业务时序图
- 分支流程表
- 功能清单
- 用户故事

请审核关键内容：

**核心诉求**: {{core_demand}}

**主业务流程图**:
```mermaid
{{main_flow_diagram}}
```

**功能清单预览**:
{{feature_list_preview}}

是否需要修改？[y/n/具体修改意见]</output>

<action>等待用户审核</action>

<check if="用户有修改意见">
  <action>根据反馈修改相应内容</action>
  <action>重新展示更新后的部分</action>
</check>

</step>

<step n="1.5" goal="分析现有代码覆盖情况">

<action>检查是否有现有代码可供分析</action>

<check if="存在相关代码路径">
  <action>扫描代码库，分析现有实现</action>
  <action>生成功能实现部分：
  
## 功能实现
### 当前系统架构
- 系统上下文（使用 C4 模型图）

### 代码对需求的覆盖情况
- 已实现功能
- 未实现功能

### 关键功能实现方案
- 功能实现方案
- 数据库设计

### 代码可维护性与扩展性分析
- 代码结构
- 可维护性问题
- 扩展性建议
  </action>
</check>

<check if="无现有代码">
  <action>生成功能实现框架部分：

## 功能实现
### 当前系统架构
- 系统上下文（待实现，使用 C4 模型图）

### 关键功能实现方案
- 功能实现方案（初步设计）
- 数据库设计（初步设计）
  </action>
</check>

<output>**功能实现分析完成 ✓**

{{#if has_existing_code}}
**代码覆盖分析**:
- 已实现: {{implemented_features_count}} 个功能
- 未实现: {{unimplemented_features_count}} 个功能
{{else}}
**注**: 无现有代码，已生成初步实现框架
{{/if}}

**系统上下文图**:
```mermaid
{{c4_context_diagram}}
```

是否继续？[y/n]</output>

</step>

<step n="1.6" goal="整合并输出完整文档">

<action>将所有部分整合为完整的产研统一设计文档</action>
<action>执行自我审阅（Post-Check）：
- 检查是否符合模板的所有二级和三级标题
- 检查术语一致性
- 检查 Mermaid 图表语法正确性
- 验证精简准确的目标
</action>

<action>生成完整文档内容，以"# 产研通用需求设计报告"作为开头</action>

<output>**产研统一设计文档生成完成 ✓**

文档结构预览：
{{document_structure_preview}}

**自我审阅结果**:
- 标题结构: {{structure_check}}
- 术语一致性: {{terminology_check}}
- 图表语法: {{diagram_check}}

准备保存文档。确认保存？[y/n]</output>

</step>

<step n="1.7" goal="保存文档并更新状态">

<action>生成时间戳: {{timestamp}}</action>
<action>保存文档到: {inputs.output_dir}/Requirements_Design_{{timestamp}}.md</action>
<action>验证文档保存成功</action>

<action>更新状态文件:
- 添加到 completed_phases: {"phase": "requirements_design_doc", "status": "completed", "timestamp": "{{now}}", "output": "Requirements_Design_{{timestamp}}.md"}
- 更新 findings.requirements_summary 为高级摘要
- 更新 last_updated 时间戳
</action>

<output>**✓ 产研统一设计文档已保存**

**文件位置**: {inputs.output_dir}/Requirements_Design_{{timestamp}}.md

**文档摘要**:
- 功能点数量: {{feature_count}}
- 用户故事数量: {{story_count}}
- 业务规则数量: {{rule_count}}

是否需要修改文档？[y/修改意见/继续]</output>

<action>等待用户最终确认</action>

<check if="用户要求修改">
  <action>根据反馈修改文档</action>
  <action>重新保存文档（覆盖原文件）</action>
  <action>重新展示确认信息</action>
</check>

<check if="用户确认继续">
  <action>设置 requirements_design_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
