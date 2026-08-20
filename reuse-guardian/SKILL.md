---
name: reuse-guardian
description: Use when implementation, bug fixing, or refactoring has changed existing code and the result may duplicate existing capabilities, introduce unnecessary abstractions, diverge from established patterns, or require behavior-preserving simplification.
---

# Reuse Guardian

## Overview

Reuse Guardian protects an existing codebase from unnecessary growth, duplicate implementations, incorrect abstractions, and unsafe simplification.

**Search aggressively. Audit adversarially. Modify only with explicit human approval. Verify with evidence.**

The goal is reuse, consistency, meaningful boundaries, minimal unnecessary change, and preserved behavior — not fewer methods, fewer classes, fewer lines, maximum DRY, or maximum abstraction.

A small amount of duplication is preferable to the wrong abstraction.

## Mandatory Workflow

```text
AUDIT
  ↓
REPORT
  ↓
STOP — HUMAN APPROVAL REQUIRED
  ↓
APPLY APPROVED ITEMS
  ↓
VERIFY
  ↓
FINAL REPORT
```

## Iron Rule

**AUDIT MUST NOT MODIFY CODE.**

During Audit, do not modify production code, tests, configuration, or other files. Do not fix findings, refactor nearby code, or make "obvious" improvements.

Audit means read, search, analyze, and report only.

After producing the audit report: **STOP and wait for explicit human approval.**

Valid approval names the findings or an unambiguous subset, for example:

```text
Apply RG-001 and RG-003.
```

When approval is ambiguous, do not infer permission.

## Phase 1 — Audit

Read `references/audit-protocol.md` and follow it completely.

Audit primarily the current task's diff or recently modified code. Inspect existing repository code when necessary to determine whether equivalent functionality or established patterns already exist and whether authoritative boundaries are preserved.

Every actionable finding receives a stable ID: `RG-001`, `RG-002`, etc. Justified new abstractions may be reported as `KEEP-001`, `KEEP-002`, etc.

Every actionable finding must include ID, Severity, Confidence, Category, Location, Finding, Evidence, Existing implementation when applicable, Recommendation, Risk/trade-off, and Proposed change scope.

After the report: **STOP. DO NOT APPLY ANY FINDING.**

## Phase 2 — Approval

Only explicit human approval authorizes modification. Approval applies only to the specifically approved findings. Unapproved findings remain read-only.

Do not treat statements such as "looks good", "interesting", "continue", "what would you do?", or "can this be improved?" as approval to modify code.

If approval specifies finding IDs, scope is locked to those IDs. Do not fix neighboring findings or perform unrelated cleanup.

## Phase 3 — Apply

Read `references/apply-protocol.md` and follow it completely.

Apply approved findings individually whenever practical, with targeted verification after each change.

### Behavior-risking changes

Use TDD by default when a change fixes a bug or changes/risks conditions, validation, state derivation, error handling, persistence, API handling, asynchronous behavior, ordering, side effects, authorization, lifecycle behavior, or semantic equivalence.

### Mechanical behavior-preserving changes

A new failing test is not mandatory when a change is demonstrably mechanical and behavior-preserving, such as removing a pure forwarding wrapper, replacing a duplicate helper with a demonstrably equivalent existing helper, or removing dead local code.

Existing relevant tests must still run. If TDD is not used, the final report MUST explain why it was unnecessary.

When behavioral equivalence is uncertain, treat the change as behavior-risking.

## Phase 4 — Verify

Read `references/verification-protocol.md` and follow it completely.

Verification is evidence gathering, not intuition. At minimum verify approval scope, targeted behavior, broader regression risk, build/static health where applicable, and final semantic diff.

Do not claim fixed, complete, safe, behavior-preserving, tests pass, or build succeeds without fresh evidence.

## Authoritative Boundaries

External authoritative sources may include platform APIs, server responses, database state, operating-system APIs, and third-party SDK responses.

Do not replace authoritative values with locally inferred, reconstructed, cached-over, or synthesized state merely to simplify architecture.

Reuse must not change the source of truth. Simplification must not weaken correctness boundaries.

## Preserve Meaningful Abstractions

Do not inline or remove an abstraction merely because it is small. A small method may be justified when it expresses a domain rule, policy, lifecycle boundary, validation, authorization, orchestration, error translation, state transition, or meaningful test seam.

Conversely, question abstractions that merely rename another operation, forward arguments unchanged, wrap a single existing call without semantics, duplicate an existing helper, or exist for hypothetical future use.

Never optimize for method count.

## Conservative Default

When evidence is insufficient: **do not modify.** Report uncertainty.

A correct KEEP decision is better than an unsafe simplification.

The best audit result may be:

**No actionable findings. No simplification needed.**