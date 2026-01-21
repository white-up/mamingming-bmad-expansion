# Java 开发规范

## 代码组织结构

### 目录结构

```Plaintext
src
├── main
│ ├── java
│ │ └── com/jlpay
│ │ └──/{_project_name_} <- Your package structure starts '{}'
│ └── resources  <- 资源文件         
├── test
└─ pom.xml  <- Maven project file
```

### 如何分包

本架构遵循 DDD（领域驱动设计）原则，**Domain 层是核心，不依赖任何技术实现**，遵守Cola架构：

1. 适配层（adapter）
   放Controller、MqListener等，负责接收外部请求（如HTTP调用）并转换为内部逻辑。
2. 接口层（client）
   定义接口（api）、通信协议对象（dto）等，adapter会调用client包下的接口。
3. 应用层（application）
   放服务编排类（apiImpl），协调多个领域对象完成业务用例，实现client包下的接口。
4. 领域层（domain）
   核心业务逻辑，包含实体（Entity）、领域服务（DomainService）、仓储接口（Gateway）。
5. 基础设施层（infrastructure）
   提供技术实现，如数据库操作（GatewayImpl）等。
6. 公共层（common）
   放一些公共类，如工具类、常量类、枚举类等，避免重复代码。

包的依赖原则：
* ✅ **允许**：`Infrastructure` 依赖 `Domain`。
* ✅ **允许**：`Application` 依赖 `Domain`。
* ❌ **严禁**：`Domain` 层依赖 `Infrastructure` 层（例如：Domain 实体不能包含 MyBatis 的 `@Table` 注解）。

分层原则：
* **顶层分层**：按照 adapter / client / application / domain / infrastructure 划分。
* **内部聚合**：在各层内部，优先按**业务域 (Domain Context)** 聚合，而非按技术类型聚合。
* *推荐*：`domain.order.model`, `domain.user.model`
* *反对*：`domain.model.order`, `domain.model.user`

详细目录结构：
```Plaintext
{_project_name_}
├── adapter
│ ├── {_domain1_} <-- 按照领域划分的目录,例如user
│ │    └── {_domain1_}Controller、{_domain1_}Listener ...
│ └── {_domain2_}
├── client
│ ├── {_domain1_}
│ │    ├── api <-- 目录存放接口（如UserApi）
│ │    └── dto <-- 目录存放通信协议对象
│ │        ├── request <-- 目录存放通信协议请求对象（如UserRequest）
│ │        └── response <-- 目录存放通信协议响应对象（如UserResponse）
│ └── {_domain2_}
│
├── application
│ ├── {_domain1_}
│ │    └── {_domain1_}ApiImpl...
│ ├── {_domain2_}
│ └── Transfer（使用MapStruct实现的转换器）
│
├── domain
│ ├── {_domain1_}
│ │    ├── model <-- 领域实体（如User）
│ │    │    ├── value <-- 目录存放值对象
│ │    │    ├── entity <-- 目录存放领域实体
│ │    │    ├── aggregate <-- 目录存放领域聚合
│ │    │    └── cmd <-- 目录存放命令对象
│ │    │── gateway <-- 目录存放仓储接口
│ │    │     └── {_domain1.1_}Gateway
│ │    └── service <-- 目录存放领域服务接口、领域服务实现类
│ │         └── {_domain1_}Service、{_domain1_}ServiceImpl、{_domain1_}Gateway
│ └── {_domain2_}
│── infrastructure
│ ├── {_domain1_} <-- 目录仓储接口实现、远程调用实现等
│ │     ├── PO <-- 目录存放持久化对象
│ │     └── gateway <-- 目录存放仓储接口
│ │          ├── repository <-- 目录存放仓储实现，如MyBatis的Mapper或外部服务接口的Client
│ │          └── {_domain1.1_}GatewayImpl
│ └── {_domain2_}
└─ common
   ├── protocol <-- 存放协议文件
   ├── config <-- 配置文件
   ├── utils <-- 工具类
   ├── constant <-- 常量类
   └── exception <-- 异常处理
```

### 命名规范
1. **类名**：PascalCase。
    * 实现类后缀：`ServiceImpl`, `GatewayImpl`。
    * 测试类后缀：`UserApiTest`。
2. **方法/变量**：camelCase。
    * 返回布尔值的方法：以 `is`, `has`, `should` 开头。
3. **常量**：UPPER_SNAKE_CASE。
4. **包名**：全部小写，单数形式 (e.g., `com.jlpay.user` 而非 `users`)。
5. **避免**：
    * 禁止使用拼音或拼音首字母。
    * 禁止无意义的缩写（如 `AbstractClass` 缩写为 `AbsCls`）。

## 对象模型与数据流转
### 对象定义

| 对象类型      | 所在层级 | 后缀                       | 描述 | 规则 |
|-----------| --- |--------------------------| --- | --- |
| **DTO**   | Client | `Request/Response/DTO`   | 数据传输对象 | 禁止包含业务逻辑。 |
| **model** | Domain | 无或 `Value/Cmd/Aggregate` | 领域实体 | 包含业务属性和**业务行为**，不依赖框架注解。 |
| **PO**    | Infra | `PO/DO`                  | 持久化对象 | 与数据库表一一对应，包含 `@Table` 等注解。 |

### 转换红线
1. **严禁透传**：`PO` 对象绝对不能出现在 `Client` 层（接口返回值）或 `Application` 层（入参）。
2. **强制转换**：必须在层与层之间进行对象转换。
    * `Infrastructure` -> `Domain`: PO 转 model。
    * `Application` -> `Infrastructure`: model 转 PO。
3. **转换工具**：推荐使用 **MapStruct** 或手写 Converter，避免逻辑中散落大量 setter/getter。

### 防腐层 (ACL)

* 当调用外部服务（Feign/Dubbo）时，必须在 `Infrastructure` 层将外部返回的 DTO 转换为我方定义的 `Domain Entity` 或 `Value Object`，防止外部模型污染内部业务。

## 编码与防御性编程规范

### 空值处理 (NPE 防御)

1. **集合返回**：返回集合（List/Set/Map）的方法，**严禁返回 null**，必须返回 `Collections.emptyList()` 等。
2. **Optional 使用**：推荐用于返回值，表示“可能不存在”。禁止作为参数或类字段。
3. **比较**：使用 `Objects.equals(a, b)` 替代 `a.equals(b)`，防止 a 为空。

### 并发处理

1. **线程创建**：**严禁**手动 `new Thread()`。必须使用全局管理的线程池。
2. **线程池**：**严禁**使用 `Executors` 类的静态方法（如 `newFixedThreadPool`，其队列无界可能导致 OOM）。必须使用 `ThreadPoolExecutor` 构造函数明确指定参数（核心数、队列大小、拒绝策略）。
3. **ThreadLocal**：必须遵循 `try-finally` 结构，在 `finally` 块中强制调用 `remove()`，防止内存泄漏。

### 复杂度控制

1. **卫语句 (Guard Clauses)**：优先判断异常/退出条件并 return，减少 `else` 嵌套。
    * *嵌套层数限制*：`if/else` 嵌套不得超过 3 层。
2. **魔法值**：**严禁**在代码中直接使用未定义的数字或字符串（如 `if (status == 1)`），必须定义为常量或枚举。
3. **大类拆分**：当一个类超过 500 行，或一个方法超过 50 行时，考虑重构（SRP 原则）。


## 异常与日志规范

### 异常处理
1. **自定义异常**：业务层统一抛出 `BizException`，包含 `ErrorCode` 和 `ErrorMessage`。
2. **吞异常**：**严禁**捕获异常后什么都不做，或只打印一行日志不处理（除非是明确的忽略逻辑）。
    * *错误*：`catch (Exception e) { e.printStackTrace(); }`
    * *正确*：`catch (Exception e) { log.error("...", e); throw new BizException(...); }`

### 日志规约
1. **工具**：统一使用 `SLF4J` 接口。
2. **格式**：**禁止**字符串拼接，必须使用占位符。
    * *正确*：`log.info("Order processed: {}", orderId);`
3. **级别**：
    * `ERROR`：必须记录堆栈信息（`log.error("msg", e)`），用于系统故障。
    * `WARN`：用于可预知的业务异常（如参数校验失败）。
    * `INFO`：关键流程节点，保留上下文（OrderId, TraceId）。
    * `DEBUG`：仅用于开发环境，生产环境禁止开启。
4. **避坑**：禁止在循环中打印日志；禁止打印超大对象（如 Base64）。

## 数据库与事务规范
1. **SQL 规范**：
    * **禁止** `SELECT *`，必须显式列出所需字段。
    * **禁止**在 SQL 中进行复杂的数学运算或逻辑判断。
2. **事务 (Transaction)**：
    * **粒度**：`@Transactional` 范围要尽可能小。
    * **RPC 隔离**：**严禁**在事务方法内部进行 HTTP/RPC 远程调用（会导致数据库连接池被耗尽）。若必须调用，应在事务外执行，或通过消息队列解耦。
3. **N+1 问题**：
    * 禁止在循环中执行数据库查询。必须改为批量查询 (`where id in (...)`) 或使用 Map 组装数据。

## 测试规范

### 单元测试 (Unit Test)

1. **范围**：重点覆盖 `Domain Service` 和 `Application Service` 中的复杂逻辑。`Controller` 和 `Repository` 简单方法可不测。
2. **工具**：JUnit 5 + Mockito。
3. **原则**：
    * **AIR 原则**：Automatic (自动化), Independent (独立), Repeatable (可重复)。
    * 不依赖 Spring 容器启动（速度快），外部依赖全部 Mock。
4. **结构**：Given (准备数据/Mock) -> When (执行调用) -> Then (断言结果/验证行为)。

### 命名与注释

* 测试类：`{TargetClass}Test`
* 测试方法：`{methodName}_{condition}_{expectedResult}`
* 例如：`calculatePrice_vipUser_shouldApplyDiscount()`


## Code Review 审查清单 (Checklist)

在提交代码前或审查他人代码时，请对照以下清单：

* [ ] **架构**：是否遵守了分层依赖原则？（Domain 层是否纯净？）
* [ ] **对象**：是否存在 PO 穿透到接口层？DTO 是否实现了序列化？
* [ ] **规范**：是否有魔法值？命名是否清晰？
* [ ] **并发**：是否安全地使用了线程池和 ThreadLocal？
* [ ] **异常**：是否吞掉了异常？日志是否包含足够上下文且无拼接？
* [ ] **数据库**：循环中是否有 SQL 查询？事务中是否有 RPC 调用？
* [ ] **逻辑**：复杂的 if-else 是否可以优化？是否有明显的空指针风险？
* [ ] **测试**：核心业务逻辑是否有对应的单测覆盖？

## 注释要求
1. 使用Javadoc注释，并遵循正确的格式和样式
2. 注释应该清晰、简单、易读，并包含必要的信息，如方法参数、返回值、异常情况、注意事项等
3. 类前注释格式
   ```java
   /**
    * @author maxiangming
    * @since {系统时间}
    * Note: {类描述}
    */
   ```
4. 方法注释格式
   ```java
   /**
    * @param {参数名} {参数类型} {参数描述}
    * @return {返回值类型} {返回值描述}
    * @throws {异常类型} {异常描述}
    * Note: {方法描述}
    */
   ```
5. 方法内注释使用行注释，外部使用文件注释
6. 实体类的每个属性都应该有注释