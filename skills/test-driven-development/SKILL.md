---
name: test-driven-development
description: >-
  The core build loop. Implement a requirement test-first with node:test —
  write a failing test (RED), minimal code to pass (GREEN), then refactor —
  looping until tests pass and the code works. Use when building a feature,
  implementing a plan task, or any time the user wants code written with tests.
  Covers unit and functional tests with node:test and node:assert.
---

# Test-Driven Development

This is the engine room. Build every requirement test-first, in a tight red-green-refactor loop, and do not move on until the tests pass and the code actually works. Tests are written with Node's built-in runner (`node:test`) and assertions (`node:assert/strict`).

Announce at start: "I'm using the test-driven-development skill to build this test-first."

## The non-negotiable loop

For every unit of behaviour:

```
┌─ 1. RED      Write ONE failing test that pins the requirement
│  2. RUN      node --test <path>  →  it MUST fail, for the right reason
│  3. GREEN    Write the MINIMAL code to make it pass — nothing more
│  4. RUN      node --test <path>  →  it passes
│  5. REFACTOR Clean up names/duplication; tests stay green
└─ repeat until the requirement is fully covered and green
```

Rules that make the loop real, not theatre:

- **Test first, always.** No production code without a failing test demanding it.
- **Watch it fail first.** A test that passes before you write code tests nothing. Confirm the failure and that the message makes sense.
- **Minimal green.** Write only enough to pass the current test. Resist building ahead.
- **Never weaken a test to pass it.** Don't delete, skip (`test.skip`), comment out, or loosen an assertion to get green. Fix the code.
- **Loop until it works.** "Tests pass" means the real behaviour is correct — not that you forced a green. If a test passes but the feature is wrong, the test was wrong; fix it.

## Test types — both use node:test

| Type | Scope | Lives in | Speed |
|---|---|---|---|
| **Unit** | One function/module in isolation; dependencies stubbed | `src/**/*.test.ts` next to source | Fast, run constantly |
| **Functional** | A feature across modules; real wiring, real data flow, mocked external services | `test/**/*.test.ts` | Slower, run per task |

Write unit tests for logic and edge cases; functional tests to prove the requirement holds end-to-end within the app (full browser/network e2e is the separate end-to-end-testing skill).

## node:test patterns

Use the built-in runner and `node:assert/strict`. No third-party framework unless `docs/technical/tech-stack.md` locks one.

```ts
import { test, describe, beforeEach, mock } from 'node:test';
import assert from 'node:assert/strict';
import { createCart } from '../src/cart.ts';

describe('cart total', () => {
  test('REQ-012-AC1: sums item prices', () => {
    const cart = createCart([{ price: 10 }, { price: 5 }]);
    assert.equal(cart.total(), 15);
  });

  test('REQ-012-AC2: throws on negative price', () => {
    assert.throws(() => createCart([{ price: -1 }]), /price must be >= 0/);
  });
});
```

Include the **REQ-ID** in the test name when the plan or `requirements.md` specifies one — this enables traceability for **conformance-check** and impact analysis.

Useful tools:
- `assert.equal` / `assert.deepEqual` / `assert.throws` / `assert.rejects` (async)
- `describe` / `test` (alias `it`), `beforeEach` / `afterEach` for setup
- `mock.fn()`, `mock.method()` for stubbing — reset with `mock.reset()` in teardown
- `t.test(...)` subtests for grouping related cases
- Async: tests can be `async`; assert with `await assert.rejects(...)`

Commands (from `package.json`, set up by project-setup):
- One file: `node --test path/to/file.test.ts`
- All: `npm test`  ·  Watch: `npm run test:watch`  ·  Coverage: `npm run test:coverage`
- TypeScript: `node --test --experimental-strip-types ...` (Node 22.6+) or `node --import tsx --test ...`

## Working from a plan

When executing a plan task (from executing-plans), the plan already specifies the test and implementation per step. Follow it, but the loop above still governs: confirm each test fails before implementing, and never skip a verification step. If the plan's test is wrong or insufficient, fix it and note the deviation.

## Coverage and completeness

Before calling a requirement done:

- Every acceptance criterion in `docs/technical/requirements.md` for this slice has a test named with its REQ-ID where applicable
- Edge cases and failure modes are tested, not just the happy path
- Error handling is asserted (the right error, not just "throws")
- `npm test` is fully green — no skipped or `.only` tests left behind
- Run `npm run test:coverage` for risky modules; cover the branches that matter, not a vanity percentage

## When stuck (the loop won't go green)

Don't thrash. After two failed attempts on the same test, switch to deliberate debugging:

1. **Isolate** — run just the failing test; reduce it to the smallest reproduction
2. **Observe** — add assertions or logging to see actual vs expected; check assumptions about inputs
3. **Hypothesise one cause**, change one thing, re-run
4. **Still stuck after a focused attempt?** Stop and report to the user with: the failing test, expected vs actual, and what you've ruled out. Do not hack the test to pass.

## Commit rhythm

One logical commit per green requirement (or per plan task). Conventional commits: `feat:`, `fix:`, `refactor:`, `test:`. Commit only with the suite green.

## Hand off

When the slice is built and green, return control to whatever invoked this loop:

- Inside executing-plans → report the task complete; the orchestrator continues
- Standalone → summarise what was built and tested, and suggest the end-to-end-testing skill to verify the running app

## Principles

- **Red before green** — no code without a failing test
- **Minimal green** — only enough to pass; refactor after
- **Tests are the spec** — make the behaviour right, never fake the green
- **REQ-ID traceability** — test names map to acceptance criteria for conformance audits
- **node:test, node:assert** — built-in by default
- **Small steps, frequent commits** — one behaviour at a time

## Anti-patterns

- Writing implementation before the test
- Skipping the "watch it fail" step
- `test.skip`, `.only`, deleting, or loosening a test to get green
- Building features no current test requires (YAGNI)
- Marking a task done with a red or skipped suite
- Endless thrashing instead of stopping to report a real blocker
