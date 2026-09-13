---
name: daily-prep
description: |-
  Full daily prep orchestration: ensure today's note exists, roll todos forward,
  prep any 1:1s on today's schedule, and set today's priorities. Chains daily-notes,
  todo-tracking, and team-notes in sequence. Use when asked to 'prep my day', 'prep
  today', or 'set up today'.
license: MIT
---

# Daily Prep

Chain the atomic skills together into one daily-prep flow: today's note, task rollover, 1:1 prep, and priorities.

## When to use

- "Prep my day", "prep today", "set up today"

## Prerequisites

- `workspace/config/roster.json` has the people you might 1:1 with today (run `setup` if not)

## Process

### Phase 1: Today's note

Invoke `daily-notes`. If today's note already exists, note that and continue — don't overwrite it.

### Phase 2: Read the schedule

1. Read today's daily note's "Meetings" / "Schedule" section.
2. For each 1:1 found, match the person against `roster.json` to get their relationship (`direct`, `peer`, `manager`, `skip-level`).
3. Not found on the roster → ask the user, or offer to run `setup`.
4. Present the plan before starting:
   > I see N 1:1s today: [Name] (direct), [Name] (peer). I'll prep each one. Ready?

Skip non-1:1 items (standups, trainings, interviews). Skip people already prepped today (today's dated entry already exists in their People file) unless the user asks to refresh.

### Phase 3: Task rollover and priorities

Invoke `todo-tracking`:
1. Roll open tasks forward from prior dailies into today's note.
2. Report any escalation-level items (2+ arrows).
3. Scan for and propose today's priorities; tag confirmed items `#today`.
4. Write the "Today's Priority Focus" section.

### Phase 4: 1:1 prep (sequential, with stops)

Work through each 1:1 **one at a time**, in schedule order. For each:
1. Invoke `team-notes` for that person.
2. Present the summary and ask before moving to the next:
   > Done with [Name]. Anything to add before I move to [Next]?

### Phase 5: Summary

> Day prepped:
> - Today's note: created / already existed
> - Rolled N tasks forward (M flagged for escalation)
> - Prepped 1:1s: [Name] (N topics), [Name] (N topics)
> - N items tagged #today
>
> Anything else before you start?

## Fast path

If the user says "just do it all", "no stops", or "fast mode": skip per-person confirmations, run every phase straight through, and give one summary at the end.

## Interaction model

Default is sequential with stops between each 1:1, so the user can add context as you go. They can switch to fast path at any point.
