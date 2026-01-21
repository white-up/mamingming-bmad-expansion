# Java 代码逆向分析工作流路由器

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-Ship-AnalyzeCode/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<critical>此路由器确定工作流模式并委派给专门的子工作流</critical>

<step n="1" goal="验证工作流并检查输出目录">

<action>检查输出目录是否存在: {inputs.output_dir}</action>
<action>如果目录不存在，创建目录</action>

<action>检查可用的分析资源：
- 当前上下文中的代码
- 用户选中的代码
- 指定的代码文件路径
- 相关设计文档
</action>

<action>统计可用资源并存储为 {{available_resources}}</action>

</step>

<step n="2" goal="检查可恢复性并确定工作流模式">
<critical>智能加载策略：首先检查状态文件，然后再决定模式</critical>

<action>检查是否存在状态文件: {inputs.output_dir}/analyze-state.json</action>

<check if="analyze-state.json 存在">
  <action>读取状态文件，提取: timestamps, mode, current_phase, analysis_target</action>
  <action>计算状态文件年龄 (当前时间 - last_updated)</action>

<ask>我发现了一个进行中的分析状态，来自 {{last_updated}}。

**当前进度:**

- 模式: {{mode}}
- 分析目标: {{analysis_target}}
- 当前阶段: {{current_phase}}
- 用户角色: {{user_role}}

请选择:

1. **从上次中断处继续** - 继续完成代码分析
2. **重新开始** - 归档旧状态并重新分析
3. **查看当前结果** - 查看已完成的分析内容
4. **取消** - 不做任何更改退出

您的选择 [1/2/3/4]:
</ask>

  <check if="用户选择 1">
    <action>设置 resume_mode = true</action>
    <action>设置 workflow_mode = {{mode}}</action>
    <action>加载状态文件中的分析进度</action>
    <action>显示: "恢复分析从 {{current_phase}} 阶段"</action>
    <action>跳转到相应阶段继续执行</action>
  </check>

  <check if="用户选择 2">
    <action>创建归档目录: {inputs.output_dir}/.archive/</action>
    <action>移动旧状态文件到: {inputs.output_dir}/.archive/analyze-state-{{timestamp}}.json</action>
    <action>设置 resume_mode = false</action>
    <action>继续到步骤 3</action>
  </check>

  <check if="用户选择 3">
    <action>显示已完成的分析内容摘要</action>
    <ask>是否继续完成剩余分析？[y/n]</ask>
  </check>

  <check if="用户选择 4">
    <action>显示: "退出工作流，不做任何更改。"</action>
    <action>退出工作流</action>
  </check>

  <check if="状态文件年龄 >= 24 小时">
    <action>显示: "发现旧状态文件 (>24小时)。建议重新开始分析。"</action>
    <ask>是否归档旧状态并重新开始？[y/n]</ask>
  </check>
</check>

<check if="analyze-state.json 不存在">
  <action>设置 resume_mode = false</action>
  <action>继续到步骤 3</action>
</check>

</step>

<step n="3" goal="检查分析上下文并确定模式" if="resume_mode == false">

<action>分析当前上下文中的代码内容</action>

<check if="上下文中有代码">
  <action>识别代码类型、类名、方法和模块</action>
  <action>估算代码行数和复杂度</action>
  
  <output>**代码上下文分析 ✓**

我已识别到以下代码内容：

**文件/模块**: {{identified_files}}
**代码行数**: {{total_lines}} 行
**主要类/接口**: {{main_classes}}
**主要方法**: {{main_methods}}

请选择分析模式:

1. **完整分析** - 全面深度逆向分析（业务逻辑 + 数据流转 + 异常处理 + 状态流转）
2. **快速分析** - 快速了解主要功能和结构（适用于简单代码）
3. **聚焦分析** - 根据您的角色需求定向分析

您的选择 [1/2/3]:</output>

  <check if="用户选择 1">
    <action>设置 workflow_mode = "full_analysis"</action>
    <action>显示: "启动完整分析流程..."</action>
    <action>继续到步骤 4</action>
  </check>
  
  <check if="用户选择 2">
    <action>设置 workflow_mode = "quick_analysis"</action>
    <action>显示: "启动快速分析流程..."</action>
    <action>加载并执行: {installed_path}/workflows/code-analysis-instructions.md 使用快速模式</action>
  </check>
  
  <check if="用户选择 3">
    <action>设置 workflow_mode = "focused_analysis"</action>
    <ask>请选择您的角色:

1. **开发人员** - 重点关注代码逻辑推演、数据流转、性能优化点
2. **架构师** - 重点关注架构设计模式、模块职责划分、系统集成点
3. **测试人员** - 重点关注业务边界、异常场景、测试覆盖点
4. **业务人员** - 重点关注业务规则实现、状态流转、功能验收标准

您的选择 [1/2/3/4]:</ask>
    <action>存储用户角色为 {{user_role}}</action>
    <action>加载并执行: {installed_path}/workflows/context-analysis-instructions.md</action>
  </check>
</check>

<check if="上下文中无代码">
  <output>**等待分析内容 ⚠️**

我已就绪。请提供以下内容之一：
1. 选中您想要分析的 Java 代码
2. 提供代码文件路径
3. 粘贴相关代码片段

并告诉我：
- 您的角色是什么？（开发/架构/测试/业务）
- 您希望重点了解哪些方面？</output>
  <action>等待用户输入</action>
</check>

</step>

<step n="4" goal="确认用户角色和分析重点">

<check if="user_role 未设置">
<ask>为了提供更精准的分析，请告诉我您的角色:

1. **开发人员** - 我将重点分析代码逻辑、数据流转、性能优化点
2. **架构师** - 我将重点分析架构设计、模块职责、系统集成
3. **测试人员** - 我将重点分析业务边界、异常场景、测试覆盖
4. **业务人员** - 我将重点分析业务规则、状态流转、验收标准

您的选择 [1/2/3/4]:</ask>
<action>存储用户角色为 {{user_role}}</action>
</check>

<output>**分析配置确认 ✓**

- **分析目标**: {{analysis_target}}
- **用户角色**: {{user_role}}
- **分析模式**: {{workflow_mode}}
- **重点关注**: {{focus_areas}}

我将开始分析，是否需要调整任何配置？</output>

<action>等待用户确认或调整</action>

</step>

<step n="5" goal="初始化状态并启动上下文分析" if="workflow_mode == full_analysis AND resume_mode == false">

<action>初始化状态文件: {inputs.output_dir}/analyze-state.json</action>
<critical>每次更新状态文件时，记录: 阶段 ID、可读摘要、精确时间戳和任何输出。</critical>

<action>写入初始状态:
{
  "workflow_version": "1.0.0",
  "timestamps": {"started": "{{current_timestamp}}", "last_updated": "{{current_timestamp}}"},
  "mode": "full_analysis",
  "current_phase": "context_analysis",
  "completed_phases": [],
  "analysis_target": "{{analysis_target}}",
  "user_role": "{{user_role}}",
  "focus_areas": {{focus_areas}},
  "findings": {},
  "outputs_generated": ["analyze-state.json"],
  "resume_instructions": "从上下文分析阶段开始"
}
</action>

<action>显示: "✓ 工作流初始化完成，开始上下文分析..."</action>
<action>加载并执行: {installed_path}/workflows/context-analysis-instructions.md</action>

</step>

<step n="6" goal="完成上下文分析后转入代码分析">

<check if="context_analysis_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "context_analysis", "status": "completed", "timestamp": "{{now}}"}
  - 更新 current_phase = "code_analysis"
  - 存储 findings.context
  - 更新 last_updated 时间戳
  </action>
  
  <action>显示: "✓ 上下文分析完成！开始代码深度分析..."</action>
  <action>加载并执行: {installed_path}/workflows/code-analysis-instructions.md</action>
</check>

</step>

<step n="7" goal="完成并提供后续步骤">

<check if="code_analysis_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "code_analysis", "status": "completed", "timestamp": "{{now}}", "output": "{{report_path}}"}
  - 更新 current_phase = "completed"
  - 更新 timestamps.completed = "{{now}}"
  </action>
</check>

<action>运行验证清单: {validation}</action>

<output>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## Java 代码逆向分析工作流完成！✓

**输出位置:** {inputs.output_dir}/

**生成的文档:**
- Analyze_Code_{{timestamp}}.md - 功能分析报告

**分析摘要:**
| 维度 | 分析结果 |
|-----|---------|
| 分析目标 | {{analysis_target}} |
| 用户角色 | {{user_role}} |
| 代码行数 | {{total_lines}} 行 |
| 主要类/接口 | {{main_classes_count}} 个 |
| 识别的业务流程 | {{business_flows_count}} 个 |
| 关键业务规则 | {{business_rules_count}} 条 |

**报告亮点:**
{{#if has_business_flow}}
- ✓ 业务流程图已生成
{{/if}}
{{#if has_state_machine}}
- ✓ 状态流转图已生成
{{/if}}
{{#if has_data_model}}
- ✓ 数据模型分析已完成
{{/if}}

**后续步骤:**

1. 审阅功能分析报告，确认理解准确
2. 如有疑问，可针对特定部分进行追问
3. 基于分析结果，可进行后续工作：
   - 测试用例设计
   - 代码重构优化
   - 文档补充完善

**相关工作流:**
- `JL-Build-Scenario-Test-Case` - 生成场景测试用例
- `JL-Build-ReviewCode` - 代码审查
- `JL-Design-DDD` - DDD 系统设计

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
</output>

<action>显示: "状态文件已保存: {inputs.output_dir}/analyze-state.json"</action>

</step>

</workflow>
