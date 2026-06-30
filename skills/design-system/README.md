# Design System

**Define and document the design system foundation** including brand identity, design tokens, typography, spacing, and component standards.

## When to use

- **Before component-library** — Establish design foundation first
- Starting a new project that needs UI consistency
- Defining brand identity and visual language
- Setting up design tokens for theming
- Creating component creation guidelines
- User asks to "define the design system", "set up design tokens", or "create brand guidelines"

## What it does

This skill creates comprehensive design system documentation that serves as the single source of truth for all UI decisions.

**Creates:**
- `docs/design/design-system.md` — Complete design system documentation
- `docs/design/component-decision-guide.md` — When to create vs. extend components
- Design tokens (colors, typography, spacing, borders, shadows)
- Brand identity documentation
- Component standards and audit process
- Tailwind/CSS configuration examples

**Prevents:**
- Component proliferation (dozens of similar components)
- Inconsistent styling (hardcoded colors, random spacing)
- Unclear extension guidelines
- Brand drift over time

## Key Features

### Component Decision Framework
- Clear rules for when to extend vs. create new
- Component audit process (search before creating)
- Examples for common scenarios
- Backward compatibility requirements

### Design Tokens
- Semantic color palette (`primary`, `destructive`, not raw colors)
- Typography scale (font sizes, weights, line heights)
- Spacing system (4px grid)
- Border radius, shadows, transitions

### Brand Identity
- Brand values and principles
- Voice and tone guidelines
- Accessibility standards (WCAG AA minimum)
- Responsive design patterns

## Prevents Component Proliferation

**Without design system:**
- Button, PrimaryButton, BlueButton, DeleteButton, IconButton, LoadingButton, etc.
- Inconsistent spacing, colors, typography
- No clear extension guidelines

**With design system:**
- Single Button component with variants, sizes, and props
- `<Button variant="destructive">`, `<Button size="sm">`, `<Button loading>`
- Clear rules: extend before creating new

## Usage Example

```
> "Define the design system for this project using Tailwind and shadcn/ui"
```

The agent will:
1. Ask about brand guidelines and requirements
2. Create comprehensive design system documentation
3. Define semantic color tokens
4. Set up typography and spacing scales
5. Document component creation rules
6. Create component decision guide
7. Provide Tailwind config examples

## Integration with Component Skills

1. **design-system** (this) → Define tokens and standards
2. **component-library** → Set up Storybook with design tokens
3. **component-development** → Build components using design system
4. **component-testing** → Verify design system compliance

## Workflow Order

```
design-system → component-library → component-development
```

**Correct order:**
1. Define design system first (tokens, standards)
2. Set up component library infrastructure
3. Build components following design system

**Why this order matters:**
- Components need design tokens to reference
- Standards prevent creating redundant components
- Ensures consistency from the start

## Part of

Component development workflow:
1. **design-system** (this) — Define foundation
2. **component-library** — Set up infrastructure  
3. **component-development** — Build components
4. **component-testing** — Verify components

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill design-system --global
```
