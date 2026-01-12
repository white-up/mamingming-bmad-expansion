# 更新接入指南工作流

<workflow>
<critical>目标: 确保对外接口文档准确</critical>

<step n="4.4.1" goal="更新 API 定义">
  <action>检查【接口契约】(JSON Schema) 或 DDD 报告中的 API 定义</action>
  <action>如果接口有变更（新增/废弃/参数修改），更新 `docs/INTEGRATION.md`</action>
</step>

<step n="4.4.2" goal="更新时序图">
  <action>从【产研设计文档】中提取最新的业务交互时序图</action>
  <action>更新文档中的 Mermaid Sequence Diagram</action>
</step>

<step n="4.4.3" goal="更新错误码">
  <action>扫描代码中的 Exception 类或错误码枚举</action>
  <action>更新文档中的“常见错误码处理”表格</action>
</step>

</workflow>
