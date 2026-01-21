# 知识库条目生成工作流路由器

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-Ship-GenKnowledge/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<critical>此路由器确定工作流模式并委派给专门的子工作流</critical>

<step n="1" goal="验证工作流并检查输出目录">

<action>检查输出目录是否存在: {inputs.output_dir}</action>
<action>如果目录不存在，创建目录</action>

<action>检查可用的输入资源：
- 当前上下文中的问题描述
- 当前上下文中的解决方案
- 用户提供的补充信息
</action>

<action>统计可用资源并存储为 {{available_resources}}</action>

</step>

<step n="2" goal="检查可恢复性并确定工作流模式">
<critical>智能加载策略：首先检查状态文件，然后再决定模式</critical>

<action>检查是否存在状态文件: {inputs.output_dir}/knowledge-state.json</action>

<check if="knowledge-state.json 存在">
  <action>读取状态文件，提取: timestamps, mode, current_phase, entries_generated</action>
  <action>计算状态文件年龄 (当前时间 - last_updated)</action>

<ask>我发现了一个进行中的知识库生成状态，来自 {{last_updated}}。

**当前进度:**

- 模式: {{mode}}
- 已生成条目: {{entries_count}} 条
- 当前阶段: {{current_phase}}

请选择:

1. **从上次中断处继续** - 继续生成知识库条目
2. **重新开始** - 归档旧状态并重新开始
3. **查看已生成条目** - 查看已完成的知识库条目
4. **取消** - 不做任何更改退出

您的选择 [1/2/3/4]:
</ask>

  <check if="用户选择 1">
    <action>设置 resume_mode = true</action>
    <action>设置 workflow_mode = {{mode}}</action>
    <action>加载状态文件中的生成进度</action>
    <action>显示: "恢复知识库生成从 {{current_phase}} 阶段"</action>
    <action>跳转到相应阶段继续执行</action>
  </check>

  <check if="用户选择 2">
    <action>创建归档目录: {inputs.output_dir}/.archive/</action>
    <action>移动旧状态文件到: {inputs.output_dir}/.archive/knowledge-state-{{timestamp}}.json</action>
    <action>设置 resume_mode = false</action>
    <action>继续到步骤 3</action>
  </check>

  <check if="用户选择 3">
    <action>显示已生成的知识库条目列表</action>
    <ask>是否继续生成新条目？[y/n]</ask>
  </check>

  <check if="用户选择 4">
    <action>显示: "退出工作流，不做任何更改。"</action>
    <action>退出工作流</action>
  </check>

  <check if="状态文件年龄 >= 24 小时">
    <action>显示: "发现旧状态文件 (>24小时)。建议重新开始。"</action>
    <ask>是否归档旧状态并重新开始？[y/n]</ask>
  </check>
</check>

<check if="knowledge-state.json 不存在">
  <action>设置 resume_mode = false</action>
  <action>继续到步骤 3</action>
</check>

</step>

<step n="3" goal="检查输入上下文并确定模式" if="resume_mode == false">

<action>分析当前上下文中的内容</action>

<check if="上下文包含问题和解决方案">
  <action>识别问题描述</action>
  <action>识别解决方案描述</action>
  <action>尝试识别业务模块</action>
  
  <output>**输入内容识别 ✓**

我已识别到以下内容：

**问题描述**: {{problem_summary}}

**解决方案**: {{solution_summary}}

**可能的业务模块**: {{business_module_guess}}

请确认:
1. 我的理解是否正确？
2. 是否需要调整业务模块分类？

[确认 / 需要调整]</output>

  <check if="用户确认">
    <action>设置 workflow_mode = "single_entry"</action>
    <action>显示: "启动单条目生成流程..."</action>
    <action>继续到步骤 4</action>
  </check>
  
  <check if="用户要求调整">
    <ask>请提供正确的信息：

1. 问题描述（如需修改）
2. 解决方案（如需修改）
3. 业务模块（从以下选择）：
   - 订单管理 / 订单售后
   - 客户管理 / 财务对账
   - 库存管理 / 物流配送
   - 系统配置 / 权限管理
   - 报表统计 / 其他

请提供调整内容:</ask>
    <action>更新输入信息</action>
    <action>继续到步骤 4</action>
  </check>
</check>

<check if="上下文包含多个问题">
  <action>识别多个问题和解决方案对</action>
  
  <output>**批量输入识别 ✓**

我识别到 {{problems_count}} 个问题需要生成知识库条目：

{{#each problems}}
{{@index}}. {{this.summary}}
{{/each}}

请选择处理模式:
1. **批量生成** - 依次为每个问题生成条目
2. **选择性生成** - 选择特定问题生成
3. **逐个确认** - 逐个确认后生成

您的选择 [1/2/3]:</output>

  <check if="用户选择 1">
    <action>设置 workflow_mode = "batch_entry"</action>
    <action>加载并执行: {installed_path}/workflows/knowledge-generation-instructions.md 批量模式</action>
  </check>
  
  <check if="用户选择 2">
    <ask>请输入要生成的问题编号（用逗号分隔）:</ask>
    <action>筛选选定的问题</action>
    <action>设置 workflow_mode = "batch_entry"</action>
    <action>加载并执行: {installed_path}/workflows/knowledge-generation-instructions.md 批量模式</action>
  </check>
  
  <check if="用户选择 3">
    <action>设置 workflow_mode = "single_entry"</action>
    <action>设置 confirm_each = true</action>
    <action>继续到步骤 4</action>
  </check>
</check>

<check if="上下文中无有效内容">
  <output>**等待输入内容 ⚠️**

我已准备好为您生成知识库QA条目。请提供：

1. **问题描述**：客户/用户遇到了什么问题？
2. **解决方案**：您是如何解决的？

您可以直接粘贴一线支持人员的原始对话记录，我会帮您整理成标准格式。

**示例输入**：
> 问题：客户投诉说在APP上点退款点不动，报错提示'当前订单状态不支持'
> 解决：查了后台发现订单已进入发货库，需要先联系仓库拦截，然后后台改状态...</output>
  <action>等待用户输入</action>
</check>

</step>

<step n="4" goal="确认业务模块分类">

<check if="business_module 未设置">
<ask>请确认该问题属于哪个业务模块：

1. **订单管理** - 订单创建、修改、查询
2. **订单售后** - 退款、退货、换货
3. **客户管理** - 客户信息、账户管理
4. **财务对账** - 发票、对账、结算
5. **库存管理** - 库存查询、调整
6. **物流配送** - 发货、物流跟踪
7. **系统配置** - 系统设置、参数配置
8. **权限管理** - 用户权限、角色管理
9. **报表统计** - 数据报表、统计分析
10. **其他** - 以上都不适用

您的选择 [1-10]:</ask>
<action>存储业务模块为 {{business_module}}</action>
</check>

<output>**生成配置确认 ✓**

| 配置项 | 值 |
|-------|-----|
| 问题摘要 | {{problem_summary}} |
| 业务模块 | {{business_module}} |
| 生成模式 | {{workflow_mode}} |

准备开始生成知识库条目...</output>

</step>

<step n="5" goal="初始化状态并启动知识库生成" if="resume_mode == false">

<action>初始化状态文件: {inputs.output_dir}/knowledge-state.json</action>
<critical>每次更新状态文件时，记录: 阶段 ID、可读摘要、精确时间戳和任何输出。</critical>

<action>写入初始状态:
{
  "workflow_version": "1.0.0",
  "timestamps": {"started": "{{current_timestamp}}", "last_updated": "{{current_timestamp}}"},
  "mode": "{{workflow_mode}}",
  "current_phase": "knowledge_generation",
  "completed_phases": [],
  "entries_generated": [],
  "outputs_generated": ["knowledge-state.json"],
  "resume_instructions": "从知识库生成阶段开始"
}
</action>

<action>显示: "✓ 工作流初始化完成，开始生成知识库条目..."</action>
<action>加载并执行: {installed_path}/workflows/knowledge-generation-instructions.md</action>

</step>

<step n="6" goal="完成并提供后续步骤">

<check if="knowledge_generation_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "knowledge_generation", "status": "completed", "timestamp": "{{now}}", "output": "{{entry_path}}"}
  - 更新 current_phase = "completed"
  - 更新 timestamps.completed = "{{now}}"
  - 更新 entries_generated 列表
  </action>
</check>

<action>运行验证清单: {validation}</action>

<output>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## 知识库条目生成工作流完成！✓

**输出位置:** {inputs.output_dir}/

**生成的条目:**
{{#each entries_generated}}
- {{this.title}} ({{this.path}})
{{/each}}

**生成摘要:**
| 指标 | 值 |
|-----|-----|
| 生成条目数 | {{entries_count}} 条 |
| 业务模块 | {{business_modules_involved}} |
| 总耗时 | {{total_duration}} |

**条目质量检查:**
- ✓ 标题符合关键词前置规则
- ✓ 标题包含同义词扩展
- ✓ 内容结构化（场景/步骤/注意事项）
- ✓ 步骤以动词开头

**后续步骤:**

1. 审阅生成的知识库条目
2. 如需调整，可重新运行工作流
3. 将条目导入知识库系统
4. 测试语义搜索匹配效果

**相关工作流:**
- `JL-Ship-AnalyzeCode` - 代码逆向分析
- `JL-Build-ReviewCode` - 代码审查

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
</output>

<action>显示: "状态文件已保存: {inputs.output_dir}/knowledge-state.json"</action>

</step>

</workflow>
