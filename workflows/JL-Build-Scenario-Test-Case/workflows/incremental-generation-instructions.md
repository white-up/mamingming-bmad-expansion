# 增量生成子工作流

<workflow>

<critical>此工作流执行增量场景添加</critical>
<critical>调用者: ../JL-Build-Scenario-Test-Case/instructions.md 路由器</critical>
<critical>处理: incremental 模式</critical>

<step n="I.1" goal="加载现有报告">

<action>加载最新的测试报告: {inputs.output_dir}/Scenario_Test_Case_*.md</action>
<action>解析现有报告结构：
- 提取已有场景列表
- 提取业务逻辑描述
- 提取现有脚本
</action>

<output>**现有报告加载完成 ✓**

**现有场景统计:**
| 场景类型 | 数量 |
|---------|-----|
| 正常流程 | {{existing_happy_path}} |
| 异常流程 | {{existing_exception}} |
| 边界测试 | {{existing_boundary}} |
| 其他 | {{existing_other}} |
| **总计** | **{{existing_total}}** |

**已有场景 ID 范围:** TC-001 ~ TC-{{max_id}}

请描述需要添加的新场景：
1. 新的业务场景
2. 新发现的异常路径
3. 补充的边界条件
4. 其他

您的输入：</output>

<action>等待用户输入新场景描述</action>

</step>

<step n="I.2" goal="分析新场景需求">

<action>根据用户输入，分析新场景：
- 识别场景类型
- 确定前置条件
- 定义预期结果
- 标注关键测试点
</action>

<action>检查与现有场景的关系：
- 是否有重复
- 是否有依赖
- 是否需要修改现有场景
</action>

<output>**新场景分析 🔍**

**识别的新场景:**
| ID | 场景名称 | 类型 | 前置条件 | 预期结果 |
|---|---------|-----|---------|---------|
{{#each new_scenarios}}
| TC-{{new_id}} | {{name}} | {{type}} | {{precondition}} | {{expected}} |
{{/each}}

**与现有场景关系:**
{{relationship_analysis}}

确认添加这些场景？[y/n/修改]</output>

<action>等待用户确认</action>

</step>

<step n="I.3" goal="生成新场景的脚本">

<action>为新场景生成执行脚本：
- cURL 请求脚本
- SQL 数据脚本
- Python 测试函数
</action>

<output>**新场景脚本生成 🔧**

### 新增 cURL 脚本
{{#each new_curl_scripts}}
#### TC-{{id}}: {{name}}
```bash
{{script}}
```
{{/each}}

### 新增 SQL 脚本
```sql
{{new_sql_scripts}}
```

### 新增 Python 测试函数
```python
{{new_python_functions}}
```

脚本生成完成？[y/确认]</output>

</step>

<step n="I.4" goal="合并到现有报告">

<action>将新场景合并到现有报告：
1. 在场景概览表末尾添加新场景
2. 更新业务逻辑描述（如需要）
3. 在脚本集中添加新脚本
4. 更新报告元数据
</action>

<action>生成合并后的完整报告</action>

<output>**报告合并完成 ✓**

**更新后的场景统计:**
| 场景类型 | 原有 | 新增 | 合计 |
|---------|-----|-----|-----|
| 正常流程 | {{old_hp}} | {{new_hp}} | {{total_hp}} |
| 异常流程 | {{old_ex}} | {{new_ex}} | {{total_ex}} |
| 边界测试 | {{old_bd}} | {{new_bd}} | {{total_bd}} |
| 其他 | {{old_ot}} | {{new_ot}} | {{total_ot}} |
| **总计** | **{{old_total}}** | **{{new_total}}** | **{{grand_total}}** |

准备保存更新后的报告？[y/n]</output>

</step>

<step n="I.5" goal="保存更新后的报告">

<action>备份原报告到 .archive 目录</action>
<action>保存更新后的报告（保持原文件名或使用新时间戳）</action>

<action>更新状态文件:
- 记录增量更新操作
- 更新场景统计
- 更新 last_updated 时间戳
</action>

<output>**✓ 增量更新完成**

**更新详情:**
- 原报告已备份
- 新增场景: {{new_count}} 个
- 更新后总场景: {{total_count}} 个

**文件位置**: {inputs.output_dir}/Scenario_Test_Case_{{timestamp}}.md

是否继续添加更多场景？[y/完成]</output>

<action>等待用户选择</action>

<check if="用户选择继续">
  <action>返回步骤 I.1</action>
</check>

<check if="用户选择完成">
  <action>设置 report_generation_completed = true</action>
  <action>返回主工作流路由器完成工作流</action>
</check>

</step>

</workflow>
