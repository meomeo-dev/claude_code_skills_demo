# Reference Index: Skill Factory

本索引用于统一管理 Skill Factory 相关的外部权威资料与本地引用文档，配合 `SKILL.md` 的“引用与双向链接指引”使用。

更新时间（Last-Reviewed）：2025-10-20

目录（TOC）
- 1. Agent Skills 概览（Claude Docs）
- 2. 自定义 Skills（Cookbook 目录与示例）
- 3. 维护与一致性检查
- 4. FORMS（外部表单交互指引，本地）

---

## 1. Agent Skills 概览（Claude Docs）

- 源链接（Source）：https://docs.claude.com/en/docs/agents-and-tools/agent-skills/overview
- 本地摘要：`references/agent-skills-overview.md`
- 适用范围：理解 Skill 的三层加载模型（Metadata/Instructions/Resources）、文件结构、跨产品差异与限制、安全注意事项。

推荐引用位置：
- 在 SKILL.md 讨论“目录结构、加载层级、上下文预算”时，链接至本条目。

---

## 2. 自定义 Skills（Cookbook 目录与示例）

- 源链接（Source）：https://github.com/anthropics/claude-cookbooks/tree/main/skills/custom_skills
- 本地摘要：`references/custom-skills-cookbook.md`
- 适用范围：参考命名方式、目录组织、示例场景（如 applying-brand-guidelines、analyzing-financial-statements 等）。

推荐引用位置：
- 在 SKILL.md 说明“如何从零搭建与命名一个 Skill 目录”时，链接至本条目。

---

## 3. 维护与一致性检查

每次新增或更新 `references/*.md`：
- 同步更新本索引的条目（源链接、本地文件、适用范围、Last-Reviewed）。
- 检查 `SKILL.md` 中与该条目关联的段落是否需要更新链接或阐述。

Backlinks：
- [SKILL.md](./SKILL.md#引用与双向链接指引)

---

## 4. FORMS（外部表单交互指引，本地）

- 源链接（Source）：本地文档（Internal）
- 本地文件：`FORMS.md`
- 适用范围：指导 LLM Skill 与外部表单（网页/后台/SaaS）进行稳定交互的操作说明（入口、字段、校验、流程、验证码/2FA、错误与重试、安全合规、变更与回归）。
- Last-Reviewed：2025-10-20

推荐引用位置：
- 在 SKILL.md 说明 Forms 的定位与使用方式时，链接至本条目，以避免在主文档粘贴大量细节。

Backlinks：
- [SKILL.md](./SKILL.md#关于-formsmdforms)
- [FORMS.md](./FORMS.md)
