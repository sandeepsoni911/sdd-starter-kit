# Gap Analysis — What Was Missing in the Source Project

> Written during the assembly of this kit from the Project Alpha pod's setup. Documents what was implicit / tribal / missing, and what I added to the kit to fix it.

---

## Method

1. Inventory every skill, rule, template, and doc in the Alpha `.cursor/`, `AGENTS.md` files, and `docs/waysofworking/`.
2. Trace each artifact to its role in the SDD lifecycle.
3. Note anything that felt tribal (worked but wasn't documented).
4. Note anything genuinely missing (a gap you'd hit trying to reproduce the setup).
5. Author or extract the missing pieces into the kit.

---

## What was excellent and copied wholesale

| Item | Why it's excellent | In this kit at |
|---|---|---|
| `New Ways of Working with AI.md` (the source project's SDD framework doc) | End-to-end spec-driven flow with 15-step checklist | Synthesized into `docs/01-sdd-playbook.md` |
| `ai-orchestration-playbook.md` (Sandeep's 5-layer pattern) | The mental model that scales SDD beyond "AI writes code" | Extracted into `docs/04-the-5-layer-pattern.md` |
| The 10 orchestration skills (`epic-implementation-planner` through `agent-learning-loop`) | Codify the recurring procedures of the milestone lifecycle | `.cursor/skills/` |
| The 10 process trigger rules | Wire skills to user intent | `.cursor/rules/process/` |
| The append-only-updates discipline (source-of-truth, divergence-trigger, prd-sync) | Preserves traceability across change | `.cursor/rules/process/source-of-truth.mdc` + `divergence-trigger.mdc` |
| Nested `AGENTS.md` pattern | Scoped context, no giant root file | Documented in `docs/05-agents-md-guide.md`; templates provided |
| One-layer-per-PR + ~400 impl line cap | Enforces reviewability | `.cursor/rules/process/one-layer-per-pr.mdc` + `pr-size-and-stack.mdc` |

---

## Gaps I found and fixed in this kit

### Gap 1: Reusable spec-gen prompts were buried

**Symptom:** the 5 spec-generation prompts (PRD, Figma manifest, Tech Solution, UI Strategy, Implementation Phases) lived inside Section 19 of the source project's 76 KB framework doc. Nobody who wasn't reading that doc end-to-end knew they existed.

**Fix:** extracted each into its own file under `prompts/`. Each has (1) inputs, (2) prompt text, (3) expected output structure, (4) validation checklist. Versionable.

**Files added:** `prompts/01-prd-from-transcripts.md`, `prompts/02-figma-manifest.md`, `prompts/03-technical-solution.md`, `prompts/04-ui-strategy.md`, `prompts/05-implementation-phases.md`, `prompts/bootstrap-agents-md.md`, `prompts/monday-kickoff.md`, `prompts/friday-wrap.md`.

### Gap 2: AGENTS.md was not templatized

**Symptom:** every AGENTS.md in the Alpha repo was hand-authored. The next project starts from scratch.

**Fix:** parametrized templates with `[PLACEHOLDER]` fields for `[PROJECT_NAME]`, `[TECH_STACK_TABLE]`, `[LAYER_TAXONOMY]`, etc.

**Files added:** `templates/AGENTS.root.md`, `templates/AGENTS.module.md`.

### Gap 3: No traceability matrix

**Symptom:** PRD requirement → Feature → Phase → PR → Test mapping was implicit in the delivery-tree folder structure. For audit / client sign-off, this needed to be explicit.

**Fix:** template with a matrix scaffold. Filled in as milestones progress.

**File added:** `templates/traceability-matrix.md`.

### Gap 4: No RACI / roles matrix

**Symptom:** who owns the PRD, who owns the SuperSpec, who owns the Implementation Plan, who signs off phase reports — all tribal.

**Fix:** RACI template with SDD-specific roles pre-populated.

**File added:** `templates/raci-roles.md`.

### Gap 5: No spec-quality-gate checklist

**Symptom:** teams start Phase 1 without SuperSpec sign-off, then rework in Phase 2.

**Fix:** explicit checklist in `docs/01-sdd-playbook.md` §15. If any item is unchecked, don't start Phase 1.

### Gap 6: No roll-off / KT runbook

**Symptom:** knowledge locked in the outgoing tech lead's head. This kit exists because of exactly this gap.

**Fix:** `docs/07-kt-guide.md` + `quickstart/roll-off-checklist.md`.

### Gap 7: `dip/` skill was superseded but still present

**Symptom:** the `dip/` (Deep Implementation Planning) skill in `.cursor/skills/dip/` was declared superseded by `.cursor/rules/jira-source-of-truth.mdc` but not deleted. Confusing for new joiners.

**Fix:** dropped from this kit entirely. Documented in this file so historical context isn't lost.

### Gap 8: Prompt versioning discipline missing

**Symptom:** the 5 spec-generation prompts were "v1" and expected to be re-tuned as models evolved, but there was no discipline for versioning.

**Fix:** `docs/09-prompt-versioning.md` — how to promote, retire, and change prompts.

### Gap 9: Bugbot / review harness pattern was ClientName-specific

**Symptom:** `.cursor/BUGBOT.md` in the Alpha repo has layered review posture instructions, but they reference ClientName-specific tooling.

**Fix:** generalized `.cursor/BUGBOT.md.template` — layered posture with `[PLACEHOLDER]` fields.

### Gap 10: `css-cascade-overrides` skill was stack-specific

**Symptom:** the skill assumed PDS (ClientName Design System) + Tailwind + CSS Modules. Not portable.

**Fix:** dropped from this kit. If your project has the same stack, re-author it — the pattern of "cascade-order override discipline" is generalizable but the specific rules aren't.

### Gap 11: Discovery-phase artifacts undocumented

**Symptom:** the source project's framework doc §1 mentions discovery ("record → save to `discovery/meeting-transcripts/` → draft PRD from transcripts"), but there was no template for the meeting-transcript-to-PRD flow.

**Fix:** covered in `prompts/01-prd-from-transcripts.md` with inputs, prompt, and expected output structure.

### Gap 12: No cadence for spec-drift detection

**Symptom:** `prd-sync` catches drift when triggered, but there was no scheduled review to catch drift that never triggered.

**Fix:** noted in `docs/01-sdd-playbook.md` §10 — recommend a weekly `## Change Log` review as part of milestone cadence.

### Gap 13: ClientName-flavored engineering rules

**Symptom:** ~15 of the 32 rules in `.cursor/rules/` were ClientName-specific (DynamoDB, Lambda cold start, TanStack Query, React Hook Form, PDS components, ClientName CORS policy, etc.). Excellent rules for their stack — not portable.

**Fix:** dropped from this kit. Kept only the 11 tech-agnostic engineering rules. The kit adopter re-authors stack-specific rules in their target project.

### Gap 14: Learning loop was mentioned but under-scaffolded

**Symptom:** the `agent-learning-loop` skill and `.cursor/LEARNINGS.md` existed, but the recipe for authoring a learning entry wasn't clear.

**Fix:** `.cursor/LEARNINGS.md.template` — provides a minimal scaffold and a per-entry template.

---

## Gaps I noted but did NOT fix

### Not fixed 1: Test-strategy doc missing

The source project's framework doc discusses TDD in §13, and `tdd-bdd-workflow` skill covers per-feature testing. But a *milestone-level* test strategy (integration tests, contract tests, load tests, E2E test suite ownership) was never captured. The kit doesn't fix this because it's stack-dependent.

**Recommendation for kit adopter:** at milestone kickoff, author `capabilities/<capability>/docs/delivery/<milestone>/test-strategy.md` covering unit / integration / E2E / performance / security testing.

### Not fixed 2: No CI/CD pipeline template

The Alpha setup has a CI pipeline (GitHub Actions), but nothing about it was captured as reusable. Pipeline definitions are highly stack-specific.

**Recommendation:** author a `.github/workflows/` folder in your target project with:
- Lint + typecheck on PR
- Unit tests on PR
- Full test suite on merge to main
- Deploy on tag

### Not fixed 3: Security review integration

The Alpha setup uses a ClientName-internal Bugbot for AI-assisted security review. Kit references the pattern but doesn't ship a security-review skill because the checks are org-specific.

**Recommendation:** copy the Bugbot layered posture from `.cursor/BUGBOT.md.template` and add security-specific checks per your stack.

### Not fixed 4: No API contract testing template

Contract-first API development (OpenAPI / Postman / Bruno) is heavily project-specific.

**Recommendation:** if you do contract-first, author a `contract-testing.md` template alongside `technical-solution.md`.

---

## Summary — items added vs. dropped vs. deferred

| Category | Count | Notes |
|---|---|---|
| **Copied wholesale from MT** | 21 | 10 skills + 10 process rules + 1 pattern doc |
| **Copied + sanitized (ClientName-specific removed)** | 11 | Engineering rules |
| **Dropped (superseded)** | 1 | `dip/` skill |
| **Dropped (stack-specific)** | 15 | ClientName-specific engineering rules + `css-cascade-overrides` skill |
| **Newly authored in this kit** | 22 | 9 orientation docs + 13 templates |
| **Newly extracted from Alpha source** | 8 | Standalone prompts |

**Net: 55 files in the kit. About half copied, half authored.**

---

## What this kit does NOT try to be

- **A Cursor user manual.** Cursor's own docs cover the IDE.
- **A style guide for a specific language.** Language-specific rules are stack-dependent.
- **A CI/CD template.** Pipelines are highly org-specific.
- **A monorepo template.** The kit works in any repo shape.
- **A DevOps runbook.** Operations is out of scope.
- **A hiring guide.** People management is out of scope.

The kit is opinionated about **process** (SDD), **artifacts** (specs, rules, skills, templates), and **cadence** (rituals, retros). Everything else, the kit stays out of.
