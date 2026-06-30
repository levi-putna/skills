# Component Library

**Set up and manage a component library showcase** that displays all UI elements in isolation with live previews, interactive testing, and documentation.

## When to use

- Starting a new project that needs a component gallery
- Setting up component-driven development infrastructure
- Creating a visual component browser for the team
- Building a design system foundation
- User asks for "component showcase", "component gallery", or "component playground"

## What it does

This skill scaffolds a complete component library infrastructure using industry-standard tooling (Storybook 8) or a custom in-app gallery, depending on project needs.

**Outputs:**
- Storybook configuration with Next.js integration
- Category structure for organising components
- Documentation on adding and testing components
- Integration with shadcn/ui and Vercel AI SDK (if present)

## Prerequisites

- Existing Next.js/React project
- `package.json` present
- Node.js and yarn/npm installed

## Quick start

```
> "Set up a component library showcase for this project."
```

The agent will:
1. Ask whether you want Storybook (recommended) or custom in-app gallery
2. Install and configure the chosen solution
3. Create category structure
4. Write documentation
5. Offer to create example components

## After installation

- **Storybook:** Run `yarn storybook` → http://localhost:6006
- **Custom gallery:** Run `yarn dev` → http://localhost:3000/components

Next: Use **component-development** skill to create components with stories.

## Part of

Component development workflow:
1. **component-library** (this) — Set up infrastructure
2. **component-development** — Build individual components
3. **component-testing** — Add comprehensive tests

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-library --global
```
