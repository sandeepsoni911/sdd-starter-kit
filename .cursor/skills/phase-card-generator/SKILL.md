---
name: phase-card-generator
description:
  Reads the milestone Implementation Plan, decomposes a target phase into atomic tickets (one per
  layer/concern), and creates them in the ticketing system linked to the parent Epic. Use when the
  user asks to generate, create, or break down cards for a phase, or asks to start a phase.
when_to_use:
  User asks to generate tickets for a phase, create tickets for the next phase, or break a phase
  into tasks.
allowed-tools: Read CallMcpTool Write AskQuestion
context: fork
---

# Phase Card Generator

Turn a phase from the Implementation Plan into a coherent set of tickets, each linked to the Epic
and populated with the 10 mandatory PRD sections via `ticket-prd-builder`.

## Inputs

- Target milestone identifier (`M<N>-<short-name>`).
- Target phase number.
- Capability path. Detect from open files; ask if ambiguous.

## Procedure

### 1. Locate The Implementation Plan

Read `capabilities/<capability>/docs/delivery/<milestone>/implementation-plan.md`.

If missing, stop and instruct the user to run the `epic-implementation-planner` skill first by
sharing the Epic.

### 2. Decompose The Phase Into Atomic Cards

Read the target phase section. For each deliverable, propose one card per layer touched. The layer
taxonomy is your project's architecture — for example: `domain`, `data-access`, `functions`,
`infrastructure`, `feature`, `experience`.

Heuristics:

- One layer per card whenever possible. A single deliverable that crosses N layers becomes N cards
  with explicit dependencies.
- Group only when the change is too small to justify separate cards; document the rationale in the
  card's Engineering Context.
- Stay in scope. Anything outside the phase exit criteria becomes Out Of Scope.

Confirm the proposed list with the user before creating any card. Use `AskQuestion` for go/no-go.

### 3. Determine `project_key` (or equivalent identifier)

Derive from the Epic key prefix (for example `MT-368` -> `MT`). If ambiguous or missing, ask the
user. Never assume.

### 4. Create Cards Via `<TICKETING_MCP>`

Before calling `CallMcpTool`, read the descriptor for the ticketing system's "create issue" method.

For each proposed card:

- Set `issue_type` to your default work type (Story / Task / etc.).
- Pass `summary`, `description`, `project_key`.
- Pass the parent Epic linkage per the descriptor's expected shape. If the descriptor is ambiguous,
  ask before assuming.
- The `description` is built by following `ticket-prd-builder` for the 10 mandatory sections.
- After creation, capture the new ticket key.

Run creations sequentially so you can capture each new key and link tickets that depend on each
other. When ticket keys need to be recorded in the Implementation Plan, read the current plan,
preserve all existing content, and append the keys to the relevant phase section without rewriting
prior text.

### 5. Wire Dependencies

For tickets that depend on each other (e.g., `domain` before `data-access`), add the relationship
using the ticketing system's "create issue link" method (read its descriptor first). Use `Blocks` /
`Is blocked by` semantics.

### 6. Confirm Output

Report to the user:

- Ticket keys created.
- Layer mapping per ticket.
- Dependency graph.
- Phase number and milestone.

## Out Of Scope

- Editing the body of tickets beyond the initial creation. Use `ticket-prd-builder` to expand or
  refine sections later.
- Producing the technical implementation. The `layered-pr-planner` skill takes over from here.
- Creating tickets for a phase that has not been planned yet in `implementation-plan.md`.

## Guardrails

- Always read the Implementation Plan first.
- Read the MCP tool descriptor before each `CallMcpTool` call.
- Do not assume `project_key`. Derive or ask.
- Tickets are created append-only: never replace existing tickets or rewrite their description
  after creation. Future changes go through `ticket-prd-builder` (extension) or `prd-sync`
  (divergence).
- Confirm decomposition with the user before creating tickets.
