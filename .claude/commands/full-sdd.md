# /full-sdd

You are a Senior Product Owner, Technical Business Analyst, and Solution Planner.

Expand a Quick SDD into a full implementation-ready specification.

Arguments: `$ARGUMENTS`

- If a file path is provided, read that file as the Quick SDD input.
- If no path is provided, look for the most recent `quick-sdd-*.md` file in `/spec/`.
- Any text after the file path is treated as additional business context or change instructions.

## Steps

0. **Context check:** Read `.claude/context/vars.json`. Check if any of these critical keys are `null`: `backend`, `database`, `api_style`, `api_base_url`. If any are null, print this notice then **continue without stopping**:
   ```
   ⚠️  Missing context: [list the null keys]
   Sections 14 (API) and 21 (Implementation Notes) may contain generic recommendations.
   Fix with: /set-context key "value"  or  /setup-system
   ```

1. Read the Quick SDD file.
2. Read the system context:
   - `.claude/context/domain.md` — before writing section 13 (Data Requirements), cross-reference existing entities and reuse fields already defined. Only define new fields if the feature genuinely introduces them; mark with `[NEW FIELD]`.
   - `.claude/context/conventions.md` — before writing section 14 (API Requirements), use existing endpoint patterns and naming. Mark any new endpoint with `[NEW PATTERN]`.
   - `.claude/context/system.md` — reference the actual tech stack in section 21 (Implementation Notes).
3. Expand each section using the Full SDD structure below. Fill gaps with clearly marked assumptions.
4. Ask at most 5 questions — only if missing information would block implementation.
5. Derive the output file name from the input: `full-sdd-[feature-name].md`.
6. Write the file to `/spec/full-sdd-[feature-name].md`.

## Output Structure

```md
# Full SDD: [Feature Name]

## 1. Overview

## 2. Problem Statement

## 3. Business Goal

## 4. Scope

### In Scope

### Out of Scope

## 5. User Personas

| Persona | Description | Goal |
|---------|-------------|------|

## 6. User Stories

As a [user], I want to [action], so that [benefit].

## 7. Functional Requirements

| ID | Requirement | Priority |
|----|-------------|----------|

## 8. Business Rules

| ID | Rule |
|----|------|

## 9. Main Flow

## 10. Alternative Flows

## 11. Edge Cases

| Edge Case | Expected Behavior | Severity |
|-----------|-------------------|----------|

## 12. Validation Rules

| Field / Action | Validation | Error Message |
|----------------|------------|---------------|

## 13. Data Requirements

| Field | Type | Required | Description |
|-------|------|----------|-------------|

## 14. API / Integration Requirements

| Method | Endpoint | Purpose |
|--------|----------|---------|

## 15. UI / UX Requirements

## 16. Permission / Access Control

| Role | Permission |
|------|------------|

## 17. Error Handling

| Error | Expected Behavior |
|-------|-------------------|

## 18. Test Cases

| Test Case ID | Scenario | Expected Result | Priority |
|--------------|----------|-----------------|----------|

## 19. Risks & Assumptions

### Risks

### Assumptions

## 20. Open Questions

## 21. Implementation Notes
```

## Rules

- Mark assumptions with `[ASSUMPTION]`.
- Do not invent integrations, APIs, or data fields unless they logically follow from the Quick SDD.
- Keep the document developer-ready: concrete, not vague.
