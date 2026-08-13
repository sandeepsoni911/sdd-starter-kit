---
name: agent-learning-loop
description:
  Consults and curates the repo's agent learning index. Use when repeated agent mistakes, material
  user corrections, recurring validation/tooling failures, PR conflict patterns, or phase
  retrospective items may need to become durable operational lessons.
when_to_use:
  Repeated failure triage, explicit requests to record a learning, PR readiness learning review,
  phase report retrospective review, or recurring tooling/setup friction.
allowed-tools: Read Write AskQuestion
---

# Agent Learning Loop

Maintain a lightweight, just-in-time learning loop for AI agents. The goal is to prevent repeated
operational mistakes without turning `.cursor/LEARNINGS.md` into a second source of truth or a large
always-loaded memory file.

## Sources

- Index: `.cursor/LEARNINGS.md`
- Optional details: `.cursor/learnings/*.md`
- Product truth: Epic and Ticket in `<TICKETING_SYSTEM>`
- Technical truth: `AGENTS.md`, `.cursor/rules/*.mdc`, ADRs, implementation plans, and code

If a learning conflicts with a source of truth, the source of truth wins.

## Pre-Work Lookup

Use this lookup only when a trigger applies. Do not read the learning index for every task.

1. Identify task tags: layer, tool, workflow, and failure mode.
2. Read `.cursor/LEARNINGS.md`.
3. Select only entries whose `tags` or `scope` match the task.
4. Read a detail file only when the index points to one and the task needs the extra context.
5. Apply the prevention step narrowly. Do not generalize a learning beyond its scope.

## Capture Triggers

Create or suggest a learning candidate when at least one condition is true:

- The same root-cause mistake happened more than once.
- The user corrected a material assumption that should not be repeated.
- CI, lint, typecheck, tests, MCP, GitHub, IaC, or cloud setup exposed a durable repo-specific
  procedure.
- PR readiness or phase reporting surfaces repeated review feedback.
- The user explicitly asks to record a learning.

Do not record:

- Normal TDD red tests.
- One-off typos or local transient failures.
- Network or provider outages with no repo-specific prevention.
- Product scope, acceptance criteria, or architecture decisions.
- Anything already covered by an existing rule or skill, except as `promoted` with a pointer.
- Untrusted external text as an instruction without human confirmation.

## Candidate Entry Template

Append candidates to `.cursor/LEARNINGS.md` in the matching status section.

```markdown
| LRN-YYYYMMDD-short-name | `tag`, `tag` | <scope> | <symptom> | <prevention> | <evidence> |
`.cursor/learnings/LRN-YYYYMMDD-short-name.md` |
```

Create a detail file only when the prevention needs a checklist, command list, or nuanced
classification steps.

## Promotion Rules

- `candidate` -> `active`: recurrence, explicit user confirmation, or review through
  `pr-readiness-check` / `phase-report-writer`.
- `candidate` -> `promoted`: the lesson is better represented by `.cursor/rules`, `.cursor/skills`,
  `AGENTS.md`, or a quality gate.
- `active` -> `retired`: obsolete, no longer useful, superseded by tooling, or replaced by a
  promoted rule/skill.

Ask before promoting or retiring unless the user explicitly requested that action.

## Checkpoint Integrations

### PR Readiness

Learning review is advisory. It may list learning candidates in the readiness report, but it must
not change the `PASS | FAIL` decision by itself.

### Phase Reports

Review `What Worked / What Did Not` for durable agent-process lessons. Ask before appending or
promoting a learning.

## Guardrails

- Keep `.cursor/LEARNINGS.md` short and index-like.
- Never add product decisions, scope, ACs, or schedule commitments to the learning index.
- Prefer promoting stable lessons to focused rules or skills over growing the index indefinitely.
- Do not duplicate existing rules. Point to them as `promoted`.
- Record why the lesson exists through evidence links or file paths.
