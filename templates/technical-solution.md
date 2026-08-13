# Technical Solution Design — [MILESTONE / FEATURE NAME]

**Status:** [Draft | In Review | Approved]
**Owner (Tech Lead):** [Name]
**Peer reviewer:** [Name]
**Last updated:** YYYY-MM-DD
**Version:** 1

Related: [PRD](../prd.md) | [UI Strategy](./ui-strategy.md) | [Implementation Phases](./implementation-phases.md)

---

## 1. Context & scope

**Problem restated (from PRD):**
...

**Scope:**
- ...

**Non-scope:**
- ...

---

## 2. Architecture

### 2.1 High-level architecture diagram

```
[ASCII or mermaid diagram — no images]
```

### 2.2 Layer touch map

| Layer | Change |
|---|---|
| domain | ... |
| data-access | ... |
| functions / API | ... |
| infrastructure | ... |
| feature (UI) | ... |
| experience (shell) | ... |

### 2.3 Data model

**New types / entities:**

```typescript
interface Entity {
  id: string;
  ...
}
```

**Persistence:** [Which store? Access patterns? GSIs if DynamoDB, indexes if RDBMS.]

**Migration plan:** [How is data migrated from any existing store?]

---

## 3. API contracts

### 3.1 New endpoints

| Method | Path | Purpose | Auth | Request | Response |
|---|---|---|---|---|---|
| GET | /entities | List | Bearer | `?filter=X&page=Y` | `{items, totalCount}` |
| POST | /entities | Create | Bearer | `CreateBody` | `Entity` |

### 3.2 OpenAPI spec

[Link to the OpenAPI file — spec is the contract, not this doc]

---

## 4. Error handling posture

| Error class | HTTP code | User feedback |
|---|---|---|
| ValidationError | 400 | Inline field errors |
| NotFoundError | 404 | Empty state UI |
| ForbiddenError | 403 | Access denied banner |
| Unhandled | 500 | Generic error toast |

Centralized in [`error-mapper.ts`](...).

---

## 5. Mock strategy

**Frontend dev without backend:** [mock-api.ts pattern? MSW? contract-driven mocks?]

**Contract tests:** [How is the frontend mock kept in sync with the real API?]

**Test data:** [Fixtures location; how are they generated?]

---

## 6. Security controls

- **Authentication:** [How users authenticate]
- **Authorization:** [Which endpoints require which role?]
- **PII handling:** [What PII does this system touch? How is it stored/logged?]
- **Audit logging:** [What events are logged for audit?]

---

## 7. Observability

- **Metrics:** [What we track]
- **Traces:** [Distributed tracing setup]
- **Logs:** [Structured logging keys]
- **Alerts:** [When we page]

---

## 8. Deployment

- **Environments:** dev → staging → prod
- **Rollback:** [Plan]
- **Feature flag:** [If yes, which flag?]

---

## 9. Risks & mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| [Risk 1] | H/M/L | H/M/L | ... |

---

## 10. Open questions

| # | Question | Owner | Status |
|---|---|---|---|
| Q1 | ... | [Name] | Open |

---

## 11. Change log

- YYYY-MM-DD (v1): Initial draft
- YYYY-MM-DD (v1.1): [What changed]
