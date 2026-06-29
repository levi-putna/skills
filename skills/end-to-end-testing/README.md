# end-to-end-testing

Verify a built application end-to-end against its requirements — and retrigger the build loop if it fails.

This is the outer verification gate. The TDD loop proves the pieces work; this proves the **whole running application** does what the requirements promise. On failure it doesn't just report — it files fix tasks and sends control back into the build loop, then re-verifies. It closes only when the app is green end-to-end.

```
test-driven-development (build) ──► end-to-end-testing (verify)
        ▲                                   │
        │  ✗ fail: file fix tasks,          │
        └──────  re-enter build loop ◄───────┤
                                            │ ✓ pass
                                            ▼
                                        shipping
```

## What it does

- **Tests the real app, not mocks** — real server, real CLI process, or a real browser, with a real test database.
- **One runner** — `node:test` drives everything: HTTP requests for APIs, `child_process` for CLIs, and Playwright (still launched from `node:test`) for web UIs. Playwright is added only when there's a browser to test.
- **Maps every scenario to an acceptance criterion** in `docs/technical/requirements.md` — the verdict is whether the running app satisfies the requirement.
- **Failure means fix** — diagnoses each failure, writes concrete fix tasks, re-enters the TDD build loop, then re-runs e2e until green.
- **Guards quality** — asserts observable behaviour not internals, keeps the suite focused, and never leaves flaky tests unmarked.

## When to use it

- As the verification step right after a build
- To confirm a feature actually works in a running system
- Before shipping

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill end-to-end-testing --global
```

Or as part of the full design-to-ship workflow:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```
