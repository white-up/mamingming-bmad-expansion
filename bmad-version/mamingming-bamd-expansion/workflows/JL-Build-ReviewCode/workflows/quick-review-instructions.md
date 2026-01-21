# 快速审查子工作流

<workflow>

<critical>此工作流执行快速代码审查</critical>
<critical>调用者: ../JL-Build-ReviewCode/instructions.md 路由器</critical>
<critical>处理: quick_review 模式</critical>
<critical>适用于: 小改动 (< 200 行)</critical>

<step n="Q.1" goal="快速上下文分析">

<action>快速分析代码变更：
- 变更行数
- 变更类型（新增/修改/删除）
- 影响范围
</action>

<output>**快速审查模式 ⚡**

**变更分析:**
- 变更行数: {{changed_lines}}
- 变更文件: {{changed_files}}
- 变更类型: {{change_type}}

开始快速审查？[y/n]</output>

</step>

<step n="Q.2" goal="关键问题扫描">

<action>快速扫描关键问题：
- 明显的安全漏洞
- 严重的编码问题
- 明显的架构违规
</action>

<output>**关键问题扫描 🔍**

### 发现的关键问题

{{#if has_critical}}
{{#each critical_issues}}
**🔴 严重** Line {{line}}: {{description}}
- 建议: {{suggestion}}
{{/each}}
{{else}}
✅ 未发现严重问题
{{/if}}

### 需要关注的点

{{#each attention_points}}
- **{{severity}}** Line {{line}}: {{description}}
{{/each}}

继续生成快速报告？[y/确认]</output>

</step>

<step n="Q.3" goal="生成快速审查报告">

<action>生成简化版审查报告</action>

<output>**快速审查结果 📝**

# 代码审查报告 (快速模式)

> **日期**: {{timestamp}}
> **模式**: 快速审查
> **总体评估**: {{quick_assessment}}

## 审查摘要

**变更范围**: {{change_scope}}

**快速检查结果**:
| 检查项 | 状态 |
|-------|-----|
| 严重安全问题 | {{security_status}} |
| 严重编码问题 | {{coding_status}} |
| 明显架构违规 | {{arch_status}} |

## 发现的问题

{{#each all_issues}}
* **{{severity}}** Line {{line}}: {{description}}
{{/each}}

## 建议

{{quick_suggestions}}

---
*注: 快速审查仅检查关键问题，建议对重要变更使用完整审查模式*

保存报告？[y/n]</output>

<action>保存报告到: {inputs.output_dir}/CR_Quick_{{timestamp}}.md</action>

<check if="用户确认">
  <action>设置 report_generation_completed = true</action>
  <action>返回主工作流路由器完成工作流</action>
</check>

</step>

</workflow>
