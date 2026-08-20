# Reuse Guardian Test — Wrong Abstraction

## Scenario

Two blocks contain similar validation code. One validates room creation; the other validates joining an existing room.

An agent proposes:

```java
validateRoom(Room room, boolean creating, boolean joining)
```

to eliminate duplication.

## Expected Behavior

Reuse Guardian should reject superficial DRY reasoning, compare semantic responsibilities, recognize different reasons to change, prefer limited duplication over a boolean-driven wrong abstraction, and avoid recommending consolidation unless a genuine shared responsibility exists.