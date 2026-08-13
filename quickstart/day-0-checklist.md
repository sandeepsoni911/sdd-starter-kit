# Day 0 Checklist — First Hour on a New Project

> Everything you should do in your first hour of working on a new project to bootstrap SDD.
> Estimated time: **60 minutes**.

---

## 15 min — Install the kit into the repo

```bash
# From the project root
cp -r ~/Documents/sdd-starter-kit/.cursor .
cp ~/Documents/sdd-starter-kit/templates/AGENTS.root.md ./AGENTS.md
```

Then rename any `<PLACEHOLDER>` in `AGENTS.md` with your project's actual values. Or, for a brownfield repo, run `prompts/bootstrap-agents-md.md` to auto-draft the AGENTS.md from PR history.

## 10 min — Wire up MCPs + fill External Tools table

At minimum:

- [ ] **Ticketing MCP** (Jira / Linear / GitHub Issues / Azure Boards / etc.) — install + authenticate
- [ ] **GitHub / GitLab MCP** — for `gh pr view`-equivalent commands
- [ ] **Sequential Thinking MCP** — small, low-effort, high-value

Then **fill in the "External Tools & Integrations" table** in your project's root `AGENTS.md` (from the template). Every tool you use gets a row — ticketing, doc system, design tool, SCM host, CI/CD, cloud, observability, auth, meeting transcripts, communication, package registry, secrets. Mark each row's MCP status (`yes: <server>` or `no: manual workflow`).

Why this matters: every kit skill reads this table to know which MCP to call. Without it, agents guess.

**If any tool has no MCP:** read `docs/13-tool-substitution-guide.md` §7 (Graceful Degradation) for the manual-workflow pattern. The kit degrades — nothing blocks you.

Test: ask the AI "list my open tickets" — it should work if the ticketing MCP row is set to `yes`.

## 10 min — Set up the workspace structure

If you don't have this yet, create it at the workspace root (not the repo root):

```
<workspace>/
├── docs/
│   ├── AGENTS.md               ← 1-line: "This folder contains program docs; skills read here for context."
│   ├── waysofworking/          ← Copy this kit here for team reference
│   ├── discovery/              ← Meeting transcripts, PRDs, discovery specs
│   ├── implementation/         ← Technical designs, ADRs
│   └── zoom-recordings-transcripts/
├── repos/                      ← Your cloned codebases
└── pending-items-tracker.md    ← Empty file for now — populate as items come up
```

## 10 min — Configure the .cursor rules for your stack

`.cursor/rules/engineering/` has 11 rules. Example code inside them leans TS/React.

**If your project is TypeScript + React:** keep all 11 as-is.

**If your project is any other language (Python, Go, Java, Kotlin, Rust, C#, ...):** open `docs/10-language-adaptation-guide.md` and follow the per-language checklist. At minimum:

- [ ] Delete `no-inline-jsx-handlers.mdc` if not React
- [ ] Delete `types-in-dedicated-files.mdc` if not TypeScript
- [ ] Delete `component-decomposition.mdc` if backend-only
- [ ] Delete `templates/ui-strategy.md` if no UI
- [ ] Rewrite code examples in `runtime-input-validation.mdc`, `typed-error-classes.mdc`, `handler-thin-services-thick.mdc`, `surface-all-errors.mdc` with your language's idioms
- [ ] Rewrite the "Layer-Specific Patterns" section in `.cursor/skills/tdd-bdd-workflow/SKILL.md` with your test framework

The language adaptation guide has copy-paste-ready examples for Python (FastAPI + Pydantic), Go (Gin + validator), Java (Spring Boot + Bean Validation), Kotlin, Rust, and C#. Budget ~2 hours.

Add stack-specific rules as they emerge (e.g., `pydantic-strict-mode.mdc`, `gin-middleware-order.mdc`, `spring-transactional-boundaries.mdc`) — but only after the pattern repeats 3+ times.

## 10 min — Test the kit works

Open a new chat in Cursor:

- [ ] Ask: "What are the mandatory rules in this repo?" — should list the 5 from AGENTS.md
- [ ] Ask: "How do I create a new ticket?" — should trigger `card-edit-trigger.mdc` → `ticket-prd-builder`
- [ ] Ask: "What's the process for opening a PR?" — should reference `pr-open-trigger.mdc` → `pr-readiness-check`

If any of these fail, the AGENTS.md or rules aren't being read. Verify file paths.

## 5 min — Add yourself a tracker

Create `pending-items-tracker.md` at the workspace root with this scaffold:

```markdown
# Pending Items Tracker

## External (waiting on stakeholders)

## Internal (waiting on me)

## Team (waiting on team members)

## Architecture decisions (open)

## Resolved log
```

You'll populate this as items come in.

---

## Done — what's next

You're now at **Level 2** of the maturity model:

> Cursor with MCPs — Ticketing/knowledge MCPs wired up; status reports and tickets pulled into chat. 1.4–1.7× throughput.

To reach **Level 3** (rules + skills adopted, rituals in place), work through `quickstart/week-1-checklist.md`.
