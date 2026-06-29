# reconciling-changes

Analyse what changed in `docs/technical/` since the last baseline and produce an impact report before any code changes.

This is the control layer for **doc-first, AI-built** development on an existing app. You update the spec; this skill tells you exactly what code and tests must change — and what must stay untouched.

```
refining-docs  →  reconciling-changes  →  planning (delta)  →  executing-plans
```

## What it does

- Establishes a baseline (last plan, git commit, or doc changelog)
- Classifies each doc change as **ADD**, **MODIFY**, **REMOVE**, or **CLARIFY**
- Maps changes to files, existing tests, and new tests needed
- Lists **frozen scope** — areas the delta plan must not touch
- Saves a reviewable impact report to `docs/plans/`
- Hands off to **planning** in delta mode

## When to use it

- You updated `requirements.md` or other technical docs and want to implement the change
- "What needs to change in the code?" after a spec update
- Before planning work on a brownfield app — prevents full rewrites

Skip for greenfield projects; use **planning** (greenfield mode) instead.

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill reconciling-changes --global
```

Or install the full harness:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```

## Example prompt

> "I updated requirements.md for CSV export. Analyse the impact and tell me what code needs to change."
