# Reuse Guardian

**An approval-gated code reuse and simplification audit skill for AI coding agents.**

[简体中文](README.zh-CN.md)

> **Search aggressively. Audit adversarially. Modify only with explicit human approval. Verify with evidence.**

Reuse Guardian helps AI coding agents protect existing codebases from duplicate implementations, unnecessary abstractions, inconsistent patterns, and unsafe simplification.

Unlike an automatic code simplifier, Reuse Guardian is **audit-first**. Its initial pass is strictly read-only. It searches the repository, produces an evidence-backed audit report, then stops. Code changes are allowed only after a human explicitly approves specific finding IDs.

## Why Reuse Guardian?

AI coding agents are good at producing working code, but they can also grow a codebase unnecessarily:

- creating a new helper when one already exists;
- wrapping an existing API without adding semantics;
- introducing a second architectural pattern;
- extracting superficially similar code into the wrong abstraction;
- simplifying away meaningful domain boundaries;
- changing a source of truth in the name of architectural cleanliness.

Reuse Guardian adds a deliberate review gate between implementation and modification.

## Workflow

```text
AUDIT (read-only)
  ↓
REPORT (RG-001, RG-002, KEEP-001...)
  ↓
STOP — HUMAN APPROVAL REQUIRED
  ↓
APPLY APPROVED ITEMS ONLY
  ↓
VERIFY WITH FRESH EVIDENCE
  ↓
FINAL REPORT
```

### 1. Audit

The agent inspects the current diff and searches the existing repository for reusable implementations, established patterns, unjustified abstractions, duplicated logic, and source-of-truth risks.

Every actionable finding gets a stable ID such as `RG-001`. Justified new abstractions may be recorded as `KEEP-001` so the audit does not become biased toward deleting code.

**The audit must not modify production code, tests, or configuration.**

### 2. Human approval

After reporting, the agent stops. You explicitly choose what may change:

```text
Apply RG-001 and RG-003.
```

Unapproved findings remain read-only. Approval for one finding is not permission for nearby cleanup.

### 3. Apply

Approved findings are applied individually when practical. Behavior-risking changes use TDD by default. Demonstrably mechanical, behavior-preserving changes may skip a new failing test, but the reason must be documented and relevant existing tests must still run.

### 4. Verify

Verification checks:

- approval scope;
- targeted behavior;
- broader regression risk;
- build/typecheck/lint/static health when supported;
- final semantic diff;
- preservation of authoritative sources of truth.

Completion claims require fresh evidence.

## Core principles

- **Reuse before creating.** Search before adding functions, classes, helpers, utilities, wrappers, queries, or abstractions.
- **Do not optimize for fewer methods or fewer lines.** Preserve meaningful domain concepts and boundaries.
- **DRY is not absolute.** Small duplication is preferable to the wrong abstraction.
- **Follow the existing codebase.** Prefer established project patterns over introducing a theoretically cleaner second architecture.
- **Respect authoritative state.** Do not replace required platform/server/database/SDK results with locally inferred state merely to simplify code.
- **Conservative by default.** When equivalence is uncertain, report the uncertainty instead of forcing reuse.
- **No modification without approval.** Audit and execution are deliberately separated.

## Audit findings

A finding includes:

```text
ID
Severity
Confidence
Category
Location
Finding
Evidence
Existing implementation (when applicable)
Recommendation
Risk / trade-off
Proposed change scope
```

Severity and confidence are deliberately independent. A finding can be `High` severity but `Low` confidence: serious if true, but unsafe to act on without more evidence.

## Installation

Clone the repository and copy the `reuse-guardian` directory into your agent skills directory.

For runtimes that support the cross-runtime Agent Skills location:

```bash
git clone https://github.com/LewLIu/reuse-guardian-skill.git
cp -r reuse-guardian-skill/reuse-guardian ~/.agents/skills/
```

Result:

```text
~/.agents/skills/
└── reuse-guardian/
    ├── SKILL.md
    ├── references/
    └── tests/
```

Then ask your coding agent, for example:

```text
Use reuse-guardian to audit the current uncommitted changes.
```

A correct first invocation should search and report, state that no code was modified, and stop for approval.

## Suggested global agent rule

You can add a short rule to your global/project agent instructions:

```markdown
After completing a non-trivial implementation, bug fix, or refactoring task,
use the `reuse-guardian` skill before final completion.

The initial audit is strictly read-only. Stop after the audit report and wait
for explicit human approval of specific RG finding IDs. Only approved findings
may be applied. Behavior-risking changes should use TDD by default. Perform
fresh verification before claiming completion.
```

Keep the full workflow in the skill itself rather than duplicating it in global instructions.

## Repository structure

```text
reuse-guardian-skill/
├── README.md
├── README.zh-CN.md
├── LICENSE
└── reuse-guardian/
    ├── SKILL.md
    ├── references/
    │   ├── audit-protocol.md
    │   ├── apply-protocol.md
    │   └── verification-protocol.md
    └── tests/
        ├── duplicate-helper.md
        ├── thin-wrapper.md
        ├── justified-method.md
        ├── wrong-abstraction.md
        └── existing-pattern.md
```

## TDD policy

Reuse Guardian does not force ceremonial TDD for every mechanical cleanup.

Use TDD by default when a change can affect behavior: business rules, validation, error handling, state derivation, persistence, API handling, async behavior, side effects, or uncertain equivalence.

A new failing test may be skipped for a demonstrably mechanical behavior-preserving change, but the final report must explain why, and relevant existing tests still have to run.

## License

MIT License. See [LICENSE](LICENSE).

## Status

Reuse Guardian is intentionally conservative. The safest valid result of an audit can be:

```text
No actionable findings. No simplification needed.
```

Contributions and real-world failure cases are welcome, especially cases where an agent duplicated existing capabilities, over-abstracted code, or simplified away important semantics.