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

将生成以下内容：
1. 测试场景概览表
2. 业务逻辑与流程图
3. 执行脚本集（cURL/SQL/Python）

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

流程图生成完成，继续生成执行脚本？[y/n]</output>

</step>

<step n="3.4" goal="生成接口请求脚本 (cURL)">
<critical>数据脱敏：严禁出现真实密钥、手机号或内网 IP，统一使用 {{PLACEHOLDER}} 替代</critical>

<action>为每个测试场景生成 cURL 请求脚本：
- 完整的请求 URL（使用占位符）
- 请求头（Content-Type, Authorization）
- 请求体（JSON 格式）
- 注释说明
</action>

<output>**接口请求脚本 🔧**

## 3. 执行脚本集 (Execution Scripts)

### A. 接口请求 (cURL)

#### TC-001: {{scenario_name}}
```bash
# {{scenario_description}}
curl -X {{method}} "{{base_url}}/{{api_path}}" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer {{token}}" \
  -d '{
    {{request_body}}
  }'
# 预期响应: {{expected_response}}
```

{{#each curl_scripts}}
#### {{scenario_id}}: {{scenario_name}}
```bash
{{script}}
```
{{/each}}

cURL 脚本生成完成，继续生成 SQL 脚本？[y/n]</output>

</step>

<step n="3.5" goal="生成数据准备与校验脚本 (SQL)">

<action>生成数据准备脚本：
- 前置数据清理
- 测试数据插入
- 状态初始化
</action>

<action>生成数据校验脚本：
- 执行后数据查询
- 状态校验
- 关联数据校验
</action>

<output>**数据准备与校验脚本 🗄️**

### B. 数据准备与校验 (SQL)

#### 前置准备
```sql
-- 1. 前置清理（测试环境）
DELETE FROM {{table_name}} WHERE batch_no = 'TEST_{{timestamp}}';

-- 2. 测试数据准备
INSERT INTO {{table_name}} ({{columns}})
VALUES ({{values}});
```

#### 结果校验
```sql
-- 1. 状态校验
SELECT status, {{key_columns}} 
FROM {{table_name}} 
WHERE {{condition}};
-- 预期: {{expected_result}}

-- 2. 关联数据校验
{{additional_validations}}
```

SQL 脚本生成完成，继续生成自动化测试脚本？[y/n]</output>

</step>

<step n="3.6" goal="生成自动化验证脚本 (Python)">

<action>生成 Python 自动化测试脚本：
- 导入必要的库
- 测试函数定义
- 请求发送
- 断言验证
- 清理操作
</action>

<output>**自动化验证脚本 🐍**

### C. 自动化验证 (Python)

```python
import requests
import json
from typing import Dict, Any

# 配置
BASE_URL = "{{BASE_URL}}"
HEADERS = {
    "Content-Type": "application/json",
    "Authorization": "Bearer {{TOKEN}}"
}

{{#each test_functions}}
def {{function_name}}() -> bool:
    """
    {{description}}
    场景ID: {{scenario_id}}
    """
    url = f"{BASE_URL}/{{api_path}}"
    payload = {{payload}}
    
    # 1. 发起请求
    resp = requests.{{method}}(url, json=payload, headers=HEADERS)
    
    # 2. 校验响应状态
    assert resp.status_code == {{expected_status}}, f"状态码错误: {resp.status_code}"
    
    # 3. 校验响应内容
    data = resp.json()
    {{assertions}}
    
    print(f"{{scenario_id}} Passed!")
    return True

{{/each}}

def run_all_tests():
    """执行所有测试"""
    results = {}
    {{#each test_functions}}
    try:
        results["{{scenario_id}}"] = {{function_name}}()
    except AssertionError as e:
        results["{{scenario_id}}"] = f"FAILED: {e}"
    {{/each}}
    
    # 输出结果汇总
    print("\n" + "="*50)
    print("测试结果汇总")
    print("="*50)
    for scenario, result in results.items():
        status = "✓ PASS" if result == True else f"✗ {result}"
        print(f"{scenario}: {status}")

if __name__ == "__main__":
    run_all_tests()
```

自动化脚本生成完成？[y/确认]</output>

</step>

<step n="3.7" goal="整合完整报告">

<action>将所有部分整合为完整的场景测试报告</action>
<action>执行自我审阅：
- 检查数据脱敏是否完整
- 检查脚本语法是否正确
- 检查场景覆盖是否完整
</action>

<action>以"# 场景测试报告"作为开头输出完整报告</action>

<output>**报告整合完成 ✓**

**报告结构预览:**
- ✓ 测试场景概览 ({{scenario_count}} 个场景)
- ✓ 业务逻辑与流程 (含时序图)
- ✓ cURL 请求脚本 ({{curl_count}} 个)
- ✓ SQL 数据脚本 (准备 + 校验)
- ✓ Python 自动化脚本

**自我审阅结果:**
- 数据脱敏: {{desensitization_check}}
- 脚本语法: {{syntax_check}}
- 场景覆盖: {{coverage_check}}

准备保存报告？[y/n]</output>

</step>

<step n="3.8" goal="保存报告并完成">

<action>生成时间戳: {{timestamp}}</action>
<action>保存报告到: {inputs.output_dir}/Scenario_Test_Case_{{timestamp}}.md</action>
<action>验证文档保存成功</action>

<action>更新状态文件:
- 添加到 completed_phases: {"phase": "report_generation", "status": "completed", "timestamp": "{{now}}", "output": "Scenario_Test_Case_{{timestamp}}.md"}
- 更新 current_phase = "completed"
- 更新 last_updated 时间戳
</action>

<output>**✓ 场景测试报告已保存**

**文件位置**: {inputs.output_dir}/Scenario_Test_Case_{{timestamp}}.md

**报告统计:**
- 测试场景: {{total_scenarios}} 个
- P0 场景: {{p0_count}} 个
- P1 场景: {{p1_count}} 个
- P2 场景: {{p2_count}} 个

是否需要修改报告？[y/修改意见/完成]</output>

<action>等待用户最终确认</action>

<check if="用户要求修改">
  <action>根据反馈修改报告</action>
  <action>重新保存报告</action>
</check>

<check if="用户确认完成">
  <action>设置 report_generation_completed = true</action>
  <action>返回主工作流路由器完成工作流</action>
</check>

</step>

</workflow>
