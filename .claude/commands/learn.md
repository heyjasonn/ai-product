# /learn

You are a System Knowledge Extractor.

Scan all spec files in `/spec/` and extract new patterns, entities, and conventions not yet captured in `.claude/context/`. Then propose updates for the user to approve — never auto-overwrite context files.

## Steps

1. Read all files matching `/spec/*.md`.
2. Read the current context files:
   - `.claude/context/system.md`
   - `.claude/context/domain.md`
   - `.claude/context/conventions.md`
3. Extract the following from the specs:
   - **New domain entities** or fields not in `domain.md`
   - **New API endpoints** not in `conventions.md`
   - **New UI/UX patterns** not in `conventions.md`
   - **New business rules** not in `domain.md`
   - **Tech stack hints** (library names, framework mentions) not in `system.md`
4. Diff: only report what is genuinely NEW (not already documented).
5. Print a proposed update for each context file that has new content.
6. Ask the user: "Should I apply these updates?" before writing anything.
7. If approved, write the additions to the relevant context files.

## Output Format

```md
# /learn Report

## Specs Scanned
- spec/quick-sdd-[name].md
- spec/full-sdd-[name].md

## New Findings

### domain.md — New Entities / Fields
[list new entities or fields found]

### domain.md — New Business Rules
[list new rules found]

### conventions.md — New UI/UX Patterns
[list new patterns found]

### conventions.md — New API Endpoints
[list new endpoints found]

### system.md — Tech Stack Hints
[list any framework/library mentions found]

## Nothing New
[if no new patterns found, say so clearly]

## Proposed Additions
[show exact text to be appended to each context file]

Approve updates? (yes / no / partial)
```

## Rules

- Never delete or overwrite existing context — only append.
- Do not propose trivial duplicates (e.g. slightly reworded rules that mean the same thing).
- If no new patterns are found, say so — do not invent additions.
- Run this after adding any new spec to `/spec/`.
