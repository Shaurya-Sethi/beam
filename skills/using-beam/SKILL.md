---
name: using-beam
description: >-
  Use when starting a new project repository and needing to run the full
  Beam initialization workflow: goal elicitation, repo scaffolding,
  AGENTS.md/README.md generation, and technical spec creation.
---

# Using Beam (The Orchestrator)

This skill is the **Master Bootstrap Skill** for the Beam harness workflow. It acts as a state machine for repository initialization. 

**You are the Conductor, not the Player.** 
Your job is NOT to write `plan.md` or code the scaffolding yourself without reading the phase instructions. Your job is to guide the user through the 4 phases, load the correct skill for the current phase, maintain a visible state for the user, and enforce the phase transitions.

## 1. State Detection & Resiliency

Beam is designed to be resumable. When invoked, you must detect the repository's current state to determine which phase to trigger. Use your file-reading tools to verify the presence of the following artifacts in order:

1. **Check for `plan.md`**
   - If it does NOT exist: The state is **Phase 1**.
   - Target skill: `skills/eliciting-project-goals/SKILL.md`
2. **Check for `collab_progress/` and `README.md`**
   - If `plan.md` exists, but either of these is missing: The state is **Phase 2**.
   - Target skill: `skills/scaffolding-repo/SKILL.md`
3. **Check for `AGENTS.md`**
   - If Phase 2 artifacts exist, but `AGENTS.md` is missing: The state is **Phase 3**.
   - Target skill: `skills/writing-agents-md/SKILL.md`
4. **Check for `docs/SPEC.md`**
   - If `AGENTS.md` exists, but `docs/SPEC.md` is missing: The state is **Phase 4**.
   - Target skill: `skills/writing-technical-spec/SKILL.md`

If all of the above artifacts exist, the Beam initialization workflow is complete.

```mermaid
flowchart TD
    Start([Invoke using-beam]) --> Check1{plan.md exists?}
    Check1 -- No --> P1[Load: eliciting-project-goals]
    Check1 -- Yes --> Check2{collab_progress/ &\nREADME.md exist?}
    Check2 -- No --> P2[Load: scaffolding-repo]
    Check2 -- Yes --> Check3{AGENTS.md exists?}
    Check3 -- No --> P3[Load: writing-agents-md]
    Check3 -- Yes --> Check4{docs/SPEC.md exists?}
    Check4 -- No --> P4[Load: writing-technical-spec]
    Check4 -- Yes --> Done([Workflow Complete])

    P1 -->|Handoff / Retry| Start
    P2 -->|Handoff / Retry| Start
    P3 -->|Handoff / Retry| Start
    P4 -->|Handoff / Retry| Start
```

## 2. Session Management & Visibility

At the start of the orchestration (and on every resume), you MUST introduce or re-introduce the workflow to the user. 

**Visible Checklist:** Provide a clear markdown checklist in the chat showing all 4 phases and checking off the ones that are already complete based on your State Detection. 

Example:
> **Beam Repository Initialization**
> - [x] Phase 1: Goal Elicitation (`plan.md`)
> - [ ] Phase 2: Scaffolding (`collab_progress/` & `README.md`)
> - [ ] Phase 3: Guidelines (`AGENTS.md`)
> - [ ] Phase 4: Technical Spec (`docs/SPEC.md`)
> 
> *Starting Phase 2...*

**Session Todos:** You MUST dynamically update your internal session todos to list the current active phase and the remaining phases to keep your own context grounded.

## 3. Phase Transitions

To transition into a phase, you must execute these steps rigidly:

1. **Load the Instructions:** Use your `read` tool to explicitly read the target phase's `SKILL.md` file directly into your context window.
2. **Yield Execution:** Stop acting as the orchestrator and adopt the persona and rules of the loaded skill. Follow its pre-flight checks, sequences, and rules exactly.
3. **Verify the Gate:** You must not declare the phase complete or transition to the next phase until the current phase's "Output Checklist" or `<HARD-GATE>` is fully satisfied.
4. **Re-run Detection:** Once the checklist is complete, return to step 1 of the Orchestrator (State Detection) to verify the artifacts exist and trigger the next phase.

## Hard Rules

- **Do not simulate the work.** You must load the child skills. Do not try to guess what a phase does without reading its `SKILL.md`.
- **Do not batch phases.** Execute exactly one phase at a time. The transition between phases requires the artifacts of the previous phase to be securely written to disk.
- **Respect constraints.** Phase 1 has a `<HARD-GATE>` requiring explicit user consent. You cannot bypass this by jumping to Phase 2 before that explicit consent and the `plan.md` artifact generation are complete.

## Orchestrator Final Output Checklist

The Beam workflow is only completely finished when:
- [ ] `plan.md` exists and is populated.
- [ ] `collab_progress/` and `README.md` are scaffolded.
- [ ] `AGENTS.md` is accurately generated.
- [ ] `docs/SPEC.md` exists with all mandatory sections.
- [ ] The user has been notified that the project is ready for development.
