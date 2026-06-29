# refining-docs

Refine, tune, and keep technical documentation consistent — the companion to the **technical-documentation** skill.

Authoring gets the docs on disk. This skill is for everything after: tuning decisions, filling gaps, keeping the set consistent as the project evolves, and tightening the writing. It makes focused, reviewable changes rather than rewriting from scratch.

## Modes

It picks the right mode from what you ask (several can combine):

| Mode | Use when you want to… |
|---|---|
| **Gap analysis** | Find what's missing — undefined decisions, untestable requirements, open ❓ tags |
| **Consistency** | Check the docs agree with each other after a change |
| **Diagrams** | Add or update Mermaid diagrams (and verify they render) |
| **Decision tuning** | Change a choice's owner — 🔒 human-locked ↔ 🤖 agent-discretion |
| **Tighten** | Clean up clarity and structure without changing meaning |
| **Changelog** | Record REQ-level doc changes in `docs/technical/CHANGELOG.md` |

Ask vaguely ("improve the docs") and it defaults to gap analysis + consistency, then reports findings before touching anything.

After behavioural doc changes, it can hand off to **conformance-check** and **reconciling-changes**.

## How it works

1. **Loads the target doc and its cross-references** — refinement is usually about the set, not one file.
2. **Diagnoses first, edits second** — reports findings so you can steer before files change.
3. **Applies scoped changes** and propagates a decision change everywhere it appears.
4. **Re-checks consistency** across the whole set.
5. **Summarises** what changed and flags anything still open.

It never silently flips a 🔒 human-locked decision — that only happens when you ask.

## When to use it

- After **technical-documentation**, to tune the generated docs
- When a project decision changes and the docs need to catch up
- To audit an existing doc set for gaps or contradictions
- To add or update diagrams in any `.md` doc

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill refining-docs --global
```

Or as part of the full design-to-ship workflow:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```
