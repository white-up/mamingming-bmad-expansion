# 架构设计检查子工作流

<workflow>

<critical>此工作流执行架构设计检查（阶段 2）</critical>
<critical>调用者: ../JL-Build-ReviewCode/instructions.md 路由器</critical>
<critical>处理: architecture_review 阶段</critical>
<critical>基于知识库: 架构设计规范, DDD与可视化规范</critical>

<step n="2.1" goal="加载架构规范">

<action>加载以下资源：
- 当前上下文中的代码
- 架构设计规范（知识库）
- DDD与可视化规范（知识库）
- 代码规约检查结果
</action>

<check if="resume_mode == true">
  <action>加载之前的检查进度</action>
  <action>显示已完成的检查维度</action>
</check>

<output>**架构设计检查准备就绪 ✓**

将从以下维度进行检查:
1. 封装性
2. 聚合边界
3. 领域逻辑
4. 值对象使用
5. 分层架构

开始架构设计检查？[y/n]</output>

</step>

<step n="2.2" goal="封装性检查">

<action>检查封装性：
- 字段访问修饰符
- 是否暴露内部实现
- 防御性拷贝
- 集合返回不可变视图
</action>

<output>**封装性检查 🔒**

### 发现的问题

{{#each encapsulation_issues}}
**{{severity}}** `{{class_name}}.{{field_name}}`
- 问题: {{issue}}
- 当前:
```java
{{current_code}}
```
- 建议:
```java
{{suggested_code}}
```
{{/each}}

**封装性评分**: {{encapsulation_score}}/5

继续检查聚合边界？[y/确认]</output>

</step>

<step n="2.3" goal="聚合边界检查">

<action>检查聚合边界：
- 聚合根是否清晰定义
- 聚合内实体访问方式
- 跨聚合引用方式（应使用 ID）
- 事务边界是否正确
</action>

<output>**聚合边界检查 🎯**

### 识别的聚合

{{#each aggregates}}
**{{aggregate_name}}**
- 聚合根: `{{root_class}}`
- 内部实体: {{entities}}
- 边界问题: {{boundary_issues}}
{{/each}}

### 发现的问题

{{#each aggregate_issues}}
**{{severity}}** {{description}}
- 位置: {{location}}
- 违反: {{violation}}
- 建议: {{suggestion}}
{{/each}}

**聚合边界评分**: {{aggregate_score}}/5

继续检查领域逻辑？[y/确认]</output>

</step>

<step n="2.4" goal="领域逻辑检查">

<action>检查领域逻辑：
- 业务逻辑是否在领域层
- 是否存在贫血模型
- 领域事件使用
- 领域服务职责
</action>

<output>**领域逻辑检查 🧠**

### 贫血模型检测

{{#each anemic_models}}
**⚠️ 可能的贫血模型**: `{{class_name}}`
- 只有 getter/setter: {{getter_setter_only}}
- 业务逻辑位置: {{logic_location}}
- 建议: {{suggestion}}
{{/each}}

### 领域服务分析

{{#each domain_services}}
**{{service_name}}**
- 职责: {{responsibility}}
- 问题: {{issues}}
{{/each}}

**领域逻辑评分**: {{domain_logic_score}}/5

继续检查值对象？[y/确认]</output>

</step>

<step n="2.5" goal="值对象使用检查">

<action>检查值对象使用：
- 值对象是否不可变
- equals/hashCode 实现
- 是否正确封装业务概念
- 基本类型偏执检测
</action>

<output>**值对象使用检查 📦**

### 识别的值对象

{{#each value_objects}}
**`{{class_name}}`**
- 不可变: {{is_immutable}}
- equals/hashCode: {{has_equals_hashcode}}
- 问题: {{issues}}
{{/each}}

### 基本类型偏执

{{#each primitive_obsession}}
**建议提取为值对象**: 
- 位置: `{{location}}`
- 字段: `{{field}}`
- 建议: 提取为 `{{suggested_vo}}`
{{/each}}

**值对象使用评分**: {{value_object_score}}/5

继续检查分层架构？[y/确认]</output>

</step>

<step n="2.6" goal="分层架构检查">

<action>检查分层架构：
- 层间依赖方向
- 循环依赖检测
- 接口定义位置
- 依赖注入使用
</action>

<output>**分层架构检查 🏗️**

### 依赖分析

{{dependency_diagram}}

### 发现的问题

{{#each layer_issues}}
**{{severity}}** {{description}}
- 违反: {{violation}}
- 位置: {{location}}
- 建议: {{suggestion}}
{{/each}}

**分层架构评分**: {{layer_score}}/5</output>

</step>

<step n="2.7" goal="汇总架构设计检查结果">

<action>计算架构设计得分:
- 封装性 (20%): {{encapsulation_score}}/5
- 聚合边界 (25%): {{aggregate_score}}/5
- 领域逻辑 (25%): {{domain_logic_score}}/5
- 值对象 (15%): {{value_object_score}}/5
- 分层架构 (15%): {{layer_score}}/5
</action>

<output>**架构设计检查汇总 📊**

## 检查结果

| 检查维度 | 评分 (1-5) | 发现的问题/异味 |
|---------|-----------|----------------|
| 封装性 | {{encapsulation_score}} | {{encapsulation_summary}} |
| 聚合边界 | {{aggregate_score}} | {{aggregate_summary}} |
| 领域逻辑 | {{domain_logic_score}} | {{domain_summary}} |
| 值对象使用 | {{value_object_score}} | {{vo_summary}} |
| 分层架构 | {{layer_score}} | {{layer_summary}} |

**架构设计得分**: {{architecture_score}}/100

问题详细列表已记录，继续安全性检查？[y/n]</output>

<action>更新状态文件:
- 存储 architecture_findings
- 存储 architecture_score
- 更新 last_updated 时间戳
</action>

<check if="用户确认继续">
  <action>设置 architecture_review_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
