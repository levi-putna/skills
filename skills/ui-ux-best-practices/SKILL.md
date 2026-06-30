---
name: ui-ux-best-practices
description: >-
  Review and guide UI/UX work for clean, professional, low-noise interfaces.
  Use before building pages/screens, when reviewing AI-generated UI, when a UI
  feels busy or generic, and as a quality gate for whitespace, hierarchy,
  responsiveness, accessibility, task flow, and anti-slop design patterns.
---

# UI/UX Best Practices

Review and guide UI/UX work so generated interfaces are clear, calm, responsive, accessible, and product-specific.

Announce at start: "I'm using the ui-ux-best-practices skill to review the interface quality and UX flow."

## Purpose

This skill prevents common AI-generated UI/UX problems:

- Generic SaaS sameness
- Too many cards, badges, gradients, icons, and decorations
- Weak hierarchy and unclear primary action
- Dense or noisy layouts that overwhelm users
- Inconsistent whitespace and spacing rhythm
- Components that look individually fine but feel like different products together
- Responsive layouts that collapse poorly on mobile
- UX flows that optimise for visual completion instead of user task completion

## Where this sits

Use this skill:

1. **After `ui-requirements.md`** — to review whether product-specific UX intent is clear
2. **During `design-system`** — to ensure tokens support clean, coherent UI
3. **During `component-development`** — to check component clarity, states, hierarchy, and density
4. **During `executing-plans`** — before integrating pages/screens that compose multiple components
5. **Before shipping** — as a final UI/UX quality gate

## Core standard

A professional UI should make the user's task easier, not merely look complete.

Every screen must answer:

1. **What is this screen for?**
2. **What should the user do next?**
3. **What information matters most?**
4. **What can be removed, delayed, collapsed, or simplified?**
5. **Does this match the design system and product context?**

If those are unclear, do not polish. Simplify the hierarchy first.

## Syntax-inspired UX check

The Syntax discussion on AI design highlighted that AI can generate attractive fragments, but often fails at product-specific UX: it throws buttons and cards onto screens without deeply understanding the user's task, flow, friction, or context.

Apply this check:

- Does the screen solve the specific app problem, or could it belong to any SaaS?
- Is the flow low-friction for the primary user task?
- Are buttons and cards supporting a real decision, or just filling space?
- Are there cards inside cards because the AI defaulted to containers?
- Is a human UX rationale visible in the structure?
- Would user feedback change this flow? If yes, capture assumptions and test them.

Do not accept "looks good" as sufficient. The screen must help the user complete the intended task.

## Clean UI principles

### 1. Hierarchy before decoration

Review in this order:

1. Primary user goal
2. Primary message or state
3. Primary action
4. Secondary actions
5. Supporting details
6. Decorative or brand elements

Rules:

- One clear primary action per view or section
- Use size, spacing, and placement before colour or decoration
- Do not give every element equal visual weight
- Remove decorative elements that do not clarify the task

### 2. Whitespace is structure

Whitespace is not unused space. It groups, separates, and reduces cognitive load.

Use proximity intentionally:

- Smallest gap: label and field, icon and text
- Medium gap: sibling controls
- Larger gap: groups of related content
- Largest gap: major sections

Rules:

- Use spacing tokens from the design system
- Preserve grouping relationships across breakpoints
- Do not collapse mobile spacing to zero
- Prefer removing content over shrinking everything to fit
- If a layout feels busy, first increase grouping clarity and remove low-value elements

### 3. Responsive design is not compression

Mobile is where weak UI breaks first.

Responsive checks:

- Primary action remains visible and reachable
- Touch targets meet the design-system minimum, typically at least 44x44px
- Multi-column layouts collapse into a meaningful reading order
- Long headings, names, labels, and values wrap gracefully
- Forms remain easy to scan and complete
- Tables have a mobile strategy: stacked rows, priority columns, horizontal scroll, or alternate card layout
- Sticky headers/footers do not consume too much vertical space

Never solve mobile by simply reducing font sizes and spacing until everything fits.

### 4. Content drives layout

Use real or realistic content before judging design quality.

Check:

- Long names, long email addresses, long labels
- Empty states
- Loading states
- Error states
- Permission-denied states
- Partial data
- Large data sets
- Internationalisation or longer translated text if relevant

If the design only works with perfect demo copy, it is not ready.

### 5. Reduce cognitive load

Rules:

- Keep choices limited and grouped
- Avoid placing multiple competing CTAs in the same visual band
- Use progressive disclosure for advanced or rarely used options
- Avoid dashboards full of equal-weight metrics unless every metric informs an action
- Prefer meaningful defaults
- Make destructive actions distinct but not visually loud until relevant
- Use plain language over generic marketing phrases

### 6. Accessibility is UX

Minimum checks:

- Keyboard navigation works in logical order
- Focus states are visible
- Colour is not the only state indicator
- Text contrast meets the documented WCAG target
- Touch targets are large enough
- Motion respects `prefers-reduced-motion`
- Error messages explain how to recover
- Form fields have labels, helper text, and clear validation timing

## AI-generated UI anti-patterns to avoid

These patterns often make interfaces look generic, busy, or AI-generated. Do not use them unless explicitly justified by the design system.

### Visual slop

- Purple-to-blue gradients as default hero backgrounds
- Gradient text for emphasis
- Neon cyan/purple accents on dark backgrounds
- Floating blurred blobs or decorative gradient orbs
- Glassmorphism or backdrop blur everywhere
- Heavy shadows on every card
- Same border radius on every object without hierarchy
- Pure white cards on pale grey backgrounds with low intentional contrast
- Decorative 3D shapes, particles, or floating icons with no informational purpose

### Component slop

- Everything wrapped in cards
- Cards inside cards
- Every feature in a three-card grid by default
- Badge above every heading
- Coloured left-border strips used decoratively
- Pill badges everywhere
- Icons above every title
- Multiple button styles competing in one section
- Loading spinners where skeletons or optimistic UI would be clearer

### Layout slop

- Hero -> three features -> pricing -> FAQ -> footer without product-specific reason
- Identical section padding everywhere
- `max-w-7xl mx-auto` used reflexively on every layout
- Equal spacing between unrelated and related elements
- Dashboard metrics presented with equal weight and no action path
- Bento grids used because they look modern, not because content needs them
- Full dark mode by default when not requested or appropriate

### Interaction slop

- Hover-only affordances with no keyboard or touch equivalent
- Missing active, disabled, loading, selected, and error states
- Animation on everything
- Infinite pulsing badges
- Focus states removed or barely visible
- Modal flows for simple inline decisions

### Copy slop

- Generic CTAs: "Get started", "Learn more", "Boost productivity"
- Buzzword-heavy headings that do not describe the product task
- Empty states that apologise but do not guide next action
- Error messages that state failure without recovery
- Placeholder content that hides actual content constraints

## AI UI anti-slop rules

When using AI to generate UI:

1. **State what not to do.** Include explicit negative constraints.
2. **Use the design system.** Do not invent new colours, shadows, radii, or spacing.
3. **Pick a product-specific direction.** Avoid generic "modern SaaS" defaults.
4. **Design around the task.** Start with user flow and information hierarchy.
5. **Review in layers.** Hierarchy -> spacing -> responsive -> states -> polish.
6. **Prefer quiet structure over loud decoration.** Whitespace, type, and grouping should do most of the work.
7. **Use fewer components better.** Extend and compose before creating new components.

## Page/screen review checklist

Use this before integrating or shipping a UI view.

### Purpose and flow

- [ ] The primary user task is clear within five seconds
- [ ] The primary action is obvious
- [ ] Secondary actions are visually secondary
- [ ] The flow has no unnecessary modal, page, or confirmation step
- [ ] The screen reflects the specific product domain, not generic SaaS defaults

### Hierarchy and composition

- [ ] One dominant message/state per section
- [ ] Visual weight matches importance
- [ ] Related items are grouped by proximity
- [ ] Unrelated groups have enough separation
- [ ] Decorative elements serve a purpose or are removed

### Whitespace and density

- [ ] Spacing comes from the design-system scale
- [ ] Internal component spacing is smaller than group spacing
- [ ] Group spacing is smaller than section spacing
- [ ] The layout does not feel cramped on mobile
- [ ] Content was cut or collapsed rather than squeezed when space was tight

### Responsiveness

- [ ] Mobile layout has a deliberate reading order
- [ ] Touch targets are large enough
- [ ] Long content wraps without breaking layout
- [ ] Tables/data-heavy areas have a mobile strategy
- [ ] Sticky elements do not crowd the viewport

### States and feedback

- [ ] Loading, empty, error, disabled, hover, focus, active, and selected states are covered where relevant
- [ ] Error states explain recovery
- [ ] Empty states guide the next useful action
- [ ] Motion is purposeful and reduced-motion compatible

### Design-system conformance

- [ ] Uses semantic colour tokens
- [ ] Uses approved typography scale
- [ ] Uses approved spacing, radius, and shadow scales
- [ ] Uses approved icon family and stroke/fill style
- [ ] Does not introduce one-off styles or components

### Anti-slop scan

- [ ] No default purple/blue gradient treatment unless explicitly in brand
- [ ] No unnecessary card nesting
- [ ] No badge-above-heading pattern by reflex
- [ ] No decorative blobs, glassmorphism, or heavy shadows without purpose
- [ ] No generic three-card grid unless content genuinely fits it
- [ ] No generic buzzword copy or vague CTAs

## Component composition guidance for views

When building pages from components:

- Start with the user's task flow, not a component inventory
- Compose the fewest components needed to express the task
- Use layout components to create rhythm and grouping
- Keep repeated regions consistent, but avoid making every section visually identical
- Let important content breathe
- Collapse advanced controls behind disclosure when they are not part of the primary task
- Prefer inline feedback over disruptive dialogs
- Use modals only for focused tasks, confirmations, or interruptions that genuinely require attention

## Required output

When this skill is used as a review, return:

```markdown
## UI/UX Review

**Verdict:** Pass | Needs revision | Blocked

### What works
- ...

### Issues to fix
1. **[Severity] [Area]** Description and why it affects UX
   - Recommended change:
   - Related design-system rule:

### Anti-slop findings
- ...

### Responsive/accessibility findings
- ...

### Required next steps
- ...
```

## Principles

- **Clarity over cleverness** — the user task comes first
- **Whitespace is information architecture** — spacing communicates relationships
- **Responsive design preserves intent** — it does not merely shrink desktop
- **Quiet UI is usually better UI** — remove noise before adding polish
- **AI needs constraints** — block generic defaults explicitly
- **UX is flow, not decoration** — a screen must reduce friction in a specific task
