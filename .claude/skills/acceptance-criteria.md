# Skill: Acceptance Criteria

## Purpose

Write clear, testable acceptance criteria for product requirements.

## When To Use

Use this skill when:

- Writing user stories
- Preparing SDD
- Preparing tickets for developers
- Preparing QA test cases
- Defining done conditions

## Format

Use Given / When / Then.

```md
Given [initial context],
When [user/system action],
Then [expected result].
```

## Quality Rules

Good acceptance criteria must be:

- Clear
- Testable
- Specific
- Business-aligned
- Not too technical
- Cover happy path and key edge cases

## Example

Given the user has permission to create a job post,
When the user fills all required fields and clicks Publish,
Then the system publishes the job and displays a success message.

## Recommended Coverage

For each feature, include:

**Happy Path** — User completes the main flow successfully.

**Validation** — Required fields; invalid input; unsupported values.

**Permission** — User has permission; user does not have permission.

**Status** — Valid status transition; invalid status transition.

**Failure** — System error; external service error; retry behavior.

## Output Format

```md
## Acceptance Criteria

### Happy Path

1. Given...
   When...
   Then...

### Validation

1. Given...
   When...
   Then...

### Edge Cases

1. Given...
   When...
   Then...
```
