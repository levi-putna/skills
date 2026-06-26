# brainstorming

Think through an idea with a sharp, trusted colleague before you build. The agent helps you clarify the goal, surface assumptions, compare approaches, and write an approved design spec.

Inspired by structured design conversations, but tuned for a collegial back-and-forth rather than a rigid questionnaire.

## What it does

The agent will:

- Scan project context and reflect back what it sees
- Ask one focused question at a time
- Flag oversized scope and suggest where to start
- Surface hidden assumptions and run a quick pre-mortem
- Propose 2–3 approaches with honest trade-offs
- Present the design in sections and revise until you agree
- Save an approved spec to `docs/designs/`
- Hand off to the planning skill when ready

It will **not** write code or start implementation until you approve the design.

## When to use it

- Starting a new feature, product, or project
- Requirements feel fuzzy or keep shifting
- You want to talk through options before committing
- Before planning or coding anything non-trivial

## Workflow

```
brainstorming → planning → executing-plans
```

Install all three for the full design-to-ship flow.

## Install

Uses [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit) via `npx` — no global install required.

### Quick install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming
```

### Global — Cursor

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming --global --agent cursor
```

### Global — Claude

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming --global --agent claude
```

### Project install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming --project
```

## Example prompt

> "I want to add a notification system to my app. Help me think through it before we build anything."

## Uninstall

```sh
npx @levi-putna/agent-kit@latest remove brainstorming
```
