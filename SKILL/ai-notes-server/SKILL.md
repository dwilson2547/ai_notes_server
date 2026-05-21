---
name: ai-notes-server
description: >
  YOU MUST use this skill at the start and end of every task and after every resolved problem.
  The AI Notes Server (http://localhost:8000) is the persistent knowledge layer for this workspace.
  Always check notes before starting work. Always save findings after non-trivial resolutions.
  Trigger phrases: check notes, save findings, persist knowledge, notes server.
applyTo: "**"
---

# AI Notes Server

A shared knowledge layer. Notes persist across sessions so agents can build on prior work instead of re-discovering the same patterns.

---

## When to Use

> **YOU MUST follow these rules on every task, without exception.**

**Before starting any task** — YOU MUST search for relevant notes first. Use broad terms,
the environment, and component names as search queries. Do not skip this step even for simple tasks.

**Immediately after resolving a non-obvious problem** — if the fix required more
than one attempt, wasn't in documentation, or involved an undocumented behavior,
write a note before moving on. Don't wait until end of session.

**After any significant architectural or configuration decision** — capture what
was decided and why, especially if alternatives were considered and rejected.

**When discovering environment-specific behavior** — version quirks, 
toolchain gotchas, platform-specific workarounds.

**After initial tool call failed and modifications were made** — 
pause and evaluate whether the path to resolution was non-obvious.
If so, note it immediately.

**Do not write a note for** — things easily found in official docs, 
routine tasks with expected outcomes, or anything that's already noted.

---

## Checking Notes

Before starting a task, run a text search and a semantic search:

```bash
# Text search — exact terms
curl "http://localhost:8000/notes?q=YOUR_TOPIC&limit=10"

# Semantic search — similar meaning
curl "http://localhost:8000/notes/search?q=YOUR_TOPIC+DESCRIPTION&limit=5"
```

If the topic has tags you know, filter by them:

```bash
curl "http://localhost:8000/notes?tags=wordpress,scraping&limit=20"
```

---

## Saving a Note

Keep notes **minimal and factual** — key learnings, gotchas, patterns. Not strategy or plans.

```bash
curl -X POST http://localhost:8000/notes \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Short descriptive title",
    "tags": ["technology", "pattern-type", "site-or-tool-name"],
    "content": "2-5 sentences of key facts. What worked. What didn'\''t. Specific details that save time next time."
  }'
```

---

## Updating an Existing Note

Prefer updating over creating a near-duplicate.

```bash
# Find the note first
curl "http://localhost:8000/notes?q=topic"

# Update it
curl -X PUT http://localhost:8000/notes/{id} \
  -H "Content-Type: application/json" \
  -d '{"content": "Updated content with new findings..."}'
```

---

## API Reference

Full guide: `GET http://localhost:8000/instructions`

| Method | Path | Purpose |
|--------|------|---------|
| `GET` | `/notes?q=&tags=&archived=` | Text search + filter |
| `GET` | `/notes/search?q=&limit=` | Semantic similarity search |
| `GET` | `/notes/{id}` | Get single note |
| `POST` | `/notes` | Create note |
| `PUT` | `/notes/{id}` | Update note |
| `DELETE` | `/notes/{id}` | Delete note |
| `POST` | `/notes/{id}/archive` | Archive/unarchive |
| `GET` | `/export` | Export all as JSON |
| `POST` | `/import` | Import from JSON array |

---

## Note Format

```json
{
  "title": "Short, searchable title",
  "tags": ["technology", "platform", "pattern"],
  "content": "Minimal prose. Key facts. Specific enough to be useful without context."
}
```

**Good tags:** technology (`wordpress`, `fastapi`), pattern type (`ajax`, `pagination`, `auth`), platform/site name, language.

**Good content:** What the endpoint/pattern looks like. What params work. What's blocked server-side vs. browser. Error conditions. Time-saving shortcuts.

**Avoid:** Long explanations, strategy, step-by-step instructions — those belong in the session, not in notes.
