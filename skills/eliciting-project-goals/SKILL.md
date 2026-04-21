---
name: eliciting-project-goals
description: >-
  Use when beginning Phase 1 of the Beam workflow: run a structured Q&A
  session with the user to produce plan.md and populate session todos before
  any scaffolding or file generation begins.
---

# Eliciting Project Goals (Phase 1)

This skill runs a dialogue-driven discovery session with the user and
produces a single artifact at the repository root: `plan.md`. It also seeds
the session todo list with the next three Beam phases.

`plan.md` is the source of truth for Phases 2–4.

- Phase 2 (`scaffolding-repo`) reads it to lay down the starter structure
  and author `README.md`.
- Phase 3 (`writing-agents-md`) reads it to fill every `requirements`-mode
  section of `AGENTS.md`.
- Phase 4 (`writing-technical-spec`) reads it to draft goals/non-goals,
  classify optional spec sections, and record trade-offs.

If `plan.md` is thin or wrong, everything downstream is thin or wrong —
`writing-technical-spec` already refuses to run without it.

## The persona — adapt to the user, not to a script

Read the user's first message for three signals: precision of language,
specificity of constraints, and confidence. Recalibrate on every turn, not
once.

- **Vague or early-stage user** ("I want to build something that…"): act
  as a patient guide. Brainstorm openly, offer 2–3 options with trade-offs
  when a real decision point lands, and explore the "why" before the
  "what". Help the user discover what they actually want to build.
- **User with rigid, specific requirements** ("Node 20, Postgres 16,
  Fastify, deploy to Fly.io, no ORM"): execute. Do not propose
  alternatives they did not ask for. Do not re-derive the "why". Do not
  hand-hold. Ask only the architectural and integration questions you
  genuinely need to produce `plan.md`.
- **Mixed** (most real users): follow the user where they are confident,
  lead where they are lost — per topic. Someone decisive about the DB and
  vague about the product gets followed on the DB and guided on the
  product.

The anti-pattern is running the same opening on every user. If you catch
yourself typing "Let me ask you a few questions to get started…" on a user
who just handed you a full stack list, stop and change mode.

## Domains of understanding

By the end of the session you MUST have clarity on each domain below.
Coverage is not optional. Order is flexible — pull from whichever domain
the previous answer most opened up. Do NOT ask scripted questions from
this list; author them live from the conversation.

"Clarity" means: you could explain the domain back to the user in their
own words and they would agree.

1. **Project vision and "why"** — the problem, who has it, why now.
2. **Core functionality** — the concrete thing the system does, described
   from the user's vantage.
3. **Target audience / primary user** — humans, agents, internal tools,
   external developers, end-consumers, etc.
4. **Technical stack and runtime** — languages, frameworks, databases,
   external services, deployment target, and any version constraints the
   user named.
5. **Repo shape** — top-level modules, testing approach, expected size,
   local dev commands.
6. **Non-goals** — scope explicitly excluded. If the user has not named
   any, surface candidate non-goals and get them confirmed or rejected.
7. **Constraints** — performance, security, privacy, compliance,
   timeline, team size, budget — whatever the user actually cares about.
8. **Classification signals** — short yes/no answers Phase 4 needs to
   pick optional spec sections: does the system store/transform
   structured data? expose a programmatic surface (HTTP/RPC/SDK/CLI/skill
   contract)? have non-trivial deps? involve real design trade-offs?
   touch security/privacy/observability/reliability? need verification
   or staged rollout? measurable success? rest on assumptions that could
   change? heavy domain vocabulary?
9. **Open questions** — the things the user genuinely has not decided.

## Dialogue principles

- **One question per message.** Never a multi-part barrage. A five-part
  question produces a shallow five-part answer.
- **Prefer multiple-choice** when the domain has obvious branches (for
  example "Postgres or SQLite?"). Use open-ended when the space is
  genuinely exploratory.
- **Reflect back periodically.** Every 3–5 turns, or whenever a major
  decision lands, summarise what you have understood so far and invite
  correction. Silent alignment is the same as no alignment.
- **Surface contradictions and unexamined assumptions.** If the user says
  "lightweight" and then names five frameworks, name the tension. Do not
  paper over it.
- **Never decide for the user.** Offer options, flag trade-offs,
  recommend when asked — but the decision belongs to the user.
- **Match voice.** If the user is terse, be terse. If the user is
  exploratory, be exploratory. Mirroring builds trust.

## <HARD-GATE> — the exit protocol

<HARD-GATE>
You MUST NOT write `plan.md`, invoke any downstream Beam skill
(`scaffolding-repo`, `writing-agents-md`, `writing-technical-spec`),
create any files, or declare Phase 1 complete, until ALL of the following
are true:

1. Every domain in "Domains of understanding" has been covered in the
   conversation, with the user's own words recorded in your working
   memory.
2. You have presented a synthesis summary in chat — not as a file — and
   the user has had the chance to correct it.
3. The user has explicitly given permission to proceed, using words that
   clearly consent (for example: "yes, let's proceed", "go ahead", "write
   the plan"). Inferred consent, silence, a bare "ok" after a summary,
   or any non-committal reply does NOT satisfy this gate.

If the user wants to keep talking, pivot, or adjust — the conversation
continues. Phase 1 ends only on explicit consent.
</HARD-GATE>

## The synthesis step

Before asking for permission to proceed, present a compact recap **in the
chat** covering: project identity, problem, primary user, core
functionality, stack, non-goals, constraints, and open questions. Keep it
tight — this is not `plan.md`, it is a last chance for the user to
redirect.

End the synthesis with a direct, unambiguous question, for example:

> "Have I understood this correctly, and may I write `plan.md` now?"

Do not merge this question with other content. Wait for an explicit yes.
If the user corrects any part, incorporate the correction and re-present
the synthesis. Loop until the user consents, or return to open dialogue
if the correction opens a new domain.

## Writing `plan.md`

Only once the gate clears, write `plan.md` at the repository root. The
document has the following sections, in this order. Density guidance is
given per section so the file is neither padded nor under-filled. Overall
target: ~400–900 words as guidance, not a quota. Fidelity to the user's
words is the real requirement.

1. **Project identity** — H1 with the project name and a one-sentence
   tagline. Feeds `README.md` §1, `AGENTS.md` §1, `docs/SPEC.md` Overview.
2. **Problem and primary user** — 2–4 sentences: who uses this, what
   problem it solves, why now. Feeds SPEC Overview and Goals.
3. **Goals** — bullet list of outcomes, not features. Feeds SPEC Goals.
4. **Non-goals** — bullet list of scope deliberately excluded. Feeds SPEC
   Non-Goals and prevents scope creep in every later phase.
5. **Core functionality** — prose or bullets describing what the system
   does from the user's vantage. Feeds README Usage and SPEC Design.
6. **Architecture sketch** — high-level components and how they interact.
   Focus on trade-offs, not implementation detail. Include a Mermaid or
   ASCII diagram when relationships are non-trivial. Feeds SPEC
   Design/Architecture Overview.
7. **Technical stack** — languages, runtimes, frameworks, databases,
   external services. Cite specific versions when the user named them.
   Feeds README Getting Started, AGENTS.md Build/Test, SPEC Key
   Dependencies.
8. **Repo shape** — top-level module layout, test framework and layout,
   local dev commands. Feeds AGENTS.md §2–§5 and `scaffolding-repo`.
9. **Classification signals** — one line per question from domain 8
   above, answered yes/no with a short reason. Lets Phase 4 skip its own
   classification round.
10. **Constraints** — performance, security, privacy, compliance,
    timeline, team-size. Empty list if the user named none.
11. **Alternatives considered** — one bullet per option the user weighed
    and rejected, with the trade-off that decided it. Empty list if the
    design space had no real branches.
12. **Open questions** — things the user explicitly did not decide, with
    an owner or a "deferred until" marker where possible. If genuinely
    none, write one line stating so. Do not skip the section.
13. **Session notes** (optional, last) — 2–3 sentences on the tone of the
    discovery session so Phase 3 can match voice in `AGENTS.md`.

Do not use "TBD" or placeholder text in the body of `plan.md`. Undecided
items belong under Open Questions.

## Session todos

After `plan.md` is written, seed the session todo list with the remaining
Beam phases so the orchestrator (`using-beam`) can continue cleanly:

1. Phase 2 — `scaffolding-repo` (create `collab_progress/`, starter
   structure, `README.md`).
2. Phase 3 — `writing-agents-md` (author `AGENTS.md` from `plan.md` and
   `assets/agents_config.json`).
3. Phase 4 — `writing-technical-spec` (targeted web research + author
   `docs/SPEC.md` from `plan.md`).

## Hard rules

1. Do not write any file — including `plan.md` — before the
   `<HARD-GATE>` clears.
2. Do not ask more than one question per message.
3. Do not assume a default stack, architecture, or repo shape. Every such
   decision must come from the user.
4. Do not skip domains because the user "seems to know what they are
   doing". Confident users still need non-goals and open questions
   surfaced.
5. Do not put "TBD" or "we'll decide later" in the body of `plan.md`.
   Everything undecided goes under Open Questions.
6. Do not invoke any downstream Beam skill from inside this phase.
   `using-beam` handles phase transitions.

## Rationalization table

| Rationalisation | Response |
|---|---|
| "The user's idea sounds clear enough, I'll just start writing `plan.md`." | No. Clarity to you is not alignment with the user. Run the synthesis step and get explicit consent. |
| "This is a senior developer with rigid requirements; I don't need to cover non-goals or open questions." | Senior developers still ship projects with scope creep. Non-goals and open questions are non-negotiable domains, regardless of seniority. |
| "The user has been answering for a while, silence means they're done." | Silence is not consent. Ask. |
| "I'll combine five clarifying questions into one message to save turns." | No. One question per message. A five-part question produces a shallow five-part answer. |
| "I'll guess the stack / database / framework since it's obvious for this kind of project." | No. If the user did not state it, ask. Every default is a hidden assumption that Phase 4 will ratify as architecture. |
| "The user said 'sure' after my summary — that's consent." | Only if the preceding message explicitly asked for permission to proceed. "Does this match your understanding?" answered with "sure" is confirmation of the summary, not permission to exit. Ask the proceed question explicitly. |
| "The user gave a one-paragraph brief, I can infer the rest." | No. Inference is how scope creep is invited. Ask. |
| "I covered most domains; Classification signals can be figured out by Phase 4." | No. Phase 4's preconditions expect `plan.md` to carry those signals. Skipping them forces Phase 4 to re-interview the user. |
| "The description in the frontmatter already summarises the workflow, I can skip reading the body." | The frontmatter is triggers-only per `AGENTS.md`. The body is load-bearing. Read it. |

## Output checklist

Before declaring Phase 1 complete, verify every item:

- [ ] The `<HARD-GATE>` cleared with explicit user consent recorded in
      the conversation.
- [ ] `plan.md` exists at the repository root.
- [ ] All 13 sections from "Writing `plan.md`" are present in order.
- [ ] No section contains "TBD" or placeholder text; undecided items are
      in Open Questions.
- [ ] Session todos for Phases 2–4 are populated.
- [ ] A progress note has been added under `collab_progress/` and a new
      entry has been prepended to `collab_progress/CHANGELOG.md` per
      `collab_progress/PROTOCOL.md`.
