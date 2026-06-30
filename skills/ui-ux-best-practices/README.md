# UI/UX Best Practices

**Review and guide UI/UX work for clean, professional, low-noise interfaces.**

Use this skill when designing or reviewing pages, screens, views, flows, and AI-generated UI output.

## When to use

- Before building a page or screen
- When reviewing AI-generated UI
- When a layout feels busy, noisy, generic, or overwhelming
- Before integrating components into application views
- Before shipping UI-heavy work
- When checking whitespace, hierarchy, responsiveness, accessibility, or interaction flow

## What it does

This skill provides a quality gate for:

- Clear hierarchy and primary actions
- Purposeful whitespace and spacing rhythm
- Responsive layout behaviour
- Accessibility and inclusive interaction states
- Product-specific UX flow
- Avoiding generic AI-generated UI patterns
- Avoiding visual noise, over-decoration, and component clutter

## Anti-slop focus

The skill explicitly guards against common AI UI fingerprints:

- Purple/blue gradients by default
- Gradient text and decorative blobs
- Glassmorphism everywhere
- Everything wrapped in cards
- Cards inside cards
- Badge above every heading
- Generic three-card feature grids
- Heavy shadows and noisy borders
- Hover-only interactions
- Missing loading, empty, error, focus, active, and disabled states
- Buzzword-heavy copy and vague CTAs

## Syntax-inspired UX lens

The skill includes a UX check inspired by the Syntax discussion on AI-generated design:

AI can generate attractive fragments, but often misses the specific product task, user flow, and friction. This skill asks whether the screen actually helps the user complete the intended task, rather than simply looking like a plausible SaaS interface.

## Fits with

Recommended UI lifecycle:

1. `technical-documentation` creates `ui-requirements.md`
2. `ui-ux-best-practices` reviews UX direction and risks
3. `design-system` defines tokens and component standards
4. `component-library` sets up isolated previews
5. `component-development` builds reusable components
6. `component-testing` verifies states, a11y, visual regression
7. `ui-ux-best-practices` reviews composed views before shipping

## Example prompt

```text
Review this dashboard screen for clean UI/UX, whitespace, responsive behaviour, and AI-generated UI antipatterns.
```

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill ui-ux-best-practices --global
```
