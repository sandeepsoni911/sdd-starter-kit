# Prompt — Bootstrap AGENTS.md from an existing repo

**Version:** 1
**Last tuned:** 2026-07
**Purpose:** scan a brownfield codebase + recent PR feedback and extract standards into a first-pass `AGENTS.md`.

## Inputs

- Repo root
- Recent merged PRs (30–90 days)
- Any existing style guides, wiki pages, or docs the team follows

## Prompt

```
You are drafting the first version of AGENTS.md for this repository.

Use the template at @templates/AGENTS.root.md as the exact structure.

Your job:
1. Scan the repo root. Identify:
   - Package manager (npm/pnpm/yarn/pip/poetry/cargo/go mod/etc.)
   - Test runner (vitest/jest/pytest/rspec/etc.)
   - Linter/formatter (eslint/biome/prettier/ruff/etc.)
   - Build tool (vite/webpack/turbopack/esbuild/rollup/setuptools/etc.)
   - CI system (GitHub Actions/GitLab CI/etc.)
   - Framework(s) (React/Vue/Django/Spring/etc.)
2. Extract commands. Look at package.json scripts / Makefile / justfile / README setup section.
3. Identify the layered architecture from the folder structure. Propose layer names and dependency direction.
4. Fetch recent merged PRs via `gh pr list --state merged --limit 50 --json number,title,body`. Look at PR review comments for recurring "please do X" patterns. Every recurring pattern → a documented convention.
5. Look for ESLint / Ruff / linter config for custom rules. Every custom rule → a documented convention with rationale.
6. Look for existing pre-commit / pre-push hooks. Document them under "Quality Gates."
7. If a "Harness Glossary" is needed (multiple concepts share the same name), draft one.

Non-negotiables:
- Do NOT invent tech stack items. If unclear, add to Open Questions.
- Do NOT invent architecture. If the folder structure doesn't reveal it, ask.
- Do NOT copy conventions from another org's AGENTS.md.
- Keep it under 400 lines. If it exceeds, split into nested AGENTS.md files.

Output: a complete root AGENTS.md draft ready for team review.
```

## Expected output structure

Matches `@templates/AGENTS.root.md` with placeholders replaced by extracted content.

## Validation checklist

- [ ] Tech Stack table is real (verified by looking at package.json / config files)
- [ ] Commands actually work (run one to verify)
- [ ] Architecture matches folder structure
- [ ] At least 3 conventions extracted from PR review history
- [ ] File length under 400 lines
- [ ] Open questions section for anything unclear

## Known failure modes

- **Cargo-culted conventions.** Mitigate by requiring grounding in PR history.
- **Fictional folder structure.** Mitigate by requiring folder-walk to verify.
- **Missed pre-commit hooks.** Mitigate by explicitly asking to check for `.husky/`, `.git/hooks/`, `lefthook.yml`, etc.

## Change log

- 2026-07-XX (v1): Initial.
