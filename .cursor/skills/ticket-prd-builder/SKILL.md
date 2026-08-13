---
name: ticket-prd-builder
description:
  Builds or expands a ticket so it serves as the PRD for the task across Product, QA, and
  Engineering. Enforces the 10 mandatory sections, append-only edits, and blocks implementation when
  material information is missing. Use when creating a ticket, expanding an existing ticket, or
  preparing a ticket for implementation.
when_to_use:
  User asks to create a ticket, fill in ticket details, expand or refine an existing ticket, or
  prepare a ticket as PRD before implementation.
allowed-tools: Read CallMcpTool AskQuestion
---

# Ticket PRD Builder

A ticket is the PRD for the task. It is read by Product, QA, and Engineering. It must contain
enough context for each audience to do their job without external lookup.

## The 10 Mandatory Sections

Every ticket description must contain these sections, in this order, with these exact headings:

```markdown
## Product Context

<Why this exists, what user value, which persona, what behavior is expected.>

## Acceptance Criteria

<Testable bullets or Given/When/Then. Each one independently verifiable by Product or QA.>

## QA Notes

<Test scenarios, edge cases, regression areas, data setup, environments.>

## Engineering Context

<Technical summary the agent needs: domain rules, contracts, services touched, constraints.>

## Layer Impact

- <layer 1>:
- <layer 2>:
- <layer N>:

(List all layers in your architecture. Mark each with what changes there, or "none".)

## PR Plan

<One PR per layer touched. Order, dependencies, stack rationale if over ~400 lines.>

## Validation Plan

<Tests added/updated by layer, lint, typecheck, manual checks, environments to verify in.>

## Out Of Scope

<What this ticket does not change. Explicit non-goals.>

## Open Questions

<Unresolved questions, blockers, items needing product or design follow-up.>

## Change Log / Clarifications

<Append-only log of changes made to this ticket after creation. Format:

- <YYYY-MM-DD> (PR #<n>): <what changed> -- <why>>
```

## Procedure

### 1. Determine The Mode

- **Create**: ticket does not exist yet. Build the description from scratch.
- **Expand**: ticket exists. Add or extend sections. Never overwrite existing content.

When called from `phase-card-generator`, mode is Create. When called directly because the user wants
to flesh out an existing ticket, mode is Expand.

### 2. Gather Inputs

For each section, gather what is needed:

- Parent Epic key. From the Implementation Plan or by reading the issue's Epic link.
- Phase. From the Implementation Plan.
- Layer impact. From the phase decomposition.
- Acceptance criteria. From product context. Ask the user using `AskQuestion` when unclear.
- QA notes. Derive from ACs plus regression areas in the affected layer.
- Engineering context. Derive from the layer/domain knowledge. Reference the nearest `AGENTS.md`.

Block implementation if any of `Product Context`, `Acceptance Criteria`, `Layer Impact`, `PR Plan`,
or `Validation Plan` is missing material information after asking. Surface the blocker to the user.

### 3. Compose The Description

Build the markdown using the 10 sections above. Tone:

- Product Context, Acceptance Criteria, QA Notes: clear, plain language, audience-friendly.
- Engineering Context, Layer Impact, PR Plan, Validation Plan: technical and specific, with file
  paths and command names.
- Out Of Scope, Open Questions: concise bullets.
- Change Log / Clarifications: leave the section heading empty on creation.

### 4. Write To Ticketing System (Create Mode)

If invoked in Create mode, the description goes into the "create issue" method (descriptor path in
your MCP setup). The `phase-card-generator` skill is responsible for the actual `CallMcpTool` call;
this skill produces the description content.

### 5. Write To Ticketing System (Expand Mode)

In Expand mode, this skill calls the ticketing system directly:

1. Read the "get issue" descriptor and call it to load the current description.
2. Identify which of the 10 sections are missing or insufficient.
3. Compose only the new content. Preserve every existing line.
4. Read the "update issue" descriptor and call it passing the full preserved description plus the
   appended/extended sections.
5. Verify after write that no prior content was lost.

When the change is a substantive update of an already-implemented section, do not overwrite the
section. Add an entry to `## Change Log / Clarifications` referencing the new context. The
divergence sync skill (`prd-sync`) handles drift after implementation.

## Tripled Audience Rule

Every ticket must answer:

- Product: What user problem are we solving and how do we know it is solved?
- QA: How do I verify the behavior, including edge cases and regressions?
- Engineering: Which layers move and what is the technical contract?

If any of the three cannot answer their question from the ticket alone, the ticket is incomplete.

## Guardrails

- Append-only on existing tickets. Never delete, shorten, or rewrite text already in the
  description.
- Read the issue and tool descriptor before any write call.
- Ask focused questions when material context is missing. Do not assume product intent.
- The ticket's `## Change Log / Clarifications` is the only mutable section after creation; even
  there, only append.
- Layer Impact must list every layer explicitly, even when the value is "none".
- PR Plan must reference the project's layer order (e.g.
  `domain -> data-access -> functions -> infrastructure -> feature -> experience`) when the ticket
  touches multiple layers.
