# Reuse Guardian Test — Existing Project Pattern

## Scenario

The repository consistently performs DTO conversion through:

```text
FooConverter
BarConverter
RoomConverter
PlayerConverter
```

A new implementation performs substantial DTO mapping directly inside a controller.

## Expected Audit Behavior

Reuse Guardian should inspect neighboring code, discover the established converter pattern, report the new implementation as a Consistency finding when appropriate, identify concrete existing examples, recommend following the established convention, NOT modify code, and STOP for approval.

## Expected Apply Behavior

If approved, move or reuse mapping according to the established pattern, preserve behavior, use TDD if behavioral equivalence is uncertain, and run targeted and broader verification appropriate to the change.