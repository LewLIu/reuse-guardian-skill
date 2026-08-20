# Reuse Guardian — Audit Protocol

## Purpose

Audit recently changed code for unnecessary growth, duplicated capability, incorrect abstraction, inconsistent project patterns, and unsafe handling of authoritative state.

**Audit is strictly read-only.**

## 1. Establish Scope

Determine what changed using repository-native evidence such as `git status`, `git diff`, `git diff --stat`, and `git diff --name-only`. When appropriate, compare against the relevant base branch or commit.

Identify modified/new files, functions, methods, classes, helpers, utilities, wrappers, queries, state, and changed behavior. Do not assume every modified line is problematic.

## 2. Search Before Judging

For every meaningful newly introduced capability, actively search the repository for existing implementations with the same or similar responsibility.

Search by multiple signals when useful: exact/partial names, synonyms, domain terminology, called APIs, parameter/return types, distinctive expressions, neighboring services, repositories, utilities, converters, validators, handlers, controllers, and equivalent behavior under another name.

Do not claim "no existing implementation" without searching.

## 3. Reuse Review

Look for duplicate helpers/services, equivalent queries, duplicate transformations, validation, null/default handling, error translation, constants, parallel domain-rule implementations, and existing APIs bypassed by new local implementations.

Prefer in order: direct reuse; extension of a semantically matching existing implementation; consolidation into an appropriate existing abstraction; new implementation only when responsibility is genuinely new.

Do not recommend reuse solely because code looks similar. Semantic responsibility must match.

## 4. Simplification Review

Look for forwarding wrappers, unnecessary helpers/interfaces/factories, one-use generic abstractions, redundant state/conversions/parameters, impossible-state defensive branches, premature extension points, compatibility layers without requirements, unnecessary indirection, and unnecessary configuration.

Apply YAGNI. Do not penalize meaningful domain abstractions.

## 5. Consistency Review

Inspect neighboring code and existing conventions for validation, persistence, DTO conversion, logging, error handling, retries, dependency injection, configuration, asynchronous work, state management, API integration, and naming.

Prefer established conventions unless there is concrete justification for a new pattern.

## 6. Abstraction Review

For each new abstraction ask:

1. Does equivalent functionality already exist?
2. Does it represent a meaningful responsibility or domain concept?
3. Does it provide a concrete boundary such as validation, policy, authorization, orchestration, lifecycle management, error translation, caching, or testability?
4. Does it consolidate genuine duplication where callers share the same responsibility and reason to change?

If none apply, question whether the abstraction should exist. Never use method length alone as evidence.

## 7. Authoritative Source Review

Identify authoritative external state such as platform API responses, server responses, database records, OS APIs, and SDK results.

Check whether new code guesses/reconstructs authoritative state, introduces stale parallel state, prefers cache over required authoritative responses, or silently changes the source of truth.

Treat source-of-truth changes as high risk.

## 8. KEEP Review

Actively identify new abstractions that are justified. This prevents simplification bias.

Example:

```text
KEEP-001
Location: RoomService.validateJoin()
Decision: Keep.
Reason: Although small, this method represents the complete domain rule governing whether a player may enter a room.
```

## 9. Severity

- **Critical** — likely correctness failure, source-of-truth violation, severe regression risk, or architectural duplication capable of conflicting behavior.
- **High** — duplicate core business behavior, parallel implementation, materially wrong abstraction, or significant architectural divergence.
- **Medium** — unnecessary wrapper/helper/indirection, avoidable duplication, inconsistent local pattern, or maintainability problem.
- **Low** — minor simplification opportunity with limited practical impact.

Severity describes impact if the finding is correct, not certainty.

## 10. Confidence

- **High** — direct evidence demonstrates the issue.
- **Medium** — evidence strongly suggests the issue but semantic uncertainty remains.
- **Low** — potential issue worth human review but evidence is insufficient for confident action.

Confidence and Severity must remain independent.

## 11. Required Report Format

```text
# Reuse Guardian Audit Report

## Scope
Files reviewed: ...
Diff basis: ...

## Executive Summary
Actionable findings: N
Critical: N
High: N
Medium: N
Low: N
KEEP findings: N

No code was modified.

---

## RG-001 — <short title>
Severity: High
Confidence: High
Category: Reuse | Simplification | Consistency | Abstraction | Source of Truth
Location: path/to/file.ext:line or symbol
Finding: ...
Evidence: ...
Existing implementation: path/to/existing/file.ext:symbol
Recommendation: ...
Risk / trade-off: ...
Proposed change scope: ...

---

## KEEP-001 — <short title>
Location: ...
Decision: Keep.
Reason: ...

---

## Approval Required
No code has been modified.
Reply with the finding IDs you approve for modification.
Example: Apply RG-001 and RG-003.
```

## 12. Mandatory Stop

After producing the report: **STOP.**

Do not fix findings, edit code/tests, prepare speculative patches, or perform unrelated cleanup. Wait for explicit approval.