---
name: phase-report-writer
description:
  Verifies phase ticket statuses via the ticketing system's MCP and writes the phase completion
  report markdown that anchors the next phase. Use when the user states a phase is complete, asks
  for a phase report, or asks to wrap up a phase.
when_to_use:
  User says a phase is done, asks to write a phase report, asks to close out a phase, or wants to
  anchor the next phase planning.
allowed-tools: Read Write CallMcpTool AskQuestion
---

# Phase Report Writer

Produce the phase report that closes the loop on a phase: what was built, deviations from plan,
decisions surfaced, and concrete anchors for the next phase. The report lives in the repo and is
the technical retrospective for the milestone.

## Output Path

```
capabilities/<capability>/docs/delivery/<milestone>/reports/phase-<N>.md
```

Create the `reports/` directory if it does not exist.

## Procedure

### 1. Resolve Inputs

- Capability path. Detect from open files; ask if ambiguous.
- Milestone identifier. Read the latest `docs/delivery/<milestone>/implementation-plan.md` or ask.
- Phase number being reported.
- Epic key from the Implementation Plan frontmatter.

### 2. Verify Ticket Statuses Via `<TICKETING_MCP>`

Read the "search" descriptor and call it with a query filtering by parent Epic and phase (adapt
the query language to your ticketing system):

```
"Epic Link" = <PROJECT>-<N> AND labels = "phase-<N>"
```

Or, if labels are not used, fetch all child issues of the Epic via "get issue" plus link traversal
and filter by phase reference in the description.

Capture for each ticket:

- Key
- Summary
- Status (Done, In Progress, Blocked, etc.)
- Layer Impact
- Linked PRs

If any ticket is not in a terminal/done state, alert the user and ask whether to:

- Hold the report until those tickets close.
- Document them as `Carried Over` to the next phase.
- Document them as `Deferred / Out Of Scope` for this milestone.

Do not write the report unilaterally when tickets remain open.

### 3. Read The Implementation Plan

Read the milestone Implementation Plan to capture the phase's original goal, exit criteria,
estimated ticket count, and dependencies.

### 4. Generate The Report

Create the markdown file with this structure:

```markdown
---
milestone: <M-N-short>
epic: <PROJECT>-<N>
phase: <N>
last_updated: <YYYY-MM-DD>
status: complete
summary: <one-line summary of phase outcome>
---

# Phase <N> Report -- <Phase Name>

> Epic: [<PROJECT>-<N>](<Epic URL>)
>
> [implementation-plan.md](../implementation-plan.md)

## Outcome

<One paragraph summarizing what was delivered and why it matters.>

## Tickets Delivered

| Ticket        | Summary   | Layer   | Status | PRs        |
| ------------- | --------- | ------- | ------ | ---------- |
| <PROJECT>-<N> | <summary> | <layer> | Done   | #<n>, #<n> |

## Tickets Not Closed

| Ticket        | Status   | Disposition  |
| ------------- | -------- | ------------ |
| <PROJECT>-<N> | <status> | Carried Over |

## Deviations From Plan

- <What changed vs the Implementation Plan, with link to the prd-sync entries that captured it.>

## Decisions Surfaced

- <Technical decisions discovered during implementation. Link to ADRs or LLDs if any.>

## What Worked / What Did Not

**Worked:**

- <Patterns, tools, processes that helped.>

**Did Not Work:**

- <Frictions, mistakes, items to fix in the harness or workflow.>

## Learning Candidates

- <Optional agent-process lessons to review through `.cursor/skills/agent-learning-loop/SKILL.md`.
  Use `None` when there are no durable candidates.>

## Anchors For Next Phase

- <Concrete artifacts the next phase can rely on (types, services, UI scaffolding, infra blocks).>
- <Open questions or pre-conditions the next phase planner must resolve.>

## References

- Implementation Plan: <relative link>
- Epic: <PROJECT>-<N>
- PRs: #<n>, #<n>, ...
```

### 5. Review Agent Learnings

Review `What Worked / What Did Not` for durable agent-process lessons. If any item reflects a
repeated root-cause mistake, material user correction, recurring validation failure, or durable
tooling/setup friction, read `.cursor/skills/agent-learning-loop/SKILL.md` and list it under
`Learning Candidates`.

Do not promote a learning to `active` or append to `.cursor/LEARNINGS.md` without explicit user
confirmation. Product decisions, scope changes, AC changes, and architecture decisions still use
the existing ticketing / implementation plan / ADR flows.

### 6. Write The File

Use `Write` to create the file at the path above. Do not overwrite an existing report. If a report
file already exists for the phase, treat it as authoritative and use `prd-sync` semantics (append a
clarification entry) instead of replacing it.

### 7. Update The Implementation Plan Index

If the Implementation Plan has a Phase Reports table or index, append a row pointing to the new
report. This is an append-only edit. Read the plan, preserve everything, add the row.

### 8. Confirm

Report to the user:

- Report path.
- Tickets covered, deferred, carried over.
- Anchors highlighted for the next phase.
- Learning candidates identified, if any.

## Out Of Scope

- Closing tickets. Status changes are owned by the team via the ticketing system directly.
- Generating tickets for the next phase. That is `phase-card-generator`.
- Writing ADRs or LLDs. They are created on demand and live in
  `docs/delivery/<milestone>/adr/adr-<topic>.md` or `docs/delivery/<milestone>/lld-<topic>.md`.

## Guardrails

- Do not write the report when tickets are unresolved without explicit user disposition.
- Do not overwrite existing reports.
- Read MCP tool descriptors before each `CallMcpTool` call.
- Source of truth for product remains the Epic; the report is a technical retrospective only.
- Learning candidates are advisory until the user confirms they should be recorded or promoted.
- Keep the report under 500 lines; link out for long content.
