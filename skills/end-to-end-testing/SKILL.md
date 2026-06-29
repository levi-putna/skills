---
name: end-to-end-testing
description: >-
  Verify a built application end-to-end against its requirements, and retrigger
  the build loop if it fails. Use as the verification step after a build — runs
  e2e tests against the running app (node:test for API/CLI, Playwright for web
  UIs), maps results to acceptance criteria, and on failure files fix tasks and
  hands control back to the TDD build loop. Use when checking that a feature
  actually works in a running system, not just in unit tests.
---

# End-to-End Testing

The outer verification gate. Unit and functional tests prove the pieces work; this proves the *whole running application* does what the requirements promise. If it doesn't, this skill doesn't just report — it turns failures into fix tasks and sends control back into the build loop, then re-verifies. It closes only when the app is green end-to-end.

Announce at start: "I'm using the end-to-end-testing skill to verify the running app."

## Where this sits — the outer loop

```
test-driven-development (build) ──► end-to-end-testing (verify)
        ▲                                   │
        │  ✗ fail: file fix tasks,          │
        └──────  re-enter build loop ◄───────┤
                                            │ ✓ pass
                                            ▼
                                        shipping
```

## Prerequisites

- A built application whose unit/functional suite is already green (from test-driven-development)
- `docs/technical/requirements.md` for acceptance criteria to verify against
- Ability to run the app locally

## Choosing the e2e approach

Detect the application type and use the lightest tool that proves the real flow:

| App type | Runner | What e2e drives |
|---|---|---|
| HTTP API / service | `node:test` | Real HTTP requests against the running server; assert status, body, headers, side effects |
| CLI / script | `node:test` | Spawn the built binary (`child_process`), assert stdout/stderr/exit code/files written |
| Web UI | **Playwright driven by `node:test`** | A real browser: navigate, click, type, assert visible result |
| Background/worker | `node:test` | Enqueue real input, assert the processed outcome |

Stay Node-native by default. Add Playwright **only** when there's a browser UI to exercise — and still drive it from `node:test` so there's one runner and one command.

## The flow

### 1. Map requirements to scenarios

Read `requirements.md`. For each feature's acceptance criteria, define an end-to-end scenario that exercises it through the real entry point (HTTP endpoint, CLI command, or UI flow). Cover the critical happy paths and the failure paths that matter (auth rejected, validation errors, empty states).

List the scenarios for the user before writing them if there are many — a quick sanity check that you're verifying the right things.

### 2. Stand up the app

Start the application the way a user/client would: launch the server, build the CLI, or boot the web app. Use a real (test) database or a disposable instance — not mocks. Tear down cleanly after.

```ts
import { test, before, after } from 'node:test';
import assert from 'node:assert/strict';

let server;
before(async () => { server = await startApp({ port: 0 }); });
after(async () => { await server.close(); });

test('POST /orders creates an order and returns 201', async () => {
  const res = await fetch(`${server.url}/orders`, {
    method: 'POST',
    headers: { 'content-type': 'application/json' },
    body: JSON.stringify({ items: [{ sku: 'A1', qty: 2 }] }),
  });
  assert.equal(res.status, 201);
  const body = await res.json();
  assert.ok(body.id, 'order id returned');
});
```

Web UI via Playwright, same runner:

```ts
import { test } from 'node:test';
import assert from 'node:assert/strict';
import { chromium } from 'playwright';

test('user can complete checkout', async () => {
  const browser = await chromium.launch();
  const page = await browser.newPage();
  await page.goto(`${appUrl}/cart`);
  await page.getByRole('button', { name: 'Checkout' }).click();
  await page.getByLabel('Card number').fill('4242424242424242');
  await page.getByRole('button', { name: 'Pay' }).click();
  await assert.doesNotReject(page.getByText('Order confirmed').waitFor());
  await browser.close();
});
```

Put e2e tests in `test/e2e/**/*.test.ts` with their own script, e.g. `"test:e2e": "node --test test/e2e/"`.

### 3. Run and judge against acceptance criteria

Run the e2e suite. For each scenario, the verdict is binary against the requirement — does the running app satisfy the acceptance criterion or not? A passing test that doesn't actually prove the criterion is a gap; tighten it.

### 4a. On failure — retrigger the build loop

This is the point of the skill. For each failure:

1. **Diagnose** — capture the scenario, expected vs actual, and the likely layer at fault (handler, data, wiring, UI)
2. **File fix tasks** — write concrete, testable tasks: which behaviour is wrong, what the correct behaviour is, and the failing e2e scenario as the acceptance check
3. **Hand back to the build loop** — re-enter test-driven-development (or executing-plans if a batch) to fix each task test-first. The e2e scenario stays as the outer proof
4. **Re-verify** — once the build loop reports green, run the e2e suite again

> "E2e found 2 failures:
> 1. `POST /orders` returns 500 on empty cart (should be 400 with a validation message).
> 2. Checkout page doesn't show confirmation after payment.
>
> Filing these as fix tasks and re-entering the build loop. I'll re-run e2e when they're green."

Loop steps 4a until the suite passes. Stop and ask the user only if the same scenario fails repeatedly after fixes (it may signal a requirements gap, not a bug).

### 4b. On success — hand off

When every scenario passes:

> "End-to-end verification green — all N acceptance scenarios pass against the running app. Ready to ship with the shipping skill?"

Suggest the shipping skill. Note any scenarios deliberately not covered.

## Scope discipline

- E2e proves requirements, not implementation detail — assert observable behaviour, not internal calls
- Don't duplicate unit-test coverage here; e2e is for whole-system flows
- Keep the suite to the criteria that matter; a slow, flaky e2e suite that no one trusts is worse than a focused one
- Flaky tests get fixed or quarantined with a reason — never ignored

## Principles

- **Verify the running app**, the way a real client uses it — real server, real browser, real data
- **Tie every scenario to an acceptance criterion** in `requirements.md`
- **Failure means fix, not just report** — file tasks and re-enter the build loop
- **One runner** — `node:test` drives everything, Playwright included
- **Close only when green** — don't hand to shipping with red e2e

## Anti-patterns

- Mocking the system under test (that's a unit test, not e2e)
- Reporting failures without filing fix tasks or retriggering the build
- Asserting internal implementation instead of user-visible behaviour
- Adding Playwright for an API/CLI that has no browser UI
- Leaving flaky tests in the suite unmarked
- Handing off to shipping with failing or skipped e2e scenarios
