# 知识补充子工作流

<workflow>

<critical>此工作流执行 DDD 建模后的深度复盘与知识传授（阶段 4）</critical>
<critical>调用者: ../JL-Design-DDD/instructions.md 路由器</critical>
<critical>处理: knowledge_supplement 阶段</critical>
<critical>角色: 资深架构师导师 & 行业领域专家</critical>

<step n="4.1" goal="加载建模产物与上下文">

<action>加载以下资源：
- {inputs.output_dir}/DDD_Design_*.md (最新的 DDD 建模报告)
- {inputs.output_dir}/Requirements_Design_*.md (产研设计文档)
- 当前的业务领域上下文
</action>

<output>**准备进行架构复盘与知识传授**

已加载 DDD 建模报告和产研设计文档。

接下来的任务：
我将从“导师”和“行业专家”的角度，为您生成一份**《架构决策与领域知识指南》**。
这不仅是对当前设计的解释，更是为了帮助团队理解设计背后的权衡（Trade-offs）和行业最佳实践。

是否开始生成？[y/n]</output>

<action>等待用户确认</action>
<check if="用户确认">
  <action>继续到步骤 4.2</action>
</check>

</step>

<step n="4.2" goal="生成《架构决策与领域知识指南》">

<action>角色切换：暂停“建模构建者”角色，切换为 **"资深架构师导师 & 行业领域专家"**。</action>

<action>分析当前的 DDD 模型，结合业务场景，生成以下内容：

## 第一章：设计辩护 (Design Defense) —— 为什么要这么建？
1. **边界划分逻辑**：分析限界上下文划分的依据（业务职能/变更频率/团队结构？）。解释为什么某些上下文没有合并。
2. **聚合根的选择**：解释核心聚合根的选择理由。它维护了什么核心的不变性（Invariants）？
3. **取舍与妥协**：揭示建模中的 Trade-offs。为了简洁性或性能，我们牺牲了什么？（如：引入数据冗余、牺牲强一致性等）。

## 第二章：行业领域雷达 (Domain Insight) —— 专家通过什么看透本质？
*假设用户是刚进入该领域的高级开发*
1. **核心暗知识**：揭示行业内“外行容易忽视，但内行必须死守”的业务规则或潜规则。
2. **通用语言 (Ubiquitous Language)**：列出 3-5 个容易产生歧义的术语，给出标准定义。
3. **避坑指南**：列举过往行业实践中，最常见的失败建模案例。

## 第三章：拓展与进阶 (Advanced Extensions)
1. **技术落地映射**：推荐工程实践（如 Java/Go 的包结构映射、Interface 定义）。
2. **未来演进**：预测业务量增长 10 倍或引入复杂新功能时，哪些模型会率先崩塌？推荐什么应对模式（CQRS, Event Sourcing 等）。
</action>

<critical>语气要求：循循善诱、专业、客观。多用对比（VS）、表格和具体业务例子，避免空泛理论。</critical>

<output>**正在生成知识指南...**</output>

<action>执行生成逻辑...</action>

<output>**《架构决策与领域知识指南》草稿**

---

{{knowledge_guide_content}}

---

请审阅这份指南。这是否解答了您对模型设计的疑问？是否需要针对某个特定点进行更深入的讲解？

[确认保存 / 补充提问]</output>

<action>等待用户确认</action>

<loop while="用户有补充提问">
  <action>针对用户的提问进行深度解答</action>
  <action>将解答追加到指南中</action>
  <output>**已补充解答**... 是否确认？[y/继续提问]</output>
  <action>等待用户确认</action>
</loop>

</step>

<step n="4.3" goal="保存指南并完成工作流">

<action>将《架构决策与领域知识指南》追加到 DDD 建模报告的末尾，或者保存为单独的文件。</action>
<action>这里选择保存为单独文件，以便于分享和阅读。</action>

<action>生成文件名: Architecture_Knowledge_Guide_{{timestamp}}.md</action>
<action>保存文件到: {inputs.output_dir}/Architecture_Knowledge_Guide_{{timestamp}}.md</action>

<action>更新状态文件:
- 添加到 completed_phases: {"phase": "knowledge_supplement", "status": "completed", "timestamp": "{{now}}", "output": "Architecture_Knowledge_Guide_{{timestamp}}.md"}
</action>

<output>**✓ 知识指南已保存**

文件位置: {inputs.output_dir}/Architecture_Knowledge_Guide_{{timestamp}}.md

**✓ 知识补充阶段已完成。**

您现在拥有了：
1. **产研统一设计文档** (Requirements Design)
2. **DDD 建模报告** (DDD Design)
3. **架构决策与领域知识指南** (Knowledge Guide)

接下来即将进入 TDD 代码实现阶段。</output>

<action>设置 knowledge_supplement_completed = true</action>
<action>返回主工作流路由器完成流程</action>

</step>

</workflow>
