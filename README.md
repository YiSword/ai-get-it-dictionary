# AI Get It / AI秒懂 · 开源 AI 术语词库（Dictionary）

用大白话解释 AI 黑话：开源词库数据（术语 + 通俗解释 + 来源）｜产品内提供知识图谱与更丰富学习体验。

本仓库是 **AI Get It / AI秒懂** 的开源词库数据仓库，面向社区协作维护，并支持同步到小程序数据库使用。小程序端的信息架构包含“搜索 / 关系 / 学习 / 账户”四个 Tab，并规划了输入联想、aliases 二次导航、AI 生成提示、相关推荐、关系页分组与“展示更多关系”付费点等交互。[Source](https://www.genspark.ai/api/files/s/7o1ZowqP)

---

## 目录

- [项目简介](#项目简介)
- [开源边界（Open vs In-app）](#开源边界open-vs-in-app)
- [仓库目录结构](#仓库目录结构)
- [数据模型（term / redirect）](#数据模型term--redirect)
- [字段约束与写作规范（摘要）](#字段约束与写作规范摘要)
- [使用方式（给集成方/产品方）](#使用方式给集成方产品方)
- [许可说明（License）](#许可说明license)
- [贡献指南（Contributing）](#贡献指南contributing)
- [Roadmap](#roadmap)

---

## 项目简介

AI Get It / AI秒懂 的目标是把 AI 领域热词（概念、模型、指标、工具、数据集、论文名、组织/产品等）整理成结构化词库，提供：

- 可检索：支持 term / norm_term / aliases 命中与联想
- 可追溯：主词条尽量都包含可访问的 sources（来源链接）
- 可扩展：支持 term / redirect 两类词条；后续可在产品内叠加知识图谱、学习路径等能力
- 可同步：词库可从 GitHub 同步到小程序数据库，支撑搜索与推荐体验

词条字段与强约束（例如：norm_term 全局唯一、scenario_tags 2~5、sources 至少 1 条、plain 文案长度与句数等）基于当前 DEMO 的词库规范执行。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)

---

## 开源边界（Open vs In-app）

### 本仓库开源内容（Open）

- 术语主信息：term / norm_term / lang / aliases / entity_type / category / difficulty
- 通俗解释：explanations.plain（面向小白）
- 场景标签：scenario_tags（自动生成、弱约束，用于按场景发现/推荐）
- 来源：sources（主词条尽量都有，且 URL 可访问）
- 重定向词条：entry_kind=redirect（缩写/别名/常见叫法 → 标准主词条）

scenario_tags 的用途与要求（2~5 个短词）来自 DEMO 规范，用于按场景发现与推荐。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)

### 产品内增值内容（In-app / Premium）

- 比喻解释：explanations.metaphor
- 示例解释：explanations.example
- 知识图谱与关系能力：关系类型、权重、原因说明、更多关系展示、多跳扩展、学习路径等
- 更丰富的推荐与付费策略（如关系页“每组 Top 3 免费，展示更多关系付费”）[Source](https://www.genspark.ai/api/files/s/7o1ZowqP)

---

## 仓库目录结构

建议结构如下（便于社区协作与 PR 减少冲突）：

- terms/
  - term/（主词条，entry_kind=term）
  - redirect/（重定向词条，entry_kind=redirect）
- schema/（JSON Schema：字段类型/枚举/条件约束等）
- docs/（字段填写规范、写作指南、标签说明等，可选但强烈建议）
- scripts/（可选：校验/打包/统计/同步辅助脚本，未来可加）
- LICENSE（MIT：代码/脚本）
- LICENSE_DATA（CC BY 4.0：词库数据）
- CONTRIBUTING.md（贡献指南）

---

## 数据模型（term / redirect）

说明：norm_term 使用 snake_case，作为去重主键与（未来）关系外键引用，必须全局唯一。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)

### 1) 主词条（entry_kind=term）

文件路径示例：terms/term/transformer.json

示例（仅展示核心字段；实际以 schema 与规范为准）：

{
  "entry_kind": "term",
  "term": "Transformer",
  "norm_term": "transformer",
  "lang": "zh",
  "aliases": ["Transformer架构", "自注意力网络"],
  "entity_type": "concept",
  "category": "模型架构",
  "difficulty": "中级",
  "explanations": {
    "plain": "（通俗解释：2-4句，60-160字，小白口径）"
  },
  "scenario_tags": ["对话", "摘要", "翻译"],
  "sources": [
    {"title": "Google ML Glossary", "url": "https://developers.google.com/machine-learning/glossary", "type": "glossary"}
  ],
  "review_status": "seed",
  "content_source": "ai_generated",
  "hit_count": 0,
  "created_at": "2026-03-15T00:00:00.000Z",
  "updated_at": "2026-03-15T00:00:00.000Z"
}

主词条的字段约束要点（摘要）：

- sources：至少 1 条；url 必须可访问；type 可用 glossary/doc/github/blog/paper 等。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)
- scenario_tags：2~5 个短词（自动生成、弱约束）。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)
- explanations.plain：2~4句、60~160字、面向小白；避免堆砌专业词；禁止营销/联系方式/二维码/夸张承诺。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)

### 2) 重定向词条（entry_kind=redirect）

用途：缩写、别名、常见叫法、拼写变体等提高命中率。命中 redirect 后应跳转到标准主词条（redirect_to 指向主词条 norm_term）。

文件路径示例：terms/redirect/gan.json

{
  "entry_kind": "redirect",
  "term": "GAN",
  "norm_term": "gan",
  "lang": "en",
  "aliases": ["生成对抗网络", "Generative Adversarial Network"],
  "redirect_to": "generative_adversarial_network",
  "entity_type": "concept",
  "category": "模型架构",
  "difficulty": "中级",
  "review_status": "seed",
  "content_source": "ai_generated",
  "hit_count": 0,
  "created_at": "2026-03-15T00:00:00.000Z",
  "updated_at": "2026-03-15T00:00:00.000Z"
}

redirect 的规则要点：

- redirect_to 必填，必须指向一个存在的主词条 norm_term（建议在同一个 PR 里一起提交主词条与 redirect）。
- redirect 词条不要求提供 explanations.plain / scenario_tags / sources（避免重复维护）；解释与来源以主词条为准。

---

## 字段约束与写作规范（摘要）

以下规范在 DEMO 词库约束中已明确，贡献者请严格遵守。[Source](https://www.genspark.ai/api/files/s/LPdjlQS5)

- norm_term：用于去重与外键引用；全局唯一；snake_case。
- aliases：0~5 个；无则 []；用于提高命中率（同义词、缩写、常见叫法）。
- entity_type：必须从枚举中选择（concept/model/product/org/metric/tool/dataset/paper/other）。
- category：必须从枚举中选择（模型架构/训练方法/推理与部署/评测指标/提示工程/安全与对齐/数据工程/产品概念/其他）。
- difficulty：入门 / 中级 / 高级。
- explanations.plain：2~4句、60~160字、面向小白；禁止营销/联系方式/二维码/夸张承诺。
- scenario_tags：2~5 个短词（自动生成、弱约束；用于按场景发现/推荐）。
- sources：至少 1 条；url 必须可访问；type 可用 glossary/doc/github/blog/paper。

---

## 使用方式（给集成方/产品方）

常见集成模式：

1) 同步到数据库：定期拉取 terms/ 数据并入库（term 与 redirect 可同表存储，通过 entry_kind 区分；或分表存储）。
2) 搜索与联想：
- 搜索：term / norm_term / aliases 命中
- 联想：prefix suggestions（最多 5 条，前端 300ms debounce；与小程序 UI 规划一致）[Source](https://www.genspark.ai/api/files/s/7o1ZowqP)
3) 重定向逻辑：
- 命中 redirect：跳转到 redirect_to 的主词条展示，并可提示“已为你跳转至标准词条”
4) 后续扩展：
- 产品内知识图谱：按 relation_type 分组展示、每组 Top 3 免费、更多关系付费解锁（UI 规划已提供）[Source](https://www.genspark.ai/api/files/s/7o1ZowqP)

---

## 许可说明（License）

本仓库采用“代码与数据分离许可”：

- 代码/脚本（如 scripts/、工具链等）：MIT License（见 LICENSE）
- 词库数据（例如 terms/ 下的 JSON 数据）：CC BY 4.0（见 LICENSE_DATA）

CC BY 4.0 官方链接：https://creativecommons.org/licenses/by/4.0/ [Source](https://creativecommons.org/licenses/by/4.0/)

---

## 贡献指南（Contributing）

欢迎贡献：

- 新增主词条（term）
- 新增重定向词条（redirect）：缩写/别名/常见叫法
- 修正通俗解释（plain）或补充更可靠的 sources
- 提交纠错：不准确/看不懂/来源失效等

快速贡献流程：

1. Fork 本仓库
2. 新建分支：add_{norm_term} 或 fix_{norm_term}
3. 新增/修改词条文件：
- 主词条：terms/term/{norm_term}.json
- 重定向：terms/redirect/{norm_term}.json
4. 主词条确保至少 1 条 sources（URL 可访问）
5. 提交 Pull Request

更详细的字段规范与命名规则请阅读：CONTRIBUTING.md

---

## Roadmap

- v0.1：1000+ 热词覆盖；term/redirect；review_status=seed；content_source=ai_generated
- v0.2：引入 verified 批次，逐步提质；完善 scenario_tags 的归一化建议
- v0.3：完善校验/打包/同步工具链；产品内知识图谱与学习路径持续迭代
