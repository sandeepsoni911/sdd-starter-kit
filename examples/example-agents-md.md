# Example — Capability AGENTS.md (sanitized from Project Alpha)

> This is a compact, sanitized version of a real per-capability AGENTS.md file. Placeholder names replace product-specific ones. Use as reference for structure and length.

```markdown
# ExampleCapability — AGENTS.md

> Capability-specific instructions. For global rules (TDD, code quality, architecture), see the [root AGENTS.md](../../AGENTS.md).

---

## Purpose

Full-stack capability for **[business purpose]**. Exports reusable widgets to be embedded in the host application.

## Layers

| Layer             | Project name                         | What Lives Here                                                                          |
| ----------------- | ------------------------------------ | ---------------------------------------------------------------------------------------- |
| `domain/`         | `example-capability-domain`          | Pure types, validation functions, constants, format helpers                              |
| `data-access/`    | `example-capability-data-access`     | Repository interfaces + persistence implementations, services, service result utilities  |
| `feature/`        | `example-capability-feature`         | React components, hooks, types, utils, shared UI subfolders                              |
| `experience/`     | `example-capability-experience`      | Vite dev shell — local testing only. Mock API + real API adapters.                        |
| `functions/`      | `example-capability-functions`       | Thin serverless handlers. Imports repositories/services from data-access.                |
| `infrastructure/` | `example-capability-infrastructure`  | Terraform IaC — API Gateway, DynamoDB, Lambdas                                            |

### Dependency Flow

\`\`\`
domain  ←  data-access  ←  functions
   ↑            ↑
feature    ←  experience
\`\`\`

## Import Aliases

\`\`\`typescript
import { EntityType, validateEntity, ENTITY_STATUS }
  from '@capabilities/example-capability/domain';

import { EntityRepository, EntityService }
  from '@capabilities/example-capability/data-access';

import { ExampleRoot } from '@pdp/example-capability-feature';
\`\`\`

## Key Files

| File | Role |
|---|---|
| `domain/src/index.ts` | Domain public API |
| `domain/src/entity.ts` | Entity type + validation |
| `domain/src/validation.ts` | All validators |
| `data-access/src/repositories/entity-repository.ts` | Interface + singleton |
| `data-access/src/repositories/dynamo-entity-repository.ts` | Impl |
| `data-access/src/services/entity-service.ts` | Business logic + orchestration |
| `feature/src/index.ts` | Feature public API |
| `feature/src/components/EntityRoot.tsx` | Root widget |
| `feature/src/hooks/useEntities.ts` | List hook |
| `functions/src/list-entities-handler/index.ts` | Handler |
| `functions/src/lib/bootstrap.ts` | Cold-start wiring |
| `experience/src/App.tsx` | Dev shell |

## Commands

\`\`\`bash
npx nx dev   example-capability-experience    # Dev server
npx nx test  example-capability-feature       # Run feature tests
npx nx build example-capability-feature       # Build publishable lib
npx nx package example-capability-functions   # Build + package Lambdas
\`\`\`

## Quality Gates

\`\`\`bash
npx nx affected -t lint typecheck test        # Affected projects only
npm run validate                              # Full quality gate
\`\`\`

Coverage thresholds are set to 60% for domain and feature. Do not lower.

## Conventions

- **Domain stays pure** — no framework, no design-system imports, no side effects.
- **Data-access owns persistence** — Lambda handlers import from data-access, never from AWS SDK directly.
- **UI comes exclusively from design system** — no custom wrappers around Button/Modal/etc.
- **Experience is disposable** — no durable product logic here.
- **API is injected** — root component accepts an `api` prop.
- **Repository pattern** — Tests mock the repository, not the SDK.
- **Service result pattern** — services return `ServiceResult<T>` instead of throwing.
- **Single-table DynamoDB** — one table, entities distinguished by `entityType`.

## Documentation (Sources Of Truth)

| Concern | Lives In |
|---|---|
| Business outcome, scope, decisions, risks | Epic in ticketing system (append-only) |
| Per-task PRD (10 mandatory sections) | Ticket (append-only) |
| Milestone Implementation Plan | `docs/delivery/<milestone>/implementation-plan.md` |
| ADRs | `docs/delivery/<milestone>/adr/adr-<topic>.md` |
| LLDs | `docs/delivery/<milestone>/lld/<ticket>.lld.md` |
| Phase Reports | `docs/delivery/<milestone>/reports/phase-<N>.md` |

## PR Naming Convention

\`\`\`
type: M# - P#-0# -- Description
\`\`\`

Examples:
- `feat: M1 - P2-01 -- Domain types for Entity`
- `feat: M2 - P3-04 -- UI: EntityList component`

Ticket IDs go in parentheses at the end.
```

## Why this shape works

- **~150 lines** — fits in the agent's context without bloat.
- **Layer table front-and-center** — the agent immediately knows the architecture.
- **Key Files table** — the agent finds imports without grep.
- **Commands table** — the agent runs the right test command.
- **Sources Of Truth table** — the agent knows what lives where.
- **Conventions bullets** — durable posture (not per-milestone).
- **PR naming convention** — the agent produces correct PR titles.
