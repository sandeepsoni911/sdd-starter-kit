# Prompt 3 — Generate the Technical Solution Design

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** produce the SRS for a milestone from the PRD + Figma manifest + repo context.

## Inputs

- PRD (`@docs/discovery/prd.md`)
- Figma manifest (`@docs/discovery/figma-manifest.md`)
- Root `AGENTS.md` for tech stack + architecture
- Any relevant ADRs (`@docs/adr/`)

## Prompt

```
You are generating a Technical Solution Design.

Inputs:
- PRD: @docs/discovery/prd.md
- Figma manifest: @docs/discovery/figma-manifest.md
- Tech stack + architecture: @AGENTS.md
- Existing ADRs: @docs/adr/

Use the template at @templates/technical-solution.md.

Your job:
1. Restate the problem from the PRD in one paragraph.
2. Design the high-level architecture. Use ASCII or mermaid — no images.
3. Map every layer touched by this milestone. Be explicit about what changes and what doesn't.
4. Define the data model. New types, new tables/collections, new indexes.
5. Define API contracts. Every endpoint the frontend calls must appear here with method, path, auth, request, response shape.
6. Define the error handling posture. Each typed error → HTTP code → user feedback.
7. Define mock strategy for frontend dev without backend.
8. List security controls (auth, authz, PII, audit).
9. List observability plan (metrics, traces, logs, alerts).
10. List risks + mitigations.

Non-negotiables:
- Follow existing conventions in @AGENTS.md. Do NOT introduce new patterns without ADR justification.
- Every endpoint referenced in the frontend must have a request/response shape.
- Every new type must have validation (schema library, e.g. Zod).
- Do NOT skip the "Risks & mitigations" section.

Output: a complete Technical Solution Design markdown, ready for review.
```

## Expected output structure

Matches `@templates/technical-solution.md`.

## Validation checklist

- [ ] Every layer touched is listed
- [ ] Every new endpoint has method + path + auth + request + response
- [ ] Every new type has validation
- [ ] Error handling posture defined
- [ ] Security controls listed
- [ ] Observability plan present
- [ ] Risks & mitigations non-empty

## Known failure modes

- **Introduces new patterns without justification.** Mitigate by requiring AGENTS.md compliance.
- **Missing observability plan.** Mitigate by making it a required section.
- **Fictional endpoints.** Mitigate by cross-referencing with PRD user actions.

## Change log

- 2026-07-XX (v1): Initial.
