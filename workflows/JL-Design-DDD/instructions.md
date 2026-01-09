# DDD 系统设计工作流路由器

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-Design-DDD/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<critical>此路由器确定工作流模式并委派给专门的子工作流</critical>

<step n="1" goal="验证工作流并获取项目信息">

<action>检查输出目录是否存在: {inputs.output_dir}</action>
<action>如果目录不存在，创建目录</action>

<check if="需求文档路径有效">
  <action>检查 {inputs.req_path} 中的文件是否存在</action>
  <action>存储找到的需求文档路径为 {{req_doc_path}}</action>
</check>

<check if="需求文档不存在">
  <output>⚠️ 未找到需求文档。</output>
  <output>请确保在以下路径之一存在 PRD 文档：</output>
  <output>- {planning_artifacts}/prd.md</output>
  <output>- {output_folder}/feature/prd.md</output>
  <ask>是否继续手动提供需求信息？[y/n]</ask>
  <check if="n">
    <action>退出工作流</action>
  </check>
  <check if="y">
    <action>设置 manual_input_mode = true</action>
  </check>
</check>

</step>

<step n="2" goal="检查可恢复性并确定工作流模式">
<critical>智能加载策略：首先检查状态文件，然后再决定模式</critical>

<action>检查是否存在状态文件: {inputs.output_dir}/design-state.json</action>

<check if="design-state.json 存在">
  <action>读取状态文件，提取: timestamps, mode, current_phase, completed_phases</action>
  <action>计算状态文件年龄 (当前时间 - last_updated)</action>

<ask>我发现了一个进行中的工作流状态，来自 {{last_updated}}。

**当前进度:**

- 模式: {{mode}}
- 已完成阶段: {{completed_phases_count}}/{{total_phases}}
- 当前阶段: {{current_phase}}

请选择:

1. **从上次中断处继续** - 从 {{current_phase}} 阶段继续
2. **重新开始** - 归档旧状态并开始新设计
3. **取消** - 不做任何更改退出

您的选择 [1/2/3]:
</ask>

  <check if="用户选择 1">
    <action>设置 resume_mode = true</action>
    <action>设置 workflow_mode = {{mode}}</action>
    <action>加载状态文件中的设计进度</action>
    <action>显示: "恢复 {{workflow_mode}} 从 {{current_phase}} 阶段"</action>
    
    <check if="current_phase == requirements_design">
      <action>加载并执行: {installed_path}/workflows/requirements-design-instructions.md 带恢复上下文</action>
    </check>
    
    <check if="current_phase == event_storming">
      <action>加载并执行: {installed_path}/workflows/event-storming-instructions.md 带恢复上下文</action>
    </check>
    
    <check if="current_phase == ddd_modeling">
      <action>加载并执行: {installed_path}/workflows/ddd-modeling-instructions.md 带恢复上下文</action>
    </check>

    <check if="current_phase == knowledge_supplement">
      <action>加载并执行: {installed_path}/workflows/knowledge-supplement-instructions.md 带恢复上下文</action>
    </check>

    <check if="current_phase == tdd_implementation">
      <action>加载并执行: {installed_path}/workflows/tdd-implementation-instructions.md 带恢复上下文</action>
    </check>
  </check>

  <check if="用户选择 2">
    <action>创建归档目录: {inputs.output_dir}/.archive/</action>
    <action>移动旧状态文件到: {inputs.output_dir}/.archive/design-state-{{timestamp}}.json</action>
    <action>设置 resume_mode = false</action>
    <action>继续到步骤 3</action>
  </check>

  <check if="用户选择 3">
    <action>显示: "退出工作流，不做任何更改。"</action>
    <action>退出工作流</action>
  </check>

  <check if="状态文件年龄 >= 72 小时">
    <action>显示: "发现旧状态文件 (>72小时)。建议重新开始设计。"</action>
    <ask>是否归档旧状态并重新开始？[y/n]</ask>
    <check if="y">
      <action>归档旧状态文件到: {inputs.output_dir}/.archive/design-state-{{timestamp}}.json</action>
      <action>设置 resume_mode = false</action>
      <action>继续到步骤 3</action>
    </check>
  </check>
</check>

<check if="design-state.json 不存在">
  <action>设置 resume_mode = false</action>
  <action>继续到步骤 3</action>
</check>

</step>

<step n="3" goal="选择工作流模式" if="resume_mode == false">

<action>检查是否存在已有设计文档</action>
<action>检查 {inputs.output_dir} 下是否有 Requirements_Design_*.md 文件</action>
<action>检查 {inputs.output_dir} 下是否有 DDD_Design_*.md 文件</action>

<check if="已有设计文档存在">
  <action>显示找到的设计文档列表</action>
  
<ask>我发现了现有的设计文档：

{{existing_design_docs_list}}

请选择工作流模式:

1. **完整设计流程** - 重新从产研设计开始，覆盖现有文档
2. **继续 DDD 建模** - 基于现有产研设计文档进行 DDD 建模
3. **仅更新产研设计** - 只更新产研统一设计文档
4. **取消** - 保持现有文档不变

您的选择 [1/2/3/4]:
</ask>

  <check if="用户选择 1">
    <action>设置 workflow_mode = "full_design"</action>
    <action>显示: "启动完整设计流程..."</action>
    <action>继续到步骤 4</action>
  </check>
  
  <check if="用户选择 2">
    <action>设置 workflow_mode = "ddd_only"</action>
    <action>加载最新的产研设计文档作为输入</action>
    <action>显示: "启动 DDD 建模流程..."</action>
    <action>加载并执行: {installed_path}/workflows/event-storming-instructions.md</action>
  </check>
  
  <check if="用户选择 3">
    <action>设置 workflow_mode = "requirements_only"</action>
    <action>显示: "启动产研设计更新..."</action>
    <action>加载并执行: {installed_path}/workflows/requirements-design-instructions.md</action>
  </check>
  
  <check if="用户选择 4">
    <action>显示: "保持现有文档不变，退出工作流。"</action>
    <action>退出工作流</action>
  </check>
</check>

<check if="无已有设计文档">
  <action>设置 workflow_mode = "full_design"</action>
  <action>显示: "未找到现有设计文档，启动完整设计流程..."</action>
  <action>继续到步骤 4</action>
</check>

</step>

<step n="4" goal="初始化状态并启动设计流程" if="workflow_mode == full_design AND resume_mode == false">

<action>初始化状态文件: {inputs.output_dir}/design-state.json</action>
<critical>每次更新状态文件时，记录: 阶段 ID、可读摘要、精确时间戳和任何输出。模糊描述不可接受。</critical>

<action>写入初始状态:
{
  "workflow_version": "1.0.0",
  "timestamps": {"started": "{{current_timestamp}}", "last_updated": "{{current_timestamp}}"},
  "mode": "full_design",
  "current_phase": "requirements_design",
  "completed_phases": [],
  "findings": {},
  "outputs_generated": ["design-state.json"],
  "resume_instructions": "从产研设计阶段开始"
}
</action>

<action>显示: "✓ 工作流初始化完成，开始产研统一设计..."</action>
<action>加载并执行: {installed_path}/workflows/requirements-design-instructions.md</action>

</step>

<step n="5" goal="完成产研设计后转入事件风暴">

<check if="requirements_design_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "requirements_design", "status": "completed", "timestamp": "{{now}}", "output": "{{requirements_doc_path}}"}
  - 更新 current_phase = "event_storming"
  - 更新 last_updated 时间戳
  </action>
  
  <output>
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ 产研统一设计已完成
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  
  **📊 进度看板**
  ```mermaid
  gantt
      title DDD设计进度
      dateFormat YYYY-MM-DD
      section 阶段
      产研设计       :done,    p1, {{timestamps.started}}, 1d
      事件风暴       :active,  p2, after p1, 1d
      DDD建模        :         p3, after p2, 1d
      知识补充       :         p4, after p3, 1d
      TDD实现        :         p5, after p4, 2d
  ```

  **下一步计划:**
  我们即将进入 **事件风暴建模** 阶段。
  1. 识别领域事件、命令和聚合
  2. 绘制事件风暴图
  3. 识别业务热点和设计建议
  
  这是一个**高交互性**的过程，需要您的持续确认。
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  </output>

  <ask>准备好开始事件风暴了吗？[y/n]</ask>
  
  <check if="y">
    <action>加载并执行: {installed_path}/workflows/event-storming-instructions.md</action>
  </check>
  
  <check if="n">
    <action>显示: "工作流暂停。您可以通过 'JL-Design-DDD' 命令并在选择恢复模式来继续。"</action>
    <action>退出工作流</action>
  </check>
</check>

</step>

<step n="6" goal="完成事件风暴后转入 DDD 建模">

<check if="event_storming_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "event_storming", "status": "completed", "timestamp": "{{now}}"}
  - 更新 current_phase = "ddd_modeling"
  - 更新 last_updated 时间戳
  </action>
  
  <output>
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ 事件风暴建模已完成
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  
  **📊 进度看板**
  ```mermaid
  gantt
      title DDD设计进度
      dateFormat YYYY-MM-DD
      section 阶段
      产研设计       :done,    p1, {{timestamps.started}}, 1d
      事件风暴       :done,    p2, after p1, 1d
      DDD建模        :active,  p3, after p2, 1d
      知识补充       :         p4, after p3, 1d
      TDD实现        :         p5, after p4, 2d
  ```
  
  **下一步计划:**
  我们即将进入 **DDD 领域建模** 阶段。
  1. 细化限界上下文
  2. 设计聚合根、实体和值对象
  3. 生成详细的类图和架构图
  4. 完成最终的 DDD 建模报告
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  </output>

  <ask>准备好开始 DDD 建模了吗？[y/n]</ask>

  <check if="y">
    <action>加载并执行: {installed_path}/workflows/ddd-modeling-instructions.md</action>
  </check>
  
  <check if="n">
    <action>显示: "工作流暂停。您可以通过恢复模式继续。"</action>
    <action>退出工作流</action>
  </check>
</check>

</step>

<step n="7" goal="完成 DDD 建模后转入知识补充">

<check if="ddd_modeling_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "ddd_modeling", "status": "completed", "timestamp": "{{now}}", "output": "{{ddd_doc_path}}"}
  - 更新 current_phase = "knowledge_supplement"
  - 更新 timestamps.completed = "{{now}}"
  </action>

  <output>
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ DDD 领域建模已完成
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  
  **📊 进度看板**
  ```mermaid
  gantt
      title DDD设计进度
      dateFormat YYYY-MM-DD
      section 阶段
      产研设计       :done,    p1, {{timestamps.started}}, 1d
      事件风暴       :done,    p2, after p1, 1d
      DDD建模        :done,    p3, after p2, 1d
      知识补充       :active,  p4, after p3, 1d
      TDD实现        :         p5, after p4, 2d
  ```
  
  **下一步计划:**
  我们即将进入 **知识补充与深度复盘** 阶段。
  在此阶段，我将切换为“资深架构师导师”，为您生成《架构决策与领域知识指南》。
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  </output>

  <ask>准备好开始知识补充了吗？[y/n]</ask>

  <check if="y">
    <action>加载并执行: {installed_path}/workflows/knowledge-supplement-instructions.md</action>
  </check>
  
  <check if="n">
    <action>显示: "工作流暂停。您可以稍后通过恢复模式生成知识指南。"</action>
    <action>退出工作流</action>
  </check>
</check>

</step>

<step n="8" goal="完成知识补充后转入 TDD 代码实现">

<check if="knowledge_supplement_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "knowledge_supplement", "status": "completed", "timestamp": "{{now}}"}
  - 更新 current_phase = "tdd_implementation"
  - 更新 timestamps.completed = "{{now}}"
  </action>

  <output>
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  ✓ 知识补充与复盘已完成
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  
  **📊 进度看板**
  ```mermaid
  gantt
      title DDD设计进度
      dateFormat YYYY-MM-DD
      section 阶段
      产研设计       :done,    p1, {{timestamps.started}}, 1d
      事件风暴       :done,    p2, after p1, 1d
      DDD建模        :done,    p3, after p2, 1d
      知识补充       :done,    p4, after p3, 1d
      TDD实现        :active,  p5, after p4, 2d
  ```
  
  **下一步计划:**
  我们即将进入最后一个阶段：**TDD 代码实现**。
  
  在此阶段，我将作为您的 TDD 结对伙伴，按照 Red-Green-Refactor 流程：
  1. 定义契约 (Contract)
  2. 编写测试 (Red)
  3. 最小化实现 (Green)
  4. 规范检查 (Refactor)
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  </output>

  <ask>准备好开始 TDD 编码了吗？[y/n]</ask>

  <check if="y">
    <action>加载并执行: {installed_path}/workflows/tdd-implementation-instructions.md</action>
  </check>
  
  <check if="n">
    <action>显示: "工作流暂停。您可以稍后通过恢复模式生成代码。"</action>
    <action>退出工作流</action>
  </check>
</check>

</step>

<step n="9" goal="完成并提供后续步骤">

<check if="tdd_implementation_completed == true">
  <action>更新状态文件:
  - 添加到 completed_phases: {"phase": "tdd_implementation", "status": "completed", "timestamp": "{{now}}"}
  - 更新 current_phase = "completed"
  - 更新 timestamps.completed = "{{now}}"
  </action>
</check>

<action>编译验证回顾变量:
  - 设置 {{verification_summary}} 为执行的验证或测试（或"无"）
  - 设置 {{open_risks}} 为任何剩余风险或待办事项（或"无"）
  - 设置 {{next_checks}} 为推荐的下一步操作（或"无"）
</action>

<output>
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

## DDD 系统设计工作流完成！✓

**输出位置:** {inputs.output_dir}/

**生成的文档:**
{{generated_files_list}}

**工作流摘要:**
- 模式: {{workflow_mode}}
- 完成阶段: {{completed_phases_count}}
- 总耗时: {{total_duration}}

**验证回顾:**
- 执行的检查: {{verification_summary}}
- 剩余风险: {{open_risks}}
- 推荐下一步: {{next_checks}}

**后续步骤:**

1. 审阅产研统一设计文档，确保业务逻辑正确
2. 检查 DDD 建模报告中的领域模型是否准确反映业务
3. 阅读《架构决策与领域知识指南》，理解设计背后的权衡
4. 将生成的 TDD 代码骨架导入 IDE，开始具体实现

**相关工作流:**
- `JL-Build-Scenario-Test-Case` - 生成测试规格
- `JL-Build-ReviewCode` - 代码评审

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
</output>

<action>显示: "状态文件已保存: {inputs.output_dir}/design-state.json"</action>

</step>

</workflow>
