# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Task Master AI Instructions
**Import Task Master's development workflow commands and guidelines, treat as if import is in the main CLAUDE.md file.**
@./.taskmaster/CLAUDE.md

## System Context (auto-loaded)

@.claude/context/vars.json
@.claude/context/system.md
@.claude/context/domain.md
@.claude/context/conventions.md

When reading `system.md`, substitute every `{{key}}` placeholder with the matching value from `vars.json`. If a key's value is `null`, treat that field as unknown.

## Session Start Checklist

At the start of every session, silently read `.claude/context/vars.json` and collect all keys whose value is `null`. If any exist, display this notice once before responding to anything else:

```
⚠️  vars.json has unfilled fields: [list the null key names, e.g. frontend, database, auth]

Options:
  /setup-system              ← guided interview
  /scan-codebase [path]      ← auto-detect from codebase
  /set-context key "value"   ← update a single field
  edit .claude/context/vars.json directly
```

Do not repeat this notice during the same session. If no `null` values exist, proceed silently.

---

## Repository Purpose

This is a **Spec Driven Development (SDD)** toolkit — a collection of AI agent definitions, commands, skills, and templates that help BA/PO teams turn rough feature ideas into complete, implementation-ready specifications.

There is no application code to build, compile, or test. All artifacts are markdown documents.

## Core Workflow

```
Raw idea
  ↓
/quick-sdd          → produces /spec/quick-sdd-[feature].md
  ↓
/full-sdd           → produces /spec/full-sdd-[feature].md
  ↓
/validate-sdd       → produces a validation report
  ↓
/refine-sdd         → updates existing spec in-place
  ↓
/implement-from-spec → produces an implementation plan
```

All spec output files are saved under `/spec/`.

## Directory Structure

| Path | Purpose |
|------|---------|
| `.claude/commands/` | Claude Code slash commands |
| `.claude/context/` | System knowledge base — loaded at session start |
| `.claude/agents/` | Agent role definitions (BA, PO, Planner, Reviewer) |
| `.claude/skills/` | Reusable analysis skills invoked by agents/commands |
| `.claude/templates/` | Blank SDD templates for quick-sdd and full-sdd |
| `.claude/examples/` | Example specs |
| `.cursor/` | Cursor equivalents (commands, agents, skills, templates) |
| `spec/` | Output directory — generated specs live here |

## Claude Code Commands

Invoke these with `/command-name [arguments]` in Claude Code. Each command embeds its agent persona and output structure — no separate agent activation needed.

| Command | Arguments | Output |
|---------|-----------|--------|
| `/quick-sdd` | Feature idea (plain text) | `/spec/quick-sdd-[feature].md` |
| `/full-sdd` | Path to Quick SDD + optional context | `/spec/full-sdd-[feature].md` |
| `/validate-sdd` | Path to SDD + optional focus (`product`/`technical`/`qa`/`ux`/`api`/`data`/`all`) | Validation report printed inline |
| `/refine-sdd` | Path to SDD + change request text | Updates file in-place, prints change summary |
| `/implement-from-spec` | Path to Full SDD + optional tech stack | Implementation plan printed inline |
| `/learn` | _(no arguments)_ | Scans `/spec/*.md`, proposes updates to `.claude/context/` |
| `/setup-system` | _(no arguments)_ | Guided interview to fill `vars.json` |
| `/scan-codebase` | Path to codebase root | Auto-detects tech stack from files, fills `vars.json` |
| `/set-context` | `key "value"` | Update a single field in `vars.json` inline |

## Agents

Agent files in `.claude/agents/` define the four personas. Their behavior is embedded directly in the Claude Code command files above, so no separate invocation is needed in Claude Code.

- **BA Agent** — requirement discovery, business rules, edge cases, alternative flows
- **PO Agent** — product goal, scope, user stories, MVP vs. future-phase separation, acceptance criteria (Given/When/Then format)
- **Planner Agent** — task breakdown, dependency mapping, implementation sequencing (data model → API → backend → frontend → QA)
- **Reviewer Agent** — spec validation across 10 dimensions; outputs Review Reports with Critical/High/Medium/Low severity findings

## Skills

Skills in `.claude/skills/` are reusable analysis modules referenced by agents:

- **`requirement-discovery`** — structured checklist to extract user, problem, goal, I/O, flow, rules, and edge cases from vague input
- **`edge-case-analysis`** — systematic edge case identification
- **`acceptance-criteria`** — Given/When/Then criteria generation
- **`test-case-design`** — test case structure and coverage planning

## Working Conventions

- **Fill in `.claude/context/system.md` first.** Add your actual tech stack before running SDD commands — this is the most impactful piece of context. Run `/learn` after adding any new spec to keep `domain.md` and `conventions.md` up to date.
- **Quick SDD first, Full SDD second.** Never jump straight to a Full SDD without a Quick SDD unless the user explicitly skips it.
- **Draft first, ask later.** Auto-fill from context; ask at most 5 questions, only when missing info blocks implementation.
- **Spec files use kebab-case names** prefixed with their type: `quick-sdd-`, `full-sdd-`, `refined-sdd-`.
- **Orchestration mode** (Single Agent / Pipeline / Multi-Agent) must be chosen and justified in every Quick SDD.
- **Reviewer Agent** uses severity levels: Critical, High, Medium, Low. Do not approve specs with Critical or unresolved High blockers.
- **Planner Agent** task format:
  ```md
  - [ ] Task name
    - Description:
    - Owner:
    - Dependency:
    - Expected output:
  ```
