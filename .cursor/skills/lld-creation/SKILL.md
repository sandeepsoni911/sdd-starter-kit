---
name: lld-creation
description:
  Generates a Low-Level Design (LLD) document for a feature, showing layered architecture, file
  names, and request flow. Use when the user mentions LLD, low-level design, architecture diagram,
  layer diagram, or asks to create an LLD for a feature or story.
---

# LLD Creation Workflow

## When to Trigger

Run this workflow when asked to create an LLD for a feature. The input is typically a phase feature
file (`capabilities/<capability>/docs/delivery/<milestone>/features/phase-<N>/<feature>.md`) or a
ticket key.

## Inputs

1. **Feature file** — read it to extract scope, columns, behaviors, and acceptance criteria.
2. **Capability `AGENTS.md`** — read `capabilities/<capability>/AGENTS.md` to get current layers,
   naming conventions, and existing file patterns.
3. **Ticket key** — used for the output file name (e.g. `<PROJECT>-<N>`).
4. **Milestone** — used for the output folder (e.g. `M2`).

## Output

A single markdown file saved at:

```
capabilities/<capability>/docs/delivery/<milestone>/lld/<ticket-key>.lld.md
```

## Procedure

### Step 1 — Read Context

1. Read the feature file to understand scope and acceptance criteria.
2. Read the capability's `AGENTS.md` for layers, naming conventions, and existing file structures.
3. Identify which layers are affected.

### Step 2 — Determine Files Per Layer

Map the feature to concrete file names following **existing naming conventions** in your project.
Only include layers and files that the feature actually requires.

### Step 3 — Build the Diagram

Produce a compact ASCII box diagram split into **two clear sections**: Frontend and Backend.

**The diagram starts from the Frontend (top) and flows down to the Backend (bottom).**

Use a horizontal separator with a label to mark the boundary:

```
═══════════════════ HTTP (network boundary) ═══════════════════
```

**Frontend section (top)** — stacked top-to-bottom in call order:

1. **Experience / App entry** — the entry point of the frontend
2. **Feature — Components** — Section, List, Filters, etc.
3. **Feature — Hooks** — hooks that call injected APIs
4. **Feature — Services** — HTTP client abstraction

**Domain (shared)** — types imported by both FE and BE. Show once between the two sections.

**Backend section (bottom)** — stacked top-to-bottom in call order:

1. **Infrastructure** — API Gateway route → Handler mapping
2. **Functions — Handler** — Entry point
3. **Functions — Service** — Business logic
4. **Functions — Data** — Repository interface + implementation

Rules:

- **File names only** inside boxes — no long descriptions.
- **Arrows between layers** labeled with what is being passed or called.
- Within Feature, group by Components, Hooks, and Services with headers.
- Within Functions, show Handler → Service → Data as nested boxes.

### Step 4 — Build the Request Flow

Below the diagram, add a linear request flow showing the end-to-end path from user action through
every layer and back. **Start from the frontend.**

```
User opens tab
  │
  ▼
FRONTEND
  App entry ──api prop──► Component (Section/List)
                              │ calls hook
                              ▼
                        useXxx(filters, sort, page)
                              │ calls injected api
                              ▼
                        xxxService.listXxx()
                              │ HTTP GET /xxx?params
═══════════════════ network boundary ═══════════════════
                              ▼
BACKEND
                        API Gateway → handler
                              │ service.list()
                              ▼
                        xxx-service
                              │ repository.findAll()
                              ▼
                        xxx-repository (impl)
                              │
                              ▼
                        { items, totalCount } → back up the chain
```

### Step 5 — Write the File

Create the LLD file with this structure:

```markdown
# LLD: <Feature Title> (<Ticket Key>)

> Feature: `<feat-name>` | Ticket: <PROJECT>-<N> | Milestone: <n> / Phase: <n>

---

## Layered Architecture

\`\`\`
[ASCII diagram here]
\`\`\`

---

## Request Flow

\`\`\`
[Flow here]
\`\`\`
```

Save to `docs/delivery/<milestone>/lld/<ticket-key>.lld.md`.

## Diagram Style Rules

- **Frontend first, Backend second.** The diagram reads top-to-bottom following the request path:
  user → FE → HTTP → BE.
- **Clear FE/BE separator.** Use a double-line `═══ HTTP (network boundary) ═══` between the two
  sections.
- Keep boxes compact — file names and short labels only.
- Use `─`, `│`, `┌`, `┐`, `└`, `┘`, `├`, `┤`, `▼`, `▲`, `►` for box drawing.
- Arrow labels describe the call or import.
- Sub-layers within Functions are indented/nested boxes.
- Components, Hooks, and Services within Feature are grouped with headers.
- Domain types box sits between FE and BE (shared by both) with upward arrows from each side
  labeled with the types they import.
- Maximum width: ~70 characters per line for readability.

## Guardrails

- **Follow existing conventions.** Always read the capability `AGENTS.md` first. File names must
  match the patterns already in use.
- **Only include affected layers.** If the feature doesn't touch infrastructure, omit it.
- **No implementation code.** The LLD shows structure and flow, not code.
- **Keep it compact.** The diagram should fit on one screen. If it doesn't, simplify.
- **One LLD per ticket.** Each ticket gets its own file.
