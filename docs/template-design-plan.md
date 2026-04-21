# Template Design Plan: `readme-template.json` and `spec-template.json`

This document proposes how the two JSON templates used during Beam's
scaffolding and spec-writing phases should be structured going forward.
The design constraint driving every decision below is:

> The JSON templates must contain only truly universal, always-required
> sections. LLMs tend to treat every key they see in a JSON template as
> required, so optional/conditional sections must not live in the JSON.
> The SKILL.md that invokes the template is the right place to instruct
> the agent to add further project-specific sections as it judges
> necessary.

## 1. Research summary

### 1.1 README best practices

- **[makeareadme.com](https://www.makeareadme.com)** — the community's
  canonical "what goes in a README". Its default editable template has
  six sections: Name, Description, Installation, Usage, Contributing,
  License. The site explicitly says "not all of the suggestions here
  will make sense for every project" and separates the rest (Badges,
  Visuals, Support, Roadmap, Authors, Project status, FAQ, Changelog)
  as contextual suggestions, not a template.
- **[awesome-readme](https://github.com/matiassingers/awesome-readme)**
  — curated list of exemplary READMEs across very different project
  types (libraries, CLIs, internal tools, workflow repos). The only
  thread that runs through all of them is: project name, one-line
  overview, how to run it, license. Everything else (Contributing,
  Badges, Roadmap, Support, Authors, Status, Visuals, FAQ, Changelog,
  Non-goals) is project-shape-dependent.
- **Beam's own `README.md` is evidence for the same point.** It has
  Installation, Usage, Non-goals, and License — and deliberately no
  "Contributing" section. A JSON that forces Contributing would
  misrepresent projects like this one.

### 1.2 SPEC best practices

- **[Design Docs at Google (industrialempathy.com)](https://www.industrialempathy.com/posts/design-docs-at-google/)**
  — canonical top-level sections are Context & Scope, Goals &
  Non-Goals, The Actual Design, Alternatives Considered, Cross-Cutting
  Concerns. Google explicitly calls the common sub-sections (System-
  Context Diagram, APIs, Data Storage, Code/Pseudo-code) "best
  practices for a large percentage of design docs" — not universal.
- **Joel Spolsky, Painless Functional Specifications** — argues
  against rigid templates altogether. His non-negotiables are
  Disclaimer/Author, Scenarios, and Nongoals.
- **[GitHub spec-kit `spec-template.md`](https://github.com/github/spec-kit/blob/main/templates/spec-template.md)**
  — the most adopted spec-driven-development skeleton in 2026. Only
  three blocks are marked `*(mandatory)*`: User Scenarios & Testing,
  Requirements, and Success Criteria. Notably, `Key Entities` is
  tagged `*(include if feature involves data)*` — the exact conditional
  pattern this plan is trying to keep *out* of our JSON.
- **Atlassian** and the **DocuWriter 2026 SDD guide** — both split
  the sections into "universal" (intro/overview, goals/non-goals,
  architecture) and "project-type-specific" (Data Design, UI, Security,
  Deployment).

### 1.3 Universal-vs-project-specific reasoning

- **README.** A README that cannot state project name, what it is,
  how to run it, and its license is broken. Contributing, Roadmap,
  Support, Badges, Visuals, FAQ, Changelog are nice-to-haves whose
  relevance depends on audience and project maturity.
- **SPEC.** A spec that cannot state what the system is, what it's
  for, what it's explicitly *not* for, and how the design works is not
  a spec. Data Model, API, Alternatives, Security, Deployment are
  entirely dependent on whether the thing being specified has data,
  APIs, real trade-offs, a sensitive surface, or a runtime footprint.
  For a skills/workflow repo like Beam, "Data Model" is nonsense and
  forcing it produces a padded section the agent will invent content
  for.

## 2. Proposed minimal `template_structure` for `readme-template.json`

Five sections. Every project of any type needs all five.

| # | Section | Justification |
|---|---------|---------------|
| 1 | **Project name & tagline** | H1 + single-sentence identity. Present in every README on makeareadme.com, awesome-readme, and Beam's own. |
| 2 | **Overview** | 2–4 sentences describing what the project does, the problem it solves, the primary audience. Universal; without it the reader can't decide whether to keep reading. |
| 3 | **Getting Started** | Minimal prerequisites plus the fastest path to a working setup. The *shape* varies per project (pip / npm / docker / clone-and-symlink) but the *purpose* is universal: every project must answer "how do I run it". |
| 4 | **Usage** | Smallest demonstration of actually invoking the thing — one code block or one-line example. Universal in purpose; missing usage is the most common failure mode in weak READMEs. |
| 5 | **License** | One-line license statement + link to `LICENSE`. Non-negotiable for any shared repo. |

**Dropped from JSON vs. current template:** *Contributing*. It is
project-shape-dependent (internal repos, personal projects, and
research prototypes routinely omit it). Beam's own README omits it.
Moved to SKILL.md guidance.

## 3. Proposed minimal `template_structure` for `spec-template.json`

Four sections. Every technical spec of any kind needs all four.

| # | Section | Justification |
|---|---------|---------------|
| 1 | **Overview** | One paragraph covering context, scope, and why the system exists. Merges Google's "Context & Scope" with a short scope line. Universal; cited by every source. |
| 2 | **Goals & Non-Goals** | Bulleted goals followed by bulleted non-goals. The single most-cited universal section across Google, Joel Spolsky, spec-kit, Atlassian, and BMAD. Non-goals are what prevent scope creep; they must stay. |
| 3 | **Design / Architecture Overview** | The "actual design". Universal because without it there is no spec. Format flexes to project (diagram, prose, or component breakdown) but the section itself is always required. |
| 4 | **Open Questions** | Known unknowns plus who resolves them. Treats the spec as a living document (per Google). Universal because every design at creation time has at least one unresolved decision — or a short statement that there are none. |

**Dropped from JSON vs. current template:**

- **Data Model** — only meaningful for systems that store, transform,
  or transmit structured data with real entities. Useless for a
  docs-only or workflow/skills repo.
- **API / Interface Design** — only meaningful for systems with a
  programmatic surface (HTTP, RPC, SDK, CLI, skill contract).
- **Key Dependencies** — only meaningful for projects with non-trivial
  runtime or service dependencies.

All three move to SKILL.md guidance as conditional additions.

**Intentionally not added to JSON:** *Alternatives Considered*. Google
calls it one of the most valuable sections, but it is only meaningful
when real trade-offs were made. Forcing it on projects whose design
space had no meaningful branches produces invented content. Moved to
SKILL.md guidance.

## 4. Proposed SKILL.md guidance

The SKILL.md bodies are not written in this session. The *content*
below is the substance the authored SKILL.md must convey.

### 4.1 `skills/scaffolding-repo/SKILL.md` — README extension guidance

The SKILL.md must tell the agent: the JSON template is a *minimum*,
not a ceiling. After filling the five mandatory sections, evaluate
each of the following and add *only* those that truly apply, using
what was learned in phase 1 (goal elicitation):

- **Prerequisites / Requirements** — if the project requires specific
  runtime versions, OS, hardware, or accounts.
- **Configuration** — if setup involves environment variables, config
  files, or credentials.
- **Features** or **Why <project>** — if positioning vs. alternatives
  matters for adoption.
- **Contributing** — if the repo accepts external contributions, has
  a CLA/DCO, or points to a `CONTRIBUTING.md`.
- **Non-goals** — if the project's scope is narrow and likely to
  attract misuse.
- **Roadmap** — if there is a meaningful sequence of planned releases.
- **Support / Community** — if there is an issue tracker, chat, or
  mailing list.
- **Badges** and **Visuals** — if CI/coverage badges exist or the UI /
  CLI output is worth showing.
- **Authors & Acknowledgments**, **Project status**, **FAQ**,
  **Changelog** — per project maturity.

**Negative instruction the SKILL.md must include verbatim (or
equivalent):** "Do not add a section just because a similar project
has it. Add it only when the project truly has content for it. A stub
section is worse than no section."

### 4.2 `skills/writing-technical-spec/SKILL.md` — SPEC extension guidance

The SKILL.md must tell the agent: the JSON template is a *minimum*.
After filling the four mandatory sections, evaluate each of the
following and add *only* those that apply:

- **Data Model** — add if the system stores, transforms, or transmits
  structured data with meaningful entities/relationships. Omit for
  pure workflow, skills, or docs repos.
- **API / Interface Design** — add if the system exposes a
  programmatic surface (HTTP, RPC, SDK, CLI, skill contract). For a
  skills repo, this may be the skill-loader contract.
- **Key Dependencies** — add if the project has non-trivial runtime,
  library, or service dependencies with version constraints. Omit for
  docs-only repos.
- **Alternatives Considered** — add if real design trade-offs exist.
  Per Google, one of the most valuable sections; per Joel, skipping
  it invites re-litigation. Omit if the design space had no meaningful
  branches.
- **Cross-Cutting Concerns** (Security / Privacy / Observability /
  Reliability) — add when any apply. Per Google, these sections exist
  to ensure the concerns are *always* considered; note them even to
  say "N/A for this project, because …".
- **Testing & Rollout Strategy** — add if the project has runtime
  behaviour that needs verification or staged deployment.
- **Success Criteria / Metrics** — add if success is measurable
  (pattern borrowed from spec-kit).
- **Assumptions** — add if the design rests on assumptions that could
  change.
- **Glossary** — add if domain vocabulary is heavy.

**Negative instruction the SKILL.md must include verbatim (or
equivalent):** "Do not invent content for a section. If Data Model is
empty, omit the section entirely; do not write 'N/A'. The one
exception is Cross-Cutting Concerns, where an explicit 'Security: not
applicable because …' is valuable."

## 5. Recommendation on the pattern

**Keep the current pattern: a minimal JSON `template_structure` plus
SKILL.md guidance.** It is the right pattern given the design
constraint.

Reasons:

- JSON `template_structure` is a strong signal to LLMs that every key
  is mandatory. Removing optional sections from the JSON entirely is
  the most reliable way to stop the agent from forcing empty
  sections. This is precisely the failure mode we are solving for.
- SKILL.md is the right home for "when to add more" because SKILL.md
  is instruction-shaped (procedural), whereas JSON is structure-shaped
  (declarative). Agents already treat SKILL.md as a set of heuristics
  rather than a checklist.

Alternatives considered and rejected:

- **Single markdown template with `<!-- if: … -->` comments.**
  Rejected. LLMs routinely strip HTML comments or, worse, leave the
  sections in anyway because the surrounding markdown structure is
  still visible. Weaker enforcement than not showing the section at
  all.
- **JSON with a `mode: "optional"` field.** Rejected. Adds a third
  mode on top of `requirements` and `exact`; in practice most agents
  default to filling in "optional" content because the key is still
  present.
- **Two separate JSONs (core + extended).** Rejected for the same
  reason as above. If the agent can see the extended template, it
  will fill it.

**Future refinement (flagged, not part of this task).** The current
schema has `mode: "requirements"` and `mode: "exact"`. If a future
need for conditional structure ever arises, the right move is a third
`mode: "conditional"` with a short natural-language `add_when`
predicate. This is speculative; for now, keep the JSONs minimal.

## 6. Follow-up todos (for subsequent implementation sessions)

These are *not* executed in this session. They are recorded here as
the natural next actions that flow from this plan.

1. **Trim `skills/scaffolding-repo/readme-template.json`** —
   `template_structure` to the five mandatory sections listed in §2.
   Do not change `document_info`.
2. **Trim `skills/writing-technical-spec/spec-template.json`** —
   `template_structure` to the four mandatory sections listed in §3.
   Do not change `document_info`.
3. **Write the full body of `skills/scaffolding-repo/SKILL.md`** —
   phase-2 workflow (create `collab_progress/`, minimal starter
   structure, generate `README.md` from the trimmed JSON) plus the
   README-extension guidance in §4.1.
4. **Write the full body of `skills/writing-technical-spec/SKILL.md`**
   — phase-4 workflow (web research using `web_search`, author
   `docs/SPEC.md` from the trimmed JSON) plus the SPEC-extension
   guidance in §4.2. The SPEC skill is currently a stub and needs
   authoring in its own right, not just consumption of the template.
5. **RED/GREEN-baseline each skill change** per the testing
   discipline in `AGENTS.md`: run a fresh subagent session without
   the change, record the failures, apply the change, re-run and
   verify.

## 7. Sources

- https://www.makeareadme.com
- https://github.com/matiassingers/awesome-readme
- https://www.industrialempathy.com/posts/design-docs-at-google/
- https://www.joelonsoftware.com/2000/10/03/painless-functional-specifications-part-2-whats-a-spec/
- https://github.com/github/spec-kit
- https://github.com/gsd-build/get-shit-done
- https://www.atlassian.com/work-management/knowledge-sharing/documentation/software-design-document
- https://www.docuwriter.ai/posts/sample-software-design-document
