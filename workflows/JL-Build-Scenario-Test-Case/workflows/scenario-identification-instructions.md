# 场景识别子工作流

<workflow>

<critical>此工作流执行测试场景识别（阶段 2）</critical>
<critical>调用者: ../JL-Build-Scenario-Test-Case/instructions.md 路由器</critical>
<critical>处理: scenario_identification 阶段</critical>

<step n="2.1" goal="加载上下文分析结果">

<action>加载以下资源：
- 当前的上下文
- 状态文件中的分析结果
- 服务拓扑信息
- 业务状态机
- 关键变量列表
</action>

<check if="resume_mode == true">
  <action>加载之前的场景识别进度</action>
  <action>显示已识别的场景数量</action>
</check>

<output>**场景识别准备就绪 ✓**

基于上下文分析，准备识别以下类型的测试场景：
- 正常流程 (Happy Path) - P0
- 异常流程 (Exception) - P1
- 边界测试 (Boundary) - P1
- 并发测试 (Concurrency) - P2
- 性能测试 (Performance) - P2

开始场景识别？[y/n]</output>

</step>

<step n="2.2" goal="识别正常流程场景 (Happy Path)">

<action>基于业务状态机，识别所有正常执行路径：
- 主流程成功路径
- 各分支的正常完成路径
- 不同入参组合的成功场景
</action>

<action>为每个场景定义：
- 场景 ID (TC-HP-XXX)
- 场景名称
- 前置条件
- 测试步骤
- 预期结果
- 关键测试点
</action>

<output>**正常流程场景识别 🟢**

| ID | 场景名称 | 前置条件 | 预期结果 | 关键测试点 | 优先级 |
|---|---------|---------|---------|-----------|-------|
{{#each happy_path_scenarios}}
| {{id}} | {{name}} | {{precondition}} | {{expected}} | {{test_point}} | P0 |
{{/each}}

**识别的正常流程场景: {{happy_path_count}} 个**

是否需要补充或修改？[y/n/补充]</output>

<action>等待用户确认</action>

<check if="用户有补充">
  <action>根据反馈添加或修改场景</action>
</check>

</step>

<step n="2.3" goal="识别异常流程场景">

<action>识别业务异常场景：
- 业务规则校验失败
- 数据不存在/已存在
- 权限校验失败
- 状态不允许操作
</action>

<action>识别系统异常场景：
- 数据库操作失败
- 外部服务调用失败
- 网络超时
- 资源不足
</action>

<action>识别补偿/回滚场景：
- 事务回滚触发条件
- 补偿操作执行
- 数据一致性恢复
</action>

<output>**异常流程场景识别 🔴**

### 业务异常
| ID | 场景名称 | 触发条件 | 预期行为 | 关键测试点 | 优先级 |
|---|---------|---------|---------|-----------|-------|
{{#each business_exceptions}}
| {{id}} | {{name}} | {{trigger}} | {{expected}} | {{test_point}} | P1 |
{{/each}}

### 系统异常
| ID | 场景名称 | 触发条件 | 预期行为 | 关键测试点 | 优先级 |
|---|---------|---------|---------|-----------|-------|
{{#each system_exceptions}}
| {{id}} | {{name}} | {{trigger}} | {{expected}} | {{test_point}} | P1 |
{{/each}}

### 补偿/回滚
| ID | 场景名称 | 触发条件 | 补偿动作 | 关键测试点 | 优先级 |
|---|---------|---------|---------|-----------|-------|
{{#each compensation_scenarios}}
| {{id}} | {{name}} | {{trigger}} | {{compensation}} | {{test_point}} | P1 |
{{/each}}

**识别的异常流程场景: {{exception_count}} 个**

是否需要补充或修改？[y/n/补充]</output>

<action>等待用户确认</action>

</step>

<step n="2.4" goal="识别边界测试场景">

<action>识别输入边界：
- 参数最大/最小值
- 字符串长度边界
- 数组/集合大小边界
- 日期/时间边界
</action>

<action>识别数据边界：
- 空值/NULL 处理
- 空字符串处理
- 空集合处理
- 特殊字符处理
</action>

<action>识别业务边界：
- 数量限制边界
- 金额限制边界
- 次数限制边界
- 时间窗口边界
</action>

<output>**边界测试场景识别 🟡**

| ID | 场景名称 | 边界类型 | 测试值 | 预期结果 | 优先级 |
|---|---------|---------|-------|---------|-------|
{{#each boundary_scenarios}}
| {{id}} | {{name}} | {{boundary_type}} | {{test_value}} | {{expected}} | P1 |
{{/each}}

**识别的边界测试场景: {{boundary_count}} 个**

是否需要补充或修改？[y/n/补充]</output>

<action>等待用户确认</action>

</step>

<step n="2.5" goal="覆盖补完 - 识别潜在遗漏场景">
<critical>思考 Happy Path 之外，日志中未体现但必然存在的潜在故障/边界点</critical>

<action>分析潜在遗漏：
- 并发操作场景
- 幂等性验证场景
- 重试场景
- 超时场景
- 数据竞争场景
</action>

<action>补充可选的高级测试场景：
- 性能压测场景 (P2)
- 安全测试场景 (P2)
- 兼容性测试场景 (P3)
</action>

<output>**潜在遗漏场景补完 🔵**

### 并发/幂等场景
| ID | 场景名称 | 测试目的 | 测试方法 | 优先级 |
|---|---------|---------|---------|-------|
{{#each concurrency_scenarios}}
| {{id}} | {{name}} | {{purpose}} | {{method}} | P2 |
{{/each}}

### 性能测试场景（可选）
| ID | 场景名称 | 性能指标 | 基准值 | 优先级 |
|---|---------|---------|-------|-------|
{{#each performance_scenarios}}
| {{id}} | {{name}} | {{metric}} | {{baseline}} | P2 |
{{/each}}

**补充的场景: {{additional_count}} 个**

是否需要调整？[y/n/补充]</output>

<action>等待用户确认</action>

</step>

<step n="2.6" goal="汇总场景清单并确认">

<action>汇总所有识别的场景</action>
<action>按优先级排序</action>
<action>生成场景统计</action>

<output>**场景清单汇总 📋**

**场景统计:**
| 场景类型 | 数量 | 优先级 |
|---------|-----|-------|
| 正常流程 | {{happy_path_count}} | P0 |
| 业务异常 | {{business_exception_count}} | P1 |
| 系统异常 | {{system_exception_count}} | P1 |
| 边界测试 | {{boundary_count}} | P1 |
| 并发/幂等 | {{concurrency_count}} | P2 |
| 性能测试 | {{performance_count}} | P2 |
| **总计** | **{{total_count}}** | - |

**完整场景清单:**
{{full_scenario_list}}

请最终确认场景清单：
1. 确认并继续生成报告
2. 添加更多场景
3. 移除某些场景
4. 调整优先级

您的选择：</output>

<action>等待用户最终确认</action>

</step>

<step n="2.7" goal="保存场景识别结果">

<action>更新状态文件:
- 存储 scenarios_identified 完整列表
- 存储 scenario_statistics
- 添加到 completed_phases
- 更新 last_updated 时间戳
</action>

<output>**✓ 场景识别阶段完成**

**已识别场景: {{total_count}} 个**

准备进入报告生成阶段？[y/n]</output>

<action>等待用户确认</action>

<check if="用户确认继续">
  <action>设置 scenario_identification_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
