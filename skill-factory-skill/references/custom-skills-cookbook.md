Title: 自定义 Skills（Cookbook 目录与示例）
Source: https://github.com/anthropics/claude-cookbooks/tree/main/skills/custom_skills
Last-Reviewed: 2025-10-20
Scope: 参考官方示例库中自定义技能的命名、目录结构与场景范式，作为落地实现的灵感与规范补充。

# 自定义 Skills（Cookbook 目录与示例）

摘要
- Cookbook 提供多个可复用的技能示例目录，展示“领域动词-ing + 名词”的命名风格（如 applying-brand-guidelines、analyzing-financial-statements、creating-financial-models）。
- 每个示例目录通常包含 SKILL.md 以及可能的辅助文件，体现如何将领域流程固化为技能。

关键要点
- 命名建议：使用 gerund（动名词 ing 形式）+ 领域对象，便于表达“此技能在做什么”。
- 结构建议：
  - 将长篇参考与外部知识收敛至 REFERENCE.md / references/*（本仓库做了增强）。
  - 为需要确定性执行的操作提供 scripts/*（如校验、格式化、转换），并在 SKILL.md 引导 Claude 通过 bash 调用。
- 示例覆盖：品牌规范应用、财务报表分析、财务模型搭建等实际业务场景，可作为模板复制与裁剪。

实操指引（与本 Skill 的关系）
- 在新建技能时：
  1) 优先确定“动词-ing + 名词”的目录名；
  2) 用本仓库的 SKILL.md frontmatter 模板与“引用与双向链接指引”；
  3) 将外部资料按条目整理到 references/*.md，并在 REFERENCE.md 建索引；
  4) 最后再将操作流程写入 SKILL.md 主体，确保“方法在前、材料在后”。

限制与注意事项
- GitHub 页面会更新，请在引用时记录 Last-Reviewed 日期，并在变更较大时复核本地摘要。
- 示例并非官方规范本体，请始终以 Claude Docs 为准；本文件定位为“实践集合”的导航。

原始链接与引用
- Cookbook: https://github.com/anthropics/claude-cookbooks/tree/main/skills/custom_skills

Backlinks
- [SKILL.md](../SKILL.md#引用与双向链接指引)
- [REFERENCE.md](../REFERENCE.md)
