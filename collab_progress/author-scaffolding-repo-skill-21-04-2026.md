# Author scaffolding-repo SKILL.md (Phase 2 body)

## Summary
Populated the previously stubbed `skills/scaffolding-repo/SKILL.md` with the full behavioral guidelines for Phase 2. This phase instructs the agent to read the `plan.md` artifact (created in Phase 1) along with structural templates (`readme-template.json` and `collab_progress_config.json`), and then scaffold the repository boilerplate and establish the core `collab_progress` tracking structure.

## Files Changed
- `skills/scaffolding-repo/SKILL.md` (updated from stub to full body)

## Rationale
Phase 2 bridges abstract planning (Phase 1) and actual project setup. The skill must be robust against AI overreach (e.g., trying to write application logic instead of just scaffolding). The instructions explicitly mandate exact adherence to the JSON configurations for `README.md` and `collab_progress/`, enforcing consistency in how the project is shaped. To ensure the new protocol is respected, the skill forces the executing agent to create its own initial progress note under the newly bootstrapped `collab_progress/` structure.

## Verification
- Frontmatter (`name`, `description`) unchanged.
- Ensured formatting guidelines are respected.
- Emphasized strict `<HARD-GATE: NO FEATURE CODE>` to prevent scope creep during scaffolding.
- Included rationalization table outlining potential AI excuses for avoiding scaffolding strictness or protocol documentation.
- The RED/GREEN agent-behavior baseline has not yet been executed in a fresh subagent, so this is deferred as a follow-up per testing discipline.

## Issues
- None at this time.

## Next Steps
- Run the RED/GREEN baseline test with an isolated agent session for Phase 2 scaffolding to confirm it parses the configuration and outputs exactly the `collab_progress/` directory without deviating.
- Author `using-beam` and `writing-agents-md` remaining stubs.