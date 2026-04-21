# Template Design Plan — progress note

## Summary

Authored `docs/template-design-plan.md`, a research-backed proposal for
how `skills/scaffolding-repo/readme-template.json` and
`skills/writing-technical-spec/spec-template.json` should be
restructured. The plan recommends shrinking each template's
`template_structure` to a truly universal, always-required skeleton,
and moving every project-type-dependent section into prose guidance
inside the SKILL.md that invokes the template.

Proposed minimal skeletons:

- **README:** Project name & tagline, Overview, Getting Started,
  Usage, License (5 sections; Contributing dropped as project-specific).
- **SPEC:** Overview, Goals & Non-Goals, Design / Architecture
  Overview, Open Questions (4 sections; Data Model, API /
  Interface Design, Key Dependencies dropped as project-specific).

The plan also recommends keeping the current "JSON template +
SKILL.md guidance" pattern over three rejected alternatives (markdown
with HTML-comment conditionals, a `mode: "optional"` field, or split
core/extended JSONs). Full reasoning, sources, and proposed SKILL.md
guidance prose are in the plan file.

## Files Changed

- `docs/template-design-plan.md` — new file (the plan).
- `collab_progress/template-design-plan-21-04-2026.md` — new file
  (this note).
- `collab_progress/CHANGELOG.md` — new entry prepended.

No JSON templates and no SKILL.md files were touched this session.

## Rationale

The existing JSON templates mark every section as
`"mode": "requirements"`, which causes agents to force-fill sections
that are meaningless for the project at hand (e.g. a Data Model
section in a pure workflow/skills repo). The design constraint is
that LLMs treat every key they see in a JSON template as mandatory,
so the fix is to keep the JSON minimal and move conditional guidance
into the procedurally-shaped SKILL.md that already drives the phase.

Research consulted: makeareadme.com, awesome-readme, Design Docs at
Google (industrialempathy.com), Joel Spolsky's Painless Functional
Specifications, GitHub `spec-kit`'s `spec-template.md` (which itself
uses an `*(include if …)*` conditional tag — the exact anti-pattern
we are removing), GSD / BMAD / Atlassian / DocuWriter 2026 guides.

## Verification

- Plan is deliberately non-executing: no JSON template, no SKILL.md,
  and no `assets/agents_config.json` was edited, so there is no
  agent-behaviour change to RED/GREEN-verify yet.
- `docs/template-design-plan.md` was checked against its own section
  list: research summary, universal-vs-specific reasoning, proposed
  minimal skeleton tables for both templates with justification per
  section, proposed SKILL.md guidance prose for both skills,
  recommendation on the pattern with rejected alternatives, follow-up
  todos, and a sources list. All present.
- Follow-up todo list in §6 of the plan includes the user-requested
  item "write the full body of `skills/writing-technical-spec/SKILL.md`"
  (not just consuming the trimmed template).

## Issues

- None at the plan level. The downstream implementation todos in §6
  will each need their own RED/GREEN verification per `AGENTS.md`.
- `skills/scaffolding-repo/SKILL.md` and
  `skills/writing-technical-spec/SKILL.md` remain stubs; this plan
  does not change that.

## Next Steps

Per §6 of the plan file:

1. Trim `readme-template.json` `template_structure` to 5 sections.
2. Trim `spec-template.json` `template_structure` to 4 sections.
3. Author `skills/scaffolding-repo/SKILL.md` body with README
   extension guidance.
4. Author `skills/writing-technical-spec/SKILL.md` body with SPEC
   extension guidance and the phase-4 web-research workflow.
5. RED/GREEN-baseline each of the above skill changes.

Working tree left dirty for user review; no `git add` or `git commit`
was run.
