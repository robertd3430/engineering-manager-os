---
name: release-tracking
description: |-
  Track app/service release information: versions, dates, status, and highlights,
  one file per app in workspace/Releases/. Summarizes release readiness by pulling
  in open risks from the linked project. Use when asked to log a release, check
  release status (for one app or across all of them), or assess release readiness.
license: MIT
---

# Release Tracking

Maintain a release history per app in `workspace/Releases/<App Name>.md`, and answer readiness/status questions from it plus the linked project file.

## When to use

- "Log a release for [app]", "we shipped [app] v2.3.0"
- "Release status of [app]", "release status" (no app named → summarize across all apps)
- "Release readiness for [app]", "is [app] ready to ship"

## File format

```markdown
---
type: release-log
app: App Name
project:
---

# Releases

| Version | Date | Status | Highlights | Links |
|---------|------|--------|-------------|-------|
| 2.3.0 | 2026-09-13 | Released | Search caching fix | |
| 2.4.0 | | Planned | Filter rework | |

# Notes

-
```

`status` is one of: `Planned`, `In Progress`, `Released`, `Rolled Back`.

## Process

### Step 1: Find or create the app's release file

Match by app name (case-insensitive) against `workspace/Releases/`. Create it with the skeleton above if it doesn't exist — ask which project (if any) it's linked to, via a plain markdown link in the `project:` frontmatter field, e.g. `[API Redesign](../Projects/API%20Redesign.md)`.

### Step 2: Log a release

Add or update a row in the table:
- New release → append a new row, newest at the bottom is fine since the table is small and chronological; if the table is long, insert in date order.
- Updating an in-flight release (e.g. `Planned` → `Released` once it ships) → update that row in place rather than adding a duplicate.
- Capture `Highlights` in a few words — link out to a project's Status Log for the full story rather than duplicating detail here.

### Step 3: Report release status

**Single app:** read its file, report the latest `Released` version and date, plus anything `Planned`/`In Progress` and its target if known.

**All apps ("release status" with no app named):** scan every file in `workspace/Releases/`, and summarize as:

```markdown
## Release Status — YYYY-MM-DD

**Released recently**
- App: vX.Y.Z (YYYY-MM-DD) — highlight

**In progress / planned**
- App: vX.Y.Z — status, highlight
```

### Step 4: Release readiness

When asked about readiness for an app:

1. Read the app's release file for the relevant `Planned`/`In Progress` row.
2. Follow the `project:` link (if set) and pull open Risks and recent Status Log entries from that project file.
3. Summarize: what's shipping, what risks are still open against it, and whether anything in the project's Risks section suggests holding the release.
4. Don't make the ship/no-ship call — surface the signal and let the user decide.

## Rules

- **One file per app**, named to match how the user refers to it.
- **Update rows in place** for a release's status changes; don't create a new row per status change for the same version.
- **Link to the project file** for detailed context rather than duplicating status-log content into the release notes.
- **Plain markdown links only.**
