# 增量生成子工作流

<workflow>

<critical>此工作流执行增量场景添加</critical>
<critical>调用者: ../JL-Build-Scenario-Test-Case/instructions.md 路由器</critical>
<critical>处理: incremental 模式</critical>

<step n="I.1" goal="加载现有报告">

<action>加载最新的测试报告: {inputs.output_dir}/Scenario_Test_Case_*.md</action>
<action>加载关联的 Python 脚本: {inputs.output_dir}/CaseTest_*.py</action>

<action>解析现有报告结构：
- 提取已有场景列表
- 提取业务逻辑描述
- 提取现有 SQL/cURL 脚本
</action>

<output>**现有资源加载完成 ✓**

**场景统计:**
- 文档场景数: {{existing_total}}
- 关联脚本: {{python_script_name}} ({{has_script}})

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

<output>**新场景分析 🔍**

**识别的新场景:**
| ID | 场景名称 | 类型 | 前置条件 | 预期结果 |
|---|---------|-----|---------|---------|
{{#each new_scenarios}}
| TC-{{new_id}} | {{name}} | {{type}} | {{precondition}} | {{expected}} |
{{/each}}

确认添加这些场景？[y/n/修改]</output>

<action>等待用户确认</action>

</step>

<step n="I.3" goal="生成新场景的脚本">

<action>为新场景生成执行脚本：
- cURL 请求脚本
- SQL 数据脚本
- **Python 测试函数** (追加模式)
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

### 新增 Python 测试函数 (将追加到脚本文件)
```python
{{new_python_functions}}
```

脚本生成完成？[y/确认]</output>

</step>

<step n="I.4" goal="合并与保存">

<action>更新 Markdown 报告：
1. 在场景概览表末尾添加新场景
2. 在脚本集中添加新 SQL/cURL 脚本
</action>

<action>更新 Python 脚本文件：
1. 读取原文件
2. 在类中追加新的 test_ 方法
3. 写入更新后的文件
</action>

<output>**合并完成 ✓**

准备保存更新后的文件？[y/n]</output>

<check if="y">
    <action>备份原文件</action>
    <action>保存 Markdown 报告: {inputs.output_dir}/Scenario_Test_Case_{{timestamp}}.md</action>
    <action>保存 Python 脚本: {inputs.output_dir}/CaseTest_{{module}}_{{timestamp}}.py</action>
    
    <output>**✓ 增量更新完成**
    文件已更新。
    </output>
</check>

</step>

</workflow>
