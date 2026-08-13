# Example — Implementation Plan (sanitized from M4 Audit Trail)

> Real milestone plan from the Project Alpha program, sanitized. Shows a three-phase delivery with concrete phase boundaries and exit criteria.

```markdown
---
milestone: M4-audit-trail
epic: <PROJECT>-543
last_updated: 2026-05-21
status: draft
summary: >
  Three-phase delivery of Audit Trail & View History — extract audit records
  from the shared table into a dedicated audit table (Phase 1), then add MPS
  matrix row history (Phase 2), then test-request history (Phase 3).
---

# Implementation Plan — M4 Audit Trail & View History

> Epic: [<PROJECT>-543](<Epic URL>)

## TL;DR

**Current state:** All records — master data (Properties, Methods, Batteries, Labs, MPS), test requests, and the field-level audit entries written by M1/M2 — live in **one shared DynamoDB table**. The capture pipeline works; the data is co-located with everything else.

**M4 change:** Create a dedicated `{prefix}-audit-table`, migrate existing audit records, redirect audit writes, add an `entity-history-handler` endpoint, and wire the `<HistoryPanel>` React component into each entity's detail view.

**Phase structure:**
- **Phase 1** — Dedicated audit table + migration + write redirect + history endpoint + shared UI components + master-data History tabs (Properties, Methods, Batteries, Labs, MPS).
- **Phase 2** — ABC matrix per-property-method row history.
- **Phase 3** — Test Request "History" tab.

## Phase Overview

| Phase | Name | Goal | Est. Cards | Dependencies |
|---|---|---|---|---|
| 1 | Table Extraction + History API + Master Data UI | New audit table, migration, write redirect, endpoint, `<HistoryPanel>`, entity pages | ~6–7 | Terraform access; confirm audit record format from M1/M2 |
| 2 | ABC Matrix History | Per-property-method row "View History" panel | ~2 | Phase 1 merged |
| 3 | Test Request History | "History" tab on Test Request detail page | ~2 | Phase 2 merged |

---

## Phase 1 — Dedicated Audit Table + History API + Master Data UI

**Goal:** Extract audit records from the shared table; provision the dedicated `{prefix}-audit-table`; migrate existing records; redirect audit writes; deliver the history query endpoint and the `<HistoryPanel>` UI for all master data entities.

**Audit Entry Schema (field-level):**

| Field | Type | Notes |
|---|---|---|
| `PK` | `string` | `{entityType}#{entityId}` |
| `SK` | `string` | `AUDIT#{timestamp}#{auditId}` |
| `id` | `string (UUID)` | Audit entry identifier |
| `entityType` | `string` | e.g. `'property'`, `'lab'`, `'mps'` |
| `entityId` | `string` | ID of the entity that was changed |
| `field` | `string` | Name of the field that changed (`_entity` = create) |
| `oldValue` | `string \| null` | Previous value (`null` for create events) |
| `newValue` | `string \| null` | New value (`null` for delete events) |
| `action` | `AuditAction` | `ADDED`, `CHANGED`, or `REMOVED` |
| `updatedBy` | `string` | Actor identity |
| `updatedAt` | `string` | ISO 8601 timestamp |

**API endpoint:**

\`\`\`
GET /v1/{entityType}/{entityId}/history?pageSize=20
\`\`\`

Response: `{ items: AuditHistoryEntry[], nextToken: string | null }`

**Prerequisites:**

- Terraform access to provision a new DynamoDB table in dev/staging/prod.
- Pre-migration scan count per environment documented before migration runs.
- `AUDIT_TABLE_NAME` agreed on as the env var convention for the new table.

**Exit Criteria:**

- [ ] `{prefix}-audit-table` provisioned with `PAY_PER_REQUEST`, PITR enabled, `PK` + `SK` keys.
- [ ] Migration script: scan shared table for `SK begins_with "AUDIT#"` → BatchWrite to audit table. Pre/post count validates zero data loss. Script is idempotent.
- [ ] All Lambdas receive `AUDIT_TABLE_NAME` env var. IAM policy: `PutItem` + `Query` on audit table.
- [ ] All service-layer audit writes redirected to `AUDIT_TABLE_NAME`.
- [ ] `AuditHistoryEntry` domain type matches spec.
- [ ] `entity-history-handler` responds with the paginated shape.
- [ ] `pageSize` defaults to 20, capped at 100. Returns HTTP 400 for unknown `entityType`.
- [ ] `<HistoryPanel>` component renders paginated audit list with load-more.
- [ ] History tab wired to Properties, Methods, Batteries, Labs, ABC detail panels.

**Expected Layers:**
- **domain**: `AuditHistoryEntry` type, `AuditAction` enum
- **data-access**: `DynamoEntityHistoryRepository`, service redirect
- **functions**: `entity-history-handler` (list history)
- **infrastructure**: New DynamoDB table, IAM policies, Lambda env var, migration Lambda
- **feature**: `<HistoryPanel>` component + hook + service call + History tab wiring
- **experience**: Mock adapter stub for history endpoint

**Cards (created by `phase-card-generator`):**
- <PROJECT>-544: Domain: AuditHistoryEntry type
- <PROJECT>-545: Data-access: DynamoEntityHistoryRepository + service redirect
- <PROJECT>-546: Functions: entity-history-handler
- <PROJECT>-547: Infrastructure: audit table + migration Lambda
- <PROJECT>-548: Feature: HistoryPanel + hook
- <PROJECT>-549: Feature: History tab wiring on master data detail panels

---

## Phase 2 — ABC Matrix History

**Goal:** Add a per-property-method row "View History" panel on the ABC matrix component.

**Prerequisites:**
- Phase 1 merged.
- `<HistoryPanel>` component reusable.
- Audit records already capture ABC matrix cell changes (from M2).

**Exit Criteria:**
- [ ] Row action "View History" appears in ABC matrix.
- [ ] Clicking opens `<HistoryPanel>` filtered to that property-method pair.

**Expected Layers:**
- **feature**: ABC matrix row action + history modal

**Cards:**
- <PROJECT>-560: Feature: ABC matrix history row action

---

## Phase 3 — Test Request History

**Goal:** Add a "History" tab to Test Request detail page.

**Prerequisites:**
- Phase 2 merged.
- Test Request domain (from M3) exists.

**Exit Criteria:**
- [ ] History tab appears on Test Request detail.
- [ ] `<HistoryPanel>` shows test-request-scoped audit entries.

**Expected Layers:**
- **feature**: Test Request detail tab wiring

**Cards:**
- <PROJECT>-570: Feature: Test Request History tab

---

## Open Questions

- Do we retain audit records past 3 years? (Blocked on compliance sign-off.)

## Change Log

- 2026-05-21: Initial plan derived from Epic <PROJECT>-543.
```

## What makes this plan work

- **TL;DR paragraph up front** — reviewer understands scope in 30 seconds.
- **Phase overview table** — dependency chain is explicit.
- **Exit criteria are checkable** — no ambiguity about "done."
- **Cards listed with layer tags** — dev pair knows the layer taxonomy for Phase 1 kickoff.
- **Prerequisites are non-empty** — flags cross-team dependencies (Terraform access, compliance sign-off).
- **Change log at bottom** — future divergences get appended, not overwritten.
