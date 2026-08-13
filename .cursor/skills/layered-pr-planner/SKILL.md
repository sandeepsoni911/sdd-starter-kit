---
name: layered-pr-planner
description:
  Reads a ticket and produces an explicit per-layer PR plan, deciding when to split into a stack of
  dependent PRs based on the ~400 implementation lines budget. Updates the ticket's PR Plan section
  in append-only fashion. Use when starting implementation of a ticket or when the PR Plan section
  is missing or stale.
when_to_use:
  User starts implementing a ticket, asks how to split the work into PRs, or asks about PR ordering
  across layers.
allowed-tools: Read CallMcpTool AskQuestion
---

# Layered PR Planner

Translate a ticket into a concrete sequence of pull requests, one per layer, stacking only when
size or dependency forces it.

## Layers And Order

Adapt to your project's layer taxonomy. Example (Nx-style capability layering):

```
domain  ->  data-access  ->  functions  ->  infrastructure  ->  feature  ->  experience
```

This is the default order for contract changes (a domain shape change cascades downstream).
Front-end-only changes can start at `feature`. Infra-only changes are isolated.

## Procedure

### 1. Read The Ticket

Read the ticketing system's "get issue" descriptor, then call it for the target ticket key. Capture:

- Layer Impact section.
- Engineering Context section.
- Acceptance Criteria.
- Existing PR Plan content if any.

### 2. Identify Touched Layers

From `Layer Impact`, list every layer marked. Default rule: one PR per touched layer. Auxiliary
edits inside another layer (barrel exports, fixtures, wiring) belong to the primary PR and must be
justified in the PR description.

### 3. Estimate Size Per Layer

Estimate implementation lines per layer based on:

- Files to create or change (from Engineering Context).
- Typical density for that layer in this codebase.
- New types, services, components, handlers, or infra blocks.

Implementation lines exclude tests, fixtures, snapshots, generated files, API test collections, and
config. The threshold is **~400 lines** for the primary layer.

If estimation is uncertain, mark the layer as TBD and ask the user during validation.

### 4. Decide PR Strategy Per Layer

For each touched layer:

- **Single PR** when estimated implementation lines are within ~400.
- **Stack of PRs** when over ~400. Split by coherent units (for example: types only, then service,
  then service tests).
- Each stacked PR must be reviewable on its own and not introduce circular dependencies.
- Stack ordering must respect downstream consumers.

### 5. Sequence Across Layers

Default sequence rules:

- A change in a producing layer must merge before a consuming layer that depends on it.
- Infrastructure that exposes a new API path must merge before the front-end that calls it.
- Tests live with the implementation in the same PR (TDD invariant).

Compute the dependency graph. If two PRs are independent and small, they can ship in parallel.

### 6. Compose The PR Plan

Build the content for the ticket's `## PR Plan` section:

```markdown
## PR Plan

| Order | PR Title (working)                        | Layer          | Est. Lines | Depends On | Stack | Notes                 |
| ----- | ----------------------------------------- | -------------- | ---------- | ---------- | ----- | --------------------- |
| 1     | feat: M<N> - P<phase>-01 -- Domain types  | domain         | ~80        | --         | no    | Adds Foo, Bar         |
| 2     | feat: M<N> - P<phase>-02 -- Service layer | data-access    | ~250       | #1         | no    | Service + tests       |
| 3     | feat: M<N> - P<phase>-03 -- Handler       | functions      | ~120       | #2         | no    | Handler + lib/response|
| 4     | feat: M<N> - P<phase>-04 -- Infra         | infrastructure | ~60        | --         | no    | Route/IaC change      |
| 5     | feat: M<N> - P<phase>-05 -- UI            | feature        | ~300       | #3, #4     | no    | Section + Aside       |

### Stacks

Stack A (UI section over ~400 lines):

- 5a feat: ... -- Hooks + service factory (~180)
- 5b feat: ... -- List view (~190)
- 5c feat: ... -- Detail panel (~140)
- Each PR depends on the previous one and is reviewable independently.
```

### 7. Append To The Ticket

Read the ticket description, preserve all existing content, append the PR Plan above into the
`## PR Plan` section. If a previous plan exists, do not overwrite it: add a new entry beneath the
existing one with a date heading (`### Updated <YYYY-MM-DD>`). Use the ticketing system's "update
issue" method after reading its descriptor.

### 8. Confirm

Report the final plan to the user with PR count, layer mapping, dependency graph, and any open
questions about size estimates.

## PR Title Convention

Follow the existing repo convention preserved in `capabilities/<capability>/AGENTS.md`:

```
type: M<N> - P<phase>-0<step> -- Description
```

`M<N>` and `P<phase>` come from the Implementation Plan. `0<step>` is the order within the phase
inside the resulting PR sequence. Use `--` (double hyphen) if that's your project's convention.

## Out Of Scope

- Validating PRs at open time. That is `pr-readiness-check`.
- Writing the actual code. That is the implementing agent following the layered plan.
- Producing the Implementation Plan. That is `epic-implementation-planner`.

## Guardrails

- One layer per PR by default.
- Estimate, do not measure. Real measurement happens in `pr-readiness-check` against an open PR.
- Append-only updates to the ticket. No rewrites.
- Stacks must be reviewable in isolation and free of circular dependency.
- TDD: tests are part of the same PR as implementation, never separate.
