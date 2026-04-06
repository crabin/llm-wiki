# LLM Wiki — 个人知识库 Schema

个人知识库 Schema

> **CRITICAL:** 这是个人 Wiki 知识库。所有 wiki 相关操作（摄入、查询、更新、检查）必须通过对应的 wiki skill 执行。详见下方"Wiki Skill 自动触发规则"。

## 这是什么

个人 LLM Wiki —— 一个由 AI 持续维护的结构化知识库。
原始素材永远不动，wiki 层由 AI 增量构建并保持互联。

今天的日期: {{currentDate}}

---

## 目录结构

```
raw/          原始素材（只读，永远不要修改）


wiki/         AI 维护的 wiki 页面
  pages/      主题页面（每主题一个 .md 文件）
  index.md    全库索引（表格格式，每次 ingest 后更新）
  log.md      操作日志（追加写入，不修改历史）

concepts/     生成的报告、分析、答案页面
```

---

## Wiki 页面规范

- 每个主题一个 `.md` 文件，文件名用英文小写加连字符：`machine-learning.md`
- 用 `[[topic-name]]` 格式链接相关主题（Obsidian wikilink）
- 每个页面顶部加 YAML frontmatter：

```yaml
---
title: 主题名称
tags: [tag1, tag2]
sources: 引用来源数量
updated: YYYY-MM-DD
---
```

- 页面内容结构建议：核心定义 → 关键要点 → 与其他主题的关联 → 未解决的问题

---

## Wiki Skill 自动触发规则（强制执行）

<CRITICAL>
在处理本知识库时，必须根据以下条件自动调用对应的 wiki skill。这是强制性的，不是可选的。
</CRITICAL>

### wiki-ingest（摄入新素材）

**TRIGGER when:**

- 用户提供 URL、文件路径、或粘贴的文本要求处理
- 用户说"摄入"、"处理"、"添加到 wiki"、"把 X 加入知识库"
- 用户提到 raw/ 目录下的新文件
- 用户分享论文、文章、笔记内容

**ACTION:** 立即调用 `wiki-ingest` skill，不要自己处理。

### wiki-query（查询知识）

**TRIGGER when:**

- 用户提问关于 wiki 中的知识
- 用户说"查一下"、"wiki 里有什么关于"、"帮我了解"
- 问题涉及已存在于 wiki 的主题（先读 wiki/index.md 判断）

**ACTION:** 立即调用 `wiki-query` skill，不要从通用知识回答。

### wiki-lint（健康检查）

**TRIGGER when:**

- 用户说"检查 wiki"、"lint"、"健康检查"
- 每 5-10 次 ingest 操作后主动建议执行
- wiki 结构可能有问题时（孤立页面、断链）

**ACTION:** 立即调用 `wiki-lint` skill。

### wiki-update（更新页面）

**TRIGGER when:**

- 用户说"更新"、"修改"某个 wiki 页面
- 新信息与现有内容冲突
- 知识需要修正或补充

**ACTION:** 立即调用 `wiki-update` skill。

---

## Skill 调用优先级

1. **先判断是否匹配触发条件** — 不确定时，优先调用 skill
2. **不要猜测** — 如果用户意图可能涉及 wiki，先调用对应 skill
3. **skill 内容是权威** — skill 内的流程覆盖本文件的简略描述

---

## agent-browser 网络补充规则（强制执行）

<CRITICAL>
当本地 wiki 信息不足或需要验证时效性内容时，必须调用 `agent-browser` skill 进行网络搜索补充。不要凭通用知识回答。
</CRITICAL>

**TRIGGER when:**

- wiki 中没有相关页面或信息不完整
- 内容涉及时效性（版本、日期、当前状态）
- 需要验证 wiki 中的信息是否仍然准确
- 用户明确要求查证或补充

**权威来源优先级（按类别）：**


| 类别       | 优先来源                                                      |
| ---------- | ------------------------------------------------------------- |
| 通用概念   | en.wikipedia.org, zh.wikipedia.org                            |
| 技术/编程  | docs.python.org, developer.mozilla.org, arxiv.org, github.com |
| AI/ML 论文 | arxiv.org, paperswithcode.com, huggingface.co                 |
| 新闻/时事  | reuters.com, bbc.com, theguardian.com                         |
| 学术       | scholar.google.com, semanticscholar.org                       |
| 官方文档   | 优先使用该技术/产品的官方网站                                 |

**ACTION:**

1. 调用 `agent-browser` skill
2. 按上述优先级选择搜索来源
3. 将获取的信息整合进回答，并标注来源 URL
4. 如果有价值，建议将新信息摄入 wiki

---

## Index 维护规则

`wiki/index.md` 是全库导航枢纽，结构如下：

```markdown
# Index

## Wiki 页面
| 页面 | 摘要 | 标签 | 最后更新 |
|------|------|------|----------|
| [[topic]] | 一句话描述 | tag1, tag2 | 2026-04-06 |

## Concepts 页面
| 页面 | 类型 | 日期 |
|------|------|------|
| [[analysis-xxx]] | 分析报告 | 2026-04-06 |
```

每次 ingest 或生成 concept 页面后必须更新 index。

---

## Log 维护规则

`wiki/log.md` 是追加式操作记录，格式：

```markdown
## [YYYY-MM-DD] <操作类型> | <标题>
- 相关页面：xxx, yyy
- 网络查证：是/否，来源：url
- 关键发现/结论：...
```

---

## 核心原则

- **raw/ 只读**：永远不修改原始素材
- **wiki/ 由 AI 写**：用户读，AI 维护
- **知识复利**：每次摄入都让整个 wiki 更丰富，而不是孤立存档
- **交叉引用优先**：新信息要整合进已有页面，而不是孤立创建
- **矛盾要标注**：新素材与旧结论冲突时，在页面中明确标出
- **好的分析要留存**：有价值的答案存入 `concepts/`，不要消失在聊天历史里
