# 阶段 2: 建立“黄金标准”测试集 (Golden Master Test Generation)

<workflow>
<critical>目标: 锁定旧系统行为，确保迁移后业务逻辑零偏差。</critical>

<step n="2.1" goal="生成单元测试 (Unit Tests)">
  <action>基于阶段 1 识别的核心 Service 类</action>
  <action>为旧代码生成 JUnit 5 单元测试</action>
  <action>重点覆盖：核心计算逻辑、状态流转、异常分支</action>
  
  <output>
  已为以下类生成单元测试：
  {{unit_test_list}}
  
  请运行这些测试，确保在旧系统中全部通过（Green）。
  [确认通过 / 需要调整]</output>
</step>

<step n="2.2" goal="生成集成测试与业务契约 (Integration & Contract)">
  <action>调用 JL-Build-Scenario-Test-Case 核心逻辑</action>
  <action>分析 Controller 入口，识别业务场景 (Happy Path + Edge Cases)</action>
  <action>生成 Gherkin 格式的**业务契约 (Specification)**</action>
  <action>生成 JSON 格式的**接口契约 (API Contract)**</action>
  
  <output>
  **业务契约示例**:
  ```gherkin
  Scenario: 创建订单-库存不足
    Given 商品 ID=1001 库存为 0
    When 用户发起下单请求 quantity=1
    Then 应返回错误码 "STOCK_NOT_ENOUGH"
  ```
  
  请确认业务契约是否准确描述了旧系统行为？[确认 / 修改]</output>
</step>

<step n="2.3" goal="指导行为录制 (Recording)">
  <output>
  为了进行更严格的 E2E 验证，建议录制真实流量。
  
  **推荐方案**:
  1. 使用 Postman/JMeter 针对旧系统运行 Step 2.2 生成的测试用例。
  2. 保存 Request/Response 对，存为 `golden-dataset.json`。
  
  请确认已准备好“黄金数据集”？（如果跳过，后续 E2E 验证将仅依赖生成的静态用例）
  [已准备好 / 跳过]</output>
  
  <check if="已准备好">
    <action>验证数据集格式</action>
  </check>
  
  <action>保存所有测试契约到 {inputs.output_dir}/tests/</action>
  <action>设置 golden_test_completed = true</action>
</step>
</workflow>
