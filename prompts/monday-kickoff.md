# Prompt — Monday Kickoff Ritual

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** start the week with a grounded plan; avoid drift into reactive mode.

**When to use:** Monday morning, fresh chat.

## Inputs

- Last week's Friday wrap (if you did one)
- Open tickets assigned to you or your team in `<TICKETING_SYSTEM>`
- `pending-items-tracker.md` (your persistent tracker)

## Prompt

```
It's Monday. Help me plan the week.

Read these:
- @pending-items-tracker.md — my open items
- Last Friday's wrap (if it exists): @docs/waysofworking/wraps/friday-<last-friday>.md
- Ticketing: fetch tickets assigned to me in the current sprint. Use the <TICKETING_MCP>.

Produce:
1. **Last week recap** (3 bullets max): what shipped, what didn't, what was surprising.
2. **This week priorities** (3–5 bullets): each anchored to a specific ticket or milestone deliverable.
3. **Open risks** (bulleted): things I should watch. If none, say "None material."
4. **Open questions I owe someone** (bulleted with owner + due date): items where the ball is in my court.
5. **One meta-question** for me: something I might be missing based on the pending tracker.

Non-negotiables:
- No padding. If a section has nothing real, write "Nothing material this week."
- No generic advice ("focus on deep work"). Specific to the tickets and tracker.
- Save the output to @docs/waysofworking/kickoffs/monday-<today>.md so I can reference it later.
```

## Expected output

A one-page markdown brief. Copy-paste-ready into Slack or 1:1 notes.

## Validation checklist

- [ ] Every priority is anchored to a specific ticket
- [ ] Open questions have owners + dates
- [ ] File saved to `docs/waysofworking/kickoffs/monday-<YYYY-MM-DD>.md`
- [ ] No "focus on deep work" style filler

## Known failure modes

- **Padded content.** Mitigate by requiring "Nothing material" placeholder.
- **Generic advice.** Mitigate by requiring ticket-anchored priorities.

## Change log

- 2026-07-XX (v1): Initial extraction from `cursor-prompt-rituals.md`.
