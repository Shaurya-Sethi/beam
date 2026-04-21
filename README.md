# Beam

**Beam** is a skills package that bootstraps a new
repository for **agent-driven development (ADD)**. It is a directory of
cooperating `SKILL.md` files that any modern coding agent — Claude Code, pi,
Codex CLI, Gemini CLI, etc — can load and follow.

There is no binary, no build step, and no language runtime. The entire
plugin is Markdown and JSON.

## What it does

When you tell your coding agent _"set up this repo for agent-driven
development,"_ Beam's master skill activates and walks the agent through a
four-phase workflow:

1. **Goal elicitation.** A short Q&A dialogue with you. The agent writes a
   `plan.md` and seeds session todos for the remaining phases.
2. **Repo scaffolding.** The agent creates `collab_progress/` (with
   `PROTOCOL.md`, `CHANGELOG.md`, and a README), a minimal starter
   directory tree reasoned from your stated stack, and the project
   `README.md` from `skills/scaffolding-repo/readme-template.json`.
3. **AGENTS.md.** The agent walks
   `skills/writing-agents-md/agents_config.json` and synthesises the
   project's contributor guide from `plan.md`, then asks you for any
   additional preferences to capture.
4. **Technical spec.** The agent uses its `web_search` tool to gather
   current documentation on the project's core frameworks, then authors a
   focused `docs/SPEC.md` from
   `skills/writing-technical-spec/spec-template.json`.

The output is an _agent-legible_ repository: structure, conventions, and
intent are all captured in version-controlled artifacts before any
application code is written.

## Installation

You can install Beam directly into your agent's skills directory using either of the popular skill package managers. Both tools automatically extract only the necessary `SKILL.md` files and safely ignore the rest of the repository.

**Using Vercel's `skills` CLI:**

```bash
# Install globally for all projects
npx skills add Shaurya-Sethi/beam -g

# Or install locally for the current project
npx skills add Shaurya-Sethi/beam
```

**Using `openskills`:**

```bash
# Install universally (~/.agents/skills)
npx openskills install Shaurya-Sethi/beam --universal

# Or install locally for a specific agent
npx openskills install Shaurya-Sethi/beam
```

**Manual Installation (Fallback)**

If you prefer not to use a package manager, you can clone the repository and symlink the `skills/` directory manually:

```bash
# 1. Clone the repository
git clone https://github.com/Shaurya-Sethi/beam.git
cd beam

# 2. Symlink into your agent's skills directory
# For pi:
ln -s "$PWD/skills" ~/.pi/agent/skills/beam

# For Codex CLI / Universal:
ln -s "$PWD/skills" ~/.agents/skills/beam

# For Claude Code:
ln -s "$PWD/skills" ~/.claude/skills/beam
```

## Usage

Open your agent in any empty (or new) directory and say something like:

> _"Set up this repo for agent-driven development."_

The master skill (`using-beam`) will trigger and orchestrate the four
phases. Each phase-skill can also be invoked independently — for example,
you can ask for a fresh `docs/SPEC.md` on an existing project by invoking
the phase-4 skill directly.

## Non-goals

Beam does **not**:

- write application code or business logic
- configure or run CI/CD pipelines
- install packages or run build tools
- manage credentials, deployments, or releases

It is purely a documentation and harness scaffolding workflow. Once Beam
finishes, your normal coding agent takes over and starts building inside
the harness it just created.

## License

See [LICENSE](LICENSE).
