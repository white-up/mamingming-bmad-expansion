# 上下文分析子工作流

<workflow>

<critical>此工作流执行测试上下文分析（阶段 1）</critical>
<critical>调用者: ../JL-Build-Scenario-Test-Case/instructions.md 路由器</critical>
<critical>处理: context_analysis 阶段</critical>

<step n="1.1" goal="加载测试上下文资源">

<action>加载以下资源：
- 当前的上下文
- 用户的补充输入
- {inputs.req_path} 项目文档
- {inputs.code_path} 代码文件（如提供）
- {inputs.log_path} 日志文件（如提供）
- {templates.scenario_test_template} 场景测试报告模板
</action>

<check if="resume_mode == true">
  <action>加载之前的分析进度</action>
  <action>显示: "检测到之前的分析进度，将从上次中断处继续..."</action>
</check>

<action>评估加载的资源内容，确定测试规格生成的范围</action>

<check if="上下文包含代码或日志">
  <output>**资源加载完成 ✓**

我已识别到以下业务上下文：

**代码分析:**
{{code_summary}}

**日志分析:**
{{log_summary}}

**识别的模块/功能:**
{{identified_modules}}

我准备为 **{{target_module}}** 生成测试规格书。

请确认：您需要覆盖哪些特定的异常场景或边界条件？
或者直接输入"默认"生成全量覆盖？</output>
</check>

<check if="上下文不明确">
  <output>**等待测试上下文 ⚠️**

我已就绪。请提供以下信息之一：
1. 选中相关代码片段
2. 提供日志文件路径
3. 描述需要测试的功能模块

告诉我您希望生成针对哪个功能的测试规格书？</output>
</check>

<action>等待用户确认或补充</action>

</step>

<step n="1.2" goal="拓扑还原 - 识别服务组件">

<action>分析上下文，识别场景涉及的服务组件：
- 应用服务/API 层
- 领域服务层
- 数据访问层
- 外部依赖服务
</action>

<action>识别数据库表及其交互：
- 主表和关联表
- 读写操作类型
- 事务边界
</action>

<action>识别外部 API 交互：
- 调用的第三方服务
- 请求/响应格式
- 超时和重试策略
</action>

<output>**拓扑还原完成 🔍**

**服务组件拓扑:**
```
{{service_topology}}
```

**数据库表交互:**
| 表名 | 操作类型 | 业务含义 |
|-----|---------|---------|
{{db_interactions}}

**外部 API 依赖:**
| 服务名 | 接口 | 调用场景 |
|-------|-----|---------|
{{external_apis}}

请确认拓扑分析是否准确？[y/n/补充]</output>

<action>等待用户确认</action>

</step>

<step n="1.3" goal="逻辑对齐 - 分析业务状态机">

<action>对比"业务描述"与"日志/代码"，分析业务状态机：
- 识别状态枚举值
- 识别状态转换触发条件
- 识别状态转换的前置和后置操作
</action>

<action>构建状态机模型：
- 初始状态
- 中间状态
- 终态（成功/失败）
- 转换路径
</action>

<output>**业务状态机分析 📊**

**状态定义:**
| 状态值 | 状态名称 | 业务含义 |
|-------|---------|---------|
{{state_definitions}}

**状态转换:**
```mermaid
stateDiagram-v2
{{state_machine_diagram}}
```

**关键转换说明:**
{{transition_explanations}}

请确认状态机分析是否正确？[y/n/补充]</output>

<action>等待用户确认</action>

</step>

<step n="1.4" goal="变量锁定 - 识别关键业务主键">

<action>识别在链路中传递的关键业务主键：
- 请求追踪 ID (TraceID, RequestID)
- 业务主键 (OrderID, UserID, TransactionID)
- 批次号 (BatchNo)
- 其他关键标识
</action>

<action>分析变量传递路径：
- 入口点接收
- 中间层传递
- 持久化存储
- 响应返回
</action>

<output>**关键变量识别 🔑**

**业务主键:**
| 变量名 | 类型 | 来源 | 用途 |
|-------|-----|-----|-----|
{{business_keys}}

**变量传递链路:**
{{variable_flow}}

**数据脱敏标记:**
以下变量将使用占位符替代：
{{sensitive_variables}}

请确认关键变量识别是否完整？[y/n/补充]</output>

<action>等待用户确认</action>

</step>

<step n="1.5" goal="保存上下文分析结果">

<action>整合所有分析结果</action>

<action>更新状态文件:
- 存储 service_topology
- 存储 state_machine
- 存储 business_keys
- 添加到 completed_phases
- 更新 last_updated 时间戳
</action>

<output>**✓ 上下文分析阶段完成**

**分析摘要:**
- 服务组件: {{component_count}} 个
- 数据库表: {{table_count}} 个
- 外部依赖: {{external_count}} 个
- 业务状态: {{state_count}} 个
- 关键变量: {{key_count}} 个

准备进入场景识别阶段？[y/n]</output>

<action>等待用户确认</action>

<check if="用户确认继续">
  <action>设置 context_analysis_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
