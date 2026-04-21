# Skills audit fixes — 21-04-2026

## Summary

Applied the 30-item implementation plan from the Beam skills architectural
review. Fixes close critical workflow bugs (Phase-1 impossible checklist
item, Phase-3 ignored-template drift, Phase-4 hardcoded IST timezone),
align the Phase-2 SKILL with `docs/SPEC.md` on package manifests, rewire
`writing-agents-md` to read its template JSON and solicit user
preferences, and bring the repository-level docs (`AGENTS.md`,
`README.md`, `docs/SPEC.md`) back in sync with the implemented skills.

No new skills, no new dependencies, no file renames. Markdown + JSON only.

## Files changed

- `skills/eliciting-project-goals/SKILL.md`
  - Replaced the impossible `collab_progress/` progress-note bullet in
    the Output Checklist with an explicit "do not write a progress note
    in this phase" note — Phase 1 predates `collab_progress/`.
  - Tightened the frontmatter `description` to triggers only (dropped
    the "run a structured Q&A…" workflow summary).
- `skills/scaffolding-repo/SKILL.md`
  - Frontmatter `description` rewritten to triggers only.
  - Step 3 now has a "skip if present, non-empty" branch per file in
    `collab_progress/` so resumes no longer clobber user notes.
  - `<HARD-GATE: NO FEATURE CODE>` tag formatting normalised
    (unbolded open, unbolded close; matches `eliciting-project-goals`).
  - Output Checklist rewrapped at 80 chars and IST reference replaced
    with "user's local timezone".
- `skills/writing-agents-md/SKILL.md`
  - Full rewrite. The skill now:
    - hard-fails on missing `plan.md`, `collab_progress/PROTOCOL.md`,
      `collab_progress/CHANGELOG.md`, `README.md`, or
      `agents_config.json`;
    - reads `plan.md` as the primary source for `requirements`-mode
      sections;
    - walks `template_structure` from
      `skills/writing-agents-md/agents_config.json` at runtime (the
      inlined nine-section block is deleted — JSON is the single source
      of structure);
    - permits appending additional Primary Sources to the
      Documentation & Knowledge Sources section if `plan.md` names a
      non-library reference that belongs there, while leaving the
      verbatim block intact;
    - ends with a new Step 4 that notifies the user, solicits any
      special instructions / preferences, and integrates them before
      declaring the phase complete;
    - carries a rationalization table (seven rows) covering
      paraphrase-of-exact, `plan.md`-skip, word-count padding, silent
      user, unauthorised section creation, re-inlining the template,
      and unauthorised rewrite of the verbatim Documentation block.
  - Frontmatter `description` rewritten to triggers only and drops the
    stale `assets/` path.
- `skills/writing-technical-spec/SKILL.md`
  - Frontmatter `description` tightened to triggers only (dropped the
    "Conducts targeted web research…" workflow narration).
  - Step 6 gains a `mkdir -p docs/` pre-write so Phase 4 is robust
    against stacks where Phase 2 did not create `docs/`.
  - Both hardcoded "IST time" occurrences replaced with "user's
    detected local timezone (see `collab_progress/PROTOCOL.md`)".
- `skills/using-beam/SKILL.md`
  - Full rewrite. Frontmatter `description` trimmed to triggers only.
    All prose wrapped at 80 chars. Section 1 (State Detection),
    Section 2 (Session Management), Section 3 (Phase Transitions), and
    Hard Rules trimmed by ~25%. "Yield Execution" language rewritten
    so the orchestrator keeps gate-verification responsibility after
    the phase skill returns.
- `skills/scaffolding-repo/collab_progress_config.json`
  - `README.md` content string harmonised with `PROTOCOL.md` and
    `CHANGELOG.md`: filenames use `DD-MM-YYYY`, date fields use ISO
    `YYYY-MM-DD`, time fields use user's local timezone.
- `AGENTS.md`
  - Project Structure tree updated to list
    `collab_progress_config.json` under `scaffolding-repo/` and
    `agents_config.json` under `writing-agents-md/`. All `assets/`
    comments removed.
  - Coding-style rule on JSON templates rewritten: "alongside their
    skill" with the four real filenames listed.
- `README.md`
  - "What it does" rewritten so step 2 captures README + scaffolding,
    step 3 captures AGENTS.md only, and template paths are real.
- `docs/SPEC.md`
  - §3 Phase 2 rewritten: trigger updated to "plan.md exists and either
    collab_progress/ or README.md missing"; lists
    `collab_progress_config.json` path; describes resume semantics;
    adds language-specific manifests; adds `README.md` output.
  - §3 Phase 3 rewritten as "AGENTS.md only", driven by
    `skills/writing-agents-md/agents_config.json`, with the new
    user-preferences step; explicit note that README is Phase 2.
  - §3 Phase 4 template path fixed (`spec-template.json`, not `.md`),
    and the length hint reconciled with the 600–1200-word target.
  - §6 Template Assets rewritten without `assets/` framing; all four
    real template paths enumerated.
  - §8 Roadmap: OQ-2 and OQ-3 moved to a new "Resolved" subsection
    with one-line justifications; OQ-1, OQ-4, OQ-5, OQ-6 remain open.

## Rationale

The review found two loop-breaking defects (Phase-1 demanding a
not-yet-existent directory; Phase-3 ignoring its own JSON template and
never reading `plan.md`), one silent regression risk (IST hardcoded in
Phase-4 clobbers user-timezone discipline from PROTOCOL.md), one
direct SKILL-vs-SPEC contradiction on package manifests, and
significant documentation drift (`assets/` paths everywhere the real
files live beside their skills). Each fix lands on the minimum surface
area needed to close the bug.

## Verification

- JSON validity of `collab_progress_config.json` after edits:
  `python3 -c "import json; json.load(open(...))"` → OK.
- `ReadLints` clean on all edited files (see next section).
- All edited files re-read and visually inspected in the same session.
- Line-length check: `awk` pass over each edited SKILL excluding table
  lines confirms no prose lines > 80 chars outside of rationalization
  tables.
- Frontmatter check: every edited SKILL starts with `Use when…`, is
  under 1024 chars, and contains triggers only (no workflow summaries).

## Issues / follow-ups

- The RED/GREEN baseline runs for each edited skill are deferred to a
  follow-up session per `AGENTS.md` testing discipline. Concretely,
  each of the five SKILLs should be pressure-tested against the
  rationalisations documented in its table; the high-priority ones are
  `writing-agents-md` (new preferences loop, new preconditions) and
  `using-beam` (trimmed orchestration language).
- Optional improvement from the review still outstanding: promoting the
  nine classification questions in `eliciting-project-goals` §"Domain 8"
  into a numbered list that `writing-technical-spec` Step 2 can
  reference 1:1. Deferred; not part of this implementation plan.
- The existing CHANGELOG entries remain in IST per PROTOCOL's
  "do not rewrite past entries" rule. Only new entries from now on use
  the user's local timezone.

## Next steps

- Run RED/GREEN baselines on the two largest-surface edits
  (`writing-agents-md`, `using-beam`).
- Merge / commit at the user's discretion; per AGENTS.md commit rules,
  a single commit with an imperative subject covering the audit-fix
  batch is appropriate.
