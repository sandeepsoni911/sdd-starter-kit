# LLD: [Feature Title] ([Ticket Key])

> Feature: `<feature-slug>` | Ticket: <PROJECT>-<N> | Milestone: <N> / Phase: <N>

---

## Layered Architecture

```
[ASCII diagram — see .cursor/skills/lld-creation/SKILL.md for structure]

FRONTEND
  App entry
    │
    ▼
  Component (Section / List / Filters)
    │ calls hook
    ▼
  useXxx(filters, sort, page)
    │ calls injected api
    ▼
  xxxService.listXxx()
    │ HTTP GET /xxx?params
═══════════════════ HTTP (network boundary) ═══════════════════
BACKEND
    │
    ▼
  API Gateway → handler
    │ service.list()
    ▼
  xxx-service
    │ repository.findAll()
    ▼
  xxx-repository (impl)

Domain (shared types)
  types/Xxx.ts   imported by both FE and BE
```

---

## Request Flow

```
User action
  │
  ▼
Component receives event
  │
  ▼
Hook (react-query / SWR / etc.)
  │
  ▼
Service (HTTP client abstraction)
  │
  ▼ [network boundary]
  ▼
Route (framework routing)
  │
  ▼
Handler (thin — parse + validate + call service + return)
  │
  ▼
Service (business logic)
  │
  ▼
Repository (data access)
  │
  ▼
Store (DB / cache / external service)
  │
  ▲ result flows back up the chain
```

---

## Files touched

| Layer | File | Purpose |
|---|---|---|
| Domain | `domain/src/xxx.ts` | Types + validation |
| Data-access | `data-access/src/lib/xxx-repository.ts` | Repository interface |
| Data-access | `data-access/src/lib/dynamo-xxx-repository.ts` | Impl |
| Functions | `functions/src/list-xxx-handler/index.ts` | Handler |
| Functions | `functions/src/lib/services/xxx-service.ts` | Business logic |
| Feature | `feature/src/components/xxx/XxxList.tsx` | UI |
| Feature | `feature/src/hooks/useXxxs.ts` | Data fetching hook |
| Feature | `feature/src/services/xxxService.ts` | HTTP client |
| Infrastructure | `infrastructure/terraform/xxx.tf` | Routes + Lambda |
