## Summary
Authored the behavior definition for the master bootstrap skill `skills/using-beam/SKILL.md`. The orchestrator guides the user through the 4 phases of repository initialization, functioning as a state machine.

## Files Changed
- `skills/using-beam/SKILL.md`

## Rationale
The `using-beam` skill handles project orchestration, meaning it evaluates the workspace to determine which phase of Beam to invoke. Its implementation ensures it:
1. Detects state robustly (e.g., checks `plan.md` for phase 1 completion, `collab_progress/` for phase 2, etc.).
2. Employs a Mermaid state diagram for clear flow.
3. Sets up dynamic session todos and checklists for user visibility.
4. Requires loading child skills explicitly into context via the `read` tool rather than simulating their behavior.

## Verification
- Validated markdown formatting.
- Verified flow matches instructions across `eliciting-project-goals`, `scaffolding-repo`, `writing-agents-md`, and `writing-technical-spec` phases.
- Verified preservation of required YAML frontmatter.

## Issues
- None

## Next Steps
- Run behavior tests to verify agent correctly assumes the orchestrator role.
