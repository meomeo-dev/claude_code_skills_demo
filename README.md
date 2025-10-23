# Claude Code Skills Demo · Skill Factory

一个用于演示与孵化 Claude Code Agent Skills 的示例仓库，内置“Skill Factory”元技能，用于规范化地搭建、撰写与校验新 Skill。

核心理念：文档即产品。SKILL.md 聚焦“指令性内容”，长文规范与外部资料下沉至 references，并通过 REFERENCE.md 建立索引与双向链接；FORMS.md 专注于“LLM 与外部表单的交互说明”。

## 仓库结构

```
.
├── skill-factory-skill/
│   ├── SKILL.md        # 主指令：方法与流程
│   ├── FORMS.md        # Forms：指导 LLM 与外部表单交互（非用户-LLM 表单）
│   ├── REFERENCE.md    # 引用索引：统一管理 references/* 与外链
│   ├── references/     # 详细外部资料的本地摘要（带 Backlinks）
│   ├── scripts/        # 工具脚本
│   └── requirements.txt# Python 依赖清单
└── README.md
```

更多规范请见：`skill-factory-skill/SKILL.md`

## 快速开始（macOS + zsh）

1) 创建并激活虚拟环境（在目标 Skill 目录内）

```zsh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

2) 基于 SKILL.md 中的目录结构, 调用工具创建文件和目录.

3) 推送到仓库（可选：使用 GitHub CLI）

```zsh
git add your-skill-skill
git commit -m "feat(skill): scaffold your-skill-skill"
git push
# 或在独立仓库中：
# gh repo create your-skill-skill --public --source=. --remote=origin --push
```

## 文档规范与双向链接

- SKILL.md：仅保留“指令性内容”，不要粘贴大段外部规范。
- FORMS.md：用于指导 LLM Skill 与外部表单交互（如网页注册/后台/第三方 SaaS），不是“用户-LLM 交互表单”。
- references/*：长文规范、API 说明与外部资料的本地摘要，顶部包含 Title/Source/Last-Reviewed/Scope，底部含 Backlinks。
- REFERENCE.md：统一索引 references/* 与外链，并与 SKILL.md、FORMS.md 建立回链，确保可追溯与更新便利。

详情参阅：
- `skill-factory-skill/SKILL.md` → “引用与双向链接指引”与“关于 FORMS.md（Forms）”
- `skill-factory-skill/REFERENCE.md` → “4. FORMS（外部表单交互指引，本地）”

## 依赖管理（requirements.txt + pip）

- 每个 Skill 目录独立维护一个 venv 与 `requirements.txt`。
- 新增依赖后，按需选择：
	- 仅记录顶层依赖版本（推荐）：`pip install <pkg>` → 用 `pip freeze | grep '^<pkg>=='` 追加到 requirements.txt。
	- 完全快照：`pip freeze > requirements.txt`（包含传递依赖，跨平台需留意）。
- 升级依赖：`pip install -U -r requirements.txt` 或升级单包后同步 requirements.txt。

更多细节见：`skill-factory-skill/SKILL.md` 的“实践指南”。

## marketplace.json 备注（含 skills 字段）

在 `.claude-plugin/marketplace.json`（如使用）中，单个插件条目支持声明技能资源位置：

完整字段与约束定义请参阅：`skill-factory-skill/SKILL.md` → “Marketplace.json”。

## 常用命令速查

```zsh
# venv
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# 新增依赖（仅顶层）
pip install <package>
pip freeze | grep -E '^(<package>)==' >> requirements.txt
sort -u requirements.txt -o requirements.txt

# 完全快照
pip freeze > requirements.txt

# 使用 gh 推送当前目录为新仓库
git init && git add . && git commit -m "init"
gh repo create your-skill-skill --public --source=. --remote=origin --push
```

## 贡献与协作

- 提交前请运行最小验证：依赖可安装、链接可达、Backlinks 完整、术语一致（Skill/Forms/References）。
- 新增/修改 references/* 后，务必同步更新 `REFERENCE.md` 索引与 Last-Reviewed。
- 如涉及表单自动化脚本（Playwright/Selenium 等），在 `FORMS.md` 标注字段映射与流程依赖。

---

如需进一步自动化脚手架、模板化生成或 CI 校验，我可以继续补充 `scripts/` 与相应说明。欢迎提出需求。
