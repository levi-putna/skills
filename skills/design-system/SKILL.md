---
name: design-system
description: >-
  Define and document the design system including brand identity, design tokens, color palette,
  typography, spacing scale, and component standards. Creates the foundation for consistent UI.
  Use before component-library when starting a project, or when establishing design system standards.
---

# Design System

Define the design system foundation: brand identity, design tokens, color palette, typography, spacing, and component standards. This is the source of truth for all UI decisions.

Announce at start: "I'm using the design-system skill to establish the design system foundation."

## Why design system first?

Without a design system:
- Components use inconsistent colors, spacing, and typography
- Dozens of similar components proliferate (Button, PrimaryButton, BlueButton, etc.)
- No clear guidelines for when to extend vs. create new
- Brand changes require updating hundreds of hardcoded values

With a design system:
- Single source of truth for all design decisions
- Components use semantic tokens (not hardcoded values)
- Clear standards prevent component proliferation
- Brand changes update in one place

## Prerequisites

Before starting:

1. **Gather brand assets** — Logo, brand guidelines, existing design if available
2. **Read UI requirements** — If `docs/technical/ui-requirements.md` exists, use it as the primary source of truth
3. **Check for existing design system** — Read any docs in `docs/design/` or design files
4. **Understand target platforms** — Web, mobile, accessibility requirements

**Ask the user:**
- "Do you have existing brand guidelines or a design file (Figma, etc.) to reference?"
- "What's your target audience and accessibility requirements?"
- "Are you using a base design system (shadcn/ui, Tailwind, MUI) or building from scratch?"

## Design system structure

Create `docs/design/design-system.md` as the single source of truth. If `docs/technical/ui-requirements.md` exists, every human-locked brand, typography, accessibility, and asset decision must be carried forward without reinterpretation.

See the full template in the skill documentation for:
- Brand identity (values, voice, tone)
- Design tokens (colors, typography, spacing, borders, shadows)
- Component standards (when to create vs. extend)
- Component audit process
- Accessibility standards
- Responsive design patterns

## Required source mapping

When creating or updating the design system, include a source mapping table:

```markdown
## Source mapping

| Design-system decision | Source | Owner |
|---|---|---|
| Primary colour token | `docs/technical/ui-requirements.md#color-requirements` | 🔒 |
| WCAG level | `docs/technical/ui-requirements.md#accessibility` | 🔒 |
| Spacing scale | Agent choice based on UI requirements | 🤖 |
```

This makes it clear which design decisions came from human brand guidance and which were agent-filled implementation details.

## Component Decision Framework

### When to Create vs. Extend

**Create a NEW component when:**
- ✅ Fundamentally different purpose (Button vs. Input)
- ✅ Different interaction pattern (Dropdown vs. Combobox)
- ✅ Different accessibility requirements (Button vs. Link)

**Extend an EXISTING component when:**
- ✅ Same purpose, different visual style (use variants)
- ✅ Same purpose, different size (use size prop)
- ✅ Same purpose, additional optional feature (add prop)

### Component Audit Process

Before creating a new component, **ALWAYS**:

1. **Search existing components:**
   ```bash
   ls components/ui/ components/composed/ components/patterns/
   ```
   
2. **Check Storybook for similar patterns:**
   - Open http://localhost:6006
   - Review all categories for similar functionality
   
3. **Evaluate extension vs. creation:**
   - Can existing component handle this with a new variant?
   - Would adding this functionality break existing use cases?
   - Is the purpose fundamentally different?

4. **Document decision:**
   - If creating new: Explain why existing components insufficient
   - If extending: List new props and impact on existing use cases

### Examples

| Scenario | Decision | Implementation |
|----------|----------|----------------|
| Need destructive button | **Extend Button** | `<Button variant="destructive">` |
| Need small button | **Extend Button** | `<Button size="sm">` |
| Need button with icon | **Extend Button** | `<Button><Icon /> Text</Button>` (composition) |
| Need loading button | **Extend Button** | `<Button loading>` (add prop) |
| Need clickable card | **Create new** | `<CardButton>` (different semantics) |

## Backward Compatibility Testing

When extending components, MUST verify:

1. **All existing stories still render**
   - Run Storybook and check no visual breaks
   - Automated via visual regression tests

2. **Existing usage patterns still work**
   - Grep codebase for component usage
   - Test critical paths with existing props

3. **Default behaviour unchanged**
   - New props must be optional
   - Default rendering must match previous version

4. **Document migration if needed**
   - If breaking change unavoidable, document migration path
   - Version the component or provide codemods

## Design governance checks

Before handing off, verify:

- [ ] Every brand/accessibility 🔒 decision from `ui-requirements.md` appears in `docs/design/design-system.md`
- [ ] No hardcoded implementation tokens are introduced without a semantic name
- [ ] Component creation vs extension rules are documented
- [ ] Accessibility baseline includes keyboard, screen reader, contrast, reduced motion, and target size requirements
- [ ] The design system states how visual regression and component audit decisions are enforced

## Design Tokens Implementation

### Colors (Semantic Tokens)

Always use semantic tokens, never hardcoded values:

```tsx
// ✅ Good
<Button className="bg-primary text-primary-foreground">

// ❌ Bad
<Button className="bg-blue-500 text-white">
```

**Rationale:** Semantic tokens enable theming and ensure consistency.

### Typography Scale

Use consistent font sizing from design system:

```tsx
// ✅ Good
<h1 className="text-4xl font-bold">

// ❌ Bad
<h1 style={{ fontSize: '36px', fontWeight: 700 }}>
```

### Spacing

Use spacing scale tokens (4px grid):

```tsx
// ✅ Good
<div className="p-4 gap-6 my-12">

// ❌ Bad
<div style={{ padding: '17px', gap: '25px', marginTop: '50px' }}>
```

## Hand off

After creating design system documentation:

> "Design system documented at `docs/design/design-system.md`.
>
> Key files created:
> - Design tokens and color palette
> - Typography scale
> - Spacing and layout standards
> - Component creation guidelines
> - Component decision guide
>
> Next steps:
> - Use **component-library** to set up Storybook with these tokens
> - All new components must follow these standards
> - Run design system conformance checks before shipping"

If the user wants to set up the component library now, offer to invoke the **component-library** skill.

## Principles

- **Single source of truth** — All design decisions documented in one place
- **Semantic tokens** — Use meaning, not appearance (`primary` not `blue`)
- **Component economy** — Extend before creating new
- **Accessibility first** — WCAG AA minimum on all components
- **Brand consistency** — All components use design system tokens
- **Backward compatibility** — Extensions must not break existing use cases
- **Traceability** — Design-system decisions trace back to UI requirements or documented agent-discretion choices
