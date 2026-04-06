---
name: wiki-query
description: Use when asking a question against a personal wiki built with wiki-init and wiki-ingest. Do not answer from general knowledge — always read the wiki pages first.
---

# Wiki Query

Ask a question. Read the wiki. Synthesize with citations. Offer to file the answer back.

## Pre-condition

Find `CLAUDE.md` (search from cwd upward). If not found, tell the user to run `wiki-init` first. Read it to get wiki root path and cross-reference convention.

## Process

### 1. Read `wiki/index.md` first

Scan the full index to identify which pages are likely relevant. Do NOT answer from general knowledge — the wiki is the source of truth here, even if you think you know the answer.

### 2. Read relevant pages

Read the identified pages in full. Follow one level of `[[slug]]` links if they point to pages that seem relevant to the question.

### 3. Network supplement (if needed)

If local wiki information is insufficient or needs verification for time-sensitive content, use `agent-browser` skill to fetch current sources. Prioritize authoritative sites:

- **General concepts**: en.wikipedia.org / zh.wikipedia.org
- **Tech/Programming**: docs.python.org, developer.mozilla.org, arxiv.org, github.com
- **AI/ML Papers**: arxiv.org, paperswithcode.com, huggingface.co
- **News/Current Events**: reuters.com, bbc.com, theguardian.com
- **Academic**: scholar.google.com, semanticscholar.org

### 4. Synthesize the answer

Write a response that:
- Is grounded in the wiki pages you read
- Cites inline using `[[slug]]` for local pages, URLs for web sources
- Notes agreements and disagreements between pages
- Flags gaps: "The wiki has no page on X" or "[[page]] doesn't cover Y yet"
- Suggests follow-up sources to ingest or questions to investigate

Format for the question type:
- Factual → prose with citations
- Comparison → table
- How-it-works → numbered steps
- What-do-we-know-about-X → structured summary with open questions

### 5. Always offer to save

After answering, say:

> "Worth saving to `concepts/<suggested-slug>.md`?"

If yes:
- Write the page with frontmatter: `tags: [query, analysis]`, `sources: <number>`, `updated: <today>`
- Update `wiki/index.md` under **Concepts Pages** table:
  ```markdown
  | [[<slug>]] | Query Synthesis | <today> |
  ```
- Append to `wiki/log.md`:
  ```markdown
  ## [<today>] query | <question summary>
  - Related pages: <slug>
  - Web verification: <yes/no>, source: <url if applicable>
  - Key findings/conclusion: Answer saved to [[<slug>]]
  ```

If no:
- Append to `wiki/log.md`:
  ```markdown
  ## [<today>] query | <question summary>
  - Related pages: <list of pages read>
  - Web verification: no
  - Key findings/conclusion: Answer provided, not filed
  ```

## Common Mistakes

- **Answering from memory** — Always read the wiki pages. The wiki may contradict what you think you know, and that contradiction is valuable signal.
- **Skipping the save offer** — Good query answers compound the wiki's value. Always offer.
- **No citations** — Every factual claim should trace back to a `[[slug]]` or URL.
