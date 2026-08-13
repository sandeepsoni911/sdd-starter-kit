# Prompt 2 — Generate a Figma design manifest

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** turn a set of Figma screen exports into a structured manifest that maps each screen to a route, layout, and component tree.

## Inputs

- Figma exports (PDF or PNG per screen) in `discovery/figma-exports/`
- Figma file URL for reference
- PRD (as context)

## Prompt

```
You are generating a Figma design manifest.

I've exported the Figma screens to @discovery/figma-exports/. The PRD is at @docs/discovery/prd.md.

Your job:
1. For every screen export, extract:
   - Screen name (from the Figma page/frame name)
   - Route path (propose one — /entities, /entities/[id], etc.)
   - Layout: header, sidebar, main content, footer regions
   - States shown: default, loading, error, empty, hover, disabled (mark which are missing)
   - Interactive elements: buttons, inputs, dropdowns, tables — with their labels
   - Content: real copy from the design, not lorem ipsum
2. Group screens by user flow. Flows come from the PRD user stories.
3. Flag missing states. Every interactive screen should have loading + error + empty states.
4. Flag inconsistencies. If two similar screens use different button styles or spacing, note it.

Non-negotiables:
- Do NOT invent content. If a label is not readable in the export, mark it as "[illegible — verify]".
- Do NOT propose component names yet. That's Prompt 4 (UI Component Strategy).
- Every screen must have a route.

Output: a markdown manifest with one section per user flow, one subsection per screen.
```

## Expected output structure

```markdown
# Figma Design Manifest

## Flow: [name]

### Screen: [name] (path: /entities)

- Layout: [description]
- States present: default ✓, loading ✗ (missing), error ✗ (missing), empty ✓
- Interactive elements:
  - Button "Create new entity" (top right)
  - Search input (top center)
  - Data table with columns [X, Y, Z]
- Content: [key copy]
- Open questions: what happens when the user clicks the row action?

### Screen: [name 2] (path: /entities/[id])
...
```

## Validation checklist

- [ ] Every screen has a route
- [ ] States present / missing are explicitly listed
- [ ] Missing states → open question raised
- [ ] Real content extracted (not lorem)

## Known failure modes

- **Invented states.** Mitigate by requiring the "states present ✓ / missing ✗" table.
- **Made-up copy.** Mitigate by requiring transcript-style citations.

## Change log

- 2026-07-XX (v1): Initial.
