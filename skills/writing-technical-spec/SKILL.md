---
name: writing-technical-spec
description: >-
  Use when beginning Phase 4 of the Beam workflow, or when the user asks
  for a technical specification, design doc, SPEC.md, docs/SPEC.md,
  architecture doc, or "spec the system" for a repository.
---

# Writing a Technical Spec (Phase 4)

This skill produces a single artifact: `docs/SPEC.md`. It is the last phase
of the Beam workflow and assumes phases 1–3 have already run (a `plan.md`
exists from goal elicitation, the repo has been scaffolded, and `AGENTS.md`
has been authored).

The spec is **the** source of truth for project scope, architecture, and
non-goals. `AGENTS.md` points at it; downstream coding agents will read it
before writing code.

## Preconditions

Before doing anything else, verify the following exist in the repository:

1. `plan.md` at the repo root (output of Phase 1 — `eliciting-project-goals`).
2. `AGENTS.md` at the repo root (output of Phase 3 — `writing-agents-md`).
3. `collab_progress/` with `PROTOCOL.md` and `CHANGELOG.md`.
4. `skills/writing-technical-spec/spec-template.json` alongside this file.

If any of these are missing, do not guess. Stop and tell the user which
preconditions failed. The spec depends on what was decided in earlier
phases — fabricating a spec without `plan.md` is the failure mode this
gate exists to prevent.

## Workflow

### Step 1 — Load the template and phase-1 output

1. Read `skills/writing-technical-spec/spec-template.json`. The
   `template_structure` array defines the mandatory skeleton. Every section
   in that array **must** appear in the final `docs/SPEC.md`.
2. Read `plan.md`. This is the single richest source of project intent.
3. Skim `README.md` and `AGENTS.md` for overlaps you can reference instead
   of restating.

### Step 2 — Classify the project

This classification decides which optional sections apply in Step 5.

From `plan.md`, answer the following questions in your head. Do not write
them into the spec. The answers control Step 5.

- Does the system **store, transform, or transmit structured data** with
  entities that have relationships? (If yes → Data Model applies.)
- Does the system **expose a programmatic surface** — HTTP, RPC, SDK, CLI,
  or skill contract? (If yes → API / Interface Design applies.)
- Does the system have **non-trivial runtime, library, or service
  dependencies**? A docs-only or pure-markdown repo generally does not. (If
  yes → Key Dependencies applies.)
- Did the design process involve **real trade-offs between reasonable
  alternatives**? Not "we picked Python because we know Python" — actual
  branch points. (If yes → Alternatives Considered applies.)
- Does the project touch **security, privacy, observability, or
  reliability** in a way that a reader might otherwise miss? (If yes →
  Cross-Cutting Concerns applies.)
- Does the system have **runtime behaviour that needs verification or
  staged deployment**? (If yes → Testing & Rollout Strategy applies.)
- Can success be **measured**? (If yes → Success Criteria / Metrics applies.)
- Does the design rest on **assumptions that could change**? (If yes →
  Assumptions applies.)
- Is the **domain vocabulary** heavy enough that readers need a glossary?
  (If yes → Glossary applies.)

### Step 3 — Targeted web research

Use your `web_search` tool to gather current documentation on the project's
core frameworks, libraries, or platforms. The research is targeted, not
general:

- **Do** search for the current API surface, config format, or version
  constraints of each dependency named in `plan.md`.
- **Do** search for the official recommended architecture pattern for the
  stack (for example: "FastAPI background tasks best practice 2026",
  "Next.js 15 app router data fetching").
- **Do** cite sources inline in `docs/SPEC.md` as markdown links when a
  design decision leans on them.
- **Do not** search for general opinion pieces, listicles, or Stack Overflow
  answers older than 18 months.
- **Do not** replace authoritative documentation with a blog post.

Record each useful URL as you go; you will reference the stronger ones
from `docs/SPEC.md`.

If the user has an MCP server configured that proxies official docs (for
example `context7`), prefer it for library-specific queries over raw web
search.

### Step 4 — Draft the four mandatory sections

Draft all four, in order, from the template:

1. **Overview.** One paragraph: what the system is, what problem it
   addresses, scope. Succinct. Link to background rather than recapping.
2. **Goals & Non-Goals.** Two bulleted lists. Pull goals from `plan.md`.
   Non-goals are the things a reasonable reader might assume are in scope
   but are deliberately excluded. Omitting non-goals invites scope creep
   — they are as important as the goals.
3. **Design / Architecture Overview.** The actual design. Start with a
   high-level view, then drill into components. Include a diagram (ASCII or
   Mermaid) when relationships are non-trivial. Focus on the trade-offs
   you made, not implementation detail — copy-pasting auto-generated
   interface definitions is an anti-pattern.
4. **Open Questions.** Known unknowns with, where possible, an owner.
   If there are genuinely none at the time of writing, state so in a single
   line. Do not skip the section.

### Step 5 — Append only the optional sections that apply

Walk the classification from Step 2. For each question answered *yes*,
append the corresponding section, in this order (after Open Questions):

- **Data Model** — entities, fields, relationships. Use a table or code
  block for schema. Focus on what is relevant to the design and its
  trade-offs; do not paste an entire ORM definition.
- **API / Interface Design** — public interfaces, endpoints, or skill
  contracts. Include request/response shapes or function signatures.
  For a skills repo, this is typically the skill-loader contract and
  how SKILLs compose.
- **Key Dependencies** — external libraries, services, or frameworks the
  project relies on, with version constraints and a short rationale per
  entry.
- **Alternatives Considered** — designs that were evaluated and rejected,
  with the trade-offs that led to the decision. Per Google's design-doc
  practice, this is one of the most valuable sections; per Joel Spolsky,
  skipping it invites re-litigation.
- **Cross-Cutting Concerns** — security, privacy, observability,
  reliability. Each gets a short paragraph. If a concern is intentionally
  out of scope, state so explicitly with a reason — this is the one
  exception to the "omit empty sections" rule, because a silent omission
  reads as "not considered".
- **Testing & Rollout Strategy** — how the system will be verified and
  released. Reference the project's test runner and layout.
- **Success Criteria / Metrics** — measurable outcomes that indicate the
  project has done its job.
- **Assumptions** — design-time assumptions that could change.
- **Glossary** — domain terms defined inline.

### Step 6 — Write `docs/SPEC.md` and update progress

1. Ensure the `docs/` directory exists at the repository root. If Phase 2
   did not create it (the stack may not have needed one), create it now
   (`mkdir -p docs` or equivalent) before writing.
2. Write the assembled content to `docs/SPEC.md`. Hit the
   `target_word_count` in `document_info` (600–1200 words) as a guide,
   not a quota.
3. Update `collab_progress/` per `collab_progress/PROTOCOL.md`: create a
   new progress note summarising what was specced and which optional
   sections were included (and deliberately excluded), and prepend a
   `CHANGELOG.md` entry with the **user's detected local timezone** (see
   `collab_progress/PROTOCOL.md`).
4. Do not run `git add` or `git commit` unless the user explicitly asks.

## Hard rules

These are not suggestions.

1. **Do not add optional sections that were not selected in Step 2.** The
   template JSON contains only mandatory sections for this exact reason.
   If Data Model does not apply, the spec has no Data Model section —
   not an empty one, not one that says "N/A", not one with a placeholder.
   The single exception is Cross-Cutting Concerns (see Step 5).
2. **Do not invent content to fill a section.** If the project genuinely
   has no Open Questions, state so in one line. Better a one-line honest
   section than a fabricated list.
3. **Do not add a section just because a reference spec has it.** Google's
   design docs, `spec-kit`, Atlassian's SDD guide, and GSD all have
   different section lists. They are evidence of what is *possible*, not
   what is *required* for this project.
4. **Do not summarise this skill's workflow inside the frontmatter
   `description`.** The description is triggers-only (per the Beam
   style rules in `AGENTS.md`). Describing the workflow there causes
   agents to follow the summary instead of loading this body.
5. **Do not start writing the spec before reading `plan.md`.** The spec
   translates `plan.md` plus research into architecture — without
   `plan.md` you will invent goals.

## Rationalization table

Common agent rationalisations and the correct response.

| Rationalisation | Response |
|---|---|
| "The template looks sparse, I'll add a Data Model section to be thorough." | The template is intentionally minimal. Only add Data Model if Step 2 classified the project as having real data entities. |
| "Alternatives Considered is a best practice, I'll include it even without real alternatives." | No. An Alternatives Considered section with fabricated alternatives is worse than no section. It misleads future readers into thinking the trade-off was weighed. |
| "The user didn't give me `plan.md`, I'll proceed based on the README." | Stop. The README is too terse to drive a spec. Ask the user to run Phase 1 (`eliciting-project-goals`) first, or point you at an existing plan. |
| "The spec is short, I'll pad it by restating the README." | Length is not the goal; fidelity is. A 400-word spec that captures real trade-offs is better than a 1500-word spec that restates marketing copy. |
| "I don't know what to put in Open Questions, I'll skip the section." | Do not skip. Write one line stating there are no open questions at the time of writing. The section signals that the spec is a living document. |
| "Web research is slow, I'll rely on my training data." | The purpose of Phase 4 is to pull *current* framework behaviour. Training-data recall is the failure mode this phase exists to prevent. |

## Output checklist

Before declaring the phase complete, confirm all of the following:

- [ ] `docs/SPEC.md` exists and starts with an `Overview` H2 (or an H1
      title if `document_info` calls for one).
- [ ] All four mandatory sections from `spec-template.json` are present,
      in order.
- [ ] Every optional section that *was* added maps to a Step-2 classification
      answered *yes*.
- [ ] No section contains fabricated or placeholder content.
- [ ] Inline citations exist for any design decision that leans on a web
      source.
- [ ] A new progress note exists in `collab_progress/` and the CHANGELOG
      has a new entry at the top, using the user's detected local
      timezone per `collab_progress/PROTOCOL.md`.
