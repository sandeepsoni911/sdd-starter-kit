---
name: epic-implementation-planner
description:
  Reads a parent ticket (Epic), identifies planning gaps, appends clarifications back to the ticket,
  and generates a phased Implementation Plan markdown file in the repo. Use when the user shares an
  epic and asks for an implementation plan, milestone breakdown, phased delivery, or project plan.
when_to_use:
  User shared an epic + wants an implementation plan, phase breakdown, milestone plan, or delivery
  roadmap.
allowed-tools: Read CallMcpTool Write AskQuestion
context: fork
---

# Epic Implementation Planner

Read an epic, ensure it carries enough context for delivery, and produce the technical
Implementation Plan markdown that drives card generation and phase reports.

The Epic is the source of truth for product (scope, user stories, decisions, risks). The
Implementation Plan is the source of truth for technical phasing.

## Adapting to your stack

Replace throughout:

- `<TICKETING_SYSTEM>` — the ticketing platform (Jira, Linear, GitHub Issues, Azure Boards, ...)
- `<TICKETING_MCP>` — the MCP server name for that system
- `<PROJECT>-<N>` — your ticket key format (e.g. `MT-368`, `LIN-1234`, `#42`)
- `<capability>` — your unit of code ownership (capability, package, module)

The Alpha reference implementation used Jira + Atlassian MCP + Nx capability layering.

## Inputs

- Epic key or URL.
- Capability path in the repo (default: detect from open files; ask if ambiguous).

## Procedure

### 1. Read the Epic via `<TICKETING_MCP>`

Before calling `CallMcpTool`, read the tool descriptor for the ticketing system's "get issue" method
so you know the required parameters.

Call the "get issue" method with the epic key. Capture: summary, description, status, related
issues, existing acceptance criteria, decisions log, risks, user stories, links.

### 2. Identify Gaps

Cross-check the Epic against this minimum content set:

- Business outcome and target users.
- Scope and non-scope.
- User stories or capability statements.
- Product-level acceptance criteria.
- Known risks and unknowns.
- Decisions log section (`## Decisions Log`).

For every missing or vague item, prepare a focused question. Use `AskQuestion` to gather only what
materially affects the plan. Do not assume product intent.

### 3. Append Clarifications to the Epic

For each clarification confirmed with the user, update the Epic via the "update issue" method (read
the tool descriptor first). Read the Epic again right before writing, preserve the full
description, append the new content. **Never replace existing text.**

Use a `## Decisions Log` entry when the change is a decision:

```text
- <YYYY-MM-DD>: <decision> -- <reason> -- <link if any>
```

Use a `## Clarifications` section for scope or AC clarifications, appending bullets.

### 4. Decide The Milestone Identifier

Use `M<N>-<short-name>` (existing repo convention; example: `M3-additional-flows`).

- If the user provides one, use it.
- If a previous milestone exists in `capabilities/<capability>/docs/delivery/`, derive the next
  number from that.
- Otherwise, ask the user. Never assume.

### 5. Generate The Implementation Plan

Create `capabilities/<capability>/docs/delivery/<milestone>/implementation-plan.md` with this
structure:

```markdown
---
milestone: <M-N-short>
epic: <PROJECT>-<N>
last_updated: <YYYY-MM-DD>
status: draft
summary: <one-line summary of the milestone>
---

# Implementation Plan -- <Milestone Name>

> Epic: [<PROJECT>-<N>](<Epic URL>)
>
> This document is the technical source of truth for milestone phasing. The Epic is the source of
> truth for product scope and decisions. Updates to this file are append-only via
> `.cursor/skills/prd-sync/SKILL.md`.

## TL;DR

<One short paragraph: number of phases, overall direction, key dependency chain.>

## Phase Overview

| Phase | Name   | Goal      | Estimated Cards | Dependencies |
| ----- | ------ | --------- | --------------- | ------------ |
| 1     | <Name> | <Outcome> | ~<N>            | None         |
| 2     | <Name> | <Outcome> | ~<N>            | Phase 1      |

## Phase 1 -- <Name>

**Goal:** <What this phase delivers>

**Prerequisites:**

- <What must be true before starting>

**Exit Criteria:**

- <What must be true for completion>

**Expected Layers:**

- <layer names from your architecture and what each holds>

## Phase 2 -- <Name>

... same structure ...

## Open Questions

- <Questions that block planning and need product follow-up>

## Change Log

- <YYYY-MM-DD>: Initial plan derived from Epic <PROJECT>-<N>.
```

Keep the file under 500 lines. Use cross-links for long context.

### 6. Validate

Confirm with the user:

- Phase boundaries make sense for the epic scope.
- Each phase has a demonstrable outcome.
- Phase 1 is doable now (no blocking unknowns).
- Estimated card counts are reasonable (~3-9 per phase).

If anything fails validation, iterate before declaring the plan ready.

## Out Of Scope

- Generating the tickets for a phase. That is `phase-card-generator`.
- Writing per-ticket PRDs. That is `ticket-prd-builder`.
- Touching legacy docs in `docs/milestones/` or `docs/features/`.

## Guardrails

- Read before write. Always.
- Append-only on the Epic. Preserve everything.
- Ask focused questions when product context is missing. Do not assume.
- Never create `docs/milestones/` or `docs/features/` directories. They are legacy patterns.
- Do not generate per-phase feature PRD files in `docs/delivery/<milestone>/features/` until phase
  cards call for them.
