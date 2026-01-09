# 阶段 5: 端到端集成验证 (E2E Verification)

<workflow>
<critical>目标: 验证整个新系统的正确性。</critical>

<step n="5.1" goal="启动新系统 (影子模式)">
  <output>
  请启动新生成的模块（影子系统）。
  确保它连接的是 Step 3 准备好的新数据库。
  </output>
  <ask>新系统启动成功了吗？[y/n]</ask>
</step>

<step n="5.2" goal="执行黑盒回归测试">
  <action>读取 Step 2.3 准备的“黄金数据集” (Request/Response)</action>
  <action>生成回归测试脚本 (Python/Shell)</action>
  <action>脚本逻辑：
  1. 向新系统发送 Request
  2. 获取 Response
  3. 对比新旧 Response (忽略 timestamp 等动态字段)
  </action>
  
  <output>
  回归测试脚本已生成: `run_e2e_regression.py`
  
  请执行该脚本。
  
  **期望结果**: Pass Rate 100%
  
  [测试通过 / 发现不一致]</output>
  
  <check if="发现不一致">
    <action>分析不一致原因 (是 Bug 还是预期变更？)</action>
    <ask>是否修复代码？[y/n]</ask>
    <check if="y">
      <output>请返回 Phase 4 修复代码，然后重试。</output>
    </check>
  </check>
  
  <check if="测试通过">
    <action>生成最终迁移报告</action>
    <action>设置 e2e_verification_completed = true</action>
  </check>
</step>
</workflow>
