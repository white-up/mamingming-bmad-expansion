# 代码审查工作流路由器

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-Build-ReviewCode/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<critical>此路由器确定工作流模式并委派给专门的子工作流</critical>

<step n="1" goal="验证工作流并检查输出目录">

<action>检查输出目录是否存在: {inputs.output_dir}</action>
<action>如果目录不存在，创建目录</action>

<action>检查可用的审查资源：
- 当前上下文中的代码
- 用户选中的代码
- 未提交的代码变更
- Git 提交记录
</action>

<action>统计可用资源并存储为 {{available_resources}}</action>

</step>

<step n="2" goal="检查可恢复性并确定工作流模式">
<critical>智能加载策略：首先检查状态文件，然后再决定模式</critical>

<action>检查是否存在状态文件: {inputs.output_dir}/review-state.json</action>

<check if="review-state.json 存在">
  <action>读取状态文件，提取: timestamps, mode, current_phase, reviewed_files</action>
  <action>计算状态文件年龄 (当前时间 - last_updated)</action>

<ask>我发现了一个进行中的审查状态，来自 {{last_updated}}。

**当前进度:**

- 模式: {{mode}}
- 已审查文件: {{reviewed_files_count}}/{{total_files}}
- 当前阶段: {{current_phase}}
- 已发现问题: {{issues_count}} 个

请选择:

1. **从上次中断处继续** - 继续完成剩余文件审查
2. **重新开始** - 归档旧状态并重新审查
3. **查看当前结果** - 查看已完成的审查结果
4. **取消** - 不做任何更改退出

您的选择 [1/2/3/4]:
</ask>

  <check if="用户选择 1">
    <action>设置 resume_mode = true</action>
    <action>设置 workflow_mode = {{mode}}</action>
    <action>加载状态文件中的审查进度</action>
    <action>显示: "恢复审查从 {{current_phase}} 阶段"</action>
    <action>跳转到相应阶段继续执行</action>
  </check>

  <check if="用户选择 2">
    <action>创建归档目录: {inputs.output_dir}/.archive/</action>
    <action>移动旧状态文件到: {inputs.output_dir}/.archive/review-state-{{timestamp}}.json</action>
    <action>设置 resume_mode = false</action>
    <action>继续到步骤 3</action>
  </check>

  <check if="用户选择 3">
    <action>显示已完成的审查结果摘要</action>
    <ask>是否继续完成剩余审查？[y/n]</ask>
  </check>

  <check if="用户选择 4">
    <action>显示: "退出工作流，不做任何更改。"</action>
    <action>退出工作流</action>
  </check>

  <check if="状态文件年龄 >= 24 小时">
    <action>显示: "发现旧状态文件 (>24小时)。建议重新开始审查。"</action>
    <ask>是否归档旧状态并重新开始？[y/n]</ask>
  </check>
</check>

<check if="review-state.json 不存在">
  <action>设置 resume_mode = false</action>
  <action>继续到步骤 3</action>
</check>

</step>

<step n="3" goal="检查审查上下文并确定模式" if="resume_mode == false">

<action>分析当前上下文中的代码内容</action>

<check if="上下文中有代码或文档">
  <action>识别代码类型、模块和范围</action>
  <action>估算代码行数和复杂度</action>
  
  <output>**代码上下文分析 ✓**

我已识别到以下代码内容：

**文件/模块**: {{identified_files}}
**代码行数**: {{total_lines}} 行
**主要类/接口**: {{main_classes}}

请选择审查模式:

1. **完整审查** - 全面深度审查（代码规约 + 架构设计 + 安全性 + 质量）
2. **快速审查** - 快速检查关键问题（适用于小改动 < 200 行）
3. **聚焦审查** - 针对特定领域深度审查

您的选择 [1/2/3]:</output>

  <check if="用户选择 1">
    <action>设置 workflow_mode = "full_review"</action>
    <action>显示: "启动完整审查流程..."</action>
    <action>继续到步骤 4</action>
  </check>
  
  <check if="用户选择 2">
    <action>设置 workflow_mode = "quick_review"</action>
    <action>显示: "启动快速审查流程..."</action>
    <action>加载并执行: {installed_path}/workflows/quick-review-instructions.md</action>
  </check>
  
  <check if="用户选择 3">
    <action>设置 workflow_mode = "focused_review"</action>
    <ask>请选择聚焦审查的领域:
1. 代码规约检查
2. 架构设计检查
3. 安全性检查
4. 代码质量检查

您的选择 [1/2/3/4]:</ask>
    <action>存储聚焦领域为 {{focus_area}}</action>
    <action>加载并执行: {installed_path}/workflows/focused-review-instructions.md</action>
  </check>
</check>

<check if="上下文中无代码">
  <output>**等待审查内容 ⚠️**

我已就绪。请提供以下内容之一：
1. 选中您想要审查的 Java 代码
2. 提供代码文件路径
3. 指定 Git 提交范围（如 HEAD~3..HEAD）

告诉我您希望我重点检查哪些方面？</output>
  <action>等待用户输入</action>
</check>

</step>

<step n="4" goal="初始化状态并启动完整审查" if="workflow_mode == full_review AND resume_mode == false">

<action>初始化状态文件: {inputs.output_dir}/review-state.json</action>
<critical>每次更新状态文件时，记录: 阶段 ID、可读摘要、精确时间戳和任何输出。</critical>

<action>写入初始状态:
{
  "workflow_version": "1.0.0",
  "timestamps": {"started": "{{current_timestamp}}", "last_updated": "{{current_timestamp}}"},
  "mode": "full_review",
  "current_phase": "code_compliance",
  "completed_phases": [],
  "files_to_review": {{files_list}},
  "reviewed_files": [],
  "findings": {"critical": [], "major": [], "minor": [], "info": []},
  "scores": {},
  "outputs_generated": ["review-state.json"],
  "resume_instructions": "从代码规约检查阶段开始"
}
</action>

<action>显示: "✓ 工作流初始化完成，开始代码规约检查..."</action>
<action>加载并执行: {installed_path}/workflows/code-compliance-instructions.md</action>

</step>

<step n="5" goal="完成代码规约检查后转入架构设计检查">

<check if="code_compliance_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "code_compliance", "status": "completed", "timestamp": "{{now}}", "score": {{compliance_score}}}
  - 更新 current_phase = "architecture_review"
  - 存储 findings.compliance
  - 更新 last_updated 时间戳
  </action>
  
  <action>显示: "✓ 代码规约检查完成！得分: {{compliance_score}}/100"</action>
  <action>显示: "开始架构设计检查..."</action>
  <action>加载并执行: {installed_path}/workflows/architecture-review-instructions.md</action>
</check>

</step>

<step n="6" goal="完成架构设计检查后转入安全性检查">

<check if="architecture_review_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "architecture_review", "status": "completed", "timestamp": "{{now}}", "score": {{architecture_score}}}
  - 更新 current_phase = "security_review"
  - 存储 findings.architecture
  - 更新 last_updated 时间戳
  </action>
  
  <action>显示: "✓ 架构设计检查完成！得分: {{architecture_score}}/100"</action>
  <action>显示: "开始安全性检查..."</action>
  <action>加载并执行: {installed_path}/workflows/security-review-instructions.md</action>
</check>

</step>

<step n="7" goal="完成安全性检查后转入报告生成">

<check if="security_review_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "security_review", "status": "completed", "timestamp": "{{now}}", "score": {{security_score}}}
  - 更新 current_phase = "report_generation"
  - 存储 findings.security
  - 更新 last_updated 时间戳
  </action>
  
  <action>显示: "✓ 安全性检查完成！得分: {{security_score}}/100"</action>
  <action>显示: "开始生成审查报告..."</action>
  <action>加载并执行: {installed_path}/workflows/report-generation-instructions.md</action>
</check>

</step>

<step n="8" goal="完成并提供后续步骤">

<check if="report_generation_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "report_generation", "status": "completed", "timestamp": "{{now}}", "output": "{{report_path}}"}
  - 更新 current_phase = "completed"
  - 更新 timestamps.completed = "{{now}}"
  </action>
</check>

<action>计算总体评分:
- 代码规约 (30%): {{compliance_score}}
- 架构设计 (30%): {{architecture_score}}
- 安全性 (20%): {{security_score}}
- 代码质量 (20%): {{quality_score}}
- **总分**: {{total_score}}/100
</action>

<action>确定审查结论:
- >= 80: Pass (通过)
- 60-79: Conditional Pass (有条件通过)
- < 60: Fail (不通过)
</action>

<action>运行验证清单: {validation}</action>

<output>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 代码审查工作流完成！✓

**输出位置:** {inputs.output_dir}/

**总体评分:** {{total_score}}/100 ({{review_result}})

**各维度得分:**
| 维度 | 得分 | 权重 |
|-----|-----|-----|
| 代码规约 | {{compliance_score}} | 30% |
| 架构设计 | {{architecture_score}} | 30% |
| 安全性 | {{security_score}} | 20% |
| 代码质量 | {{quality_score}} | 20% |

**问题统计:**
- 🔴 严重问题: {{critical_count}} 个
- 🟠 主要问题: {{major_count}} 个
- 🟡 次要问题: {{minor_count}} 个
- 🔵 提示信息: {{info_count}} 个

**审查结论:** {{conclusion}}

**后续步骤:**
{{#if has_critical}}
1. ⚠️ **必须修复** {{critical_count}} 个严重问题后再合并
{{/if}}
{{#if has_major}}
2. 建议修复 {{major_count}} 个主要问题
{{/if}}
3. 参考详细报告进行代码改进
4. 修复后可重新运行审查验证

**相关工作流:**
- `JL-Build-Scenario-Test-Case` - 生成测试用例
- `JL-Design-DDD` - DDD 系统设计

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
</output>

<action>显示: "状态文件已保存: {inputs.output_dir}/review-state.json"</action>

</step>

</workflow>
