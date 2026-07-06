# UI Design Laws

**A law-first checklist of established UI/UX design laws — Fitts's, Hick's, Jakob's, Miller's, Gestalt grouping principles, and others.**

Where `ui-element-best-practices` starts from an element (button, form, modal, ...) and looks up which laws apply, this skill starts from the law and sweeps every element against it. Use whichever entry point fits: element-first for a deep single-component review, law-first for a fast pass across a whole screen or as a design constraint before you start building.

## When to use

- Before building a UI element, as a design constraint rather than an afterthought
- As a fast checklist pass during `component-development`, ahead of the deeper element-specific review in `ui-element-best-practices`
- When `ui-ux-best-practices` flags a screen-level issue and you need to name the mechanism behind it
- Any time a review needs to say "this violates the Law of Proximity," not "this looks off"

## The four groups

1. **Grouping & perception** (Gestalt) — Proximity, Similarity, Common Region, Uniform Connectedness, Prägnanz, Figure-Ground. Does the eye parse the layout correctly before reading a word?
2. **Attention & memory** — Miller's Law, Serial Position Effect, Von Restorff Effect, Zeigarnik Effect, Selective Attention. What does the user notice, hold onto, and remember?
3. **Interaction cost** — Fitts's Law, Hick's Law, Jakob's Law, Doherty Threshold, Postel's Law. What does it cost the user to act?
4. **Motivation & complexity** — Goal-Gradient Effect, Peak-End Rule, Tesler's Law, Aesthetic-Usability Effect, Occam's Razor/Pareto Principle. What keeps the user going, and who absorbs the system's complexity?

Each law in the skill has a one-line definition, a concrete "apply it" action, an "ask yourself" question, and an example of what breaking it looks like.

## Fits with

1. `design-system` defines the tokens (spacing, colour, radius) these laws assume
2. Use before/during `component-development` as a design constraint
3. `ui-element-best-practices` for the fuller, element-specific rule set once the element type is known
4. `ui-ux-best-practices` reviews the composed screen — cite the specific law when flagging an issue

## Example prompt

```text
Check this settings screen against the UI design laws — start with the grouping and perception laws.
```

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill ui-design-laws --global
```
