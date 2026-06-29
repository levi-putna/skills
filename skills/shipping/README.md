# shipping

The release step — take built, verified work and get it ready to merge.

Runs after **end-to-end-testing** passes. Handles branch hygiene, conventional commits, changelog, and a clear pull request. It's careful by default: it proves the build is green and gets explicit go-ahead before anything outward-facing (push, PR, merge).

## What it does

- **Pre-flight checks** — full `node:test` suite, e2e, and lint must be green. Red routes back to the build or e2e loop; it never ships unverified work.
- **Branch hygiene** — refuses to commit on `main`, names branches by intent, and tidies noisy history (only what's local and unpushed).
- **Conventional commits** — keeps history machine-readable for changelogs and tooling.
- **Changelog** — adds a user-facing entry when the project keeps one.
- **Pull request** — pushes and opens a PR with `gh` (after confirmation), with a body covering what/why, changes, testing results, and acceptance criteria mapped to `requirements.md`.
- **Merge** — only when asked, respecting the repo's merge style and branch protection.

## When to use it

- After e2e verification passes
- To open a PR or prepare a release
- To merge completed work

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill shipping --global
```

Or as part of the full design-to-ship workflow:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```
