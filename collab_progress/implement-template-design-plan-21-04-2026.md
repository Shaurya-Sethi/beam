# Implement Template Design Plan — progress note

## Summary

Executed three of the follow-up todos from
[template-design-plan.md](../docs/template-design-plan.md) (§6):

- Trimmed `skills/scaffolding-repo/readme-template.json` to the 5
  mandatory sections (Project name & tagline, Overview, Getting Started,
  Usage, License). Dropped the previously-mandatory **Contributing**
  section; it is now guidance-only in the scaffolding-repo SKILL.md
  (not authored in this session).
- Trimmed `skills/writing-technical-spec/spec-template.json` to the 4
  mandatory sections (Overview, Goals & Non-Goals, Design / Architecture
  Overview, Open Questions). Dropped the previously-mandatory **Data
  Model**, **API / Interface Design**, and **Key Dependencies**; each
  is now a conditional addition driven by the new SKILL.md body.
- Authored the full body of
  `skills/writing-technical-spec/SKILL.md` (was a `<!-- TODO -->` stub).
  The body covers: preconditions, a six-step workflow (load template →
  read plan.md → classify project → web research → draft mandatory
  sections → append optional sections → write `docs/SPEC.md` + update
  collab_progress), hard rules, a rationalisation table, and an output
  checklist. Triggers-only frontmatter description per the Beam style
  rules in `AGENTS.md`.

## Files Changed

- `skills/scaffolding-repo/readme-template.json` — 6 sections → 5.
- `skills/writing-technical-spec/spec-template.json` — 7 sections → 4.
- `skills/writing-technical-spec/SKILL.md` — stub → ~210-line body.
- `collab_progress/implement-template-design-plan-21-04-2026.md` — new
  (this note).
- `collab_progress/CHANGELOG.md` — new entry prepended.

Not changed in this session:

- `skills/scaffolding-repo/SKILL.md` — still a stub. Authoring its body
  (with the README-extension guidance in plan §4.1) remains an open
  follow-up.
- `skills/using-beam/SKILL.md`, `skills/eliciting-project-goals/SKILL.md`,
  `skills/writing-agents-md/SKILL.md` — still stubs; out of scope for
  this session.

## Rationale

The plan in `docs/template-design-plan.md` argued that LLMs treat every
section in a JSON `template_structure` as mandatory and will fabricate
content to fill inapplicable sections (e.g. a Data Model section for a
pure workflow/skills repo). The fix has two parts: a minimal JSON
skeleton containing only truly universal sections, and a procedural
SKILL.md that tells the agent how to decide whether to append further
sections. This session implements both parts for the SPEC path and the
JSON-skeleton part for the README path.

The SPEC SKILL.md deliberately:

- Puts the project-classification rubric (Step 2) **before** any
  drafting, so the agent commits to an optional-section shortlist
  before momentum carries it into writing Data Model for a skills repo.
- Lists hard rules and a rationalisation table, per the TDD-for-skills
  discipline in `AGENTS.md` — the rationalisations are the failure
  modes this skill is designed to block.
- Forbids inventing content for empty sections, with one explicit
  exception for Cross-Cutting Concerns (silence there would read as
  "not considered", per Google's design-doc reasoning).
- Keeps the frontmatter `description` triggers-only; the workflow
  summary lives in the body, not the frontmatter, so agents load the
  skill rather than following a summary.

## Verification

- `python3 -m json.tool` equivalent parsing of both JSON files
  succeeded; section counts confirmed 5 and 4 respectively.
- `markdownlint`-style line-length check: all non-table lines in
  `skills/writing-technical-spec/SKILL.md` are ≤80 characters
  (`awk 'NR!~/^$/ {if (length > 80 && $0 !~ /^\|/) print …}'` returns
  empty). Table rows are exempt per the `AGENTS.md` style rules.
- `SKILL.md` is 210 lines, well under the 500-line ceiling in the
  Agent Skills specification.
- `ReadLints` across all three edited files returned no diagnostics.
- RED/GREEN of agent behaviour against the new SKILL.md has **not** yet
  been run. The `AGENTS.md` testing discipline requires a baseline in a
  fresh subagent session without the change followed by a re-run with
  the change; see Issues below.

## Issues

- No RED/GREEN baseline has been run yet for the SPEC SKILL.md. Per
  `AGENTS.md` §Testing Guidelines this must happen before the change
  is considered verified. Deferred to a follow-up session because it
  requires spinning up a fresh subagent without the change loaded.
- `skills/scaffolding-repo/SKILL.md` remains a stub. The README
  template is now minimal, but without the extension guidance in the
  corresponding SKILL.md body, a phase-2 agent will produce a
  bare-minimum README for every project type. This is acceptable
  short-term (phase-2 is not yet wired up end-to-end) but should be
  closed out before Beam is shipped.
- The spec skill assumes `web_search` and optionally a `context7`-style
  MCP server are available. No fallback path is documented for agents
  without network access; if that becomes a real constraint, add one.

## Next Steps

1. Author `skills/scaffolding-repo/SKILL.md` body with the README
   extension guidance from plan §4.1 and the phase-2 scaffolding flow
   (create `collab_progress/`, minimal starter structure, generate
   `README.md` from the trimmed JSON).
2. Author the three remaining stubs:
   `skills/using-beam/SKILL.md` (master orchestrator),
   `skills/eliciting-project-goals/SKILL.md` (phase 1),
   `skills/writing-agents-md/SKILL.md` (phase 3).
3. RED/GREEN-baseline each of the above per the `AGENTS.md` discipline.
4. Once the phase-2 SKILL is authored, consider whether the README
   template should pick up a sixth mandatory section (e.g. "Contributing")
   for `beam`-style public open-source projects — the current split
   makes Contributing guidance-only, which is correct for the general
   case but could be relaxed for the Beam project itself.

Working tree left dirty for user review; no `git add` or `git commit`
was run.
