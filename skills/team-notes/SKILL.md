---
name: team-notes
description: |-
  Prepare 1:1 talking points and maintain a running notes file for anyone on the
  roster — direct reports, peers, your manager, or skip-levels. Tone adapts to the
  relationship: coaching for direct reports, partnership for everyone else. Use
  when prepping a 1:1, asked to log notes about a team member, or as part of daily-prep.
license: MIT
---

# Team Notes

Prepare talking points for a 1:1 and write them to `workspace/People/<name>.md`, adapting tone to the relationship in `workspace/config/roster.json`.

## When to use

- "Prep 1:1 with [name]", "1:1 talking points for [name]", "notes on [name]"
- As part of `daily-prep`, for each 1:1 on today's schedule

## Prerequisites

- The person is listed in `workspace/config/roster.json`, under some team's `members` (direct reports) or `collaborators` (peer/manager/skip-level)
- Run `setup` first if they aren't there yet

## Process

### Step 1: Identify the person and relationship

Match the requested name against `roster.json` (`name` or `vault_name`), across all teams. If ambiguous or not found, ask — or offer to run `setup` to add them.

Relationship determines tone:
- `direct` → **coaching-oriented**
- `peer` / `manager` / `skip-level` → **partnership-oriented** (never coaching language; `manager` also gets upward/escalation topics)

### Step 2: Gather context

From `workspace/People/<vault_name>.md` (create if missing, with just a `# Notes` heading):
- Open items and recent themes from prior dated entries
- Anything tagged `#followup` or `#feedback`
- Stale items (open more than ~2 weeks)

From the last 5-7 daily notes in `workspace/Dailies/`:
- Mentions of this person
- Shared project context, decisions, or commitments involving them

From `workspace/Projects/`:
- Active projects this person (or their team) is tied to, for status check-in questions

### Step 3: Build talking points

**For direct reports (coaching-oriented):**
- **Recognition** — lead with something specific and positive if there's signal; name the actual project/decision, not generic praise
- **Open follow-ups** — reference existing open items, note when first raised; don't recreate as checkboxes
- **Project status** — frame as check-in questions ("how's X going?"), not status demands
- **Coaching opportunities** — frame as a question, not an instruction

**For peers/managers/skip-levels (partnership-oriented):**
- **Open follow-ups** — commitments either side made; "did this happen?" framing
- **Shared project alignment** — active shared work, decisions needed, possible misalignment
- **Partnership rhythms** — cadence, process, review workflow questions
- **Strategic topics** — roadmap, cross-team dynamics
- **Escalation/upward** (manager relationship only) — blockers, resource asks, guidance needed

### Step 4: Write to the People file

Prepend a new dated entry above the most recent one:

```markdown
#### 2026-09-13 1:1

**Topic Name** (Context Type)
- Brief context, plain bullets only
- Check in: question about the topic
```

Context types: `(Recognition)`, `(Follow-up)`, `(Project Status)`, `(Coaching)`, `(Feedback)` for directs; `(Follow-up)`, `(Alignment)`, `(Partnership)`, `(Strategic)`, `(Escalation)` for others.

Link to project files with plain markdown: `[API Redesign](../Projects/API%20Redesign.md)`. Never `[[wikilinks]]`.

### Step 5: Present before writing

Unless the user said "just do it", present a brief summary first:

> For [Name]: N open follow-ups from [dates], recent work on [project]. Want me to write this up?

## Rules

- **Never create `- [ ]` checkboxes** in prep notes — plain bullets only, so open tasks aren't duplicated between the People file and daily notes.
- **Bold text for topic headings**, not markdown headers (headers are reserved for file/date structure).
- **Idempotent per day** — if today's entry already exists, update it instead of duplicating.
- **Tone discipline**: never use coaching language for a non-direct relationship, and never skip recognition for a direct report when there's real signal to draw on.
