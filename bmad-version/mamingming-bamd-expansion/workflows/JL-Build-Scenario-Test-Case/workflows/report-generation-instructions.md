# 报告生成子工作流

<workflow>

<critical>此工作流执行场景测试报告生成（阶段 3）</critical>
<critical>调用者: ../JL-Build-Scenario-Test-Case/instructions.md 路由器</critical>
<critical>处理: report_generation 阶段</critical>

<step n="3.1" goal="加载模板和场景数据">

<action>加载以下资源：
- {templates.scenario_test_template} 场景测试报告模板
- 状态文件中的场景列表
- 上下文分析结果
</action>

<check if="resume_mode == true">
  <action>加载之前的报告生成进度</action>
  <action>显示已生成的部分</action>
</check>

<output>**报告生成准备就绪 ✓**

将生成以下两部分交付物：
1. **测试文档 (Markdown)**: 包含场景概览、业务逻辑、SQL/cURL脚本
2. **自动化测试脚本 (Python)**: 包含可执行的 End-to-End 测试逻辑

开始生成？[y/n]</output>

</step>

<step n="3.2" goal="生成测试场景概览表">

<action>按照模板格式生成测试场景概览表：
| ID | 场景名称 | 前置条件 | 预期结果 | 关键测试点 |
</action>

<action>按优先级排序：P0 → P1 → P2</action>

<output>**测试场景概览表 📊**

## 1. 测试场景概览 (Test Scenarios)

| ID | 场景名称 | 前置条件 | 预期结果 | 关键测试点 |
|---|---------|---------|---------|-----------|
{{#each scenarios}}
| {{id}} | {{name}} | {{precondition}} | {{expected}} | {{test_point}} |
{{/each}}

场景概览表生成完成，继续生成业务逻辑描述？[y/n]</output>

</step>

<step n="3.3" goal="生成业务逻辑与流程">

<action>生成业务逻辑描述：
- 核心数据流转逻辑说明
- 关键业务规则说明
- 状态转换说明
</action>

<action>生成 Mermaid 时序图：
- 正常流程时序图
- 异常流程时序图（含补偿）
</action>

<output>**业务逻辑与流程 📈**

## 2. 业务逻辑与流程 (Logic & Flow)

### 核心流程说明
{{business_logic_description}}

### 正常流程时序图
```mermaid
sequenceDiagram
{{normal_sequence_diagram}}
```

### 异常流程时序图
```mermaid
sequenceDiagram
{{exception_sequence_diagram}}
```

流程图生成完成，继续生成数据准备与验证脚本？[y/n]</output>

</step>

<step n="3.4" goal="生成执行脚本集 (SQL/cURL)">
<critical>数据脱敏：严禁出现真实密钥、手机号或内网 IP，统一使用 {{PLACEHOLDER}} 替代</critical>

<action>为每个测试场景生成 cURL 请求脚本</action>
<action>生成数据准备与校验 SQL 脚本</action>

<output>**执行脚本集 🔧**

## 3. 执行脚本集 (Execution Scripts)

### A. 接口请求 (cURL)
{{#each curl_scripts}}
#### {{scenario_id}}: {{scenario_name}}
```bash
{{script}}
```
{{/each}}

### B. 数据准备与校验 (SQL)
```sql
{{sql_scripts}}
```

Markdown文档部分生成完成。
下一步：生成独立的 Python 自动化测试脚本文件。 [y/n]</output>

</step>

<step n="3.6" goal="生成并保存自动化测试脚本 (Python)">

<action>生成 Python 自动化测试脚本内容，包含：
1. 数据准备 (通过 HTTP 调用或 SQL 执行)
2. HTTP E2E 测试逻辑
3. 断言与结果验证
</action>

<action>确定文件名: CaseTest_{{module_name}}_{{timestamp}}.py</action>

<output>**Python 脚本预览 🐍**

文件: `CaseTest_{{module_name}}_{{timestamp}}.py`

```python
import requests
import unittest
# ... (imports)

class Test{{module_name}}(unittest.TestCase):
    def setUp(self):
        # Data Preparation
        pass

    {{#each test_functions}}
    def test_{{scenario_id}}_{{function_name}}(self):
        """
        场景: {{scenario_name}}
        """
        # 1. Prepare Data
        # 2. Execute Request
        # 3. Assert Response
        pass
    {{/each}}

if __name__ == '__main__':
    unittest.main()
```

确认写入该文件？[y/n]</output>

<check if="y">
    <action>使用 tool 'Write' 将脚本内容写入磁盘: {inputs.output_dir}/CaseTest_{{module_name}}_{{timestamp}}.py</action>
    <output>✓ Python 脚本已保存。</output>
</check>

</step>

<step n="3.7" goal="整合报告与完成">

<action>将 Step 3.2, 3.3, 3.4 的内容整合为 Markdown 报告</action>
<action>在报告末尾添加对 Python 脚本的引用说明</action>

<action>以"# 场景测试报告 (QA)"作为开头输出完整报告</action>

<output>**报告整合完成 ✓**

**文档结构:**
1. 测试场景概览
2. 业务逻辑与流程
3. 执行脚本集 (cURL/SQL)
4. **自动化测试**: 请见独立脚本 `CaseTest_{{module_name}}_{{timestamp}}.py`

准备保存文档报告？[y/n]</output>

</step>

<step n="3.8" goal="保存报告并完成">

<action>保存 Markdown 报告到: {inputs.output_dir}/Scenario_Test_Case_{{timestamp}}.md</action>

<action>更新状态文件:
- 记录生成的 .md 和 .py 文件路径
- 更新 current_phase = "completed"
</action>

<output>**✓ 场景测试生成完成**

**输出文件**:
1. 文档: `{inputs.output_dir}/Scenario_Test_Case_{{timestamp}}.md` (后续将被 JL-Doc-README 归档为 QA.md)
2. 脚本: `{inputs.output_dir}/CaseTest_{{module_name}}_{{timestamp}}.py`

请运行 `JL-Doc-README` 进行文档归档。
</output>

<action>完成工作流</action>

</step>

</workflow>
