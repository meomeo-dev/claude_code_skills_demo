---
name: Skill Factory
description: A meta-skill to scaffold, document, and validate new Claude Agent Skills with human-in-loop workflow.
---

# Skill Factory

## 简介（What it is & What it does）

Skill Factory 是一个“用于创建 Skill 的 Skill（元技能）”。它将人在回路（human-in-loop）的提案-反馈-确认流程固化为可复用的方法与脚本，帮助快速、规范地创建新的 Claude Agent Skill 目录骨架，并保证相关文档、引用与 marketplace 清单一致与合规。

Claude Code Agent Skills 是一组封装的文档+代码. 相当于固化了一套工作文档用于特定情境下的解决方案, 因此你在构建文档时, 应站在指导 LLM 如何复用 Skills 来完成特定任务.

---

## How to Use（如何使用）

在构建 Skill 之前, 先进行 human-in-loop, 通过持续的提案-反馈-修改流程澄清并确认用户需求.
当需求确认后, 初始化目录结构, 仅搭建骨架, 文档均保持空白.
初始化完成后, 开始创建具体的 Skill.
创建 Skill 时, 先定义 SKILL.md .
如需补充外部知识, 使用 context7 查询权威的 API 文档, 编写清晰指引, 并建立双向链接文档.
如需补充 API 文档之外的知识, 使用 websearch 工具进行研究, 并明确标注引用来源, 避免因记忆模糊导致的幻觉或虚构.
每次文档变更后, 检查并同步更新必要的 .md 文档, 避免因漏更新造成逻辑缺口或相互矛盾.

---

## Claude Code Agent Skills 元提示词核心定义

本章节附录了构建 Claude Code Agent Skills 的核心定义与规范，作为创建所有 Skills 时必须遵循的元规则。

### 目录结构

```
.
├── .claude-plugin/
|   └── marketplace.json
├── [skill_name]-skill/
|   ├── SKILL.md (main instructions)
|   ├── FORMS.md (form-filling guide：指导 LLM Skill 与外部资源的表单交互的说明文档，非“用户-LLM 交互表单”)
|   ├── REFERENCE.md (indexed API reference)
|   ├── references/ (detailed API reference)
|   |   └── ...
|   ├── ....md (others guide/policy/rule)
|   ├── [sub_folder]/
|   |   └── ....md (others guide/policy/rule)
|   ├── assets/ (Files used in output: templates, icons, fonts, etc.)
|   ├── scripts/
|   |   └── ....py (utility script)
|   └── requirements.txt
├── [skill_name]-skill
├── ...
```

### Marketplace.json

```
The output should be a VALID JSON object with the following keys:
* "name":
  - description: 市场名称（标识符），用于唯一标识该 marketplace
  - constraints: 必须为 kebab-case，无空格；正则建议 ^[a-z0-9]+(?:-[a-z0-9]+)*$
  - data_type: string
  - required: true
* "owner":
  - description: 市场维护者信息
  - constraints: 无硬性约束；建议包含名称和可联系邮箱
  - data_type: object
  - required: true
  - properties:
    * "name":
      * Description: 维护团队或个人名称
      * Constraints: 非空字符串
      * Data Type: string
      * Required: false
    * "email":
      * Description: 联系邮箱
      * Constraints: 应为有效邮箱格式
      * Data Type: string
      * Required: false
* "plugins":
  - description: 可用插件的列表
  - constraints: 每个条目至少包含 name 与 source；name 必须为 kebab-case
  - data_type: array
  - required: true
  - items:
    * Description: 单个插件条目（遵循插件清单 schema 的可选化版本，外加 marketplace 字段）
    * Data Type: object
    * Required: true
    * Default for item: null
    * properties:
      - "name":
        * Description: 插件标识符
        * Constraints: 必须为 kebab-case，无空格；正则建议 ^[a-z0-9]+(?:-[a-z0-9]+)*$
        * Data Type: string
        * Required: true
      - "source":
        * Description: 插件获取来源；本地路径或远程仓库/URL
        * Constraints: 
          - string: 本地或相对路径（可结合 metadata.pluginRoot 解析）
          - object: 必须包含 source 字段
            - source 枚举: ["github", "url", "git"]
            - 当 source="github" 时需要 repo（形如 owner/repo）
            - 当 source="url" 或 "git" 时需要 url（有效 URL）
        * Data Type: string or object
        * Required: true
        * properties:
          + "source":
            - Description: 源类型
            - Constraints: 枚举值 ["github", "url", "git"]
            - Data Type: string
            - Required: true
          + "repo":
            - Description: GitHub 仓库标识
            - Constraints: 格式 owner/repo；正则建议 ^[^/]+/[^/]+$
            - Data Type: string
            - Required: false
          + "url":
            - Description: 远程 Git/归档地址
            - Constraints: 必须为有效 URL
            - Data Type: string
            - Required: false
      - "description":
        * Description: 插件简介
        * Constraints: 简短描述
        * Data Type: string
        * Required: false
      - "version":
        * Description: 插件版本
        * Constraints: 建议使用 SemVer（如 1.2.3），但非强制
        * Data Type: string
        * Required: false
      - "author":
        * Description: 插件作者信息
        * Constraints: 无硬性约束
        * Data Type: object
        * Required: false
        * properties:
          + "name":
            - Description: 作者名称
            - Constraints: 非空字符串
            - Data Type: string
            - Required: false
          + "email":
            - Description: 作者邮箱
            - Constraints: 有效邮箱格式
            - Data Type: string
            - Required: false
      - "homepage":
        * Description: 插件主页或文档链接
        * Constraints: 有效 URL
        * Data Type: string
        * Required: false
      - "repository":
        * Description: 源代码仓库地址
        * Constraints: 有效 URL
        * Data Type: string
        * Required: false
      - "license":
        * Description: 许可证
        * Constraints: 应为有效 SPDX 标识（如 MIT, Apache-2.0）
        * Data Type: string
        * Required: false
      - "keywords":
        * Description: 关键字标签
        * Constraints: 字符串数组
        * Data Type: array
        * Required: false
        * items:
          + Description: 单个关键字
          + Data Type: string
          + Required: false
      - "category":
        * Description: 插件分类（组织用途）
        * Constraints: 自由文本
        * Data Type: string
        * Required: false
      - "tags":
        * Description: 标签（用于搜索/筛选）
        * Constraints: 字符串数组
        * Data Type: array
        * Required: false
        * items:
          + Description: 单个标签
          + Data Type: string
          + Required: false
      - "strict":
        * Description: 是否要求插件目录包含 plugin.json；当 strict=true 时 marketplace 字段仅补充 plugin.json
        * Constraints: 布尔值
        * Data Type: boolean
        * Required: false
        * Default for strict: false
      - "commands":
        * Description: 命令组件位置（文件/目录路径），可覆盖默认位置
        * Constraints: 字符串或字符串数组；路径可相对插件根目录（支持 ${CLAUDE_PLUGIN_ROOT}）
        * Data Type: string or array
        * Required: false
        * items:
          + Description: 命令文件或目录路径
          + Data Type: string
          + Required: false
      - "agents":
        * Description: 代理组件位置（文件路径或目录）
        * Constraints: 字符串或字符串数组
        * Data Type: string or array
        * Required: false
        * items:
          + Description: 代理文件或目录路径
          + Data Type: string
          + Required: false
      - "skills":
        * Description: 技能组件位置（文件/目录路径）
        * Constraints: 字符串或字符串数组；路径可相对插件根目录（支持 ${CLAUDE_PLUGIN_ROOT}）
        * Data Type: string or array
        * Required: false
        * items:
          + Description: 技能文件或目录路径（例如 ./skill-creator, ./document-skills/docx, ./document-skills/pptx, ./document-skills/pdf）
          + Data Type: string
          + Required: false
      - "hooks":
        * Description: 钩子配置或钩子文件路径
        * Constraints: 
          - string: 指向 hooks 配置文件的路径
          - object: 事件到钩子列表的映射，如 PostToolUse
        * Data Type: string or object
        * Required: false
        * properties:
          + "<eventName>":
            - Description: 某事件对应的钩子规则列表
            - Constraints: 每项为钩子规则对象（例如 {matcher, hooks:[...]}）
            - Data Type: array
            - Required: false
            - Default for item: null
      - "mcpServers":
        * Description: MCP 服务器配置或配置文件路径
        * Constraints:
          - string: 指向 MCP 配置文件的路径
          - object: 服务器名称到其配置的映射
        * Data Type: string or object
        * Required: false
        * properties:
          + "<server_name>":
            - Description: 单个 MCP 服务器配置
            - Constraints: 应包含可执行命令与可选参数
            - Data Type: object
            - Required: false
            - properties:
              • "command":
                - Description: 启动服务器的可执行命令路径
                - Constraints: 非空字符串，支持 ${CLAUDE_PLUGIN_ROOT}
                - Data Type: string
                - Required: true
              • "args":
                - Description: 传递给可执行程序的参数
                - Constraints: 字符串数组
                - Data Type: array
                - Required: false
                - items:
                  · Description: 单个参数
                  · Data Type: string
                  · Required: false
* "metadata":
  - description: 市场的附加元信息
  - constraints: 所有字段可选；用于描述和解析插件相对路径
  - data_type: object
  - required: false
  - properties:
    * "description":
      * Description: 市场简介
      * Constraints: 简短描述
      * Data Type: string
      * Required: false
    * "version":
      * Description: 市场文件版本
      * Constraints: 建议使用 SemVer，但非强制
      * Data Type: string
      * Required: false
    * "pluginRoot":
      * Description: 相对插件源路径的基准目录（用于解析相对路径 source）
      * Constraints: 有效路径
      * Data Type: string
      * Required: false
```

### SKILL.md 基本结构

``````
---
name: Your Skill Name
description: Brief description of what this Skill does and when to use it
---

# Your Skill Name

## Instructions
[Clear, step-by-step guidance for Claude to follow]

## Examples
[Concrete examples of using this Skill]
``````

### .md 文档内容写作要求

```
#### 1. **核心能力与定位 (What it is & What it does)**
这类内容是文档的起点，旨在让用户快速理解该工具/规范的核心价值和用途。

*   **作用**: 定义其身份和能力，回答“这是什么？”和“它能做什么？”的问题。
*   **包含的章节**:
    *   `Core Capabilities` (核心能力)
    *   `Capabilities` (能力)
    *   `Brand Identity` (品牌识别 - 特定于品牌规范)

#### 2. **使用指南与方法 (How to use it)**
这是文档的核心，提供了具体的操作说明和交互细节，是用户的“操作手册”。

*   **作用**: 指导用户如何正确地与工具/产品进行交互，完成特定任务。
*   **包含的章节**:
    *   `Input Requirements` / `Input Format` (输入要求/格式)
    *   `Output Formats` / `Output Format` (输出格式)
    *   `How to Use` (如何使用)
    *   `Example Usage` (使用示例)
    *   `Application Instructions` (应用说明)
    *   `Model Types Supported` (支持的模型类型 - 技术细节)

#### 3. **标准、规范与最佳实践 (Rules & Best Practices)**
这类内容为使用者提供了一套需要遵守的规则和官方推荐的高效用法，以保证质量和一致性。

*   **作用**: 确保产出物的质量、风格统一，并帮助用户更高效、更正确地使用。
*   **包含的章节**:
    *   `Best Practices Applied` / `Best Practices` (最佳实践)
    *   `Visual Standards` (视觉标准)
    *   `Document Standards` (文档标准)
    *   `Content Guidelines` (内容指南)
    *   `Quality Standards` / `Quality Checks` (质量标准/检查)

#### 4. **配套资源与工具 (Supporting Resources)**
这类内容提供了辅助性的材料，帮助用户更好地集成和使用。

*   **作用**: 提供现成的脚本或工具，简化用户的工作流程。
*   **包含的章节**:
    *   `Scripts Included` / `Scripts` (包含的脚本/脚本)

#### 5. **补充信息与免责 (Caveats & Extra Info)**
这类内容涵盖了重要的注意事项、限制和维护信息，用于管理用户预期和规避风险。

*   **作用**: 明确工具/规范的边界，告知用户潜在的问题、更新计划和其他重要附注。
*   **包含的章节**:
    *   `Limitations and Disclaimers` / `Limitations` (限制与免责声明)
    *   `Updates and Maintenance` (更新与维护)
    *   `Notes` (备注)
```

### 关于 FORMS.md（Forms）

FORMS.md 用于指导 LLM Skill 如何与外部资源中的表单进行交互（如网页注册/登录、后台工单、SaaS 配置页面等）。它不是“用户-LLM 交互用的表单”，而是提供给 LLM 的操作说明，帮助其可靠地定位、填写并提交目标表单。

建议包含内容

- 表单入口与上下文：页面/URL、导航路径、登录前置条件、权限要求。
- 字段清单与约束：字段含义、类型（文本/选择/复选等）、校验规则、占位符示例、定位方式（如 CSS 选择器/xpath/label 文本）。
- 交互流程：打开 → 等待加载 → 填写 → 处理验证码/二次确认 → 提交 → 成功判定与后置动作。
- 错误与重试策略：加载超时、字段校验失败、验证码失败、频率限制、回退与重试间隔。
- 安全与合规：敏感数据处理、速率限制、避免触发风控的操作建议。

示例（批量注册邮箱 Skill）

- 表单入口：`https://example.com/signup`
- 关键字段：Email（必填，格式校验）、Password（必填，强度校验）、Captcha（必填，需调用验证码解决流程或人工介入）。
- 定位方式：
  - Email：`input[name="email"]`
  - Password：`input#password`
  - Captcha：`img.captcha` + `input[name="captcha"]`
- 提交按钮：`button[type="submit"]`；成功判定：页面出现“Welcome”或跳转至 `/dashboard`。
- 重试：验证码失败时最多重试 3 次，每次间隔 5–10s；触发 429 时退避至 60s。

关联建议

- 在 `SKILL.md` 中仅引用 FORMS.md 的关键锚点，不粘贴大段细节。
- 将与表单相关的外部规范或 API 文档放入 `references/`，并在 `REFERENCE.md` 维护索引与回链。
- 如使用自动化脚本（如 Playwright/Selenium），将脚本置于 `scripts/` 并在 FORMS.md 中说明脚本与表单字段的映射关系。

## 引用与双向链接指引（SKILL.md → REFERENCE.md → references/*）

为确保知识可追溯、可更新且不污染上下文窗口，请遵循以下引用链路：

- SKILL.md 只保留方法与流程的“指令性内容”；任何长篇规范、API 说明与外部资料一律收敛到 references/*.md。
- 在 SKILL.md 中仅链接到 REFERENCE.md 的对应索引项或锚点，不直接粘贴大段原文。
- 在 REFERENCE.md 中维护索引目录：包含条目标题、来源 URL、最后校对时间、对应的 references 文件路径。
- 在每一篇 references/*.md 顶部使用统一信息块：Title、Source、Last-Reviewed、Scope，并在文末添加“Backlinks”指向 SKILL.md 对应段落与 REFERENCE.md 索引项。

命名规范与模板：

- 文件命名：kebab-case，示例：agent-skills-overview.md、custom-skills-cookbook.md
- 引用文档模板（复制到新建文件开头）：
  ```md
  Title: <标题>
  Source: <权威链接>
  Last-Reviewed: <YYYY-MM-DD>
  Scope: <覆盖范围与使用边界>

  # <标题>
  - 摘要
  - 关键要点
  - 实操指引（与本 Skill 的关系）
  - 限制与注意事项
  - 原始链接与引用
  - Backlinks: [SKILL.md](./SKILL.md#引用与双向链接指引)，[REFERENCE.md](./REFERENCE.md)
  ```
维护流程（每次新增/修改引用时执行）：

1) 新建/更新 references/*.md，补齐信息块与要点摘要，并注明 Last-Reviewed 日期。
2) 更新 REFERENCE.md 索引：添加条目并链接到该 references 文件与源链接。
3) 如需在 SKILL.md 处引用，添加指向 REFERENCE.md 条目的链接或锚点（优先链接 REFERENCE.md，而非外网）。
4) 进行一次一致性检查：SKILL.md 的链接可达、REFERENCE.md 的索引完整、references/*.md 的 Backlinks 正确。

---

## 实践指南：使用 requirements.txt 管理 Python 依赖与用 gh/git 创建新 Skill 项目

本节提供最小但实用的操作指引，适用于 macOS + zsh 环境。

### 1) Python 依赖管理（requirements.txt + pip）

- 位置：本 Skill 的依赖清单位于 `skill-factory-skill/requirements.txt`
- 目标：固定项目依赖的版本，使团队成员与 CI 能复现一致的环境。

推荐流程（每个 Skill 目录独立管理依赖）

1. 创建并激活虚拟环境（建议每个 Skill 单独一个 venv）

```zsh
cd path/to/your-skill-skill
python3 -m venv .venv
source .venv/bin/activate  # macOS + zsh
python -V; pip -V          # 可选：检查版本
```

2. 安装依赖

```zsh
pip install -r requirements.txt
```

3. 新增依赖（两种常见策略）

- A. 精准固定“顶层依赖”版本（推荐）
  - 安装依赖：
    ```zsh
    pip install <package>
    ```
  - 将所装依赖的准确版本写入 requirements.txt（仅顶层，不包含传递依赖）：
    ```zsh
    pip freeze | grep -E '^(<package>)==' >> requirements.txt
    sort -u requirements.txt -o requirements.txt
    ```
  - 提交变更。

- B. 完全快照（锁定所有当前环境依赖）
  - 适合小型/一次性工具，或你希望 100% 可复现：
    ```zsh
    pip freeze > requirements.txt
    ```
  - 注意：这会包含传递依赖与平台相关包，跨平台时需留意兼容性。

4. 升级依赖

```zsh
pip install -U -r requirements.txt
# 或升级单个包
pip install -U <package>
# 再次按策略 A 或 B 更新 requirements.txt
```

5. 重新安装/复原环境（排障或 CI 复现）

```zsh
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

6. 额外建议

- 将 `.venv/` 加入仓库根目录 `.gitignore`。
- 中国大陆网络环境可按需添加镜像：
  ```zsh
  pip install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
  ```
- 如需更专业的“顶层依赖声明 + 锁定文件”，可引入 pip-tools（pip-compile/pip-sync）；本仓库默认保持简单，不强制使用。

常见问题速查

- venv 没有激活：确保使用 `source .venv/bin/activate` 后再运行 pip。
- 版本冲突：先尝试 `pip install -U <package>`，若仍冲突，清空 venv 重装。
- M1/M2/M3 Mac 安装报错：优先选择纯 Python 轮子（wheels）；如需编译，确保安装 Xcode Command Line Tools：`xcode-select --install`。

---

### 2) 使用 GitHub CLI（gh）与 git 创建新的 Skill 项目

以下提供两种工作模式：

• 单仓库多 Skill（Monorepo）：在现有仓库中新增一个 `your-skill-skill/` 目录。
• 独立仓库单 Skill（Single repo per skill）：为每个 Skill 新建独立 GitHub 仓库。

准备工作

```zsh
# 一次性登录 GitHub CLI（若未登录）
gh auth login
```

方案 A：在当前仓库新增一个 Skill 目录（Monorepo）

```zsh
cd /path/to/your-monorepo

# 以现有模板为蓝本拷贝（可用本仓库的 skill-factory-skill 作为起点）
cp -R skill-factory-skill your-skill-skill

# 重命名/编辑以下文件以匹配你的 Skill：
# - your-skill-skill/SKILL.md    （更新前言 name/description 与正文）
# - your-skill-skill/FORMS.md    （如需要）
# - your-skill-skill/REFERENCE.md（新增/更新引用索引）
# - your-skill-skill/references/ （添加具体参考文档）
# - your-skill-skill/scripts/    （添加自动化脚本）
# - your-skill-skill/requirements.txt（声明依赖）

git add your-skill-skill
git commit -m "feat(skill): scaffold your-skill-skill"
git push
```

方案 B：创建独立仓库并推送（Single repo per skill）

```zsh
# 1) 本地初始化
mkdir your-skill-skill && cd your-skill-skill
python3 -m venv .venv && source .venv/bin/activate

# 2) 初始化目录骨架（可从现有 Skill 拷贝最小结构）
mkdir -p references scripts
cp /path/to/claude_code_skills_demo/skill-factory-skill/SKILL.md ./SKILL.md
cp /path/to/claude_code_skills_demo/skill-factory-skill/REFERENCE.md ./REFERENCE.md 2>/dev/null || true
cp /path/to/claude_code_skills_demo/skill-factory-skill/FORMS.md ./FORMS.md 2>/dev/null || true
cp /path/to/claude_code_skills_demo/skill-factory-skill/requirements.txt ./requirements.txt 2>/dev/null || echo "# add your deps here" > requirements.txt

# 3) 首次提交
git init
git add .
git commit -m "chore: initial scaffold for your-skill-skill"

# 4) 创建远端仓库（使用 gh）
gh repo create your-skill-skill --public --source=. --remote=origin --push

# 若不用 gh，也可在 GitHub 网页创建空仓库后：
# git remote add origin git@github.com:<you>/your-skill-skill.git
# git branch -M main
# git push -u origin main
```

提交前检查清单

- 目录结构是否齐全：`SKILL.md / FORMS.md / REFERENCE.md / references/ / scripts/ / requirements.txt`
- `SKILL.md` 是否仅保留“指令性内容”，长文规范是否下沉到 `references/*.md`，并通过 `REFERENCE.md` 建立索引与双向链接。
- `requirements.txt` 是否可成功安装：`pip install -r requirements.txt`。
- 是否添加 `.gitignore` 并忽略 `.venv/`、`__pycache__/` 等。

常见问题速查（gh/git）

- gh 报权限/认证错误：先运行 `gh auth status` 查看状态，必要时 `gh auth login` 重新登录。
- 推送被拒绝：确认已 `git branch -M main` 并设置上游：`git push -u origin main`。
- 子目录作为独立仓库：可使用 Git 子模块或将子目录抽出为新仓库再回连；根据团队治理选择。

---

### 3) 命令速查（可复制粘贴）

依赖

```zsh
# 创建 venv 并安装依赖
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt

# 新增依赖并写入 requirements.txt（仅顶层）
pip install <package>
pip freeze | grep -E '^(<package>)==' >> requirements.txt
sort -u requirements.txt -o requirements.txt

# 完全快照
pip freeze > requirements.txt
```

仓库

```zsh
# gh 创建远端并推送当前目录
git init && git add . && git commit -m "init"
gh repo create your-skill-skill --public --source=. --remote=origin --push

# 仅使用 git（远端已在网页创建）
git init
git branch -M main
git remote add origin git@github.com:<you>/your-skill-skill.git
git add . && git commit -m "init"
git push -u origin main
```

---

# 开始

上述内容为 Skill Factory 运行所需要的背景知识, 你在开始任务时务必择关注相关内容.
每次文档变更后, 检查并同步更新必要的 .md 文档, 避免因漏更新造成逻辑缺口或相互矛盾.

你每开始一项子任务前, 先前置 checklist, 然后继续. `Checklist`的形态是结构化的、离散的、原子化的。它将模糊的“原则”固化为可逐项检查的“规则”。它的位置从生成过程的“内部” (`Plan scratch`是过程的一部分) 移动到了“前部” (`前置`)。 `Checklist`的功能不再是“探索性推理”，而是“**符合性验证 (Compliance Verification)**”。其目的非常明确——`方便监督后续生成`。
