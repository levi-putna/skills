# Component Testing

**Add comprehensive testing to UI components** with interaction tests, visual regression, accessibility audits, and unit tests to ensure production-ready quality.

## When to use

- After creating a component (use after **component-development**)
- Adding tests to existing components
- Setting up visual regression testing
- Verifying accessibility compliance
- Preparing components for production release
- User asks to "test this component", "add tests", or "set up visual regression"

## What it does

This skill adds multiple layers of testing to ensure components are robust, accessible, and regression-proof.

**Testing layers:**
1. **Interaction tests** — Storybook play functions simulate user behaviour
2. **Visual regression** — Chromatic/Playwright catch visual regressions
3. **Accessibility** — a11y addon verifies WCAG compliance
4. **Unit tests** — React Testing Library for logic and edge cases
5. **API mocking** — MSW for components that fetch data

**Outputs:**
- Interaction tests in story files
- Visual regression configuration
- Unit test files with RTL
- CI workflow for automated testing
- Accessibility audit reports

## Prerequisites

- **component-development** skill completed (component + stories exist)
- Storybook installed and running
- Test runner installed (`@storybook/test-runner`)

## Quick start

```
> "Add comprehensive tests to the UserCard component including interaction tests, visual regression, and accessibility checks."
```

The agent will:
1. Add interaction tests with play functions
2. Configure visual regression (Chromatic or Playwright)
3. Verify accessibility compliance
4. Create unit tests with React Testing Library
5. Set up CI workflow

## Testing strategies

**By component type:**
- **Primitives** (Button, Input) — Visual variants, states, keyboard nav
- **Forms** — Validation, error handling, submission
- **Data components** — Loading/empty/error states
- **Interactive** (Modal, Dropdown) — Open/close, focus management
- **AI components** — Streaming, tool invocations, multi-step flows

**Best practices:**
- Test user behaviour, not implementation
- Use realistic interactions with `userEvent`
- Query by role for accessibility
- Mock at network boundary with MSW
- Automate in CI

## CI integration

Automatically runs on every PR:
- Interaction tests (headless Storybook)
- Visual regression (Chromatic snapshots)
- Unit tests (Vitest/Jest)
- Accessibility audits (a11y addon)

## Part of

Component development workflow:
1. **component-library** — Set up infrastructure
2. **component-development** — Build individual components
3. **component-testing** (this) — Add comprehensive tests

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-testing --global
```
