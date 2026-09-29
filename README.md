# skills-selcy

A collection of [Agent Skills](https://agentskills.io/) for spec-driven development, built for any AI coding agent.

## The skill

| Skill | What it does |
| --- | --- |
| `sdd-bootstrap` | Run `/sdd-bootstrap` once at project start. It interviews you in structured rounds (product, stack, architecture shape, security, error handling, testing, deployment), then batch generates the whole spec-driven development context bundle plus numbered feature specs. Safe to resume in a new chat. |

## Install

Uses [npx skills](https://github.com/vercel-labs/skills). Pick your agent:

```bash
# Claude Code (installs into .claude/skills, then restart Claude Code)
npx skills@latest add maulanaadib/skills-selcy -a claude-code

# OpenCode, Codex, and other .agents/skills readers
npx skills@latest add maulanaadib/skills-selcy -a opencode

# GitHub Copilot
npx skills@latest add maulanaadib/skills-selcy -a github-copilot

# Any other supported agent, or all of them
npx skills@latest add maulanaadib/skills-selcy -a cursor
npx skills@latest add maulanaadib/skills-selcy --all
```

Or install from the full URL:

```bash
npx skills@latest add https://github.com/maulanaadib/skills-selcy
```

List what is inside before installing:

```bash
npx skills@latest add maulanaadib/skills-selcy --list
```

## How to use it

Run `/sdd-bootstrap` once at the start of a project. It asks everything up front in batched decision panels, then writes the whole context bundle in one pass:

```
context/
  AGENTS.md                  # the read order, the entry point every later chat reads first
  product-context.md         # the why: problem, users, success metrics, non goals
  project-overview.md        # the what: product definition, goals, features, scope
  progress-tracker.md        # current phase, completed work, open questions, next steps
  architecture-context.md    # stack, system boundaries, storage model, invariants, env vars
  architecture-standards.md  # architecture style, module boundaries, when to split
  security-standards.md      # auth, authorization, validation, secrets, compliance
  error-handling.md          # error contract, logging, observability
  testing-standards.md       # test pyramid, what is tested first, definition of done
  data-model.md              # canonical entities, indexes, migration rules
  ui-context.md              # theme, colors, typography, component conventions
  code-standards.md          # implementation rules and conventions
  ai-workflow-rules.md       # development workflow, scoping rules, delivery approach
  feature-specs/
    00-project-setup.md      # scaffold, boilerplate cleanup, repo, first deploy
    NN-name.md               # one per feature
```

Then build each feature in its own new chat: name the feature spec file, the builder reads `context/AGENTS.md` and the context files, builds that one feature, updates `progress-tracker.md`, and commits.

The interview covers every dimension that a builder would otherwise invent: product framing, capabilities, go-to-market, stack, architecture shape, security, error handling, testing, UI direction, code standards, repository, deployment and CI, build approach, and feature decomposition. Each answer is saved to a resume state file, so a chat that dies mid-interview picks up at the last answered panel.

## Why this exists

The `/scope` and `/architect` style of skill is deliberately one decision at a time. That is correct for a living project, but expensive at project start, when you want the whole context bundle written once and then forgotten about. This skill front loads the interview, then batch writes.

It also closes the gaps where a builder most often hallucinates: security rules, architecture shape, the error contract, the testing contract, the data model, money and timezone handling, and the environment variable list. Each is decided once, written to a standards file, and inherited by every feature spec.

## Requirements

Any Agent Skills client: Claude Code, OpenCode, Codex, Cursor, GitHub Copilot, Gemini CLI, Windsurf, and [75 more](https://agentskills.io/clients). No runtime, no service, no tokens.

## License

MIT
