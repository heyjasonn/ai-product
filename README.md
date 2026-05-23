# Spec Driven Agents

AI agents, commands, skills, and templates for Business Analysts, Product Owners, and Spec Driven Development — optimized for **Claude Code**.

## What This Is

This repository gives your team a structured workflow to turn rough feature ideas into complete, implementation-ready specifications. It works as a toolkit inside Claude Code: you run slash commands, Claude acts as a BA/PO/Planner/Reviewer, and the output is saved as markdown spec files.

It also maintains a **system knowledge base** so Claude understands your actual tech stack, domain entities, and conventions — meaning every spec it generates fits your existing system instead of being generic.

---

## Getting Started

### 1. Clone and open in Claude Code

```bash
git clone <repo-url>
cd ai-product
claude  # open Claude Code in this directory
```

### 2. Fill in your system context

On first session, Claude will show a notice if your system context is incomplete. Fill it using one of:

```
/setup-system          ← guided interview (recommended for new projects)
/scan-codebase [path]  ← auto-detect from your existing codebase
```

This populates `.claude/context/system.md` with your tech stack, architecture decisions, and product phase. It's a one-time setup — Claude will use it in every session from that point on.

### 3. Start writing specs

```
/quick-sdd add a saved jobs dashboard for job seekers
```

---

## Core Workflow

```
Raw idea
  ↓
/quick-sdd          → /spec/quick-sdd-[feature].md
  ↓
/full-sdd           → /spec/full-sdd-[feature].md
  ↓
/validate-sdd       → validation report (inline)
  ↓
/refine-sdd         → updates spec in-place
  ↓
/implement-from-spec → implementation plan (inline)
```

---

## Commands

All commands live in `.claude/commands/` and are invoked with `/command-name [arguments]` inside Claude Code.

### SDD Workflow Commands

| Command | Arguments | Output |
|---------|-----------|--------|
| `/quick-sdd` | Feature idea in plain text | `/spec/quick-sdd-[feature].md` |
| `/full-sdd` | Path to Quick SDD + optional context | `/spec/full-sdd-[feature].md` |
| `/validate-sdd` | Path to SDD + optional focus (`product` / `technical` / `qa` / `ux` / `api` / `data` / `all`) | Validation report inline |
| `/refine-sdd` | Path to SDD + change request text | Updates file in-place, prints change summary |
| `/implement-from-spec` | Path to Full SDD + optional tech stack | Implementation plan inline |

### System Context Commands

| Command | Arguments | Purpose |
|---------|-----------|---------|
| `/setup-system` | _(none)_ | Guided interview — asks ~16 questions in 4 batches, writes `vars.json` |
| `/scan-codebase` | Path to codebase root | Auto-detects tech stack from `go.mod`, `package.json`, `docker-compose.yml`, etc., writes `vars.json` |
| `/set-context` | `key "value"` | Update a single field in `vars.json` inline |
| `/learn` | _(none)_ | Scans all `/spec/*.md`, extracts new patterns/entities/endpoints, proposes updates to context files |

### Usage Examples

```
/quick-sdd add a notification centre for job application status updates

/full-sdd spec/quick-sdd-notification-centre.md

/validate-sdd spec/full-sdd-notification-centre.md technical

/refine-sdd spec/full-sdd-notification-centre.md add support for email notifications in addition to in-app

/implement-from-spec spec/full-sdd-notification-centre.md

/scan-codebase ../my-job-platform
/set-context features_in_progress "search filters, employer analytics dashboard"
/set-context product_phase "Production"
```

---

## System Context (How Claude Learns Your System)

Four files in `.claude/context/` are auto-loaded at every Claude Code session start. They tell Claude what your system looks like so specs stay consistent.

| File | What It Contains | How to Update |
|------|-----------------|---------------|
| `vars.json` | Dynamic variables — tech stack, phase, features in progress | Commands write here; or edit directly in any editor |
| `system.md` | Template that references `{{vars}}` from `vars.json` — auto-resolved at session start | Edit structure here; values come from `vars.json` |
| `domain.md` | Domain entities (fields, types), key business rules | Run `/learn` after adding new specs, or edit directly |
| `conventions.md` | UI/UX patterns, API patterns, naming conventions, spec writing rules | Run `/learn` after adding new specs, or edit directly |

### How vars.json works

`system.md` uses `{{placeholder}}` syntax. At session start, Claude reads `vars.json` and substitutes each `{{key}}` with its value. If a value is `null`, it's treated as unknown.

```json
{
  "backend": "Go 1.25.3 / go-zero v1.9.2",
  "database": "PostgreSQL",
  "features_in_progress": null
}
```

Three ways to update a value:

```bash
# 1. Single field — fastest
/set-context features_in_progress "search filters, employer analytics"

# 2. Auto-detect from codebase
/scan-codebase ../my-project

# 3. Guided interview for all null fields
/setup-system
```

Or edit `.claude/context/vars.json` directly in any text editor — it's plain JSON.

**Session start behaviour:** If `vars.json` has any `null` values, Claude shows a one-time notice listing the missing keys and which command to run.

**Keeping context fresh:** Run `/learn` after adding any new spec to update `domain.md` and `conventions.md` with new patterns.

---

## Agents

Agent definitions live in `.claude/agents/`. Their personas are embedded directly into the relevant commands — no separate activation needed in Claude Code.

| Agent | Role | Used By |
|-------|------|---------|
| BA Agent | Clarifies requirements, identifies business rules and edge cases | `/quick-sdd`, `/full-sdd` |
| PO Agent | Defines product goal, scope, user stories, acceptance criteria | `/quick-sdd`, `/full-sdd` |
| Planner Agent | Breaks work into tasks, maps dependencies, sequences implementation | `/implement-from-spec` |
| Reviewer Agent | Validates specs for completeness, ambiguity, conflicts, QA readiness | `/validate-sdd` |

---

## Skills

Reusable analysis modules in `.claude/skills/`, invoked by agents during spec generation.

| Skill | Purpose |
|-------|---------|
| `requirement-discovery` | Structured checklist to extract user, problem, goal, I/O, flow, rules, and edge cases from vague input |
| `edge-case-analysis` | Identifies edge cases across 6 categories: input, permission, status, time-based, integration, data |
| `acceptance-criteria` | Generates Given/When/Then criteria for happy path, validation, permission, status, and failure scenarios |
| `test-case-design` | Structures test cases covering happy path, validation, permission, status transitions, edge cases, integration failures |

---

## Repository Structure

```
.
├── .claude/
│   ├── commands/        ← Claude Code slash commands
│   ├── context/         ← System knowledge base (auto-loaded at session start)
│   │   ├── system.md    ← Tech stack, architecture (fill this first)
│   │   ├── domain.md    ← Domain entities and business rules
│   │   └── conventions.md ← UI/UX and API patterns
│   ├── agents/          ← Agent role definitions
│   ├── skills/          ← Reusable analysis skills
│   └── templates/       ← Blank SDD templates
├── .cursor/             ← Cursor equivalents (for teams using Cursor)
│   ├── commands/
│   ├── agents/
│   ├── skills/
│   └── templates/
└── spec/                ← Generated spec output files
```

---

## Working Conventions

- **Specs are written in Vietnamese** (or your team's language — update `conventions.md`)
- **Quick SDD first** — never jump to Full SDD without a Quick SDD unless explicitly skipped
- **Draft first, ask later** — commands auto-fill from context; ask at most 3–5 questions only when information is missing
- **Spec file naming** — `quick-sdd-[feature].md`, `full-sdd-[feature].md`, `refined-sdd-[feature].md`
- **Reviewer severity gates** — do not approve specs with unresolved Critical or High findings
- **`[NEW]` / `[NEW FIELD]` / `[NEW PATTERN]`** — commands flag deviations from existing system patterns so you can decide whether to adopt them

---

## For Cursor Users

The `.cursor/` directory contains equivalent commands, agents, skills, and templates in Cursor's format. The workflow and file naming are identical — only the command invocation mechanism differs.
