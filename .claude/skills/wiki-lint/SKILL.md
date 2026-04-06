---
name: wiki-lint
description: Use when auditing a wiki for health issues — contradictions between pages, orphan pages, broken cross-references, stale claims, missing pages, or coverage gaps. Run after every 5-10 ingests.
---

# Wiki Lint

Audit the wiki. Produce a categorized report. Offer concrete fixes. Log the operation.

## Pre-condition

Find `CLAUDE.md` (search from cwd upward). If not found, tell the user to run `wiki-init` first. Read it to get wiki root path and conventions.

## Process

### 1. Build the page inventory

Read `wiki/index.md` and all files in `wiki/pages/`. Build a map of:
- All existing slugs (filenames without `.md`)
- All `[[slug]]` references found in any page
- All `sources` listed in frontmatter

### 2. Run all checks

**Red Errors (must fix)**

- **Broken links** — `[[slug]]` references where no corresponding `wiki/pages/<slug>.md` exists
- **Missing frontmatter** — pages without required `title`, `tags`, `sources`, or `updated` fields

**Yellow Warnings (should fix)**

- **Orphan pages** — pages with zero inbound `[[slug]]` links from any other page (excluding index.md)
- **Contradictions** — claims in one page that directly conflict with claims in another (look for the same entity described differently: dates, counts, names, relationships)
- **Stale claims** — pages not updated within 90 days that contain "current", "latest", "recent", "state-of-the-art", or year literals two or more years old

**Blue Info (consider addressing)**

- **Missing concept pages** — `[[slug]]` references that appear 3+ times across the wiki but have no dedicated page
- **Missing cross-references** — two pages that discuss the same entity but don't link to each other

### 3. Write the lint report

Write `concepts/lint-<today>.md` (lint reports go to concepts/):

```markdown
---
title: Lint Report <today>
tags: [lint, maintenance]
sources: 0
updated: <today>
---

# Lint Report — <today>

## Summary
- Red Errors: N
- Yellow Warnings: N
- Blue Info: N

## Red Broken Links
- [[source-page]] references [[missing-slug]] — does not exist
  Fix: create the page or remove the reference

## Red Missing Frontmatter
- [[page]] is missing: title, updated

## Yellow Orphan Pages
- [[slug]] — no inbound links
  Fix: add link from [[related-page]], or delete if no longer relevant

## Yellow Contradictions
- [[page-a]] says: "<claim>"
- [[page-b]] says: "<conflicting claim>"
  Recommendation: <which to trust, or "investigate further">

## Yellow Stale Claims
- [[page]] last updated <date>, contains "latest" — may be outdated
  Fix: re-verify claims or add a "as of <date>" qualifier

## Blue Missing Concept Pages
- [[slug]] referenced N times but no page exists
  Fix: run wiki-ingest or create a stub

## Blue Missing Cross-References
- [[page-a]] and [[page-b]] both discuss <entity> but don't link to each other
```

### 4. Update `wiki/index.md`

Add entry to **Concepts Pages** table:
```markdown
| [[lint-<today>]] | Lint Report | <today> |
```

### 5. Offer concrete fixes

For each fixable category, offer:
- **Broken links:** "Remove the broken `[[slug]]` references? (I'll show each change before writing)"
- **Missing cross-references:** "Add the missing links between these page pairs?"
- **Orphan page tags:** "Add `status: orphan` to frontmatter of orphan pages?"
- **Missing frontmatter:** "Add missing frontmatter fields with placeholder values?"

Show the exact diff for each change before writing. Apply only after confirmation.

### 6. Append to `wiki/log.md`

Always append — do not ask permission:

```markdown
## [<today>] lint | Wiki Health Check
- Issues found: broken links N, orphan pages N, missing pages N
- Created pages: xxx, yyy (if any)
- Fixed broken links: file → link (if any)
- Fixed orphan pages: file → added reference (if any)
- Other fixes: ... (if no fixes, write "Report only, pending action")
```
