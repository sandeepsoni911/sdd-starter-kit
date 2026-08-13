# Example — Feature-Phase File (sanitized from M4 Phase 1)

> Real per-phase feature file from Project Alpha, sanitized. Shows how a single feature within a phase is scoped end-to-end.

```markdown
---
milestone: M4-audit-trail
epic: <PROJECT>-543
phase: 1
feature: history-ui-master-data
layers: [feature, experience]
tickets: [<PROJECT>-548, <PROJECT>-549]
last_updated: 2026-05-21
status: implemented
---

# History UI — Master Data Detail Panels

> Milestone: [M4-audit-trail](../../implementation-plan.md) | Phase: 1
> Tickets: [<PROJECT>-548](<URL>), [<PROJECT>-549](<URL>)

## Purpose

Wire the shared `<HistoryPanel>` component into the detail panels of all master data entities (Properties, Methods, Batteries, Labs, MPS). A user opening any entity's detail can click a "History" tab and see the paginated audit trail for that entity.

## Scope

**In scope:**
- New `<HistoryPanel>` component in `feature/src/components/shared/HistoryPanel.tsx`
- `useAuditHistory(entityType, entityId, pageSize)` hook
- History tab wiring on 5 detail panels: `PropertyDetailPanel`, `MethodDetailPanel`, `BatteryDetailPanel`, `LabDetailPanel`, `MPSDetailPanel`
- Mock stub for history endpoint in `experience/src/mock-api.ts`

**Out of scope:**
- ABC matrix per-row history (Phase 2)
- Test Request history (Phase 3)
- Audit record capture (already exists from M1/M2)
- Audit table extraction (separate ticket in Phase 1)

## Acceptance criteria

- [ ] AC1: Each master-data detail panel has an "Overview" and "History" tab.
- [ ] AC2: Clicking "History" fetches audit records for that entity via `GET /v1/{entityType}/{entityId}/history?pageSize=20`.
- [ ] AC3: History renders as a chronological list (newest first) with actor, timestamp, field, old value, new value, action.
- [ ] AC4: Pagination: "Load more" button appears when `nextToken` is non-null.
- [ ] AC5: Empty state shown when no audit records exist.
- [ ] AC6: Loading skeleton shown while fetching.
- [ ] AC7: Error state shown on API failure with retry action.

## Design

[Figma link — omitted in this example]

## Layer implementation

### Feature

**Components:**
- `feature/src/components/shared/HistoryPanel.tsx` — presentational; consumes items + pagination handlers.

**Hooks:**
- `feature/src/hooks/useAuditHistory.ts`:
  \`\`\`typescript
  useAuditHistory(entityType, entityId, pageSize = 20)
    → { items, isLoading, error, hasMore, loadMore }
  \`\`\`

**Wiring per detail panel:**
- `PropertyDetailPanel.tsx` — add `<Tabs>` with "Overview" + "History"; render `<HistoryPanel entityType="property" entityId={property.id} />` in the History tab.
- Same pattern for Methods, Batteries, Labs, MPS.

**Design tokens used:**
- Table borders, actor/timestamp typography, action colors (ADDED = green, CHANGED = amber, REMOVED = red).

### Experience

- Extend `mock-api.ts` with `history` endpoint returning fixture audit entries.
- LocalStack HTTP adapter passes through to the real backend.

## E2E scenarios

1. **Happy path — open history:**
   - Given: property with 3 audit records exists
   - When: user opens the property detail and clicks "History"
   - Then: 3 audit rows shown, newest first, with actor + timestamp + field change

2. **Pagination:**
   - Given: entity with 25 audit records exists (pageSize=20)
   - When: user opens History
   - Then: 20 rows shown + "Load more" button; clicking it loads the remaining 5

3. **Empty state:**
   - Given: freshly created entity with no history
   - When: user opens History
   - Then: empty state "No history yet" shown

4. **Error state:**
   - Given: API returns 500
   - When: user opens History
   - Then: error alert with retry button; retry re-fetches

## Test strategy

- **Domain:** no changes.
- **Data-access:** no changes (endpoint tested in sibling ticket).
- **Feature:** 
  - `HistoryPanel.test.tsx` — renders items, empty state, loading, error, load-more.
  - `useAuditHistory.test.ts` — hook fetches, paginates via `nextToken`, handles errors.
  - `PropertyDetailPanel.test.tsx` — tab switch renders `<HistoryPanel>` (integration).
- **Experience:** mock adapter test.

## Change log

- 2026-05-21: Initial draft.
- 2026-06-02 (PR #789): Split from original single ticket into two tickets (component + wiring) after size estimate exceeded ~400 lines.
```

## What makes this feature-phase file work

- **Scope in/out is explicit** — ABC matrix history and Test Request history are deferred to Phase 2/3, not lost.
- **ACs are testable** — every AC maps to at least one E2E scenario.
- **Layer-by-layer implementation** — the dev pair knows which files to touch in what order.
- **E2E scenarios cover happy + edge + error** — no scenario like "user is happy."
- **Change log captures splits** — a mid-phase decision to split into two tickets is recorded, not silent.
