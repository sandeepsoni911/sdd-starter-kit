# The 5-Layer Pattern

> Adapted from Sandeep Soni's "AI Orchestration Playbook" (ClientName Project Alpha pod). This is the mental model that makes SDD scale beyond "AI writes code" into "AI orchestrates a program."

---

## The mindset shift

Most teams use AI-assisted IDEs as **faster autocomplete**. That caps you at 1.2–1.5× productivity.

To get to 2–3×, treat the AI IDE as an **orchestration layer** that sits over your codebase, your knowledge base, your stakeholder tools, and your communication channels — not as an editor with a smart sidebar.

| Default mindset | Orchestration mindset |
|---|---|
| Cursor helps me write code | Cursor helps me run a program — code is one of many outputs |
| I prompt it for what I need | I encode my standards once (rules), then it enforces them every prompt |
| Each chat starts cold | Each chat inherits context from a structured workspace |
| AI writes, I edit | AI orchestrates artifacts; I make decisions |
| Tools = IDE features | Tools = MCPs + skills + hooks + rules + custom commands |

---

## The stack

Think of your AI-augmented delivery system as five stacked layers. Each layer multiplies the one above it. Skipping any of them limits your ceiling.

```
Layer 5 — Leadership / IP Replication   (cross-team adoption, narrative, scaling)
Layer 4 — Operating Cadence             (rituals, prompts, status reports)
Layer 3 — Knowledge Management          (workspace structure, persistent trackers, session logs)
Layer 2 — Behavioral Standards          (always-applied rules, skills, hooks)
Layer 1 — Tool Surface                  (MCPs, custom commands, file access, browser)
```

Most teams are stuck at Layer 1. The compounding effect kicks in when Layers 1–4 are all in place.

---

## Layer 1 — Tool Surface

**What it is:** the MCPs (Model Context Protocol servers), custom commands, and integrations your AI IDE can reach.

**Minimum viable set (Week 1):**

| MCP | What it unlocks | Effort |
|---|---|---|
| Ticketing (Jira / Linear / GitHub Issues) | Search company knowledge, generate status reports, create tickets from a doc | 1 hour |
| GitHub / GitLab | Read PRs, search code across repos, manage issues from chat | 30 min |
| Sequential Thinking | Multi-step reasoning for design decisions | 5 min |

**Add when relevant (Week 2+):**

| MCP | When to add |
|---|---|
| Doc system (Confluence / Notion / GitHub Wiki) | Any program with substantial internal docs |
| Figma | Any team with design-to-code or code-to-design needs |
| Cloud provider docs (AWS / GCP / Azure) | Cloud-native architecture work |
| Terraform | IaC-heavy programs |
| Markitdown | Converting external docs (PDF, Office) into markdown for chat context |
| Chrome DevTools | UI debugging, browser automation |
| Box / SharePoint | Document-heavy programs |

**Anti-pattern:** installing 20 MCPs at once. Start with 3, prove value, add when you hit a real friction point.

---

## Layer 2 — Behavioral Standards

**What it is:** written-once, applied-every-prompt behaviors that remove the cost of repeating "use my voice", "don't refactor adjacent code", "match existing conventions" in every chat.

Three mechanisms:

### 2a. Always-applied rules (`.cursor/rules/*.mdc`)

Read on every prompt automatically. See `.cursor/rules/` in this kit for the portable set (21 rules).

**Anti-pattern:** writing one giant rule file with 500 lines. Split by concern.

### 2b. Skills (`.cursor/skills/<name>/SKILL.md`)

Trigger-word-activated mini-agents. See `.cursor/skills/` in this kit for the portable set (10 skills).

**Skills compound rules.** A rule says *how* to write; a skill says *what* to produce.

### 2c. Hooks (`.cursor/hooks/*.sh`) — optional

Shell scripts that fire on IDE session events. Use sparingly — overuse causes confusion.

Two hooks the Alpha pod found genuinely useful:

| Hook | Event | Why |
|---|---|---|
| `keep-awake-start.sh` | `sessionStart` | Stops the machine sleeping mid-agent (`caffeinate -dimsu -t 1200`) |
| `pending-items-reconcile.sh` | `sessionEnd` | Appends `## Session ended <UTC>` to `pending-items-tracker.md` |

**Not included in this kit** — hooks are environment-specific. If you want them, ask the AI to author them per your OS and preferences.

---

## Layer 3 — Knowledge Management

**What it is:** the workspace structure and persistent trackers that give the agent stable context across sessions.

### 3a. Workspace structure (recommended)

```
<program>/
├── docs/
│   ├── AGENTS.md               <- folder-level instruction file
│   ├── waysofworking/          <- HOW we work (this kit lives here or is copied here)
│   ├── discovery/              <- WHAT we found (living spec, integration map)
│   ├── legacy/                 <- AS-IS analysis (if applicable)
│   ├── implementation/         <- TO-BE design, ADRs, technical stories
│   └── zoom-recordings-transcripts/  <- meeting transcripts + MOMs
├── repos/                      <- cloned codebases for analysis (read-only) and active builds
├── data/                       <- production data samples, exports
```

**The non-obvious move:** `AGENTS.md` files at folder level. Cursor reads them automatically when working in that folder. See `docs/05-agents-md-guide.md`.

### 3b. Living specs, not snapshot specs

Pick 1–3 documents to be **living specs** (e.g., `integration-discovery-spec.md`, `to-be-architecture-narrative.md`). Every important decision, finding, or open question goes there. Add a `## Session log` with date-stamped entries. The agent can be told to read that section every time.

This replaces the "where did we land on X?" search you'd otherwise do 5×/day.

### 3c. Persistent on-disk trackers

`pending-items-tracker.md` in the workspace root. Categorized: external (waiting on stakeholders), internal (waiting on you), team, architecture decisions. The agent reads and reconciles it at session start and end.

This is what stops you forgetting an item you asked a stakeholder about three weeks ago.

---

## Layer 4 — Operating Cadence

**What it is:** rituals that turn the system from "I have to remember to use the AI IDE well" into "the AI IDE structures my week."

### The four rituals

| Ritual | When | Output | Save? |
|---|---|---|---|
| **Monday Kickoff** | Mon AM, fresh chat | 1-page brief: last-week recap, this-week priorities, risks, open questions | `monday-YYYY-MM-DD.md` |
| **Daily Catch-up** | Tue–Fri AM, fresh chat | 5–10 lines: stale TODOs + one focused question | No — disposable |
| **Friday Wrap** | Fri PM, fresh chat | Status report (4 sections, ticket-grounded) | Copy to Slack/email |
| **Mid-day Reset** | Post-lunch slump | 5 lines: shipped / blocked / next | No |

Full prompt text: `prompts/monday-kickoff.md` and `prompts/friday-wrap.md`.

**Constraint baked in:** if a section has nothing real to say, the prompt requires the agent to write "Nothing material this week" instead of padding. This is what keeps status reports honest.

### Meeting-driven cadence

| Meeting type | Workflow |
|---|---|
| Stakeholder call | Record → save transcript → generate MOM → review → fan-out actions to tickets |
| Architecture walkthrough | Same MOM flow + update `architecture-walkthrough-action-items.md` tracker |
| 1:1 with manager | Run Monday Kickoff or Friday Wrap as input — don't show up empty-handed |

### Communication patterns

Draft outbound comms (kudos replies, blockers, sympathy, scoping, status updates) via a shared rule that encodes voice, tone, structure. See `outbound-comms-style.mdc` in the Alpha setup for a full template (not included here because it's personal; author your own).

---

## Layer 5 — Leadership / IP Replication

**What it is:** the layer that lets your team's gains spread to the rest of the org.

Three mechanisms:

1. **Document the pattern, not the artifacts.** Other teams don't need your discovery spec; they need your *method* of producing one. This kit is that method.

2. **One-pager for leadership** with concrete numbers. Manual baseline vs. typical-AI-usage vs. your team's actual. Template: `mt-program-leadership-summary.md` (in the Alpha waysofworking folder — study it, then author your own).

3. **Adoption pairing, not adoption training.** Pair with another team's tech lead for half a day. Walk them through your workspace, your rules, your rituals. They leave with their own copy, not a slide deck. See `docs/07-kt-guide.md`.

---

## Maturity model — where is your team?

| Level | Behaviors | Throughput multiplier |
|---|---|---|
| 0 — AI IDE as autocomplete | Tab-completion in editor; chat used for "explain this code" | 1.0–1.2× |
| 1 — AI IDE as chat assistant | Whole-codebase questions, but no rules, no MCPs, no workspace structure | 1.2–1.4× |
| 2 — AI IDE with MCPs | Ticketing/knowledge MCPs wired up; status reports and tickets pulled into chat | 1.4–1.7× |
| 3 — AI IDE with rules + skills | Behavioral standards encoded once; rituals adopted | 1.7–2.2× |
| **4 — AI IDE as orchestration layer** | **All 5 layers in place; cross-team IP being shared** | **2.2–3.0×** |
| 5 — Multi-agent orchestration | Subagents fanning out parallel work; closed-loop validation; agentic CI | 3.0×+ (frontier) |

**The Project Alpha pod operated at Level 4.** Most teams at large enterprises are at Level 1–2. The gap between Level 2 and Level 4 is **not skill** — it's investment in Layers 2, 3, and 4. That investment pays back in 2–3 weeks.

---

## Cost & risk callouts

- **Up-front time cost:** ~10–15 hours over 2 weeks to get to Level 3. Mostly one-time.
- **Discipline cost:** the rules and rituals only work if you follow them. The first 2 weeks feel like overhead. Stick with it.
- **Vendor lock concern:** the workspace structure, `AGENTS.md` convention, most rules, and all templates are tool-agnostic. If the AI IDE ever needs to be replaced, the artifacts (docs, specs, MOMs) survive — only the rules and skills are tool-specific, and even those port with minor edits.
- **AI-output trust:** rules like `coding-behavior` (think before coding, surgical changes, goal-driven verification) are explicitly designed to reduce confident-but-wrong output. Don't skip them.

---

## Setup checklist for a new team (from the Alpha playbook)

### Week 1 — foundation (5–8 hours across the week)

- [ ] Create workspace folder structure
- [ ] Add root `AGENTS.md`
- [ ] Add folder-level `AGENTS.md` where useful
- [ ] Install 3 core MCPs (Ticketing, GitHub, Sequential Thinking)
- [ ] Authenticate MCPs
- [ ] Copy universal rules from this kit (`coding-behavior`, `chat-context-management` if you have your own, plus the 21 rules in this kit's `.cursor/rules/`)
- [ ] Create your living spec doc with a `## Session log` section

### Week 2 — operating cadence

- [ ] Adopt Monday Kickoff and Friday Wrap rituals
- [ ] Install `meeting-mom` skill (or equivalent) — process your next stakeholder call through it
- [ ] Create `pending-items-tracker.md` in workspace root — migrate action items into it
- [ ] Pair with one other team member for 30 min showing them your workspace setup

### Week 3 — team-specific rules

- [ ] Write your team's equivalents of `architecture-agent.mdc`, `discovery-agent.mdc`
- [ ] Add `persistent-todo-tracking` behavior
- [ ] Start the learning loop — one entry a week for the first month

### Week 4+ — measure and tune

- [ ] Track PRs/week, review turnaround, AI-generated-then-rejected %
- [ ] Add new MCPs only when you hit a friction point that justifies the cognitive cost
- [ ] Review the coaching log weekly if you have one — repeat tips signal a pattern that should become a snippet or skill

---

## How to start the conversation with another team

> "I'd like to spend 90 minutes walking you through how our team is using AI-assisted delivery. It's not an IDE demo — the IDE's built into your laptop already. It's a workspace structure, a set of always-applied rules, and four operating rituals that compress the discovery → design → delivery cycle. The setup cost is ~10–15 hours over 2 weeks. We're seeing roughly a 2× throughput improvement vs. default usage. I'll bring my workspace, you'll leave with a copy of the foundational kit and a checklist for week 1."

That's it. No slide deck.

---

## Anti-patterns

| Anti-pattern | Why it hurts | Do this instead |
|---|---|---|
| Treating the AI IDE as autocomplete | Caps you at 1.2× | Layer 2 + 3 |
| One giant chat for everything | Context bloat, slow agent, repeated questions | One chat per task; describe past chats by topic |
| Pasting documents into chat | Wastes tokens, can't be re-referenced | Save to `docs/`, use `@filename` |
| Refactoring adjacent code | Bloated diffs, harder reviews | `coding-behavior` rule enforces surgical changes |
| Letting the AI pick silently between options | You inherit decisions you didn't make | Demand 2 options + trade-offs + recommendation |
| Skipping the Friday Wrap | Nobody knows what shipped, including you | Schedule it as a Friday calendar block |
| Reading every transcript when referencing past work | Burns context window | `chat-context-management` — describe by topic |
| Building rules for problems you don't have yet | Cognitive overhead with no payoff | Start with 4–5 rules; add when a pattern repeats 3+ times |
| Letting the team copy your individual workflow | Doesn't scale; people drift back to defaults | Document the pattern; do an adoption pairing |
