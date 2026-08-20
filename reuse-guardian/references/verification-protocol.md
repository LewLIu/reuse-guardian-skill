# Reuse Guardian — Verification Protocol

## Purpose

Produce fresh evidence that approved modifications were applied within scope and did not unintentionally break behavior.

**Evidence before claims.**

## 1. Scope Verification

Inspect repository state and final diff using appropriate commands such as `git status`, `git diff --stat`, `git diff --name-only`, and `git diff`.

Compare actual modifications against approved finding IDs. Confirm only approved findings were intentionally modified, no unapproved finding was applied, no unrelated cleanup occurred, and no unexpected files changed.

If unrelated pre-existing changes exist, distinguish them from changes made during Apply. Do not overwrite or revert unrelated user changes.

## 2. Targeted Verification

For each applied finding run the most relevant available tests. Targeted verification should directly exercise the changed behavior when possible.

Record command, exit status, test count when available, and failures.

## 3. TDD Evidence

When TDD was required, record RED/GREEN evidence.

For a regression test:

- **RED:** test failed for the expected reason before the fix.
- **GREEN:** the same test passed after the approved change.

Do not claim a regression test was proven without observing the meaningful failure and subsequent pass.

## 4. Broader Regression Verification

Determine the reasonable blast radius. A shared utility may require consumer/module tests; a core service may require integration tests; widely shared infrastructure may justify the full suite.

Choose verification proportional to risk. Do not use a tiny targeted test as proof that an entire high-impact system is healthy.

## 5. Build and Static Verification

Run project-supported checks when applicable: build, compile, typecheck, lint, and static analysis.

Use commands actually supported by the repository. Do not invent tooling merely for verification.

Tests passing does not prove build success; lint passing does not prove compilation; build passing does not prove behavior. Each claim requires appropriate evidence.

## 6. Semantic Diff Review

Inspect the final diff manually. For each approved finding compare before/after semantics: inputs, outputs, null/default behavior, exceptions, validation, authorization, side effects, persistence, state transitions, call ordering, async behavior, caching, and authoritative source usage.

Ask: **Did simplification accidentally change meaning?**

## 7. Approval-Scope Review

Map every intentional changed hunk back to an approved finding, for example:

```text
file A lines X-Y → RG-001
file B lines X-Y → RG-001
file C lines X-Y → RG-004
```

If an intentional modification cannot be mapped to an approved finding, investigate it. Do not silently classify it as cleanup.

## 8. Verification Failure

If any required verification fails, do not report completion.

Report the failed command/test, observed failure, affected finding, current repository state, and whether the finding was reverted or remains unresolved.

Do not hide partial failure behind successful checks.

## 9. Final Report

```text
# Reuse Guardian Apply Report

## Approved Scope
RG-001
RG-004

## Applied Findings

### RG-001
Status: Applied | Blocked | Reverted
Change: ...
TDD: Yes | No
TDD reason: Required when TDD = No.
Targeted verification:
Command: ...
Result: PASS / FAIL
Evidence: ...

## Scope Verification
Approved findings modified: ...
Unapproved findings modified: None
Unrelated intentional cleanup: None

## Broader Verification
Tests: ...
Build: ...
Typecheck: ...
Lint: ...
Other: ...

Only include checks actually available and executed.

## Semantic Review
Describe important behavior-preservation conclusions and remaining uncertainties.

## Remaining Audit Findings
RG-002
RG-003
...

## Result
State only what the collected evidence supports.
```

## 10. Completion Rule

Do not say complete, fixed, safe, behavior preserved, all tests pass, or build passes unless fresh verification evidence supports the exact claim.

If evidence is incomplete, say so explicitly.