---
name: wiki-ingest
description: Use when adding a new source to a wiki — a paper, article, URL, file, transcript, or any document. One ingest may touch 5-15 wiki pages.
---

# Wiki Ingest

Add a source to the wiki. Read it, discuss with the user, write/update wiki pages, update index, and log.

## Pre-condition

Search for `CLAUDE.md` starting from the current directory and upward. If not found, tell the user to run `wiki-init` first.

Read `CLAUDE.md` to learn: wiki root path, page frontmatter format, cross-reference convention, log entry format, index table format.

## Process

### 1. Accept the source

The source can be:
- **File path** — read it directly from `raw/`
- **URL** — use the `agent-browser` skill to fetch it; save to `raw/<slug>.<ext>` if needed
- **Pasted text** — use what was provided

### 2. Read the source in full

Read all content. For long sources, read in sections. Do not skip.

### 3. Surface takeaways — BEFORE writing anything

Tell the user:
- 3-5 bullet points of key takeaways
- What entities/concepts this introduces or updates
- Whether it contradicts anything already in the wiki (read `wiki/index.md` and relevant pages to check)

Ask: **"Anything specific you want me to emphasize or de-emphasize?"**

Wait for the user's response before proceeding.

### 4. Network supplement (recommended)

For core concepts or key claims, use `agent-browser` skill to fetch current authoritative sources. Prioritize by category:

- **General concepts**: en.wikipedia.org / zh.wikipedia.org
- **Tech/Programming**: docs.python.org, developer.mozilla.org, arxiv.org, github.com
- **AI/ML Papers**: arxiv.org, paperswithcode.com, huggingface.co
- **News/Current Events**: reuters.com, bbc.com, theguardian.com
- **Academic**: scholar.google.com, semanticscholar.org
- **Official docs**: prefer the official site for the topic

### 5. Generate the slug

Lowercase, hyphens, no special characters.
Example: "Attention Is All You Need" → `attention-is-all-you-need`

### 6. Write or update wiki pages

Write to `wiki/pages/<slug>.md`:

```markdown
---
title: <source title>
tags: [<relevant tags>]
sources: <number of sources>
updated: <today>
---

# <Source Title>

**Source:** <original URL or file path in raw/>
**Date ingested:** <today>
**Type:** <paper | article | transcript | code | other>

## Core Definition

<Definition of core concepts>

## Key Takeaways

- <bullet>

## Related Topics

- [[related-slug]] — <relationship>

## Open Questions

<If any>
```

### 7. Update related entity/concept pages

For each entity/concept touched by this source:

- **Page exists:** Read it, add to or update the relevant section, update `updated` date
- **Page doesn't exist:** Create it with the same frontmatter format

### 8. Backlink audit — do not skip

Scan ALL existing pages in `wiki/pages/` for any that mention this source's entities/concepts but don't yet link to the new page. Add `[[new-slug]]` references where appropriate.

This is the step most commonly skipped. A compounding wiki's value comes from bidirectional links.

### 9. Update `wiki/index.md`

Add or update entry in the table format:

```markdown
## Wiki Pages
| Page | Summary | Tags | Last Updated |
|------|---------|------|--------------|
| [[<slug>]] | One-line description | tag1, tag2 | <today> |
```

For any new entity/concept pages created, add those too.

### 10. Append to `wiki/log.md`

Always append — do not ask permission:

```markdown
## [<today>] ingest | <source title>
- Created/Updated pages: xxx, yyy
- Web verification: yes/no, source: <url>
- Key findings: ...
```

### 11. Report to user

- Summary page: `wiki/pages/<slug>.md`
- Entity/concept pages created or updated: <list>
- Pages that received backlinks: <list>
- Index updated
