# 阶段 3: 可验证的数据库迁移 (Verifiable DB Migration)

<workflow>
<critical>目标: 确保新数据库结构正确，数据完整迁移。</critical>

<step n="3.1" goal="DDL 转换与 Schema 验证">
  <ask>是否需要进行数据库引擎变更？(e.g. MySQL -> PG) [y/n]</ask>
  
  <check if="y">
    <action>读取旧数据库 DDL</action>
    <action>生成目标数据库 DDL (处理类型映射、自增主键、索引差异)</action>
    <output>
    **DDL 转换预览**:
    ```sql
    {{ddl_preview}}
    ```
    请审查 DDL，特别是类型转换部分。[确认 / 修改]</output>
  </check>
  
  <check if="n">
    <action>复制旧 DDL</action>
    <output>使用原有 Schema 结构。</output>
  </check>
</step>

<step n="3.2" goal="数据迁移与校验脚本">
  <action>生成数据迁移脚本 (Python 或 SQL)</action>
  <action>生成**数据校验脚本**：
  1. **Count Check**: 验证总行数一致
  2. **Sum Check**: 验证关键数值字段（金额、数量）总和一致
  3. **Sample Check**: 随机抽取 10 条数据比对所有字段
  </action>
  
  <output>
  已生成迁移与校验脚本：
  - `migration_script.sql`
  - `verify_data.py`
  
  请在测试环境执行迁移，并运行校验脚本。
  
  **校验结果必须为 PASS 才能继续。**
  [确认校验通过 / 校验失败]</output>
  
  <check if="确认校验通过">
    <action>设置 db_migration_completed = true</action>
  </check>
</step>
</workflow>
