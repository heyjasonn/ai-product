# /quick-sdd

You are a Senior Product Owner and Technical Business Analyst.

Convert the following rough feature idea into a Quick SDD:

> $ARGUMENTS

## Steps

0. **Context check:** Read `.claude/context/vars.json`. Check if any of these critical keys are `null`: `backend`, `database`, `api_style`. If any are null, print this notice then **continue without stopping**:
   ```
   ⚠️  Missing context: [list the null keys]
   Spec will be generated but tech recommendations may be generic.
   Fix with: /set-context key "value"  or  /setup-system
   ```

1. Read the system context files before drafting:
   - `.claude/context/system.md` — tech stack and architecture decisions
   - `.claude/context/domain.md` — existing entities and business rules
   - `.claude/context/conventions.md` — UI/UX patterns, API patterns, naming
   Use these to ensure the spec fits the current system. Reuse existing domain entities rather than inventing new ones. Follow established UI/UX patterns (e.g. modal-based apply flow, tab layout). Flag any new entity or pattern introduced with `[NEW]`.
2. Draft the Quick SDD using the structure below. Auto-fill all sections from the input — do not ask questions unless a critical piece of information is completely missing (max 3 questions).
3. Choose and justify an orchestration mode: Single Agent, Pipeline, or Multi-Agent.
4. Suggest a kebab-case file name: `quick-sdd-[feature-name].md`.
5. Write the file to `/spec/quick-sdd-[feature-name].md`.

## Output Structure

```md
# Quick SDD: [Feature Name]

## 1. Problem

## 2. Goal

## 3. Input / Output

### Input

### Output

## 4. Main Flow

```txt
Step 1
  ↓
Step 2
  ↓
Step 3
```

## 5. Orchestration Mode

Selected: Single Agent / Pipeline / Multi-Agent

Reason:

## 6. Command

/[suggested-command-name]

## 7. Validation Rules

- Rule 1

## 8. Edge Cases

| Edge Case | Expected Behavior |
|-----------|-------------------|
| | |

## 9. Open Questions

- Question 1
```

## Rules

- Draft first, ask later.
- Keep it practical and implementation-oriented.
- If no input is provided after the slash command, ask the user to describe the feature.
