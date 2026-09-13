---
name: daily-notes
description: |-
  Create or open today's daily note in workspace/Dailies/ from the standard template.
  Handles frontmatter (date, teams, projects), is idempotent if today's note already
  exists, and archives old dailies on request. Use when starting the day, asked to
  'open today's note', or as the first step of daily-prep.
license: MIT
allowed-tools:
  - shell
---

# Daily Notes

Create today's daily note from `workspace/templates/daily-note.md`, or open it if it already exists.

## When to use

- "Start my day", "open today's note", "create today's daily"
- As step 1 of the `daily-prep` orchestration skill

## Prerequisites

- `workspace/templates/daily-note.md` exists (the standard skeleton: Priorities, Meetings, Project Notes, Waiting On, Risks, Ideas, End of Day)

## Process

### Step 1: Check if today's note exists

Look for `workspace/Dailies/YYYY-MM-DD.md` for today's date. If it exists, open it and stop here — **never overwrite an existing daily note**.

### Step 2: Create from template

If it doesn't exist:

1. Copy `workspace/templates/daily-note.md`.
2. Fill in frontmatter:
   - `date:` today's date, `YYYY-MM-DD`
   - `teams:` ask which team(s) are relevant today if not obvious from context (recent dailies, active projects); leave blank if the user has no preference
   - `projects:` ask or infer from the most recently active project files
3. Leave section bodies as placeholders (`-`) — `todo-tracking` fills in rolled-over items and priorities in a later step.
4. Write the file to `workspace/Dailies/YYYY-MM-DD.md`.

### Step 3: Confirm

Report what was created and prompt for next steps (e.g. run `todo-tracking` to roll items forward, or `daily-prep` for the full flow).

## Archiving old dailies

On request ("archive last month's dailies", "clean up old notes"):

1. Move `workspace/Dailies/YYYY-MM-DD.md` files older than the requested cutoff into `workspace/Archive/Dailies/`.
2. Never archive today's note.
3. Confirm the count moved before deleting anything from `Dailies/`.

## Rules

- **Idempotent.** Re-running this on a day whose note already exists is a no-op (open, don't recreate).
- **Never overwrite** an existing daily note's content.
- **Plain markdown links only** — no `[[wikilinks]]` — when referencing other files from a daily note, e.g. `[API Redesign](../Projects/API%20Redesign.md)`.
- Frontmatter `teams`/`projects` should reference names exactly as they appear in `workspace/config/roster.json` and `workspace/Projects/*.md`, so other skills can cross-reference reliably.
