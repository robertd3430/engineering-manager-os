# Engineering Manager OS

A chief-of-staff project for running multiple engineering teams: daily notes, todo tracking, multi-team project notes, app release tracking, and notes on direct reports and other team members. Works with **Claude Code and GitHub Copilot CLI** (and anything else that speaks the open Agent Skills format).

Inspired by [philpalmieri/engineering-manager-skills](https://github.com/philpalmieri/engineering-manager-skills), but with **no Obsidian dependency** and **no GitHub enrichment** (everything is manual/note-based for now — both could be added later as additional skills without changing anything below).

## Layout

```
skills/                  Canonical skill source — see below
.claude/skills   -> ../skills   (Claude Code discovers skills here)
.github/skills   -> ../skills   (GitHub Copilot CLI discovers skills here)
.agents/skills   -> ../skills   (generic Agent Skills location)
workspace/
  config/roster.json     Teams, direct reports, and collaborators (peers/manager/skip-levels)
  templates/daily-note.md
  Dailies/YYYY-MM-DD.md
  Projects/<Project Name>.md
  People/<Name>.md
  Releases/<App Name>.md
  Archive/Dailies/       Old dailies moved out of Dailies/
```

Skills live once, in `skills/`, and are symlinked into every location an agent tool might look for them — edit the files under `skills/`, never inside the `.claude`/`.github`/`.agents` symlinked copies (they're the same files on disk, but `skills/` is the name to reach for).

`workspace/` is the only place notes live — skills are instructions, not data. Everything in `workspace/` is plain markdown/JSON; no app or plugin required to read or edit it by hand.

## Multi-tool compatibility

Each `SKILL.md` uses the shared Agent Skills frontmatter: `name` (lowercase-hyphenated, matches its folder), `description` (what it does + when to use it), `license`, and — on skills that run shell commands (`daily-notes`, `todo-tracking`) — `allowed-tools: [shell]` so Copilot CLI can pre-approve those steps instead of prompting per command. Nothing in a skill's body assumes which tool is running it.

## Conventions

- **Links:** plain relative markdown only, e.g. `[API Redesign](../Projects/API%20Redesign.md)`. Never `[[wikilinks]]`.
- **No live queries.** Anything that looks like an aggregated view (e.g. "Today's Priority Focus") is a static section a skill overwrites when it runs — not a Dataview-style live query.
- **Checkboxes:** `- [ ]` open, `- [x]` done, `- [>]` forwarded to a later daily (never deleted, so history stays intact).
- **Tags:** `#today` marks an active priority; `➡️` before `#today` counts how many days it's rolled over without being finished; `#followup` / `#figureout` flag items for the todo-tracking scan.
- **Frontmatter:** dailies and projects carry `type`, `date`/`teams`/`projects` metadata — plain YAML, just for humans and for skills to grep reliably.
- **Filenames:** `Dailies/YYYY-MM-DD.md`, `Projects/<Project Name>.md`, `People/<Name>.md`, `Releases/<App Name>.md` — names must match exactly across frontmatter references and `roster.json`.

## Skills

| Skill | Does |
|-------|------|
| `setup` | Create/update `workspace/config/roster.json` — teams, direct reports, collaborators |
| `daily-notes` | Create or open today's daily note from the template |
| `todo-tracking` | Roll open tasks forward, tag today's priorities, write Today's Priority Focus |
| `project-notes` | Status/decisions/risks per project, cross-team aware |
| `release-tracking` | Version/date/status per app, plus readiness checks against a project's open risks |
| `team-notes` | 1:1 prep + running notes per person — coaching tone for direct reports, partnership tone for everyone else |
| `daily-prep` | Orchestrates all of the above into one "prep my day" flow |

## Config — `workspace/config/roster.json`

Multi-team roster: a `teams` array, each with `id`, `name`, `members` (direct reports, `relationship: "direct"`) and `collaborators` (`relationship: "peer" | "manager" | "skip-level"`). Also carries `fiscal_year` and the org's PI/sprint calendar (`PI Quarters`, `Sprints`) that other skills can reference when tagging projects/releases. See the `setup` skill for the full schema and how to change it conversationally — don't hand-edit unless it's a quick fix.

## Typical flow

```
"prep my day"                        → daily-prep runs the full sequence
"prep 1:1 with Sam"                  → team-notes, standalone
"update project status for API Redesign"
"log a release for Checkout App: v2.4.0 planned"
"release status"                     → summary across all apps
"add Jane to Team A as a direct report" → setup
```
