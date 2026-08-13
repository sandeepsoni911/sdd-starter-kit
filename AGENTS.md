# AGENTS.md — SDD Starter Kit

> **You are inside the SDD Starter Kit itself.** This is a portable, tech-agnostic scaffold for spec-driven, AI-assisted software delivery. This file orients any AI agent (Cursor, Claude Code, Windsurf, Cline, Aider, Continue.dev, Copilot Chat, etc.) working on or with this kit.
>
> **If you were expecting a project's `AGENTS.md`, you're in the wrong folder.** This folder is the kit, not a project. See "What this folder is NOT" below.

---

## Fast orientation (30 seconds)

- The kit contains **skills, rules, templates, prompts, and docs** — all as reusable IP for future software projects.
- Kit contents are **copied into real project folders**, not used from here directly.
- Primary human-facing orientation: [`README.md`](./README.md).
- Primary process reference: [`docs/01-sdd-playbook.md`](./docs/01-sdd-playbook.md).
- Complete file inventory + why-each-exists: [`docs/02-artifacts-catalog.md`](./docs/02-artifacts-catalog.md).

## What this folder IS

- A **starter kit** for AI-assisted delivery. Reusable across languages, domains, and tools.
- The source of truth for the **SDD (Spec-Driven Development) method**: PRD → Technical Solution → UI Strategy → Implementation Phases → phased delivery → per-phase retro.
- A collection of **75+ files** organized under 7 directories (`docs/`, `.cursor/skills/`, `.cursor/rules/`, `templates/`, `prompts/`, `quickstart/`, `examples/`).
- Meant to be forked / cloned / symlinked into new project workspaces on Day 0.

## What this folder is NOT

- **Not a project workspace.** Do not treat this folder as if it were a working codebase.
- **Not a place for real project code**, real customer data, real ticket references, gold datasets, or client-specific artifacts. Those belong in the actual project folder.
- **Not language-specific.** Language- or stack-specific patterns belong in [`docs/10-language-adaptation-guide.md`](./docs/10-language-adaptation-guide.md), not woven into the core rules and skills.
- **Not tool-specific** beyond the `.cursor/` convention. Cross-tool mappings live in [`docs/12-tool-portability.md`](./docs/12-tool-portability.md).
- **Not a garbage dump for drafts.** All content here is production-quality reference material.

---

## Two intents to route on

When someone asks you something in this folder, first decide: are they **adopting** the kit, or **maintaining** it?

### Intent 1: "Help me adopt / use / understand the kit"

The user wants to apply this kit to a real project (theirs or a hypothetical one). Route by role:

| Role | Read in this order |
|---|---|
| Tech lead, new project | `README.md` → `docs/01-sdd-playbook.md` → `quickstart/day-0-checklist.md` → `quickstart/week-1-checklist.md` |
| Tech lead, brownfield / inherited project | `README.md` → `docs/07-kt-guide.md` → `docs/02-artifacts-catalog.md` |
| Senior engineer / dev pair | `README.md` → `docs/01-sdd-playbook.md` (Development onwards) → `docs/06-skills-and-rules-guide.md` |
| Product / delivery lead | `README.md` → `docs/01-sdd-playbook.md` (Discovery, PRD, Change Management) → `templates/raci-roles.md` |
| KT recipient | `README.md` → `docs/07-kt-guide.md` |
| Non-TS/React language | `docs/10-language-adaptation-guide.md` (find your language section) |
| RAG / LLM / agent app | `docs/11-rag-and-agent-apps.md` → `templates/eval-plan.md` |
| Switching AI IDE from Cursor | `docs/12-tool-portability.md` |
| Unusual tool stack (non-Figma design, non-GitHub SCM, no MCP for ticketing, etc.) | `docs/13-tool-substitution-guide.md` |

**Never do "adoption" work inside this folder.** Direct the user to create a new project folder outside the kit and copy the relevant files into it. Example:

```bash
mkdir -p ~/Documents/projects/<new-project>
cd ~/Documents/projects/<new-project>
cp -r ~/Documents/sdd-starter-kit/.cursor .
cp ~/Documents/sdd-starter-kit/templates/AGENTS.root.md ./AGENTS.md
# Now open THIS folder as the workspace, not the kit
```

### Intent 2: "Help me maintain / improve the kit"

The user wants to update the kit itself — add a rule, retire a skill, fix a doc, capture a learning.

- Follow the **append-only** discipline: change logs at the bottom of long docs; strikethrough + Updated notation when a passage is superseded.
- Follow the **rule of three**: don't add a new rule, skill, or template until you've seen the pattern repeat 3+ times.
- Anti-bloat: **kit should stay under ~100 files.** Beyond that, retire aggressively.
- All examples must be **sanitized**. No real names, tickets, URLs, customer data, or company-internal jargon.
- New language-, stack-, or domain-specific content goes in adaptation guides (`docs/10`, `11`, `12`), not into the core rules/skills.

---

## Rules for agents editing files here

1. **Never add non-sanitized examples.** If an example needs a company name, ticket key, or URL, use `<PROJECT>-<N>`, `[COMPANY]`, `<example.com>`, or `example.com`.
2. **Never mix intents.** If someone asks "help me build feature X," they are adopting, not maintaining. Route them out of this folder.
3. **Preserve append-only discipline** on long docs. Do not rewrite; extend with change-log notation.
4. **Preserve the anti-bloat invariant.** Before adding a file, ask: "does this belong in this kit, or in one project's fork of this kit?" Kit-level content is reused across 3+ hypothetical projects.
5. **Preserve the language-agnostic core.** Adding TypeScript-specific behavior to a "core" rule? Move it to `docs/10-language-adaptation-guide.md` instead.
6. **Preserve the tool-agnostic conventions.** Use `AGENTS.md`, `.cursor/rules/*.mdc`, `.cursor/skills/<name>/SKILL.md`, `.cursor/BUGBOT.md`, `.cursor/LEARNINGS.md` — do not introduce tool-specific conventions in the kit's core.

## Structure at a glance

```
sdd-starter-kit/
├── AGENTS.md                     ← You are here.
├── README.md                     ← Human-facing orientation.
├── docs/                         13 orientation docs (01-13)
├── .cursor/
│   ├── skills/                   10 orchestration skills
│   ├── rules/process/            11 SDD process trigger rules
│   ├── rules/engineering/        11 tech-agnostic engineering rules
│   ├── BUGBOT.md.template        Layered PR review posture
│   └── LEARNINGS.md.template     Learning index scaffold
├── templates/                    14 spec + artifact templates
├── prompts/                      8 reusable spec-generation prompts
├── quickstart/                   4 checklists (Day 0 / Week 1 / First Milestone / Roll-Off)
└── examples/                     4 sanitized real-world examples
```

## When you don't know what to do

- **User's intent unclear?** Ask: "are you trying to apply this kit to a project, or improve the kit itself?"
- **File you'd want to add doesn't fit anywhere?** It's probably a project artifact, not a kit artifact. Direct out.
- **Feature request that would grow the kit substantially?** Push back. Ask if this belongs in `docs/10/11/12` adaptation guides instead of a new top-level directory.
- **Someone wants to build a RAG bot / real product here?** Redirect firmly. See `quickstart/day-0-checklist.md` for how to start a new project.

---

## Notes for AI IDEs

This file is written to be auto-loaded by Cursor, Claude Code, Windsurf, and other tools that follow the `AGENTS.md` convention.

- **Cursor** reads this file automatically for any prompt in this folder.
- **Claude Code** users: this file is also available as `CLAUDE.md` (symlinked) at the kit root for the Anthropic convention.
- **Windsurf, Cline, Aider, Continue.dev, Copilot Chat, Zed, JetBrains AI:** see `docs/12-tool-portability.md` for how the kit is consumed in each tool.

---

## Change log

- 2026-07-03 (v1): Initial kit-root AGENTS.md. Establishes intent routing (adopt vs. maintain), anti-bloat invariants, and cross-tool convention hooks.
