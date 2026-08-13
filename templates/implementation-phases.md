# Implementation Phases — [MILESTONE NAME]

**Status:** [Draft | In Review | Approved]
**Owner (Tech Lead):** [Name]
**Product reviewer:** [Name]
**Last updated:** YYYY-MM-DD
**Version:** 1

Related: [PRD](../prd.md) | [Technical Solution](./technical-solution.md) | [UI Strategy](./ui-strategy.md)

---

## Purpose

Break the milestone into vertically sliced phases. Each phase:

- Has a **demonstrable outcome** (something you can show).
- Cuts through all necessary layers (small vertical slice, not a horizontal layer sweep).
- Fits in one PR or a coherent stack (~400 impl lines per PR).
- Has explicit exit criteria + E2E scenarios.

---

## Phase overview

| Phase | Name | Demonstrable outcome | Est. cards | Depends on |
|---|---|---|---|---|
| 1 | [Foundation — types + basic scaffolding] | Types exist; unit tests green | ~3 | — |
| 2 | [First feature end-to-end] | User can [action] | ~5 | Phase 1 |
| 3 | [Second feature] | User can [action] | ~5 | Phase 2 |
| 4 | [Polish + edge cases] | Empty/error/loading states; a11y | ~3 | Phase 3 |

---

## Phase 1 — [Name]

**Goal:** [One sentence]

**Prerequisites:**
- [What must be true before starting]

**Exit criteria:**
- [ ] [Testable outcome 1]
- [ ] [Testable outcome 2]

**Layers touched:**
- `domain`: [what changes]
- `data-access`: [what changes]
- `functions`: [what changes]

**E2E test scenarios:**

1. **Scenario:** [Happy path]
   - Given: ...
   - When: ...
   - Then: ...

2. **Scenario:** [Error path]
   - Given: ...
   - When: ...
   - Then: ...

**Estimated cards:**
- Card 1.1: Domain types
- Card 1.2: Data-access repository
- Card 1.3: Handler
- Card 1.N: [layer N]

**Estimated impl lines (primary layer):** [~N]

---

## Phase 2 — [Name]

... same structure ...

---

## Cross-phase concerns

**Observability:** [Added at which phase?]
**Feature flag:** [Introduced at which phase?]
**Migration:** [If applicable, when?]

---

## Anchors (post-milestone)

At milestone close, the following becomes stable for future milestones:

- [Type A is finalized]
- [Endpoint B is production]
- [Component C is reusable]

---

## Change log

- YYYY-MM-DD (v1): Initial draft
