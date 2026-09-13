---
name: project-notes
description: |-
  Capture and update project information across the teams you run: status, decisions,
  risks, and a dated status log per project. One file per project in workspace/Projects/,
  cross-referenced by team. Use when asked to log project status, record a decision,
  note a risk, or check where a project stands.
license: MIT
---

# Project Notes

Maintain one file per project in `workspace/Projects/<Project Name>.md`, readable across every team that touches it.

## When to use

- "Update project status for [project]", "log a decision on [project]", "note a risk on [project]"
- "What's the status of [project]", "show me [project]"
- As part of `daily-prep` or `daily-notes` when today's note references a project

## File format

```markdown
---
type: project
teams:
  - Team A
status: on-track | at-risk | blocked | done
dri:
---

# Overview

What this project is, in 2-3 sentences.

# Status Log

## YYYY-MM-DD

- What changed, what shipped, what's blocked. Newest entry on top.

# Decisions

- YYYY-MM-DD: DECISION text

# Risks

- Risk description

# Links

- [Label](url or relative path)
```

## Process

### Step 1: Find or create the project file

Match by name (case-insensitive) against files in `workspace/Projects/`. If it doesn't exist and the user is logging real content (not just asking), create it with the skeleton above — ask for `teams` and a one-line overview if not already clear from context.

### Step 2: Apply the update

- **Status update / log entry** → prepend a new `## YYYY-MM-DD` entry under Status Log (newest first). Update the `status:` frontmatter field if it changed.
- **Decision** → add a line under Decisions (`YYYY-MM-DD: ...`) AND include it in that day's Status Log entry, so it's visible both places.
- **Risk** → add or update a line under Risks. If a previously-listed risk is resolved, move it out of Risks and note the resolution in the Status Log rather than silently deleting it.
- **Multi-team projects** → keep the `teams:` frontmatter list current; when reporting status, note which team owns which piece if that's known.

### Step 3: Cross-reference

- If today's daily note (`workspace/Dailies/YYYY-MM-DD.md`) has a "Project Notes" section mentioning this project, keep both in sync: the daily captures the day's raw notes, the project file captures the durable status/decision/risk record. Don't duplicate full paragraphs — the daily can just link to the project file.
- Use plain markdown links, e.g. `[API Redesign](../Projects/API%20Redesign.md)`. Never `[[wikilinks]]`.

### Step 4: Report

When asked for status rather than giving an update, read the file back and summarize: current `status`, most recent Status Log entry, open Risks, and any Decisions from the last 2 weeks.

## Rules

- **One file per project**, named to match exactly how it's referenced elsewhere (daily frontmatter `projects:`, roster references).
- **Status Log is append-only, newest first.** Don't rewrite history.
- **Never delete a risk or decision** — resolve/supersede it with a new dated note instead.
- **Idempotent per day** — a second update on the same date updates that day's Status Log entry rather than creating a duplicate `## YYYY-MM-DD` heading.
