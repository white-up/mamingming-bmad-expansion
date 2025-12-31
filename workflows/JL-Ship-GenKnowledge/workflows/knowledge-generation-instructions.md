# 知识库条目生成子工作流

<critical>此子工作流由父工作流 JL-Ship-GenKnowledge 调用</critical>
<critical>您必须已经加载: {parent_path}/workflow.yaml</critical>
<critical>所有响应使用 {communication_language} 进行沟通</critical>

<workflow phase="knowledge_generation">

<step n="1" goal="加载模板和分析输入内容">

<action>加载知识库条目模板: {templates.knowledge_template}</action>
<action>加载用户提供的问题描述: {{problem_description}}</action>
<action>加载用户提供的解决方案: {{solution_description}}</action>
<action>加载业务模块分类: {{business_module}}</action>

<action>分析输入内容，提取关键信息:
- **问题场景**: 问题发生的背景和触发条件
- **错误特征**: 错误码、错误提示（如有）
- **涉及系统**: 涉及的系统、模块、功能
- **解决步骤**: 解决问题的具体操作
- **注意要点**: 权限要求、风险提示
</action>

<action>存储提取的信息:
- {{problem_scene}} = 问题场景
- {{error_features}} = 错误特征
- {{involved_systems}} = 涉及系统
- {{solution_steps}} = 解决步骤
- {{attention_points}} = 注意要点
</action>

</step>

<step n="2" goal="生成优化标题">

<critical>标题生成是知识库条目最关键的部分，直接影响语义搜索匹配度</critical>

<action>应用标题生成规则</action>

<action>规则1 - 关键词前置:
- 将业务模块放在最前面
- 使用【】标注
- 格式: 【{{business_module}}】
</action>

<action>规则2 - 拒绝口语化:
- 删除: 请问、帮我、解决一下、怎么办、能不能
- 删除: 我想、希望、麻烦
- 转换疑问句为陈述句
</action>

<action>规则3 - 包含特征词:
- 如果有错误码，必须包含: "报错'{error_code}'"
- 如果有错误提示，必须包含提示文案
- 如果涉及特定系统，包含系统名称
</action>

<action>规则4 - 覆盖同义词:
- 在括号中补充可能的搜索关键词
- 考虑用户可能使用的不同表达方式
- 格式: （同义词1/同义词2/同义词3）
</action>

<action>生成标题:
格式: 【{{business_module}}】{{action}} + {{object}} ({{synonyms}})
</action>

<action>存储生成的标题为 {{generated_title}}</action>

<output>**标题生成结果**

**生成的标题**: {{generated_title}}

**标题分析**:
- 业务模块: 【{{business_module}}】
- 核心动作: {{action}}
- 操作对象: {{object}}
- 同义词扩展: ({{synonyms}})

标题是否准确？[确认 / 需要调整]</output>

<action>等待用户确认或调整</action>

<check if="用户要求调整">
  <ask>请提供您希望的标题调整：

1. 业务模块是否正确？
2. 核心动作是否准确？
3. 需要添加哪些同义词？

请说明调整内容:</ask>
  <action>根据用户反馈调整标题</action>
</check>

</step>

<step n="3" goal="结构化内容生成">

<action>生成「问题背景」部分</action>

<action>问题背景生成规则:
- 一句话描述问题发生的场景
- 包含触发条件
- 简洁明了，无废话
- 格式: 当[条件]时，[现象]会发生。
</action>

<action>存储问题背景为 {{problem_background}}</action>

<action>生成「解决方案」部分</action>

<action>解决方案生成规则:
- 使用编号列表（1. 2. 3.）
- 每步有加粗的步骤名称
- 每步必须以动词开头
- 常用动词: 点击、打开、选择、输入、确认、提交、联系、检查、修改
</action>

<action>步骤格式:
1. **{{步骤名称}}**: {{具体操作详情}}
2. **{{步骤名称}}**: {{具体操作详情}}
...
</action>

<action>存储解决方案为 {{solution_content}}</action>

<action>生成「注意事项」部分（如有）</action>

<action>注意事项包含:
- 权限要求（如需要特定角色/权限）
- 避坑点（操作风险、常见错误）
- 前置条件（需要先完成什么）
- 后续确认（如何验证操作成功）
</action>

<action>存储注意事项为 {{attention_content}}</action>

</step>

<step n="4" goal="去冗余化优化">

<critical>确保内容精炼，"一看就懂，上手就能做"</critical>

<action>检查并删除以下冗余内容:

**客套话删除**:
- "好的"、"收到"、"明白"
- "感谢您的反馈"
- "请稍等"、"我看一下"

**废话删除**:
- 无意义的排查过程描述
- 重复的问题确认
- 与解决方案无关的对话

**精简优化**:
- 合并重复步骤
- 简化冗长描述
- 保留关键信息
</action>

<action>验证动词开头:
- 检查每个步骤是否以动词开头
- 如果不是，重写步骤描述
</action>

<action>存储优化后的内容</action>

</step>

<step n="5" goal="组装完整条目">

<action>基于模板组装知识库条目:

```markdown
# 知识库QA条目

## {{generated_title}}

**问题背景**：
{{problem_background}}

**解决方案**：
{{solution_content}}

**注意事项**：
{{attention_content}}
```
</action>

<action>存储完整条目为 {{complete_entry}}</action>

</step>

<step n="6" goal="验证条目质量">

<action>运行质量验证检查</action>

<action>标题验证:
- [ ] 【业务模块】存在且正确
- [ ] 无口语化表达
- [ ] 包含错误特征词（如有）
- [ ] 同义词扩展存在
</action>

<action>内容验证:
- [ ] 问题背景一句话描述
- [ ] 解决方案步骤完整
- [ ] 所有步骤以动词开头
- [ ] 注意事项完善（如适用）
</action>

<action>格式验证:
- [ ] Markdown 格式正确
- [ ] 加粗格式正确
- [ ] 列表格式统一
</action>

<check if="验证通过">
  <action>设置 validation_passed = true</action>
</check>

<check if="验证未通过">
  <action>记录验证问题</action>
  <action>自动修复可修复的问题</action>
  <action>如有无法自动修复的问题，提示用户</action>
</check>

</step>

<step n="7" goal="用户确认与保存">

<output>**知识库条目预览**

---

{{complete_entry}}

---

**质量检查结果**:
{{#if validation_passed}}
✓ 所有检查通过
{{else}}
⚠️ 以下项需要注意:
{{validation_issues}}
{{/if}}

请确认:
1. 条目内容是否准确？
2. 是否需要调整任何部分？

[确认保存 / 需要调整]</output>

<action>等待用户确认</action>

<check if="用户确认保存">
  <action>生成文件名: Business_Knowledge_{{timestamp}}.md</action>
  <action>保存条目到: {inputs.output_dir}/Business_Knowledge_{{timestamp}}.md</action>
  <action>存储条目路径为 {{entry_path}}</action>
  
  <output>**条目已保存 ✓**

文件路径: {{entry_path}}

条目标题: {{generated_title}}

您可以继续生成下一个条目，或结束工作流。</output>
</check>

<check if="用户要求调整">
  <ask>请告诉我需要调整的部分:

1. 标题需要修改
2. 问题背景需要调整
3. 解决方案步骤需要修改
4. 注意事项需要补充
5. 其他（请说明）

您的选择:</ask>
  <action>根据用户反馈进行调整</action>
  <action>重新生成条目</action>
  <action>重新预览确认</action>
</check>

</step>

<step n="8" goal="完成知识库生成阶段">

<action>设置 knowledge_generation_completed = true</action>

<action>编译生成结果:
{
  "entry_path": "{{entry_path}}",
  "entry_title": "{{generated_title}}",
  "business_module": "{{business_module}}",
  "validation_passed": {{validation_passed}}
}
</action>

<action>返回控制权给父工作流</action>

</step>

</workflow>

---

## 批量生成模式

<workflow phase="batch_generation" if="batch_mode == true">

<critical>批量模式：依次处理多个问题，生成多个知识库条目</critical>

<step n="1" goal="初始化批量处理">

<action>加载待处理的问题列表: {{problems_list}}</action>
<action>设置 current_index = 0</action>
<action>设置 total_count = {{problems_list.length}}</action>

<output>**批量生成模式启动**

共有 {{total_count}} 个问题需要生成知识库条目。

我将依次处理每个问题，每个条目生成后会请您确认。

开始处理第 1 个问题...</output>

</step>

<step n="2" goal="循环处理每个问题">

<loop for="problem in problems_list">
  <action>显示: "处理第 {{current_index + 1}}/{{total_count}} 个问题"</action>
  
  <action>设置当前问题:
  - problem_description = {{problem.description}}
  - solution_description = {{problem.solution}}
  - business_module = {{problem.module}}
  </action>
  
  <action>执行单条目生成流程（步骤 1-7）</action>
  
  <action>current_index += 1</action>
  
  <check if="current_index < total_count">
    <ask>第 {{current_index}} 个条目已完成。继续处理下一个？[y/n]</ask>
    <check if="n">
      <action>显示: "批量处理暂停，已完成 {{current_index}}/{{total_count}} 个条目。"</action>
      <action>保存进度到状态文件</action>
      <action>退出循环</action>
    </check>
  </check>
</loop>

</step>

<step n="3" goal="批量处理完成">

<output>**批量生成完成 ✓**

**处理结果**:
- 总数: {{total_count}} 个
- 已完成: {{completed_count}} 个
- 跳过: {{skipped_count}} 个

**生成的条目**:
{{#each generated_entries}}
{{@index}}. {{this.title}} ({{this.path}})
{{/each}}

所有条目已保存到: {inputs.output_dir}/</output>

<action>设置 knowledge_generation_completed = true</action>

</step>

</workflow>

---

## 条目优化模式

<workflow phase="refine_entry" if="workflow_mode == refine_entry">

<critical>优化模式：对已有的知识库条目进行优化改进</critical>

<step n="1" goal="加载现有条目">

<ask>请提供需要优化的知识库条目内容（可直接粘贴）:</ask>
<action>解析现有条目结构</action>

</step>

<step n="2" goal="分析优化点">

<action>分析现有条目的问题:
- 标题是否符合规范
- 内容是否结构化
- 是否存在冗余
- 步骤是否以动词开头
</action>

<output>**条目分析结果**

**现有标题**: {{existing_title}}

**发现的问题**:
{{#each issues}}
- {{this}}
{{/each}}

**优化建议**:
{{#each suggestions}}
- {{this}}
{{/each}}

是否按建议进行优化？[是 / 否 / 自定义]</output>

</step>

<step n="3" goal="执行优化">

<action>按照标题生成规则优化标题</action>
<action>按照内容生成规则重构内容</action>
<action>执行去冗余化</action>
<action>验证优化后的条目</action>

<output>**优化后的条目**

---

{{optimized_entry}}

---

**优化对比**:
| 方面 | 优化前 | 优化后 |
|-----|-------|-------|
| 标题 | {{old_title}} | {{new_title}} |
| 结构 | {{old_structure}} | {{new_structure}} |
| 冗余度 | {{old_redundancy}} | {{new_redundancy}} |

确认保存优化后的条目？[确认 / 放弃]</output>

</step>

</workflow>
