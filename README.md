# LLM Wiki — AI 驱动的个人知识库

**语言 / Language / 言語：** 中文（默认）｜ [English](README.en.md) ｜ [日本語](README.ja.md)

---

## 这是什么

LLM Wiki 是一个基于 [Claude Code](https://claude.ai/code) 的个人知识管理系统。你把原始素材（笔记、网页剪藏、论文、日记）丢进 `raw/`，AI 自动将其转化为结构化、互联的 wiki 页面，存入 `wiki/`。

**核心思路：**
- `raw/` 只存放、永不修改原始素材
- `wiki/` 由 AI 写，持续维护，页面间互相链接
- 每次摄入都让整个知识库更丰富，不是孤立存档

---

## 工作原理

```
你的笔记 / 网页 / 论文
        ↓
   raw/  （只读原始层）
        ↓  Claude Code 处理
   wiki/ （AI 维护的结构化页面）
        ↓
   index.md（全库导航） + log.md（操作历史）
```

AI 在处理过程中会：
1. 提取关键概念，生成/更新 wiki 页面
2. 使用 `agent-browser` 检索权威网络来源补充内容
3. 在页面间建立 `[[双向链接]]`
4. 将有价值的分析存入 `concepts/`

---

## 使用截图

![LLM Wiki 使用示例 — 在 Claude Code 中查询和更新 wiki](assets/demo-screenshot.png)

---

## 快速开始

### 前置条件

- [Claude Code](https://claude.ai/code) CLI
- （可选）[agent-browser](https://github.com/mediar-ai/agent-browser)：用于网络检索增强
  ```bash
  brew install mediar-ai/agent-browser/agent-browser
  ```

### 安装

```bash
git clone https://github.com/crabin/llm-wiki.git my-wiki
cd my-wiki
```

用 Claude Code 打开项目：
```bash
claude .
```

如果需要兼容其他 agent，可从 `AGENTS.md` 和 `.agents/skills/` 开始；Claude Code 继续使用 `CLAUDE.md` 和 `.claude/skills/`。

### 开始使用

1. **初始化**：在 Claude Code 中说"初始化 wiki"（仅首次需要）
2. **放入原始素材**：将你的笔记、PDF、网页剪藏放入 `raw/` 的对应子目录
3. **摄入**：说"摄入 raw/xxx.md"或提供 URL/文本
4. **查询**：直接提问，AI 会查阅 wiki 并回答
5. **健康检查**：说"检查 wiki"，AI 扫描断链、孤立页面等问题，并按 skill 流程提出或执行修复

---

## 目录结构

```
llm-wiki/
├── AGENTS.md          # 跨 agent 入口与兼容说明
├── CLAUDE.md          # Claude 专用行为规范（核心配置）
├── .agents/
│   └── skills/        # 供其他 agents/tools 使用的兼容引用
│       ├── agent-browser/
│       ├── wiki-init/
│       ├── wiki-ingest/
│       ├── wiki-query/
│       ├── wiki-lint/
│       └── wiki-update/
├── .claude/
│   └── skills/        # canonical skill 定义
│       ├── agent-browser/   # 浏览器自动化
│       ├── wiki-init/       # 初始化 wiki
│       ├── wiki-ingest/     # 摄入新素材
│       ├── wiki-query/      # 查询 wiki
│       ├── wiki-lint/       # 健康检查
│       └── wiki-update/     # 更新页面
├── raw/               # 原始素材（只读，不提交到 git）
│   ├── Clippings/     # 网页剪藏
│   ├── 学习笔记/
│   ├── 个人/
│   └── ...
├── wiki/              # AI 维护的 wiki 页面
│   ├── index.md       # 全库索引（表格格式）
│   ├── log.md         # 操作日志（追加写入）
│   └── pages/         # 主题页面（每主题一个 .md）
│       ├── topic-a.md
│       └── topic-b.md
└── concepts/          # 生成的分析报告、答案页面
```

---

## 五种操作模式

### Init（初始化）

首次使用时初始化 wiki 结构：

```
你：初始化 wiki
AI：询问配置 → 创建目录结构 → 写入 CLAUDE.md 和 AGENTS.md → 初始化 index.md 和 log.md
```

### Ingest（摄入）

```
你：摄入 raw/学习笔记/machine-learning.md
AI：读取文件 → 与用户讨论要点 → 网络补充 → 生成/更新 wiki 页面 → 反向链接检查 → 更新 index → 写 log
```

一次摄入可能涉及 5-15 个相关页面。

### Query（查询）

```
你：什么是联邦学习？它与隐私保护有什么关系？
AI：查阅 index → 读取相关页面 → 必要时检索网络 → 综合回答（带引用）→ 提议保存到 concepts/
```

AI 总是先读 wiki 页面，不凭记忆回答。

### Lint（健康检查）

```
你：检查 wiki
AI：扫描断链、孤立页面、矛盾内容、过期声明 → 输出分类报告 → 提供修复建议 → 执行修复 → 写 log
```

建议每 5-10 次摄入后运行一次。

### Update（更新）

```
你：更新 xxx 页面，因为新信息显示...
AI：读取当前内容 → 提出修改建议（显示 diff）→ 确认后写入 → 检查下游影响 → 更新 index → 写 log
```

所有修改都必须标注来源。

---

## Wiki 页面格式

每个 wiki 页面遵循统一格式：

```markdown
---
title: 主题名称
tags: [tag1, tag2]
sources: 3
updated: 2026-04-06
---

# 主题名称

**Source:** raw/xxx/yyy.md 或 https://example.com/article
**Date ingested:** 2026-04-06
**Type:** paper | article | transcript | code | other

## 核心定义
...

## 关键要点
- ...

## 与其他主题的关联
- [[related-topic-a]] - 说明
- [[related-topic-b]] - 说明

## 未解决的问题
<如果有>

## 来源
- 本地文件：raw/xxx/yyy.md
- 网络来源：https://example.com/article
```

---

## Skills 说明

`AGENTS.md` 和 `.agents/skills/` 是面向非 Claude agents 的兼容入口，canonical skill 定义仍然保存在 `.claude/skills/` 中。


### wiki-init

初始化一个新的 wiki 知识库，适用于任何知识领域（研究、代码文档、读书笔记、竞品分析等）。交互式询问配置（路径、领域、素材类型、索引分类），创建目录结构，写入 `CLAUDE.md`、`AGENTS.md`、`index.md`、`log.md`，并建立回指 `.claude/skills/` 的 `.agents/skills/` 兼容层。

### wiki-ingest

摄入新素材（文件、URL、文本）。核心流程：
1. 读取素材全文（不跳过任何内容）
2. **先讨论再写入**：向用户呈现 3-5 条关键要点，询问重点偏好
3. 网络补充（推荐）：通过 `agent-browser` 检索权威来源验证核心概念
4. 生成 slug，写入/更新 wiki 页面（含来源引用）
5. 更新相关实体/概念页面（不存在则创建）
6. **反向链接检查**（关键步骤）：扫描所有现有页面，添加指向新页面的链接
7. 更新 index 和 log

### wiki-query

查询 wiki 回答问题。总是先读取 `wiki/index.md` 和相关页面，不凭记忆回答。本地 wiki 信息不足时，通过 `agent-browser` 检索网络补充。综合回答时使用 `[[slug]]` 引用本地页面，URL 引用网络来源。回答后**总是提议**保存到 `concepts/`。

### wiki-lint

健康检查，检测：
- 🔴 **错误**：断链（`[[slug]]` 指向不存在的页面）、缺失 frontmatter
- 🟡 **警告**：孤立页面（零入站链接）、矛盾内容、过期声明（90 天未更新且含"最新"等词）
- 🔵 **信息**：高频引用但无专页的概念、缺失交叉引用

生成报告存入 `concepts/lint-<date>.md`，逐项提供修复建议并**显示 diff 后再写入**。

### wiki-update

更新现有 wiki 页面。流程：
1. 读取当前内容，显示 diff 前后对比（含修改原因和来源引用）
2. **逐页确认**后写入，不批量应用
3. **下游影响检查**：扫描引用该页面的其他页面，标记可能需要同步更新的内容
4. **矛盾扫描**：若新信息与 wiki 内容冲突，搜索所有出现该矛盾声明的页面，全部更新
5. 更新 index 和 log，所有修改必须标注来源

### agent-browser

浏览器自动化，用于网络检索增强。按类别优先使用权威来源：

| 类别 | 优先来源 |
|------|----------|
| 通用概念 | Wikipedia (en/zh) |
| 技术/编程 | 官方文档, MDN, arxiv, GitHub |
| AI/ML | arxiv, Papers with Code, Hugging Face |
| 学术 | Google Scholar, Semantic Scholar |
| 官方文档 | 该技术/产品的官方网站 |

---

## 设计理念

**知识复利**：每次摄入不只是存档，而是把新知识织入已有网络。第 100 次摄入时，AI 能发现这篇笔记与 50 个已有页面的关联。

**原始素材不动**：`raw/` 永远保持原样。wiki 是 AI 的解读层，不是原文替代品。

**可验证的来源**：每个页面都标注来源（本地文件路径或 URL），避免 AI 凭空生成内容。

**反向链接优先**：新页面创建后，AI 会扫描所有现有页面，在相关位置添加指向新页面的链接。这是知识复利的关键。

---

## License

MIT
