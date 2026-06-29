---
name: executing-plans
description: >-
  Execute a written implementation plan task by task with verification and
  review checkpoints. Use when a plan exists in docs/plans/ or the user
  provides a task list to implement with structured progress tracking.
---

# Executing Plans

Load a written plan, sanity-check it, then execute tasks in order with verification at each step. Stop when blocked. Report progress at checkpoints.

Announce at start: "I'm using the executing-plans skill to implement this plan."

## Prerequisites

- A plan file (typically from the planning skill) or an equivalent task list from the user
- User consent to start implementation
- Do not begin on `main`/`master` without explicit user approval — use a feature branch when the plan involves git work

## Step 1: Load and review

1. Read the full plan
2. Scan the codebase areas the plan touches
3. Review critically — note gaps, wrong paths, missing dependencies, or ambiguous steps
4. If concerns exist, raise them with the user before writing code
5. If the plan is sound, create a todo list from the plan tasks and begin

## Step 2: Execute in batches (the build loop)

Work in batches of **3 tasks** (adjust down if tasks are large). Each task is built with the **test-driven-development** skill — that red-green-refactor loop with `node:test` is the inner engine; this skill orchestrates batches and checkpoints around it. For each task:

1. Mark the task in progress
2. Run the TDD loop: failing test → minimal code → refactor, until the task's tests pass (follow the plan's test/implementation steps; do not skip the "watch it fail" verification)
3. Run tests and commands exactly as the plan specifies
4. Mark complete only when verification passes
5. Commit when the plan says to

After each batch, **checkpoint** with the user:

```text
Batch complete — tasks N–M done.

Completed:
- [summary]

Verification:
- [test results]

Up next:
- [next tasks]

Continue, or pause for review?
```

Wait for approval before the next batch unless the user said to run through without stopping.

## Step 3: Verify end-to-end (the outer loop)

When all tasks are built and the unit/functional suite is green, the build isn't done until the running app is verified. Run the **end-to-end-testing** skill:

1. It exercises the running app against the acceptance criteria in `docs/technical/requirements.md`
2. **If e2e fails**, it files fix tasks and hands control back here — re-enter the build loop (Step 2) on those tasks, then re-verify. Repeat until e2e is green
3. **If e2e passes**, proceed to finish

Do not treat the work as complete while e2e is red.

## Step 4: Finish

When all tasks are done and e2e is green:

1. Run the full test suite and e2e once more to confirm
2. Summarise what changed: files touched, behaviour added, anything deferred
3. Hand off to the **shipping** skill to open a PR, or present options (merge, keep the branch, more work)

If the project uses a finishing or review workflow, follow it. Otherwise, present options clearly.

## When to stop

Stop immediately and ask the user when:

- A test fails and the fix is not obvious from the plan
- A file or dependency in the plan does not exist
- Instructions contradict the codebase or each other
- You need a decision the plan does not cover
- Verification fails twice on the same step

Do not guess past blockers. Do not silently change the plan's approach.

## When to revisit the plan

Return to review (Step 1) when:

- The user updates the plan based on what you found
- A fundamental assumption in the plan was wrong
- Scope needs to shrink or split mid-execution

Minor deviations (typo in path, renamed export) can be fixed inline and noted in the checkpoint summary.

## Working style

- Follow the plan's code and commands; do not substitute your own architecture
- Keep changes minimal per task — no drive-by refactors
- Match existing project conventions (style, imports, test patterns)
- Update todos as you go so progress is visible

## Integration

Typical workflow:

1. **brainstorming** — design and spec
2. **technical-documentation** — architecture, data model, tech-stack decisions
3. **planning** — the plan document
4. **project-setup** — scaffold the repo and wire `node:test`
5. **executing-plans** — orchestrate the build (this skill)
   - **test-driven-development** — inner red-green-refactor loop per task
   - **end-to-end-testing** — outer verification gate; retriggers the build loop on failure
6. **shipping** — PR and release

If subagents are available, dispatching one task per subagent with review between tasks is fine. This skill still applies for checkpointing and verification.
