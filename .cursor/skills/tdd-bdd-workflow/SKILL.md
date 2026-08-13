---
name: tdd-bdd-workflow
description: Step-by-step TDD workflow with BDD-style test writing. Use when implementing any feature, fixing a bug, adding a component, creating a hook, writing a handler, or when the user mentions TDD, BDD, red-green-refactor, or test-first.
---

# TDD/BDD Workflow

## Core Cycle

Every coding task follows Red-Green-Refactor. No exceptions.

### Red — Write a Failing Test

1. Identify the **single behavior** to implement next.
2. Create or open the test file co-located with the source (e.g. `<name>.test.ts` or
   `<name>.test.tsx`; adapt to your language convention).
3. Write one `it` (or equivalent) block describing the expected behavior.
4. Run the test with your project's runner (e.g. `<TEST_COMMAND> --testPathPattern="<file>"`).
5. **Confirm failure.** Read the output. If the test passes, the test is wrong — it's not covering
   new behavior.

### Green — Write Minimum Code

1. Write the **smallest amount of code** that makes the failing test pass.
2. Do not add logic the test doesn't require. No speculative code.
3. Run the test again.
4. **Confirm pass.** If it fails, fix the implementation — not the test (unless the test has a
   bug).

### Refactor — Clean Up

1. Look for duplication, unclear names, or unnecessary complexity.
2. Improve structure while keeping the public contract unchanged.
3. Run the test again.
4. **Confirm still green.** If anything breaks, undo the refactor and try a smaller step.

### Repeat

Go back to Red for the next behavior. One behavior per cycle.

---

## BDD-Style Test Writing

Tests describe **observable behavior from the consumer's perspective**, not implementation details.

### Structure

```typescript
describe('UnitUnderTest', () => {
  it('does something observable when given specific input', () => {
    // Arrange — set up inputs and dependencies
    // Act — call the unit under test
    // Assert — verify the observable outcome
  });
});
```

### Naming Rules

- `describe` names the unit: component, function, or handler.
- `it` starts with a verb describing behavior: "renders", "returns", "throws when", "disables",
  "calls".
- No implementation details in test names.

Good:
```
it('renders an error message when submission fails')
it('returns an empty array when no items match')
it('disables the submit button while loading')
```

Bad:
```
it('sets state to error')
it('calls the filter method')
it('updates the loading ref')
```

### Grouping

Use nested `describe` blocks to group related scenarios:

```typescript
describe('OrderForm', () => {
  describe('when submitted with valid data', () => {
    it('shows a success message', () => { /* ... */ });
    it('clears the form fields', () => { /* ... */ });
  });

  describe('when submitted with missing fields', () => {
    it('shows validation errors', () => { /* ... */ });
    it('does not call the submit handler', () => { /* ... */ });
  });
});
```

---

## Layer-Specific Patterns

Determine the layer from the file path and apply the matching pattern. The examples below use
TypeScript + Vitest + React Testing Library — adapt to your stack.

### Domain — Pure Logic

Environment: node runtime. No mocks needed — functions are pure.

Rules:
- Import test primitives (`describe`, `expect`, `it`) explicitly.
- No framework imports. No mocking libraries. Domain stays pure.
- Test edge cases: empty input, boundary values, invalid input.

### Feature — UI Components and Hooks

Environment: browser-like (jsdom). Test setup provides matchers and stubs for common browser APIs
(`ResizeObserver`, `matchMedia`, etc.).

Query priority (follow Testing Library guiding principles):

1. `getByRole` — accessible roles (button, heading, status, textbox)
2. `getByLabelText` — form elements
3. `getByText` — visible text content
4. `getByTestId` — last resort only

For user interactions, use `userEvent` (async). Assertions target *observable* outcomes: rendered
text, ARIA state, callbacks invoked with expected arguments.

Rules:
- Follow your project's convention on whether test primitives are global or imported.
- Do not recreate or modify the setup file mid-project.
- Wrap components with required providers (e.g., `ThemeProvider`) only if the test fails without
  them.

### Functions — Backend Handlers

Environment: node. Mock external service events (request objects, message payloads).

Rules:
- Build event objects inline — keep them minimal and readable.
- Test success paths, validation failures, and error paths.
- Do not mock the code under test; mock its dependencies.

---

## Workflow Checklist

Use this to track each TDD cycle:

```
TDD Cycle:
- [ ] Identify the single behavior to implement
- [ ] Write failing test (Red)
- [ ] Run test — confirm failure
- [ ] Write minimum implementation (Green)
- [ ] Run test — confirm pass
- [ ] Refactor if needed
- [ ] Run test — confirm still green
```

For multi-behavior tasks, repeat the checklist for each behavior. Finish one cycle before starting
the next.

---

## Guardrails

- **Test before code.** Never write implementation before the test exists.
- **Run at every phase.** Never skip running tests after Red, Green, or Refactor.
- **One behavior per cycle.** Do not batch multiple behaviors into one test or one implementation
  step.
- **No speculative code.** If no test requires it, don't write it.
- **Domain stays pure.** Never mock in the domain layer — if you need mocks, the function has side
  effects and belongs elsewhere.
- **Hard to test = design signal.** If a test is difficult to write, simplify the interface or
  break the unit into smaller pieces first.
- **Don't test implementation.** Test what the consumer observes, not internal mechanics.
