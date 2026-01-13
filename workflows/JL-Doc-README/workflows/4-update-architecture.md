# 更新架构文档工作流

<workflow>
<critical>目标: 保持架构文档与代码实现一致</critical>

<step n="4.3.1" goal="更新架构图">
  <action>检查是否有新的 DDD 建模报告</action>
  <check if="有新报告">
    <action>从 DDD 报告中提取 C4 Context 和 Container 图</action>
    <output>
    **架构图更新**:
    即将用 DDD 报告中的 C4 图更新 `docs/ARCHITECTURE.md`。
    [确认 / 跳过]
    </output>
    <check if="确认">
      <action>更新 Mermaid 图表代码</action>
    </check>
  </check>
</step>

<step n="4.3.2" goal="更新架构决策 (ADR)">
  <action>分析代码审查报告中的“架构设计检查”部分</action>
  <action>如果有新的重大决策（如引入新中间件、分层调整），追加到 ADR 表格中</action>
</step>

<step n="4.3.3" goal="更新技术债">
  <action>从迁移上下文报告或代码审查报告中提取新的 Tech Debt</action>
  <action>更新文档中的“技术/逻辑债务”章节</action>
  <output>✓ 架构文档已更新。</output>
</step>

</workflow>
