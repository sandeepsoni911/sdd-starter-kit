# Prompt 1 — Draft a PRD from meeting transcripts

**Version:** 1
**Last tuned:** 2026-07 against GPT-5 / Claude 4
**Purpose:** turn discovery-phase meeting transcripts + design context into a first-pass PRD.

## Inputs

- One or more discovery meeting transcripts (`@discovery/meeting-transcripts/*.md`)
- Product summary from the product owner (short)
- Design context (Figma link + brief description if available)
- Target audience: [describe the persona]

## Prompt

```
You are drafting a Product Requirement Document (PRD) using the transcripts and context I've shared.

Use the template at @templates/prd.md as the exact structure.

Your job:
1. Extract user stories from the transcripts. Attribute each to the participant who described it.
2. For each user story, extract testable acceptance criteria. If the transcript doesn't include an AC, mark it as "TBD" and add an open question with an owner.
3. Extract non-functional requirements. Quantify them where possible. If the transcript says "fast", ask what "fast" means in the Open Questions.
4. Capture out-of-scope items explicitly. These are as important as the goals.
5. List every unresolved question with an owner and due date.

Non-negotiables:
- Do NOT invent acceptance criteria not grounded in a transcript or explicit product input.
- Do NOT drop the "Open Questions" section — an empty PRD without open questions is a red flag.
- If a section has no material content, write "None captured in discovery — needs product follow-up."
- Cite the transcript source for each user story (e.g. "See @discovery/meeting-transcripts/2026-01-15.md, minute 22").

Output: a complete markdown PRD ready for review, in the shape of @templates/prd.md.
```

## Expected output structure

Matches `@templates/prd.md`:
- Executive summary
- Problem statement + user personas
- Goals + non-goals
- User stories with acceptance criteria + citations
- Functional requirements (by screen / flow)
- Non-functional requirements (quantified)
- Out of scope
- Open questions with owners
- Success metrics
- Change log

## Validation checklist

- [ ] Every user story has at least one AC or an "TBD" marker with an open question.
- [ ] Every NFR is quantified or has an open question ("What P95 target?").
- [ ] Every user story cites the transcript source.
- [ ] Open Questions section is not empty.
- [ ] Out-of-scope section is not empty.

## Known failure modes

- **Fictional ACs.** Mitigate by requiring transcript citation on every story.
- **Padded content.** Mitigate by requiring "None captured" placeholder for empty sections.
- **Missing NFRs.** Mitigate by explicitly listing perf, security, accessibility, observability in the prompt.

## Change log

- 2026-07-XX (v1): Initial version.
