# AGENTS.md Guide

> `AGENTS.md` is the single most-underused file in AI-assisted development. This guide explains what it is, why nested files matter, what belongs at each level, and how to author one that actually works.

---

## What AGENTS.md is

`AGENTS.md` is a plain markdown file that AI coding IDEs (Cursor, and increasingly others) read **automatically** when working in a folder. It is the agent's *baseline context* for every file in that folder and its descendants.

Think of it as a `.editorconfig` for AI agents — except much richer.

**Cursor's behavior:** it reads the nearest `AGENTS.md` walking up from the file being edited. Multiple files stack; the deepest one takes precedence for overlapping concerns.

---

## Why nested AGENTS.md files matter

A monorepo with 6 capabilities and 30 modules can't fit its full context in one file. Splitting concerns by scope:

- The root `AGENTS.md` says "we are a Node.js monorepo using [X] for CI, [Y] for tests, [Z] for infra."
- A capability's `AGENTS.md` says "this capability's domain vocabulary is [A, B, C]; its layer taxonomy is domain → data-access → functions → feature → experience."
- An `infrastructure/AGENTS.md` says "run `terraform fmt` before every push; env vars for local dev are [D, E]."

When the agent edits `capabilities/foo/infrastructure/main.tf`, it reads all three, in order. It doesn't need to re-derive that this is a monorepo or that the capability uses layered architecture — those are baseline.

---

## What belongs at each level

### Root `AGENTS.md`

| Section | What to include |
|---|---|
| **Tech Stack** | Table: layer → technology + version. One row per layer. |
| **Setup** | Commands to bootstrap the repo (`nvm use`, `npm install`, etc.) |
| **Commands** | Most-used dev commands (`npm start`, `npm test`, `npm run lint`, per-project targets) |
| **Mandatory Rules** | 3–7 non-negotiables (TDD; no destructive commands; no git commands; think before you code; critical thinking over compliance) |
| **Architecture** | The layered model, dependency direction, project naming conventions |
| **Project Tags / Module Boundaries** | If you use tag-based module boundaries (Nx, similar), document the tag conventions |
| **Path Aliases** | TypeScript path aliases or equivalent |
| **Project Structure** | ASCII tree of top-level folders |
| **Code Conventions** | TypeScript style, imports, components, testing, styling |
| **Code Quality** | ESLint / linter customizations, quality gates, coverage thresholds |
| **Harness Glossary** | Terms specific to your program (e.g., "experience harness", "PR readiness harness", "quality gate harness") |

**Length target:** 150–350 lines. If you exceed 400, some of it belongs in a nested `AGENTS.md`.

### Per-capability `AGENTS.md`

| Section | What to include |
|---|---|
| **Capability overview** | What this capability does |
| **Domain vocabulary** | Key terms and their definitions |
| **Layer taxonomy** | This capability's layer names (if it deviates from the root standard) |
| **Local commands** | Any capability-specific commands |
| **Notes on gotchas** | Known tricky spots |

**Length target:** 50–150 lines.

### Per-module `AGENTS.md` (e.g., `infrastructure/`)

| Section | What to include |
|---|---|
| **Module purpose** | Why this module exists |
| **Local commands** | Module-specific commands (`terraform apply`, `docker compose up`, etc.) |
| **Env vars** | Required environment variables for local dev |
| **Deployment notes** | If different from the standard |
| **Gotchas** | e.g., "always run `terraform fmt` before commit" |

**Length target:** 30–80 lines.

---

## The bootstrapping problem

**Greenfield:** author the root `AGENTS.md` from scratch. Use `templates/AGENTS.root.md` in this kit as the starting point.

**Brownfield:** run `prompts/bootstrap-agents-md.md`. The prompt scans the codebase and recent PR feedback, extracts standards from PR blocking comments, and drafts a first-pass AGENTS.md. Iterate 2–3 rounds.

**Every blocking convention comment in a PR review becomes a documented standard.** This is the ratchet that keeps AI output improving over time.

---

## Anti-patterns

| Anti-pattern | Why it hurts | Do this instead |
|---|---|---|
| One giant AGENTS.md (600+ lines) | Bloats context; agent can't find what it needs | Split into nested files |
| AGENTS.md that describes the business | Wastes context on things that don't affect code | Business context goes in the PRD; AGENTS.md is for code + conventions |
| AGENTS.md not updated after PR feedback | Same conventions re-litigated in every PR | Every blocking comment in a review → new line in AGENTS.md |
| Copy-pasting AGENTS.md from another repo without editing | Wrong stack, wrong conventions, wrong layer names | Fill placeholders; delete what doesn't apply |
| Rules and skills duplicated inside AGENTS.md | Redundancy; drift | AGENTS.md points to rules and skills; rules and skills stand on their own |
| Fictional examples in AGENTS.md | Agent generates code based on fictional patterns | Real examples with real paths |

---

## How Cursor reads AGENTS.md

- Read automatically when the user opens a file in that folder or a descendant.
- Contents included in the agent's system context.
- Multiple AGENTS.md files stack — root, then capability, then module.
- The `nearest AGENTS.md` invariant is a *convention*, not a hard rule — some IDEs treat each file as additive.

**For maximum portability:** treat every AGENTS.md as *additive* rather than override-based. Nested files should refine, not contradict, ancestor files.

---

## Relationship to rules and skills

| Artifact | Scope | Read | Fires |
|---|---|---|---|
| `AGENTS.md` | Folder + descendants | Automatic on any prompt in that folder | Provides baseline context |
| `.cursor/rules/*.mdc` | Whole repo | Automatic on every prompt (subject to rule's `alwaysApply` or globs) | Applies posture / behavior |
| `.cursor/skills/<name>/SKILL.md` | Whole repo | Only when triggered | Produces a specific artifact |
| `.cursor/hooks/*.sh` | Whole repo | Automatic on session events | Runs a shell command |

**Rule of thumb:**
- Facts about the code → AGENTS.md
- How the agent should behave → rules
- What the agent should produce for a specific task → skills

---

## Sample: root AGENTS.md skeleton

See `templates/AGENTS.root.md` for the full template with placeholder guidance. Skeleton:

```markdown
# AGENTS.md

Root-level instructions for AI coding agents working on this repository.

> Each [module|capability] has its own `AGENTS.md` with scoped context.

## Tech Stack
| Layer | Technology |
|---|---|
| ... | ... |

## Setup
```bash
...
```

## Commands
```bash
...
```

## Mandatory Rules
1. Test-Driven Development
2. No Git Commands
3. No Destructive Commands
4. Think Before You Code
5. Critical Thinking Over Compliance

## Architecture
[Diagram + layer table]

## Project Structure
[ASCII tree]

## Code Conventions
[Imports, components, testing, styling]

## Code Quality
[ESLint customizations, quality gates]

## Harness Glossary
[Program-specific terms]
```

---

## When to update

| Trigger | Update what |
|---|---|
| PR blocked on a convention comment | Root or capability AGENTS.md — add the convention |
| New tech added (new library, new pattern) | Root Tech Stack table |
| New layer added / renamed | Root Architecture section + affected capability files |
| New module added | Create module-level AGENTS.md |
| Recurring "the AI didn't know X" moment | Add X to the relevant AGENTS.md |

**Cadence:** review at the end of each milestone as part of the retro. If AGENTS.md hasn't been updated in 4 weeks, either the code is perfectly self-explanatory (unlikely) or you're leaving throughput on the table.
