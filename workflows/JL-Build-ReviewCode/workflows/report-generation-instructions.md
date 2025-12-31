# 报告生成子工作流

<workflow>

<critical>此工作流执行代码审查报告生成（阶段 4）</critical>
<critical>调用者: ../JL-Build-ReviewCode/instructions.md 路由器</critical>
<critical>处理: report_generation 阶段</critical>

<step n="4.1" goal="加载所有检查结果">

<action>加载以下资源：
- {templates.cr_template} 代码审查报告模板
- 代码规约检查结果
- 架构设计检查结果
- 安全性检查结果
- 状态文件中的所有 findings
</action>

<action>汇总所有问题并分类：
- Critical (严重)
- Major (主要)
- Minor (次要)
- Info (提示)
</action>

<output>**报告生成准备就绪 ✓**

**问题统计:**
- 🔴 严重问题: {{critical_count}}
- 🟠 主要问题: {{major_count}}
- 🟡 次要问题: {{minor_count}}
- 🔵 提示信息: {{info_count}}

**各维度得分:**
- 代码规约: {{compliance_score}}/100
- 架构设计: {{architecture_score}}/100
- 安全性: {{security_score}}/100

开始生成报告？[y/n]</output>

</step>

<step n="4.2" goal="生成审查摘要">

<action>生成审查摘要：
- 审查范围
- 主要发现
- 总体评价
</action>

<output>**审查摘要生成 📝**

## 1. 审查摘要 (Executive Summary)

**审查范围:**
- 文件数: {{file_count}}
- 代码行数: {{total_lines}}
- 主要模块: {{main_modules}}

**主要发现:**
{{executive_summary}}

**总体评价:**
{{overall_assessment}}

继续生成规约检查表？[y/确认]</output>

</step>

<step n="4.3" goal="生成代码规约检查表">

<action>生成规约检查表格</action>

<output>**代码规约检查表 ✅**

## 2. 代码核心规约检查 (Compliance Check)

| 检查项 | 状态 | 备注 |
|:------|:-----|:----|
| Java 命名规范 | {{naming_status}} | {{naming_remarks}} |
| 异常处理 (No Swallow) | {{exception_status}} | {{exception_remarks}} |
| 日志规范 | {{log_status}} | {{log_remarks}} |
| 注释规范 | {{comment_status}} | {{comment_remarks}} |
| 安全性 (OWASP) | {{security_status}} | {{security_remarks}} |
| 单元测试覆盖 | {{test_status}} | {{test_remarks}} |

继续生成架构检查表？[y/确认]</output>

</step>

<step n="4.4" goal="生成架构设计检查表">

<action>生成架构检查表格</action>

<output>**架构设计检查表 🏗️**

## 3. 架构设计检查

| 检查维度 | 评分 (1-5) | 发现的问题/异味 |
|:--------|:----------|:---------------|
| 封装性 | {{encapsulation_score}} | {{encapsulation_issues}} |
| 聚合边界 | {{aggregate_score}} | {{aggregate_issues}} |
| 领域逻辑 | {{domain_score}} | {{domain_issues}} |
| 值对象使用 | {{vo_score}} | {{vo_issues}} |
| 分层架构 | {{layer_score}} | {{layer_issues}} |

继续生成详细发现？[y/确认]</output>

</step>

<step n="4.5" goal="生成详细发现列表">

<action>按严重程度生成详细问题列表</action>

<output>**详细发现列表 🔍**

## 4. 详细发现 (Detailed Findings)

### 🔴 严重问题 (Critical)
{{#each critical_issues}}
* **Line {{line}}** (`{{file}}`): {{description}}
    * *违反规则*: {{rule}}
    * *修复建议*: 
```java
{{fix_suggestion}}
```

{{/each}}

### 🟠 主要问题 (Major)
{{#each major_issues}}
* **Line {{line}}** (`{{file}}`): {{description}}
    * *建议*: {{suggestion}}

{{/each}}

### 🟡 次要问题 (Minor)
{{#each minor_issues}}
* **Line {{line}}**: {{description}}
    * *建议*: {{suggestion}}

{{/each}}

### 🔵 提示信息 (Info)
{{#each info_issues}}
* **Line {{line}}**: {{description}}

{{/each}}

继续生成结论？[y/确认]</output>

</step>

<step n="4.6" goal="生成结论和建议">

<action>计算最终得分和结论</action>

<action>确定审查结果:
- >= 80 且无严重问题: Pass
- 60-79 或有 1 个严重问题: Conditional Pass
- < 60 或有多个严重问题: Fail
</action>

<output>**结论与建议 📋**

## 5. 结论与下一步

**总体评分**: {{total_score}}/100 ({{review_result}})

**审查结论**:
{{conclusion}}

**下一步建议**:
{{#if has_critical}}
1. ⚠️ **必须修复严重问题** ({{critical_count}} 个) - 阻塞合并
{{/if}}
{{#if has_major}}
2. **建议修复主要问题** ({{major_count}} 个) - 影响代码质量
{{/if}}
3. 可选修复次要问题和优化建议
4. 修复后建议重新运行代码审查

准备保存报告？[y/n]</output>

</step>

<step n="4.7" goal="整合并保存报告">

<action>将所有部分整合为完整报告</action>
<action>以"# 代码审查报告"作为开头</action>
<action>添加元数据（日期、评分）</action>

<action>生成时间戳: {{timestamp}}</action>
<action>保存报告到: {inputs.output_dir}/CR_{{timestamp}}.md</action>
<action>验证文档保存成功</action>

<action>更新状态文件:
- 添加到 completed_phases: {"phase": "report_generation", "status": "completed", "timestamp": "{{now}}", "output": "CR_{{timestamp}}.md"}
- 更新 current_phase = "completed"
- 更新 last_updated 时间戳
</action>

<output>**✓ 代码审查报告已保存**

**文件位置**: {inputs.output_dir}/CR_{{timestamp}}.md

**报告统计:**
- 总体评分: {{total_score}}/100
- 审查结果: {{review_result}}
- 严重问题: {{critical_count}} 个
- 主要问题: {{major_count}} 个

是否需要修改报告？[y/修改意见/完成]</output>

<action>等待用户最终确认</action>

<check if="用户要求修改">
  <action>根据反馈修改报告</action>
  <action>重新保存报告</action>
</check>

<check if="用户确认完成">
  <action>设置 report_generation_completed = true</action>
  <action>返回主工作流路由器完成工作流</action>
</check>

</step>

</workflow>
