---
name: export-memories
version: 1.2.0
description: >
  Export session histories and synthesize knowledge from chat memory into
  structured discovery files. Enumerate available sessions and export user-selected
  sessions into a Markdown file.
parent: prompteng-SKILL.md
peers: captureng
---

# export-memories

*export-memories* - export session histories and synthesize knowledge from chat memory.

Exports stored memories and past conversation context into a structured Markdown
file for building an externally stored long-term memory knowledge graph. Sources:
userMemories (Long-Term/summary-grade) and conversation_search + recent_chats
(Selective/verbatim-grade). Does not guess - marks unknown fields explicitly.
Surfaces errors, incongruous items, points of tension, and conflicting matter.

---

## Identity

| Field | Value | Source |
|---|---|---|
| Name | - | |
| Age | - | |
| Location | - | |
| Education | - | |
| Family | - | |
| Relationships | - | |
| Languages | - | |
| Personal interests | - | |

## Does NOT Cover

- Session initialization → `prompteng-SKILL.md §2`
- Checkpoint write order → `captureng-SKILL.md`
- Skill packaging → `packageng-SKILL.md`

## Note

Search history is currently limited to a maximum of 100 chat sessions (5 paginated calls x 20 results per call). If the total number of sessions in the project exceeds 100, the user is expected to invoke the discovery process again for the remaining sessions, using the oldest retrieved session's timestamp as the starting boundary for the next run.

---

## 1. Discovery Protocol

**[RULES]**

1. Before executing, plan an efficient search strategy for sub-tasks. State plan
   explicitly before first tool call.
2. Do not guess any detail. If data is unavailable from verifiable sources
   (userMemories, conversation_search, recent_chats), mark the field as `unknown`.
3. Preserve user's words verbatim where possible - especially for instructions,
   preferences, and corrections.
4. Surface and highlight: errors, incongruous items, points of tension, conflicting
   matter. Never silently resolve.
5. Ask for clarifications when needed before proceeding.
6. Chat history search is project-scoped. Cross-project content is inaccessible by
   design - flag this if relevant to completeness.
7. Keep the export file's frontmatter and footer as small as possible. Frontmatter:
   one line only - `export: {filename}`. Footer: one line only -
   `*{filename} - Generated: {session ID} · {model} · {date}*`. Move all
   sources/scope/sessions metadata into the Sources section (§2 schema).
8. Use hyphens (-) only. No em-dashes or en-dashes anywhere in the export file or
   this skill document. Replace any em-dash or en-dash with a plain hyphen.

**[ACTIONS]**

1. Call `recent_chats(n=20, sort_order='desc')` to enumerate all available sessions in the project. Present the list with session names, dates, and one-line summaries. Ask the user: "Which sessions do you want to export?" (users may specify "all", "historical only", individual session names, or date ranges). Wait for explicit response before proceeding with tool calls.
2. State sub-task plan with tool call sequence before executing.
3. Execute search plan:
   a. `recent_chats(n=20, sort_order='desc')` - enumerate all project sessions,
      get titles + timestamps.
   b. Paginate if needed (up to 5 calls) using `before` cursor.
   c. Targeted `conversation_search` calls per topic cluster: identity/career,
      preferences/instructions, project names, key decisions, artifact names.
   d. Combine userMemories (in context) with Selective-tier verbatim results.
4. Compile output file per §2 schema.
5. Run completeness check (§3) after presenting file.
6. Report what remains uncaptured and why.

---

## 2. Output Schema

Single Markdown file. Sections in this order.

### Header Block

```markdown
---
export: {YYYY_MM_DD-HHMMSS}-{project}-knowledge-discovery.md
---
```

### Category 1 - Identity

Fields: Name, age, location, education, family, relationships, languages,
personal interests. Mark `unknown` if unavailable. Cite source per field.

### Category 2 - Career

Fields: current and past roles, companies, capabilities, general areas of interest.
Mark `unknown` if unavailable.

### Category 3 - Projects

One entry per meaningful project. Per entry:
- **What it does** - one sentence.
- **Status** - active / paused / complete / open-source pending.
- **Key decisions** - table: Decision | One-liner.

#### Category 3.1 - Chat Name and History of Messages

One subsection per chat session. Sorted newest-first. Per session:
- Session title and timestamp as header.
- Table: `[YYYY_MM_DD-HHMMSS]` | Message content.
- Verbatim where retrieved. Flag sessions where only summary is available.

#### Category 3.2 - Instructions

Rules the user explicitly stated across sessions - tone, format, style,
"always do X", "never do Y", corrections to agent behavior. Also:
- Design, decision, and judgment patterns from memories and conversations.
- 🟢 **Delightful / novel** - pleasantly surprising things learned.
- 🔴 **Cumbersome / unrewarding** - friction points flagged for awareness.

#### Category 3.3 - Project Artifacts

Table sorted newest-first:

| Datetime | Artifact | Notes |
|---|---|---|
| `[YYYY_MM_DD-HHMMSS]` | artifact name | brief note |

### Category 5 - Preferences

Broadly applicable preferences confirmed across multiple sessions. Subsections:
working style, output format, session governance, platform, IP/ethics.

### Sources

```
sources: {userMemories (Long-Term/summary-grade) + conversation_search + recent_chats (Selective/verbatim-grade)}
scope: {project name} - project-scoped chat history only
sessions_retrieved: {count} sessions ({session IDs})
```

### Conflict + Gap Summary

Table: `#` | Item | Nature | Status.

### Footer

```markdown
*{filename} - Generated: {session ID} · {model} · {date}*
```

---

## 3. Completeness Check

**[ACTIONS]**

After presenting the export file:

1. Count sessions retrieved vs sessions expected (from `recent_chats` total).
2. Identify categories with `unknown` fields - note whether data simply was not
   stated vs data is inaccessible (different root cause).
3. Flag sessions where only summary was available (verbatim not retrieved).
4. Report cross-project inaccessibility if relevant.
5. State explicitly: "Completeness check: {N} of {M} sessions verbatim. {K} fields
   unknown because not stated. {J} fields unknown due to project-scope access limit."

---

## 4. Source Trust Hierarchy

Per `agent.md §3.1` four-tier model - applied to this skill:

| Tier | Use in discovery | Trust |
|---|---|---|
| Long-Term (userMemories) | Summary-grade context; fills gaps where chat history absent | Hints - not verbatim |
| Selective (conversation_search, recent_chats) | Verbatim evidence of what was said | Evidence-grade; cite verbatim |
| Short-Term (platform memories) | Do not use as primary source | Informational only |
| Latent (model training) | Never use for personal facts | Forbidden for identity/career claims |

**[RULES]**

1. A claim about the user's identity, career, or stated preferences must trace to
   a Selective-tier verbatim quote OR a userMemory summary explicitly citing a
   session. If it cannot, mark `unknown`.
2. Latent knowledge must never be used to infer personal details. No assumptions.
3. When Selective and userMemories conflict, cite both and flag in Conflict + Gap Summary.

---

## 5. Output File

**[ACTIONS]**

1. Write export to `/home/claude/{filename}`, then copy to
   `/mnt/user-data/outputs/{filename}`.
2. Compute BLAKE3: `b3sum /mnt/user-data/outputs/{filename}` (install via `apt-get install -y b3sum` if absent). Record first 8 chars only in user-facing output.
3. Call `present_files` with output path.
4. After presenting, run §3 completeness check inline (not in a separate file).

---

*export-memories-SKILL.md v1.2.0*
