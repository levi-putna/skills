# executing-plans

Execute a written implementation plan task by task, with verification at every step and review checkpoints between batches.

## What it does

The agent will:

- Load and critically review the plan before coding
- Work through tasks in order, following each checkbox step
- Run tests and commands as specified
- Pause every 3 tasks (or sooner if blocked) for your review
- Stop and ask when something fails or the plan has gaps
- Summarise results when all tasks are complete

## When to use it

- A plan exists in `docs/plans/` and you are ready to build
- You want structured progress with checkpoints, not a single long coding burst
- After the planning skill has produced a task list

## Workflow

```
brainstorming → planning → executing-plans
```

## Install

Uses [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit) via `npx` — no global install required.

### Quick install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans
```

### Global — Cursor

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans --global --agent cursor
```

### Global — Claude

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans --global --agent claude
```

### Project install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans --project
```

## Example prompt

> "Implement the plan at docs/plans/2026-06-26-notifications.md. Checkpoint after each batch."

## Uninstall

```sh
npx @levi-putna/agent-kit@latest remove executing-plans
```
