---
name: todo-tracking
description: |-
  Roll open tasks forward from previous daily notes into today's, tag today's real
  priorities with #today, and write a static Today's Priority Focus list. Handles
  the rollover arrow, source-date tracking, and forwarded-task marking. Use when
  prepping today's note, asked about todos, or 'what should I focus on today'.
license: MIT
allowed-tools:
  - shell
---

# Todo Tracking

Keep open tasks moving forward across daily notes without duplicating them, and surface today's real priorities.

## When to use

- "Roll tasks forward", "move yesterday's todos", "what's still open"
- "Today's priorities", "what should I focus on today"
- As step 2 of `daily-prep`, after today's note exists (`daily-notes`)

## Prerequisites

- Today's daily note already exists (run `daily-notes` first if not)
- Daily notes follow `workspace/Dailies/YYYY-MM-DD.md`

## Conventions

- `- [ ]` open task, `- [x]` completed (never touched by this skill), `- [>]` forwarded (moved to a later daily, kept as historical record)
- `#today` marks a task as an active priority; `➡️` is added before `#today` each time a `#today` task rolls forward without being finished — the arrow count shows how long it's been stuck
- Source-date links use plain markdown: `[2026-09-11](../Dailies/2026-09-11.md)`, always pointing at the ORIGINAL daily the task first appeared on, not an intermediate one

## Process

### Step 1: Scan for open tasks

```bash
grep -rl '^\- \[ \]' workspace/Dailies/
```

Exclude today's note. For each match, collect the open task lines.

### Step 2: Process each open task

For every `- [ ]` line found on a prior daily:

1. If it has `#today`: add one more `➡️` before `#today` (or the first one, if none yet).
2. If it doesn't have `#today`: leave as-is.
3. Add a source-date link if missing, in the format `[YYYY-MM-DD](../Dailies/YYYY-MM-DD.md)` using the date it was FIRST created. If it already has a source-date link (from an earlier rollover), keep that original date — don't overwrite it with the intermediate daily's date.

### Step 3: Write into today's note

Add two sections to today's daily note (before "Today's Priority Focus" if present):

```markdown
### 🔥 #today items (rolled forward)

- [ ] ➡️➡️ #today Task text [2026-09-11](../Dailies/2026-09-11.md)

### 📥 Moved Open Tasks

- [ ] Task text [2026-09-12](../Dailies/2026-09-12.md)
```

- `🔥` section: only `#today` items, sorted by arrow count descending. Omit the section entirely if there are none.
- `📥` section: everything else, sorted by source date, oldest first.

**Escalation thresholds** when reporting back to the user:
- 2+ arrows: mention it needs attention
- 3+ arrows: flag with ⚠️, suggest a decision
- 4+ arrows: flag with 🚨 — it needs to happen today or come off `#today`

### Step 4: Mark source tasks as forwarded

In each source daily note, change moved `- [ ]` lines to `- [>]`. This is a **move, not a copy** — the task must never appear as `- [ ]` in two places.

Never touch `- [x]` (completed) tasks.

### Step 5: Scan for today's priorities

Beyond what's already rolled over, scan for additional signal:

| Signal | Where | Meaning |
|--------|-------|---------|
| `#followup` | People/, Projects/, recent Dailies/ | needs a follow-up action |
| `#figureout` | People/, Projects/, recent Dailies/ | needs investigation/decision |
| due-date markers you find in project/people notes | Projects/, People/ | flag if due today or overdue |
| an item on the same topic appearing in the last 3 daily notes unresolved | Dailies/ (last 3) | likely stuck, worth surfacing |

Present findings grouped as **Must do** (deadlines, overdue, meeting prep) / **Should do** (follow-ups, active project work) / **If time allows**. Ask the user which ones to tag `#today` — don't tag anything without confirmation.

For confirmed items, add `#today` to the task **in its source file**, in place. Never move or duplicate it just to tag it.

### Step 6: Write Today's Priority Focus

Replace (don't append to) the "Today's Priority Focus" section — or add it near the end if it doesn't exist yet — with a plain, regenerated list of every open task in the workspace that currently has `#today`:

```markdown
## Today's Priority Focus

- [ ] ➡️➡️ #today Task text [2026-09-11](../Dailies/2026-09-11.md)
- [ ] #today Another #today item [2026-09-13](../Dailies/2026-09-13.md)
```

This is a snapshot, not a live query — re-run this skill to refresh it after tagging or completing items.

### Step 7: Verify and report

- Confirm zero `- [ ]` remain on any daily note before today.
- Report: "Moved N tasks from M daily notes. X items flagged for escalation. Y new items tagged #today."

## Rules

- **Move, never copy.** A task lives in exactly one place at a time (marked `[>]` at the source once moved).
- **One arrow per rollover**, only when a `#today` task actually moves forward a day.
- **Preserve the original source date**, not the most recent daily it sat on.
- **Never touch completed (`[x]`) tasks.**
- **Never process today's own note** as a rollover source.
- **Idempotent** — running this twice on the same day must not create duplicate sections, duplicate arrows, or re-move already-`[>]` tasks.
- **Never remove `#today` without the user confirming.** If stale `#today` items are found on a daily older than today (not yet rolled over), ask whether to keep or drop the tag before touching it.
