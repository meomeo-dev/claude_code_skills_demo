Title: Agent Skills 概览（Claude Docs）
Source: https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview
Last-Reviewed: 2025-10-20
Scope: 官方概览，聚焦三层加载模型、Skill 结构、跨产品支持、限制与安全注意事项，作为元技能的权威依据。

# Agent Skills 概览（Claude Docs）

摘要
- Agent Skills 是基于文件系统的可复用能力包（instructions + resources + optional scripts），用于将通用代理专精到特定领域。
- 采用“渐进披露”设计：
  - Level 1（始终加载）：Frontmatter 元数据（name, description），轻量地进入系统提示，用于发现与触发。
  - Level 2（触发时加载）：SKILL.md 主体指令（流程、最佳实践、操作步骤）。
  - Level 3（按需加载）：附带资源与可执行脚本（FORMS.md、REFERENCE.md、scripts/* 等）。
- Claude 在触发 Skill 时通过 bash 读取文件；执行脚本时仅输出进入上下文，脚本源码本身不进入上下文窗口。

关键要点
- 必备文件：`SKILL.md`，前置 YAML 仅支持 name 与 description（长度限制：name ≤ 64，description ≤ 1024）。
- 目录结构推荐：
  - SKILL.md（主指令）
  - FORMS.md（表单/结构化输入指引，可选）
  - REFERENCE.md（集中索引与权威引用，可选）
  - references/（具体引用细化文档，本 Skill 增强项）
  - scripts/（可执行脚本，按需）
- 跨产品差异：Claude API、Claude Code、Claude.ai 对技能的发现/上传/共享范围不同；Claude Code 的技能是文件系统级，不需 API 上传。
- 约束：无网络访问、不可临时安装依赖；仅能使用容器中预装包。需在设计技能时规避网络依赖。
- 安全：仅使用可信来源的技能；谨慎对待可能包含外部抓取或工具误用的内容。

实操指引（与本 Skill 的关系）
- 在本 Skill（Skill Factory）中，将通用规范与模式抽取为可复用模板：
  - 在 SKILL.md 中仅保留“如何做”的指令，避免大段外部文档粘贴。
  - 将权威外链收敛为 `references/*.md` 摘要与要点，配合 `REFERENCE.md` 建立稳定索引。
  - 使用 Backlinks 建立双向可达，以便在上下文受限时最短路径检索到关键事实。

限制与注意事项
- 请遵守官方 frontmatter 字段与长度限制；描述应包含“做什么 + 何时用”。
- 资源文件越多，上下文预算越友好（因按需加载），但索引与指令需清晰，避免迷失。
- 若脚本涉及数据访问或文件操作，请最小化权限并在文档中明确风险与目的。

原始链接与引用
- Claude Docs（Agent Skills 概览）：https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview
- 相关：最佳实践、Quickstart、API 使用指南（可在 REFERENCE.md 中继续扩展条目）。

Backlinks
- [SKILL.md](../SKILL.md#引用与双向链接指引)
- [REFERENCE.md](../REFERENCE.md)
