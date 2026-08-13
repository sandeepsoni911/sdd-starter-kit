# AGENTS.md

Root-level instructions for AI coding agents working on this repository.

> Each [MODULE_TYPE — e.g. capability, package, workspace] has its own `AGENTS.md` with scoped context. **Always read the nearest AGENTS.md** to the files you are editing — it takes precedence over this one for [scope]-specific guidance.

---

## Tech Stack

| Layer | Technology |
| ----------- | ------------------------------- |
| [Monorepo tooling] | [e.g. Nx 21, Turborepo, Lerna, Node workspaces] |
| Language | [e.g. TypeScript 5.8 (strict mode), Python 3.12, Go 1.23] |
| UI | [e.g. React 19, Vue 3, Svelte 5] |
| Data | [e.g. TanStack Query, SWR, React Hook Form] |
| Build | [e.g. Vite 7, esbuild, Turbopack] |
| Test | [e.g. Vitest, Jest, Playwright] |
| Lint/Format | [e.g. ESLint, Biome, Prettier, Husky pre-commit + pre-push] |
| Infra | [e.g. Terraform, Pulumi, CDK, AWS Lambda, API Gateway, DynamoDB] |

## Setup

```bash
[e.g. nvm use]                   # Node version
[e.g. npm install]               # or pnpm install / yarn / bun install
```

## Commands

```bash
[e.g. npm start]                 # Dev server
[e.g. npm test]                  # Run all tests
[e.g. npm run build]             # Build all projects
[e.g. npm run lint]              # Lint all projects
[e.g. npm run lint:fix]          # Auto-fix lint issues
[e.g. npm run typecheck]         # Type-check all projects
[e.g. npm run validate]          # Lint + typecheck + test
```

---

## External Tools & Integrations

Declare every external tool the project uses. AI agents read this to know which MCPs to call, which CLIs to use, and where to place artifacts. **If a row says "no MCP" for a tool, agents fall back to a manual workflow — see `docs/13-tool-substitution-guide.md` for the graceful-degradation pattern.**

| Concern | Tool | MCP available? | Notes / config |
| ------- | ---- | -------------- | -------------- |
| **Ticketing** | [e.g. Jira / Linear / GitHub Issues / Azure Boards / ClickUp / Trello / Shortcut / other] | [yes: `<mcp-server-name>` / no: manual workflow] | [project key or workspace ID; URL to instance] |
| **Doc system** | [e.g. Confluence / Notion / GitHub Wiki / GitBook / plain markdown / other] | [yes: `<mcp-server-name>` / no] | [space or workspace ID; URL] |
| **Design tool** | [e.g. Figma / Sketch / Penpot / Adobe XD / Miro / none / other] | [yes / no] | [file URL; export folder path] |
| **SCM host** | [e.g. GitHub / GitLab / Bitbucket / Azure DevOps / Gerrit] | [CLI: `gh` / `glab` / `bb` / `az repos`] | [org or workspace; repo URL] |
| **CI/CD** | [e.g. GitHub Actions / GitLab CI / CircleCI / Jenkins / Azure Pipelines / Travis / other] | — | [workflow file paths, secrets store] |
| **Cloud / hosting** | [e.g. AWS / GCP / Azure / Cloudflare / Vercel / Fly.io / on-prem / hybrid] | [MCP server name or CLI] | [account / project / subscription ID] |
| **Observability** | [e.g. Datadog / New Relic / Grafana / Sentry / CloudWatch / Langfuse / LangSmith / other] | [MCP server name if any] | [dashboard URLs, ingest keys location] |
| **Auth** | [e.g. Okta / Auth0 / Cognito / Azure AD / Google Identity / custom] | — | [tenant, client ID location] |
| **Meeting transcripts source** | [e.g. Zoom / Teams / Meet / Otter / manual / none] | — | [folder path where transcripts land] |
| **Communication** | [e.g. Slack / Teams / Discord / email-only] | [MCP server name if any] | [workspace ID for channel references] |
| **Package registry** | [e.g. npm / PyPI / Maven Central / GitHub Packages / private Artifactory] | — | [registry URL, auth token location] |
| **Secrets management** | [e.g. AWS Secrets Manager / HashiCorp Vault / 1Password / GCP Secret Manager / doppler / .env files] | — | [store name, access pattern] |

### Notes for AI agents

- **When invoking any skill that references `<TICKETING_MCP>` or `<DOC_SYSTEM>`**, read this table first to determine which MCP to actually call.
- **When an MCP is marked "no" for a concern**, the agent produces the artifact locally (e.g., a draft ticket as a markdown file in `_pending-tickets/`) and asks the human to move it into the tool manually.
- **When the human names a tool not in this table**, ask whether it should be added, then update this table (append-only) before continuing.

---

## Mandatory Rules

### 1. Test-Driven Development (TDD)

Every technical task **must** follow the TDD cycle:

1. **Red** — Write a failing test that describes the expected behavior.
2. **Green** — Write the minimum code to make the test pass.
3. **Refactor** — Clean up the implementation while keeping the tests green.

Do not skip straight to implementation. Tests come first.

### 2. No Git Commands

**Never** execute git commands (`git add`, `git commit`, `git push`, `git checkout`, `git rebase`, etc.). The developer manages version control manually.

### 3. No Destructive Commands

**Never** run commands that can cause irreversible data or state loss (`rm -rf`, `DROP`, `--force`, `--hard`, etc.). When in doubt, **ask before executing**.

### 4. Think Before You Code

Before writing or modifying any code:

- **Analyze** the problem, the existing codebase, and the architectural boundaries.
- **Consider** at least two approaches and their trade-offs.
- **Plan** the changes — identify which layers and files are affected.
- **Only then** start coding.

### 5. Critical Thinking Over Compliance

Do **not** agree with the developer's statements just for convenience:

- If a request contradicts the architecture or conventions, **raise the concern**.
- If you believe there is a better approach, **propose it with reasoning**.
- If information seems incorrect or incomplete, **ask for clarification**.

---

## Architecture

### Layered Model

[Adapt to your project. Example for a Nx-style capability layering:]

```
domain ← data-access ← functions
   ↑         ↑
feature ← experience
```

| Layer | Purpose | Platform | External Deps |
| ----------------- | ---------------------------------------------- | -------- | ------------- |
| `domain/` | Pure types, validation, constants | Shared | None |
| `data-access/` | Repositories + service layer | Backend | [AWS SDK / etc.] |
| `feature/` | [UI library — publishable, e.g. React lib] | Frontend | [Design system] |
| `experience/` | [Dev shell — local testing only] | Frontend | Anything |
| `functions/` | [Thin serverless handlers, e.g. Lambda] | Backend | data-access |
| `infrastructure/` | [IaC, e.g. Terraform] | Infra | — |

**Domain is always pure.** No frameworks, no UI libraries, no side effects — only [LANGUAGE] types, interfaces, and business-rule functions.

### Project Naming

Projects follow the pattern `[<PATTERN>]`:

```
[e.g. <capability>-<layer>]

[example]
material-testing-domain
material-testing-data-access
material-testing-feature
material-testing-experience
material-testing-functions
material-testing-infrastructure
```

### Project Tags & Module Boundaries

[If you use tag-based module boundaries (Nx, similar):]

| Tag pattern | Allowed dependencies |
| ------------------------- | -------------------- |
| `type:domain` | `scope:shared` only |
| `type:data-access` | `type:domain`, `scope:shared` |
| `type:feature` | `type:domain`, `type:util`, `type:feature`, `scope:shared` |
| `type:app` | Anything |
| `scope:capability:<name>` | Same capability + `scope:shared` |

### Path Aliases

[If your language supports path aliases (TypeScript, etc.):]

```
[e.g. @capabilities/<capability>/domain → capabilities/<capability>/domain/src/index.ts]
```

Every library **must** export its public API through `src/index.ts` (or your equivalent).

---

## Project Structure

```
[Adapt to your repo. Example:]

capabilities/
  <capability>/
    domain/                    # Pure types, validation, constants (zero deps)
    data-access/               # Repositories + service layer
    feature/                   # UI component library (publishable)
    experience/                # Dev app (internal, dev only)
    functions/                 # Serverless function handlers
    infrastructure/            # IaC (Terraform / Pulumi / CDK)
    bruno-collection/          # API test collection [or postman/insomnia/etc.]
    docs/                      # PRDs, LLDs, milestones
    AGENTS.md                  # Capability-specific instructions
    HOW-TO-RUN.md              # Local dev setup guide
tools/                         # Shared build/test config
shared/                        # Shared cross-capability infrastructure
scripts/                       # Workspace utility scripts
```

---

## Code Conventions

### [Language]

- [e.g. Strict mode enabled across all projects.]
- [e.g. Prefer `interface` for object shapes, `type` for unions and aliases.]
- [e.g. Prefer named exports. One public `index.ts` barrel per library.]

### Imports

[If your linter enforces import order:]

1. External packages
2. Internal aliases
3. Relative imports

No duplicate imports. Newline required after import block.

### Components

[Frontend-specific — remove if backend-only:]

- [e.g. Functional components only.]
- [e.g. Props defined as `interface` co-located with the component.]
- [e.g. Use `[DESIGN_SYSTEM]` components for all UI.]
- [e.g. Style with `[STYLING_APPROACH]` utility classes.]

### Testing

- **Framework**: [e.g. Vitest / Jest / etc.]
- **File naming**: `<Module>.test.ts` or `<Component>.test.tsx`, co-located with source.
- **Setup files**: [e.g. `vitest.setup.ts` per project with browser tests]
- **Environment**: [e.g. jsdom for component tests, node for pure logic]
- **Assertions**: [e.g. Prefer Testing Library queries over DOM selectors]
- **Shared config**: [e.g. `tools/config.ts` provides `defaultViteConfig()`]

### Styling

[Frontend-specific — remove if backend-only:]

- [e.g. Tailwind CSS 4 via Vite plugin — no separate config file.]
- [e.g. Design tokens applied via `<ThemeProvider>` from `[DESIGN_SYSTEM]`.]

---

## Code Quality

- Follow existing linter and formatter configuration — do not override or disable rules.
- Respect module boundaries.
- [Language]-specific constants live in [dedicated location].
- Keep components/functions/modules small, focused, and testable.
- Prefer composition over inheritance; prefer pure functions over side effects.

### Custom Lint Rules

[If you have project-specific lint rules:]

| Rule | What it catches |
| ---- | --------------- |
| [e.g. `JSON.parse() as Type`] | Unsafe cast — use runtime validation instead |
| [e.g. `error.message.includes(...)`] | Fragile error matching — use `instanceof` with typed Error classes |
| [e.g. Hex color literals in style props] | Hardcoded colors — use design tokens |
| [e.g. Block-body arrow functions in JSX props] | Inline handlers — extract to named `handleXyz` functions |

### Quality Gates

Two automated gates enforce code quality [if using Husky or equivalent]:

- **Pre-commit** (fast): [format staged files, lint affected projects, re-stage formatted files]
- **Pre-push** (thorough): [type-check + run tests on affected projects]

To run the full gate manually: `[e.g. npm run validate]`

---

## Harness Glossary

The word "harness" is overloaded. To avoid ambiguity in this project:

| Term | Where It Lives | Purpose |
| ---- | -------------- | ------- |
| [e.g. Experience harness] | `capabilities/*/experience/` | Disposable dev shell |
| [e.g. Test harness] | `**/*.test.*` | Test setup helpers |
| [e.g. Review harness] | `.cursor/BUGBOT.md` | Posture for PR reviews |
| [e.g. Learning loop] | `.cursor/LEARNINGS.md` | Durable operational lessons |
| [e.g. PR readiness harness] | `.cursor/skills/pr-readiness-check/` | Self-verification gate |
| [e.g. Quality gate harness] | `npm run validate` | Local enforcement of lint/typecheck/tests |
| [e.g. Module boundary harness] | `.eslintrc.json` + tags | Enforces layered dependency flow |

Name the specific harness you mean instead of using the bare term.

---

## Notes

- This file is read automatically by Cursor and other AI IDEs that support `AGENTS.md`.
- Keep it under 400 lines. Split into nested `AGENTS.md` files if it grows.
- Every PR blocking comment about a convention → add a line here.
