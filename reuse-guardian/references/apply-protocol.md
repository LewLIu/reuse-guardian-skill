# Reuse Guardian — Apply Protocol

## Purpose

Safely implement only the Reuse Guardian findings explicitly approved by the human.

## 1. Establish Approval Scope

Extract the exact approved finding IDs. Only approved findings authorize code changes. If approval is ambiguous, stop and ask.

## 2. Lock Scope

Before modifying code, identify the approved finding, expected files/symbols, intended behavior, and expected simplification.

Do not use an approved finding as permission for general cleanup. Unapproved findings remain read-only.

## 3. Apply Findings Individually

When practical:

```text
RG-001 → change → targeted verification
RG-004 → change → targeted verification
→ final verification
```

Avoid bundling unrelated findings into one large modification. This improves failure isolation and reviewability.

## 4. Decide Whether TDD Is Required

### Behavior-risking

Use TDD by default when modifying business rules, validation, conditional behavior, error handling, state derivation, persistence, semantically changed queries, API handling, async behavior, ordering, side effects, lifecycle behavior, authorization, retry/fallback behavior, or implementations whose equivalence is uncertain.

Bug fixes are behavior changes and should use TDD. When in doubt, classify as behavior-risking.

### Mechanical behavior-preserving

A new failing test is not mandatory when the change is demonstrably mechanical, for example: removing a pure forwarding wrapper; redirecting a duplicate helper to a demonstrably equivalent existing helper; removing dead local code or an unused parameter; eliminating a redundant intermediate variable; replacing identical duplicated code with an already-tested existing utility.

Existing relevant tests must still run.

If TDD is skipped, record:

```text
TDD: No
Reason: <why this modification is demonstrably mechanical and behavior-preserving>
```

"Simple change" is not a sufficient reason.

## 5. TDD Procedure

When TDD applies:

1. Identify the behavior that must remain or be corrected.
2. Write or identify the smallest meaningful test.
3. For a new regression test, run it and observe the expected failure.
4. Implement the minimum approved change.
5. Run the test and observe it pass.
6. Refactor only within approved scope.
7. Re-run the test.

Follow the project's established TDD workflow when one exists. Do not invent unrelated tests solely to satisfy process.

## 6. Preserve Existing Semantics

Before replacing new code with an existing implementation compare inputs, outputs, null/default behavior, exceptions, validation, permissions, side effects, persistence effects, call ordering, async behavior, caching, and source of truth.

Similar-looking code is not necessarily equivalent.

If equivalence cannot be established: **STOP that finding.** Report uncertainty instead of forcing reuse.

## 7. Preserve Authoritative Sources

Do not simplify external-state handling by guessing server state, reconstructing platform state locally, replacing required API results with local assumptions, preferring stale cache over authoritative responses, or introducing parallel truth.

An approved simplification does not authorize changing the system's source of truth unless explicitly approved.

## 8. Failure Handling

If an approved finding cannot be safely implemented, stop modifying that finding, preserve or restore safe behavior, and report why implementation was blocked.

If verification for one finding fails, investigate that finding instead of blindly continuing with later findings. Prefer isolating and correcting or reverting the failed change before continuing.

## 9. No Opportunistic Cleanup

Do not rename unrelated symbols, reformat unrelated files, fix unrelated warnings, update unrelated dependencies, restructure neighboring modules, or apply unapproved findings.

If another issue is discovered, report it separately. It is not automatically approved.