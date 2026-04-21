# Beam Changelog (collab_progress index)


## Purpose
- This file is the single, ordered index of all progress notes under collab_progress.
- It makes recent changes easy to find and links each entry to the detailed Markdown note(s).

## Rules
- Always append a new entry at the TOP (after this Rules section) after every task/change (feature, fix, refactor, tests, docs).
- Include **date** and **time in IST** (India Standard Time, UTC+05:30). Example date: `2026-04-08`; example time: `21:18 IST`. You may use full ISO 8601 with offset instead, e.g. `2026-04-08T21:18:00+05:30`.
  - Shell (Linux/macOS): `TZ=Asia/Kolkata date '+%Y-%m-%d %H:%M %Z'`

- Keep the Summary concise; link to detailed files for depth.
- Reference the branch/PR when applicable.
- Do not remove or rewrite past entries; corrections should be a new entry.

Entry Template
- Copy/paste and fill all fields. Keep newest entries at the top.

```
## [Title]: Short description of the change
- Date: YYYY-MM-DD
- Time (IST): HH:MM IST
- Branch/PR: <branch-name> (<PR link> if available)
- Files Changed (high level): <modules or folders>
- Details: See <relative-links-to-detailed-notes.md>
- Verification: <tests run, tools, results>
- Notes: <optional follow-ups/known issues>
```

