# conformance-check

Audit whether the codebase matches `docs/technical/` — and vice versa.

Finds missing tests for requirements, orphan tests, violations of 🔒 human-locked tech choices, and gaps between API contracts / data model and implementation.

## What it does

- Builds a traceability matrix: REQ-ID → tests → files
- Flags critical drift (untested requirements, locked stack violations)
- Reports warnings (orphan tests, stale code for removed REQs)
- Recommends the next skill: refining-docs, reconciling-changes, or planning

## When to use it

- Before **reconciling-changes** — know existing drift before planning a delta
- After a build — confirm code matches the updated spec
- "Does the code match the docs?" or "Are we missing tests?"

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill conformance-check --global
```

Or install the full harness:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```

## Example prompt

> "Run a conformance check — do our tests cover every requirement in requirements.md?"
