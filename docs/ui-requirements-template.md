# UI Requirements Documentation Template

This template should be created by the **technical-documentation** skill for projects with UI components.

## Purpose

`docs/technical/ui-requirements.md` captures design system needs, brand identity, and UI/UX requirements **before** implementation begins. This document feeds directly into the **design-system** skill.

## When to Create

- Web applications with UI
- Mobile applications  
- Any project with visual components
- Projects using component libraries (shadcn/ui, etc.)

## Template Content

See `skills/technical-documentation/SKILL.md` for the complete `ui-requirements.md` template, which includes:

### Sections

1. **UI/UX Requirements**
   - Target platforms (web, mobile, tablet)
   - Accessibility requirements (WCAG level)
   - Browser/device support

2. **Brand Identity**
   - Brand values (what design should communicate)
   - Voice and tone
   - Visual style direction

3. **Color Requirements**
   - Brand colors (primary, secondary, accent)
   - Semantic colors (success, error, warning, info)
   - Color accessibility (contrast requirements)
   - Light/dark mode support

4. **Typography Requirements**
   - Font personality
   - Typeface preferences (heading, body, monospace)
   - Readability requirements

5. **Layout and Spacing**
   - Layout style (spacious, compact, balanced)
   - Grid system (4px, 8px, 12-column, etc.)
   - Responsive behavior (mobile-first, desktop-first)

6. **Component Style Preferences**
   - Buttons (style, size, hover effects)
   - Forms (style, validation, error display)
   - Cards (style, spacing)
   - Navigation (style, position)

7. **Interaction Patterns**
   - Transitions (speed, style)
   - Loading states (spinner, skeleton, progress)
   - Empty states (style, tone)

8. **Existing Design Assets**
   - Design files (Figma, etc.)
   - Brand guidelines
   - Logo/assets location
   - Design system base (shadcn/ui, Material UI, custom)

9. **Component Requirements**
   - Must-have components list
   - Complex patterns needed

## Workflow Integration

```
brainstorming
    ↓
technical-documentation
    ├─ requirements.md
    ├─ architecture.md
    ├─ tech-stack.md
    ├─ ui-requirements.md  ← Captures design needs
    └─ ...
    ↓
design-system  ← Reads ui-requirements.md
    ↓
component-library
    ↓
component-development
```

## Decision Ownership in ui-requirements.md

| Decision | Ownership |
|----------|-----------|
| Brand colors | 🔒 Human-locked |
| Logo, brand assets | 🔒 Human-locked |
| Typography (specific fonts) | 🔒 Human-locked if specified |
| WCAG level | 🔒 Human-locked |
| Semantic color mapping | 🤖 Agent discretion (uses brand colors) |
| Spacing scale | 🤖 Agent discretion |
| Component sizes | 🤖 Agent discretion |
| Border radius, shadows | 🤖 Agent discretion |

## Hand-off

After `ui-requirements.md` is approved:

> "UI requirements documented. Next: Use **design-system** skill to convert these into design tokens and component standards."

The **design-system** skill will:
1. Read `ui-requirements.md`
2. Create semantic color tokens from brand colors
3. Define typography scale
4. Set up spacing system
5. Create component standards
6. Document extension vs. creation rules
