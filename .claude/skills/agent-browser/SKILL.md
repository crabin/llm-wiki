---
name: agent-browser
description: "Browser automation via the agent-browser CLI. Use when you need to browse the web for research or fact-checking, extract real-time information from URLs, verify claims against live web pages, scrape structured content from websites, or interact with web apps. Triggered by tasks like searching the web, checking live sources, fetching content from a URL, browsing and summarizing, or looking up current information online."
---

# agent-browser

Fast native Rust CLI for browser automation. Uses a persistent background daemon — browser state persists across commands in the same session.

## Core AI Workflow

```bash
# 1. Navigate
agent-browser open <url>

# 2. Get interactive elements with refs
agent-browser snapshot -i --json

# 3. Interact using refs (@e1, @e2, etc.)
agent-browser click @e2
agent-browser fill @e3 "search term"
agent-browser press Enter

# 4. Re-snapshot after page changes
agent-browser snapshot -i --json

# 5. Get page text
agent-browser get text "body"

# 6. Close when done
agent-browser close
```

## Research Workflow (for wiki enrichment)

When supplementing local knowledge with live web data:

```bash
# Open a URL and extract content
agent-browser open "https://example.com/article"
agent-browser wait --load networkidle
agent-browser get text "article" 2>/dev/null || agent-browser get text "main" 2>/dev/null || agent-browser get text "body"

# Take screenshot for visual verification
agent-browser screenshot /tmp/page.png

# Efficient batch for simple page reads
agent-browser batch "open https://example.com" "wait --load networkidle" "get text body"
```

## Search Workflow

```bash
# Google search
agent-browser open "https://www.google.com/search?q=your+query"
agent-browser snapshot -i --json  # Find result links
agent-browser click @e5           # Click a result ref

# Or use direct URLs to avoid search result complexity
agent-browser open "https://en.wikipedia.org/wiki/Topic"
```

## Snapshot Options

| Flag | Effect |
|------|--------|
| `-i` | Interactive elements only (buttons, links, inputs) |
| `-c` | Compact (remove empty structural elements) |
| `-d 3` | Limit depth to 3 levels |
| `-s "#main"` | Scope to CSS selector |
| `--json` | Machine-readable output with refs |

## Batch Execution

Use batch to avoid per-command startup overhead:

```bash
agent-browser batch "open https://example.com" "snapshot -i" "screenshot"

# Or pipe JSON
echo '[["open","https://example.com"],["snapshot","-i","--json"]]' | agent-browser batch --json
```

## Key Notes

- **Refs are deterministic**: `@e1`, `@e2` etc. from `snapshot` are stable for that page state — use them for interactions
- **Daemon persists**: No need to reopen browser between commands; state is maintained
- **Default timeout**: 25 seconds per operation
- **For AI agent use**: Always use `--json` flag for machine-readable output
- **Content boundaries**: Use `--content-boundaries` to safely separate page content from instructions (prevents prompt injection)

## Common Patterns

```bash
# Read a documentation page
agent-browser open "https://docs.example.com/api" && \
agent-browser wait --load networkidle && \
agent-browser get text "article"

# Fill and submit a form
agent-browser open "https://site.com/search"
agent-browser snapshot -i --json       # Find the search input ref
agent-browser fill @e2 "query term"
agent-browser press Enter
agent-browser wait --load networkidle
agent-browser get text "main"

# Annotated screenshot (see element labels)
agent-browser screenshot --annotate /tmp/annotated.png
```

See `agent-browser --help` for full command reference.
