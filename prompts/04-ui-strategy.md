# Prompt 4 — Generate the UI Component Strategy

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** map every UI element in every Figma screen to a design-system component, an internal component, or a new custom component.

## Inputs

- Figma manifest (`@docs/discovery/figma-manifest.md`)
- PRD (`@docs/discovery/prd.md`)
- Root `AGENTS.md` (tech stack + design system)
- Storybook / design system inventory URL
- Existing components list (path in repo, e.g. `capabilities/**/feature/src/components/**`)

## Prompt

```
You are generating a UI Component Strategy.

Inputs:
- Figma manifest: @docs/discovery/figma-manifest.md
- PRD: @docs/discovery/prd.md
- Design system: [DESIGN_SYSTEM_NAME] at [package]
- Existing internal components: @capabilities/**/feature/src/components/**

Use the template at @templates/ui-strategy.md.

Your job:
1. For every UI element on every Figma screen, categorize:
   - **Reuse from design system** — component exists in [DESIGN_SYSTEM]. Cite the component name.
   - **Reuse from internal library** — component exists in this codebase. Cite the file path.
   - **Build new** — no existing component fits. Justify why.
2. For every "Build new" component, provide:
   - Path where it will live
   - Purpose
   - Props signature
   - Design tokens used
   - Accessibility notes (ARIA, keyboard, focus)
   - State variants (default, hover, active, disabled, loading, error)
3. List every design token used (color, spacing, typography) with its role.
4. Confirm the styling approach (Tailwind, CSS Modules, etc.) matches AGENTS.md.
5. Flag any Figma element that violates a design system rule (custom color instead of token, non-standard spacing, etc.).

Non-negotiables:
- Do NOT propose building a "Button", "Card", "Modal", "Toast", "Input", "Select", "Table" without checking the design system inventory first.
- Every "Build new" needs a written justification of why no existing component fits.
- Every UI element must map to exactly one category.

Output: a complete UI Component Strategy markdown per @templates/ui-strategy.md.
```

## Expected output structure

Matches `@templates/ui-strategy.md`.

## Validation checklist

- [ ] Every UI element categorized
- [ ] "Build new" components have justification
- [ ] Design tokens listed
- [ ] Accessibility notes for new components
- [ ] Design system violations flagged

## Known failure modes

- **Proposes building common components.** Mitigate by hard-coding the "Do not build without justification" list in the prompt.
- **Missing accessibility notes.** Mitigate by requiring a11y section per new component.

## Change log

- 2026-07-XX (v1): Initial.
