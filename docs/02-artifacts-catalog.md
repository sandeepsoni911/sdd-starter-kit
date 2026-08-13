# Artifacts Catalog

> Every file in this kit, what it is, why it exists, and when to use it. Read this once to understand what you're inheriting.

The kit contains **55 files** across 7 categories:

- 9 orientation docs (`docs/`)
- 10 process skills (`.cursor/skills/`)
- 10 process trigger rules + 11 engineering rules (`.cursor/rules/`)
- 2 AGENTS.md templates
- 11 other templates
- 8 prompts
- 4 quickstart checklists
- 4 examples

If you drop 20% of them, the remaining 80% still work. This catalog tells you which are essential vs. nice-to-have.

---

## Legend

- **Essential** — drop this and SDD breaks. Copy as-is.
- **Recommended** — SDD works without it but you'll rebuild it in month 2 anyway. Copy on Day 1.
- **Optional** — situational; adopt when the situation arises.

---

## 1. Orientation docs (`docs/`)

| File | Category | Purpose | Read when |
|---|---|---|---|
| `README.md` | Essential | Master orientation, role-based start paths | Day 1 |
| `docs/01-sdd-playbook.md` | Essential | End-to-end SDD process | Day 1, then reference |
| `docs/02-artifacts-catalog.md` | Essential | This file — inventory | Day 1 |
| `docs/03-milestone-lifecycle.md` | Essential | Epic → Milestone → Phase → PR flow | Before starting your first milestone |
| `docs/04-the-5-layer-pattern.md` | Recommended | How to think about Cursor as an orchestration layer | Week 2 — when you're beyond autocomplete |
| `docs/05-agents-md-guide.md` | Essential | AGENTS.md deep dive | When authoring or reviewing an AGENTS.md |
| `docs/06-skills-and-rules-guide.md` | Recommended | When to write a skill vs. a rule vs. a hook | When authoring new automation |
| `docs/07-kt-guide.md` | Recommended | How to hand SDD to a new tech lead in 90 minutes | Roll-off, onboarding |
| `docs/08-gap-analysis.md` | Optional | Audit method — what was missing in the source project | Retro, kit maintenance |
| `docs/09-prompt-versioning.md` | Optional | Discipline for evolving prompts as models improve | Month 3+ |

---

## 2. Process skills (`.cursor/skills/`)

Skills are trigger-word-activated mini-agents. Each has a `SKILL.md` with frontmatter (`name`, `description`, `when_to_use`, `allowed-tools`).

| Skill | Category | What it does | Triggered by |
|---|---|---|---|
| `epic-implementation-planner` | Essential | Reads an Epic, identifies planning gaps, writes the milestone Implementation Plan markdown | User shares an epic + asks for implementation plan |
| `phase-card-generator` | Essential | Decomposes a phase into atomic tickets (one per layer), creates them in the ticketing system linked to the epic | User asks to generate cards for phase N |
| `ticket-prd-builder` | Essential | Builds or expands a ticket into a 10-section mini-PRD (Product Context, ACs, QA Notes, Engineering Context, Layer Impact, PR Plan, Validation Plan, Out Of Scope, Open Questions, Change Log) | User creates or expands a ticket |
| `layered-pr-planner` | Essential | Produces an explicit per-layer PR plan for a ticket; splits into a stack if > ~400 impl lines | User starts implementation |
| `pr-readiness-check` | Essential | Self-verification loop before opening a PR (size, ACs, tests, lint, typecheck, template, module boundaries) | Before opening a PR |
| `phase-report-writer` | Essential | Verifies phase card statuses via the ticketing MCP, writes the phase completion report | User says a phase is complete |
| `prd-sync` | Essential | Detects drift between implementation and planning artifacts; applies append-only corrections | Divergence detected during a PR |
| `agent-learning-loop` | Recommended | Consults and curates the durable-lesson index (`.cursor/LEARNINGS.md`) | Repeated mistake, or user requests a learning entry |
| `tdd-bdd-workflow` | Essential | Step-by-step TDD flow with BDD-style test names | Any feature/bug/component work |
| `lld-creation` | Recommended | Generates a Low-Level Design for a feature (layered architecture, file names, request flow) | Complex phase that needs a design pass before implementation |

**Notes:**
- The ClientName Project Alpha repo also has a `dip/` skill and a `css-cascade-overrides` skill. Both were dropped from this kit: `dip/` was superseded by `epic-implementation-planner` + `ticket-prd-builder`; `css-cascade-overrides` is stack-specific (PDS + Tailwind).
- If you don't use a ticketing MCP, the "reads via MCP" steps in each skill become "reads via `<TICKETING_SYSTEM>` in your preferred way." Skill logic is unchanged.

---

## 3. Process trigger rules (`.cursor/rules/process/`)

Rules that fire based on user intent and trigger the appropriate skill.

| Rule | Triggers | Fires when |
|---|---|---|
| `source-of-truth.mdc` | Baseline | Always applied — asserts ticket-system + repo-docs as sources of truth; append-only |
| `epic-shared-trigger.mdc` | `epic-implementation-planner` | User shares an epic + asks for a plan |
| `phase-cards-trigger.mdc` | `phase-card-generator` | User asks to generate cards for a phase |
| `card-edit-trigger.mdc` | `ticket-prd-builder` | User creates or expands a ticket |
| `pr-plan-trigger.mdc` | `layered-pr-planner` | User starts implementing a ticket |
| `pr-open-trigger.mdc` | `pr-readiness-check` | User asks if a PR is ready / is about to open |
| `phase-end-trigger.mdc` | `phase-report-writer` | User says a phase is complete |
| `divergence-trigger.mdc` | `prd-sync` | Implementation contradicts a documented decision |
| `one-layer-per-pr.mdc` | Invariant | Enforces one primary layer per PR |
| `pr-size-and-stack.mdc` | Invariant | Enforces ~400 impl line cap; describes stacking rules |
| `agent-learning-loop.mdc` | `agent-learning-loop` | Repeated mistakes trigger a learning entry |

---

## 4. Engineering rules (`.cursor/rules/engineering/`)

Tech-agnostic coding standards. Enforced by the agent on every prompt.

| Rule | What it enforces |
|---|---|
| `tdd-workflow.mdc` | Red-green-refactor; tests before implementation |
| `runtime-input-validation.mdc` | Never `JSON.parse() as Type`; validate at boundaries (Zod / AJV / Pydantic / etc.) |
| `typed-error-classes.mdc` | Custom Error classes; never `error.message.includes(...)` |
| `surface-all-errors.mdc` | Errors bubble to observability; don't swallow silently |
| `extract-duplicated-logic.mdc` | Rule of three — extract when repeated 3+ times |
| `types-in-dedicated-files.mdc` | Types live in dedicated files, not co-located ad-hoc |
| `handler-thin-services-thick.mdc` | Route handlers stay thin; business logic in services |
| `package-dependency-hygiene.mdc` | Pin versions; audit new deps; no pre-releases |
| `doc-code-consistency.mdc` | When code changes, referencing docs update too |
| `component-decomposition.mdc` | Break components down when they exceed ~200 lines or 3+ responsibilities |
| `no-inline-jsx-handlers.mdc` | Extract inline handlers to named `handleXyz` functions (React) |

**Notes on what was dropped** (ClientName-specific rules not portable):
- `api-verb-semantics.mdc` — assumed a specific REST convention
- `cors-origin-security.mdc` — assumed AWS API Gateway
- `no-hardcoded-tokens.mdc` — assumed ClientName auth flow
- `dynamo-*.mdc` — DynamoDB-specific
- `lambda-cold-start-safety.mdc` — Lambda-specific
- `tanstack-query-adoption.mdc` — assumed TanStack Query
- `react-hook-form-patterns.mdc` — assumed React Hook Form
- `use-pds-components.mdc` — ClientName Design System (PDS) specific
- `css-cascade-overrides.mdc` — PDS + Tailwind specific

**These are excellent rules for their stack — re-author them in your target project's `.cursor/rules/engineering/` as your stack solidifies.**

---

## 5. AGENTS.md templates (`templates/`)

| File | Use |
|---|---|
| `templates/AGENTS.root.md` | Root of the repo. Tech stack, commands, architecture, mandatory rules, code conventions. |
| `templates/AGENTS.module.md` | Per-module (capability, package, workspace). Module-specific commands and gotchas. |

**Placeholders to fill:** `[PROJECT_NAME]`, `[TECH_STACK_TABLE]`, `[LAYER_TAXONOMY]`, `[NX_OR_MONOREPO_CONVENTION]`, `[TICKETING_SYSTEM]`, `[DOC_SYSTEM]`.

**Rule of thumb:** an AGENTS.md that's over 300 lines needs splitting. Nested `AGENTS.md` files are read automatically by Cursor when working in that folder.

---

## 6. Spec + artifact templates (`templates/`)

| Template | Purpose | Owner |
|---|---|---|
| `templates/prd.md` | The product requirement doc | Product |
| `templates/technical-solution.md` | Milestone-level technical design (SRS) | Tech Lead |
| `templates/ui-strategy.md` | UI Component Strategy — Figma-to-code mapping | Design + Frontend Lead |
| `templates/implementation-phases.md` | Milestone broken into vertically sliced phases | Tech Lead |
| `templates/milestone-implementation-plan.md` | Milestone-level plan (lives in `capabilities/<capability>/docs/delivery/<milestone>/`) | Tech Lead |
| `templates/feature-phase.md` | Per-phase per-feature detail | Tech Lead + Dev pair |
| `templates/phase-report.md` | Written at the end of every phase | Tech Lead |
| `templates/adr.md` | Architecture Decision Record | Anyone; ratified by tech lead |
| `templates/lld.md` | Low-Level Design (layered architecture, file names, flow) | Tech Lead + Dev pair |
| `templates/traceability-matrix.md` | PRD requirement → Feature → Phase → PR → Test mapping | Delivery Lead |
| `templates/raci-roles.md` | Who owns what in SDD | Delivery Lead |

---

## 7. Prompts (`prompts/`)

Standalone reusable prompts. Each has: (1) inputs, (2) prompt text, (3) expected output structure, (4) validation checklist.

| Prompt | Purpose |
|---|---|
| `prompts/01-prd-from-transcripts.md` | Discovery transcripts + design context → PRD draft |
| `prompts/02-figma-manifest.md` | Figma exports → design manifest (screen → component map) |
| `prompts/03-technical-solution.md` | PRD + Figma manifest → Technical Solution Design draft |
| `prompts/04-ui-strategy.md` | PRD + Figma manifest + design system inventory → UI Component Strategy draft |
| `prompts/05-implementation-phases.md` | PRD + Tech Solution + UI Strategy → Implementation Phases draft |
| `prompts/bootstrap-agents-md.md` | Repo + recent PR feedback → AGENTS.md draft |
| `prompts/monday-kickoff.md` | Week-planning ritual |
| `prompts/friday-wrap.md` | Weekly status ritual |

---

## 8. Quickstart checklists (`quickstart/`)

| Checklist | Duration | When to use |
|---|---|---|
| `quickstart/day-0-checklist.md` | 1 hour | New project day 1 |
| `quickstart/week-1-checklist.md` | 5–8 hours across a week | Week 1 |
| `quickstart/first-milestone-checklist.md` | 15 steps, 2–4 weeks | First milestone |
| `quickstart/roll-off-checklist.md` | 1 day | When you leave the project |

---

## 9. Examples (`examples/`)

Sanitized real-world examples from the Project Alpha pod.

| Example | Shows |
|---|---|
| `examples/example-agents-md.md` | A real root AGENTS.md structure |
| `examples/example-implementation-plan.md` | A real milestone implementation plan (M4-audit-trail) |
| `examples/example-feature-phase.md` | A real phase feature file |
| `examples/example-phase-report.md` | A real phase retro |

---

## Skills vs. rules vs. templates vs. prompts — the mental model

- **Rules** — "always be this way" (behavior + posture). Applied on every prompt.
- **Skills** — "when trigger X happens, produce output Y." Activated by keywords.
- **Templates** — the shape of an artifact. Filled in by humans + AI collaboratively.
- **Prompts** — how to get an AI to draft the first version of an artifact.
- **AGENTS.md** — context about *this* codebase that the agent needs before any of the above make sense.

Different tools, one goal: minimize the tokens the agent burns rediscovering things.

---

## Adopting subsets — three profiles

### Profile A: "SDD lite" (Days, not weeks)
- `README.md`
- `docs/01-sdd-playbook.md`
- `templates/prd.md`, `templates/implementation-phases.md`, `templates/phase-report.md`
- `.cursor/rules/engineering/tdd-workflow.mdc`, `runtime-input-validation.mdc`, `typed-error-classes.mdc`
- Root `AGENTS.md`

**Gets you to Level 2 in 2 days. No orchestration; just spec-driven with basic quality rules.**

### Profile B: "SDD with orchestration" (Recommended)
- Everything in Profile A
- All 10 process trigger rules + `.cursor/skills/` (all 10)
- Nested `AGENTS.md` files per module
- All templates
- Quickstart checklists

**Gets you to Level 3 in a week. This is the sweet spot for most teams.**

### Profile C: "Full SDD" (Kit as-shipped)
- Everything above
- All 8 prompts
- `docs/04-the-5-layer-pattern.md` adopted (rituals, knowledge management, learning loop)
- `.cursor/BUGBOT.md` review posture set up
- `.cursor/LEARNINGS.md` curated over time

**Gets you to Level 4 in 2–3 weeks. Compound gains kick in in month 2.**
