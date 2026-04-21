# Beam — Technical Specification

**Version:** 0.1.0-draft
**Status:** Authoritative
**Crate:** none — this project is a Markdown/JSON skills plugin

> Beam is a [superpowers][sp]-style skills plugin that bootstraps a new
> repository for agent-driven development (ADD). This document is the
> single source of truth for what Beam is, what it deliberately is not,
> how the skills cooperate, and how it ships.

[sp]: https://github.com/obra/superpowers

## 1. Problem & Goal

Initializing a repository for ADD is repetitive and error-prone when done
by hand. Engineers must:

- write `AGENTS.md` from scratch with the right structure,
- decide on a collaboration-tracking protocol and create its files,
- create a place for the technical spec to live before any code,
- structure the project skeleton in a way the agent can navigate, and
- ensure every artifact is **agent-legible** — short, linked, and
  version-controlled.

The OpenAI harness-engineering note (Feb 2026) made the underlying point
explicit: *agent throughput collapses when the environment is
underspecified.* The discipline shows up in the scaffolding, not the
code.

**Beam's goal is to eliminate the bootstrapping cost using only the tools
the user already has** — their coding agent, its built-in `web_search`,
its todo tool, and its ability to read SKILL.md files. No CLI, no
binary, no separate runtime.

## 2. What Beam Is Not

| Non-goal | Why |
| -------- | --- |
| A CLI or compiled binary | Adds nothing the agent cannot already do; locks users to a single runtime |
| A code generator | Beam scaffolds the harness; the user's agent writes the application code afterwards |
| A CI/CD or deployment tool | Out of scope; Beam never runs build, test, or deploy commands |
| A package manager | Beam writes documentation files only; it does not run `cargo`, `npm`, etc. |
| A pi extension | Pi is one of the supported agents, not the only one. Beam ships as portable skills so it works in Claude Code, Codex CLI, and Gemini CLI as well |
| A cross-session state tracker | No `.beam/state.json`, no drift detection, no manifest. Each invocation is fresh |

## 3. The Four Phases

Beam orchestrates four cooperating phase-skills. Each is independently
invocable; the master skill (§4) chains them in order for a fresh-repo
bootstrap.

### Phase 1 — Goal elicitation

- **Skill:** `eliciting-project-goals`
- **Trigger:** "Use when starting a new project for ADD and the agent
  has no project context yet."
- **Tools used:** the agent's todo tool (`TodoWrite` in Claude Code; the
  equivalent in pi/Codex), `Write`.
- **Output:** `plan.md` at the repo root containing project name,
  one-paragraph description, primary audience, language/tech stack,
  architectural constraints, testing/CI requirements, and any
  compliance notes. A session todo list is created with one entry per
  remaining phase.
- **Discipline:** the skill ends with a hard-gate forbidding any file
  scaffolding until `plan.md` is written and the user has approved it.

### Phase 2 — Repo scaffolding

- **Skill:** `scaffolding-repo`
- **Trigger:** "Use when `plan.md` exists and either `collab_progress/`
  or `README.md` is missing."
- **Tools used:** `Bash` (mkdir), `Read` (templates), `Write`.
- **Output:**
  - `collab_progress/PROTOCOL.md`, `CHANGELOG.md`, `README.md`
    (rendered from
    `skills/scaffolding-repo/collab_progress_config.json`). On a resume
    where `CHANGELOG.md` is already populated, pre-existing non-empty
    files are left intact and only missing files are written.
  - A minimal starter directory tree reasoned from `plan.md`. The skill
    consults `plan.md` for language and tech stack, then creates the
    directories the user will need to start writing code (typically
    `src/`, `tests/`, `docs/`).
  - The project `README.md` at the repository root, rendered from
    `skills/scaffolding-repo/readme-template.json`.
  - Language-specific package manifests reasoned from `plan.md` (for
    example `package.json`, `requirements.txt`, `Cargo.toml`, `go.mod`)
    with the project name and minimal configuration — enough to
    `install` / `build` a no-op. No application logic is written.

### Phase 3 — `AGENTS.md`

- **Skill:** `writing-agents-md`
- **Trigger:** "Use when `plan.md` and `collab_progress/` exist and
  `AGENTS.md` is missing or empty."
- **Tools used:** `Read` (template and `plan.md`), `Write`, the agent's
  conversational turn (to solicit user preferences).
- **Output:**
  - `AGENTS.md` at the repository root, rendered from
    `skills/writing-agents-md/agents_config.json`. `requirements`-mode
    sections are synthesised from `plan.md` plus repository context;
    `exact`-mode sections are copied verbatim. The
    Documentation & Knowledge Sources section may be augmented with
    project-specific non-library references (domain specs, methodology
    notes) after the verbatim block.
  - A recorded decision from the user on any additional preferences or
    special instructions they want captured, integrated into the file
    before Phase 3 is declared complete.
- Phase 3 does **not** write `README.md`; that is Phase 2's responsibility.

### Phase 4 — Technical spec

- **Skill:** `writing-technical-spec`
- **Trigger:** "Use when `AGENTS.md` exists and `docs/SPEC.md` is
  missing or empty, or when the user explicitly asks for a fresh spec."
- **Tools used:** `web_search` (mandatory — must not be skipped), `Read`
  for `plan.md`, `Write`.
- **Output:** `docs/SPEC.md` following the structure in
  `skills/writing-technical-spec/spec-template.json`. The skill instructs
  the agent to:
  1. Read `plan.md` to identify the core frameworks and constraints.
  2. Run `web_search` for each core framework to confirm current
     versions, idiomatic patterns, and recently changed APIs.
  3. Synthesize findings into a focused spec — *map, not encyclopedia*
     — targeting the 600–1200-word range declared in
     `spec-template.json`'s `document_info.target_word_count`.

## 4. Skill Structure & Orchestration

Beam follows the obra superpowers pattern: **one master skill, several
phase-skills, each with a tight trigger.**

### `using-beam` (master)

- **Trigger:** narrowly scoped to new-project intent. Working draft:
  *"Use when the user asks to initialize a new repository for
  agent-driven development, bootstrap a project harness, or set up a
  fresh repo for collaborative work with coding agents."*
- **Behavior:**
  1. Detect or confirm the current working directory is empty (or
     contains only a `.git/` directory).
  2. Read `plan.md` if it already exists; otherwise invoke phase 1.
  3. Walk the four phases in order. At each transition, check
     completion criteria from the previous phase's output.
  4. Stop after phase 4 with a short report listing every file
     created.
- **Why narrow trigger:** unlike obra's `using-superpowers`, which
  fires on every conversation, `using-beam` must stay dormant during
  normal coding sessions. A wide trigger would force the agent to
  consult Beam on every prompt, polluting context and slowing the
  user down.
- **Open question (OQ-1):** the exact wording of the `using-beam`
  description. The current draft prioritizes recall over precision;
  empirical baseline runs (see §6) will tell us whether it
  over-triggers.

### Phase-skill independence

Each phase-skill must work standalone — invoking only
`writing-technical-spec` on an existing project should work. This means:

- Each phase-skill's preamble lists its own preconditions
  (e.g. "this skill assumes `plan.md` exists; if not, instruct the user
  to run phase 1 first").
- No phase-skill silently invokes another. The master skill is the only
  orchestrator.

## 5. Distribution Model

Beam is a git-hosted directory of skills. There is nothing to compile
and nothing to publish to a binary registry.

| Audience | Install |
| -------- | ------- |
| Claude Code | `git clone <repo> ~/.claude/skills/beam` |
| Codex CLI / pi | `git clone <repo> ~/.agents/skills/beam`, or symlink |
| Pi (canonical, future) | `pi install git:github.com/<you>/beam` |
| Gemini CLI | clone into the path Gemini scans for skills (varies) |

Updates: `git pull` in the install location. Versioning follows the repo
tags; agents pick up changes on the next session start.

## 6. Template Assets

Beam does **not** use a top-level `assets/` directory. Each JSON
template lives alongside the skill that consumes it:

- **`skills/scaffolding-repo/readme-template.json`** — section list and
  content requirements for the project `README.md` that phase 2 writes.
- **`skills/scaffolding-repo/collab_progress_config.json`** — directory
  structure and verbatim file contents for `collab_progress/PROTOCOL.md`,
  `collab_progress/CHANGELOG.md`, and `collab_progress/README.md`.
- **`skills/writing-agents-md/agents_config.json`** — section list and
  rendering modes (`exact` vs. `requirements`) for the generated
  `AGENTS.md`. `exact`-mode sections are copied verbatim;
  `requirements`-mode sections are filled from `plan.md` plus the
  section's content requirements.
- **`skills/writing-technical-spec/spec-template.json`** — mandatory
  section skeleton and `target_word_count` for `docs/SPEC.md`.

These templates are consumed by the phase skills via `Read`, not by any
code. They are intentionally JSON (not Markdown) so the agent has
machine-readable structure to walk; the generated artifacts are
Markdown.

## 7. Testing Approach

Skills are tested with the TDD-for-skills cycle from
`superpowers:writing-skills`:

1. **RED** — run the target scenario in a fresh subagent without the
   changed skill loaded; record the agent's behavior verbatim.
2. **GREEN** — make the smallest skill edit that addresses the
   observed failures; re-run the same scenario with the change loaded.
3. **REFACTOR** — capture any new rationalizations as rows in the
   skill's rationalization table; re-run until the behavior is
   bulletproof.

There is no automated test runner. Verification artifacts (baseline
transcripts, post-change transcripts, rationalization tables) live in
`collab_progress/` alongside the change note.

## 8. Roadmap & Open Questions

| ID | Question / item | Notes |
| -- | --------------- | ----- |
| OQ-1 | Exact wording of `using-beam`'s `description` | Needs baseline runs to tune recall vs. precision |
| OQ-4 | Whether phase 3 should also create a starter `LICENSE` | Currently out of scope; user picks license manually |
| OQ-5 | Pi-package manifest (`package.json` with `pi` key) for `pi install` | Adds optional convenience without affecting other agents |
| OQ-6 | How to detect "this is a fresh repo" reliably across agents | Master skill currently delegates to the agent's filesystem tools |

**Resolved:**

- **OQ-2** (Phase 2 starter structure) — resolved: phase 2 reasons the
  directory tree from `plan.md` and also creates language-specific
  package manifests. See §3 Phase 2.
- **OQ-3** (spec template format) — resolved: the template is
  `skills/writing-technical-spec/spec-template.json`, a JSON skeleton the
  agent walks. See §6.

*End of Beam Technical Specification v0.1.0-draft*
