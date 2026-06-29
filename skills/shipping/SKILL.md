---
name: shipping
description: >-
  Ship verified work — branch hygiene, conventional commits, changelog, and a
  pull request. Use after end-to-end verification passes, when the user wants to
  open a PR, prepare a release, or merge completed work. Runs pre-flight checks
  and never ships a red or unverified build.
---

# Shipping

The release step. Take work that is built and verified green, and get it ready to merge — clean history, a clear PR, an updated changelog. This skill is careful by default: it confirms the build is genuinely green and gets explicit go-ahead before anything outward-facing (pushing, opening a PR, merging).

Announce at start: "I'm using the shipping skill to prepare this for release."

## Prerequisites

- Unit/functional suite green (test-driven-development)
- End-to-end verification green (end-to-end-testing)
- A clean understanding of what changed in this branch

If e2e hasn't run, say so and recommend running it first — don't ship unverified work.

## The flow

### 1. Pre-flight — prove it's shippable

Run the checks and report results plainly. Do not proceed past a red check.

- [ ] `npm test` — full suite green
- [ ] `npm run test:e2e` — end-to-end green (or confirmed run via end-to-end-testing)
- [ ] `npm run lint` — clean
- [ ] Working tree status reviewed — you know every changed file and why

If anything is red, stop and route it back: failing tests → build loop, failing e2e → end-to-end-testing.

### 2. Branch hygiene

- Confirm you are **not** on `main`/`master`. If you are, create a feature branch before committing anything.
- Branch name reflects the work: `feat/<short-name>`, `fix/<short-name>`.
- Review the commit history. If it's noisy (WIP commits, fix-the-fix), offer to tidy it into logical commits — but only squash/rewrite history the user hasn't pushed or shared.

### 3. Conventional commits

Ensure commits follow Conventional Commits so history and changelog stay machine-readable:

- `feat:` new capability · `fix:` bug fix · `refactor:`, `test:`, `docs:`, `chore:`
- Scope where useful: `feat(cart): ...`
- Body explains the *why* when not obvious
- End commit messages with the project's required trailer if it has one

### 4. Changelog

If the project keeps a `CHANGELOG.md` (or the user wants one), add an entry summarising user-facing changes for this work, grouped by type (Added / Fixed / Changed). Derive it from the conventional commits. Skip silently if the project clearly doesn't use one.

### 5. Pull request

Confirm before anything leaves the machine.

> "Pre-flight is green. Ready to push `feat/checkout` and open a PR against `main`?"

**✋ Get explicit go-ahead — pushing and opening a PR are outward-facing.**

On approval, push the branch and open the PR with `gh`. The PR body should cover:

- **What & why** — one paragraph
- **Changes** — bulleted summary of the meaningful changes
- **Testing** — unit/functional + e2e results, how to verify locally
- **Acceptance criteria** — checklist mapped to `requirements.md`
- Link the design/plan docs if useful

Match any existing PR template in the repo. End the PR body with the project's required attribution line if it has one.

### 6. Merge

Only when the user asks. Respect the project's merge style (squash/rebase/merge commit) and branch protection. After merge, offer to delete the branch and pull `main`.

## When to stop and ask

- Any pre-flight check is red
- You'd have to rewrite shared/pushed history to tidy commits
- Branch protection or CI is failing on the PR
- The user hasn't approved the push/PR/merge

## Principles

- **Never ship red or unverified** — green build and green e2e are the entry ticket
- **Confirm outward actions** — push, PR, and merge each need a go-ahead
- **Readable history** — conventional commits, logical units, no WIP noise
- **Respect the repo** — match its templates, merge style, and conventions
- **Don't rewrite shared history** — tidy only what's local and unpushed

## Anti-patterns

- Pushing or opening a PR without confirmation
- Shipping with failing or skipped tests/e2e
- Committing on `main` directly
- Force-pushing over shared branches to "clean up"
- A PR body that doesn't say how the change was tested
