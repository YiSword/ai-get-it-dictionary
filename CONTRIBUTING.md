# Contributing Guide · AI Get It / AI秒懂 术语词库

感谢你愿意为 **AI Get It / AI秒懂** 的开源 AI 术语词库贡献内容！

本仓库维护的是「开源词库数据层」：术语名称 + 解释（explanations）+ 场景标签 + 来源 + 重定向（redirect）。

> 提示：term 需要维护三段 explanations（plain/metaphor/example）。

---

## 0. 你可以贡献什么？

你可以通过 PR 贡献以下内容：

- 新增主词条（`entry_kind = term`）
- 新增重定向词条（`entry_kind = redirect`），用于缩写/别名/常见叫法/拼写变体
- 修正主词条的通俗解释（`explanations.plain`）
- 补充/修复来源（`sources`）
- 增补别名（`aliases`）与场景标签（`scenario_tags`）

---

## 1. 目录结构与文件命名

- 主词条：`terms/term/{norm_term}.json`
- 重定向：`terms/redirect/{norm_term}.json`

规则：
- 文件名必须等于 `norm_term`（便于去重、索引、同步与关系外键引用）
- 每个文件只包含 **一个** 词条对象（不要把多个对象塞进同一个文件）

---

## 2. norm_term 规范（snake_case）

`norm_term` 是全局唯一键（也是后续关系/图谱的外键引用）。必须遵守：

- 全小写
- 允许字符：`a-z`、`0-9`、`_`
- 空格、短横线 `-`、斜杠 `/` 等统一替换为 `_`
- 连续多个 `_` 需压缩为单个 `_`
- 不允许以下情况：以 `_` 开头或结尾
- 必须全仓库唯一（不可重复）

冲突处理建议：
- 同义词/缩写/别名：用 **redirect** 词条解决
- 同名异义（确实不同概念）：在 `norm_term` 后添加后缀区分，例如：`bert_model` / `bert_metric`，并在 `aliases` 中写清

---

## 3. 主词条（entry_kind=term）规则

### 3.1 必填字段（最小集）

主词条必须至少包含以下字段（字段名以仓库约定为准）：

- `entry_kind`: 必须为 `"term"`
- `term` / `norm_term` / `lang`
- `aliases`: 0~5 个；无则 `[]`
- `entity_type` / `category` / `difficulty`
- `explanations`
- `scenario_tags`: 2~5 个短词
- `sources`: 至少 1 条，且 URL 可访问
- `review_status` / `content_source`
- `hit_count` / `created_at` / `updated_at`

### 3.2 explanations.plain（通俗解释）写作要求（硬性）

为了保证用户体验一致，`explanations` 必须满足：

- 2~4 句
- 60~160 字（中文）
- 面向小白口径：尽量少用新术语堆叠
- 禁止内容：营销话术、联系方式、二维码、夸张承诺、引流信息

推荐写法（结构建议）：
- 第 1 句：一句话定义（它是什么）
- 第 2 句：它解决什么问题/为什么重要
- 第 3~4 句（可选）：典型使用场景或常见误解澄清

### 3.3 scenario_tags（场景标签）规则

- 数量：2~5 个
- 形式：短词，不要写长句
- 用途：用于按场景发现/推荐（例如：对话/摘要/翻译/检索/写作/代码/训练/部署/评测…）

### 3.4 sources（来源）规则

主词条必须至少提供 1 条来源（建议越权威越好）。

每条 source 建议结构：
- `title`: 来源标题
- `url`: 可访问链接
- `type`: 建议使用 `glossary` / `doc` / `github` / `blog` / `paper`

注意：
- URL 必须可访问
- 允许多个 sources

---

## 4. 重定向词条（entry_kind=redirect）规则

### 4.1 何时需要 redirect？

以下情况建议新增 redirect：

- 缩写：GAN、LoRA、MoE…
- 常见别名：Transformer 架构 / 自注意力网络（若你决定将其作为独立入口）
- 拼写变体：有多种常见写法但指向同一概念
- 中英混写入口：用户常用的搜索输入形态

### 4.2 redirect 必填字段（最小集）

redirect 词条必须至少包含：

- `entry_kind`: 必须为 `"redirect"`
- `term` / `norm_term` / `lang`
- `redirect_to`: 必须指向一个存在的主词条 `norm_term`
- `review_status` / `content_source`
- `hit_count` / `created_at` / `updated_at`

建议保留（便于搜索排序/展示/统计）：

- `aliases`（0~5）
- `entity_type` / `category` / `difficulty`

说明：
- redirect 词条 **不要求** 提供 `explanations.plain` / `scenario_tags` / `sources`（避免重复维护）
- `redirect_to` 指向的主词条必须已存在，或在同一个 PR 中一并提交

---

## 5. review_status 与 content_source（两条轴）

为了区分“内容质量”与“内容来源”，我们使用两条轴：

### 5.1 review_status（质量/生命周期）

- `seed`: 初稿/待校验（首发默认）
- `verified`: 已校验
- `needs_fix`: 需要修正（例如被反馈不准确/看不懂）
- `deprecated`: 弃用（建议配合 redirect 指向替代词条）

### 5.2 content_source（内容来源）

- `ai_generated`: AI 生成（首发默认）
- `human_written`: 人工撰写/深度编辑
- `imported`: 由权威来源整理改写

---

## 6. 提交流程（Fork → PR）

1) Fork 本仓库

2) 新建分支（建议命名）：
- 新增：`add_{norm_term}`
- 修复：`fix_{norm_term}`

3) 新增/修改文件：
- 主词条：`terms/term/{norm_term}.json`
- redirect：`terms/redirect/{norm_term}.json`

4) 自检清单（提交前请确认）

- [ ] `norm_term` 符合 snake_case 且全仓库唯一
- [ ] 主词条包含 `sources` 且 URL 可访问（至少 1 条）
- [ ] `aliases` 数量 0~5，空则 `[]`
- [ ] 主词条 `scenario_tags` 数量 2~5
- [ ] 主词条 `explanations.plain` 满足 2~4句、60~160字、小白口径
- [ ] redirect 词条包含 `redirect_to` 且目标主词条存在

5) 提交 PR

PR 描述建议包含：
- 本次新增/修改了哪些词条（列出 `norm_term`）
- 每个主词条的来源简述（来自何处）
- 是否包含 redirect

---

## 7. 常见问题（FAQ）

Q1：我想新增一个缩写词条（如 GAN），是放在 aliases 还是 redirect？
- 建议：缩写作为 **redirect**（更适合搜索入口），主词条在 aliases 中也可以保留。

Q2：sources 找不到“官方”来源怎么办？
- 仍需提供至少 1 条可访问来源；若权威性不足，可先提交 `review_status=seed`，后续再补强。

Q3：explanations 里能不能出现很多术语？
- 尽量避免。plain 的目标是“让小白读懂”，不要用术语解释新术语。

---
再次感谢你的贡献！
