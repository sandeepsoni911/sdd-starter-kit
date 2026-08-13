# Tool Portability — Running the Kit on Non-Cursor AI IDEs

> The kit's **content is 100% portable across AI IDEs** — every file is plain markdown. The **automation** (auto-loading of rules, triggering of skills) varies by tool. This guide covers what changes when you switch from Cursor to Claude Code, Windsurf, Cline, Aider, Continue.dev, GitHub Copilot Chat, or Zed.

---

## The core insight

The kit uses three conventions:

1. **Nested `AGENTS.md` files** — auto-loaded facts about the codebase.
2. **`.cursor/rules/*.mdc`** — always-applied behavioral posture.
3. **`.cursor/skills/<name>/SKILL.md`** — trigger-word-activated mini-agents.

These conventions are becoming *de facto standards* — most modern AI IDEs support at least (1). Rules and skills are converging but not yet uniform.

**Practical implication:** if you switch tools, you keep the entire kit; you just re-wire how the tool loads it.

---

## Compatibility matrix

| Tool | AGENTS.md | Rules | Skills | Sub-agents | Kit compat | Notes |
|---|---|---|---|---|---|---|
| **Cursor** | ✅ Native | ✅ `.cursor/rules/*.mdc` | ✅ `.cursor/skills/<name>/SKILL.md` | ✅ `Task` tool | **100%** | Reference implementation |
| **Claude Code** (Anthropic CLI/IDE) | ✅ Uses `CLAUDE.md`; `AGENTS.md` also supported | ✅ Similar rule format in `.claude/` | ✅ `.claude/skills/` (analogous) | ✅ Subagents native | **~90%** | Rename file conventions; content unchanged |
| **Windsurf** (Codeium) | ✅ Reads `AGENTS.md` | ✅ `.windsurfrules` (convert frontmatter) | ⚠️ Cascade workflows — manual re-wiring | ⚠️ Limited | **~85%** | Cascade workflows do the skill-like work; verbose to author |
| **Cline** (VS Code) | ✅ Reads `AGENTS.md` | ✅ `.clinerules` file | ⚠️ Custom instructions blob; no per-skill triggering | ❌ No | **~70%** | Skills folded into one custom-instructions text |
| **Aider** | ✅ Via `--read` flag + `CONVENTIONS.md` convention | ⚠️ `.aiderconfig` + system prompt | ⚠️ Manual — invoke by hand | ❌ No | **~60%** | More CLI-driven; skills become named prompts you copy-paste |
| **GitHub Copilot Chat** | ✅ Since 2025 reads `AGENTS.md` | ⚠️ "Custom instructions" in VS Code settings | ❌ No skill concept | ❌ No | **~50%** | Rules folded into custom instructions; skills unsupported |
| **Continue.dev** | ⚠️ Via context providers | ⚠️ Custom "assistants" config | ⚠️ Slash commands (per-team) | ⚠️ Limited | **~50%** | Requires more setup; docs improving |
| **Zed** (AI features) | ⚠️ Reads `.rules` (evolving) | ⚠️ Emerging | ❌ No skill concept yet | ❌ No | **~40%** | Fast-moving target |
| **JetBrains AI Assistant** | ⚠️ Limited | ⚠️ Prompts library | ❌ No | ❌ No | **~40%** | JetBrains IDE features + AI plugin; less convention-driven |
| **Codeium** (free) | ⚠️ Limited | ❌ | ❌ | ❌ | **~30%** | Autocomplete-focused; use Windsurf for full kit |

**Legend:** ✅ native / ⚠️ partial or workaround / ❌ not supported

---

## Per-tool adaptation guides

### Cursor (reference — no changes needed)

The kit ships targeting Cursor. Use as-is.

Optional Cursor-specific extras:
- **`.cursor/hooks/*.sh`** — session lifecycle hooks (not shipped in this kit; author yourself if needed).
- **`~/.cursor/rules/*.mdc`** — user-global rules that apply across all projects. Keep personal patterns here (e.g., outbound comms style with your voice).

---

### Claude Code (Anthropic) — ~90% compat

**The Anthropic CLI + IDE ecosystem is the closest cousin to Cursor for this kit.**

**File renames / copies:**

| Cursor | Claude Code | Notes |
|---|---|---|
| `AGENTS.md` | `CLAUDE.md` **or** `AGENTS.md` (both work) | Keep both for max portability |
| `.cursor/rules/*.mdc` | `.claude/rules/*.md` | Content unchanged; frontmatter mostly the same |
| `.cursor/skills/<name>/SKILL.md` | `.claude/skills/<name>/SKILL.md` | Same format |
| `.cursor/BUGBOT.md` | `.claude/review-posture.md` | Same content, different file name convention |
| `.cursor/LEARNINGS.md` | `.claude/LEARNINGS.md` | Same |

**Setup at Day 0 in a new project using Claude Code:**

```bash
# Copy the kit
cp -r ~/Documents/sdd-starter-kit/.cursor .claude
cp ~/Documents/sdd-starter-kit/templates/AGENTS.root.md ./CLAUDE.md
# Symlink AGENTS.md for cross-tool compat
ln -s CLAUDE.md AGENTS.md
```

**What changes in the skills / rules:**
- Rules load automatically the same way. No content changes needed.
- Skills — the `allowed-tools` frontmatter uses Claude Code tool names (`Read`, `Write`, `Task`, etc.). The kit already uses this generic set, so content ports directly.
- Sub-agent invocation: Claude Code's `Task` tool works the same as Cursor's — kit's PR-readiness-check and other multi-step skills work unchanged.

**Kit compat: 90%** — only difference is file naming.

---

### Windsurf (Codeium) — ~85% compat

**File conversions:**

| Cursor | Windsurf | Notes |
|---|---|---|
| `AGENTS.md` | `AGENTS.md` | Native support |
| `.cursor/rules/*.mdc` | `.windsurfrules` (single file) or `.windsurf/rules/*.md` | Concatenate rules into `.windsurfrules` or use folder |
| `.cursor/skills/<name>/SKILL.md` | Cascade Workflows | Manual conversion — see below |

**Rule conversion (`.mdc` → `.windsurfrules`):**

Windsurf uses a single `.windsurfrules` file (or `.windsurf/rules/` folder in newer versions). Concatenate all your `.cursor/rules/*.mdc` files, drop the frontmatter, and add section headings:

```markdown
# .windsurfrules

## Source of Truth (append-only)
[body of source-of-truth.mdc]

## One Layer Per PR
[body of one-layer-per-pr.mdc]

## PR Size And Stack
[body of pr-size-and-stack.mdc]

... etc
```

**Skill conversion (SKILL.md → Cascade Workflow):**

Skills in Cursor auto-trigger on user intent. In Windsurf, Cascade Workflows are more explicit — user invokes them by name. Author each Cascade Workflow to reference the skill's `SKILL.md`:

```
Cascade Workflow: "Generate phase cards"
Description: See .cursor/skills/phase-card-generator/SKILL.md
Steps:
  1. Read the implementation plan
  2. [rest of skill procedure]
```

**What you lose:**
- Auto-triggering on user intent (Cursor rules fire when the user says "share this epic..." — Windsurf requires explicit invocation).
- Nested rule files (Windsurf prefers one file).

**What still works:**
- All content (specs, templates, prompts) unchanged.
- AGENTS.md nesting works.
- Sub-agent-style parallelism via Windsurf's cascade.

**Kit compat: 85%** — content portable; skill invocation needs manual step.

---

### Cline (Anthropic wrapper in VS Code) — ~70% compat

**File conversions:**

| Cursor | Cline | Notes |
|---|---|---|
| `AGENTS.md` | `AGENTS.md` | Native support |
| `.cursor/rules/*.mdc` | `.clinerules` (single file) | Concatenate; keep short — Cline loads all of it |
| `.cursor/skills/<name>/SKILL.md` | Custom Instructions blob (VS Code setting) or referenced in prompts | Skills become named prompt templates |

**Skill invocation in Cline:**

Skills don't auto-trigger. To invoke, paste this into the Cline chat:

```
Read .cursor/skills/phase-card-generator/SKILL.md and follow it. My epic is <PROJECT>-123.
```

Or wrap in a VS Code snippet for quick access.

**What you lose:**
- Automatic rule application (partially — `.clinerules` does apply to every prompt).
- Auto-triggering skills.
- Skill composability (skills that call other skills).

**What still works:**
- All content.
- AGENTS.md nesting.
- Sub-agents via Cline's task delegation (limited but workable).

**Kit compat: 70%** — you get the specs + templates + rules; skills become manually-invoked prompts.

---

### Aider — ~60% compat

Aider is CLI-first and paired-programming-oriented. Different model.

**File conversions:**

| Cursor | Aider | Notes |
|---|---|---|
| `AGENTS.md` | Add via `--read AGENTS.md` on invocation, or `.aider.conf.yml` | Aider convention: `CONVENTIONS.md` also read automatically |
| `.cursor/rules/*.mdc` | System prompt in `.aider.conf.yml` | Concatenate rules into the system prompt |
| `.cursor/skills/*/SKILL.md` | Named prompt files invoked via `/load` | Skills become macros |

**`.aider.conf.yml` example:**

```yaml
read:
  - AGENTS.md
  - .cursor/rules/process/source-of-truth.mdc
  - .cursor/rules/process/one-layer-per-pr.mdc
  - .cursor/rules/process/pr-size-and-stack.mdc
  - .cursor/rules/engineering/tdd-workflow.mdc

# System prompt additions
custom-arguments:
  - "--model-messages"
```

**Skill invocation in Aider:**

```
/read .cursor/skills/pr-readiness-check/SKILL.md
Follow this procedure for the current PR.
```

**What you lose:**
- Automatic rule application (Aider requires explicit `--read`).
- Auto-triggering skills.
- Nested AGENTS.md (Aider reads what you give it).

**What still works:**
- Content is fully portable.
- Aider's git-native workflow pairs well with the kit's PR discipline.
- Pair-programming mode fits SDD's "AI drafts, dev pair reviews" pattern.

**Kit compat: 60%** — you get specs, templates, and structure; skills become manual invocations.

---

### GitHub Copilot Chat — ~50% compat

Copilot Chat added `AGENTS.md` support in 2025 but has weaker rule and skill concepts.

**File conversions:**

| Cursor | Copilot Chat | Notes |
|---|---|---|
| `AGENTS.md` | `AGENTS.md` | Reads at repo root; nested support improving |
| `.cursor/rules/*.mdc` | `.github/copilot-instructions.md` (single file) | Concatenate |
| `.cursor/skills/*/SKILL.md` | Not supported — reference in prompts | Manual invocation |

**Setup:**

```bash
# Create the copilot-instructions.md by concatenating rules
cat .cursor/rules/process/*.mdc .cursor/rules/engineering/*.mdc \
  > .github/copilot-instructions.md
```

**What you lose:**
- Sub-agents / task delegation (limited in Copilot).
- Skill auto-triggering.
- Nested AGENTS.md support (limited).

**What still works:**
- Content, specs, templates.
- Copilot Chat can read your `.cursor/skills/*/SKILL.md` if you paste `Read this and follow: <path>`.

**Kit compat: 50%** — mostly specs + templates; skills / sub-agents unsupported natively.

---

### Continue.dev — ~50% compat

Continue.dev is an open-source AI coding assistant with a custom "assistant" configuration model.

**File conversions:**

| Cursor | Continue.dev | Notes |
|---|---|---|
| `AGENTS.md` | Loaded via context providers | Configure `context.md` provider in `.continue/config.yaml` |
| `.cursor/rules/*.mdc` | Assistant system prompt in `.continue/config.yaml` | Or via `promptFiles` config |
| `.cursor/skills/*/SKILL.md` | Slash commands via `.continue/prompts/*.prompt.md` | Convert each skill to a slash command |

**Slash command example (from a skill):**

```markdown
# .continue/prompts/generate-phase-cards.prompt.md
---
name: generate-phase-cards
description: Generate cards for a phase per the kit's skill
---

Read .cursor/skills/phase-card-generator/SKILL.md and follow it exactly for phase {{{ input }}}.
```

Invoke: `/generate-phase-cards 2`

**Kit compat: 50%** — usable but requires manual conversion of every skill to a slash command.

---

### JetBrains AI Assistant — ~40% compat

JetBrains IDEs' AI features are less convention-driven and more IDE-integrated.

- **AGENTS.md:** partial support — can be added to context manually.
- **Rules:** JetBrains "Prompts library" — save rule content as reusable prompts.
- **Skills:** no equivalent; reference them in prompts manually.

**Best for:** Java/Kotlin projects where you're already in IntelliJ. Combine with Aider or Claude Code CLI for the full SDD workflow.

**Kit compat: 40%** — content usable; automation minimal.

---

## The lowest-common-denominator setup

If you don't know which tool you'll use next, or you need something that works everywhere:

**Absolute minimum for portability:**

1. **`AGENTS.md` at repo root** — 8 out of 10 tools read this. Author it well.
2. **`CLAUDE.md` symlink to `AGENTS.md`** — covers Claude Code.
3. **`CONVENTIONS.md` symlink to `AGENTS.md`** — covers Aider.
4. **`.github/copilot-instructions.md`** — copy of your rules (concatenated) for Copilot Chat.
5. **Docs, templates, prompts as plain markdown** — usable by any tool.
6. **Skills referenced in a `SKILLS-INDEX.md`** — one page listing every skill with a "How to invoke in tool X" section.

This gets you 60-70% functionality in any tool without special automation.

---

## When to switch tools

Switching AI IDE has a real cost. Guidelines:

- **Stay in Cursor if:** you're happy with speed + rules + skills automation; your team is on Cursor; you use Cursor's sub-agents for parallelism.
- **Switch to Claude Code if:** you want a CLI-first workflow, you need Anthropic-specific features (very long context, structured outputs), or your team is standardizing on it.
- **Switch to Windsurf if:** you're on Codeium already, or you want a more workflow-oriented UX.
- **Switch to Cline if:** you're deeply in VS Code, or want to use custom LLM endpoints (Ollama, local models).
- **Switch to Aider if:** you're a CLI purist, want tight git integration, or want to run against local LLMs via Ollama.
- **Switch to Continue.dev if:** you want fully open-source, want to fine-tune the assistant behavior heavily.

**Don't switch just because a tool is trendy.** Switching = 1–2 days of adaptation + team retraining. Have a reason.

---

## Future-proofing

**Standards to watch:**

- **`AGENTS.md`** — approaching a de facto standard. Author it well; it'll be your longest-lasting asset.
- **Model Context Protocol (MCP)** — Anthropic's open protocol for AI tool integrations. Cursor, Claude Code, Windsurf, Cline all support MCPs. Your MCP setup is highly portable.
- **OpenTelemetry for GenAI (OTel-genai)** — emerging spec for LLM app telemetry. Adopting it gives you portability across observability tools (Langfuse, LangSmith, Arize, W&B).
- **`.rules` file convention** — Zed and others are converging on this. Watch for it.

**Standards NOT to bet on:**

- **Any single vendor's proprietary "workflows" or "flows"** — heavy vendor lock.
- **`copilot-instructions.md`** — GitHub-specific; not adopted by others.
- **VS Code-specific extensions** for AI configuration — tied to VS Code.

---

## The migration checklist

If you need to move a project from Cursor to another tool:

- [ ] Audit which conventions the target tool supports (`AGENTS.md`, rules, skills).
- [ ] Convert file naming per the target tool's expectations (see per-tool sections above).
- [ ] Concatenate rules into the target's rules format if it uses one file.
- [ ] Author slash commands or workflow triggers to replace skill auto-triggering.
- [ ] Test one full milestone lifecycle end-to-end in the new tool before committing.
- [ ] Update your `AGENTS.md` and `README` to reflect the new tool.
- [ ] Update your quickstart checklists in `quickstart/` for future team members.
- [ ] Document what changed in your `docs/08-gap-analysis.md` or a new "migration report."

**Typical migration duration:** 1–3 days for a well-adopted kit; 1 week if you're using Cursor-specific advanced features.

---

## What NEVER changes across tools

The 10 SDD invariants from `docs/10-language-adaptation-guide.md` hold regardless of tool. So do the LLM-app invariants (11–15) from `docs/11-rag-and-agent-apps.md`.

The tool is the vehicle. The kit is the map. The invariants are the destination.
