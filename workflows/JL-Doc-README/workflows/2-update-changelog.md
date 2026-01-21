# 更新变更日志工作流

<workflow>
<critical>目标: 生成语义化的版本更新日志</critical>

<step n="4.1.1" goal="提取变更点">
  <action>分析【产研设计文档】，提取:
  - 新增功能 (Added)
  - 业务规则变更 (Changed)
  </action>
  
  <action>分析【代码审查报告】，提取:
  - 修复的 Bug (Fixed)
  - 性能优化 (Optimized)
  </action>
  
  <action>分析【数据库/API变更】，提取:
  - 兼容性影响 (Impact)
  </action>
</step>

<step n="4.1.2" goal="生成日志文件">
  <action>基于 {templates.changelog} 渲染内容</action>
  <action>生成文件: `docs/CHANGELOG/{{version}}-{{summary}}.md`</action>
  
  <output>
  **CHANGELOG 预览**:
  {{changelog_preview}}
  
  确认生成？[y/n]
  </output>
  
  <check if="y">
    <action>写入文件</action>
  </check>
</step>

<step n="4.1.3" goal="更新 README 链接">
  <action>更新 README.md 中的“最新版本”或相关链接指向新的 CHANGELOG</action>
</step>

</workflow>
