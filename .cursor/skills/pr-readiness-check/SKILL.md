---
name: pr-readiness-check
description:
  Validates a pull request against the harness invariants (one layer per PR, ~400 implementation
  lines, ACs covered, tests passing, lint, typecheck, Ticket/Epic linked, PR template followed)
  using a self-verification loop with up to 3 iterations grounded in external feedback. Use before
  opening a PR or when verifying that a PR is ready to be opened or merged.
when_to_use:
  User asks to open a PR, verify PR readiness, run pre-PR checks, or check if a PR is ready for
  review.
allowed-tools: Read Shell Grep CallMcpTool AskQuestion
---

# PR Readiness Check

Gate that runs before a PR is opened, applying the invariant rules through a self-verification loop.
Decisions are grounded in external feedback (test runs, lint output, typecheck, GitHub PR files
metadata via `gh pr view --json files`), not in re-reading the agent's own work.

## Adapting to your stack

- Replace `<TEST_COMMAND>`, `<LINT_COMMAND>`, `<TYPECHECK_COMMAND>` with your project's commands
  (e.g. `npx nx test <project>` / `npm test` / `pytest` / `go test`).
- Replace `<PR_SOURCE_PATH>` with your production-code source path pattern (e.g.
  `capabilities/**/src/**`).
- Replace `<TICKETING_MCP>` and `<PROJECT>-<N>` with your ticket format.
- `gh` (GitHub CLI) is portable across GitHub-hosted repos. If you use a different SCM, substitute
  its equivalent.

## Invariants Validated

1. **One layer per PR** (`.cursor/rules/process/one-layer-per-pr.mdc`).
2. **PR size and stack** (`.cursor/rules/process/pr-size-and-stack.mdc`): ~400 implementation lines
   max.
3. **Ticket and Epic linked** in the PR description.
4. **Acceptance Criteria covered** by tests or explicit manual checks.
5. **PR template followed** (`.github/pull_request_template.md`).
6. **Tests pass** for the affected project(s).
7. **Lint passes** for the affected project(s).
8. **Typecheck passes** for the affected project(s).
9. **Architectural boundaries** held (enforced by your module-boundary tooling).
10. **TDD evidence**: tests added or updated alongside implementation.

## Self-Verification Loop (Max 3 Iterations)

External-feedback only. Do not re-judge from re-reading code without evidence.

```
Iteration N (1..3):
  1. Run external checks (commands below).
  2. Collect failures and warnings.
  3. If clean: pass; report and stop.
  4. If failures: classify (auto-fixable vs needs user) and surface them.
  5. If auto-fixable and within scope: fix, then loop.
  6. If not auto-fixable: stop, report blocker to user.
After 3 iterations: stop and surface remaining issues.
```

## Procedure

### 1. Detect Context

Determine:

- Ticket key and Epic key from the branch / PR description / user input. If missing, ask.
- Affected project(s) from changed paths.
- Open PR number, if any (`gh pr view --json number`). If no PR yet, run pre-PR checks against the
  working tree and a base ref the user names; ask if base is unclear.

### 2. Measure Size

Use `gh` (never raw `git`):

```bash
gh pr view <PR_NUMBER> --json files --jq '.files[] | "\(.additions)\t\(.deletions)\t\(.path)"'
```

Filter the returned file metadata to implementation files:

- Include: `<PR_SOURCE_PATH>` (production code in the primary layer).
- Exclude: `**/*.test.*`, `**/*.spec.*`, `**/__tests__/**`, `**/__mocks__/**`, `**/*.generated.*`,
  `**/fixtures/**`, API test collection folders, `*.md`, `*.json`, `*.yml`.

Sum the `additions` values across remaining files.

When no PR exists yet:

- Estimate from `Read`/`Grep` of touched files plus the user-provided base ref.
- If estimation is uncertain, ask the user. Do not pretend to measure precisely.

### 3. Run Quality Gates

For each affected project:

```bash
<TEST_COMMAND>
<LINT_COMMAND>
<TYPECHECK_COMMAND>
```

Capture failure output verbatim. Do not summarize away errors.

### 4. Validate Layer Boundaries

If your linter enforces module boundaries (Nx tags, dependency-cruiser, custom lint rules), lint
already covers this. Otherwise, run your project-specific boundary check.

If lint reported boundary violations, surface the rule code and the offending import.

Do not mark this invariant satisfied without running the command.

### 5. Validate PR Template

If a PR is open, fetch the body via `gh pr view <PR_NUMBER> --json body` (or a GitHub MCP). Verify
required sections from `.github/pull_request_template.md` are present and filled:

- Summary
- Ticketing links (Epic + Card + Phase)
- Layer Scope (primary layer marked, justification if multi-layer)
- Size Check (under ~400 or split rationale)
- Stack (standalone or stack)
- Acceptance Criteria (all checked or explicitly mapped)
- Validation
- Risks / Rollback

### 6. Validate ACs Covered

Cross-reference the ticket's `## Acceptance Criteria` against tests added/updated in the PR. Each
AC should map to at least one test case or, when not test-automatable, an explicit manual check in
the Validation section of the PR.

If any AC is unaccounted for, fail this iteration with the specific list.

### 7. Validate Ticket And Epic Linked

The PR description must include the ticket key and Epic key. If missing, propose an edit to the PR
body and stop the loop until the user updates it. Do not silently rewrite the body; the PR body is
owned by the author.

### 8. Loop Or Pass

After collecting all results, decide:

- **Pass** when all gates green and all invariants satisfied.
- **Iterate** when failures are auto-fixable in scope (for example, fix a small lint, add a missing
  test, refresh the PR body).
- **Stop and surface** when failures need user judgment, design changes, or out-of-scope work.

Cap at 3 iterations. Report final status with the remaining blockers.

### 9. Learning Review (Advisory)

After the `PASS | FAIL` decision is determined, review whether the run surfaced a repeated
root-cause mistake, invalid command pattern, forgotten rule, recurring validation failure, or
durable tooling/setup friction.

If yes, read `.cursor/skills/agent-learning-loop/SKILL.md` and list candidate learnings in the
report. This review is advisory only: it must not change the readiness decision or add a new
blocking invariant.

## Output Report Template

```markdown
PR Readiness Report -- <PR or pre-PR>

- Ticket: <PROJECT>-<N>
- Epic: <PROJECT>-<N>
- Primary layer: <layer>
- Implementation lines: <N> (limit ~400)
- Tests: <pass | fail with summary>
- Lint: <pass | fail>
- Typecheck: <pass | fail>
- Boundaries: <ok | violations>
- ACs covered: <N/N> (list any missing)
- PR template: <ok | missing sections>

Decision: <PASS | FAIL after 3 iterations>
Remaining blockers (if any):

- <description and required action>

Learning candidates (advisory):

- <none | candidate IDs / summaries to review through agent-learning-loop>
```

## Guardrails

- External feedback only. Do not declare pass without running the actual commands.
- Never run `git` CLI directly. Use `gh` or a GitHub MCP.
- Self-verification loop is capped at 3 iterations. Do not extend silently.
- Learning Review is advisory and happens after the readiness decision; it does not redefine the PR
  invariants or block a clean PR by itself.
- Do not edit the PR body or commit messages without explicit user consent.
- TDD is enforced by `.cursor/rules/engineering/tdd-workflow.mdc`. If tests are missing, fail the
  gate.
- This skill validates the invariant rules (`one-layer-per-pr`, `pr-size-and-stack`,
  `source-of-truth`); it does not redefine them.
