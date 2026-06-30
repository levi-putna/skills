---
name: project-setup
description: >-
  Scaffold a new project from its technical docs so the TDD build loop can run.
  Use once after planning, before executing-plans — to initialise the repo
  structure, package.json, the node:test runner, tooling, CI, and a seed
  CLAUDE.md/AGENTS.md. For UI projects, also ensure design-system and component
  library prerequisites are ready. Use when starting a greenfield codebase or
  when a project has docs but no working skeleton yet.
---

# Project Setup

Turn an approved technical doc set into a working, empty project skeleton that builds, lints, and runs `node --test` green on a zero-test suite — so the test-driven-development loop has solid ground to stand on. The human defined the stack; this skill wires it up exactly as specified.

Announce at start: "I'm using the project-setup skill to scaffold the project."

Do not implement features here. The output is a runnable skeleton, not application logic.

## Where this sits

```
planning  →  project-setup  →  executing-plans (TDD build loop)
docs/plans/   scaffolds repo    writes features
```

## Prerequisites

- A technical doc set in `docs/technical/` (from the technical-documentation skill), especially `tech-stack.md`, `architecture.md`, and `engineering-guidelines.md`
- For UI projects, `docs/technical/ui-requirements.md` should exist. If it does, run or schedule the **design-system** skill before component implementation.
- If those don't exist, suggest the technical-documentation skill first
- User consent to create files and install dependencies

## Honour the locked decisions

Read `docs/technical/tech-stack.md` first. Every 🔒 **human-locked** choice is non-negotiable — use exactly that language, framework, and version. For 🤖 **agent-discretion** rows, pick a sensible default and state what you chose so the user can override. Never silently substitute a locked choice.

## The flow

### 1. Read the docs and confirm the toolchain

Summarise the stack you're about to scaffold in a few lines — runtime version, language, framework, test setup — and flag any 🤖 choices you're filling in.

> "From `tech-stack.md`: Node 22 (🔒), TypeScript (🔒), Fastify (🔒). Test runner unspecified, so I'll use the built-in `node:test` per the standard. Linter unspecified — I'll add ESLint + Prettier. OK to proceed?"

**✋ Checkpoint — confirm before creating files.**

### 2. Scaffold the structure

Create the directory layout from `architecture.md` (components → folders). Follow conventions in `engineering-guidelines.md`. Typical Node project:

```
src/                  # application code
test/                 # functional/integration tests (or *.test.ts next to source for unit)
docs/                 # already exists
package.json
tsconfig.json         # if TypeScript
.gitignore
.github/workflows/ci.yml
README.md             # project-level, not this skill's
```

### 3. Wire up node:test

The test runner is the spine of the build loop. Configure `package.json` scripts so the TDD loop has exact commands to run:

```jsonc
{
  "type": "module",
  "scripts": {
    "test": "node --test",
    "test:watch": "node --test --watch",
    "test:coverage": "node --test --experimental-test-coverage",
    "test:unit": "node --test 'src/**/*.test.ts'",
    "test:functional": "node --test 'test/**/*.test.ts'"
  }
}
```

Notes:
- Use Node's built-in runner (`node:test`) and `node:assert/strict` — no third-party test framework unless `tech-stack.md` locks one.
- **TypeScript:** on Node 22.6+ use `node --test --experimental-strip-types`; otherwise add `tsx` and run `node --import tsx --test`. Pick based on the locked Node version and say which.
- Test file convention: `*.test.ts`. Document it in `engineering-guidelines.md` if not already there.

Add one trivial smoke test so the suite is runnable:

```ts
import { test } from 'node:test';
import assert from 'node:assert/strict';

test('test runner is wired up', () => {
  assert.equal(1 + 1, 2);
});
```

### 4. Tooling and CI

- **Linter/formatter** — ESLint + Prettier unless locked otherwise. Add config and `lint`/`format` scripts.
- **CI** — a minimal GitHub Actions workflow that installs deps, lints, and runs `npm test` on push/PR.
- **.gitignore** — `node_modules`, build output, coverage, env files.
- **Env** — `.env.example` if the architecture needs configuration; never commit real secrets.

### 5. Seed agent guidance

If no `CLAUDE.md`/`AGENTS.md` exists, generate one from `docs/technical/engineering-guidelines.md` — conventions, the `node:test` commands, structure rules, and the non-negotiable agent rules. This is what keeps later build sessions consistent.

### 6. UI foundation (when applicable)

If `docs/technical/ui-requirements.md` exists or the architecture includes pages/screens/components:

1. Confirm whether `docs/design/design-system.md` exists
   - If missing, pause feature scaffolding and run **design-system** first
   - If present, read it and summarise the locked design tokens and component rules
2. Confirm whether component-library infrastructure exists
   - Storybook config or the project's chosen component showcase
   - Component folders such as `components/ui`, `components/composed`, `components/patterns`, `components/ai`
3. If missing, run **component-library** before feature work starts
4. Seed agent guidance with UI rules:
   - No one-off inline UI
   - Use design-system tokens only
   - Audit existing components before creating new ones
   - Verify backwards compatibility when extending components

### 7. Verify the skeleton

Prove it works before handing off:

- [ ] `npm install` succeeds
- [ ] `npm test` runs and the smoke test passes
- [ ] `npm run lint` passes
- [ ] CI workflow is valid
- [ ] UI projects have design-system and component-library prerequisites ready or explicitly queued

Report the results plainly. If something fails, fix it here — the build loop assumes a green baseline.

### 8. Commit and hand off

Commit the skeleton (conventional commit, e.g. `chore: scaffold project`). Then:

> "Skeleton is up — `npm test` is green on the smoke test. Ready to build features with the executing-plans skill (it runs the TDD loop per task). Start now?"

## Principles

- **Locked decisions are law** — scaffold exactly the stack the docs specify
- **Green baseline** — hand off only when test, lint, and CI pass
- **node:test by default** — built-in runner and assertions, no extra framework unless locked
- **No features** — skeleton only; the TDD loop writes the logic
- **State your defaults** — every 🤖 choice you fill in is called out for override
- **UI foundation first** — never hand off UI feature work without design-system and component-library readiness

## Anti-patterns

- Substituting a different framework/runner than `tech-stack.md` locks
- Adding a third-party test framework when `node:test` covers it
- Handing off with a red or unrunnable test suite
- Implementing features "to save time"
- Committing secrets or real `.env` files
- Starting UI feature implementation before design tokens, component standards, and component showcase infrastructure exist
