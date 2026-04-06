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

### 开始使用

1. **放入原始素材**：将你的笔记、PDF、网页剪藏放入 `raw/` 的对应子目录
2. **摄入**：在 Claude Code 中说"摄入 raw/xxx.md"
3. **查询**：直接提问，AI 会查阅 wiki 并回答
4. **健康检查**：说"检查 wiki"，AI 扫描并修复断链、孤立页面等问题

---

## 目录结构

```
llm-wiki/
├── CLAUDE.md          # AI 行为规范（核心配置）
├── index.md           # 全库索引（AI 维护）
├── log.md             # 操作日志（AI 追加）
├── raw/               # 原始素材（只读，不提交到 git）
│   ├── Clippings/     # 网页剪藏
│   ├── 学习笔记/
│   ├── 个人/
│   └── ...
├── wiki/              # AI 维护的 wiki 页面
│   ├── topic-a.md
│   └── topic-b.md
├── concepts/          # 生成的分析报告、答案页面
└── .claude/
    └── skills/
        └── agent-browser/  # 浏览器自动化 skill
```

---

## 三种操作模式

### Ingest（摄入）
```
你：摄入 raw/学习笔记/machine-learning.md
AI：读取文件 → 网络补充 → 生成/更新 wiki 页面 → 更新 index → 写 log
```

### Query（查询）
```
你：什么是联邦学习？它与隐私保护有什么关系？
AI：查阅 wiki → 必要时检索网络 → 综合回答 → 有价值的答案存入 concepts/
```

### Lint（健康检查）
```
你：检查 wiki
AI：扫描断链、孤立页面、缺失引用 → 输出报告 → 执行修复 → 写 log
```

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

## 核心定义
...

## 关键要点
...

## 关联主题
- [[related-topic-a]] - 说明
- [[related-topic-b]] - 说明

## 来源
- 本地文件：raw/xxx/yyy.md
- 网络来源：https://example.com/article
```

---

## 设计理念

**知识复利**：每次摄入不只是存档，而是把新知识织入已有网络。第 100 次摄入时，AI 能发现这篇笔记与 50 个已有页面的关联。

**原始素材不动**：`raw/` 永远保持原样。wiki 是 AI 的解读层，不是原文替代品。

**可验证的来源**：每个页面都标注来源（本地文件路径或 URL），避免 AI 凭空生成内容。

---

## License

MIT
