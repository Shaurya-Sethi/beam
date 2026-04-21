# Convert template files from .md to .json

## Summary

Renamed both skill template files from Markdown to JSON, bringing them into
alignment with the existing `assets/agents_config.json` schema. Converted
their content from freeform Markdown placeholders to structured JSON objects
using the same `document_info` + `template_structure` schema already in use.

Deleted the `.md` files and created `.json` replacements. Updated all
references in AGENTS.md (directory tree + Coding Style naming-conventions
example) and in the two affected SKILL.md stubs.

## Files Changed

- `skills/scaffolding-repo/readme-template.md` → **deleted**
- `skills/scaffolding-repo/readme-template.json` → **created**
- `skills/writing-technical-spec/spec-template.md` → **deleted**
- `skills/writing-technical-spec/spec-template.json` → **created**
- `AGENTS.md` — updated tree entries and naming-conventions example
- `skills/scaffolding-repo/SKILL.md` — updated description reference
- `skills/writing-technical-spec/SKILL.md` — updated description reference

## Rationale

JSON is the established template format in this repo (`assets/agents_config.json`
already drives AGENTS.md generation with the same schema). Using the same
format for README and SPEC templates keeps the tooling surface consistent and
gives agents structured, parseable section specs instead of freeform Markdown
placeholders.

## Verification

- `grep -r "readme-template\|spec-template"` across AGENTS.md and skills/
  confirms every reference uses `.json`.
- `find skills/` confirms no `.md` template files remain.
- Both new JSON files are valid JSON (schema mirrors agents_config.json).

## Issues

None.

## Next Steps

- Awaiting user review before committing.
- Skill bodies to be written in future sessions.
