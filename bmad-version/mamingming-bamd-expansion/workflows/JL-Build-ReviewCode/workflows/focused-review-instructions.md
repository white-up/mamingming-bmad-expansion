# 聚焦审查子工作流

<workflow>

<critical>此工作流执行聚焦代码审查</critical>
<critical>调用者: ../JL-Build-ReviewCode/instructions.md 路由器</critical>
<critical>处理: focused_review 模式</critical>
<critical>针对特定领域进行深度审查</critical>

<step n="F.1" goal="确认聚焦领域">

<action>根据用户选择确定聚焦领域：
1. 代码规约检查
2. 架构设计检查
3. 安全性检查
4. 代码质量检查
</action>

<output>**聚焦审查模式 🎯**

**选定领域**: {{focus_area}}

将对该领域进行深度审查，其他领域仅做快速扫描。

开始聚焦审查？[y/n]</output>

</step>

<step n="F.2" goal="执行聚焦审查">

<check if="focus_area == 代码规约">
  <action>执行深度代码规约检查</action>
  <action>加载并执行: {installed_path}/workflows/code-compliance-instructions.md</action>
</check>

<check if="focus_area == 架构设计">
  <action>执行深度架构设计检查</action>
  <action>加载并执行: {installed_path}/workflows/architecture-review-instructions.md</action>
</check>

<check if="focus_area == 安全性">
  <action>执行深度安全性检查</action>
  <action>加载并执行: {installed_path}/workflows/security-review-instructions.md</action>
</check>

<check if="focus_area == 代码质量">
  <action>执行深度代码质量检查：
  - 单元测试覆盖
  - 代码复杂度
  - 重复代码检测
  - 性能隐患
  </action>
</check>

</step>

<step n="F.3" goal="其他领域快速扫描">

<action>对非聚焦领域进行快速扫描</action>
<action>仅检查严重问题</action>

<output>**其他领域快速扫描 ⚡**

{{#each other_areas}}
### {{area_name}}
- 状态: {{status}}
- 严重问题: {{critical_count}}
{{/each}}

继续生成聚焦报告？[y/确认]</output>

</step>

<step n="F.4" goal="生成聚焦审查报告">

<action>生成聚焦审查报告</action>

<output>**聚焦审查结果 📝**

# 代码审查报告 (聚焦模式)

> **日期**: {{timestamp}}
> **模式**: 聚焦审查 - {{focus_area}}
> **聚焦领域得分**: {{focus_score}}/100

## 审查摘要

**聚焦领域**: {{focus_area}}
**审查深度**: 深度审查

## {{focus_area}} 详细检查

{{focus_area_detailed_results}}

## 其他领域快速扫描

| 领域 | 状态 | 严重问题 |
|-----|-----|---------|
{{#each other_areas}}
| {{name}} | {{status}} | {{critical}} |
{{/each}}

## 详细发现

### 🔴 严重问题
{{#each critical_issues}}
* Line {{line}}: {{description}}
{{/each}}

### 🟠 主要问题
{{#each major_issues}}
* Line {{line}}: {{description}}
{{/each}}

## 建议

{{focused_suggestions}}

保存报告？[y/n]</output>

<action>保存报告到: {inputs.output_dir}/CR_Focused_{{focus_area}}_{{timestamp}}.md</action>

<check if="用户确认">
  <action>设置 report_generation_completed = true</action>
  <action>返回主工作流路由器完成工作流</action>
</check>

</step>

</workflow>
