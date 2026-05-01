# AGENTS.md

This repository is a scaffold for an AI-maintained personal wiki.

## Purpose

- `raw/` stores original source material and should be treated as read-only
- `wiki/` stores AI-maintained structured knowledge pages
- `concepts/` stores generated analyses, summaries, and answer pages

## Agent entrypoints

- `CLAUDE.md` contains Claude-specific project rules and workflow triggers
- `.claude/skills/` contains the canonical skill definitions for this repository
- `.agents/skills/` contains compatibility references for other agents and tools

When a skill exists in both places, treat `.claude/skills/<skill>/SKILL.md` as the source of truth.

## Working rules

- Never modify files under `raw/`
- Prefer updating existing wiki pages over creating disconnected new ones
- Keep citations and source paths when writing wiki or concept content
- Update `wiki/index.md` and `wiki/log.md` when a workflow requires it

## Skill map

Available compatibility entrypoints under `.agents/skills/`:

- `agent-browser`
- `wiki-init`
- `wiki-ingest`
- `wiki-query`
- `wiki-lint`
- `wiki-update`

Each compatibility skill file points to the canonical definition in `.claude/skills/`.
