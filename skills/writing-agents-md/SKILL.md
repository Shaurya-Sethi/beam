---
name: writing-agents-md
description: >-
  Use when beginning Phase 3 of the Beam workflow: generate AGENTS.md for a
  new repository by populating the assets/agents_config.json template with
  project-specific content.
---

Generate a file named `AGENTS.md` in the repository root. This file is the
primary contributor guide for coding agents and human collaborators.

---

## Step 1 — Gather repository context

Run the following commands to understand the current repository state before
writing any content:

```bash
ls -R          # full directory tree
cat README.md  # project overview and setup notes (if it exists)
```

Also read any files that help clarify purpose, commands, and conventions
(e.g. `docs/SPEC.md`, `package.json`, `Makefile`, `.markdownlint.json`).

---

## Step 2 — Build `AGENTS.md` section by section

The document **must** be titled `# Repository Guidelines` and written in
Markdown. Target 200–400 words. Use `##` headings for each section below.

Process every section in the order listed. Sections have two possible modes:

- **`exact`** — copy the `exact_content` value **verbatim**, character for
  character, with no paraphrasing or reformatting.
- **`requirements`** — synthesize original content from the
  `content_requirements` guidance and the repository context you gathered
  in Step 1.

### Sections to generate

#### 1. Project Purpose & High-Level Overview
**Mode:** `requirements`

Describe the project's core purpose, target audience, and provide a
high-level overview of the project.

---

#### 2. Project Structure & Module Organization
**Mode:** `requirements`

Outline the location of source code, tests, and assets. Use the `ls -R`
output from Step 1 to produce an accurate directory tree. Annotate each
significant file or folder with a short comment describing its purpose.

---

#### 3. Build, Test, and Development Commands
**Mode:** `requirements`

List key commands for local development and explain their purpose. Draw from
`README.md`, `Makefile`, `package.json`, or equivalent files. If no build
step exists, state that explicitly.

---

#### 4. Coding Style & Naming Conventions
**Mode:** `requirements`

Specify indentation rules, language-specific preferences, and linting tools
(e.g. `.markdownlint.json` config, ESLint, Prettier). Include naming patterns
for files, directories, and identifiers where relevant.

---

#### 5. Testing Guidelines
**Mode:** `requirements`

Identify test frameworks, naming conventions for test files, and coverage
expectations. If the project uses a non-standard testing model (e.g.
TDD-for-skills), describe it clearly.

---

#### 6. Commit & Pull Request Guidelines
**Mode:** `requirements`

Summarize commit message format (style, tense, length) and PR requirements
(descriptions, links, behavior-change notes).

---

#### 7. Documentation & Knowledge Sources
**Mode:** `exact` — copy the block below **verbatim**:

```
## Documentation & Knowledge Sources

ALWAYS reference official documentation for libraries/frameworks before
implementing anything new or changing existing behavior.

### **Primary Sources**:

1. **[docs/SPEC.md](docs/SPEC.md)** for project scope, architecture, and
   non-goals before designing or implementing product behavior.
2. Official library and framework documentation on the internet. Use your
   built-in `web search` tools.
3. If all else fails, browse dependency source locally (for example through
   your package manager cache, a vendored directory, or published source
   tarballs and repository tags). Ask the user to add or update dependencies
   if needed so the relevant code is available to you.
```

---

#### 8. Collaboration & Progress Tracking Protocol
**Mode:** `exact` — copy the block below **verbatim**:

```
## Collaboration & Progress Tracking Protocol

**Before starting any new task:**
- The `collab_progress` folder exists to track and understand the current
  state, recent changes, and overall repo progress.
- Decide whether reviewing these is necessary for the given task. Then `ls`
  the folder contents to view file names and check relevant files if you so
  decide.

**After completing any significant change:**
- Follow the instructions in `collab_progress/PROTOCOL.md` to document
  your work.

**This protocol is mandatory for all contributors and must be followed for
every codebase change.**
```

---

#### 9. Full-Code Ownership & Test-Driven Development (TDD) Mandate
**Mode:** `exact` — copy the block below **verbatim**:

```
## Full-Code Ownership & Test-Driven Development (TDD) Mandate

> **You are the sole implementor for this repository.**
>
> * Treat the project as a green-field codebase:
>   Write all source files, config, and docs needed to meet requirements.
> * Follow strict TDD:
>   - Write a failing automated test for any new behavior before writing
>     production code, using this repository's standard test runner and
>     layout.
>   - Write minimal production code to pass the test.
>   - Refactor for clarity after passing.
> * Maintain strong automated test coverage on new and changed behavior; add
>   edge and corner-case tests for every feature, using the project's
>   coverage tooling when it is configured.
```

---

## Step 3 — Write the file

Write the assembled content to `AGENTS.md` in the repository root,
overwriting any prior version. Confirm the file was created successfully by
reading it back.

---

## Quality checklist

Before marking Phase 3 complete, verify every item:

- [ ] File exists at `AGENTS.md` in the repo root.
- [ ] Document title is `# Repository Guidelines`.
- [ ] All nine sections are present and in order.
- [ ] Sections 7, 8, and 9 match their `exact_content` blocks character
      for character.
- [ ] Sections 1–6 are written in the project's voice, not boilerplate.
- [ ] Word count is between 200 and 400 words (excluding exact-copy blocks).
- [ ] Markdown is valid (no broken links, no unclosed fences).
