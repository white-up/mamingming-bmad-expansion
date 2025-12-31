# 代码规约检查子工作流

<workflow>

<critical>此工作流执行代码规约检查（阶段 1）</critical>
<critical>调用者: ../JL-Build-ReviewCode/instructions.md 路由器</critical>
<critical>处理: code_compliance 阶段</critical>
<critical>基于知识库: Java编码规范</critical>

<step n="1.1" goal="加载代码和规范">

<action>加载以下资源：
- 当前上下文中的代码
- 用户选中的代码文件
- Java编码规范（知识库）
- {templates.cr_template} 代码审查报告模板
</action>

<check if="resume_mode == true">
  <action>加载之前的检查进度</action>
  <action>显示: "检测到之前的检查进度，将从上次中断处继续..."</action>
</check>

<output>**代码规约检查准备就绪 ✓**

待检查代码:
- 文件数: {{file_count}}
- 总行数: {{total_lines}}
- 主要类: {{main_classes}}

开始代码规约检查？[y/n]</output>

</step>

<step n="1.2" goal="Java 命名规范检查">

<action>检查命名规范：
- 类名: UpperCamelCase
- 方法名: lowerCamelCase
- 常量: UPPER_SNAKE_CASE
- 变量: lowerCamelCase，有意义的名称
- 包名: 全小写，点分隔
</action>

<action>识别违规项并记录：
- 行号
- 违规内容
- 违反的规则
- 建议修复
</action>

<output>**命名规范检查 📝**

| 位置 | 当前命名 | 问题 | 建议 |
|-----|---------|-----|-----|
{{#each naming_issues}}
| {{location}} | `{{current}}` | {{issue}} | `{{suggestion}}` |
{{/each}}

**检查结果**: {{naming_issues_count}} 个问题

继续检查异常处理？[y/确认]</output>

</step>

<step n="1.3" goal="异常处理规范检查">

<action>检查异常处理：
- 空 catch 块（No Swallow Exception）
- 异常信息是否包含上下文
- checked/unchecked 异常使用
- finally 块资源释放
- 异常链传递
</action>

<output>**异常处理检查 ⚠️**

### 发现的问题

{{#each exception_issues}}
**{{severity}}** Line {{line}}: {{description}}
```java
{{code_snippet}}
```
- 违反规则: {{rule}}
- 修复建议: {{suggestion}}

{{/each}}

**检查结果**: {{exception_issues_count}} 个问题

继续检查日志规范？[y/确认]</output>

</step>

<step n="1.4" goal="日志规范检查">

<action>检查日志规范：
- 使用参数化日志（避免字符串拼接）
- 日志级别使用正确性
- 敏感信息脱敏
- 异常日志包含堆栈
</action>

<output>**日志规范检查 📋**

### 发现的问题

{{#each log_issues}}
**{{severity}}** Line {{line}}: {{description}}
```java
// 当前写法
{{current_code}}

// 建议写法
{{suggested_code}}
```
{{/each}}

**检查结果**: {{log_issues_count}} 个问题

继续检查注释规范？[y/确认]</output>

</step>

<step n="1.5" goal="注释规范检查">

<action>检查注释规范：
- 类级别 JavaDoc
- 公共方法注释
- 复杂逻辑行内注释
- 过时或误导性注释
- TODO/FIXME 跟踪
</action>

<output>**注释规范检查 📖**

### 缺失的注释
{{#each missing_comments}}
- **{{type}}** `{{element}}` 缺少{{comment_type}}注释
{{/each}}

### 问题注释
{{#each problematic_comments}}
- Line {{line}}: {{issue}}
{{/each}}

### TODO/FIXME 统计
{{#each todos}}
- Line {{line}}: {{content}}
{{/each}}

**检查结果**: {{comment_issues_count}} 个问题</output>

</step>

<step n="1.6" goal="汇总代码规约检查结果">

<action>计算代码规约得分:
- 基础分: 100
- 每个严重问题: -20
- 每个主要问题: -10
- 每个次要问题: -5
</action>

<action>生成检查结果汇总</action>

<output>**代码规约检查汇总 📊**

## 检查结果

| 检查项 | 状态 | 问题数 | 扣分 |
|-------|-----|-------|-----|
| Java 命名规范 | {{naming_status}} | {{naming_count}} | -{{naming_deduction}} |
| 异常处理 | {{exception_status}} | {{exception_count}} | -{{exception_deduction}} |
| 日志规范 | {{log_status}} | {{log_count}} | -{{log_deduction}} |
| 注释规范 | {{comment_status}} | {{comment_count}} | -{{comment_deduction}} |

**代码规约得分**: {{compliance_score}}/100

问题详细列表已记录，继续架构设计检查？[y/n]</output>

<action>更新状态文件:
- 存储 compliance_findings
- 存储 compliance_score
- 更新 last_updated 时间戳
</action>

<check if="用户确认继续">
  <action>设置 code_compliance_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
