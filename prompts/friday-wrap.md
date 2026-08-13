# Prompt — Friday Wrap Ritual

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** produce an honest, ticket-grounded weekly status update. Never shows up empty.

**When to use:** Friday afternoon, fresh chat.

## Inputs

- Monday kickoff of this week (if you did one)
- Merged PRs from this week
- Tickets that changed status this week
- `pending-items-tracker.md`

## Prompt

```
It's Friday. Write my weekly status update.

Read these:
- @docs/waysofworking/kickoffs/monday-<this-monday>.md — this week's plan
- Merged PRs this week: `gh pr list --author @me --state merged --search "merged:>=<monday>"`
- Ticketing: fetch tickets I closed / moved this week. Use the <TICKETING_MCP>.
- @pending-items-tracker.md

Produce 4 sections. No filler.

**What shipped this week:**
- One bullet per PR merged. Include ticket link + one-line outcome. If nothing shipped, say "No merges this week — [reason]."

**What didn't ship (and why):**
- Anything from Monday's plan that slipped. Reason.
- If everything shipped, say "All Monday-planned items shipped."

**Risks / open items:**
- Things I owe someone. From the pending tracker.
- Blockers on my critical path.
- If none material, say "Nothing material."

**Next week focus:**
- 3 bullets max. Preview of Monday kickoff.

Non-negotiables:
- Bullet-heavy, not prose.
- Ticket-grounded. Every "did X" bullet links to a ticket or PR.
- Honest about slippage. Do NOT hide items that didn't ship.
- If a section has nothing real, write "Nothing material this week." Do not pad.

Save output to @docs/waysofworking/wraps/friday-<today>.md.
```

## Expected output

A four-section markdown suitable for Slack thread or email to leadership.

## Validation checklist

- [ ] Every shipped item links to a PR or ticket
- [ ] Slipped items are named, not hidden
- [ ] No filler (paragraphs, generic advice)
- [ ] File saved to `docs/waysofworking/wraps/friday-<YYYY-MM-DD>.md`

## Known failure modes

- **Padded / sanitized output.** Mitigate by requiring honesty about slippage.
- **Prose instead of bullets.** Mitigate by explicit bullet requirement.

## Change log

- 2026-07-XX (v1): Initial extraction from `cursor-prompt-rituals.md`.
