# 文档管理工作流路由器
name: "JL-Doc-README"
version: "1.0.0"
description: "自动化维护项目文档体系"
author: "JL"

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-Doc-README/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<step n="1" goal="初始化与模式选择">

<action>检查项目根目录是否存在 README.md</action>
<action>检查是否存在 docs/ 目录</action>

<check if="README.md 不存在 OR docs/ 不存在">
  <output>
  检测到当前项目缺少标准文档结构。
  即将进入 **初始化模式 (Init Mode)**。
  </output>
  <action>设置 current_phase = "init_structure"</action>
  <action>跳转到 Step 2</action>
</check>

<check if="文档结构完整">
  <output>
  文档结构完整。
  即将进入 **更新模式 (Update Mode)**。
  </output>
  <action>设置 current_phase = "collect_inputs"</action>
  <action>跳转到 Step 3</action>
</check>

</step>

<step n="2" goal="初始化文档结构" if="current_phase == 'init_structure'">
  <action>加载并执行: {installed_path}/workflows/1-init-project-docs.md</action>
  
  <check if="init_completed == true">
    <action>设置 current_phase = "collect_inputs"</action>
    <output>✓ 文档结构初始化完成。开始收集更新内容...</output>
    <action>继续到 Step 3</action>
  </check>
</step>

<step n="3" goal="收集输入信息 (Collect Inputs)" if="current_phase == 'collect_inputs'">
  <action>扫描输出目录，查找最近生成的关键文档：
  1. 产研设计文档 (`Requirements_Design_*.md`)
  2. 场景测试报告 (`Scenario_Test_Case_*.md`)
  3. 代码审查报告 (`Review_Report_*.md`)
  4. DDD 建模报告 (`DDD_Design_*.md`)
  </action>
  
  <output>
  **发现以下最近生成的工件**:
  {{found_artifacts_list}}
  
  请确认本次文档更新的**版本号** (e.g. v1.1.0) 和**变更主题**:
  </output>
  
  <ask>请输入版本号和变更主题 (e.g. "v1.1.0 微信支付集成"):</ask>
  <action>存储版本信息</action>
  
  <action>设置 current_phase = "update_docs"</action>
  <action>继续到 Step 4</action>
</step>

<step n="4" goal="更新文档体系" if="current_phase == 'update_docs'">
  <output>正在更新文档体系...</output>
  
  <action>执行子工作流:
  1. **更新 CHANGELOG**: {installed_path}/workflows/2-update-changelog.md
  2. **更新 FEATURES**: {installed_path}/workflows/3-update-features.md
  3. **更新 ARCHITECTURE**: {installed_path}/workflows/4-update-architecture.md
  4. **更新 INTEGRATION**: {installed_path}/workflows/5-update-integration.md
  </action>
  
  <check if="all_updates_completed == true">
    <action>设置 current_phase = "completed"</action>
    <action>继续到 Step 5</action>
  </check>
</step>

<step n="5" goal="完成与验证">
  <action>运行验证清单: {validation}</action>
  
  <output>
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  🎉 **项目文档更新完成！**
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  
  **📊 文档状态**
  - **README.md**: 已更新
  - **CHANGELOG**: 新增 {{version}}
  - **FEATURES**: 同步了 SPEC 和 TEST_CASES
  - **ARCHITECTURE**: 架构视图已刷新
  
  请查阅 `docs/` 目录并提交 Git 变更。
  ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  </output>
  <action>退出工作流</action>
</step>

</workflow>
