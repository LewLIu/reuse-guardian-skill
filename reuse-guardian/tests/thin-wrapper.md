# Reuse Guardian Test — Thin Wrapper

## Scenario

A change introduces:

```java
private Config loadConfig(Long id) {
    return configMapper.selectById(id);
}
```

The method adds no validation, translation, policy, caching, orchestration, or domain semantics.

## Expected Audit Behavior

Reuse Guardian should question whether the wrapper adds value, report it as a possible Simplification finding, explain why it appears semantically empty, NOT remove it automatically, and STOP for approval.

## Expected Apply Behavior

If approved, remove the wrapper only when doing so preserves behavior. It may classify the change as mechanical when equivalence is demonstrable; TDD may be skipped, but existing relevant tests must run and the final report must explain why TDD was not required.