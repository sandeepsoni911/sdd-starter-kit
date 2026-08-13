# The SDD Playbook

> The end-to-end method for spec-driven, AI-assisted delivery. This is the canonical process doc. Everything else in the kit either implements a step described here or supports a role described here.

---

## Table of contents

1. [First principles](#1-first-principles)
2. [The five artifact families](#2-the-five-artifact-families)
3. [Discovery](#3-discovery)
4. [PRD creation](#4-prd-creation)
5. [The Room (design walkthrough)](#5-the-room-design-walkthrough)
6. [Bootstrap: AGENTS.md and repository standards](#6-bootstrap-agentsmd-and-repository-standards)
7. [The SuperSpec — Technical Solution + UI Strategy + Implementation Phases](#7-the-superspec)
8. [Development — phase by phase](#8-development--phase-by-phase)
9. [Decision-making during development](#9-decision-making-during-development)
10. [Change management](#10-change-management)
11. [Testing (TDD/BDD)](#11-testing)
12. [Code review + PR process](#12-code-review--pr-process)
13. [Demo + feedback loops](#13-demo--feedback-loops)
14. [Retrospectives + continuous improvement](#14-retrospectives)
15. [Spec quality gates](#15-spec-quality-gates)
16. [Anti-patterns](#16-anti-patterns)

---

## 1. First principles

**1 — We are not greenfield, even when we are.** Every project has conventions — naming, layering, design system, API patterns, security posture, logging, observability. The agent must respect them. On Day 0 of a new repo, the conventions come from the org's other repos. Discover and encode them in `AGENTS.md`.

**2 — Specs are context, not bureaucracy.** The PRD, Technical Solution, UI Strategy, and Implementation Phases are the **context window** for the AI agent. Time spent refining specs is time saved during implementation and code review.

**3 — Reuse over reinvention.** Before building anything custom, the agent must know what already exists. The UI Component Strategy and the repository standards document exist to prevent the agent from rebuilding what's already there.

**4 — Cross-functional requirements are built in, not bolted on.** Logging, observability, security, entitlements, error handling, accessibility — these are not afterthoughts. They must be in the specs so they end up in the generated code from Phase 0.

**5 — The workflow will get messy — and that's okay.** Real development involves micro-iterations, scope changes, new discoveries, and ambiguity. Understanding *why* each artifact exists — what context it provides to the agent — helps you adapt when the process breaks down.

---

## 2. The five artifact families

Everything in SDD reduces to one of five families:

| Family | Purpose | Owner | Lives in |
|---|---|---|---|
| **Product artifacts** | What we're building and why (PRD, epic, user stories, ADRs on product decisions) | Product + Tech Lead | `<TICKETING_SYSTEM>` (source of truth) + `<DOC_SYSTEM>` |
| **Design artifacts** | Visual and interaction design (Figma files, exported screens, design manifest) | Design + Dev | `<DESIGN_TOOL>` + `discovery/figma-exports/` |
| **Technical artifacts** | How we're building it (Technical Solution, UI Strategy, LLDs, ADRs, Implementation Plan, phase feature files) | Tech Lead + Dev pair | `capabilities/<capability>/docs/delivery/<milestone>/` |
| **Behavioral artifacts** | Standards the AI agent enforces (AGENTS.md files, `.cursor/rules/`, `.cursor/skills/`, hooks) | Tech Lead | `AGENTS.md` + `.cursor/` |
| **Retrospective artifacts** | What we learned (phase reports, learning-loop entries, updated AGENTS.md) | Everyone | `capabilities/<capability>/docs/delivery/<milestone>/reports/` + `.cursor/LEARNINGS.md` |

The four "spec" files people talk about (PRD, Tech Solution, UI Strategy, Implementation Phases) are all in the product/technical families. The kit templatizes all five families.

---

## 3. Discovery

**Goal:** capture what the user wants, what the constraints are, what already exists in adjacent systems, and what is unknown.

**Inputs:** stakeholder conversations, existing systems, legacy documentation, competitor analysis.

**Outputs:**
- Meeting transcripts saved to `discovery/meeting-transcripts/` (or equivalent).
- MOMs (Minutes of Meeting) — one per session, extracted via the `meeting-mom` skill (if installed) or by hand.
- Integration inventory / current-state analysis if the project touches existing systems.
- A **living discovery spec** (single markdown file) that captures scope, key findings, and open questions with owners.

**Cadence:** front-loaded in the first 2–4 weeks of a program, then reduces to episodic sessions when a new integration or unknown emerges.

**Anti-pattern:** letting discovery run for months. If you can't produce a PRD after 3–4 weeks, discovery has failed — narrow the scope.

**Kit assets:**
- `docs/03-milestone-lifecycle.md` — where discovery fits
- Templates: none directly (discovery is free-form) — but MOMs feed the PRD template
- Skills: any meeting-transcript skill (out of scope for this kit)

---

## 4. PRD creation

**Goal:** translate discovery + design into a single Product Requirement Document that the whole team signs off on.

**Structure:** see `templates/prd.md`. Mandatory sections:

1. Executive summary
2. Problem statement + user personas
3. Goals + non-goals
4. User stories with acceptance criteria
5. Functional requirements (per screen / per flow)
6. Non-functional requirements (perf, security, accessibility, observability)
7. Out-of-scope
8. Open questions with owners + due dates
9. Change log

**Generation:** use `prompts/01-prd-from-transcripts.md` to draft a first pass from meeting transcripts + design context. Iterate 2–4 rounds with product, design, tech lead.

**Ownership:** Product owns the doc; Tech Lead reviews NFRs; Design reviews screens.

**Definition of ready for sign-off:**
- Every user story has acceptance criteria.
- Every open question has an owner + due date.
- NFRs are quantified (not "fast" — "P95 < 300ms").

---

## 5. The Room (design walkthrough)

**Goal:** in ≤ 60 minutes, get product, design, and dev in one room to challenge every screen, flow, and requirement.

**Attendees:** product owner, designer, tech lead, at least one developer, delivery lead.

**Flow:**

1. Product walks the PRD (10 min).
2. Design walks the Figma flows (15 min).
3. Dev challenges assumptions (20 min): edge cases, cross-functional gaps, missing states (empty, loading, error, disabled).
4. Everyone reviews open questions and resolves what can be resolved (10 min).
5. Sign-off or fix-list captured.

**Outputs:**
- Updated PRD + Figma.
- Sign-off (or a fix-list with a re-review date).

**Anti-pattern:** treating this as a status meeting. It's a challenge session. The goal is to break the design before development does.

---

## 6. Bootstrap: AGENTS.md and repository standards

**Goal:** encode the org's / repo's standards into files the AI agent reads automatically.

### The AGENTS.md hierarchy

`AGENTS.md` files at multiple folder levels. Cursor (and most modern AI IDEs) read the nearest one to the file being edited.

- **Root `AGENTS.md`** — tech stack, mandatory rules, architecture overview, project structure, top-level conventions, commands.
- **Per-capability `AGENTS.md`** (e.g., `capabilities/<capability>/AGENTS.md`) — capability-specific context, domain vocabulary, layer dependencies, tests to run.
- **Per-module `AGENTS.md`** (e.g., `capabilities/<capability>/infrastructure/AGENTS.md`) — module-specific commands (terraform apply / npm run test / etc.), env vars, gotchas.

### Bootstrapping AGENTS.md from PR history

If the codebase is not greenfield, use `prompts/bootstrap-agents-md.md` to scan recent PR feedback and extract standards. Every "we don't do X here" comment becomes a documented convention.

### Rules and skills

- **Rules** in `.cursor/rules/*.mdc` — always-applied. Describe *how* the agent should behave. See `docs/06-skills-and-rules-guide.md`.
- **Skills** in `.cursor/skills/<name>/SKILL.md` — trigger-word activated. Describe *what* the agent should produce for a specific task.

**Rule of thumb:** if you find yourself repeating "please do X" in prompts more than 3 times, promote it to a rule.

**Kit assets:**
- `templates/AGENTS.root.md` — root template with placeholders
- `templates/AGENTS.module.md` — per-module template
- `docs/05-agents-md-guide.md` — deep dive
- `.cursor/rules/` — 21 pre-built rules ready to copy
- `.cursor/skills/` — 10 pre-built skills

---

## 7. The SuperSpec

Three technical spec files that together form the "SuperSpec" for a milestone. Each is generated by an AI prompt, then iterated 3–5 rounds with the team.

### 7a. Technical Solution Design

**What it is:** the SRS for the milestone. Architecture diagram, domain types, API contracts, mock strategy, error handling posture, security controls.

**Generation:** `prompts/03-technical-solution.md`.

**Template:** `templates/technical-solution.md`.

**Sign-off:** tech lead + a peer tech lead (external review helps).

### 7b. UI Component Strategy

**What it is:** every Figma element mapped to either an existing design-system component, an existing internal component, or a new custom component. Includes design tokens used, CSS approach, accessibility notes per element.

**Generation:** `prompts/04-ui-strategy.md`.

**Template:** `templates/ui-strategy.md`.

**Sign-off:** design + tech lead + a frontend engineer.

**Why this matters:** without it, the agent will build a `Button` component even when your design system already has one. This is the #1 source of "AI-generated but rejected in review" code.

### 7c. Implementation Phases

**What it is:** the milestone broken into vertically sliced phases, each phase = one PR (or a small stack). Each phase has:

- Goal + demonstrable outcome
- Prerequisites + exit criteria
- Layers touched (`domain → data-access → functions → feature → experience` — adjust to your architecture)
- E2E test scenarios
- Estimated implementation lines (target ~400 or fewer for the primary layer)

**Generation:** `prompts/05-implementation-phases.md`.

**Template:** `templates/implementation-phases.md`.

**Sign-off:** tech lead + product (for phase boundaries) + delivery lead (for schedule).

### 7d. Milestone-level artifacts

Per milestone, in `capabilities/<capability>/docs/delivery/<milestone>/`:

- `implementation-plan.md` — the phase overview + phase-by-phase detail
- `milestone/scope.md`, `milestone/phases.md`, `milestone/schedule.md`, `milestone/architecture.md`, `milestone/README.md`
- `features/phase-<N>/<feature-slug>.md` — per-phase per-feature detail (one file per feature per phase)
- `reports/phase-<N>.md` — post-phase retrospective (written when phase closes)

Templates for each: `templates/milestone-implementation-plan.md`, `templates/feature-phase.md`, `templates/phase-report.md`.

---

## 8. Development — phase by phase

**The loop per phase:**

```
1. Ticketing system: parent epic + one card per layer (auto-generated from phase file)
2. AI implements smallest layer first (usually domain)
3. AI writes tests alongside (TDD — red/green/refactor)
4. Dev pair reviews AI output → iterates → validates locally
5. AI runs pre-commit validation (lint, typecheck, tests)
6. Open PR (single layer, small — ~400 impl lines cap)
7. Reviewer(s) look at PR
8. Merge → repeat for next layer
9. Phase closes when all layers ship + E2E passes
10. Write phase report → update AGENTS.md if new conventions emerged
```

**One layer per PR** is the invariant. See `.cursor/rules/process/one-layer-per-pr.mdc`.

**Phase boundaries are commit boundaries.** Do not roll one phase into the next without a phase report.

**Skills that drive this loop:**
- `epic-implementation-planner` — Epic → Implementation Plan
- `phase-card-generator` — Implementation Plan phase → tickets
- `ticket-prd-builder` — expands a ticket to a mini-PRD (10 sections)
- `layered-pr-planner` — decides if a ticket is one PR or a stack
- `pr-readiness-check` — self-verification before opening a PR
- `phase-report-writer` — closes a phase, writes the retro
- `prd-sync` — detects drift between code and spec, applies append-only corrections
- `tdd-bdd-workflow` — enforces test-first
- `lld-creation` — for phases that need a low-level design first
- `agent-learning-loop` — captures durable lessons

---

## 9. Decision-making during development

**Whenever the AI hits an ambiguity, three options:**

1. **Trivial + low-risk:** AI picks with justification; dev pair reviews.
2. **Non-trivial + local:** AI presents 2 options + trade-offs + recommendation; dev pair decides.
3. **Cross-cutting / architectural:** escalate to tech lead. Record in the milestone's `architecture.md` or as an ADR (`templates/adr.md`).

**The "critical thinking over compliance" rule:** the agent must push back if a request contradicts the architecture. Encoded in the root `AGENTS.md` mandatory rules.

---

## 10. Change management

Requirements will change. The system:

1. **Living specs, not snapshot specs.** PRD, Tech Solution, UI Strategy are living. All changes are append-only via a `## Change Log` section.
2. **Divergence detection.** When implementation contradicts a spec, the `prd-sync` skill (triggered by the `divergence-trigger.mdc` rule) applies an append-only correction with a strikethrough on the obsolete sentence and an `Updated <YYYY-MM-DD> (PR #<n>): <reason>` note.
3. **No silent rewrites.** If the change is material, the spec change goes to a peer review before code changes land.

**Anti-pattern:** rewriting the PRD to match what shipped. Instead, add a change log entry explaining why the ship diverged.

---

## 11. Testing

**TDD (red-green-refactor)** is mandatory. See `.cursor/skills/tdd-bdd-workflow/SKILL.md`.

**BDD-style test names.** Use `describe('given ... when ... then ...')` structure. This makes test output readable in phase reports and PR descriptions.

**Coverage thresholds** enforced per project by the test runner config. Do not lower existing thresholds without a written trade-off decision.

**E2E scripts** live in each phase feature file (`features/phase-<N>/<feature>.md`). These are the acceptance test scenarios the AI runs against the built feature.

---

## 12. Code review + PR process

### Invariants

1. **One layer per PR.** See `.cursor/rules/process/one-layer-per-pr.mdc`.
2. **~400 implementation lines cap** (production code in the primary layer; tests/fixtures/config excluded). See `.cursor/rules/process/pr-size-and-stack.mdc`.
3. **Every PR links to a ticket + epic.**
4. **PR description follows a template** (`.github/pull_request_template.md`) — Summary, Layer Scope, Acceptance Criteria checklist, Test Plan.
5. **Layered stack rule:** for milestones spanning contract changes, PRs land in order: `domain → data-access → functions → infrastructure → feature → experience` (or your project's layer order).

### PR readiness self-check

Before opening a PR, run the `pr-readiness-check` skill. It runs a 3-iteration self-verification loop grounded in external feedback (GitHub file metadata, lint, typecheck, tests, ticket linkage, template adherence, AC coverage, module-boundary check).

### Review harness

`.cursor/BUGBOT.md.template` provides a layered review posture. The reviewer (human or AI review bot) checks different things at different layers:

- **Domain layer:** type purity, no framework leaks, business-rule correctness.
- **Data-access layer:** query safety, pagination, error handling, idempotency.
- **Functions/handlers:** thin — no business logic; validate input; typed errors; observability.
- **Feature (UI):** design system compliance, accessibility, no inline handlers, hooks correctness.
- **Experience:** demo-only — no durable product logic.

### PR feedback → AGENTS.md

Every blocking convention comment in a review becomes a documented standard. This is the ratchet that keeps AI output improving.

---

## 13. Demo + feedback loops

- **Per-phase demo** (10–15 min): dev pair shows the phase's demonstrable outcome; product + design react.
- **Feedback batched into a fix-list**, not applied ad-hoc. The fix-list becomes the next phase's scope or a divergence in the current phase.
- **Sign-off is per-milestone, not per-phase.** Phase demos are checkpoints; milestone demos are decision points.

---

## 14. Retrospectives

**Phase report.** Written at the end of every phase. Template: `templates/phase-report.md`. Sections:

- What shipped (cards closed, PRs merged, tests added)
- What worked
- What did not
- Anchors for the next phase (open items, decisions still needed)

**Milestone retro.** Written at the end of every milestone. Aggregates the phase reports + adds team-level observations.

**Learning loop.** When the same root-cause mistake repeats, or a durable operational lesson emerges, promote it into `.cursor/LEARNINGS.md` via the `agent-learning-loop` skill. The learning informs updates to AGENTS.md, rules, or skills.

---

## 15. Spec quality gates

Before phase 1 of any milestone starts, the following must be true:

- [ ] PRD signed off by product + tech lead + design.
- [ ] Technical Solution Design signed off by tech lead + peer reviewer.
- [ ] UI Component Strategy signed off by design + tech lead + a frontend engineer.
- [ ] Implementation Phases signed off by tech lead + product + delivery lead.
- [ ] Milestone `implementation-plan.md` created in the delivery folder.
- [ ] Root and per-capability AGENTS.md updated with any milestone-specific context.
- [ ] Open questions in the PRD have owners + due dates.
- [ ] NFRs are quantified.

If any of these are missing, do not start Phase 1. Start Phase 0 (spec fix-up) instead.

---

## 16. Anti-patterns

| Anti-pattern | Why it hurts | Do this instead |
|---|---|---|
| Skipping the SuperSpec ("we'll figure it out as we go") | Every decision gets litigated in PR review; AI generates code that doesn't fit | Invest 1–2 weeks up front |
| Writing one giant PR per phase | Un-reviewable, hides risk | One layer per PR; ~400 line cap |
| Refactoring adjacent code | Bloats diffs, hides intent | Encoded in `coding-behavior.mdc` |
| Silent rewrites of the PRD | Loses traceability | Append-only + change log |
| Rules for problems you don't have yet | Cognitive overhead with no payoff | Start with 4–5 rules; add when a pattern repeats 3+ times |
| Building components the design system already has | Wasted time, rejected in review | UI Component Strategy up front |
| Letting the AI pick silently between options | You inherit decisions you didn't make | Demand 2 options + trade-offs + recommendation |
| Skipping phase reports | Anchors for the next phase are lost | Phase report before starting next phase |
| One giant AGENTS.md file | Bloats context, hard to maintain | Nested AGENTS.md per module |
| Not measuring | You can't defend the throughput claim | Track PRs/week, review turnaround, AI-generated-then-rejected % |

---

## Summary: end-to-end flow

```
Discovery
   ↓
PRD  ←→  Design
   ↓
The Room (walkthrough + sign-off)
   ↓
Bootstrap AGENTS.md + install kit
   ↓
SuperSpec: Tech Solution + UI Strategy + Implementation Phases
   ↓
Milestone kicked off in delivery folder
   ↓
Phase 1: tickets → layered PRs → tests → merge → phase report
   ↓
Phase 2: (repeat)
   ↓
Milestone closes → milestone retro → learnings propagate
   ↓
Next milestone: repeat, richer AGENTS.md, sharper rules
```

Each artifact in the kit implements one of these boxes. When something feels missing, ask: *which box in the flow does this belong to?* If none, don't build it yet.
