# 阶段 1: 领域边界识别与依赖分析 (Scope & Context Analysis)

<workflow>
<critical>目标: 理解旧系统，划定重构范围，不写新代码。</critical>

<step n="1.1" goal="确认迁移范围">
  <ask>请提供旧系统的源码根目录 (Source Directory):</ask>
  <action>使用 LS/Glob 工具扫描目录结构</action>
  
  <ask>请确认本次迁移的**业务范围**（例如：只迁移 Order 模块，还是整个系统？）：</ask>
  <action>记录业务范围</action>
  
  <ask>请确认**目标架构**信息：
  1. JDK 版本 (e.g. 17)
  2. Spring Boot 版本 (e.g. 3.2)
  3. ORM 框架 (e.g. MyBatis-Plus)
  4. 数据库类型 (e.g. PostgreSQL)
  </ask>
  <action>记录目标架构信息</action>
</step>

<step n="1.2" goal="代码静态分析">
  <output>正在分析旧系统代码结构...</output>
  <action>读取关键 Controller, Service, DAO, Entity 代码</action>
  <action>分析 AST，生成简化的调用链 (Call Graph)</action>
  <action>识别隐藏的领域实体和值对象</action>
  <action>识别外部依赖 (Redis, MQ, Feign Clients)</action>
  <action>识别技术债 (如：SQL 注入风险, 过时 API, 复杂逻辑块)</action>
</step>

<step n="1.3" goal="生成上下文报告">
  <action>基于 {templates.migration_context} 生成《迁移上下文报告》</action>
  
  <output>
  **迁移上下文报告草稿已生成**
  
  {{context_report_preview}}
  
  请确认：
  1. 业务范围是否准确？
  2. 识别的外部依赖是否完整？
  3. 数据库 Schema 差异分析是否正确？
  
  [确认 / 修改]</output>
  
  <check if="用户确认">
    <action>保存报告到 {inputs.output_dir}/Migration_Context.md</action>
    <action>设置 scope_analysis_completed = true</action>
  </check>
</step>
</workflow>
