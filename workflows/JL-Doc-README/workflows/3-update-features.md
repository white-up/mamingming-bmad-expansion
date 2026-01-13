# 更新功能特性文档工作流

<workflow>
<critical>目标: 将散落的 Spec 和 TestCase 归档到功能目录</critical>

<step n="4.2.1" goal="归档需求规格">
  <action>检查【产研设计文档】(Requirements_Design_*.md)</action>
  <check if="存在">
    <action>识别所属模块 (e.g. Order)</action>
    <ask>识别到需求文档属于 {{module_name}} 模块，是否确认？[y/n]</ask>
    <action>创建目录 `docs/FEATURES/{{module_name}}/`</action>
    <action>复制文档并重命名为 `SPEC.md`</action>
  </check>
</step>

<step n="4.2.2" goal="归档测试用例">
  <action>检查【场景测试报告】(Scenario_Test_Case_*.md)</action>
  <check if="存在">
    <action>复制文档并重命名为 `TEST_CASES.md` 到对应模块目录</action>
  </check>
</step>

<step n="4.2.3" goal="更新索引">
  <action>更新 README.md 中的“核心功能”链接列表，指向新的 Feature 目录</action>
  <output>✓ 功能特性文档已归档。</output>
</step>

</workflow>
