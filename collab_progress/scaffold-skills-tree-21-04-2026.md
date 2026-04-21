# Scaffold skills/ tree and update AGENTS.md

## Summary

Created the full `skills/` directory tree with stub `SKILL.md` files for all
five skills (using-beam, eliciting-project-goals, scaffolding-repo,
writing-agents-md, writing-technical-spec). Added `readme-template.md` under
`scaffolding-repo/` and `spec-template.md` under `writing-technical-spec/`.

Updated `AGENTS.md` to:
- Remove the "aspirational" qualifier from the Project Structure section.
- Reflect the actual directory layout (structure now matches the real file tree).
- Move `readme-template.md` from `writing-agents-md/` to `scaffolding-repo/`
  in both the tree and the phase numbering (Phase 3 is now AGENTS.md-only;
  README generation is folded into Phase 2).
- Relabel the high-level phase list item 3 to reflect AGENTS.md generation
  only (README.md generation is now part of Phase 2 scaffolding).

No skill bodies were written; all SKILL.md files contain only stub frontmatter
and a TODO comment.

## Files Changed

- `AGENTS.md` — removed aspirational note, updated tree, updated phase list
- `skills/using-beam/SKILL.md` — created (stub)
- `skills/eliciting-project-goals/SKILL.md` — created (stub)
- `skills/scaffolding-repo/SKILL.md` — created (stub)
- `skills/scaffolding-repo/readme-template.md` — created (placeholder template)
- `skills/writing-agents-md/SKILL.md` — created (stub)
- `skills/writing-technical-spec/SKILL.md` — created (stub)
- `skills/writing-technical-spec/spec-template.md` — created (placeholder template)

## Rationale

The AGENTS.md structure section described files that did not yet exist on disk,
and labelled the discrepancy "aspirational". Creating the files removes the gap
between documentation and reality. Moving `readme-template.md` to
`scaffolding-repo/` aligns the file location with the phase that uses it.

## Verification

- `find` output confirmed all files exist at the expected paths.
- `grep` confirmed the word "aspirational" no longer appears in AGENTS.md.
- `grep` confirmed `readme-template.md` is attributed to `scaffolding-repo/`
  in AGENTS.md and lives there on disk.

## Issues

None. Skill bodies are intentionally left as stubs — to be written in a
future session.

## Next Steps

- Await user review before committing.
- Write skill bodies for each phase in subsequent sessions.
