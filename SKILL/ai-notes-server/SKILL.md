---
name: ai-notes-server
description: >
  Use this skill whenever the AI Notes Server is running (http://localhost:8000).
  Instructs agents to persist and retrieve knowledge notes across sessions.
  Trigger phrases: check notes, save findings, persist knowledge, notes server.
---

# AI Notes Server

A shared knowledge layer. Notes persist across sessions so agents can build on prior work instead of re-discovering the same patterns.

---

## When to Use

**Start of every session** — search for notes relevant to the current task before doing any work.

**End of every session** (or after a significant finding) — save key learnings as notes.

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
