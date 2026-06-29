# project-setup

Scaffold a new project from its technical docs so the TDD build loop has solid ground to run on.

Runs once after **planning**, before **executing-plans**. It reads `docs/technical/` and produces a runnable, empty skeleton: directory structure, `package.json`, the `node:test` runner wired up, linter, CI, and a seed `CLAUDE.md`/`AGENTS.md` — all matching the locked stack decisions.

```
planning  →  project-setup  →  executing-plans (TDD build loop)
docs/plans/   scaffolds repo    writes features
```

## What it does

- **Honours locked decisions** — every 🔒 human-locked choice in `tech-stack.md` is used exactly; 🤖 agent-discretion gaps are filled with stated defaults you can override.
- **Wires `node:test`** — `test`, `test:watch`, `test:coverage`, `test:unit`, `test:functional` scripts using Node's built-in runner and `node:assert/strict`. No third-party framework unless the docs lock one.
- **Sets up tooling and CI** — linter/formatter, a minimal GitHub Actions workflow, `.gitignore`, env scaffolding.
- **Seeds agent guidance** — generates `CLAUDE.md`/`AGENTS.md` from `engineering-guidelines.md` so later build sessions stay consistent.
- **Verifies a green baseline** — install, test, lint, and CI must pass before hand-off.

## When to use it

- Starting a greenfield codebase
- You have technical docs but no working project skeleton yet
- Right before the first build session

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill project-setup --global
```

Or as part of the full design-to-ship workflow:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```
