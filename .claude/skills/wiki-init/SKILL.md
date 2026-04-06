---
name: wiki-init
description: Use when bootstrapping a new personal wiki for any knowledge domain — research, codebase documentation, reading notes, competitive analysis, or any long-term knowledge accumulation project.
---
# Wiki Init

Bootstrap a new LLM-maintained wiki at a user-specified path.

## Pre-flight

Check whether a `CLAUDE.md` already exists nearby. If yes, ask the user if they want to reinitialize or just continue with the existing wiki.

## Process

### 1. Gather configuration (one question at a time)

Ask:

1. **Where should the wiki live?** (absolute path, e.g. `~/wikis/ml-research`)
2. **What is the domain/purpose?** (one sentence)
3. **What types of sources will you add?** (papers, URLs, code files, transcripts, etc.)
4. **What categories should `index.md` use?**
   - Research default: `Wiki Pages | Concepts Pages | Topic Relations | Quick Navigation`
   - Codebase default: `Modules | APIs | Decisions | Flows`
   - Or specify custom

### 2. Create directory structure

```
<wiki-root>/
├── CLAUDE.md         ← conventions + absolute path (how other skills find the wiki)
├── raw/              ← immutable source documents (you add these, LLM never modifies)
├── wiki/
│   ├── index.md      ← content catalog: table format with page, summary, tags, updated
│   ├── log.md        ← append-only operation log
│   └── pages/        ← all wiki pages, flat, slug-named (NO subdirectories)
└── concepts/         ← generated reports, analyses, answers
```

**Critical:** `wiki/pages/` is flat. All pages live here as `<slug>.md`. No subdirectories. Slugs are lowercase, hyphen-separated.

### 3. Write `CLAUDE.md`

Create a project-specific CLAUDE.md with the following structure:

```markdown
# <Domain> Wiki Schema

## What Is This

LLM Wiki for <user's domain description> — a structured knowledge base continuously maintained by AI.
Raw sources are never modified; the wiki layer is incrementally built and interconnected by AI.

Today's date: {{currentDate}}

---

## Directory Structure

```
raw/          Raw sources (read-only, never modify)

wiki/         AI-maintained wiki pages
  pages/      Topic pages (one .md file per topic)
  index.md    Full index (table format)
  log.md      Operation log (append-only, never modify history)

concepts/     Generated reports, analyses, answer pages
```

---

## Wiki Page Standards

- One `.md` file per topic, filename in lowercase with hyphens: `machine-learning.md`
- Use `[[topic-name]]` format to link related topics (Obsidian wikilink)
- Add YAML frontmatter at the top of each page:

\`\`\`yaml
---
title: Topic Name
tags: [tag1, tag2]
sources: Number of sources
updated: YYYY-MM-DD
---
\`\`\`

- Suggested page structure: Core Definition → Key Takeaways → Related Topics → Open Questions

---

## Operations

### Ingest (Add New Sources)

1. Read source files from `raw/` (do not modify originals)
2. Discuss key takeaways with user (optional)
3. **Network supplement** (recommended): Call `agent-browser` skill to fetch authoritative sources
4. Write or update related topic pages in `wiki/pages/`, **cite sources**
5. Update `wiki/index.md` (table format)
6. Append a record to `wiki/log.md`

### Query (Ask Questions)

1. First read `wiki/index.md` to find relevant pages
2. Read relevant wiki pages
3. If information is insufficient, call `agent-browser` skill to fetch current sources
4. Synthesize answer with citations
5. If valuable, save to `concepts/`, update `wiki/index.md`
6. Append a record to `wiki/log.md`

### Lint (Health Check)

1. Scan wiki pages for issues
2. Output check report with suggested fixes
3. Append check record to `wiki/log.md`

---

## Index Maintenance Rules

`wiki/index.md` uses table format:

\`\`\`markdown
# Index

## Wiki Pages
| Page | Summary | Tags | Last Updated |
|------|---------|------|--------------|
| [[topic]] | One-line description | tag1, tag2 | YYYY-MM-DD |

## Concepts Pages
| Page | Type | Date |
|------|------|------|
| [[analysis-xxx]] | Analysis Report | YYYY-MM-DD |
\`\`\`

Update index after every ingest or concept page generation.

---

## Log Maintenance Rules

`wiki/log.md` is append-only operation record:

\`\`\`markdown
# Operation Log

Append-only, never modify history.

Format: \`## [YYYY-MM-DD] <operation> | <title>\`

---

## [YYYY-MM-DD] init | Wiki Initialized
\`\`\`

---

## Core Principles

- **raw/ is read-only**: Never modify raw sources
- **wiki/ is AI-written**: Users read, AI maintains
- **Knowledge compounding**: Each ingest enriches the entire wiki
- **Cross-references first**: Integrate new info into existing pages
- **Mark contradictions**: Clearly flag when new sources conflict with old conclusions
- **Preserve good analysis**: Save valuable answers to `concepts/`
```

### 4. Write `wiki/index.md`

```markdown
# Index

## Wiki Pages
| Page | Summary | Tags | Last Updated |
|------|---------|------|--------------|
<!-- entries added by wiki-ingest -->

## Concepts Pages
| Page | Type | Date |
|------|------|------|
| (No saved analysis pages yet) | - | - |

## Topic Relations

```
(To be populated)
```

## Quick Navigation

### By Research Area
(To be populated)
```

### 5. Write `wiki/log.md`

```markdown
# Operation Log

Append-only, never modify history.

Format: `## [YYYY-MM-DD] <operation> | <title>`

---

## [<today>] init | <domain>
- Created CLAUDE.md schema
- Initialized log.md
```

### 6. Create `concepts/` directory

```bash
mkdir -p concepts
```

### 7. Confirm

Tell the user:

- Wiki initialized at `<path>`
- Add sources to `raw/` manually, or run `wiki-ingest` directly with a URL or file path
- Run `wiki-lint` periodically to keep the wiki healthy
- `CLAUDE.md` is how all other skills locate this wiki — do not move or delete it
