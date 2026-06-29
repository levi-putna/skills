---
name: conformance-check
description: >-
  Detect drift between docs/technical/ and the codebase — missing tests for
  requirements, orphan tests, locked tech-stack violations, and unmapped code.
  Use before reconciling-changes or planning, after a build completes, or when
  the user asks if code matches the spec or docs are out of date.
---

# Conformance Check

Audit whether the codebase still matches `docs/technical/`. Find drift in both directions: docs that code doesn't satisfy, and code/tests that no longer map to any requirement.

Announce at start: "I'm using the conformance-check skill to audit docs against code."

Read-only by default — report findings first. Fix only when the user asks to reconcile drift.

## Where this sits

```
                    ┌─ conformance-check (audit)
                    │
refining-docs  →  reconciling-changes  →  planning  →  executing-plans
                    │                                              │
                    └─ conformance-check (post-build verify) ◄─────┘
```

Run **before** impact analysis to surface existing drift. Run **after** a delta build to confirm the app matches the updated spec.

## Prerequisites

- `docs/technical/` exists (at minimum `requirements.md`)
- A codebase to inspect
- For post-build checks: the test suite should be runnable

## The flow

### 1. Load the spec side

Read:

- `docs/technical/requirements.md` — every REQ-ID and acceptance criterion
- `docs/technical/tech-stack.md` — every 🔒 human-locked choice
- `docs/technical/api-contracts.md`, `data-model.md` — when the project has them
- `docs/technical/engineering-guidelines.md` — agent rules and testing strategy

Extract a checklist of traceable items: REQ-IDs, locked stack entries, contract endpoints, entities.

### 2. Load the code side

Scan:

- Test files (`src/**/*.test.ts`, `test/**/*.test.ts`, e2e tests)
- Source modules referenced by requirements and architecture
- `package.json` / config for locked tech choices
- Recent git history if helpful for orphan detection

Look for REQ-ID references in test names, comments, or describe blocks (e.g. `REQ-042`, `[REQ-042-AC1]`).

### 3. Run checks

Report findings in these categories:

#### A. Requirement coverage

| Finding | Severity | Meaning |
|---|---|---|
| REQ with no test | 🔴 Critical | Acceptance criteria not verified |
| REQ marked removed in docs but code remains | 🟡 Warning | Stale implementation |
| AC untestable (vague wording) | 🟡 Warning | Doc quality issue — suggest refining-docs |

#### B. Test traceability

| Finding | Severity | Meaning |
|---|---|---|
| Test with no REQ-ID mapping | 🟡 Warning | Orphan test — may be stale or untracked |
| Test references unknown REQ-ID | 🔴 Critical | Test and docs disagree |
| Duplicate tests for same AC | 🟢 Info | Possible consolidation |

#### C. Locked decision compliance

| Finding | Severity | Meaning |
|---|---|---|
| 🔒 stack choice violated in code/deps | 🔴 Critical | Agent or dev changed a human-locked decision |
| Config contradicts `architecture.md` | 🟡 Warning | Doc or code needs update |

#### D. Contract and model alignment

When `api-contracts.md` or `data-model.md` exist:

- Endpoints in code but not in contracts (or vice versa)
- Entities/fields in schema but not in data model (or vice versa)

#### E. Scope boundary

- Code modules with no doc reference and no obvious REQ mapping — flag as **unowned** (may be dead code or missing docs)

### 4. Conformance report

Save to:

- Default: `docs/plans/YYYY-MM-DD-conformance-report.md` (or append to an existing impact report if run as part of reconciling)
- Ephemeral report in chat is fine when the user only wants a quick audit

Template:

```markdown
# Conformance Report

> **Date:** YYYY-MM-DD
> **Docs:** docs/technical/
> **Codebase:** [branch / commit if relevant]

## Summary

[Pass / fail / partial — one paragraph]

## Critical (must fix before planning or shipping)

- [ ] …

## Warnings (should fix; may proceed with acknowledgement)

- [ ] …

## Info

- …

## Traceability matrix (sample)

| REQ-ID | Tests | Status |
|---|---|---|
| REQ-001 | `src/auth.test.ts` | ✅ covered |
| REQ-042 | — | ❌ no test |

## Recommended next step

- [ ] refining-docs — fix doc gaps
- [ ] reconciling-changes — plan code updates
- [ ] Add missing tests in next delta plan
```

**✋ Checkpoint** — present findings; let the user decide what to fix now vs defer.

### 5. Hand off

| Outcome | Suggest |
|---|---|
| Doc gaps (vague REQs, missing IDs) | **refining-docs** |
| Spec changed, code behind | **reconciling-changes** |
| Code drift on locked decisions | **refining-docs** (if docs wrong) or fix in next **delta plan** |
| All green | **planning** or **shipping** as appropriate |

## Principles

- **Read-only audit first** — diagnose before editing
- **REQ-IDs are the spine** — every requirement should map to ≥1 test
- **🔒 means 🔒** — locked stack violations are always critical
- **Both directions** — docs ahead of code and code ahead of docs are both drift
- **Actionable output** — every finding names a file, REQ-ID, or next skill

## Anti-patterns

- Auto-fixing code or docs without user direction
- Treating missing REQ-IDs as "all fine" — flag and suggest adding them
- Running only unit tests and skipping doc reads
- Marking conformance pass when critical findings remain
