# Traceability Matrix — [Milestone Name]

**Purpose:** map every PRD requirement to a phase, feature, ticket, PR, and test. Filled progressively as milestone progresses.

**Last updated:** YYYY-MM-DD
**Milestone:** M<N>-<short-name>

---

## Matrix

| PRD ID | PRD requirement / AC | Phase | Feature | Ticket | PR(s) | Test(s) | Status |
|---|---|---|---|---|---|---|---|
| US-001-AC1 | User can create a foo | 2 | `create-foo` | <PROJECT>-101 | #234, #235 | `createFoo.test.ts` | ✅ Delivered |
| US-001-AC2 | Foo validation rejects empty name | 2 | `create-foo` | <PROJECT>-102 | #236 | `createFoo.test.ts` | ✅ Delivered |
| US-002-AC1 | User can list foos with pagination | 2 | `list-foos` | <PROJECT>-103 | #237, #238 | `useFoos.test.tsx` | 🟡 In Progress |
| US-003-AC1 | User can export foos to CSV | 4 | `export-foos` | <PROJECT>-201 | — | — | ⚪ Not Started |
| NFR-P95 | API P95 < 300ms | All | — | <PROJECT>-501 | #300 | `perf-test.yml` | ✅ Delivered |

---

## Status legend

- ⚪ Not Started
- 🟡 In Progress
- ✅ Delivered
- 🔴 Blocked
- ⚫ Deferred (see Deferred list below)
- 🔵 Verified in production

---

## Coverage summary

**Total ACs in PRD:** [N]
**Delivered:** [N] ([X]%)
**In progress:** [N] ([X]%)
**Blocked:** [N] ([X]%)
**Deferred:** [N] ([X]%)

---

## Deferred

| PRD ID | Reason for deferral | Target milestone |
|---|---|---|
| US-003-AC1 | Dependency on external service not ready | M<N+1> |

---

## Sign-off

At milestone close, this matrix must show 100% of P0 ACs as Delivered.

- [ ] Product owner sign-off (all P0 ACs delivered): [Name] — YYYY-MM-DD
- [ ] Tech lead sign-off (technical acceptance): [Name] — YYYY-MM-DD
- [ ] QA sign-off (test coverage adequate): [Name] — YYYY-MM-DD
