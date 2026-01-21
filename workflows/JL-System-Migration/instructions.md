# 遗留系统现代化迁移路由器
name: "JL-System-Migration"
version: "1.0.0"
description: "主流程控制器，协调 5 个阶段的迁移工作"
author: "JL"

<critical>工作流执行引擎受控于: {project-root}/_bmad/core/tasks/workflow.xml</critical>
<critical>您必须已经加载并处理: {project-root}/_bmad/bmm/workflows/JL-System-Migration/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow>

<step n="1" goal="初始化与模式选择">

<action>检查输出目录是否存在: {inputs.output_dir}</action>
<action>如果目录不存在，创建目录</action>

<action>检查是否存在状态文件: {inputs.output_dir}/migration-state.json</action>

<check if="migration-state.json 存在">
  <action>读取状态文件</action>
  <ask>检测到进行中的迁移任务（阶段: {{current_phase}}，更新于: {{last_updated}}）。
  
  请选择:
  1. **恢复迁移** - 从 {{current_phase}} 继续
  2. **重新开始** - 归档旧任务并重新开始
  
  您的选择 [1/2]:</ask>
  
  <check if="用户选择 1">
    <action>设置 resume_mode = true</action>
    <action>设置 current_phase = {{state.current_phase}}</action>
    <action>跳转到对应阶段</action>
  </check>
  
  <check if="用户选择 2">
    <action>归档旧状态文件</action>
    <action>设置 resume_mode = false</action>
    <action>初始化新状态文件: {"current_phase": "scope_analysis", "completed_phases": []}</action>
    <action>跳转到 Step 2 (Scope Analysis)</action>
  </check>
</check>

<check if="migration-state.json 不存在">
  <action>初始化状态文件: {"current_phase": "scope_analysis", "completed_phases": [], "started_at": "{{now}}"}</action>
  <action>跳转到 Step 2 (Scope Analysis)</action>
</check>

</step>

<step n="2" goal="阶段 1: 领域边界识别 (Scope Analysis)" if="current_phase == 'scope_analysis'">
  <action>加载并执行: {installed_path}/workflows/1-scope-analysis-instructions.md</action>
  
  <check if="scope_analysis_completed == true">
    <action>更新状态文件: current_phase = "golden_test"</action>
    <action>添加到 completed_phases: "scope_analysis"</action>
    
    <output>
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ✓ 阶段 1：边界分析完成
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    **📊 迁移进度看板**
    ```mermaid
    gantt
        title 遗留系统迁移进度
        dateFormat YYYY-MM-DD
        section 阶段
        边界识别       :done,    p1, {{timestamps.started}}, 1d
        黄金测试集     :active,  p2, after p1, 2d
        DB迁移        :         p3, after p2, 1d
        分层重构       :         p4, after p3, 3d
        E2E验证       :         p5, after p4, 1d
    ```

    **下一步计划:**
    即将进入 **建立黄金标准测试集** 阶段。
    - 生成 JUnit 5 单元测试
    - 定义业务契约 (Gherkin)
    - 录制行为数据
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    </output>
    <action>继续到 Step 3</action>
  </check>
</step>

<step n="3" goal="阶段 2: 建立黄金标准测试集 (Golden Master)" if="current_phase == 'golden_test'">
  <action>加载并执行: {installed_path}/workflows/2-golden-test-gen-instructions.md</action>
  
  <check if="golden_test_completed == true">
    <action>更新状态文件: current_phase = "db_migration"</action>
    <action>添加到 completed_phases: "golden_test"</action>

    <output>
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ✓ 阶段 2：黄金测试集建立完成
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    **📊 迁移进度看板**
    ```mermaid
    gantt
        title 遗留系统迁移进度
        dateFormat YYYY-MM-DD
        section 阶段
        边界识别       :done,    p1, {{timestamps.started}}, 1d
        黄金测试集     :done,    p2, after p1, 2d
        DB迁移        :active,  p3, after p2, 1d
        分层重构       :         p4, after p3, 3d
        E2E验证       :         p5, after p4, 1d
    ```

    **下一步计划:**
    即将进入 **可验证数据库迁移** 阶段。
    - DDL 转换 (MySQL -> PG/TiDB)
    - 生成数据校验脚本
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    </output>
    <action>继续到 Step 4</action>
  </check>
</step>

<step n="4" goal="阶段 3: 可验证数据库迁移 (DB Migration)" if="current_phase == 'db_migration'">
  <action>加载并执行: {installed_path}/workflows/3-db-migration-instructions.md</action>
  
  <check if="db_migration_completed == true">
    <action>更新状态文件: current_phase = "layered_refactoring"</action>
    <action>添加到 completed_phases: "db_migration"</action>
    
    <output>
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ✓ 阶段 3：数据库迁移准备完成
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    **📊 迁移进度看板**
    ```mermaid
    gantt
        title 遗留系统迁移进度
        dateFormat YYYY-MM-DD
        section 阶段
        边界识别       :done,    p1, {{timestamps.started}}, 1d
        黄金测试集     :done,    p2, after p1, 2d
        DB迁移        :done,    p3, after p2, 1d
        分层重构       :active,  p4, after p3, 3d
        E2E验证       :         p5, after p4, 1d
    ```

    **下一步计划:**
    即将进入 **分层代码重构** 核心阶段。
    我们将按照 COLA 架构分层顺序，结合 TDD 进行重构：
    1. Infra 层 (DO/Repository)
    2. Domain 层 (Entity/Aggregate)
    3. App/Client 层 (Cmd/Query/DTO)
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    </output>
    <action>继续到 Step 5</action>
  </check>
</step>

<step n="5" goal="阶段 4: 分层代码重构 (Layered Refactoring)" if="current_phase == 'layered_refactoring'">
  <action>加载并执行: {installed_path}/workflows/4-layered-refactoring-instructions.md</action>
  
  <check if="layered_refactoring_completed == true">
    <action>更新状态文件: current_phase = "e2e_verification"</action>
    <action>添加到 completed_phases: "layered_refactoring"</action>
    
    <output>
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ✓ 阶段 4：分层代码重构完成
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    **📊 迁移进度看板**
    ```mermaid
    gantt
        title 遗留系统迁移进度
        dateFormat YYYY-MM-DD
        section 阶段
        边界识别       :done,    p1, {{timestamps.started}}, 1d
        黄金测试集     :done,    p2, after p1, 2d
        DB迁移        :done,    p3, after p2, 1d
        分层重构       :done,    p4, after p3, 3d
        E2E验证       :active,  p5, after p4, 1d
    ```

    **下一步计划:**
    即将进入 **端到端集成验证** 阶段。
    - 启动影子系统
    - 执行黑盒回归测试 (对比 Golden Dataset)
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    </output>
    <action>继续到 Step 6</action>
  </check>
</step>

<step n="6" goal="阶段 5: 端到端集成验证 (E2E Verification)" if="current_phase == 'e2e_verification'">
  <action>加载并执行: {installed_path}/workflows/5-e2e-verification-instructions.md</action>
  
  <check if="e2e_verification_completed == true">
    <action>更新状态文件: current_phase = "completed"</action>
    <action>添加到 completed_phases: "e2e_verification"</action>
    <action>生成最终迁移报告</action>
    
    <action>编译看板变量:
      - {{progress_chart}}: 基于 Mermaid gantt 的进度图
      - {{total_tests}}: 黄金测试集用例总数
      - {{passed_tests}}: 通过的测试数
      - {{coverage}}: 代码覆盖率
    </action>
    
    <output>
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    🎉 **系统迁移工作流全部完成！**
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    
    **📊 迁移看板**
    
    ```mermaid
    gantt
        title 遗留系统迁移进度表
        dateFormat YYYY-MM-DD
        section 阶段
        边界识别       :done,    des1, {{timestamps.started}}, 1d
        黄金测试集     :done,    des2, after des1, 2d
        DB迁移        :done,    des3, after des2, 1d
        分层重构       :done,    des4, after des3, 3d
        E2E验证       :active,  des5, after des4, 1d
    ```
    
    **✅ 质量指标**
    - 黄金测试覆盖率: **{{coverage}}%**
    - 回归测试通过率: **{{passed_tests}}/{{total_tests}} (100%)**
    - 数据库一致性: **PASS**
    
    **📁 交付物清单**
    - [x] 迁移上下文报告
    - [x] 业务契约 & 接口契约
    - [x] 数据库迁移与校验脚本
    - [x] 影子系统源码 (COLA架构)
    - [x] E2E 验证报告
    
    请查阅输出目录: `{inputs.output_dir}`
    
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    ⚠️ **文档同步提醒**
    
    迁移工作已完成，为了保持文档体系的“鲜活”，请务必执行以下指令以更新 README 和架构文档：
    
    `JL-Doc-README`
    ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
    </output>
    <action>退出工作流</action>
  </check>
</step>

</workflow>
