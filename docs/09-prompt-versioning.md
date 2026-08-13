# Prompt Versioning Discipline

> Prompts age. Models improve. What worked with the 2025 generation of models needs re-tuning for the 2027 generation. This doc explains how to version, retire, and evolve the prompts in this kit.

---

## Why prompts age

1. **Model instruction-following improves.** Prompts written to compensate for weak instruction-following become bloated when the model gets sharper.
2. **New capabilities become native.** Prompts that manually orchestrated multi-step reasoning become obsolete when the model does it natively.
3. **New failure modes emerge.** Prompts written to prevent one failure mode become blind to a new one.
4. **Vocabulary drifts.** Terms like "AGENTS.md", "tool use", "MCP" evolve.

**Rule of thumb:** re-review prompts every model generation (~6 months). Every 12–18 months, expect a substantive rewrite.

---

## Prompt file structure

Every prompt in `prompts/` follows this structure:

```markdown
# <Prompt Name>

**Version:** 1
**Last tuned:** YYYY-MM-DD against <MODEL>
**Purpose:** One-line description.

## Inputs

- Bullet list of inputs the caller must provide.

## Prompt

```
<The actual prompt text, ready to copy-paste.>
```

## Expected output structure

- Bulleted list of sections the output should contain.

## Validation checklist

- [ ] Section A is present and populated
- [ ] Section B references real files
- [ ] No fictional example paths
- [ ] Change Log entry appended

## Known failure modes

- Fictional acceptance criteria — mitigate by requiring "cite the source" in the prompt
- ...

## Change Log

- 2026-01-15 (v1): initial version tuned for GPT-4 / Claude 3.5.
```

---

## Versioning scheme

**Major (v1 → v2):** the structure of the output changes (new sections; sections renamed; format changed).

**Minor (v1.0 → v1.1):** the prompt text is adjusted but the output structure is unchanged.

**Patch (v1.1.0 → v1.1.1):** typo fixes, clarifications.

**Where to store older versions:** in `prompts/archive/`. Never delete — someone may still be on the older model.

---

## Retirement

When a prompt is fully obsolete:

1. Move to `prompts/archive/`.
2. Add an entry to the prompt's own Change Log: `RETIRED YYYY-MM-DD (reason).`
3. Update any doc that referenced it.
4. Note it in `.cursor/LEARNINGS.md` — someone will ask why it's gone.

**Never edit a retired prompt** — it's a historical record.

---

## Promotion — new prompt candidacy

A prompt is a candidate for promotion into the kit when:

1. You've run the same procedure via a chat prompt 5+ times.
2. The output structure is consistent across those runs.
3. It's not covered by an existing skill.

**Skills vs. prompts:** skills are prompts + procedure + tool calls. If your prompt needs the agent to call an MCP or write a file in a specific location, promote to a skill. If it's a pure "write me this document" prompt, keep it as a prompt.

---

## Testing a prompt after a model upgrade

When you switch to a new model version, run every prompt through this test:

1. **Feed it the same inputs** you used last time.
2. **Compare the output** to the previous run.
3. **Score:**
   - **Structure preserved?** (headings, sections, order)
   - **Content quality?** (specificity, hallucinations, factual grounding)
   - **Length in bounds?** (not padded, not truncated)
   - **Guardrails held?** (didn't invent, didn't hallucinate, cited sources)

4. If any dimension regressed → tune the prompt (bump minor version) or promote a warning (bump major version).

---

## The prompts in this kit

| Prompt | Version | Last tuned |
|---|---|---|
| `prompts/01-prd-from-transcripts.md` | 1 | 2026-07 against GPT-5 / Claude 4 |
| `prompts/02-figma-manifest.md` | 1 | 2026-07 |
| `prompts/03-technical-solution.md` | 1 | 2026-07 |
| `prompts/04-ui-strategy.md` | 1 | 2026-07 |
| `prompts/05-implementation-phases.md` | 1 | 2026-07 |
| `prompts/bootstrap-agents-md.md` | 1 | 2026-07 |
| `prompts/monday-kickoff.md` | 1 | 2026-07 |
| `prompts/friday-wrap.md` | 1 | 2026-07 |

Update the table when you tune a prompt.

---

## When to break out of the versioning discipline

- **One-off prompts.** Don't version prompts you'll never use again.
- **Personal prompts.** Only kit-shipped prompts need this discipline.
- **Experimental prompts.** Mark clearly (`prompts/experimental/`) and iterate freely. Promote when stable.

---

## Anti-patterns

| Anti-pattern | Why it hurts | Do this instead |
|---|---|---|
| Editing prompts silently | Nobody knows the current state | Every edit bumps the version + Change Log |
| Keeping prompts inline in a giant doc | Not versionable, not shareable | Extract to standalone files |
| Over-engineering the prompt for one bad output | Prompt bloats with defensive language | Fix the one bad output in review; don't add another guardrail |
| Not testing after model upgrades | Silently degraded outputs | Run all prompts through the test above on every model upgrade |
| Cargo-culting long prompts from Twitter | Prompt not tuned for your task | Adapt, don't copy; measure output |
