# UI Component Development Skills — Implementation Summary

## Overview

Three new skills have been created to fill the gap in UI component development and testing:

1. **component-library** — Sets up component showcase infrastructure
2. **component-development** — Builds components in isolation following CDD best practices
3. **component-testing** — Adds comprehensive testing (interaction, visual, accessibility, unit)

## Key Features

### Component Library Skill

**Purpose:** Set up the foundation for component-driven development

**Features:**
- Storybook 8 installation and configuration (recommended)
- Alternative: Custom in-app gallery route (`/components`)
- Integration with Next.js and React
- Support for shadcn/ui and Vercel AI SDK components
- Auto-generated documentation
- Component category structure (primitives, composed, patterns, ai)

**Output:**
- Storybook configuration with Vite
- Preview configuration with global styles
- NPM scripts for development and testing
- Documentation on component library structure
- Helper decorators for common scenarios

### Component Development Skill

**Purpose:** Build UI components in isolation following TDD principles

**Features:**
- Stories-first development (write stories before implementation)
- CSF3 (Component Story Format 3) with TypeScript
- Variant systems with class-variance-authority (CVA)
- shadcn/ui composition patterns (compound components with Context)
- Vercel AI SDK patterns (tool invocations, streaming states)
- Interaction testing with play functions
- Accessibility-first development

**Process:**
1. Design component API (props, variants, states)
2. Create story file with all variants
3. Implement component with TypeScript
4. Add interaction tests
5. Verify accessibility
6. Create unit tests (optional but recommended)

**Output:**
- Component implementation (`component.tsx`)
- Story file with variants (`component.stories.tsx`)
- Unit tests (`component.test.tsx`)
- Auto-generated documentation in Storybook

### Component Testing Skill

**Purpose:** Add comprehensive testing to ensure production-ready quality

**Testing layers:**
1. **Interaction tests** — Storybook play functions simulate user behaviour
2. **Visual regression** — Chromatic (cloud) or Playwright (self-hosted)
3. **Accessibility** — @storybook/addon-a11y with axe-core
4. **Unit tests** — React Testing Library with user-centric queries
5. **API mocking** — Mock Service Worker (MSW) at network boundary

**Features:**
- Interaction testing with `@storybook/test`
- Visual regression testing (Chromatic or Playwright)
- WCAG compliance verification
- React Testing Library best practices
- MSW handler factories for API mocking
- CI/CD integration with GitHub Actions

**Output:**
- Play functions in story files
- Visual regression configuration
- Unit test files with React Testing Library
- GitHub Actions workflow
- Accessibility audit reports

## Design Patterns Enforced

### Component-Driven Development (CDD)

- Build components in isolation before page integration
- Stories serve as executable specifications
- Components are genuinely decoupled (forced to think about API)
- Any component state reachable in seconds

### shadcn/ui Composition Patterns

- **Composition over configuration** — No prop-heavy components
- **Compound components with Context** — Avoid prop drilling
- **Correct hierarchy** — Always use proper nesting (e.g., SelectItem inside SelectGroup)
- **Variant systems** — CVA for systematic variant patterns
- **Semantic tokens** — Use design system tokens, not hardcoded colours

### Vercel AI SDK Patterns

- **Component registry** — Map tool names to React components
- **State rendering** — Handle input-streaming, input-available, output-available states
- **Type safety** — Zod schemas for tool parameters
- **Graceful degradation** — Fallback for unknown tool names

### Testing Best Practices

- **User-centric queries** — Query by role, not by test ID
- **Realistic interactions** — Use `userEvent`, not `fireEvent`
- **Test behaviour, not implementation** — Verify what users see
- **Mock at network boundary** — MSW for API mocking
- **Multiple layers** — Interaction + visual + accessibility + unit
- **Automated in CI** — All tests run on every PR

## Integration with Existing Skills

These skills complement the existing skill harness:

### Standalone utility
- **general-writing** — Polish component documentation
- **mermaid-diagrams** — Diagram component architecture

### Design phase
- **brainstorming** → Can discuss component requirements
- **technical-documentation** → Can specify component contracts

### Build phase
- **planning** → Can create task lists for component implementation
- **test-driven-development** → Component tests follow TDD principles
- **executing-plans** → Can execute component implementation plans

### Ship phase
- **shipping** → Component changes follow same commit/PR process

## Workflow Examples

### Example 1: New component from scratch

```
1. User: "Set up a component library for this project"
   → Skill: component-library
   → Output: Storybook configured, documentation created

2. User: "Create a UserCard component with avatar, name, role, and expand/collapse"
   → Skill: component-development
   → Output: UserCard + stories + initial tests

3. User: "Add comprehensive tests to UserCard"
   → Skill: component-testing
   → Output: Interaction tests, visual regression, accessibility verified
```

### Example 2: Building multiple related components

```
1. User: "Build a set of dashboard components: StatCard, ChartCard, MetricCard"
   → Skill: component-development (invoked 3 times)
   → Output: Three components with stories

2. User: "Test all dashboard components together"
   → Skill: component-testing
   → Output: Comprehensive test suite for all components
```

### Example 3: Refactoring existing component

```
1. User: "The Button component needs destructive and ghost variants"
   → Skill: component-development
   → Output: Updated component with new variants, updated stories

2. User: "Verify the new variants pass all tests"
   → Skill: component-testing
   → Output: Visual regression tests verify no unintended changes
```

## File Structure

```
project/
  .storybook/                      # Storybook configuration
    main.ts
    preview.ts
  
  components/
    ui/                            # Primitives (shadcn/ui or base)
      button/
        button.tsx
        button.stories.tsx
        button.test.tsx
    
    composed/                      # Application-specific combinations
      user-card/
        user-card.tsx
        user-card.stories.tsx
        user-card.test.tsx
    
    patterns/                      # Complex reusable patterns
      dashboard-header/
        dashboard-header.tsx
        dashboard-header.stories.tsx
        dashboard-header.test.tsx
    
    ai/                           # AI SDK components
      message-bubble/
        message-bubble.tsx
        message-bubble.stories.tsx
        message-bubble.test.tsx
    
    gallery/                      # Custom gallery (optional)
      component-gallery.tsx
      component-registry.tsx
  
  docs/
    component-library.md          # Component library documentation
    ui-component-development-research.md  # Research summary
  
  .github/
    workflows/
      component-tests.yml         # CI workflow for component tests
```

## Technology Stack

| Technology | Purpose |
|------------|---------|
| **Storybook 8** | Component development environment and showcase |
| **Vite** | Fast build tool (Storybook uses Vite by default) |
| **CSF3** | Component Story Format 3 (TypeScript-first) |
| **@storybook/test** | Interaction testing framework |
| **@storybook/addon-a11y** | Accessibility auditing |
| **Chromatic** | Visual regression testing (cloud) |
| **Playwright** | Visual regression testing (self-hosted alternative) |
| **React Testing Library** | Unit testing with user-centric queries |
| **Vitest** | Test runner (fast, ESM-native) |
| **MSW** | API mocking at network boundary |
| **CVA** | class-variance-authority for variant systems |

## Best Practices Enforced

1. **Stories first, component second** — TDD for UI
2. **Isolation** — Components developed independently of the application
3. **Accessibility by default** — WCAG compliance verified automatically
4. **Variants with CVA** — Systematic approach to style variants
5. **Composition over props** — Compound components for complex state
6. **Type safety** — Full TypeScript coverage
7. **Documentation from code** — JSDoc generates Storybook docs
8. **Test at multiple layers** — Interaction + visual + accessibility + unit
9. **User-centric testing** — Test behaviour, not implementation
10. **Automated quality gates** — All tests run in CI

## Research Foundation

The skills are based on extensive research of industry best practices in 2026:

- Storybook 8 as the industry standard
- Component-Driven Development (CDD) methodology
- shadcn/ui composition patterns
- Vercel AI SDK UI patterns
- React Testing Library best practices
- Visual regression testing with Chromatic/Playwright
- Accessibility testing with axe-core
- MSW for API mocking

See `docs/ui-component-development-research.md` for detailed research findings.

## Usage Recommendations

### When to use these skills

**Use component-library:**
- Starting a new project
- Need a component showcase or playground
- Setting up design system infrastructure
- Team needs visual component browser

**Use component-development:**
- Creating any new UI component
- Building reusable component patterns
- Composing shadcn/ui primitives
- Creating AI-powered components
- Need to build UI elements in isolation

**Use component-testing:**
- After creating a component (quality gate)
- Before deploying to production
- Setting up CI/CD for component tests
- Need to verify accessibility compliance
- Want visual regression protection

### Workflow integration

**Standalone component work:**
```
component-library → component-development → component-testing
```

**With planning skills:**
```
planning → executing-plans (drives component-development) → component-testing
```

**With full harness:**
```
technical-documentation → planning → executing-plans → component-development → component-testing → shipping
```

## Next Steps

To use these skills:

1. **Install skills:**
   ```bash
   npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-library --global
   npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-development --global
   npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-testing --global
   ```

2. **Set up component library:**
   ```
   > "Set up a component library showcase for this project"
   ```

3. **Build components:**
   ```
   > "Create a [ComponentName] with [features]"
   ```

4. **Add tests:**
   ```
   > "Add comprehensive tests to [ComponentName]"
   ```

## Conclusion

These three skills provide a complete workflow for building production-ready UI components following industry best practices. They enforce:

- Component-driven development (isolation first)
- Comprehensive testing (multiple layers)
- Accessibility by default (WCAG compliance)
- Visual regression protection (pixel-perfect accuracy)
- Type safety (TypeScript throughout)
- Documentation automation (from code to docs)

The skills work standalone or integrate with the existing spec-driven development harness for full end-to-end workflow support.
