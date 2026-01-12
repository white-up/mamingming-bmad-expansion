# 初始化文档结构工作流

<workflow>
<critical>目标: 构建标准的项目文档骨架</critical>

<step n="2.1" goal="创建目录结构">
  <action>创建目录:
  - {inputs.docs_dir}/CHANGELOG
  - {inputs.docs_dir}/FEATURES
  </action>
</step>

<step n="2.2" goal="生成 README.md">
  <action>基于 {templates.readme} 生成 README.md</action>
  <action>自动填充:
  - 项目名称: {project_name}
  - 核心目标: (请求用户输入或从 pom.xml 提取)
  </action>
</step>

<step n="2.3" goal="生成基线文档">
  <action>生成 `docs/INTEGRATION.md` (基于模板)</action>
  <action>生成 `docs/ARCHITECTURE.md` (基于模板)</action>
  <action>生成 `docs/CHANGELOG/v0.0.0-初始化.md`:
  
  ```markdown
  # [v0.0.0] 项目初始化
  
  ## 📅 基本信息
  - **发布日期**: {{today}}
  
  ## 🚀 核心变更
  - 项目脚手架初始化
  - 文档体系建立
  ```
  </action>
</step>

<step n="2.4" goal="完成初始化">
  <output>✓ 文档骨架已创建。</output>
  <action>设置 init_completed = true</action>
</step>

</workflow>
