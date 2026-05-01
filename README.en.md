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

## Screenshot

![LLM Wiki in action — querying and updating wiki in Claude Code](assets/demo-screenshot.png)

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

For cross-agent setups, generic agents can start from `AGENTS.md` and `.agents/skills/`, while Claude Code continues to use `CLAUDE.md` and `.claude/skills/`.

### Usage

1. **Initialize**: Tell Claude Code "initialize wiki" (first time only)
2. **Add raw materials**: Put your notes, PDFs, or web clippings into the appropriate `raw/` subdirectory
3. **Ingest**: Tell Claude Code "ingest raw/xxx.md" or provide a URL/text
4. **Query**: Ask questions directly — the AI reads the wiki and answers
5. **Health check**: Say "check wiki" — the AI scans for broken links, isolated pages, and similar issues, then proposes or applies fixes per the skill workflow.

---

## Directory Structure

```
llm-wiki/
├── AGENTS.md          # Cross-agent entrypoint and compatibility guidance
├── CLAUDE.md          # Claude-specific behavior rules (core config)
├── .agents/
│   └── skills/        # Compatibility references for other agents/tools
│       ├── agent-browser/
│       ├── wiki-init/
│       ├── wiki-ingest/
│       ├── wiki-query/
│       ├── wiki-lint/
│       └── wiki-update/
├── .claude/
│   └── skills/        # Canonical skill definitions
│       ├── agent-browser/   # Browser automation
│       ├── wiki-init/       # Initialize wiki
│       ├── wiki-ingest/     # Ingest new sources
│       ├── wiki-query/      # Query wiki
│       ├── wiki-lint/       # Health check
│       └── wiki-update/     # Update pages
├── raw/               # Raw materials (read-only, not committed to git)
│   ├── Clippings/     # Web clippings
│   ├── Notes/
│   ├── Personal/
│   └── ...
├── wiki/              # AI-maintained wiki pages
│   ├── index.md       # Global index (table format)
│   ├── log.md         # Operation log (append-only)
│   └── pages/         # Topic pages (one per topic)
│       ├── topic-a.md
│       └── topic-b.md
└── concepts/          # Generated analyses and answer pages
```

---

## Five Operation Modes

### Init (Initialize)

Initialize the wiki structure on first use:

```
You:  initialize wiki
AI:   Ask configuration → Create directory structure → Write CLAUDE.md and AGENTS.md → Initialize index.md and log.md
```

### Ingest

```
You:  ingest raw/notes/machine-learning.md
AI:   Read file → Discuss key points with user → Web supplement → Create/update wiki pages → Backlink audit → Update index → Write log
```

A single ingest may touch 5-15 related pages.

### Query

```
You:  What is federated learning and how does it relate to privacy?
AI:   Read index → Read relevant pages → Search web if needed → Synthesize answer (with citations) → Offer to save to concepts/
```

The AI always reads wiki pages first, never answers from memory.

### Lint (Health Check)

```
You:  check wiki
AI:   Scan for broken links, orphan pages, contradictions, stale claims → Output categorized report → Offer fixes → Execute fixes → Write log
```

Recommended to run after every 5-10 ingests.

### Update

```
You:  Update xxx page because new information shows...
AI:   Read current content → Propose changes (show diff) → Confirm and write → Check downstream effects → Update index → Write log
```

All changes must cite their sources.

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

**Source:** raw/xxx/yyy.md or https://example.com/article
**Date ingested:** 2026-04-06
**Type:** paper | article | transcript | code | other

## Core Definition
...

## Key Points
- ...

## Related Topics
- [[related-topic-a]] - description
- [[related-topic-b]] - description

## Unresolved Questions
<if any>

## Sources
- Local file: raw/xxx/yyy.md
- Web source: https://example.com/article
```

---

## Skills Reference

`AGENTS.md` and `.agents/skills/` are compatibility entrypoints for non-Claude agents. Canonical skill definitions remain in `.claude/skills/`.


### wiki-init

Initialize a new wiki knowledge base for any knowledge domain (research, codebase docs, reading notes, competitive analysis, etc.). Interactively asks for configuration (path, domain, source types, index categories), creates directory structure, writes `CLAUDE.md`, `AGENTS.md`, `index.md`, `log.md`, and sets up `.agents/skills/` as a compatibility layer that points back to `.claude/skills/`.

### wiki-ingest

Ingest new sources (files, URLs, text). Core flow:
1. Read source in full (never skip content)
2. **Discuss before writing**: present 3-5 key takeaways, ask user for emphasis preferences
3. Web supplement (recommended): fetch authoritative sources via `agent-browser` to verify core concepts
4. Generate slug, write/update wiki pages (with source citations)
5. Update related entity/concept pages (create if missing)
6. **Backlink audit** (critical step): scan all existing pages and add links to the new page
7. Update index and log

### wiki-query

Query wiki to answer questions. Always reads `wiki/index.md` and relevant pages first — never answers from memory. When local wiki info is insufficient, fetches web sources via `agent-browser`. Uses `[[slug]]` citations for local pages, URLs for web sources. **Always offers** to save the answer to `concepts/`.

### wiki-lint

Health check, detects:
- 🔴 **Errors**: Broken links (`[[slug]]` pointing to non-existent pages), missing frontmatter
- 🟡 **Warnings**: Orphan pages (zero inbound links), contradictions, stale claims (90+ days without update containing "latest"/"current")
- 🔵 **Info**: Frequently referenced concepts without dedicated pages, missing cross-references

Generates report to `concepts/lint-<date>.md`, offers concrete fixes with **diffs shown before writing**.

### wiki-update

Update existing wiki pages. Flow:
1. Read current content, show before/after diff (with reason and source citation)
2. **Per-page confirmation** before writing — no batch apply
3. **Downstream impact check**: scan pages that reference updated pages, flag those needing sync
4. **Contradiction sweep**: if new info conflicts with wiki content, find all pages containing the contradicted claim and update all
5. Update index and log — all changes must cite sources

### agent-browser

Browser automation for web-augmented research. Prioritizes authoritative sources by category:

| Category | Preferred Sources |
|----------|-------------------|
| General concepts | Wikipedia (en/zh) |
| Tech/Programming | Official docs, MDN, arxiv, GitHub |
| AI/ML | arxiv, Papers with Code, Hugging Face |
| Academic | Google Scholar, Semantic Scholar |
| Official docs | The technology/product's official website |

---

## Design Philosophy

**Compound knowledge**: Each ingest doesn't just archive — it weaves new knowledge into the existing network. By the 100th ingest, the AI can find connections between a new note and 50 existing pages.

**Raw materials untouched**: `raw/` always stays as-is. The wiki is the AI's interpretation layer, not a replacement for the source.

**Verifiable sources**: Every page cites its sources (local file paths or URLs), preventing AI from generating unsupported content.

**Backlinks first**: After creating a new page, the AI scans all existing pages and adds links to the new page wherever relevant. This is the key to compound knowledge value.

---

## License

MIT
