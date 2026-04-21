# Author `eliciting-project-goals` SKILL.md — progress note

## Summary

Replaced the stub body of
`skills/eliciting-project-goals/SKILL.md` with a full Phase 1 behaviour
definition. The skill codifies an adaptive, dialogue-driven discovery
session: calibrate to the user (junior/guide vs. senior/executor),
cover nine mandatory domains of understanding, obey a one-question-per-
message dialogue discipline, and exit only through an explicit
`<HARD-GATE>` that requires synthesis-in-chat plus unambiguous user
consent.

Phase 1's single artifact is `plan.md` at the repo root. The skill
specifies its 13 sections, density guidance per section, and a mapping
from each section to the downstream phase that consumes it (Phase 2
scaffolding, Phase 3 `AGENTS.md`, Phase 4 `docs/SPEC.md`). Section 9
("Classification signals") deliberately answers the nine questions in
`writing-technical-spec/SKILL.md` Step 2, so Phase 4 can skip its own
classification round.

The skill is discipline-enforcing and therefore ships with a
rationalization table (nine entries) per `AGENTS.md` testing guidelines.
The existing frontmatter was kept verbatim (triggers-only).

## Files Changed

- `skills/eliciting-project-goals/SKILL.md` — stub replaced with full
  body (~239 lines, within the 180–240 target set in the plan).
- `collab_progress/author-eliciting-project-goals-skill-21-04-2026.md`
  — this note.
- `collab_progress/CHANGELOG.md` — new entry prepended.

No other skills, templates, or `assets/` files were touched.

## Rationale

Phase 1 is the foundation the rest of the Beam workflow rests on. The
prior stub left the agent to improvise, which (per brainstorming-skill
precedent and the existing `writing-technical-spec` precondition check)
is the exact failure mode that produces thin or wrong downstream
artifacts. The plan (see
`.cursor/plans/phase-1_skill_body_0e534672.plan.md`) was followed
section-for-section; the only deviations from the plan are cosmetic
(section ordering within the "Writing `plan.md`" list and a ninth
rationalization-table row about skipping the SKILL body because the
frontmatter "already summarises" the workflow — added for symmetry with
the triggers-only rule in `AGENTS.md`).

The skill deliberately contains no scripted clarifying questions and no
dialogue flowchart. Both were considered and rejected in the plan: any
example questions become the agent's default script, and the session
branches too heavily on user input for a flowchart to stay honest.

## Verification

- All non-table lines ≤80 chars (verified with `awk '{ if (length($0)
  > 80) print NR }'`). The nine long lines are all inside the
  rationalization table, which is exempt under
  `.markdownlint.json` (`"tables": false`).
- File length: 239 lines, within the 180–240 target.
- Frontmatter unchanged from prior stub (triggers-only, conforming to
  `AGENTS.md` §Coding Style).
- Self-review confirmed no scripted question lists leaked in; the only
  quoted strings are either user-voice examples (e.g. "Node 20,
  Postgres 16, Fastify…") or an illustrative multiple-choice example
  ("Postgres or SQLite?") explicitly marked "for example".
- **RED/GREEN agent-behaviour baseline: PASS** (single-turn opener
  test, two parallel read-only subagents, identical scenario:
  "Hey, I'd like to kick off a new project. I want to build some
  kind of SaaS task management app for small teams. Can you help
  me get the repo set up properly?"):
  - **RED** (no skill access): 5–6 distinct questions in one
    message; proposed a full stack (Next.js 15 + TypeScript +
    Tailwind + shadcn + Prisma + Postgres + Auth.js + Stripe),
    a Turborepo monorepo layout, and specific Prisma models —
    all before the user answered anything; explicitly offered
    to "scaffold the whole thing right now" on a single "go"
    from the user. Every failure mode the skill is designed to
    prevent.
  - **GREEN** (skill loaded): 1 question, anchored on "why"
    (domain 1); no stack, architecture, or structure proposed;
    explicit "before I touch anything in the repo…" guard;
    correctly self-calibrated to *patient guide* from the user's
    "some kind of" hedge and casual register; referenced the 9
    domains / synthesis / explicit-consent gate internally.
  - **REFACTOR:** no new rationalisations surfaced; the existing
    9-entry rationalization table covers what the RED run
    exhibited (multi-part question stacking, assumed-defaults
    scaffolding, barrel-forward-without-consent). No skill
    edits required.
  - Agent transcripts: subagent IDs recorded only in session
    output, not persisted; re-runnable via the same prompts in
    this note.
- **Follow-up still open:** a multi-turn baseline (not just
  first-turn) would stress-test consent parsing ("sure" after a
  summary) and the one-question-per-message rule across 8–10
  turns. Not run this session; logged as a future verification.

## Issues

- RED/GREEN baseline outstanding (see Verification). Until it runs, the
  `<HARD-GATE>` and one-question-per-message rules are unproven against
  live agent rationalizations. New rationalizations surfaced by the
  baseline should be added to the existing table, not stored elsewhere.
- `skills/using-beam/SKILL.md`, `skills/scaffolding-repo/SKILL.md`, and
  `skills/writing-agents-md/SKILL.md` remain stubs. This session does
  not change that.

## Next Steps

1. Run the RED/GREEN baseline for `eliciting-project-goals`:
   - RED: fresh subagent session without this skill loaded, asked to
     do Phase 1 for a sample project; record where it barrels forward
     without consent, asks multi-part questions, or skips domains.
   - GREEN: re-run with the skill loaded; confirm compliance.
   - REFACTOR: fold any new rationalizations into the skill's table.
2. Author the remaining three stub skills (`using-beam`,
   `scaffolding-repo`, `writing-agents-md`) per the plan in
   `docs/template-design-plan.md` §6.
3. No `git add` / `git commit` performed; working tree left dirty for
   user review.
