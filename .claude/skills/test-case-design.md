# Skill: Test Case Design

## Purpose

Generate practical test cases from requirements or SDD.

## When To Use

Use this skill when:

- A feature spec is ready
- QA needs test cases
- Developer needs validation scenarios
- Product wants to verify behavior

## Test Case Types

Include:

- Happy path
- Validation test
- Permission test
- Status transition test
- Edge case test
- Integration failure test
- Regression test

## Output Format

```md
| Test Case ID | Scenario | Preconditions | Steps | Expected Result | Priority |
|--------------|----------|---------------|-------|-----------------|----------|
| TC001 | | | | | |
```

## Priority

Use:

- High
- Medium
- Low

## Test Design Rules

- Each test case should test one behavior.
- Expected result must be clear.
- Include negative cases.
- Include boundary cases.
- Include permission cases if the feature has roles.
- Include data consistency checks if data is saved.

## Example

| Test Case ID | Scenario | Preconditions | Steps | Expected Result | Priority |
|--------------|----------|---------------|-------|-----------------|----------|
| TC001 | Create job post successfully | User has permission | Fill required fields and click Publish | Job is published successfully | High |
