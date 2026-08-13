# KT Guide — Handing SDD to a New Tech Lead in 90 Minutes

> Adapted from Sandeep Soni's roll-off from the Project Alpha pod (Jul 2026). This is the runbook for onboarding a new tech lead (or another team's tech lead) onto SDD.

---

## Audience

- **New tech lead** joining a project already running SDD.
- **Peer tech lead** in a sibling team adopting SDD from scratch.
- **Yourself, 6 months from now**, when you've forgotten the details.

---

## The core promise

In 90 minutes, the recipient should be able to:

1. Explain SDD in one paragraph.
2. Locate every artifact family in the workspace.
3. Fire the right skill from a prompt for at least three common scenarios.
4. Know where to look when something goes wrong.

---

## Session structure (90 min)

### 0:00–0:10 — The 60-second pitch (10 min)

Deliver this without slides:

> "We use spec-driven development. That means we invest in 4 spec files up front — PRD, Technical Solution Design, UI Component Strategy, Implementation Phases — before writing any code. The AI then generates code that respects the specs. Everything else is scaffolding: workspace structure, always-applied rules, orchestration skills, ticketing-driven cards, phase-by-phase PRs, and per-phase retros. It's not magic; it's discipline. Setup cost is 10–15 hours over 2 weeks. Steady-state throughput is roughly 2× vs. default AI-IDE usage. The full method fits in one folder called the SDD starter kit — I'll walk you through it."

Then ask: *"Where are you starting from — greenfield project, brownfield takeover, or evaluation?"* Tune the rest of the session to their answer.

### 0:10–0:30 — Workspace tour (20 min)

Screen share; walk the workspace structure top-down.

1. **Root:** show `AGENTS.md`, `.cursor/rules/`, `.cursor/skills/`. Explain the trinity (context / behavior / skills).
2. **`docs/`:** show `waysofworking/`, `discovery/`, `implementation/`. Explain living specs.
3. **`capabilities/<capability>/`:** show `AGENTS.md`, `domain/`, `feature/`, `functions/`, `infrastructure/`.
4. **`capabilities/<capability>/docs/delivery/<milestone>/`:** show `implementation-plan.md`, `features/phase-N/`, `reports/`.
5. **`.cursor/BUGBOT.md`:** show the layered review posture.
6. **`.cursor/LEARNINGS.md`:** show the durable-lesson index.

**Callout:** the workspace *is* the doc. If they can navigate the folders, they understand 60% of SDD.

### 0:30–0:50 — Live demo of the milestone lifecycle (20 min)

Walk through one real milestone end-to-end:

1. **The Epic** in the ticketing system. Point to Product Context, ACs, Decisions Log.
2. **The implementation plan.** Show it in the repo. Point out phase boundaries + demonstrable outcomes.
3. **One phase's feature files.** Show `features/phase-<N>/<feature>.md`.
4. **The cards for that phase.** Show them in the ticketing system, linked to the epic.
5. **The PRs for one card.** Show the PR template. Point at "Layer Scope."
6. **The phase report.** Show `reports/phase-<N>.md`. Point at "What Worked / What Did Not."

**Callout:** "This is the shape of the work. Every milestone looks like this."

### 0:50–1:10 — Live demo of the 5 killer skills (20 min)

Fire up the AI IDE. Show these five skills firing in a real chat:

1. **`epic-implementation-planner`** — paste an epic key, watch it produce an implementation plan draft.
2. **`ticket-prd-builder`** — expand an existing ticket to the 10 mandatory sections.
3. **`layered-pr-planner`** — ask "how should I split PR-ing this ticket?"
4. **`pr-readiness-check`** — before opening a PR, ask "is this PR ready?"
5. **`phase-report-writer`** — at phase close, ask "wrap up phase 2."

**Callout:** "You don't type long prompts. You state intent. The rules pick the right skill."

### 1:10–1:25 — Hands-off exercise (15 min)

Give the recipient a real task from your backlog. Watch them:

1. Open the right AGENTS.md.
2. Fire the right skill.
3. Recognize when the skill asks for input.
4. Notice which rule is enforcing what.

**Callout:** correct silently. Don't take over the keyboard.

### 1:25–1:30 — Roll-off checklist (5 min)

Hand them:
1. This kit (or a link to your fork of it).
2. Access to the workspace (repo + ticketing + docs).
3. `docs/07-kt-guide.md` (this file) for repeat listens.
4. `docs/08-gap-analysis.md` so they know what was retrofitted vs. always-in-place.

Then answer: "What's the biggest risk you see?" and address it before you sign off.

---

## Common questions from recipients

### "How do I know when to use a skill vs. write a prompt?"

If the skill exists, use it. Skills exist because we ran the prompt 5+ times and codified it. Don't reinvent.

### "The AI wants to refactor code that's not in my task. Should I let it?"

No. `coding-behavior.mdc` forbids it. Reject and refocus.

### "What if the epic changes mid-milestone?"

Append to `## Decisions Log` in the epic. Then `divergence-trigger.mdc` will fire on any doc/code that drifts, and `prd-sync` will apply append-only corrections. Nothing gets silently rewritten.

### "The AI-generated PR is 800 lines. What do I do?"

Split it. `pr-size-and-stack.mdc` says ~400 impl lines is the cap. Rerun `layered-pr-planner` — it will produce a stack.

### "I don't have Atlassian MCP. Skills reference Jira commands."

Skills in this kit use `<TICKETING_SYSTEM>` placeholders. Bind them at setup time. See `quickstart/day-0-checklist.md`.

### "The team wasn't disciplined enough for TDD. How do I introduce it?"

Start with the `tdd-bdd-workflow` skill on one small feature. Show the ROI (fewer bugs, faster review, clearer intent). Then make it a rule (`.cursor/rules/engineering/tdd-workflow.mdc` is always-applied — flip the switch).

### "How much do I actually save by doing this?"

MT pod: ~2.2–2.5× throughput over 15 weeks vs. baseline. See `mt-program-leadership-summary.md` for the metrics conversation.

### "What about the initial 10–15 hours of setup?"

Amortized over a 3-month project, that's ~0.3% of engineering time. You recover it in week 1.

---

## What to hand off (physical checklist)

- [ ] The **SDD starter kit** (this folder or a fork).
- [ ] The **project workspace** access (repo, ticketing, docs, cloud).
- [ ] The **MCP configurations** — list of installed MCPs + how to auth.
- [ ] The **`pending-items-tracker.md`** (or your equivalent) with all open external / internal / architectural items.
- [ ] A list of **stakeholder contacts** and their engagement patterns (who to escalate to for what).
- [ ] The **living specs** (discovery spec, to-be architecture doc) with a `## Session log` up to date.
- [ ] The **most recent milestone retro** so they know the current state of the world.
- [ ] The **top 3 open decisions** (with your recommendation) that they'll inherit.
- [ ] A **calendar of established rituals** (Monday kickoff / Friday wrap) so they don't have to invent cadence.

---

## What to NOT hand off

- **Your personal chat history.** Not portable. Delete or archive.
- **Your personal rules** (e.g., outbound-comms-style with your voice). Recipient should author their own.
- **Half-baked skills** you never validated. Delete or clearly mark experimental.

---

## Post-KT follow-up

- **Week 1:** stay on retainer for 30-minute questions. Don't do the work for them.
- **Week 2:** one 60-minute checkpoint. Are they running their first Monday kickoff / Friday wrap?
- **Week 4:** one final checkpoint. How's their first milestone going? Any skills or rules they've added?
- **Month 3:** ask for their fork of the kit. Their learnings should merge back.

---

## The pattern, in one paragraph, to hand to leadership

> "SDD (Spec-Driven Development) is a pattern for using AI coding IDEs in enterprise software delivery. It treats specs (PRD, technical design, UI strategy, phase plan) as the *context window* for the AI, not as bureaucracy. Combined with always-applied behavioral rules and orchestration skills, it produces code that respects org standards from Phase 0 rather than being fixed in review. The Project Alpha pod achieved ~2.2–2.5× throughput vs. default AI-IDE usage over 15 weeks. The setup cost is 10–15 hours over 2 weeks. It's portable across stacks and can be adopted by any tech lead in 90 minutes of KT."
