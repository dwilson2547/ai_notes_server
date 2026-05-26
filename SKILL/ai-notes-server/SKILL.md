---
name: ai-notes-server
description: >
  The AI Notes Server (http://localhost:8000) is the persistent knowledge layer for this workspace.
  Before starting any task, assess whether additional context is needed to complete it confidently.
  If yes, search notes. Always save findings after non-trivial resolutions.
  Trigger phrases: check notes, save findings, persist knowledge, notes server.
applyTo: "**"
---

# AI Notes Server

A shared knowledge layer. Notes persist across sessions so agents can build on prior work instead of re-discovering the same patterns.

> If `notes` is not installed, follow [INSTALL.md](./INSTALL.md). If the CLI is unusable, see [FALLBACK.md](./FALLBACK.md) for curl commands.
> In Copilot bash, `source ~/.bashrc` first.

---

## When to Use

**Before starting a task** — assess whether additional context is needed to complete it confidently.
If the task involves infrastructure, deployment, scraping patterns, known gotchas, or anything
with project-specific history, search notes. Skip the lookup for straightforward tasks where
the codebase alone is sufficient.

**After resolving a non-obvious problem** — save findings before moving on.

**After significant architectural or configuration decisions** — capture what was decided and why.

**When discovering environment-specific behavior** — version quirks, toolchain gotchas, platform workarounds.

**Do not write a note for** — things easily found in official docs, routine tasks, or anything already noted.

---

## Usage

```bash
# Search — always start here
notes search "cloudflare bypass playwright"
notes search "pagination pattern wordpress" --limit 10

# Create
notes add "Title" "2-5 sentences. Key facts. What worked. Specific details." --tags technology,pattern

# Update — prefer over creating near-duplicates
notes search "topic"                          # find the ID
notes update 42 --content "Updated findings."
notes update 42 --tags newtag,anothertag      # replaces existing tags

# Other
notes get 42
notes archive 42
```

---

## Note Quality

- **Content:** 2-5 sentences. Facts and gotchas, not strategy.
- **Tags:** technology, platform, site name, pattern type.
- Prefer updating existing notes over creating near-duplicates.

---

## Escalate to Playbooks

When saving a note, ask: *does this content have headers or span multiple steps?*

**If yes — ingest to playbooks first, then add a pointer note:**

```bash
# Write the structured content to a temp file, then ingest
playbooks ingest /tmp/strategy.md --slug "scope/topic" --description "..." --tags "tag1,tag2"

# Then add or update the note with a slug pointer
notes add "Topic Strategy" "Full procedure documented. See playbooks: scope/topic" --tags tag1,tag2
# or, if a note already exists:
notes update 42 --content "Existing content. Full procedure at playbooks: scope/topic"
```

**Playbooks is the living source of truth for structured documents.** If a plan or strategy was updated this session, the playbook must be updated too — don't let notes point to stale content.

Use `playbooks update <slug> --file <file>` to keep playbooks current whenever the underlying document changes.
