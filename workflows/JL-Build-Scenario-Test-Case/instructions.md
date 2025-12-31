# 场景测试用例生成工作流路由器

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-Build-Scenario-Test-Case/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<critical>此路由器确定工作流模式并委派给专门的子工作流</critical>

<step n="1" goal="验证工作流并检查输出目录">

<action>检查输出目录是否存在: {inputs.output_dir}</action>
<action>如果目录不存在，创建目录</action>

<action>检查可用的输入资源：
- 项目文档: {inputs.req_path}
- 代码路径: {inputs.code_path}
- 日志文件: {inputs.log_path}
</action>

<action>统计可用资源数量并存储为 {{available_resources}}</action>

</step>

<step n="2" goal="检查可恢复性并确定工作流模式">
<critical>智能加载策略：首先检查状态文件，然后再决定模式</critical>

<action>检查是否存在状态文件: {inputs.output_dir}/test-case-state.json</action>

<check if="test-case-state.json 存在">
  <action>读取状态文件，提取: timestamps, mode, current_phase, completed_scenarios</action>
  <action>计算状态文件年龄 (当前时间 - last_updated)</action>

<ask>我发现了一个进行中的工作流状态，来自 {{last_updated}}。

**当前进度:**

- 模式: {{mode}}
- 已完成场景: {{completed_scenarios_count}}/{{total_scenarios}}
- 当前阶段: {{current_phase}}

请选择:

1. **从上次中断处继续** - 继续完成剩余场景
2. **重新开始** - 归档旧状态并重新生成
3. **增量添加** - 在现有报告基础上添加新场景
4. **取消** - 不做任何更改退出

您的选择 [1/2/3/4]:
</ask>

  <check if="用户选择 1">
    <action>设置 resume_mode = true</action>
    <action>设置 workflow_mode = {{mode}}</action>
    <action>加载状态文件中的进度</action>
    <action>显示: "恢复生成从 {{current_phase}} 阶段"</action>
    <action>跳转到相应阶段继续执行</action>
  </check>

  <check if="用户选择 2">
    <action>创建归档目录: {inputs.output_dir}/.archive/</action>
    <action>移动旧状态文件到: {inputs.output_dir}/.archive/test-case-state-{{timestamp}}.json</action>
    <action>设置 resume_mode = false</action>
    <action>继续到步骤 3</action>
  </check>

  <check if="用户选择 3">
    <action>设置 workflow_mode = "incremental"</action>
    <action>加载现有报告作为基础</action>
    <action>显示: "增量模式：将在现有报告基础上添加新场景"</action>
    <action>加载并执行: {installed_path}/workflows/incremental-generation-instructions.md</action>
  </check>

  <check if="用户选择 4">
    <action>显示: "退出工作流，不做任何更改。"</action>
    <action>退出工作流</action>
  </check>

  <check if="状态文件年龄 >= 48 小时">
    <action>显示: "发现旧状态文件 (>48小时)。建议重新开始。"</action>
    <ask>是否归档旧状态并重新开始？[y/n]</ask>
    <check if="y">
      <action>归档旧状态文件</action>
      <action>设置 resume_mode = false</action>
      <action>继续到步骤 3</action>
    </check>
  </check>
</check>

<check if="test-case-state.json 不存在">
  <action>设置 resume_mode = false</action>
  <action>继续到步骤 3</action>
</check>

</step>

<step n="3" goal="检查现有报告并确定工作流模式" if="resume_mode == false">

<action>检查 {inputs.output_dir} 下是否有 Scenario_Test_Case_*.md 文件</action>

<check if="已有测试报告存在">
  <action>显示找到的测试报告列表</action>
  
<ask>我发现了现有的测试报告：

{{existing_reports_list}}

请选择工作流模式:

1. **重新生成** - 覆盖现有报告，重新生成
2. **增量添加** - 在现有报告基础上添加新场景
3. **取消** - 保持现有报告不变

您的选择 [1/2/3]:
</ask>

  <check if="用户选择 1">
    <action>设置 workflow_mode = "full_generation"</action>
    <action>显示: "启动完整生成流程..."</action>
    <action>继续到步骤 4</action>
  </check>
  
  <check if="用户选择 2">
    <action>设置 workflow_mode = "incremental"</action>
    <action>加载最新的测试报告作为基础</action>
    <action>显示: "启动增量生成流程..."</action>
    <action>加载并执行: {installed_path}/workflows/incremental-generation-instructions.md</action>
  </check>
  
  <check if="用户选择 3">
    <action>显示: "保持现有报告不变，退出工作流。"</action>
    <action>退出工作流</action>
  </check>
</check>

<check if="无已有测试报告">
  <action>设置 workflow_mode = "full_generation"</action>
  <action>显示: "未找到现有测试报告，启动完整生成流程..."</action>
  <action>继续到步骤 4</action>
</check>

</step>

<step n="4" goal="初始化状态并启动上下文分析" if="workflow_mode == full_generation AND resume_mode == false">

<action>初始化状态文件: {inputs.output_dir}/test-case-state.json</action>
<critical>每次更新状态文件时，记录: 阶段 ID、可读摘要、精确时间戳和任何输出。模糊描述不可接受。</critical>

<action>写入初始状态:
{
  "workflow_version": "1.0.0",
  "timestamps": {"started": "{{current_timestamp}}", "last_updated": "{{current_timestamp}}"},
  "mode": "full_generation",
  "current_phase": "context_analysis",
  "completed_phases": [],
  "scenarios_identified": [],
  "outputs_generated": ["test-case-state.json"],
  "resume_instructions": "从上下文分析阶段开始"
}
</action>

<action>显示: "✓ 工作流初始化完成，开始上下文分析..."</action>
<action>加载并执行: {installed_path}/workflows/context-analysis-instructions.md</action>

</step>

<step n="5" goal="完成上下文分析后转入场景识别">

<check if="context_analysis_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "context_analysis", "status": "completed", "timestamp": "{{now}}"}
  - 更新 current_phase = "scenario_identification"
  - 更新 last_updated 时间戳
  </action>
  
  <action>显示: "✓ 上下文分析完成！开始场景识别..."</action>
  <action>加载并执行: {installed_path}/workflows/scenario-identification-instructions.md</action>
</check>

</step>

<step n="6" goal="完成场景识别后转入报告生成">

<check if="scenario_identification_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "scenario_identification", "status": "completed", "timestamp": "{{now}}"}
  - 更新 current_phase = "report_generation"
  - 更新 last_updated 时间戳
  </action>
  
  <action>显示: "✓ 场景识别完成！开始生成测试报告..."</action>
  <action>加载并执行: {installed_path}/workflows/report-generation-instructions.md</action>
</check>

</step>

<step n="7" goal="完成并提供后续步骤">

<check if="report_generation_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "report_generation", "status": "completed", "timestamp": "{{now}}", "output": "{{report_path}}"}
  - 更新 current_phase = "completed"
  - 更新 timestamps.completed = "{{now}}"
  </action>
</check>

<action>运行验证清单: {validation}</action>

<action>编译验证回顾变量:
  - 设置 {{scenario_summary}} 为场景统计信息
  - 设置 {{coverage_analysis}} 为覆盖率分析结果
  - 设置 {{next_steps}} 为推荐的下一步操作
</action>

<output>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 场景测试用例生成工作流完成！✓

**输出位置:** {inputs.output_dir}/

**生成的文档:**
{{generated_files_list}}

**场景统计:**
- 正常流程 (P0): {{happy_path_count}} 个
- 异常流程 (P1): {{exception_count}} 个
- 边界测试 (P1): {{boundary_count}} 个
- 其他场景: {{other_count}} 个
- **总计:** {{total_scenarios}} 个测试场景

**覆盖分析:**
{{coverage_analysis}}

**后续步骤:**

1. 审阅生成的测试场景，确认覆盖完整性
2. 根据业务优先级调整测试用例优先级
3. 将测试脚本集成到 CI/CD 流水线
4. 执行测试并记录结果

**相关工作流:**
- `JL-Workflow-Build-ReviewCode` - 代码评审
- `JL-Design-DDD` - DDD 系统设计

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
</output>

<action>显示: "状态文件已保存: {inputs.output_dir}/test-case-state.json"</action>

</step>

</workflow>
