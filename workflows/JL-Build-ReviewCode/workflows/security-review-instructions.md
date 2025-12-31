# 安全性检查子工作流

<workflow>

<critical>此工作流执行安全性检查（阶段 3）</critical>
<critical>调用者: ../JL-Build-ReviewCode/instructions.md 路由器</critical>
<critical>处理: security_review 阶段</critical>

<step n="3.1" goal="加载安全检查规范">

<action>加载以下资源：
- 当前上下文中的代码
- OWASP Top 10 检查项
- 安全编码最佳实践
- 前序检查结果
</action>

<check if="resume_mode == true">
  <action>加载之前的检查进度</action>
  <action>显示已完成的检查项</action>
</check>

<output>**安全性检查准备就绪 ✓**

将进行以下安全检查:
1. OWASP Top 10 漏洞检测
2. 输入验证检查
3. 敏感数据处理检查
4. 会话管理检查

开始安全性检查？[y/n]</output>

</step>

<step n="3.2" goal="OWASP Top 10 检查">

<action>检查常见安全漏洞：
- SQL 注入
- XSS 跨站脚本
- CSRF 跨站请求伪造
- 不安全的反序列化
- 敏感数据泄露
</action>

<output>**OWASP Top 10 检查 🔐**

### SQL 注入检测

{{#each sql_injection_risks}}
**🔴 高风险** Line {{line}}
```java
{{code_snippet}}
```
- 风险: 可能存在 SQL 注入
- 建议: 使用参数化查询或预编译语句
```java
{{safe_code}}
```
{{/each}}

### XSS 检测

{{#each xss_risks}}
**{{severity}}** Line {{line}}
- 风险: {{description}}
- 建议: {{suggestion}}
{{/each}}

### 其他 OWASP 风险

{{#each other_owasp_risks}}
- **{{risk_type}}**: {{description}} (Line {{line}})
{{/each}}

**OWASP 检查结果**: {{owasp_issues_count}} 个风险点

继续输入验证检查？[y/确认]</output>

</step>

<step n="3.3" goal="输入验证检查">

<action>检查输入验证：
- 外部输入验证
- 参数边界检查
- 类型转换安全
- 文件上传安全
</action>

<output>**输入验证检查 ✅**

### 缺失的验证

{{#each missing_validations}}
**{{severity}}** `{{method_name}}`
- 参数: `{{parameter}}`
- 问题: 缺少{{validation_type}}验证
- 建议:
```java
{{suggested_validation}}
```
{{/each}}

### 边界检查问题

{{#each boundary_issues}}
- Line {{line}}: {{description}}
{{/each}}

**输入验证问题**: {{validation_issues_count}} 个

继续敏感数据检查？[y/确认]</output>

</step>

<step n="3.4" goal="敏感数据处理检查">

<action>检查敏感数据处理：
- 密码存储方式
- 敏感数据日志
- 传输加密
- 密钥管理
</action>

<output>**敏感数据处理检查 🔑**

### 密码处理

{{#each password_issues}}
**{{severity}}** Line {{line}}
- 问题: {{issue}}
- 建议: {{suggestion}}
{{/each}}

### 敏感信息泄露风险

{{#each sensitive_data_leaks}}
**⚠️** Line {{line}}: 可能泄露 `{{data_type}}`
- 当前: `{{current_code}}`
- 建议: `{{suggested_code}}`
{{/each}}

### 加密使用检查

{{#each crypto_issues}}
- {{description}}
{{/each}}

**敏感数据问题**: {{sensitive_issues_count}} 个

继续会话管理检查？[y/确认]</output>

</step>

<step n="3.5" goal="会话管理检查">

<action>检查会话管理：
- 会话超时设置
- 会话固定防护
- Cookie 安全配置
- 认证授权检查
</action>

<output>**会话管理检查 🎫**

### 会话配置检查

| 配置项 | 当前状态 | 建议 |
|-------|---------|-----|
| 会话超时 | {{session_timeout}} | {{timeout_suggestion}} |
| HttpOnly | {{http_only}} | 应启用 |
| Secure | {{secure_flag}} | 生产环境应启用 |
| SameSite | {{same_site}} | 建议设置为 Strict/Lax |

### 认证授权问题

{{#each auth_issues}}
**{{severity}}** {{description}}
- 位置: {{location}}
- 风险: {{risk}}
- 建议: {{suggestion}}
{{/each}}

**会话管理问题**: {{session_issues_count}} 个</output>

</step>

<step n="3.6" goal="汇总安全性检查结果">

<action>计算安全性得分:
- 基础分: 100
- 每个高风险漏洞: -25
- 每个中风险问题: -10
- 每个低风险问题: -5
</action>

<output>**安全性检查汇总 📊**

## 检查结果

| 检查项 | 状态 | 风险数 | 严重程度 |
|-------|-----|-------|---------|
| OWASP Top 10 | {{owasp_status}} | {{owasp_count}} | {{owasp_severity}} |
| 输入验证 | {{validation_status}} | {{validation_count}} | {{validation_severity}} |
| 敏感数据 | {{sensitive_status}} | {{sensitive_count}} | {{sensitive_severity}} |
| 会话管理 | {{session_status}} | {{session_count}} | {{session_severity}} |

**安全性得分**: {{security_score}}/100

{{#if has_critical_security}}
⚠️ **警告**: 发现 {{critical_security_count}} 个高风险安全问题，强烈建议修复后再合并！
{{/if}}

问题详细列表已记录，继续生成审查报告？[y/n]</output>

<action>更新状态文件:
- 存储 security_findings
- 存储 security_score
- 更新 last_updated 时间戳
</action>

<check if="用户确认继续">
  <action>设置 security_review_completed = true</action>
  <action>返回主工作流路由器继续下一阶段</action>
</check>

</step>

</workflow>
