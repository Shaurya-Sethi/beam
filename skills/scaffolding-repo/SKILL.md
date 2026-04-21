---
name: scaffolding-repo
description: >-
  Use when `plan.md` exists at the repository root but either
  `collab_progress/` or `README.md` is missing, or when the user asks to
  scaffold a new repository for agent-driven development.
---

# Scaffolding the Repository (Phase 2)

This skill bridges the gap between the abstract plan and the tangible
codebase. Your objective is to lay down the initial repository skeleton
based purely on the decisions recorded in `plan.md`, establish the project's
public face (`README.md`), and bootstrap the collaboration tracker.

## Pre-flight Checks (The Inputs)

Before executing any bash commands or writing files, you MUST read:
1. `plan.md` at the repository root. (If it does not exist, hard-fail and
   instruct the user to run the `eliciting-project-goals` skill first).
2. `skills/scaffolding-repo/readme-template.json`
3. `skills/scaffolding-repo/collab_progress_config.json`

Do not proceed until you have fully ingested these three files.

## Execution Sequence

Complete the following steps strictly in order.

### 1. Scaffold the Minimal Starter Structure
Analyze the "Technical stack" and "Repo shape" sections of `plan.md`.
Determine the idiomatic file and folder structure for that stack.
- Create the required root folders (e.g., `src/`, `tests/`, `docs/`).
- Create the package management manifests (e.g., `package.json`,
  `requirements.txt`, `Cargo.toml`, `go.mod`) with the project name and
  basic configuration.
- Create a `.gitignore` tailored to the stack.
- Create empty or minimal entry point files (e.g., `src/main.rs`, `index.js`).

<HARD-GATE: NO FEATURE CODE>
You are scaffolding, not implementing. Do NOT write any application logic,
database schemas, or business features. Your job is to create the canvas,
not paint the picture.
</HARD-GATE>

### 2. Generate the Repository `README.md`
Parse `readme-template.json` and generate `README.md` at the repository root.
- You must output exactly the sections defined in the template array, in
  that exact order.
- Map data from `plan.md` into the template's requirements. For example, map
  "Project identity" to the H1, and "Core functionality" to the Usage section.
- Keep it concise. This is the project's public face, not a design spec.

### 3. Bootstrap `collab_progress/`
The collaboration progress tracker is how agents and humans communicate what
has been done. You must install it exactly as configured, but you must
NOT clobber user notes on a resume.

- Parse `collab_progress_config.json`.
- If `collab_progress/` does not exist, create it at the repository root.
- For each of the three files defined in the config (`CHANGELOG.md`,
  `PROTOCOL.md`, `README.md` inside `collab_progress/`):
  - If the file is **absent or empty**, write it using the EXACT literal
    string content provided in the `content` field of the JSON. Do not
    modify or truncate that content.
  - If the file is **present and non-empty**, leave it alone. A previous
    Phase 2 run (or a human edit) already populated it; overwriting would
    destroy user-authored notes or edits to `PROTOCOL.md`.
- If `collab_progress/CHANGELOG.md` is already populated, treat this
  invocation as a resume: do not re-initialise the tracker, just move on
  to Step 4 and prepend a new entry per the existing protocol.

### 4. Write the First Progress Note
Because you have just made a significant change to the repository, you must
comply with the `PROTOCOL.md` you just created.
- Create a detailed markdown note: `collab_progress/repo-scaffold-DD-MM-YYYY.md`
  documenting the directories and baseline files you just scaffolded.
- Prepend an entry to `collab_progress/CHANGELOG.md` (just below the Rules
  section) summarizing this work.
- You MUST use the user's detected local timezone in the CHANGELOG.md entry
  (e.g., `date '+%Y-%m-%d %H:%M %Z'`).

## Hard Rules

1. **Never invent requirements.** If `plan.md` is silent on a technical
   detail, pick the safest minimal default for the stack.
2. **Never skip the progress note.** Phase 2 is not complete until
   `CHANGELOG.md` has an entry for the scaffolding work.
3. **Respect the templates.** The schemas in `readme-template.json` and
   `collab_progress_config.json` are strict.

## Rationalization table

| Rationalization | Response |
|---|---|
| "I'll just add a quick 'hello world' API endpoint to show it works." | No. Scaffolding only. Setup the structure, leave the implementation to Phase 4+. |
| "The user didn't mention a linter, I'll set up Prettier and ESLint." | Only if it's considered standard boilerplate for the stack. Do not go overboard with tooling not requested in `plan.md`. |
| "I'll summarize the collab_progress rules instead of pasting the literal text." | No. The config file dictates the literal string content. Copy it exactly. |
| "I won't log a progress note because this is just setup." | Setting up the repo is a change. Follow the protocol. Log it. |

## Output Checklist

Before declaring Phase 2 complete, verify:
- [ ] The core directory structure and base manifests exist.
- [ ] `README.md` is created and follows the JSON template structure.
- [ ] `collab_progress/` exists with its 3 core files matching the config
      exactly (or pre-existing non-empty files were left intact on resume).
- [ ] `collab_progress/repo-scaffold-<date>.md` exists.
- [ ] `collab_progress/CHANGELOG.md` has a new entry at the top, formatted
      correctly with the user's local timezone.
- [ ] The session todo list is updated, prompting the user to start Phase 3
      (`writing-agents-md`).
