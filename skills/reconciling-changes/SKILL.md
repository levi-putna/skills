---
name: reconciling-changes
description: >-
  Analyse what changed in docs/technical/ since the last baseline and produce
  an impact report before code changes. Use when requirements or technical docs
  were updated, when syncing code to new documentation, when the user asks what
  needs to change, or before planning a brownfield delta. Classifies doc changes
  as ADD/MODIFY/REMOVE/CLARIFY and maps them to affected files and tests.
---

# Reconciling Changes

Bridge between **refining-docs** (human updates the spec) and **planning** (agent builds a delta). Analyse what changed in the living docs, map the impact on existing code and tests, and produce a reviewable impact report — before any implementation starts.

Announce at start: "I'm using the reconciling-changes skill to analyse the doc delta and impact."

Do not write application code in this skill. The output is an impact report and a hand-off to planning (delta mode).

## Where this sits

```
refining-docs  →  reconciling-changes  →  planning (delta)  →  executing-plans
docs/technical/    docs/plans/impact-…      docs/plans/
```

For greenfield work (no existing app), skip this skill and go straight to **planning** (greenfield mode).

## Prerequisites

- An existing codebase with `docs/technical/` as the source of truth
- A doc change the user wants to implement — or a request to "sync code to the updated docs"
- If docs haven't been updated yet, suggest **refining-docs** first

## Establishing the baseline

Before diffing, identify what "before" means. Use the first available source, in order:

1. **Last shipped plan** — most recent file in `docs/plans/` for this feature area
2. **Git tag or merge commit** — user-provided or `git log` for the last release/PR
3. **Git diff** — `git diff <baseline-commit> -- docs/technical/` if the user names a commit
4. **Doc changelog** — entries in `docs/technical/CHANGELOG.md` since the last baseline date

Report which baseline you used. If none exists, tell the user and ask them to pick a commit or date before continuing.

## The flow

### 1. Load both sides

1. Read the current `docs/technical/` set (at minimum `requirements.md`; include `api-contracts.md`, `data-model.md`, `architecture.md` when relevant)
2. Load the baseline (prior plan, git version of docs, or changelog entries)
3. Scan the codebase areas the docs describe — modules, routes, tests, migrations

### 2. Classify each scenario by scenario

For each requirement, contract, entity, or NFR that differs between baseline and current docs, assign one type:

| Type | Meaning | Typical code action |
|---|---|---|
| **ADD** | New requirement or capability | New code + new tests |
| **MODIFY** | Same intent, changed behaviour or constraints | Surgical edit + update tests |
| **REMOVE** | Deliberately dropped from scope | Remove or deprecate code + tests |
| **CLARIFY** | Wording only; behaviour unchanged | No code change; note in report |

Use stable **REQ-IDs** from `requirements.md` (e.g. `REQ-042`, `REQ-042-AC1`). If docs lack IDs, flag that and assign temporary IDs for the report; suggest **refining-docs** to add them before planning.

### 3. Map impact

For each ADD/MODIFY/REMOVE item, record:

- **REQ-ID(s)** affected
- **Doc sections** that changed (file + heading)
- **Likely files** — create / modify / delete, with paths from the codebase scan
- **Existing tests** — which cover this behaviour today (`*.test.ts`, e2e)
- **New tests needed** — which acceptance criteria need new or updated assertions
- **Ripple effects** — downstream contracts, migrations, UI copy, NFR checks

Explicitly list **frozen scope** — areas of the app and docs with no classified changes. The delta plan must not touch these.

### 4. Impact report

Save to:

- Default: `docs/plans/YYYY-MM-DD-<short-name>-impact.md`
- User's preferred location overrides the default

Use this template:

```markdown
# <Feature> — Change Impact Report

> **Baseline:** [plan file / commit / date]
> **Current docs:** docs/technical/ as of YYYY-MM-DD
> **Prepared for:** delta planning — no code changes yet

## Summary

[2–4 sentences: what changed in the docs and the overall implementation scope]

## Change inventory

| Type | REQ-ID | Summary | Code impact |
|---|---|---|---|
| ADD | REQ-055 | … | New module + tests |
| MODIFY | REQ-042 | … | Edit `src/…`, update tests |
| REMOVE | REQ-018 | … | Delete handler + tests |
| CLARIFY | REQ-003 | … | None |

## Frozen scope (do not change)

- [Modules, files, or requirements explicitly untouched]

## File map

| Action | Path | Driven by |
|---|---|---|
| Modify | `src/…` | REQ-042 |
| Create | `src/….test.ts` | REQ-055-AC1 |

## Test map

| REQ-ID | Existing test | Action |
|---|---|---|
| REQ-042 | `src/cart.test.ts` | Update assertions |
| REQ-055 | — | Create new |

## Risks and open questions

- [Decisions the docs leave ambiguous, migration concerns, breaking changes]

## Recommended next step

Run **planning** in **delta mode** using this report as input.
```

**✋ Checkpoint — wait for user approval** before suggesting planning.

### 5. Hand off

When approved:

> "Impact report saved to `[path]`. Want me to turn this into a delta implementation plan with the planning skill?"

Do not start planning or coding without go-ahead.

## Integration with other skills

| Skill | When |
|---|---|
| **refining-docs** | Docs need updating before impact analysis |
| **conformance-check** | Run before reconciling to find existing drift |
| **planning** (delta mode) | After impact report is approved |
| **executing-plans** | Executes the delta plan |
| **conformance-check** | Run again after build, before shipping |

## Principles

- **Docs lead, code follows** — the human changed the spec; code catches up surgically
- **Classify before coding** — every change gets ADD/MODIFY/REMOVE/CLARIFY
- **Frozen scope is explicit** — prevents full-app rewrites
- **REQ-IDs are the traceability spine** — link docs, impact, plan tasks, and tests
- **Impact before plan** — no delta plan without an approved impact report

## Anti-patterns

- Writing code in this skill
- Planning a full greenfield rebuild when only a delta is needed
- Skipping frozen scope — leaving the agent free to "clean up" unrelated code
- Treating CLARIFY as MODIFY without evidence the behaviour changed
- Proceeding without a baseline — diffing against nothing produces noise
