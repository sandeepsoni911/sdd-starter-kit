# Spec-Driven Development (SDD) Starter Kit

A portable, tech-agnostic scaffold for running **spec-driven, AI-assisted software delivery** using Cursor (or any comparable AI coding IDE). Drop it into a new project on Day 1 and you skip the first 10–15 hours of setup.

> **What this kit encodes:** the delivery method I built and refined while tech-leading the ClientName Project Alpha pod (Mar–Jul 2026) — a spec-driven, AI-assisted delivery flow (SuperSpec + 15-step milestone) combined with the "AI Orchestration Playbook" 5-layer pattern — refactored into a portable, reusable form. About 2–3× throughput vs. default Cursor usage.

---

## Who this is for

- **Tech leads** starting a new project (greenfield or brownfield) who want to use AI coding agents effectively from Day 1.
- **Senior engineers / architects** who want to establish spec-driven delivery without re-inventing the process.
- **Anyone doing KT** to a new team adopting AI-assisted delivery — hand them this folder.

---

## What SDD is (in one paragraph)

**SDD (Spec-Driven Development)** treats specs as the *context window* for AI coding agents, not as bureaucracy. You invest time up front producing a PRD, a Technical Solution Design, a UI Component Strategy, and an Implementation Phases plan — then AI generates the code that respects them. Every artifact in this kit exists to encode a piece of context (product intent, architectural constraint, coding standard, review posture) so that generated code is compliant *from the start*, not fixed in review.

The five first principles:

1. **We are not greenfield.** Whether it's a new repo or an existing one, there are conventions to follow — discover them and encode them.
2. **Specs are context, not paperwork.** Better specs → better generated code.
3. **Reuse over reinvention.** The agent must know what already exists before it builds anything.
4. **Cross-functional requirements are built in, not bolted on.** Logging, security, error handling, accessibility go in the specs so they land in Phase 0 code.
5. **The workflow will get messy — and that's okay.** Understand *why* each artifact exists so you can improvise when the process breaks down.

---

## Start-here paths (by role)

| If you are a... | Read in this order |
|---|---|
| **Tech lead — Day 1 of a new project** | `README.md` → `docs/01-sdd-playbook.md` → `quickstart/day-0-checklist.md` → `quickstart/week-1-checklist.md` |
| **Tech lead — inheriting an existing project** | `README.md` → `docs/07-kt-guide.md` → `docs/02-artifacts-catalog.md` |
| **Senior engineer / dev pair** | `README.md` → `docs/01-sdd-playbook.md` (§Development onwards) → `docs/06-skills-and-rules-guide.md` |
| **Product / delivery lead** | `README.md` → `docs/01-sdd-playbook.md` (§Discovery, §PRD, §Change Management) → `templates/raci-roles.md` |
| **Someone I'm doing KT to** | `README.md` → `docs/07-kt-guide.md` → live walkthrough of one milestone folder |
| **Adopting on a non-TS/React project (Python, Go, Java, ...)** | `README.md` → `docs/10-language-adaptation-guide.md` → `quickstart/day-0-checklist.md` |
| **Building a RAG / LLM / agent app** | `README.md` → `docs/11-rag-and-agent-apps.md` → `templates/eval-plan.md` → `docs/10-language-adaptation-guide.md` (Python section) |
| **Switching from Cursor to another AI IDE** | `README.md` → `docs/12-tool-portability.md` |
| **Adopting on a stack with unusual tools (non-Figma, non-GitHub, non-Zoom, non-Jira, ...)** | `README.md` → `docs/13-tool-substitution-guide.md` |

---

## Kit contents (55 files, ~4 hours to skim end-to-end)

```
sdd-starter-kit/
├── README.md                        ← You are here.
├── docs/                            9 orientation docs (SDD process, playbook, catalog, KT, gaps)
├── .cursor/
│   ├── rules/process/               10 SDD process triggers (portable as-is)
│   ├── rules/engineering/           11 tech-agnostic coding rules
│   ├── skills/                      10 orchestration skills (epic → phase → card → PR → report)
│   ├── BUGBOT.md.template           Layered review posture
│   └── LEARNINGS.md.template        Learning index scaffold
├── templates/                       13 spec + artifact templates (PRD, tech solution, UI strategy, etc.)
├── prompts/                         8 standalone reusable prompts (spec generation + rituals)
├── quickstart/                      4 checklists (Day 0, Week 1, First Milestone, Roll-Off)
└── examples/                        4 sanitized real-world examples from Project Alpha
```

---

## The three things this kit gets right

1. **Skills, rules, and AGENTS.md files are separated by role, not thrown in one folder.** You know what to copy and what to edit.
2. **Templates have `[PLACEHOLDER]` fields, not example content.** No accidentally shipping "Project Alpha" into your new project.
3. **The kit itself is documented.** Every skill and rule has a "why this exists" note. You can drop 20% of them and the remaining 80% still work.

---

## The 15-step first milestone (the shape of the work)

The full playbook lives in `docs/01-sdd-playbook.md`. Here's the shape:

| Phase | Steps | Owner |
|---|---|---|
| **Discovery** | 1. Record discovery sessions → 2. Design in Figma → 3. Draft PRD | Product + Design |
| **The Room** | 4. Design walkthrough → 5. PRD + design sign-off | Everyone |
| **SuperSpec** | 6. Export Figma + generate manifest → 7. Bootstrap AGENTS.md → 8. Tech Solution → 9. UI Strategy → 10. Implementation Phases → 11. Team sign-off | Dev pair + Tech Lead + AI |
| **Delivery** | 12. Build phase by phase → 13. PR feedback loop → 14. Product/design review batches | Dev pair + AI |
| **Retro** | 15. Retrospective → update AGENTS.md and this kit | Everyone |

---

## Portability contract

This kit assumes:

- You use **Cursor** as your AI-assisted IDE (or a compatible one that reads `AGENTS.md`, `.cursor/rules/`, and `.cursor/skills/`).
- You have some **ticketing system** (Jira, Linear, GitHub Issues, Azure Boards — anything).
- You have some **knowledge/doc system** (Confluence, Notion, GitHub Wiki — anything).
- Your project has a concept of **layered architecture** (even if it's just `client/` / `server/` / `shared/`).

Where the kit references these, it uses `<TICKETING_SYSTEM>`, `<DOC_SYSTEM>`, and `<LAYER>` placeholders. Bind them to your stack at setup time — see `quickstart/day-0-checklist.md`.

### Language portability

**~80% of the kit is fully language-agnostic** (SDD process, skills, rules, templates, prompts). Only ~14 files carry TypeScript/React code examples. When you drop the kit into a Python / Go / Java / Kotlin / Rust / C# project, follow `docs/10-language-adaptation-guide.md` — it lists every language-dependent file with concrete adaptations per language. Total adaptation time on Day 0: ~2 hours.

### Domain portability (RAG / LLM / agent apps)

**~85% of the kit applies to LLM / RAG / agent applications** as-is. The SDD process is arguably *more* important for LLM apps because probabilistic outputs make undisciplined changes far riskier. For a first LLM project, `docs/11-rag-and-agent-apps.md` covers the ~15% that changes: LLM-app layer taxonomy (`ingestion → embedding → retrieval → generation → agents → evaluation → api → ui`), evaluation as a first-class quality gate parallel to unit tests, 10 LLM-specific engineering rules, prompt-as-code discipline, and a reference milestone breakdown. Pair with `templates/eval-plan.md` for the eval harness.

### Tool portability

**Content is 100% portable across AI IDEs** — every file is plain markdown. **Automation varies by tool.** The kit is optimized for Cursor; for Claude Code (~90% compat), Windsurf (~85%), Cline (~70%), Aider (~60%), GitHub Copilot Chat (~50%), Continue.dev (~50%), and JetBrains AI (~40%), `docs/12-tool-portability.md` documents the file renames, rule format conversions, and skill invocation adaptations needed. The nested `AGENTS.md` convention is emerging as a de facto standard across tools — that's your longest-lasting asset.

### External tool substitutions

The kit ships with defaults (GitHub, Figma, Zoom, Jira via Atlassian MCP, `gh` CLI). Real projects vary — different design tool, ticketing system, SCM host, meeting source, or missing MCPs entirely. The `templates/AGENTS.root.md` has an "External Tools & Integrations" table where you declare your project's actual tools; every skill reads it. When any tool has no MCP available, the kit degrades gracefully to a manual workflow (draft artifacts locally, human moves them into the tool). Full swap recipes and no-MCP fallback patterns are in `docs/13-tool-substitution-guide.md`.

---

## Maturity model — how far you get with this kit

| Level | Behaviors | Throughput multiplier |
|---|---|---|
| 0 — Cursor as autocomplete | Tab-completion; chat used for "explain this code" | 1.0–1.2× |
| 1 — Cursor as chat assistant | Whole-codebase questions, no rules, no MCPs | 1.2–1.4× |
| 2 — Cursor with MCPs | Ticketing/knowledge MCPs wired; status reports pulled into chat | 1.4–1.7× |
| 3 — Cursor with rules + skills | Behavioral standards encoded once; rituals adopted | 1.7–2.2× |
| **4 — Cursor as orchestration layer** | **All 5 layers of `docs/04-the-5-layer-pattern.md`; this kit fully adopted** | **2.2–3.0×** |
| 5 — Multi-agent orchestration | Subagents fanning out parallel work; closed-loop validation | 3.0×+ (frontier) |

**This kit gets a disciplined team to Level 4 in 2–3 weeks.**

---

## Credits

- **Sandeep Soni** — author of this kit. Built the SuperSpec method (PRD → Tech Solution → UI Strategy → Implementation Phases, 15-step milestone) and the "AI Orchestration Playbook" 5-layer pattern (Tool Surface → Behavioral Standards → Knowledge Management → Operating Cadence → Leadership) while tech-leading the Client Project (Mar–Jul 2026), then refactored the whole thing into this portable, tech-agnostic form.
- **The Project pod** — proving ground for these patterns (Mar–Jul 2026).

---

## Maintenance

This kit is a living artifact. When you finish a project:

1. Note which rules/skills you added or dropped → propagate the durable ones back to this kit.
2. Update the maturity level of your setup honestly.
3. If a pattern repeated 3+ times, promote it into a skill or rule.

See `docs/08-gap-analysis.md` for the audit method.

Reach out to me if you have any questions or feedback on Sandeep.Soni@thoughtworks.com

Blog Post Link : https://thoughtworks.workvivo.com/comments/update/4435828
