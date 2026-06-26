# planning

Turn an approved design into a bite-sized, test-driven implementation plan. Each task has exact file paths, complete code snippets, and verification steps.

## What it does

The agent will:

- Read your approved spec or requirements
- Map files to create and modify
- Break work into small, reviewable tasks with checkbox steps
- Include failing tests first, then minimal implementation (TDD)
- Self-review for gaps, placeholders, and inconsistencies
- Save the plan to `docs/plans/`
- Offer to hand off to executing-plans

## When to use it

- After brainstorming, when the design is approved
- When you have clear requirements and need a task breakdown
- Before an agent or engineer starts implementation

## Workflow

```
brainstorming → planning → executing-plans
```

## Install

Requires [agent-kit](https://github.com/levi-putna/agent-kit):

```sh
npm install -g @levi-putna/agent-kit
```

### Quick install

```sh
agent-kit add levi-putna/skills --skill planning
```

### Global — Cursor

```sh
agent-kit add levi-putna/skills --skill planning --global --agent cursor
```

### Global — Claude

```sh
agent-kit add levi-putna/skills --skill planning --global --agent claude
```

### Project install

```sh
agent-kit add levi-putna/skills --skill planning --project
```

## Example prompt

> "The design spec is approved. Write an implementation plan for the notification system."

## Uninstall

```sh
agent-kit remove planning
```
