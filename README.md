# export-memories

Export session histories and synthesize knowledge from chat memory into structured discovery files.

> `export-memories` enables enumeration of available chat sessions and export of user-selected sessions into a consolidated Markdown knowledge graph for external long-term storage and cross-project reference.

## Introduction

Session history enumeration, user-directed selection, knowledge synthesis, and structured Markdown export with conflict surfacing and completeness checks.

Standalone skill with optional integration to `captureng` and `prompteng` peer skills. Works independently or as part of a multi-session knowledge management workflow.

## Set of files

| File | Purpose |
|---|---|
| `SKILL.md` | Router — load order, peer references |
| `export-memories-SKILL.md` | Content — export protocol, schema, search strategy, conflict rules |
| `README.md` | Installation and usage instructions for this software package |
| `LICENSE` | GNU GPL 3.0 |

## Install

- **claude.ai web platform:** upload `export-memories.skill` via Project Knowledge → Skills (recommended). Or paste `export-memories-SKILL.md` contents into Personal Preferences via `Settings > General`.

- **Claude Code desktop app:** copy contents of this folder into `~/.claude/skills/export-memories/`.

- **Other platforms:** adapt this set of files to your harness+model. Star or Fork the git repo if you like.

## Quickstart

**Step 1 — Upload.** In Claude.ai → Project Knowledge → upload `export-memories.skill`.

**Step 2 — Invoke.** In chat, type:
```
/export-memories
```
or
```
export memories
```

Agent enumerates all available chat sessions in the project and presents a list with dates and summaries. Waits for user to specify which sessions to export.

**Step 3 — Select sessions.** User responds with:
- `"all"` — export all available sessions
- `"historical only"` — exclude current session
- `"CP_05, CP_07, CP_09"` — export specific named sessions
- Date range — e.g., `"2026-04-20 to 2026-04-25"` — export sessions within range

**Step 4 — Export.** Agent retrieves, synthesizes, and outputs Markdown file with knowledge discovery structure: Identity, Career, Projects, Chat History, Instructions, Preferences, Sources, and Conflict+Gap Summary.

> **Claude Code:** place `export-memories-SKILL.md` in `.claude/` or project root, reference in `CLAUDE.md`. Same invocation syntax.

## When It Triggers

- Explicit trigger: `/export-memories`, `"export memories"`, `"export history"`, `"knowledge discovery"`.
- Does not trigger on ambient chat — must be explicitly invoked.

## Peer Skills

Optional integration:

- **[prompteng](https://github.com/ecological-codes/prompteng)** — parent skill; defines session init, 7-part framework, memory precedence rules.
- **[captureng](https://github.com/ecological-codes/captureng)** — session-knowledge capture, CHECKPOINT mode — complements export-memories for session closure workflows.

## Companion Files

Available in **[ecological-codes/user-prefs](https://github.com/ecological-codes/user-prefs)**

- `memory-enablement-checklist.md` — 4-tier memory classification and conflict resolution; defines Selective tier used by export-memories.

## Limitations

- **Search history max:** 100 chats per invocation (5 paginated API calls × 20 results per call). If project exceeds 100 sessions, user re-invokes for remaining sessions using oldest retrieved session timestamp as boundary.
- **Cross-project isolation:** export-memories is scoped to current project only. Cannot enumerate or export sessions from other projects.
- **Transcript verbosity:** extracts full session summaries. Very large projects may produce >50KB Markdown files.

## Style Convention

Directives use the following section headers with numbered lists, shared across all 4 peer skills:

- **[RULES]** — enforceable constraints applied at runtime.
- **[ACTIONS]** — autonomous steps agent executes in normal workflow.
- **[HUMAN ACTIONS]** — UI actions; agent skips, cannot delegate.

## License

GNU GPL 3.0. (C) Copyright 2026 - Sameer Khan - Various and Several Rights Reserved.

---
README.md v1.2.0 - Human Approved
