# test-driven-development

The core build loop — implement every requirement test-first with Node's built-in test runner.

This is the engine room of the pipeline. It runs a tight **red-green-refactor** loop and doesn't move on until the tests pass and the code actually works.

```
┌─ RED       write ONE failing node:test
│  RUN       node --test → must fail for the right reason
│  GREEN      minimal code to pass
│  RUN       node --test → passes
└─ REFACTOR  clean up, tests stay green   (repeat until covered)
```

## What it does

- **Test first, always** — no production code without a failing test demanding it, and it watches the test fail before writing code.
- **Unit + functional, both on `node:test`** — fast isolated unit tests next to source, functional tests that prove the requirement across modules. `node:assert/strict` for assertions, `mock` for stubs.
- **Never fakes green** — won't skip, delete, or loosen a test to pass. If a test is wrong, it fixes the test; if the code is wrong, it fixes the code.
- **Knows when to stop** — after a focused debugging attempt it reports a real blocker (failing test, expected vs actual, what's ruled out) instead of thrashing.
- **Covers what matters** — every acceptance criterion tested, edge cases and error handling asserted, suite fully green before done.

## When to use it

- Building a feature or implementing a plan task
- Any time you want code written test-first
- Called per-task by **executing-plans**, or standalone for a single feature

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill test-driven-development --global
```

Or as part of the full design-to-ship workflow:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```
