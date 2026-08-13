# Roll-Off Checklist — Handing SDD to a Successor

> Everything you should do when you leave a project so the next tech lead inherits a working system, not tribal knowledge.
> Estimated time: **1 day** of focused work + a 90-minute KT session.

Pair this with `docs/07-kt-guide.md` for the KT session structure.

---

## Two weeks before roll-off

- [ ] **Identify the successor.** Get commitment on who's inheriting.
- [ ] **Schedule the 90-minute KT session** in advance. Don't cram it into the last day.
- [ ] **Book a 30-min weekly follow-up for the successor's first month.** Retainer, not full engagement.

---

## One week before roll-off

- [ ] **Update `pending-items-tracker.md`** so it reflects reality.
- [ ] **Close out any personal experiments** in `.cursor/rules/experimental/` or draft skills.
- [ ] **Retire any obsolete skills / rules** you know are dead code.
- [ ] **Author a "current state" doc** in `docs/waysofworking/` capturing:
   - What's shipped (link to latest milestone retro)
   - What's next (link to next milestone implementation plan)
   - Top 3 open decisions with your recommendation
   - Top 3 risks
- [ ] **Update AGENTS.md** with any conventions that crystallized recently but you never wrote down.

---

## Day of roll-off

### Handoff artifacts (physical checklist)

- [ ] **This SDD starter kit** — fork it or share the path.
- [ ] **Project workspace access** — repo, ticketing, docs, cloud, CI.
- [ ] **MCP configurations** — list of installed MCPs + how to auth each.
- [ ] **`pending-items-tracker.md`** — with all open external / internal / architectural items.
- [ ] **Stakeholder contacts** — who to escalate to for what.
- [ ] **Living specs** — discovery spec, to-be architecture doc, with `## Session log` up to date.
- [ ] **Most recent milestone retro** — so they know the current state.
- [ ] **Top 3 open decisions** — with your recommendation.
- [ ] **Calendar of established rituals** — Monday kickoff / Friday wrap templates.

### KT session (90 minutes)

Follow `docs/07-kt-guide.md`. In order:

- [ ] **0:00–0:10** — 60-second pitch of SDD
- [ ] **0:10–0:30** — Workspace tour
- [ ] **0:30–0:50** — Live demo of the milestone lifecycle
- [ ] **0:50–1:10** — Live demo of the 5 killer skills
- [ ] **1:10–1:25** — Hands-off exercise (they drive)
- [ ] **1:25–1:30** — Roll-off checklist (this file)

### What to NOT hand off

- [ ] Delete or archive personal chat history (not portable).
- [ ] Delete personal rules with your voice / tone (e.g., outbound-comms-style with your name).
- [ ] Delete half-baked skills you never validated.
- [ ] Delete personal calendar templates.

---

## Week 1 after roll-off

- [ ] **30-min checkpoint** with the successor.
   - Are they running Monday kickoff / Friday wrap?
   - Any skills / rules confusing them?
   - Any stakeholders they need to meet?

## Week 2 after roll-off

- [ ] **30-min checkpoint** with the successor.
   - How's their first milestone going?
   - Any patterns emerging that they'd add to the kit?

## Week 4 after roll-off

- [ ] **Final 30-min checkpoint**.
   - Full unwind. They should be independent.
   - Ask for their fork of the kit — their learnings merge back into your master copy.

---

## The IP that stays with you

Your personal starter kit (this repo) is yours. Take it to the next project.

- The **process** (SDD) is portable IP.
- The **artifacts** (skills, rules, templates) are portable IP.
- The **rituals** (Monday / Friday) are portable IP.
- The **client-specific context** (their names, their decisions, their code) stays with them.

Rule of thumb: if a file references a specific client, person, or product name, it stays. If it references a pattern, it comes with you.
