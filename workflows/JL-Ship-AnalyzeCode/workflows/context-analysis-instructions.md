# 上下文分析子工作流

<critical>此子工作流由父工作流 JL-Ship-AnalyzeCode 调用</critical>
<critical>您必须已经加载: {parent_path}/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow phase="context_analysis">

<step n="1" goal="识别代码内容">

<action>分析当前上下文中的代码</action>

<action>识别以下信息:
- 类名和类型（Controller/Service/Repository/Entity/VO/DTO 等）
- 主要方法和功能
- 包路径和模块归属
- 代码行数估算
- 代码复杂度初判
</action>

<action>存储识别结果:
- {{identified_classes}} = 识别的类列表
- {{identified_methods}} = 主要方法列表
- {{code_type}} = 代码类型判断
- {{total_lines}} = 代码行数
</action>

<check if="代码识别成功">
  <output>**代码识别结果 ✓**

我已识别到以下代码内容：

| 属性 | 值 |
|-----|-----|
| **主要类** | {{identified_classes}} |
| **代码类型** | {{code_type}} |
| **代码行数** | {{total_lines}} 行 |
| **主要方法** | {{identified_methods}} |

</output>
</check>

<check if="代码识别失败或不完整">
  <ask>我无法完全识别代码内容。请确认：

1. 您想分析的是哪个类/方法？
2. 是否需要补充更多代码上下文？</ask>
  <action>等待用户补充</action>
</check>

</step>

<step n="2" goal="加载相关文档">

<action>检查 {inputs.doc_path} 路径下的文档</action>

<action>搜索以下类型的相关文档:
- 设计文档（*design*.md, *DDD*.md）
- 需求文档（*prd*.md, *requirements*.md）
- 架构文档（*architecture*.md）
- API 文档（*api*.md）
</action>

<check if="找到相关文档">
  <action>存储相关文档路径为 {{related_docs}}</action>
  
  <output>**相关文档发现 ✓**

我在文档目录中找到了以下相关文档：

{{#each related_docs}}
- {{this.name}} ({{this.path}})
{{/each}}

这些文档将帮助我更准确地理解代码的设计意图和业务背景。</output>

  <action>读取相关文档，提取关键信息:
  - 业务背景和目标
  - 设计约定和规范
  - 领域模型定义
  - 接口契约
  </action>
</check>

<check if="未找到相关文档">
  <output>**文档状态 ⚠️**

未在指定路径找到相关设计文档。我将基于代码本身进行逆向分析。

如果您有相关文档，可以：
1. 提供文档路径
2. 粘贴关键文档内容
3. 描述业务背景

或者，我可以直接基于代码进行分析，是否继续？</output>
  <action>等待用户确认</action>
</check>

</step>

<step n="3" goal="确认分析范围">

<action>综合代码识别结果和文档信息</action>

<output>**分析范围确认**

基于我的分析，这段代码的核心功能是：

**功能概述**: {{function_summary}}

**涉及的业务领域**: {{business_domain}}

**代码结构**:
{{#each identified_classes}}
- `{{this.name}}`: {{this.description}}
{{/each}}

请确认：
1. 我的理解是否正确？
2. 是否需要扩展或缩小分析范围？
3. 您最关心的是哪些方面？</output>

<action>等待用户确认或调整</action>

<check if="用户确认">
  <action>存储确认的分析目标为 {{analysis_target}}</action>
  <action>继续到步骤 4</action>
</check>

<check if="用户要求调整">
  <action>根据用户反馈调整分析范围</action>
  <action>重新确认分析目标</action>
</check>

</step>

<step n="4" goal="确定分析重点">

<check if="user_role 已设置">
  <action>根据用户角色确定分析重点</action>
</check>

<check if="user_role 未设置">
  <ask>为了提供更精准的分析，请告诉我您的角色：

1. **开发人员** 
   - 重点：代码逻辑推演、数据流转、异常处理、性能优化点
   
2. **架构师**
   - 重点：架构设计模式、模块职责划分、系统集成点、技术评估
   
3. **测试人员**
   - 重点：业务逻辑边界、输入输出验证、异常场景、测试覆盖点
   
4. **业务人员**
   - 重点：业务规则实现、状态流转逻辑、业务约束、验收标准

您的选择 [1/2/3/4]:</ask>
  <action>存储用户角色为 {{user_role}}</action>
</check>

<action>根据角色设置分析重点:
- developer: ["代码逻辑推演", "数据流转", "异常处理", "性能优化点"]
- architect: ["架构设计模式", "模块职责划分", "系统集成点", "技术选型评估"]
- tester: ["业务逻辑边界", "输入输出验证", "异常场景", "测试覆盖点"]
- business: ["业务规则实现", "状态流转逻辑", "业务约束条件", "功能验收标准"]
</action>

<action>存储分析重点为 {{focus_areas}}</action>

<output>**分析配置完成 ✓**

| 配置项 | 值 |
|-------|-----|
| 分析目标 | {{analysis_target}} |
| 用户角色 | {{user_role}} |
| 分析重点 | {{focus_areas}} |
| 相关文档 | {{related_docs_count}} 份 |

准备开始深度代码分析...</output>

</step>

<step n="5" goal="生成上下文分析摘要">

<action>编译上下文分析摘要:
{
  "analysis_target": "{{analysis_target}}",
  "identified_classes": {{identified_classes}},
  "identified_methods": {{identified_methods}},
  "code_type": "{{code_type}}",
  "total_lines": {{total_lines}},
  "user_role": "{{user_role}}",
  "focus_areas": {{focus_areas}},
  "related_docs": {{related_docs}},
  "function_summary": "{{function_summary}}",
  "business_domain": "{{business_domain}}"
}
</action>

<action>设置 context_analysis_completed = true</action>
<action>返回控制权给父工作流</action>

</step>

</workflow>
