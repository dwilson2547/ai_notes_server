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

> If `notes` is not installed, follow [INSTALL.md](./INSTALL.md). If the CLI is unusable, see [FALLBACK.md](./FALLBACK.md) for curl commands.

---

## When to Use

**Before starting any task** — search for relevant notes first. Do not skip this step.

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
