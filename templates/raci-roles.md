# RACI — SDD Roles & Ownership

**R** = Responsible (does the work)
**A** = Accountable (owns the outcome; sign-off authority)
**C** = Consulted (input required before completion)
**I** = Informed (kept aware; no action required)

---

## By artifact

| Artifact | Product | Design | Tech Lead | Dev pair | QA | Delivery Lead | Client |
|---|---|---|---|---|---|---|---|
| **Discovery** — transcripts + MOMs | A | C | C | I | I | R | C |
| **PRD** | A/R | C | C | I | C | I | A (sign-off) |
| **Design (Figma)** | C | A/R | C | I | I | I | A (sign-off) |
| **AGENTS.md** — root | I | I | A/R | C | I | I | I |
| **AGENTS.md** — per-module | I | I | A | R | I | I | I |
| **Technical Solution Design** | C | I | A/R | C | I | I | C |
| **UI Component Strategy** | C | A | R | R | I | I | I |
| **Implementation Phases** | C | I | A/R | C | I | C | I |
| **Milestone Implementation Plan** | C | I | A/R | C | I | C | I |
| **Ticket (10 sections)** | C (Product Context, AC) | I | A | R | C (QA Notes) | I | I |
| **Layered PR Plan** | I | I | A | R | I | I | I |
| **Code + tests** | I | I | A | R | C | I | I |
| **PR review** | I | I | A/R | R | C | I | I |
| **PR readiness check** | I | I | A | R | I | I | I |
| **Phase Report** | I | I | A/R | C | C | C | I |
| **ADR** | C (product ADRs) | I | A/R | C | I | I | I |
| **LLD** | I | I | A | R | I | I | I |
| **Traceability Matrix** | C | I | C | I | C | A/R | I |
| **Milestone demo** | R | C | C | R | C | A | A (accept) |
| **Milestone Retrospective** | R | R | R | R | R | A | I |
| **Change requests (post-sign-off)** | R | C | C | I | I | A | A |

---

## By process step

| Step | R | A | C | I |
|---|---|---|---|---|
| Discovery kick-off | Product | Product | Design, Tech Lead | Everyone |
| Design walkthrough (The Room) | Everyone | Product | — | Client |
| SuperSpec generation | Dev pair | Tech Lead | Product, Design | Delivery Lead |
| SuperSpec sign-off | Tech Lead | Tech Lead | Product, Design | Client, Delivery Lead |
| Phase kickoff | Dev pair | Tech Lead | Product | QA, Delivery Lead |
| Card creation | Dev pair (via `phase-card-generator`) | Tech Lead | Product | QA |
| Implementation | Dev pair | Tech Lead | — | Product, Delivery Lead |
| PR review | Peer dev + Tech Lead | Tech Lead | Design (for UI PRs) | Product |
| Phase demo | Dev pair | Tech Lead | Product, Design | Client |
| Phase close | Tech Lead | Tech Lead | Delivery Lead | Product, Client |
| Milestone close | Tech Lead | Delivery Lead | Product | Client |
| Retro | Everyone | Delivery Lead | — | Client |

---

## Anti-patterns

- **Two "A"s on the same row.** Only one accountable owner per artifact. If unclear, escalate.
- **Blank "A" column.** Every artifact needs an owner.
- **"R" without "A" support.** The doer needs a decision-maker they can escalate to.
- **Client on the "R" line.** Clients don't produce artifacts; they sign off on them.
