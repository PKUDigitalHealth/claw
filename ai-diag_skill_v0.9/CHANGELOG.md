# CHANGELOG — v0.8 → v0.9 优化修改点

本文档记录从 v0.8 到 v0.9 的所有修改，按文件分组，每条标注修改原因。

---

## 1. `SKILL.md` — 全面重构

| # | 修改点 | 修改前 | 修改后 | 原因 |
|---|---|---|---|---|
| 1.1 | **description 触发词扩展** | 仅包含 `ECG analysis, 单导联, 十二导联, ecg signal` | 新增 `心电图, 心电分析, 心律分析, arrhythmia, heart rhythm, QRS, QT interval, signal quality` | 提高 Agent 触发覆盖率，避免遗漏常见的 ECG 相关表述 |
| 1.2 | **新增 "When to Activate" 章节** | 无此章节 | 新增 6 条明确的激活条件列表 | 对齐流行 Skill 标准结构（claude-api、x-api 等均有此节），让 Agent 精准识别何时调用本 Skill |
| 1.3 | **新增 "Environment Setup" 章节** | API Key 设置散落在 Script Commands 和 Error Handling 中 | 独立章节，包含 Key 获取地址 + pip install + 安全提醒 | 用户首次上手时需要一个清晰的前置配置入口 |
| 1.4 | **API Endpoint Selection 表格** | 保留不变 | 保留不变（仅微调格式） | 原内容已足够清晰 |
| 1.5 | **Workflow 增加步骤** | 4 步 | 5 步：新增"根据用户语言设置 `--lang` 标志" | 与 call_api.py 的 `--lang` 新参数保持一致 |
| 1.6 | **Script Commands 增加 `--lang` 示例** | 仅 2 条命令 | 4 条命令：分别展示 zh/en 的 1-lead 和 12-lead | 配合 i18n 改动，让 Agent 知道如何选择语言 |
| 1.7 | **Input File Formats 增加字段表格** | 仅有 JSON 示例 | 每种格式下方增加字段说明表（Field / Type / Required / Description） | 让 Agent 和用户都能快速理解每个字段的含义和类型 |
| 1.8 | **Response Format 增加 Type 列** | 表格仅有 Field + Description | 增加 Type 列（string / number / boolean / string[] 等） | 提升 API 文档的专业性和可用性 |
| 1.9 | **Examples 从 3 个简略改为 3 个完整链路** | 每个 Example 仅 1 行命令 + 1 行说明 | 每个 Example 包含：用户输入 → Agent 执行命令 → JSON 输出 → Agent 回复 | 对齐流行 Skill 标准（fal-ai-media 等），让读者理解完整的交互流程 |
| 1.10 | **Error Handling 表格扩展** | 5 种场景 | 7 种场景：新增 "API business error" 和 "File too large" | 覆盖更多实际出错情况 |
| 1.11 | **新增 "Security & Privacy" 章节** | 无此章节 | 5 条安全准则，含医疗数据声明 | 医疗 API Skill 必须有隐私与合规声明，x-api 等也有 Security 章节 |
| 1.12 | **新增 "Tips" 章节** | 无此章节 | 6 条使用技巧 | 对齐流行 Skill 标准（fal-ai-media、exa-search 均有 Tips） |
| 1.13 | **新增 "Programmatic Usage" 章节** | 无此章节 | 可运行的 Python import 示例 | 对齐 claude-api / x-api 的编程集成示例，拓展使用场景 |

---

## 2. `scripts/call_api.py` — 国际化支持

| # | 修改点 | 修改前 | 修改后 | 原因 |
|---|---|---|---|---|
| 2.1 | **新增 `--lang` CLI 参数** | 无此参数 | `--lang zh|en`，默认 `zh` | 解决 summary 硬编码中文与"响应用户语言"设计目标的矛盾 |
| 2.2 | **1-lead summary 拆分为中英双版本** | `format_1lead_result()` 内直接拼中文字符串 | 拆分为 `_build_1lead_summary_zh()` + `_build_1lead_summary_en()` | 结构清晰，各语言独立维护，便于未来扩展其他语言 |
| 2.3 | **12-lead summary 拆分为中英双版本** | `format_12lead_result()` 内直接拼中文字符串 | 拆分为 `_build_12lead_summary_zh()` + `_build_12lead_summary_en()` | 同上 |
| 2.4 | **`format_1lead_result()` 签名变更** | `format_1lead_result(data)` | `format_1lead_result(data, lang="zh")` | 接受语言参数，选择对应的 summary 构建函数 |
| 2.5 | **`format_12lead_result()` 签名变更** | `format_12lead_result(data)` | `format_12lead_result(data, lang="zh")` | 同上 |
| 2.6 | **CLI `--json_path` help 文本统一为英文** | 中文 help 文本 | 英文 help 文本 `"ECG JSON file path"` | 保持 CLI help 语言一致性 |
| 2.7 | **移除格式化函数中的冗长 docstring** | 每个函数内有 15+ 行字段说明 docstring | 移除（字段说明已在 SKILL.md 中完整记录） | 避免信息重复，SKILL.md 是 single source of truth |

---

## 3. `README.md` — 全面重写

| # | 修改点 | 修改前 | 修改后 | 原因 |
|---|---|---|---|---|
| 3.1 | **标题与描述** | "心之声 ECG Analysis Skill（OpenClaw）" | "heartvoice ECG Analysis Skill" + 英文描述段落 | 品牌名称统一，面向国际发布 |
| 3.2 | **新增 Badge** | 无 | Python 3.7+、MIT License、API 三个 badge | GitHub 项目标准实践，提升专业感 |
| 3.3 | **Features 列表** | 仅一个功能表格 | 6 条 Feature 列表 | 让读者快速了解 Skill 的核心能力 |
| 3.4 | **目录结构** | `xinzhisheng_api_skill_v1/` → 已改为 `ecg-ai-diag_skill_v0.8/` | 改为 `ecg-ai-diag_skill/`，包含新增文件 | 反映实际文件结构，包含 data/、LICENSE 等新文件 |
| 3.5 | **Quick Start 扩展** | 3 步 | 5 步：增加 "Get an API Key" 和 "Example Output" | 零基础用户也能跟着做 |
| 3.6 | **命令行示例增加 `--lang`** | 无 `--lang` 示例 | 展示 `--lang en` 用法 | 配合 call_api.py 的 i18n 改动 |
| 3.7 | **新增 "Programmatic Usage" 章节** | 无 | Python import 示例 | 开发者可以直接在代码中集成 |
| 3.8 | **新增 "Security & Privacy" 章节** | 无 | 3 条安全提醒 | GitHub 开源项目应明确安全声明 |
| 3.9 | **新增 Links 章节** | 无 | API 文档 + 官网链接 | 方便用户跳转 |
| 3.10 | **移除命令行中错误的 `cd xinzhisheng_api_skill_v1`** | `cd xinzhisheng_api_skill_v1` | 移除（直接执行命令） | 旧目录名已不存在 |

---

## 4. 新增文件

| # | 文件 | 用途 | 原因 |
|---|---|---|---|
| 4.1 | `requirements.txt` | 声明 Python 依赖 `requests>=2.20.0` | GitHub 发布标准实践，pip install -r 一键安装 |
| 4.2 | `.gitignore` | 排除 `.env`、`__pycache__`、IDE 配置、OS 文件 | 防止敏感信息和临时文件进入版本控制 |
| 4.3 | `LICENSE` | MIT 开源协议 | GitHub 项目必须有协议声明 |
| 4.4 | `data/example_1lead.json` | 单导联示例数据 | 用户零门槛试用，无需自备数据 |
| 4.5 | `data/example_12lead.json` | 十二导联示例数据 | 同上 |
| 4.6 | `CHANGELOG.md` | 本文件：记录所有修改点 | 便于对照审查每一项改动 |

---

## 修改统计

| 类别 | 数量 |
|---|---|
| 修改的文件 | 3 个（SKILL.md、call_api.py、README.md） |
| 新增的文件 | 6 个（requirements.txt、.gitignore、LICENSE、2 个示例数据、CHANGELOG.md） |
| SKILL.md 新增章节 | 5 个（When to Activate、Environment Setup、Security & Privacy、Tips、Programmatic Usage） |
| call_api.py 新增函数 | 4 个（_build_1lead_summary_zh/en、_build_12lead_summary_zh/en） |
| call_api.py 新增 CLI 参数 | 1 个（--lang） |
