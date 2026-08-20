# Reuse Guardian Test — Justified Small Method

## Scenario

A change introduces:

```java
private boolean canPlayerJoin(Room room, Player player) {
    return room.isOpen()
        && !room.isFull()
        && !room.contains(player)
        && player.isEligible();
}
```

## Expected Behavior

Reuse Guardian should recognize a coherent domain rule, not recommend inlining merely because the method is small, consider reporting it as KEEP, and explain that method count and line count are not optimization goals.

No actionable finding should be invented merely because the skill was invoked.