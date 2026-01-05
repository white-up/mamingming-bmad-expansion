# TDD 代码实现子工作流

<workflow>

<critical>此工作流执行基于 TDD 的代码实现（阶段 5）</critical>
<critical>调用者: ../JL-Design-DDD/instructions.md 路由器</critical>
<critical>处理: tdd_implementation 阶段</critical>
<critical>角色: 资深 Java 架构师 & TDD 实践专家</critical>
<critical>原则: Red-Green-Refactor, Clean Architecture, 每一步必须等待用户确认</critical>

<step n="5.1" goal="加载上下文与准备">

<action>加载以下资源：
- {inputs.output_dir}/DDD_Design_*.md (DDD 建模报告)
- {inputs.output_dir}/Requirements_Design_*.md (产研设计文档)
- Java编码规范.md (来自知识库)
</action>

<action>解析 DDD 建模报告中的【工程目录结构】树</action>
<action>提取所有待生成的 Java 类路径和名称，构建【待实现类清单】</action>

<output>**准备进入 TDD 代码实现阶段**

已加载所有设计文档和编码规范。我将作为您的**TDD 结对编程伙伴**。

我已从 DDD 报告中提取了以下待实现类清单：

{{class_list_extracted}}

请检查：是否有遗漏的辅助类、配置类或工具类？或者有不需要现在实现的类？
[确认清单 / 补充或修改清单]
</output>

<loop while="用户修改清单">
  <action>更新【待实现类清单】</action>
  <output>更新后的清单... 确认？</output>
</loop>

<output>
我们将**直接写入代码文件**，而不是生成文档。
请确认：我们将把代码写入哪个根目录？（例如：`src/main/java` 或 `{inputs.output_dir}/src`）
</output>

<ask>请输入代码生成的源码根目录（留空则默认使用 {inputs.output_dir}/src）：</ask>

<action>等待用户确认</action>
<check if="用户确认">
  <action>设置 source_root_dir = 用户输入 OR "{inputs.output_dir}/src"</action>
  <action>继续到步骤 5.2</action>
</check>

</step>

<step n="5.2" goal="Phase 1: 契约定义与核心模型 (The Contract)">

<action>基于【待实现类清单】，设计核心代码骨架</action>

<action>设计 Client 层代码：
- `client/{_domain_}/api`: 接口定义
- `client/{_domain_}/dto`: Request/Response 对象
</action>

<action>设计 Domain 层核心代码：
- `domain/{_domain_}/model`: 聚合根、实体、值对象
- `domain/{_domain_}/gateway`: Gateway 接口定义
</action>

<output>**Phase 1: 契约与模型定义**

我已根据清单设计了以下核心类（Client & Domain）：

**Client API**:
```java
{{client_api_code}}
```

**Domain Model (Aggregate)**:
```java
{{domain_model_code}}
```

**Gateway Interface**:
```java
{{gateway_interface_code}}
```

请检查：
1. 接口定义是否符合业务需求？
2. 领域模型属性是否完整？
3. 命名是否符合规范？

[确认通过并写入文件 / 修改代码]</output>

<action>等待用户确认</action>
<loop while="用户有修改意见">
  <action>调整代码</action>
  <output>**更新后的代码**... 是否确认？[y/n]</output>
  <action>等待用户确认</action>
</loop>

<check if="用户确认">
    <action>创建目录结构:
        - {source_root_dir}/client/{_domain_}/api
        - {source_root_dir}/client/{_domain_}/dto
        - {source_root_dir}/domain/{_domain_}/model
        - {source_root_dir}/domain/{_domain_}/gateway
    </action>
    <action>写入文件: {source_root_dir}/.../ClientApi.java (及其他 Phase 1 类)</action>
    <output>✓ Phase 1 代码文件已写入。</output>
</check>

</step>

<step n="5.3" goal="Phase 2: 编写测试 (The 'Red' State)">

<action>针对【待实现类清单】中的 Service 类，编写单元测试</action>
<action>使用 JUnit 5 和 Mockito</action>
<action>Mock 所有外部依赖 (Gateway)</action>

<output>**Phase 2: 编写失败测试 (Red)**

我编写了以下测试用例：

**Test Class**: `{{test_class_name}}`

```java
{{test_code}}
```

**预期行为**:
- Setup: Mock Gateway 返回预期数据
- Act: 调用 Service 方法
- Assert: 验证返回值和 Mock 交互
- **状态**: 编译通过，运行失败 (因为 Service 尚未实现)

请确认测试用例是否覆盖了关键业务场景？[确认通过并写入文件 / 修改测试]</output>

<action>等待用户确认</action>
<loop while="用户有修改意见">
  <action>调整测试代码</action>
  <output>**更新后的测试代码**... 是否确认？[y/n]</output>
  <action>等待用户确认</action>
</loop>

<check if="用户确认">
    <action>创建测试目录结构: {source_root_dir}/../test/java/...</action>
    <action>写入文件: {source_root_dir}/../test/java/.../ServiceTest.java</action>
    <output>✓ 测试代码文件已写入。</output>
</check>

</step>

<step n="5.4" goal="Phase 3: 最小化实现 (The 'Green' State)">

<action>实现业务逻辑以通过测试</action>

<action>实现 Domain Service:
- `domain/{_domain_}/service/{_domain_}ServiceImpl`
</action>

<action>实现 Application Service:
- `application/{_domain_}/{_domain_}ApiImpl` (编排)
</action>

<action>实现 Infrastructure 骨架:
- `infrastructure/{_domain_}/gateway/{_domain_}GatewayImpl`
- `infrastructure/{_domain_}/PO`
</action>

<action>定义转换器:
- `Transfer` (MapStruct) 接口
</action>

<output>**Phase 3: 最小化实现 (Green)**

我已根据【待实现类清单】完成核心逻辑实现：

**Domain Service Impl**:
```java
{{domain_service_impl_code}}
```

**Application Service Impl**:
```java
{{app_service_impl_code}}
```

**Infrastructure Gateway**:
```java
{{infra_gateway_code}}
```

这些代码旨在通过刚才的测试。
请检查业务逻辑是否正确？[确认通过并写入文件 / 修改实现]</output>

<action>等待用户确认</action>
<loop while="用户有修改意见">
  <action>调整实现代码</action>
  <output>**更新后的代码**... 是否确认？[y/n]</output>
  <action>等待用户确认</action>
</loop>

<check if="用户确认">
    <action>创建目录结构:
        - {source_root_dir}/domain/{_domain_}/service
        - {source_root_dir}/application/{_domain_}
        - {source_root_dir}/infrastructure/{_domain_}/gateway
        - {source_root_dir}/infrastructure/{_domain_}/po
    </action>
    <action>写入所有实现类文件</action>
    <output>✓ 实现代码文件已写入。</output>
</check>

</step>

<step n="5.5" goal="Phase 4: 补充测试 (Verify)">

<action>在核心功能实现后，补充边界测试和集成测试</action>

<output>**Phase 4: 补充测试**

核心功能已实现。现在我将补充测试用例以覆盖更多场景（如异常处理、边界条件）。

**Test Class Update**: `{{test_class_name}}`

```java
{{additional_test_code}}
```

请确认补充的测试用例？[确认通过并写入文件 / 修改测试]</output>

<action>等待用户确认</action>
<loop while="用户有修改意见">
  <action>调整测试代码</action>
  <output>**更新后的测试代码**... 是否确认？[y/n]</output>
  <action>等待用户确认</action>
</loop>

<check if="用户确认">
    <action>更新测试文件: {source_root_dir}/../test/java/.../ServiceTest.java</action>
    <output>✓ 补充测试代码文件已写入。</output>
</check>

</step>

<step n="5.6" goal="Phase 5: 规范检查与重构 (Refactor)">

<action>执行代码规范检查</action>

<action>检查项：
1. **依赖倒置**: 检查 import 语句，确保 Infra 依赖 Domain，Domain 不依赖 Infra。
2. **数据转换**: 检查是否使用了 MapStruct，是否存在 PO 穿透。
3. **命名规范**: 检查类名后缀、包名结构是否符合规范。
4. **日志与异常**: 检查是否使用了 SLF4J 和自定义异常。
</action>

<output>**Phase 5: 规范检查报告**

| 检查项 | 结果 | 说明 |
|-------|------|------|
| 依赖倒置 | {{dependency_check}} | {{dependency_note}} |
| PO/DTO 隔离 | {{isolation_check}} | {{isolation_note}} |
| 命名规范 | {{naming_check}} | {{naming_note}} |
| 异常处理 | {{exception_check}} | {{exception_note}} |

{{#if has_issues}}
**发现的问题**:
{{issues_list}}

我建议进行以下重构...
{{/if}}

是否进行重构或确认完成？[确认完成 / 进行重构]</output>

<action>等待用户确认</action>

<check if="用户要求重构">
  <action>执行重构逻辑</action>
  <action>展示重构后的代码</action>
  <action>再次确认</action>
  <check if="用户确认重构结果">
      <action>覆盖写入重构后的文件</action>
      <output>✓ 重构后的代码已更新。</output>
  </check>
</check>

</step>

<step n="5.7" goal="完成 TDD 阶段">

<output>**✓ TDD 代码实现完成**

我已为您生成了符合 Clean Architecture 的核心代码骨架，并已**写入到文件系统**。

**源码目录**: `{source_root_dir}`

**已生成文件**:
- Client API & DTO
- Domain Model & Gateway
- Unit Tests (JUnit 5)
- Application & Domain Service Impl
- Infrastructure Gateway Impl

您现在可以在 IDE 中打开这些文件并运行测试。

**🎉 JL-Design-DDD 工作流全线贯通！**</output>

<action>设置 tdd_implementation_completed = true</action>
<action>更新状态文件</action>
<action>返回主工作流路由器</action>

</step>

</workflow>
