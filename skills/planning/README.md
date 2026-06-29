# planning

Turn an approved design into a bite-sized, test-driven implementation plan. Supports **greenfield** (new feature) and **delta** (doc change on existing app) modes.

Each task has exact file paths, REQ-ID traceability, complete code snippets, and verification steps.

## What it does

The agent will:

- Read your approved spec, impact report, or requirements
- Choose greenfield or delta mode based on context
- Map files to create and modify (delta plans respect frozen scope)
- Break work into small, reviewable tasks with checkbox steps
- Include failing tests first, then minimal implementation (TDD)
- Self-review for gaps, placeholders, and inconsistencies
- Save the plan to `docs/plans/`
- Offer to hand off to executing-plans

## When to use it

- After **technical-documentation**, when docs are approved (greenfield)
- After **reconciling-changes**, when the impact report is approved (delta)
- When you have clear requirements and need a task breakdown

## Workflow

```
Greenfield:  brainstorming → technical-documentation → planning → executing-plans

Brownfield:  refining-docs → reconciling-changes → planning (delta) → executing-plans
```

## Install

Uses [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit) via `npx` — no global install required.

### Quick install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning
```

### Global — Cursor

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning --global --agent cursor
```

### Global — Claude

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning --global --agent claude
```

### Project install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning --project
```

## Example prompt

> "The design spec is approved. Write an implementation plan for the notification system."

## Uninstall

```sh
npx @levi-putna/agent-kit@latest remove planning
```
