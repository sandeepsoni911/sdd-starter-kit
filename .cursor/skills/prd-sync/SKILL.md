---
name: prd-sync
description:
  Detects divergence between actual implementation and the planning artifacts (Implementation Plan
  markdown, Ticket Change Log, Epic Decisions Log) and applies append-only corrections to the
  artifact that drifted. Use when an implementation choice changes a documented decision, scope,
  layer impact, or acceptance criterion in any of the three artifacts.
when_to_use:
  Implementation reality diverges from the Implementation Plan, ticket, or Epic decisions and needs
  an append-only correction.
allowed-tools: Read Write CallMcpTool AskQuestion
---

# PRD Sync (Divergence Append-Only)

Synchronize the planning artifacts when implementation reality diverges from what was planned.
Updates are always append-only: the original record stays visible; new entries explain what changed
and why.

## Triggers

Apply this skill when, during or after implementation, you notice that:

- The chosen technical approach contradicts a phase entry in
  `capabilities/<capability>/docs/delivery/<milestone>/implementation-plan.md`.
- The behavior, layer impact, validation plan, or acceptance criteria of a ticket no longer match
  what was implemented.
- A product decision recorded in the Epic was overridden, refined, or replaced.

If none of the three artifacts is affected, no sync is needed.

## Required Reading Before Editing

| Artifact                     | Read first                                                                                     |
| ---------------------------- | ---------------------------------------------------------------------------------------------- |
| Implementation Plan markdown | `Read` the current `implementation-plan.md`                                                    |
| Ticket                       | `CallMcpTool` with "get issue" after reading the ticketing MCP descriptor                      |
| Epic                         | `CallMcpTool` with "get issue" after reading the ticketing MCP descriptor                      |

Always load the current state of the affected artifact before editing. Never compose updates from
memory.

## Update Procedures

### Implementation Plan (`docs/delivery/<milestone>/implementation-plan.md`)

Apply an in-line additive correction:

```markdown
~~Original sentence or bullet that no longer matches reality.~~ **Updated <YYYY-MM-DD> (PR #<n>):**
What was actually implemented and why the plan changed.
```

Only the affected sentence is struck through. Surrounding context stays intact.

### Ticket

Read the issue, preserve its description, and append a new line in the `## Change Log /
Clarifications` section. Format:

```text
- <YYYY-MM-DD> (PR #<n>): <what changed> -- <why>
```

If the section does not exist on the ticket yet, add it at the bottom of the description and write
the first entry inside it. Never edit other sections of the ticket.

When calling the ticketing system's "update issue" method, before the call read its descriptor and
pass the full preserved description plus the new appended entry. The tool typically replaces fields
wholesale, so the read-modify-write must keep all existing content.

### Epic

Read the Epic, preserve its description, and append a new line in the `## Decisions Log` section.
Create the section at the bottom of the description if it does not exist. Format:

```text
- <YYYY-MM-DD>: <decision> -- <reason> -- <link to PR or ticket>
```

Same read-modify-write rule as the ticket: load full description, keep everything, append the
entry.

## Guardrails

- Append-only. Never delete, rewrite, or shorten existing content in any of the three artifacts.
- One divergence per execution. If multiple changes happened, run the skill once per change so each
  entry stays traceable.
- Ambiguity stops the skill. If the divergence might be a refactor that does not alter behavior,
  ask the user before writing. Use `AskQuestion`.
- Never touch legacy docs (e.g., `capabilities/<capability>/docs/milestones/` or
  `capabilities/<capability>/docs/features/` if your repo used those pre-SDD).
- Always include the date and a traceable link (PR number, commit SHA, ticket key, or both).
- The reason field is mandatory. "Why" matters more than "what" for future readers.

## Workflow Checklist

```
PRD Sync:
- [ ] Identify which artifact diverged (Implementation Plan / Ticket / Epic)
- [ ] Read the current state of that artifact
- [ ] Confirm the divergence is real (ask the user if ambiguous)
- [ ] Compose the append-only entry with date, what, why, link
- [ ] Apply the update preserving all existing content
- [ ] Verify after write that no prior content was lost
```
