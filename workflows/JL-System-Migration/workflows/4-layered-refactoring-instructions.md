# 阶段 4: 分层代码重构 (Layered Code Refactoring)

<workflow>
<critical>目标: 使用 COLA 架构重构代码，严格遵循 TDD 流程。</critical>

<step n="4.1" goal="基础设施层重构 (Infra Layer)">
  <action>生成数据对象 (DO) 和 MyBatis Mapper/Repository Impl</action>
  <action>生成 Infra 层集成测试：连接 Step 3 的新 DB，执行 CRUD</action>
  
  <output>
  **Infra 层代码预览**:
  - `OrderDO.java`
  - `OrderRepositoryImpl.java`
  - `InfraIntegrationTest.java`
  
  请运行集成测试。
  [测试通过 / 失败]</output>
</step>

<step n="4.2" goal="领域层重构 (Domain Layer)">
  <action>生成纯净的领域实体 (Entity) 和聚合根 (Aggregate)</action>
  <action>生成值对象 (Value Object)</action>
  <action>定义 Repository 接口 (Gateway)</action>
  <action>生成转换器 (Entity <-> DO)</action>
  
  <output>
  **Domain 层代码预览**:
  - `Order.java` (Entity)
  - `Address.java` (ValueObject)
  - `OrderGateway.java` (Interface)
  
  请确认领域模型不依赖 Spring/MyBatis 等框架。
  [确认 / 修改]</output>
</step>

<step n="4.3" goal="应用层与业务逻辑迁移 (App & Client Layer)">
  <critical>核心步骤：业务逻辑零偏差迁移</critical>
  <action>生成 Command/Query 对象 (DTO)</action>
  <action>生成 Application Service / Executor</action>
  <action>**迁移业务逻辑**: 将旧 Service 代码逻辑“逐行语义迁移”到 App Service 或 Domain Entity</action>
  <action>**连接黄金测试集**: 将 Step 2 生成的单元测试适配到新代码上</action>
  
  <output>
  **业务逻辑迁移完成**:
  - `OrderCreateCmdExe.java`
  
  **关键验证**:
  请运行“黄金标准单元测试”，必须全绿 (Green)。
  如果有测试失败，说明逻辑存在偏差。
  
  [测试全部通过 / 仍有失败]</output>
  
  <loop while="仍有失败">
    <action>分析失败原因，修正业务代码</action>
    <output>代码已修正，请再次运行测试。[测试全部通过 / 仍有失败]</output>
  </loop>
  
  <check if="测试全部通过">
    <action>设置 layered_refactoring_completed = true</action>
  </check>
</step>
</workflow>
