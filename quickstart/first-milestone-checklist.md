# First Milestone Checklist — 15 Steps

> The end-to-end shape of a milestone in SDD. Follow this the first time. After that, you'll know which steps to compress and which to spend more time on.
> Estimated time: **2–4 weeks** depending on milestone size.

---

## Discovery (Steps 1–3)

- [ ] **1. Record discovery sessions.** Save transcripts to `discovery/meeting-transcripts/`. Interview stakeholders. Capture context.

- [ ] **2. Design in Figma.** Designer creates mockups, validates with users, iterates.

- [ ] **3. Draft the PRD** using `prompts/01-prd-from-transcripts.md`. Capture open questions with owners.

## The Room (Steps 4–5)

- [ ] **4. Design walkthrough.** Developers, product, and design in one room for ≤1 hour. Challenge everything. Resolve open questions.

- [ ] **5. Sign off PRD + design** with client stakeholders.

## Bootstrap (Steps 6–7)

- [ ] **6. Export Figma screens** to `discovery/figma-exports/`. Generate the manifest using `prompts/02-figma-manifest.md`.

- [ ] **7. Update / create AGENTS.md** — scan the codebase; feed in recent PR feedback via `prompts/bootstrap-agents-md.md`.

## SuperSpec (Steps 8–10)

- [ ] **8. Generate Technical Solution Design** using `prompts/03-technical-solution.md`. Iterate 3–5 rounds with the team.

- [ ] **9. Generate UI Component Strategy** using `prompts/04-ui-strategy.md`. Iterate 3–5 rounds with design + team.

- [ ] **10. Generate Implementation Phases** using `prompts/05-implementation-phases.md`. Iterate 3–5 rounds.

## Sign-off (Step 11)

- [ ] **11. Team sign-off.** Developer pair, tech lead, product, and design review all four spec files and confirm readiness. See `docs/01-sdd-playbook.md` §15.

## Delivery (Steps 12–14)

- [ ] **12. Kick off Phase 1.** Use `epic-implementation-planner` skill to create the milestone folder and generate `implementation-plan.md`. Use `phase-card-generator` to create phase 1 tickets. For each ticket, use `ticket-prd-builder` to fill the 10 sections.

- [ ] **13. Build phase by phase.** Per ticket: `layered-pr-planner` → implement (TDD) → `pr-readiness-check` → open PR → review → merge. Track in `templates/traceability-matrix.md`.

- [ ] **14. Product/design review completed phases** in batches. Apply feedback as fixes.

## Retro (Step 15)

- [ ] **15. Milestone retrospective.** Run `phase-report-writer` for each phase. At milestone close, aggregate into milestone retro. Update `AGENTS.md` with new conventions. Promote durable lessons via `agent-learning-loop`. Update this kit with what worked.

---

## Cadence during delivery

| Frequency | Ritual |
|---|---|
| Monday | Monday kickoff |
| Daily | 15-min standup (not from this kit — your team's cadence) |
| Per PR | `pr-readiness-check` before opening; layered PRs only |
| Per phase | Phase demo + phase report |
| Weekly | Friday wrap |
| Per milestone | Retro + AGENTS.md update + kit sync |

---

## Common pitfalls in first milestone

| Pitfall | How it manifests | Fix |
|---|---|---|
| Rushed SuperSpec | AI generates code the design didn't envision | Spend 3–5 rounds on each doc — it's cheaper than review cycles |
| Horizontal phases (all domain first, then all UI) | Phase 1 has nothing demonstrable | Vertically slice — every phase produces user-visible outcome |
| PRs over ~400 impl lines | Un-reviewable; stalls merges | `layered-pr-planner` splits into stacks |
| Skipping phase report | Anchors lost for next phase | Non-negotiable step; enforced by `phase-end-trigger.mdc` |
| One monolithic AGENTS.md | Bloat, harder to maintain | Nested AGENTS.md per module |
| Not measuring | Can't defend the throughput claim | Track: PRs/week, review turnaround, AI-generated-then-rejected % |

---

## Success signal at milestone close

- All P0 ACs delivered (see traceability matrix)
- Every phase has a report with a "What Worked / What Did Not" section
- AGENTS.md updated with new conventions
- Zero rejected-in-review PRs for architectural reasons in the last 2 phases (proves standards work)
- Client sign-off achieved without last-minute scope surprises
