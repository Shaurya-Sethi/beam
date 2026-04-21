---
name: writing-agents-md
description: >-
  Use when beginning Phase 3 of the Beam workflow, or when a repository has
  a `plan.md` and `collab_progress/` but no `AGENTS.md` at the root (or the
  user asks to author or refresh `AGENTS.md`).
---

# Writing AGENTS.md (Phase 3)

This skill produces a single artifact at the repository root: `AGENTS.md`.
It is the primary contributor guide for coding agents and human
collaborators, and it is the source of truth `writing-technical-spec`
(Phase 4) will cross-reference.

## Preconditions

Before doing anything else, verify the following exist in the repository.
If any are missing, **stop and tell the user which precondition failed** —
do not guess. Fabricating `AGENTS.md` from a thin source is the failure
mode this gate exists to prevent.

1. `plan.md` at the repository root (output of Phase 1 —
   `eliciting-project-goals`). This is the **primary source** for every
   `requirements`-mode section.
2. `collab_progress/PROTOCOL.md` and `collab_progress/CHANGELOG.md`
   (output of Phase 2 — `scaffolding-repo`).
3. `README.md` at the repository root (also Phase 2).
4. `skills/writing-agents-md/agents_config.json` alongside this file. This
   is the template the skill walks — without it, there is nothing to
   render.

## Step 1 — Gather repository context

Read, in order:

1. `plan.md` — the authoritative project intent from Phase 1. Every
   `requirements`-mode section below is synthesised primarily from this
   file.
2. `skills/writing-agents-md/agents_config.json` — the section list,
   `mode` values, and `content_requirements` / `exact_content` for each
   section. This is the skeleton of the output.
3. `README.md` — secondary context for tone and public-facing wording.
4. Any other files that clarify commands or conventions (e.g.
   `docs/SPEC.md` if Phase 4 has already run, `package.json`, `Makefile`,
   `.markdownlint.json`).
5. `ls -R` (or equivalent) for the current directory tree.

Do not proceed until all five are in your working context.

## Step 2 — Walk `template_structure` and render each section

Open the `template_structure` array from `agents_config.json`. The document
**must** be titled with the `document_info.title` value (`# Repository
Guidelines`) and written in Markdown. Target the word count in
`document_info.target_word_count` (200–400 words, excluding `exact`-mode
blocks).

Process every entry of `template_structure` **in order**. For each
section, follow the rules for its `mode`:

- **`exact` mode:** copy the `exact_content` value **verbatim**, character
  for character, with no paraphrasing or reformatting. One exception
  applies below.
- **`requirements` mode:** synthesise original content from `plan.md` plus
  the repository context you gathered in Step 1, guided by the section's
  `content_requirements` string. The section title comes from
  `section_title`.

### Augmenting the Documentation & Knowledge Sources section

The **Documentation & Knowledge Sources** section is `exact` mode, and
the `exact_content` block must appear verbatim. After copying it, you
**may** append extra numbered entries to its Primary Sources list if
`plan.md` or repository context names a non-library reference that is
genuinely load-bearing for the project — for example a methodology note
(the OpenAI harness-engineering guide), a domain specification, an
internal runbook, or a research paper the project implements.

Rules for augmenting:

- Do not modify the verbatim block. Append only.
- Only add sources that are already visible in `plan.md`, `README.md`, or
  equivalent project context. Do not invent or web-search for them here.
- A project-specific reference should be a single numbered bullet with a
  short sentence explaining why it belongs. If no such reference applies,
  leave the section untouched.

Do not extend any other `exact`-mode section. They are immutable.

## Step 3 — Write the file

Write the assembled content to `AGENTS.md` at the repository root,
overwriting any prior version. Confirm the write by reading the file
back.

## Step 4 — Solicit user preferences and finalise

After `AGENTS.md` is on disk:

1. **Notify the user** that `AGENTS.md` has been written. Summarise in
   one or two sentences which sections came from `plan.md` and which
   were copied verbatim from the template.
2. **Ask explicitly** for any special instructions or preferences the
   user wants captured that are not already in the file — for example
   preferred testing conventions, commit-message style, tooling
   constraints, voice/tone, secrets-handling rules, or references the
   agent should always consult. Ask as a single direct question; do not
   bundle it with other requests.
3. **If the user provides any**, integrate the additions into the
   appropriate `requirements`-mode section(s) and rewrite `AGENTS.md`.
   Do not add material to `exact`-mode sections (except per the
   Documentation-and-Knowledge-Sources rule above).
4. **If the user declines or says there's nothing to add**, record that
   decision in the Phase 3 progress note and continue.

This step is not optional. The user's voice is Phase 3's last chance to
enter the harness.

## Hard rules

1. Do not author `AGENTS.md` without `plan.md`. If it is missing, stop
   and ask the user to run Phase 1 (`eliciting-project-goals`) first.
2. Do not skip the `agents_config.json` template. The JSON is the
   template; this SKILL is the renderer.
3. Do not paraphrase, reformat, or shorten any `exact`-mode
   `exact_content` value. Verbatim means verbatim.
4. Do not extend `exact`-mode sections, with the single exception of
   adding extra Primary Sources to "Documentation & Knowledge Sources"
   under the rules in Step 2.
5. Do not skip Step 4. An AGENTS.md that was never shown to the user
   for a preferences pass is incomplete, regardless of how good it looks
   on disk.
6. Do not invoke any downstream Beam skill from inside this phase.
   `using-beam` handles phase transitions.

## Rationalization table

| Rationalisation | Response |
|---|---|
| "I'll paraphrase the exact blocks so they flow better with the rest of the document." | No. `exact_content` is verbatim. Paraphrasing is the failure mode this skill's gate exists to prevent. |
| "`plan.md` is thin / missing, but `README.md` is rich enough — I'll just use that." | No. `plan.md` is the authoritative Phase-1 artifact. If it is missing, stop and ask the user to run Phase 1. README is a public-face document and will miss goals, non-goals, and classification signals. |
| "200–400 words is a guideline, so padding the sections to be more thorough is fine." | No. 400 words excluding `exact` blocks is a ceiling, not a target to hit. Fidelity to `plan.md` beats length every time. |
| "The user didn't reply to the 'any preferences?' prompt right away, I'll assume none and move on." | No. Silence is not confirmation. Wait, or ask once more explicitly. Only a direct "no / nothing to add" from the user closes Step 4. |
| "This project clearly needs a Docs & Knowledge Sources entry for the harness-engineering note, so I'll just rewrite that section from scratch." | No. Copy the `exact_content` verbatim first, then append the extra source as a new numbered bullet. Do not replace or edit the verbatim block. |
| "The JSON template and the inlined content used to drift, so I'll re-inline everything in the SKILL to be safe." | No. The JSON is the single source of structure; the SKILL is the renderer. Re-inlining is exactly what reintroduces drift. |
| "The user named a new section they want, I'll invent a new entry in the template." | No. Additions go into the nearest matching existing section. Changing `agents_config.json` is a template-authoring task, not a Phase-3 task. |

## Output checklist

Before marking Phase 3 complete, verify every item:

- [ ] All four Preconditions passed before any writing began.
- [ ] `plan.md` was read and used to fill every `requirements`-mode
      section.
- [ ] `AGENTS.md` exists at the repository root.
- [ ] Document title matches `document_info.title` (`# Repository
      Guidelines`).
- [ ] Every entry in `template_structure` appears in the final file, in
      order.
- [ ] Every `exact`-mode section is byte-for-byte identical to its
      `exact_content` value (Documentation & Knowledge Sources may have
      extra appended Primary Sources, and only those).
- [ ] Every `requirements`-mode section is written in the project's
      voice, not boilerplate.
- [ ] Word count is between 200 and 400 words, excluding `exact` blocks.
- [ ] Step 4 executed: the user was notified, explicitly asked for
      special instructions/preferences, and any provided additions are
      integrated.
- [ ] A progress note has been added under `collab_progress/` per
      `collab_progress/PROTOCOL.md`, and a new entry has been prepended
      to `collab_progress/CHANGELOG.md` with the user's detected local
      timezone.
