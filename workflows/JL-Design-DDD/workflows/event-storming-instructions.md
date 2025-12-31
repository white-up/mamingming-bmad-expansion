# 事件风暴子工作流

<workflow>

<critical>此工作流执行事件风暴和初步 DDD 建模（阶段 2）</critical>
<critical>调用者: ../JL-Design-DDD/instructions.md 路由器</critical>
<critical>处理: event_storming 阶段</critical>

<step n="2.1" goal="加载上下文和产研设计文档">

<action>加载以下资源：
- 当前的上下文
- 用户的补充输入
- {inputs.req_path} 需求描述文档
- {inputs.output_dir}/Requirements_Design_*.md 产研通用设计文档（最新版本）
- {templates.ddd_template} DDD 建模报告模板
- DDD与可视化规范（来自知识库）
</action>

<check if="resume_mode == true">
  <action>加载之前的事件风暴进度</action>
  <action>显示: "检测到之前的事件风暴进度，将从上次中断处继续..."</action>
</check>

<action>提取产研设计文档中的关键信息：
- 数据字典
- 业务规则汇总
- 主业务流程
- 功能清单
</action>

<output>**事件风暴准备就绪 ✓**

已加载产研设计文档，提取以下信息：
- 数据字典: {{term_count}} 个术语
- 业务规则: {{rule_count}} 条
- 主业务流程: {{flow_summary}}
- 核心功能: {{core_features_summary}}

准备开始事件风暴。请确认开始？[y/n]</output>

</step>

<step n="2.2" goal="识别领域事件">
<critical>事件风暴是迭代过程，需要与用户充分对话</critical>

<action>分析业务流程，识别所有关键的领域事件</action>
<action>领域事件命名规则：使用过去式，如"订单已创建"、"支付已完成"</action>

<output>**第一轮事件识别 🔶**

基于业务流程分析，我识别出以下领域事件：

{{#each domain_events}}
{{@index + 1}}. **{{event_name}}**
   - 描述: {{description}}
   - 触发时机: {{trigger_timing}}
{{/each}}

**问题确认**:
1. 是否有遗漏的关键业务事件？
2. 事件命名是否准确反映业务含义？
3. 事件的发生顺序是否正确？

请提供您的反馈：</output>

<action>等待用户反馈</action>

<check if="用户提供反馈">
  <action>根据反馈调整领域事件列表</action>
  <action>记录调整原因</action>
</check>

</step>

<step n="2.3" goal="识别命令和触发源">

<action>为每个领域事件识别触发命令</action>
<action>识别命令的来源：
- 用户操作
- 系统调度
- 外部系统调用
- 其他事件触发
</action>

<output>**命令识别 🔷**

为领域事件识别的触发命令：

| 领域事件 | 触发命令 | 命令来源 | 触发条件 |
|---------|---------|---------|---------|
{{#each event_command_mappings}}
| {{event_name}} | {{command_name}} | {{source}} | {{condition}} |
{{/each}}

**问题确认**:
1. 命令与事件的对应关系是否正确？
2. 是否有命令可以触发多个事件的情况？
3. 触发条件是否完整？

请提供您的反馈：</output>

<action>等待用户反馈</action>

</step>

<step n="2.4" goal="识别聚合和外部系统">

<action>识别初步聚合：
- 根据领域事件和命令，识别相关的业务实体
- 将相关实体聚合在一起
- 确定聚合边界
</action>

<action>识别外部系统和参与者：
- 用户角色
- 第三方服务
- 其他内部系统
</action>

<output>**聚合和外部系统识别 🟨🟪**

**初步识别的聚合**:
{{#each aggregates}}
- **{{aggregate_name}}**
  - 相关实体: {{entities}}
  - 关联的事件: {{related_events}}
{{/each}}

**外部系统/参与者**:
{{#each external_systems}}
- **{{name}}** ({{type}})
  - 交互方式: {{interaction}}
{{/each}}

**问题确认**:
1. 聚合边界是否合理？
2. 是否有实体应该分到不同聚合？
3. 外部系统列表是否完整？

请提供您的反馈：</output>

<action>等待用户反馈</action>

</step>

<step n="2.5" goal="识别业务规则和热点">

<action>将产研设计中的业务规则映射到事件和命令</action>
<action>识别业务热点：
- 高并发场景
- 复杂业务逻辑
- 数据一致性要求
- 性能敏感点
</action>

<output>**业务规则和热点标注 🟣**

**业务规则映射**:
{{#each business_rules_mapping}}
- **{{rule_name}}**
  - 关联命令: {{related_commands}}
  - 约束条件: {{constraints}}
  - 处理方式: {{handling}}
{{/each}}

**识别的热点问题**:
{{#each hotspots}}
- **{{hotspot_name}}** ⚠️
  - 问题描述: {{description}}
  - 建议处理: {{suggestion}}
{{/each}}

是否有补充或修改？</output>

<action>等待用户反馈</action>

</step>

<step n="2.6" goal="绘制事件风暴图">
<critical>事件风暴图样式必须遵守 DDD 与可视化规范</critical>

<action>根据收集的所有元素，绘制完整的事件风暴图</action>
<action>使用正确的颜色编码：
- 橙色：领域事件
- 蓝色：命令
- 黄色：聚合
- 粉色：外部系统/用户
- 紫色：策略/业务规则
</action>

<output>**事件风暴图 🎨**

```mermaid
{{event_storming_diagram}}
```

**图例说明**:
- 🔶 橙色方块: 领域事件
- 🔷 蓝色方块: 命令
- 🟨 黄色方块: 聚合
- 🟪 粉色方块: 外部系统/用户
- 🟣 紫色方块: 业务规则/策略

**问题确认**:
1. 事件流程是否正确反映业务？
2. 元素之间的连接是否准确？
3. 是否需要调整布局？

请提供您的反馈：</output>

<action>等待用户反馈</action>

<check if="用户要求修改">
  <action>根据反馈修改图表</action>
  <action>重新展示更新后的图表</action>
  <action>继续迭代直到用户满意</action>
</check>

</step>

<step n="2.7" goal="生成元素清单">

<action>整理所有识别的元素，生成结构化清单</action>

<output>**事件风暴元素清单 📋**

### 领域事件列表
{{#each domain_events}}
| 序号 | 事件名称 | 描述 | 触发时机 |
|-----|---------|-----|---------|
| {{@index + 1}} | {{name}} | {{description}} | {{timing}} |
{{/each}}

### 命令列表
{{#each commands}}
| 序号 | 命令名称 | 来源 | 触发事件 |
|-----|---------|-----|---------|
| {{@index + 1}} | {{name}} | {{source}} | {{triggers}} |
{{/each}}

### 聚合列表
{{#each aggregates}}
| 序号 | 聚合名称 | 包含实体 | 相关事件 |
|-----|---------|---------|---------|
| {{@index + 1}} | {{name}} | {{entities}} | {{events}} |
{{/each}}

### 外部系统列表
{{#each external_systems}}
| 序号 | 系统名称 | 类型 | 交互方式 |
|-----|---------|-----|---------|
| {{@index + 1}} | {{name}} | {{type}} | {{interaction}} |
{{/each}}

### 业务规则列表
{{business_rules_table}}

### 潜在热点问题
{{hotspots_table}}

请确认元素清单的完整性：</output>

</step>

<step n="2.8" goal="生成设计建议">

<action>基于事件风暴结果，生成初步设计建议</action>

<output>**设计建议 💡**

### 聚合设计建议
{{aggregate_design_suggestions}}

### 事件设计建议
{{event_design_suggestions}}

### 架构改进建议
{{architecture_suggestions}}

### 潜在风险提醒
{{risk_reminders}}

是否同意这些建议？[y/n/修改意见]</output>

<action>等待用户确认</action>

</step>

<step n="2.9" goal="保存事件风暴结果">

<action>整合所有内容，生成 DDD 建模报告（部分）</action>
<action>报告内容截止到「设计建议」章节</action>

<action>生成时间戳: {{timestamp}}</action>
<action>保存文档到: {inputs.output_dir}/DDD_Design_{{timestamp}}.md</action>

<action>更新状态文件:
- 添加到 completed_phases: {"phase": "event_storming", "status": "completed", "timestamp": "{{now}}"}
- 存储 event_storming_results 摘要
- 更新 last_updated 时间戳
</action>

<output>**✓ 事件风暴阶段完成**

**已保存**: {inputs.output_dir}/DDD_Design_{{timestamp}}.md

**完成内容**:
- 业务分析总结
- 事件风暴图
- 元素清单
- 设计建议

**统计**:
- 领域事件: {{event_count}} 个
- 命令: {{command_count}} 个
- 聚合: {{aggregate_count}} 个
- 业务规则: {{rule_count}} 条

准备进入 DDD 领域建模阶段？[y/n/修改]</output>

<action>等待用户确认</action>

<check if="用户要求修改">
  <action>返回相应步骤进行修改</action>
</check>

<check if="用户确认继续">
  <action>设置 event_storming_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
