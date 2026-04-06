# LLM Wiki — 个人知识库 Schema

## 这是什么

一个由 AI 持续维护的结构化个人知识库。
原始素材永远不动，wiki 层由 AI 增量构建并保持互联。

今天的日期: {{currentDate}}

---

## 目录结构

```
raw/          原始素材（只读，永远不要修改）
wiki/         AI 维护的 wiki 页面（每主题一个 .md 文件）
concepts/     生成的报告、分析、答案页面
index.md      全库索引（每次 ingest 后更新）
log.md        操作日志（追加写入，不修改历史）
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

## 操作流程

### Ingest（摄入新素材）

当用户说"摄入"或"处理"某个文件时：

1. 读取 `raw/` 中的源文件（不修改原文）
2. 与用户讨论关键要点（可选）
3. **网络补充**（推荐）：对核心概念或关键主张，调用 `agent-browser` skill 检索当前权威来源，补充本地文件可能缺失的最新信息。优先按类别选择来源：
   - **通用概念**：en.wikipedia.org / zh.wikipedia.org
   - **技术/编程**：docs.python.org、developer.mozilla.org、arxiv.org、github.com
   - **AI/ML 论文**：arxiv.org、paperswithcode.com、huggingface.co
   - **新闻/时事**：reuters.com、bbc.com、theguardian.com
   - **学术**：scholar.google.com、semanticscholar.org
   - **官方文档**：优先访问主题的官网
4. 在 `wiki/` 中写或更新相关主题页面，**标注来源**：本地文件来源用文件路径，网络来源用 URL
5. 更新 `index.md`（添加或修改条目）
6. 在 `log.md` 追加一条记录，格式：
   ```
   ## [YYYY-MM-DD] ingest | 文件名或标题
   - 新建/更新的 wiki 页面：xxx, yyy
   - 网络查证：是/否，来源：url
   - 关键发现：...
   ```

一次摄入可能触及 5–15 个 wiki 页面，这是正常的。

### Query（查询）

当用户提问时：

1. 先读 `index.md`，找到相关页面
2. 读取相关 wiki 页面
3. 若本地 wiki 信息不足或需要验证时效性内容，调用 `agent-browser` skill 检索当前来源（优先权威站点，同上）
4. 综合回答，注明引用来源（`[[page-name]]` 用于本地页面，URL 用于网络来源）
5. 如果答案有价值，将其存入 `concepts/` 作为独立页面，并更新 `index.md`
6. 在 `log.md` 追加一条记录，格式：
   ```
   ## [YYYY-MM-DD] query | 问题摘要
   - 查阅页面：xxx, yyy
   - 网络查证：是/否，来源：url
   - 生成 concept 页面：是/否，路径：concepts/xxx.md
   - 关键结论：...
   ```

### Lint（健康检查）

当用户说"检查 wiki"或"lint"时：

1. 扫描 wiki 页面，找出：
   - 互相矛盾的内容
   - 已被新素材推翻的旧结论
   - 孤立页面（没有其他页面指向它）
   - 重要概念被多次提及但没有独立页面
   - 缺失的交叉引用
2. 输出一份检查报告，建议修复动作
3. 在 `log.md` 追加检查记录（无论是否执行修复），格式：
   ```
   ## [YYYY-MM-DD] lint | wiki 健康检查
   - 发现问题：断链 N 处、孤立页面 N 个、缺失页面 N 个
   - 新建页面：xxx, yyy（若有）
   - 修复断链：文件 → 链接（若有）
   - 修复孤立：文件 → 添加了引用（若有）
   - 其他修复：...（若无修复则填"仅输出报告，待处理"）
   ```

---

## Index 维护规则

`index.md` 是全库导航枢纽，结构如下：

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

## 可用工具

### agent-browser（浏览器自动化，可选）

用于补充本地文件缺乏的实时信息。需要单独安装：

- macOS: `brew install mediar-ai/agent-browser/agent-browser`
- 或参考：https://github.com/mediar-ai/agent-browser

安装后，使用时调用 `agent-browser` skill 获取完整用法。

**使用时机：**

- 原始素材有 URL 引用但无本地副本时
- wiki 内容需要验证当前状态时
- 查询涉及时效性信息（版本、日期、当前状态）时
- 生成 concept 页面需要多角度来源时

---

## 核心原则

- **raw/ 只读**：永远不修改原始素材
- **wiki/ 由 AI 写**：用户读，AI 维护
- **知识复利**：每次摄入都让整个 wiki 更丰富，而不是孤立存档
- **交叉引用优先**：新信息要整合进已有页面，而不是孤立创建
- **矛盾要标注**：新素材与旧结论冲突时，在页面中明确标出
- **好的分析要留存**：有价值的答案存入 `concepts/`，不要消失在聊天历史里
