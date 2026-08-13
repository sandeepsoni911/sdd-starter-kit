# Skills and Rules Guide

> When to write a skill vs. a rule vs. a hook. How to author each. Anti-bloat invariants.

---

## The mental model

| Mechanism | Applied when | Describes | Analogy |
|---|---|---|---|
| **Rule** | Every prompt (automatic) | *How* the agent should behave | Standing orders |
| **Skill** | Only when triggered | *What* the agent should produce | Job description |
| **Hook** | On IDE events (session start/end) | *When* to run a shell command | Cron for the IDE |
| **AGENTS.md** | On file access in a folder | *Facts* about the code / conventions | The employee handbook |

**Rule of thumb:**
- If you repeat "please do X" in prompts 3+ times → write a **rule**.
- If you have a recurring task with a predictable output structure → write a **skill**.
- If you want something to happen automatically without a prompt → write a **hook**.
- If it's a fact about *this codebase* that the agent needs to know → put it in **AGENTS.md**.

---

## Rules in depth

### File location

`.cursor/rules/<name>.mdc` (or under subdirectories like `.cursor/rules/process/` and `.cursor/rules/engineering/` for organization).

### Anatomy

```markdown
---
alwaysApply: true                    # or: false + globs
description: "One-line description"
---

# <Rule Name>

<Body: what the rule enforces, when it applies, and the do-this-not-that pattern.>

<Optional: pair-with references to related rules.>
```

### When to use `alwaysApply` vs. globs

- `alwaysApply: true` — universal behaviors (e.g., coding-behavior, chat-context, source-of-truth).
- `globs: ["**/*.tsx"]` — file-type-specific behaviors (e.g., react-specific rules).
- Explicit trigger conditions in the body — process rules that fire based on user intent ("when the user shares an epic...").

### Trigger-style rules vs. invariant rules

**Trigger rules** in the process/ folder respond to intent:
- "When the user starts implementation of a card and the PR Plan is empty → read the layered-pr-planner skill."

**Invariant rules** in the engineering/ folder describe posture:
- "Every pull request must have exactly one primary capability layer."

Both are always applied; the difference is that trigger rules point to skills, invariant rules describe constraints.

### Anti-bloat invariants

From the Alpha setup:

- Keep rules short. 20–80 lines is typical; over 120 is a smell.
- No inlined content from big files (`.cursor/LEARNINGS.md`, external docs). Rules point; skills load.
- No duplicated content across rules. Split by concern.
- If two rules trigger the same behavior, merge them.

### Rule vs. AGENTS.md — a common confusion

**AGENTS.md** describes *this codebase*: the tech stack is X, the layers are Y, the commands are Z.

**Rules** describe *how the agent should behave*: use TDD; don't refactor adjacent code; write typed error classes.

If you're not sure: does the content change per-repo (facts about code) or apply across repos (posture)? Facts → AGENTS.md. Posture → rules.

---

## Skills in depth

### File location

`.cursor/skills/<skill-name>/SKILL.md` (one folder per skill).

### Anatomy

```markdown
---
name: skill-name
description: One-line description shown in the skill picker.
when_to_use: Concrete user intent that should trigger this skill.
allowed-tools: Read Write CallMcpTool AskQuestion
context: fork                        # or: main
---

# Skill Name

<Purpose in 1–2 sentences.>

## Inputs
- Explicit list of parameters (ticket key, milestone name, capability, etc.)

## Procedure

### 1. <Step name>
<What to do; if calling an MCP, read the descriptor first.>

### 2. <Step name>
...

### N. <Final validation step>

## Out Of Scope
- Explicit list of things this skill does NOT do; points to sibling skills.

## Guardrails
- Read before write.
- Ask focused questions when material context is missing.
- Append-only on shared artifacts.
```

### When to write a skill

The skill test:
1. Can I describe the output structure precisely? (No → too fuzzy for a skill; use a prompt or template.)
2. Will I run this same procedure 5+ times in the project? (No → one-off prompt is fine.)
3. Does the procedure need external tools (MCPs, files, ticketing) in a specific order? (Yes → skill.)

### Skill naming

- Use kebab-case with a verb-noun structure: `epic-implementation-planner`, `phase-card-generator`.
- Avoid generic names (`helper`, `utility`).
- Don't prefix with `mt-` or your org name — skills should be portable.

### Skill composition

Skills can reference each other. Example: `phase-card-generator` calls `ticket-prd-builder` for each card it creates. Encode the composition in the "Out Of Scope" section: "Filling the ticket's 10 mandatory sections is handled by `ticket-prd-builder`."

---

## Hooks (brief)

Not shipped in this kit — hooks are environment-specific. But if you want them:

**Useful patterns:**

| Hook | Event | Purpose |
|---|---|---|
| `keep-awake-start.sh` | `sessionStart` | Stop the machine sleeping during long agent runs |
| `pending-items-reconcile.sh` | `sessionEnd` | Append session-end marker to a tracker file |
| `coaching-prompt-check.sh` | `beforeSubmitPrompt` | Check the prompt for triggers of installed skills; warn |

**Anti-pattern:** overusing hooks. Users hate opaque behavior. 2 hooks max in most setups.

---

## Authoring a new skill — the process

1. **Notice a recurring task.** If you've prompted the same procedure 5+ times, it's a skill candidate.
2. **Write the procedure in a doc** first — just prose, no frontmatter.
3. **Extract the inputs, steps, and guardrails.** Add frontmatter (name, description, when_to_use, allowed-tools).
4. **Put it in `.cursor/skills/<name>/SKILL.md`.**
5. **Add a trigger rule** in `.cursor/rules/process/` that references the skill:
   ```
   When [trigger condition], read `.cursor/skills/<name>/SKILL.md` and follow it.
   ```
6. **Test.** Prompt the agent with the trigger condition and see if the skill fires and produces the right output.
7. **Iterate.** Skills usually need 2–3 revisions before they're stable.

---

## Authoring a new rule — the process

1. **Notice a repeat.** Same "please do X" 3+ times.
2. **Draft the rule.** Frontmatter (alwaysApply, description) + body.
3. **Keep it under 80 lines.** If longer, split by concern.
4. **Put it in `.cursor/rules/`.**
5. **Reload the IDE** so the rule is picked up.
6. **Verify.** Prompt with a scenario that should trigger the rule; see if it holds.

---

## Retiring a rule / skill

Rules and skills accumulate. When one is superseded, obsolete, or unused:

1. Add a note to `.cursor/LEARNINGS.md` capturing *why* it's being retired.
2. Delete the rule / skill file.
3. Update any AGENTS.md, README, or trigger references that mentioned it.

**Example from MT:** the `dip/` skill was retired when `epic-implementation-planner` + `ticket-prd-builder` replaced it. The `.cursor/rules/jira-source-of-truth.mdc` explicitly notes this: "The legacy `.cursor/skills/dip/` flow is superseded and must not be used for new milestones."

---

## Cadence of maintenance

| Period | What to do |
|---|---|
| Weekly | Skim the coaching log (if you have one). Repeat tips → new snippet or skill candidate. |
| Per milestone | Retro: which rules fired most? Which never fired? Any redundant ones? |
| Per model upgrade | Skills and prompts may need retuning. See `docs/09-prompt-versioning.md`. |
| Per project end | Push durable rules/skills into your personal starter kit copy. |

---

## Portable skills in this kit

10 skills, ordered by frequency of use:

1. **`tdd-bdd-workflow`** — used on every feature. Enforces red-green-refactor.
2. **`ticket-prd-builder`** — used per ticket. Expands to 10 sections.
3. **`layered-pr-planner`** — used per ticket at implementation start.
4. **`pr-readiness-check`** — used before every PR open.
5. **`phase-card-generator`** — used once per phase.
6. **`epic-implementation-planner`** — used once per epic.
7. **`phase-report-writer`** — used once per phase close.
8. **`prd-sync`** — used on divergence detection.
9. **`agent-learning-loop`** — used on repeat mistakes.
10. **`lld-creation`** — used on complex phases.

---

## Portable rules in this kit

21 rules across two categories:

### Process (10 rules)
Trigger + invariant rules that drive the SDD lifecycle. Copy all 10.

### Engineering (11 rules)
Tech-agnostic coding standards. Copy the ones that match your stack; drop the rest.

**None of these are optional if you want the full SDD workflow.** Skip a process rule and the lifecycle breaks silently.
