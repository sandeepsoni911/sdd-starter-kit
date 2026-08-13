---
milestone: M<N>-<short-name>
epic: <PROJECT>-<N>
phase: <N>
feature: <feature-slug>
layers: [domain, data-access, functions, feature]
tickets: [<PROJECT>-<N>, <PROJECT>-<N>]
last_updated: YYYY-MM-DD
status: draft
---

# [Feature Title]

> Milestone: [M<N>-<short-name>](../../implementation-plan.md) | Phase: [<N>]
>
> Tickets: [<PROJECT>-<N>](<URL>), [<PROJECT>-<N>](<URL>)

## Purpose

[One paragraph: what this feature does, who it's for, and how it fits in the phase.]

## Scope

**In scope:**
- ...

**Out of scope:**
- ...

## Acceptance criteria (from ticket)

- [ ] AC1: [testable]
- [ ] AC2: ...

## Design

[Screenshot / Figma link / ASCII wireframe]

## Layer implementation

### Domain

**Types added:**
```typescript
interface Foo { ... }
```

**Validation:**
- ...

### Data-access

**Repository methods:**
- `foo.findAll(filters)`
- `foo.save(entity)`

**Access patterns / queries:**
- ...

### Functions / API

**Endpoints:**
- `GET /foos` — returns paginated list
- `POST /foos` — creates a new foo

**Handler → service → repository flow:**
- ...

### Infrastructure

- API Gateway route
- IAM roles
- ...

### Feature (UI)

**Components:**
- `FoosList` — shows the list
- `FooDetailAside` — shows details

**Hooks:**
- `useFoos(filters)`
- `useCreateFoo()`

**Design tokens used:**
- ...

### Experience

- Mock API stub update
- ...

## E2E scenarios

1. **Happy path — create a foo:**
   - Given: user is authenticated
   - When: user submits the create form with valid data
   - Then: foo appears in the list; success toast shown

2. **Validation error:**
   - Given: user is authenticated
   - When: user submits the form with an empty required field
   - Then: inline validation error shown; foo NOT created

## Test strategy

- **Domain:** pure functions — unit tests
- **Repository:** contract tests against local DB
- **Handler:** unit tests with mocked service
- **UI:** component tests with mocked API; E2E scenarios via testing-library user events

## Change log

- YYYY-MM-DD: Initial draft
