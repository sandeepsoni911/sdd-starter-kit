# UI Component Strategy — [MILESTONE / FEATURE NAME]

**Status:** [Draft | In Review | Approved]
**Owner (Frontend Lead):** [Name]
**Design reviewer:** [Name]
**Last updated:** YYYY-MM-DD
**Version:** 1

Related: [PRD](../prd.md) | [Technical Solution](./technical-solution.md)

---

## Purpose

Every UI element in every Figma screen maps to one of three categories:

1. **Reuse from design system** — already exists in `[DESIGN_SYSTEM]`.
2. **Reuse from internal component library** — already exists in this codebase.
3. **Build new** — must be custom.

If we skip this doc, the AI agent will happily build `Button`, `Modal`, `Toast` components that already exist. This doc prevents that.

---

## Design system inventory

**Design system in use:** [name + version]

**Location:** [package name / repo path]

**Storybook:** [URL]

**Design tokens:** [How they're consumed — CSS vars? JS objects?]

---

## Screen-by-screen breakdown

### Screen: [Screen name]

| Figma element | Category | Component | Notes / props |
|---|---|---|---|
| Primary CTA button | Reuse (DS) | `Button` from `@[DS]/pre` | variant="primary" |
| Search input | Reuse (DS) | `TextField` from `@[DS]/pre` | icon="search" |
| Filter chips | Reuse (internal) | `ChipList` from `components/common/` | ... |
| Data table | Reuse (internal) | `DataTable` from `components/data-table/` | Uses TanStack Table |
| Custom widget | Build new | `MetricSparkline` | New — see design token map |

### Screen: [Screen name 2]

...

---

## New components to build

| Component | Path | Purpose | Props | Design tokens |
|---|---|---|---|---|
| `MetricSparkline` | `feature/src/components/metrics/` | Tiny inline chart | `data, height, color` | Uses `--pds-color-accent` |

For each new component:

- **Accessibility notes:** ARIA labels, keyboard navigation, focus management
- **State variants:** default, hover, active, disabled, loading, error
- **Responsive behavior:** mobile / tablet / desktop
- **Test strategy:** what does a component test cover?

---

## Design tokens used

| Token | Where used | Value (dark) | Value (light) |
|---|---|---|---|
| `--pds-color-accent` | CTAs, links | ... | ... |
| ... | ... | ... | ... |

---

## Styling approach

- **Utility CSS:** [Tailwind? / vanilla-extract? / CSS Modules?]
- **Theme:** [How is the theme applied? Provider? CSS vars?]
- **Icon library:** [Which one? How imported?]

---

## Accessibility checklist

- [ ] All interactive elements reachable by keyboard
- [ ] Color contrast meets WCAG 2.1 AA
- [ ] ARIA labels on decorative-only elements
- [ ] Focus visible on all interactive elements
- [ ] Screen reader tested (VoiceOver / NVDA)

---

## Anti-patterns to avoid

- Do not create `Button`, `Card`, `Modal`, `Toast`, `Input`, `Select`, `Table` — all exist in `[DESIGN_SYSTEM]`.
- Do not use hex color literals; use design tokens.
- Do not create one-off `TextField` variants; use existing variants or propose a design system PR.

---

## Change log

- YYYY-MM-DD (v1): Initial draft
