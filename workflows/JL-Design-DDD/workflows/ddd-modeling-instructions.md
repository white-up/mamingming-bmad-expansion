# DDD 领域建模子工作流

<workflow>

<critical>此工作流执行完整的 DDD 领域建模（阶段 3）</critical>
<critical>调用者: ../JL-Design-DDD/instructions.md 路由器</critical>
<critical>处理: ddd_modeling 阶段</critical>

<step n="3.1" goal="加载事件风暴结果">

<action>加载以下资源：
- 当前的上下文
- 用户的补充输入
- {inputs.output_dir}/Requirements_Design_*.md 产研设计文档
- {inputs.output_dir}/DDD_Design_*.md 已完成部分的 DDD 建模报告
- {templates.ddd_template} DDD 建模报告模板
- DDD与可视化规范（来自知识库）
</action>

<check if="resume_mode == true">
  <action>加载之前的建模进度</action>
  <action>显示: "检测到之前的建模进度，将从上次中断处继续..."</action>
</check>

<action>提取事件风暴结果：
- 领域事件列表
- 命令列表
- 聚合列表
- 业务规则
- 设计建议
</action>

<output>**DDD 领域建模准备就绪 ✓**

已加载事件风暴结果：
- 领域事件: {{event_count}} 个
- 命令: {{command_count}} 个
- 初步聚合: {{aggregate_count}} 个

准备开始详细领域建模。请确认开始？[y/n]</output>

</step>

<step n="3.2" goal="细化领域清单">

<action>基于事件风暴结果，生成详细的领域清单</action>

<output>**领域清单细化 📋**

### 领域事件清单
{{#each domain_events}}
**事件名称**: {{name}}
- **描述**: {{description}}
- **关联逻辑解释**: {{logic_explanation}}
- **触发条件**: {{trigger_conditions}}
- **产生的数据**: {{produced_data}}

{{/each}}

### 触发命令清单
{{#each commands}}
**命令名称**: {{name}}
- **描述**: {{description}}
- **发起者**: {{initiator}}
- **目的**: {{purpose}}
- **关联逻辑解释**: {{logic_explanation}}

{{/each}}

### 角色/聚合关联清单
{{role_aggregate_associations}}

### 业务规则清单
{{business_rules_detailed}}

请审核领域清单的完整性和准确性：</output>

<action>等待用户审核</action>

<check if="用户有修改意见">
  <action>根据反馈修改领域清单</action>
</check>

</step>

<step n="3.3" goal="划分限界上下文">

<action>分析领域元素，划分限界上下文</action>
<action>确定子域类型：
- 核心子域：业务核心竞争力
- 通用子域：通用功能，可复用
- 支撑子域：支撑核心业务的辅助功能
</action>

<output>**限界上下文划分 🔲**

### 限界上下文清单
{{#each bounded_contexts}}
**限界上下文**: {{name}}
- **核心业务关注点**: {{focus}}
- **包含的领域元素**:
  - 领域事件: {{events}}
  - 命令: {{commands}}
  - 聚合: {{aggregates}}
- **与其他上下文的关系**: {{relationships}}

{{/each}}

### 子域划分
{{#each subdomains}}
**{{name}}** ({{type}})
- **包含业务内容**: {{content}}
- **划分依据**: {{rationale}}
- **对业务的价值**: {{business_value}}

{{/each}}

**问题确认**:
1. 限界上下文边界是否清晰？
2. 子域分类是否准确？
3. 上下文之间的关系是否正确？

请提供您的反馈：</output>

<action>等待用户反馈</action>

</step>

<step n="3.4" goal="设计聚合根">

<action>为每个聚合设计聚合根</action>
<action>定义聚合根的属性和行为</action>

<output>**聚合根设计 🏛️**

{{#each aggregates}}
### {{aggregate_name}} 聚合

**聚合根**: {{root_name}}

**选择原因**: {{selection_rationale}}

**聚合根属性**:
| 属性名 | 类型 | 含义 | 用途 |
|-------|------|-----|-----|
{{#each root_attributes}}
| {{name}} | {{type}} | {{meaning}} | {{usage}} |
{{/each}}

**聚合根行为**:
| 方法名 | 参数 | 返回值 | 功能描述 | 触发条件 |
|-------|-----|-------|---------|---------|
{{#each root_behaviors}}
| {{name}} | {{params}} | {{return}} | {{description}} | {{trigger}} |
{{/each}}

---
{{/each}}

请审核聚合根设计：</output>

<action>等待用户审核</action>

</step>

<step n="3.5" goal="设计实体和值对象">

<action>为每个聚合设计内部实体和值对象</action>

<output>**聚合内实体与值对象设计 📦**

{{#each aggregates}}
### {{aggregate_name}} 聚合

#### 实体
{{#each entities}}
**实体名称**: {{name}}
- **属性**:
{{attributes_table}}
- **行为**:
{{behaviors_table}}
- **与聚合根的关系**: {{root_relationship}}

{{/each}}

#### 值对象
{{#each value_objects}}
**值对象名称**: {{name}}
- **属性**: {{attributes}}
- **不可变性说明**: {{immutability_explanation}}
- **业务含义**: {{business_meaning}}

{{/each}}

---
{{/each}}

请审核实体和值对象设计：</output>

<action>等待用户审核</action>

</step>

<step n="3.6" goal="设计聚合间关系">

<action>分析并设计聚合之间的关系</action>

<output>**聚合间关系设计 🔗**

| 源聚合 | 目标聚合 | 关系类型 | 关系说明 |
|-------|---------|---------|---------|
{{#each aggregate_relationships}}
| {{source}} | {{target}} | {{type}} | {{description}} |
{{/each}}

**关系详细说明**:
{{#each aggregate_relationships}}
### {{source}} → {{target}}
- **关系类型**: {{type}}
- **数据共享**: {{data_sharing}}
- **行为依赖**: {{behavior_dependency}}
- **在业务流程中的体现**: {{business_manifestation}}

{{/each}}

请审核聚合间关系：</output>

<action>等待用户审核</action>

</step>

<step n="3.7" goal="关联领域事件">

<action>建立领域事件与聚合的关联</action>
<action>分析领域事件之间的关系</action>

<output>**领域事件关联 🔔**

### 聚合产生的领域事件
{{#each aggregates}}
**{{aggregate_name}}**:
{{#each produced_events}}
- **{{event_name}}**
  - 关联原因: {{association_reason}}
  - 触发条件: {{trigger_condition}}
  - 事件作用: {{event_effect}}
{{/each}}

{{/each}}

### 领域事件之间的关系
{{#each event_relationships}}
**{{event_name}}**:
- 前驱事件: {{predecessors}}
- 后继事件: {{successors}}
- 并发事件: {{concurrent}}

{{/each}}

请审核领域事件关联：</output>

<action>等待用户审核</action>

</step>

<step n="3.8" goal="生成可视化图表">
<critical>所有图表必须使用 Mermaid 语法，遵守可视化规范</critical>

<action>生成以下可视化图表：
1. 架构包图（COLA 架构）
2. 核心领域上下文类图
3. 关键业务流程时序图
4. 跨上下文协作图
5. 关键实体生命周期图
6. 类详细目录结构树状图
</action>

<output>**可视化图表生成 📊**

### 1. 架构包图 (COLA 架构)
```mermaid
{{package_diagram}}
```

### 2. 核心领域上下文类图
```mermaid
{{class_diagram}}
```

### 3. 关键业务流程时序图
```mermaid
{{sequence_diagram}}
```

### 4. 跨上下文协作图
```mermaid
{{context_collaboration_diagram}}
```

### 5. 关键实体生命周期图
```mermaid
{{lifecycle_diagram}}
```

### 6. 类详细目录结构
```
{{directory_structure}}
```

请审核所有图表：</output>

<action>等待用户审核</action>

<check if="用户要求修改图表">
  <action>根据反馈修改相应图表</action>
  <action>重新展示更新后的图表</action>
</check>

</step>

<step n="3.9" goal="整合完整 DDD 建模报告">

<action>将所有内容整合为完整的 DDD 建模报告</action>
<action>确保报告结构与模板一致</action>
<action>执行自我审阅：
- 检查是否符合 DDD 建模报告模板结构
- 检查图表与文字描述的一致性
- 验证术语使用的一致性
</action>

<output>**DDD 建模报告整合完成 ✓**

**报告结构**:
- ✓ 业务分析总结
- ✓ 事件风暴图
- ✓ 元素清单
- ✓ 设计建议
- ✓ 领域清单
- ✓ 限界上下文划分
- ✓ 领域建模
- ✓ 可视化图表

**自我审阅结果**:
- 结构完整性: {{structure_check}}
- 图文一致性: {{consistency_check}}
- 术语一致性: {{terminology_check}}

准备保存完整报告？[y/n]</output>

</step>

<step n="3.10" goal="保存并完成">

<action>更新 DDD 建模报告文件: {inputs.output_dir}/DDD_Design_{{timestamp}}.md</action>
<action>验证文档保存成功</action>

<action>运行验证清单: {validation}</action>

<action>更新状态文件:
- 添加到 completed_phases: {"phase": "ddd_modeling", "status": "completed", "timestamp": "{{now}}", "output": "DDD_Design_{{timestamp}}.md"}
- 更新 current_phase = "completed"
- 更新 last_updated 时间戳
</action>

<output>**✓ DDD 建模报告已完成并保存**

**文件位置**: {inputs.output_dir}/DDD_Design_{{timestamp}}.md

**建模统计**:
- 限界上下文: {{context_count}} 个
- 聚合: {{aggregate_count}} 个
- 实体: {{entity_count}} 个
- 值对象: {{value_object_count}} 个
- 领域事件: {{event_count}} 个

**生成的图表**:
- 架构包图 ✓
- 类图 ✓
- 时序图 ✓
- 协作图 ✓
- 生命周期图 ✓
- 目录结构 ✓

是否需要最终修改？[y/修改意见/完成]</output>

<action>等待用户最终确认</action>

<check if="用户要求修改">
  <action>根据反馈修改文档</action>
  <action>重新保存文档</action>
  <action>重新展示确认信息</action>
</check>

<check if="用户确认完成">
  <action>设置 ddd_modeling_completed = true</action>
  <action>返回主工作流路由器完成工作流</action>
</check>

</step>

</workflow>
