---
name: planning
description: >-
  Turn an approved design or spec into a step-by-step implementation plan.
  Supports greenfield (new feature) and delta (doc change on existing app) modes.
  Use after brainstorming or reconciling-changes, when requirements are clear,
  before writing code, or when the user asks for an implementation plan or task breakdown.
---

# Planning

Turn an approved design into a plan an engineer (or agent) can execute without guessing. Assume the implementer is capable but has zero context about this codebase and may not know your conventions yet.

Announce at start: "I'm using the planning skill to write the implementation plan."

## Planning modes

Pick the mode before writing tasks:

| Mode | When | Input |
|---|---|---|
| **Greenfield** | New project, new subsystem, no existing code to preserve | Approved `docs/technical/` or design spec |
| **Delta** | Doc change on an existing app | Approved impact report from **reconciling-changes** |

**Greenfield** — full plan covering all requirements in scope.

**Delta** — tasks **only** for ADD/MODIFY/REMOVE items in the impact report. Include an explicit **frozen scope** section copied from the impact report. Do not plan tasks for CLARIFY items or frozen areas.

If the user asks to implement doc changes on brownfield code without an impact report, suggest **reconciling-changes** first.

## Prerequisites

- An approved design spec, impact report, or clear requirements from the user
- If a technical doc set exists (`docs/technical/` from the technical-documentation skill), read it first — it carries the locked tech-stack decisions, architecture, and constraints the plan must respect
- If requirements are still fuzzy, use the brainstorming skill first

Save plans to:

- Default: `docs/plans/YYYY-MM-DD-<short-name>.md`
- User's preferred location overrides the default

## Scope check

If the spec covers multiple independent subsystems, split into separate plans. Each plan should produce working, testable software on its own.

Before writing tasks, map files to create or modify and what each one owns. Lock in decomposition here:

- One clear responsibility per file where possible
- Files that change together live together
- Follow existing codebase patterns; do not restructure unrelated code
- If a file you must touch is already unwieldy, include a focused split in the plan

## Task sizing

A task is the smallest unit worth its own review gate. It should:

- End with something independently testable
- Include its own setup steps if needed (do not orphan "create the file" as a separate task)
- Take roughly 5–20 minutes for a skilled implementer, not hours

Each task uses checkbox steps (`- [ ]`) for tracking.

## Plan header

Every plan starts with:

```markdown
# [Feature Name] Implementation Plan

> **For implementers:** Execute with the executing-plans skill, or work through tasks directly with review checkpoints.

**Mode:** Greenfield | Delta

**Goal:** [One sentence]

**Architecture:** [2–3 sentences on approach]

**Tech stack:** [Key libraries, frameworks, runtime]

## Constraints

[Project-wide rules copied verbatim from the spec: versions, naming, platforms, copy rules, etc.]

---
```

For **delta** plans, add after Constraints:

```markdown
## Delta context

**Baseline:** [impact report path / commit / date]
**Doc changes:** [REQ-IDs from impact report — ADD/MODIFY/REMOVE only]

## Frozen scope (do not change)

[Copied verbatim from the impact report — modules, files, requirements untouched]
```

## Task template

````markdown
### Task N: [Name]

**REQ-IDs:** [e.g. REQ-042, REQ-042-AC1]

**Files:**
- Create: `exact/path/to/file.js`
- Modify: `exact/path/to/existing.js` (around lines X–Y if helpful)
- Test: `exact/path/to/file.test.js`

**Depends on:** [Earlier tasks, or "none"]

**Produces:** [Functions, exports, or behaviour later tasks rely on — names and signatures]

- [ ] **Step 1: Write the failing test**

```js
// complete test code
```

- [ ] **Step 2: Run test — expect fail**

Run: `npm test -- path/to/test.js`
Expected: FAIL — [reason]

- [ ] **Step 3: Minimal implementation**

```js
// complete implementation code
```

- [ ] **Step 4: Run test — expect pass**

Run: `npm test -- path/to/test.js`
Expected: PASS

- [ ] **Step 5: Commit**

```bash
git add ...
git commit -m "feat: ..."
```
````

Adapt commands to the project's test runner and conventions.

## No placeholders

These are plan failures — never use them:

- TBD, TODO, "implement later", "add error handling"
- "Write tests for the above" without test code
- "Similar to Task N" instead of repeating code
- Steps that describe work without showing code when code changes
- References to functions or types not defined in an earlier task

Every step must contain what the implementer needs to act without opening the chat history.

## Self-review

After the full plan is written:

1. **Spec coverage** — each spec requirement maps to at least one task; delta plans cover every ADD/MODIFY/REMOVE from the impact report and nothing in frozen scope
2. **Placeholder scan** — fix any vague steps
3. **Name consistency** — function names, paths, and types match across tasks
4. **Order** — no task depends on something not built yet

Fix issues inline before presenting the plan.

## Hand off

When the plan is saved, offer:

> "Plan saved to `[path]`. Want me to execute it with executing-plans (batched with checkpoints), or will you take it from here?"

If executing in the same session, invoke the executing-plans skill. Do not start coding without the user's go-ahead.

## Principles

- **TDD** — failing test first, then minimal code, then refactor if needed
- **REQ-ID traceability** — every task cites the requirement(s) it satisfies
- **Delta discipline** — brownfield plans change only what the impact report lists
- **DRY and YAGNI** — only what the spec requires
- **Frequent commits** — one logical commit per task when possible
- **Exact paths** — no "the utils folder" without a full path
