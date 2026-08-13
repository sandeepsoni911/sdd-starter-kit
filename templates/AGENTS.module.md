# AGENTS.md — [MODULE_NAME]

Module-scoped instructions for AI coding agents working in this folder.

> Read the **root `AGENTS.md`** first for tech stack, architecture, and mandatory rules. This file adds module-specific context.

---

## Module purpose

[1–2 sentences: what this module does and where it sits in the overall architecture.]

---

## Local commands

```bash
[e.g. npx nx test <this-module>]
[e.g. terraform fmt && terraform validate]
[e.g. docker compose up]
```

---

## Environment variables

Required for local dev:

| Var | Purpose | Default | Where to source |
|---|---|---|---|
| `[VAR_1]` | [purpose] | [default] | [1Password / secrets manager / other] |
| `[VAR_2]` | [purpose] | [default] | [source] |

---

## Deployment notes

[If different from the standard:]

- [e.g. This module deploys via `terraform apply` from `infrastructure/`, not from CI]
- [e.g. Manual approval required for production deploy]

---

## Domain vocabulary

[Key terms specific to this module:]

| Term | Definition |
|---|---|
| [Term A] | [definition] |
| [Term B] | [definition] |

---

## Layer taxonomy (if this module deviates)

[Only if this module has a different layer taxonomy from the root. Otherwise inherit from root.]

---

## Gotchas

- [e.g. Always run `terraform fmt` before commit — pre-commit hook enforces it]
- [e.g. The `<X>` client is not thread-safe; instantiate per request]
- [e.g. This module's tests need a running `<Y>` instance — use `docker compose up y` first]

---

## Related files

- Root: [`../../AGENTS.md`](../../AGENTS.md)
- Sibling module: [`../<sibling>/AGENTS.md`](../<sibling>/AGENTS.md)
- Documentation: [`./docs/README.md`](./docs/README.md)

---

## What NOT to do

- [e.g. Don't add UI components here — this is a data-access module]
- [e.g. Don't call external services directly — go through the service layer in `../functions/`]
