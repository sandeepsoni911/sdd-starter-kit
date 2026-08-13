# Prompt 5 — Generate the Implementation Phases

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** break a milestone into vertically sliced phases, each phase = one PR or a coherent stack.

## Inputs

- PRD (`@docs/discovery/prd.md`)
- Technical Solution Design (`@docs/technical-solution.md`)
- UI Component Strategy (`@docs/ui-strategy.md`)
- Root `AGENTS.md`

## Prompt

```
You are generating the Implementation Phases plan.

Inputs:
- PRD: @docs/discovery/prd.md
- Technical Solution: @docs/technical-solution.md
- UI Strategy: @docs/ui-strategy.md
- Tech stack + layers: @AGENTS.md

Use the template at @templates/implementation-phases.md.

Your job:
1. Break the milestone into **vertically sliced** phases. Each phase must have a *demonstrable outcome* — something you can show to product/design.
2. Aim for ~3-6 phases. If you have more than 8, split into sub-milestones.
3. For each phase:
   - Name it
   - Describe the demonstrable outcome (one sentence)
   - List prerequisites (what must be true before starting)
   - List exit criteria (testable outcomes for completion)
   - List which layers this phase touches
   - Estimate cards (~3-9 per phase)
   - Estimate implementation lines in the primary layer (~400 cap per PR)
   - Write E2E test scenarios (Given/When/Then)
4. Sequence the phases. Phase 1 is the foundation (types + basic scaffolding). Later phases build on it.
5. Identify cross-phase concerns: when is observability added? When is the feature flag introduced? When is migration performed?

Non-negotiables:
- Vertically sliced, not horizontal. A phase that only touches domain is a red flag — it should also touch data-access and something visible.
- Every phase has a demonstrable outcome or it's not a phase.
- No phase over ~800 total impl lines. Split.
- E2E scenarios must include the error path, not just happy path.

Output: a complete Implementation Phases markdown per @templates/implementation-phases.md.
```

## Expected output structure

Matches `@templates/implementation-phases.md`.

## Validation checklist

- [ ] Every phase has a demonstrable outcome
- [ ] Phase count is 3–8
- [ ] Every phase has E2E scenarios (happy + error path)
- [ ] Layer touch is vertically sliced
- [ ] Implementation lines estimated per phase
- [ ] Cross-phase concerns listed

## Known failure modes

- **Horizontally sliced phases (all-domain, then all-data-access, then all-UI).** Mitigate by requiring "demonstrable outcome" per phase.
- **Missing error-path E2E scenarios.** Mitigate by explicit requirement.
- **Over-large phases.** Mitigate by ~800 line cap.

## Change log

- 2026-07-XX (v1): Initial.
