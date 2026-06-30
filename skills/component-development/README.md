# Component Development

**Build UI components in isolation** following component-driven development (CDD) best practices with Storybook stories, variants, interaction tests, and full documentation.

## When to use

- Creating new UI components
- Adding variants to existing components
- Building reusable component patterns
- Composing shadcn/ui primitives into application components
- Creating AI-powered components with Vercel AI SDK
- User asks to "create a component", "build a UI element", or "add a variant"

## What it does

This skill guides you through building production-ready components using TDD principles: stories first, then implementation, then tests.

**Process:**
1. Design component API (props, variants, states)
2. Create story file with all variants
3. Implement component
4. Add interaction tests
5. Verify accessibility
6. Document with JSDoc

**Outputs:**
- Component implementation (`component.tsx`)
- Story file with variants (`component.stories.tsx`)
- Unit tests (`component.test.tsx`)
- Auto-generated documentation in Storybook

## Prerequisites

- **component-library** skill completed (Storybook installed)
- TypeScript project
- React/Next.js

## Quick start

```
> "Create a UserCard component with avatar, name, role, and an expand/collapse interaction."
```

The agent will:
1. Design the component API
2. Create story file with all variants (collapsed, expanded, loading, error)
3. Implement the component with shadcn/ui primitives
4. Add interaction tests for expand/collapse
5. Verify accessibility
6. Show you how to import and use it

## Patterns covered

- **Primitive components** — Base UI elements (buttons, inputs, cards)
- **Composed components** — Application-specific combinations
- **Compound components** — Multi-part components with shared context
- **Form components** — Controlled/uncontrolled state management
- **Data components** — Loading, empty, error states
- **AI components** — Streaming, tool invocations, multi-step flows

## Best practices enforced

- Stories first (TDD for UI)
- TypeScript with full type safety
- Accessibility by default (WCAG compliance)
- Semantic design tokens (not hardcoded colours)
- Interaction testing with play functions
- User-centric testing patterns

## Part of

Component development workflow:
1. **component-library** — Set up infrastructure
2. **component-development** (this) — Build individual components
3. **component-testing** — Add comprehensive tests

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-development --global
```
