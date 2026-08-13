# Tool Substitution Guide — How to Not Get Stuck

> The kit ships with defaults (GitHub, Figma, Zoom, Jira via Atlassian MCP, `gh` CLI). Real projects vary. This guide catalogs every "kit assumes X but you use Y" situation, with concrete swap recipes and graceful-degradation patterns for when no MCP exists.

**The one-line rule:** anything the kit hardcodes to a specific tool is one placeholder swap away from working with your alternative. When there's no MCP available, the agent falls back to a *manual workflow* — produces the artifact locally, hands it off to you to move into the tool.

---

## The 6 friction points at a glance

| # | Kit assumption | Common alternatives | Swap effort |
|---|---|---|---|
| 1 | **Figma** for design manifest | Sketch, Penpot, Adobe XD, Miro, none | 15 min prompt rewrite |
| 2 | **GitHub** for SCM + `gh` CLI | GitLab (`glab`), Bitbucket (`bb`), Azure DevOps (`az repos`), Gerrit | 30 min per-skill rewrite |
| 3 | **Ticketing MCP exists** (Jira via Atlassian) | Linear, GitHub Issues, ClickUp, Notion, Trello, Shortcut, Basecamp, Asana | Depends on MCP availability |
| 4 | **Zoom** for meeting transcripts | Teams, Meet, Otter, manual | 5 min path change |
| 5 | **Confluence / Notion** for docs | GitHub Wiki, GitBook, plain markdown | 5 min mental model swap |
| 6 | **MCP exists for every tool** | No MCP → manual workflow | Use graceful-degradation pattern (§7) |

**None of these are hard blockers.** Worst case, one skill degrades to a manual step. The rest of SDD keeps working.

---

## 1. Design tool substitution

### Kit default

- `prompts/02-figma-manifest.md` assumes Figma screens exported to `discovery/figma-exports/`.
- Generates a manifest that maps each screen to a route, layout, states, interactive elements.

### If you use Sketch

- Sketch export via `File → Export` produces PNG/PDF + optional JSON via plugins like [sketch-to-json](https://github.com/BohemianCoding/Sketch/wiki/JSON-Format).
- Change `prompts/02-figma-manifest.md`:
  - Replace "Figma exports" with "Sketch exports"
  - Replace `discovery/figma-exports/` with `discovery/sketch-exports/`
  - Reference Sketch's page/artboard naming (`Artboard 1`, `Screen - Login`) instead of Figma's page/frame naming
  - Content extraction: point to the JSON export if available, else PNG + human transcription of copy

### If you use Penpot

- Penpot exports SVG + JSON natively. Better than Figma for AI ingestion — SVG is text, includes computed styles.
- Change `prompts/02-figma-manifest.md`:
  - Ingest SVG directly, no image OCR needed
  - Route/layout extraction works the same

### If you use Adobe XD

- XD exports PNG + optional prototype JSON. Less structured than Figma.
- Same pattern: swap paths, adjust file format references.

### If you use Miro or a whiteboard tool

- Miro exports PNG + JSON via API. Good for early-stage wireframes.
- The "manifest" concept still applies but the content is looser (concepts, not pixel-perfect screens).

### If you use no design tool

- Skip `prompts/02-figma-manifest.md` entirely.
- Instead, in the PRD (`templates/prd.md`), fill the "Functional requirements (by screen / flow)" section with prose descriptions of each screen — states (empty, loading, error), actions, data displayed.
- The AI can generate the UI from prose alone; you just lose the visual reference.

**Recipe summary:** in `AGENTS.md` "Design tool" row, set your tool + export folder. Duplicate `prompts/02-figma-manifest.md` as `prompts/02-<tool>-manifest.md`, adjust references, keep the rest of SDD unchanged.

---

## 2. SCM host substitution

### Kit default

- `.cursor/skills/pr-readiness-check/SKILL.md` uses `gh pr view --json files` for size measurement.
- Multiple skills assume PR concept + GitHub-shaped URLs.

### If you use GitLab

- Replace `gh` CLI with `glab`.
- Equivalent commands:
  - `gh pr view <N> --json files` → `glab mr view <N> --json changes` (with adjusted jq)
  - `gh pr create` → `glab mr create`
  - `gh pr list` → `glab mr list`
- Terminology swap: PR → MR (Merge Request). Update PR template references.

### If you use Bitbucket

- Use `bb` CLI ([official Atlassian tool](https://developer.atlassian.com/cloud/bitbucket/rest/intro/)) or REST API.
- `bb` doesn't have JSON PR file output by default → use REST API:
  ```bash
  curl -H "Authorization: Bearer $BB_TOKEN" \
    "https://api.bitbucket.org/2.0/repositories/<workspace>/<repo>/pullrequests/<id>/diffstat"
  ```
- Same terminology as GitHub (Pull Request).

### If you use Azure DevOps

- Use `az repos pr` (part of Azure CLI).
- `az repos pr show <id>` returns PR details; combine with `az repos pr policy list` for the file-list equivalent.
- Terminology: PR (same as GitHub).

### If you use Gerrit

- Different model — changes are "Change-Ids" not PRs, and reviews happen per patchset.
- The `pr-readiness-check` skill logic mostly still applies, but the mechanics change: use `ssh -p 29418 <gerrit> gerrit query --format=json` for change info.
- Consider whether the kit's PR-review-based flow fits your Gerrit workflow; may need larger adaptation.

**Recipe summary:** in `AGENTS.md` "SCM host" row, set your host + CLI. In `.cursor/skills/pr-readiness-check/SKILL.md`, swap `gh` for your CLI in the commands section. If your CLI doesn't emit JSON, use the platform's REST API.

---

## 3. Ticketing system + MCP substitution

### Kit default

- Skills reference `<TICKETING_MCP>` as a placeholder — designed for Atlassian MCP (Jira + Confluence).
- All 8 process skills that touch tickets pass through this abstraction.

### Common alternatives + MCP status (mid-2026)

| Ticketing | Official MCP | Community MCP | Fallback |
|---|---|---|---|
| **Jira / Atlassian** | ✅ Atlassian MCP | — | — |
| **Linear** | ✅ Linear MCP | — | — |
| **GitHub Issues** | ✅ GitHub MCP | — | — |
| **Azure DevOps Boards** | ✅ Azure DevOps MCP | — | — |
| **Notion** | ✅ Notion MCP (usable for tickets) | — | — |
| **ClickUp** | ❌ No official | ✅ Community MCPs exist | Manual workflow |
| **Trello** | ❌ No official | ✅ Community MCPs exist | Manual workflow |
| **Shortcut** | ❌ No official | ✅ Community MCPs exist | Manual workflow |
| **Basecamp** | ❌ No official | ❌ | Manual workflow |
| **Asana** | ❌ No official | ✅ Community MCPs exist | Manual workflow |

Verify MCP availability at time of adoption — the ecosystem moves quickly. Search "modelcontextprotocol.io/servers" and GitHub for updated status.

### Swap recipe

1. In `AGENTS.md` "Ticketing" row, set your tool + MCP status.
2. If MCP exists: the skills' `<TICKETING_MCP>` references now resolve to your MCP. Read the MCP's tool descriptor before each call (skill guardrails already say this).
3. If no MCP: see §7 (graceful degradation).

### API-name differences to know

Skills reference generic method names (`get_issue`, `create_issue`, `update_issue`, `search`, `create_issue_link`). Actual method names per MCP vary:

| Kit generic | Jira MCP | Linear MCP | GitHub MCP | Notion MCP |
|---|---|---|---|---|
| `get_issue` | `jira_get_issue` | `linear_get_issue` | `github_get_issue` | `notion_pages_retrieve` |
| `create_issue` | `jira_create_issue` | `linear_create_issue` | `github_create_issue` | `notion_pages_create` |
| `update_issue` | `jira_update_issue` | `linear_update_issue` | `github_update_issue` | `notion_pages_update` |
| `search` | `jira_search` | `linear_search_issues` | `github_search_issues` | `notion_databases_query` |
| `create_issue_link` | `jira_create_issue_link` | `linear_update_issue` (via relationships) | (via body text `#123`) | (via `relates_to` property) |

The kit's skills abstract these — just look up the actual name in your MCP's tool descriptor.

---

## 4. Meeting transcripts source substitution

### Kit default

- `~/.cursor/rules/zoom-transcripts.mdc` (personal rule, not in kit) + `docs/01-sdd-playbook.md` §Discovery reference Zoom.
- Transcripts land at `docs/zoom-recordings-transcripts/` or similar.

### If you use Teams

- Teams meetings produce `.docx` transcripts or `.vtt` files.
- Change folder name to `docs/teams-recordings-transcripts/`.
- If you use the `meeting-mom` skill (personal, not in kit), adjust the transcript-format prompt to expect Teams' speaker-turn format.

### If you use Google Meet

- Meet transcripts come from Google Docs (auto-generated when caption is on).
- Export as `.txt`; land in `docs/meet-recordings-transcripts/`.

### If you use Otter.ai

- Otter exports `.txt`, `.srt`, or `.docx`. Well-formatted.
- Same folder pattern.

### If you have manual notes only

- Type meeting notes into `docs/meeting-notes/YYYY-MM-DD-<topic>.md`.
- Skip transcript-based extraction; use notes directly as PRD input.

**Recipe summary:** in `AGENTS.md` "Meeting transcripts source" row, set the source + folder path. Update `prompts/01-prd-from-transcripts.md`'s "Inputs" reference to point at your folder.

---

## 5. Documentation system substitution

### Kit default

- `<DOC_SYSTEM>` placeholder. Assumes Confluence-shaped concept: pages, spaces, hierarchical structure, with an MCP for search.
- Living discovery/architecture docs live in the doc system for stakeholder visibility.

### If you use Notion

- Notion is well-suited for SDD docs — has an MCP + hierarchical pages + databases.
- Living specs (integration spec, architecture narrative) as Notion pages.
- Fine-grained: use Notion databases for tracking Open Questions across meetings.

### If you use GitHub Wiki

- Simpler, no MCP. Directly in-repo (`.wiki` submodule).
- No search MCP; use `grep` on the wiki checkout.
- Change management: PRs to the wiki repo.

### If you use GitBook

- Great for public docs, less for internal working specs.
- GitBook Git Sync means docs live in a repo as markdown → same tooling as GitHub Wiki.

### If you use plain markdown in-repo

- Simplest option. Living specs at `docs/*.md`.
- No MCP needed — kit skills already reference repo paths for technical artifacts.
- Product context (Epic) still needs SOMEWHERE stakeholder-visible — either back to a ticketing system, or Slack canvases, or a shared doc.

**Recipe summary:** in `AGENTS.md` "Doc system" row, set your system + workspace. If in-repo, kit's default doc handling works without changes.

---

## 6. Cloud + observability + auth substitution

These rarely cause "stuck" moments because the kit doesn't hardcode them heavily. They matter mostly for:
- `.cursor/rules/engineering/handler-thin-services-thick.mdc` examples (AWS Lambda specific — swap for your framework)
- `docs/11-rag-and-agent-apps.md` (mentions Langfuse/LangSmith — swap for your observability)
- ClientName-specific rules (dropped from the kit) reference AWS deployment posture

**Recipe summary:** update AGENTS.md "Cloud" / "Observability" / "Auth" rows. Skills don't hardcode these — the agent adapts based on AGENTS.md.

---

## 7. Graceful degradation — the no-MCP fallback pattern

**When any external tool has no MCP available, use this pattern:**

### 1. Produce the artifact locally

The skill writes what would have been sent to the tool as a **markdown file in a staging folder**:

```
_pending/
├── tickets/
│   ├── 2026-07-08-domain-audit-entry.md         ← what the skill would have posted as a ticket
│   ├── 2026-07-08-data-access-audit-repo.md
│   └── 2026-07-08-functions-audit-handler.md
├── doc-updates/
│   ├── 2026-07-08-epic-decision-log-append.md   ← what the skill would have appended to the epic
│   └── 2026-07-08-adr-002-audit-storage.md
└── meeting-actions/
    └── 2026-07-08-arch-walkthrough-actions.md   ← action items to fan out to tickets
```

### 2. Skill reports the deferral

At the end of the skill run, the agent tells you:

> "Ticketing MCP is not available for ClickUp. I've drafted 3 tickets in `_pending/tickets/`. Please review and create them in ClickUp when convenient. Card key placeholders in the implementation plan use `TBD-1`, `TBD-2`, `TBD-3` — replace with real IDs once created."

### 3. Placeholder linking

Skills use temporary IDs (`TBD-1`) in artifacts (implementation plan, phase reports). When you manually create tickets and get real IDs, run a find-replace across the milestone folder.

### 4. Skills that must know an MCP result

Some skills genuinely need to talk to the tool (e.g., `phase-report-writer` verifies ticket statuses). Without an MCP:
- The skill asks *you* the status: "Please list the tickets for phase 2 and their current status."
- You paste ticket keys + statuses into the chat.
- The skill continues from there.

**This is not ideal but it's not blocked.** Manual workflows scale poorly beyond ~20 tickets/phase, so if you're on a fast-moving project, invest in getting or writing an MCP for your tool.

---

## 8. Adding a NEW tool the kit doesn't anticipate

Say your client uses a bespoke internal system, or an obscure tool. The pattern:

1. **Add a row to your project's AGENTS.md** "External Tools & Integrations" table:
   ```
   | Bespoke design tool | InternalDesignApp v3.4 | no | export folder: `discovery/design-exports/` |
   ```
2. **Author a project-scoped adaptation note** in your project's `docs/adaptations.md` (not in the kit — in the project). Document what's unique.
3. **Duplicate + adapt the affected kit skill or prompt** into your project's `.cursor/skills/` or `prompts/` folder with a project-scoped name (e.g., `prompts/02-internaldesign-manifest.md`).
4. **Do NOT push this back into the kit** unless you've seen the same tool in 3+ projects (rule of three).

---

## 9. When you SHOULD get stuck (i.e., stop and think)

Not every friction is fixable in 15 minutes. Situations that warrant stepping back:

- **The tool has no API and no exports.** Can't feed the AI. Consider whether this tool needs to be replaced or bypassed.
- **The company forbids AI tools from touching production data.** Adapt the discovery + PRD phases to use redacted / synthetic data.
- **The org uses a proprietary workflow tool (e.g., custom in-house PM tool) with no interface.** Fall back to fully manual SDD — you keep specs in the repo, ignore the internal tool for AI-visible work.
- **Compliance requires all AI outputs to be reviewed by a specific team.** Add a "compliance review" gate to `pr-readiness-check`.

In all four cases, the kit still works — just with more manual gates. Don't force AI-native workflows onto environments that resist them.

---

## The confidence check

Before starting a new project with this kit, walk through this checklist:

- [ ] `AGENTS.md` "External Tools & Integrations" table filled in
- [ ] For each row, MCP availability verified (or "no MCP" acknowledged)
- [ ] For each "no MCP" row, fallback workflow understood
- [ ] SCM CLI (`gh` / `glab` / etc.) installed and authenticated
- [ ] Design tool export path exists (or design is skipped for this project)
- [ ] Doc system chosen (in-repo, or MCP-accessible)
- [ ] Meeting transcript source known (or "no meetings" acknowledged)

If any of these is unclear when you start, spend the 30 minutes to sort it out. That's cheaper than getting stuck at Phase 2 and unwinding.

---

## Summary — 3 things to remember

1. **The kit degrades gracefully.** No tool combination is a hard blocker; worst case, one step goes manual.
2. **AGENTS.md External Tools table is the single source of truth.** Fill it once, every skill reads it.
3. **When you hit a novel friction point, adapt in the project, not the kit.** Rule of three governs kit updates.
