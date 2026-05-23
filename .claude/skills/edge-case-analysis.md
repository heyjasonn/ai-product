# Skill: Edge Case Analysis

## Purpose

Identify edge cases that may break the feature or create unclear behavior.

## When To Use

Use this skill when:

- A flow has multiple statuses
- A feature depends on time
- A feature has user input
- A feature involves external systems
- A feature changes existing data
- A feature affects notifications or payments

## Edge Case Categories

### 1. Input Edge Cases

- Required field is empty
- Invalid format
- Unsupported value
- Very long text
- Special characters
- Duplicate input

### 2. Permission Edge Cases

- User has no permission
- User has partial permission
- User role changes during process
- User is deactivated

### 3. Status Edge Cases

- Invalid status transition
- Duplicate action
- Action on completed item
- Action on expired item
- Action on deleted item

### 4. Time-Based Edge Cases

- Expired data
- Timezone mismatch
- Holiday / day off
- Cut-off time
- Delayed job failure
- Retry behavior

### 5. Integration Edge Cases

- External API timeout
- External API returns error
- Partial success
- Duplicate webhook
- Missing response field

### 6. Data Edge Cases

- Data already exists
- Data deleted by another user
- Stale data
- Concurrent update
- Large data volume

## Output Format

```md
| Edge Case | Expected Behavior | Severity |
|-----------|---------------------|----------|
| | | |
```
