# LLM Wiki — AI-Powered Personal Knowledge Base

**Language / 语言 / 言語：** [中文](README.md) ｜ English（default）｜ [日本語](README.ja.md)

---

## What is this?

LLM Wiki is a personal knowledge management system powered by [Claude Code](https://claude.ai/code). Drop your raw materials (notes, web clippings, papers, journals) into `raw/`, and the AI automatically transforms them into structured, interlinked wiki pages stored in `wiki/`.

**Core idea:**
- `raw/` stores original materials and is never modified
- `wiki/` is written and maintained by AI, with pages linking to each other
- Every ingest enriches the entire knowledge base — not just isolated archiving

---

## How It Works

```
Your notes / web pages / papers
        ↓
   raw/  (read-only original layer)
        ↓  Processed by Claude Code
   wiki/ (AI-maintained structured pages)
        ↓
   index.md (global navigation) + log.md (operation history)
```

During processing, the AI will:
1. Extract key concepts and create/update wiki pages
2. Use `agent-browser` to fetch authoritative web sources to supplement content
3. Build `[[bidirectional links]]` between pages
4. Store valuable analyses in `concepts/`

---

## Quick Start

### Prerequisites

- [Claude Code](https://claude.ai/code) CLI
- (Optional) [agent-browser](https://github.com/mediar-ai/agent-browser): for web-augmented research
  ```bash
  brew install mediar-ai/agent-browser/agent-browser
  ```

### Installation

```bash
git clone https://github.com/crabin/llm-wiki.git my-wiki
cd my-wiki
```

Open with Claude Code:
```bash
claude .
```

### Usage

1. **Add raw materials**: Put your notes, PDFs, or web clippings into the appropriate `raw/` subdirectory
2. **Ingest**: Tell Claude Code "ingest raw/xxx.md"
3. **Query**: Ask questions directly — the AI reads the wiki and answers
4. **Health check**: Say "lint" or "check wiki" — the AI scans and fixes broken links, isolated pages, etc.

---

## Directory Structure

```
llm-wiki/
├── CLAUDE.md          # AI behavior rules (core config)
├── index.md           # Global index (AI-maintained)
├── log.md             # Operation log (AI-appended)
├── raw/               # Raw materials (read-only, not committed to git)
│   ├── Clippings/     # Web clippings
│   ├── Notes/
│   ├── Personal/
│   └── ...
├── wiki/              # AI-maintained wiki pages
│   ├── topic-a.md
│   └── topic-b.md
├── concepts/          # Generated analyses and answer pages
└── .claude/
    └── skills/
        └── agent-browser/  # Browser automation skill
```

---

## Three Operation Modes

### Ingest
```
You:  ingest raw/notes/machine-learning.md
AI:   Read file → Supplement with web sources → Create/update wiki pages → Update index → Write log
```

### Query
```
You:  What is federated learning and how does it relate to privacy?
AI:   Read wiki → Search web if needed → Synthesize answer → Save valuable answers to concepts/
```

### Lint (Health Check)
```
You:  check wiki  (or: lint)
AI:   Scan for broken links, isolated pages, missing cross-references → Report → Fix → Write log
```

---

## Wiki Page Format

Every wiki page follows a consistent format:

```markdown
---
title: Topic Name
tags: [tag1, tag2]
sources: 3
updated: 2026-04-06
---

# Topic Name

## Definition
...

## Key Points
...

## Related Topics
- [[related-topic-a]] - description
- [[related-topic-b]] - description

## Sources
- Local file: raw/xxx/yyy.md
- Web: https://example.com/article
```

---

## Design Philosophy

**Compound knowledge**: Each ingest doesn't just archive — it weaves new knowledge into the existing network. By the 100th ingest, the AI can find connections between a new note and 50 existing pages.

**Raw materials untouched**: `raw/` always stays as-is. The wiki is the AI's interpretation layer, not a replacement for the source.

**Verifiable sources**: Every page cites its sources (local file paths or URLs), preventing AI from generating unsupported content.

---

## License

MIT
