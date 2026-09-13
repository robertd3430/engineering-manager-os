---
name: setup
description: |-
  Interactive setup for Engineering Manager OS. Creates or updates workspace/config/roster.json
  with teams, direct reports, and collaborators. Run this first, and any time a team's
  roster changes (new hire, reorg, new peer relationship). Use when asked to 'set up',
  'configure', 'add a team member', or 'update the roster'.
license: MIT
---

# Setup

Walk the user through creating or updating `workspace/config/roster.json`, the single config file every other skill reads to know who's on which team and how to talk about them.

## When to trigger

- "Set up", "configure", "initialize the roster"
- "Add [name] to Team X", "[name] just joined", "add a peer/collaborator"
- Any skill (e.g. `team-notes`, `daily-prep`) can't find a person or team it needs — offer to run setup rather than guessing.

## Process

### Step 1: Locate roster.json

Read `workspace/config/roster.json`. If it doesn't exist, create it with an empty `teams` array and standard calendar-year `fiscal_year` (see schema below).

### Step 2: Teams

Ask which team this change applies to, or whether to add a new team. Each team needs:

| Field | Required | Description |
|-------|----------|-------------|
| `id` | Yes | short slug, e.g. `team-a` |
| `name` | Yes | display name, e.g. "Team A" |
| `members` | Yes (can be empty) | direct reports on this team |
| `collaborators` | Yes (can be empty) | peers, manager, skip-levels relevant to this team |

### Step 3: Members (direct reports)

For each direct report, collect:

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | display name |
| `relationship` | Auto | always `"direct"` |
| `vault_name` | No | filename in `workspace/People/` (defaults to `name`) |
| `level` | No | job level/title |
| `pronouns` | No | e.g. "she/her" |
| `joined` | No | `YYYY-MM` |

### Step 4: Collaborators (peers, manager, skip-levels)

For each collaborator, collect:

| Field | Required | Description |
|-------|----------|-------------|
| `name` | Yes | display name |
| `relationship` | Yes | one of `"peer"`, `"manager"`, `"skip-level"` |
| `vault_name` | No | filename in `workspace/People/` |
| `role` | No | their role, for context |

### Step 5: Fiscal year (optional)

Ask if the org uses a non-standard fiscal year. Default to calendar quarters if not.

### Step 6: Write and confirm

1. Assemble the JSON, preserving any teams/people not being edited this run.
2. Show a preview.
3. Write `workspace/config/roster.json`.
4. If a `vault_name` was set (or defaulted) for a new person, offer to create their `workspace/People/<vault_name>.md` file with just an `# Notes` heading.

### Post-setup

```
✅ roster.json updated.

Try:
  "prep my day"                    — full daily orchestration
  "prep 1:1 with {name}"           — talking points for a 1:1
  "project status for {project}"   — update or view a project
  "log a release for {app}"        — track a release
```

## Validation

- Every member has `name` and `relationship: "direct"`.
- Every collaborator has `name` and a `relationship` in `{peer, manager, skip-level}`.
- No duplicate `name` (or `vault_name`) within a team.
- `fiscal_year`, if present, covers all 12 months across 4 quarters.

## Schema reference

```json
{
  "teams": [
    {
      "id": "team-a",
      "name": "Team A",
      "members": [
        { "name": "Jane Doe", "relationship": "direct", "vault_name": "Jane Doe", "level": "Senior", "pronouns": "she/her", "joined": "2024-01" }
      ],
      "collaborators": [
        { "name": "Sam PM", "relationship": "peer", "vault_name": "Sam PM", "role": "Product Manager" }
      ]
    }
  ],
  "fiscal_year": { "q1": ["Jan","Feb","Mar"], "q2": ["Apr","May","Jun"], "q3": ["Jul","Aug","Sep"], "q4": ["Oct","Nov","Dec"] },
  "PI Quarters": ["PI1", "PI2", "PI3", "PI4"],
  "Sprints": ["PI1S1", "PI1S2", "PI1S3", "..."]
}
```

`PI Quarters` and `Sprints` are the org's Program Increment/sprint calendar — a flat list of valid PI and sprint labels other skills can validate against or offer as options when a project/release entry needs a PI or sprint tag. Edit these arrays directly when the calendar changes; they don't need the interactive wizard.

Additional fields are ignored by other skills, so it's safe to extend this file.
