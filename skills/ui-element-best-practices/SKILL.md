---
name: ui-element-best-practices
description: >-
  Apply proven, element-level UX best practices (buttons, forms, navigation, modals,
  cards, tables, empty/loading/error states, notifications, tooltips) grounded in the
  Laws of UX. Use when building or reviewing a single UI element or component, not a
  full screen. Complements ui-ux-best-practices (screen/flow level) and is invoked
  during component-development when an element type has known failure modes.
---

# UI Element Best Practices

Apply element-specific UX best practices, grounded in the Laws of UX (lawsofux.com), to individual UI elements before they're considered done.

Announce at start: "I'm using the ui-element-best-practices skill to check this element against known UX best practices."

## Purpose

`ui-ux-best-practices` reviews composed screens and flows. This skill operates one level down: it gives per-element rules for the specific component being built or reviewed (a button, a form, a modal, a table, ...), each backed by a named, citable UX law rather than a matter of taste.

Use it to answer: *for this specific element, what does the research-backed default look like, and what does getting it wrong look like?*

## Where this sits

1. **During `component-development`** — when building or extending a component, check it against the matching element section below before marking it complete
2. **During `ui-ux-best-practices`** — as supporting detail when a composed screen's anti-slop scan flags a specific element
3. **During manual review** — "review this button/form/modal against best practices"

## Grounding: Laws of UX

Each rule below cites one or more of these (see lawsofux.com for full definitions):

| Law | One-line definition |
|---|---|
| **Fitts's Law** | Time to reach a target is a function of its size and distance — bigger, closer targets are faster to hit |
| **Hick's Law** | Decision time increases with the number and complexity of choices |
| **Jakob's Law** | Users spend most of their time on other products; they expect yours to work the same way |
| **Miller's Law** | The average person can hold about 7±2 items in working memory — chunk accordingly |
| **Law of Prägnanz** | People simplify ambiguous or complex images to the most orderly form available |
| **Law of Proximity** | Objects near each other are perceived as grouped |
| **Law of Common Region** | Elements sharing a clearly defined boundary are perceived as a group |
| **Law of Similarity** | Visually similar elements are perceived as related or of the same type |
| **Doherty Threshold** | Productivity/engagement rises when system response is under ~400ms |
| **Von Restorff Effect** | The one item that differs from its surroundings is the one most remembered |
| **Serial Position Effect** | Items at the start and end of a list are remembered best; middle items fade |
| **Zeigarnik Effect** | Unfinished or interrupted tasks are remembered better than completed ones — open loops nag |
| **Peak-End Rule** | People judge an experience mostly by its most intense point and its ending, not the average |
| **Goal-Gradient Effect** | Motivation to complete a task increases as the perceived distance to the goal shrinks |
| **Tesler's Law** | Every process has a fixed amount of irreducible complexity — the question is who absorbs it, the system or the user |
| **Postel's Law** | Be liberal in what you accept from users, conservative in what you show/send back |

## Element-by-element best practices

### 1. Buttons & CTAs

**Laws:** Fitts's Law, Hick's Law, Von Restorff Effect

- Primary action is the largest and/or most visually distinct control in its group (Von Restorff) — but only one per view/section
- Minimum hit target 44x44px, with enough padding that adjacent buttons don't cause mis-taps (Fitts's Law)
- Limit visible competing actions to what the user actually needs to decide between right now (Hick's Law) — move rare actions into a menu instead of laying out six buttons
- Label describes the action's result ("Delete project"), not a generic verb ("Submit", "OK")
- Destructive actions are visually distinct from the primary action, not just a colour swap on an identical button shape

**Fails when:** every button on the screen is the same size/weight and the user has to read all of them to find the one that matters; or a row of 5+ equally-weighted buttons forces the user to stop and parse instead of act.

### 2. Forms & Inputs

**Laws:** Miller's Law, Doherty Threshold, Law of Prägnanz, Postel's Law

- Group fields into chunks of ≤7 related items with a heading per group, not one long undifferentiated list (Miller's Law)
- Inline validation feedback appears in under ~400ms of the user leaving the field, not only on submit (Doherty Threshold)
- One column, one input per line for anything on mobile-width viewports (Law of Prägnanz) — multi-column forms increase scan time and error rate
- Accept flexible input formats (phone numbers, dates) and normalise on the backend rather than rejecting valid input over formatting (Postel's Law)
- Labels sit above fields, not just as placeholder text that disappears on focus
- Required/optional marking is consistent and stated once, not inferred per-field

**Fails when:** a 20-field form has no grouping or progress indication; or errors only appear after a full-page submit-and-reload cycle.

### 3. Navigation & Menus

**Laws:** Jakob's Law, Miller's Law, Serial Position Effect

- Primary nav uses the platform-conventional position and pattern (top bar, left rail, bottom tab bar) unless there's a specific reason to deviate (Jakob's Law) — novelty here costs comprehension, not delight
- Top-level nav items stay within ~5-7 (Miller's Law); deeper structure goes in submenus, not more top-level items
- Most important destinations go first or last in the list, never buried in the middle (Serial Position Effect)
- Current location is always visible (active state), not left for the user to infer
- Every interactive nav item has a visible focus state and is reachable by keyboard in DOM order

**Fails when:** navigation reinvents a pattern users already know (e.g. hamburger-only nav on desktop with no visible reason), or a flat list of 12 top-level items with no grouping.

### 4. Modals & Dialogs

**Laws:** Zeigarnik Effect, Von Restorff Effect, Aesthetic-Usability Effect

- A modal is only used for a task that must interrupt the user's flow (confirmation, focused short task) — not for content that could be inline (Tesler's Law: don't move complexity onto the user that the system could absorb)
- Every modal has one clear way to complete the task and one clear way to cancel/dismiss — never leave the "open loop" ambiguous (Zeigarnik Effect: an unclear exit path keeps nagging at the user)
- Background is dimmed/blurred enough that the modal reads as the one thing demanding attention (Von Restorff Effect)
- Focus moves into the modal on open and returns to the triggering element on close; Escape closes it; focus is trapped inside while open
- Destructive confirmations state the specific consequence ("Delete 12 items? This can't be undone"), not a generic "Are you sure?"

**Fails when:** a modal has no visible close affordance, traps focus but doesn't return it, or is used to show a whole secondary page of content that should have been its own view.

### 5. Cards & Content Groups

**Laws:** Law of Common Region, Law of Proximity, Law of Similarity

- A card boundary (border, background, shadow) exists only where it communicates a real grouping — not by default on every block of content
- Related fields inside a card sit closer to each other than to fields in a neighbouring card (Law of Proximity)
- Cards of the same type look identical in structure (same field order, same affordance placement) so users pattern-match instantly (Law of Similarity)
- Avoid nesting a card inside a card — if two boundaries are needed, question whether the outer one is doing anything the page background wasn't already doing

**Fails when:** every piece of content gets wrapped in its own bordered/shadowed box regardless of whether it's actually a distinct group, producing "card soup."

### 6. Tables & Data Grids

**Laws:** Law of Prägnanz, Miller's Law, Law of Uniform Connectedness

- Show the columns the user's task needs by default; put the rest behind a column picker or detail view rather than cramming everything in (Miller's Law, Tesler's Law)
- Row/column relationships are shown with alignment and subtle dividers, not heavy borders around every cell (Law of Prägnanz)
- Related columns (e.g. a value and its unit, a name and its avatar) are visually connected — spacing or shared background — so the eye reads them as one unit (Law of Uniform Connectedness)
- Every data-heavy table has a defined mobile strategy: horizontal scroll with a frozen key column, stacked card view, or explicit column priority — never just shrink the font
- Sortable/filterable columns have a visible, consistent affordance, not a hover-only indicator

**Fails when:** a table has 15 equally-weighted columns with heavy gridlines and no responsive strategy, forcing horizontal scrolling with no anchor column.

### 7. Empty, Loading, and Error States

**Laws:** Doherty Threshold, Peak-End Rule, Aesthetic-Usability Effect

- Anything that takes longer than ~400ms to respond shows a loading indicator; anything that takes longer than a couple of seconds shows a skeleton that matches the eventual layout, not a generic spinner (Doherty Threshold)
- Empty states tell the user what this space is for and give them the one action that fills it — never just "No data"
- Error states name what went wrong in plain language and give a recovery action ("Retry", "Check your connection") — never a bare error code
- Because users judge the experience by its ending (Peak-End Rule), a state the user hits often (empty, error, or the end of a long list) gets deliberate design attention, not the default fallback

**Fails when:** a blank screen with no explanation stands in for an empty state, or every loading moment is an unstyled spinner regardless of how long the wait actually is.

### 8. Notifications & Toasts

**Laws:** Von Restorff Effect, Serial Position Effect, Goal-Gradient Effect

- A toast stands out from the page (Von Restorff) only when it's carrying information the user didn't just cause themselves to expect — don't toast every successful save if the UI already shows the save happened
- Toasts appear in a consistent screen position every time, so users build a habit of glancing there (Serial Position Effect applies to where in their scan pattern they expect it)
- Multi-step progress (uploads, imports, onboarding) shows how close the user is to done, not just a static "in progress" — proximity to the goal increases motivation to finish (Goal-Gradient Effect)
- Destructive-action toasts include an undo affordance where the action is reversible, rather than relying on a confirmation dialog for everything

**Fails when:** every minor state change fires a toast (notification fatigue), or toasts appear in a different corner depending on which part of the app triggered them.

### 9. Tooltips & Progressive Disclosure

**Laws:** Fitts's Law, Doherty Threshold, Tesler's Law

- Tooltip triggers (icons, "?" affordances) meet the same minimum hit-target size as any other interactive element (Fitts's Law)
- Tooltips appear after a short, consistent delay (~300-500ms) on hover/focus, not instantly and not after a multi-second lag (Doherty Threshold)
- Advanced/rarely-used detail is disclosed on demand (accordion, "Show more", secondary panel) rather than shown to every user by default — push complexity to where it's needed, not onto everyone (Tesler's Law)
- Tooltip content adds information the label doesn't already convey — never repeats the visible label verbatim
- Every tooltip has a keyboard/focus-triggered equivalent, not hover-only

**Fails when:** critical information is hidden only in a hover tooltip with no keyboard access, or progressive disclosure hides something the primary task actually needs every time.

## Required output

When used as a review, return:

```markdown
## Element Review: [element name]

**Verdict:** Pass | Needs revision | Blocked

### Applicable laws
- [Law] — how it applies here

### Issues found
1. **[Severity]** Description
   - Law violated:
   - Recommended fix:

### What works
- ...
```

## Principles

- **Every rule cites a law, not a preference** — if you can't name the mechanism, it's not a rule here, it's a style opinion
- **Element-level, not screen-level** — for composed views and flows, use `ui-ux-best-practices` instead
- **Defaults, not mandates** — these are the well-supported default; deviate deliberately and say why, don't deviate by accident
- **Fix the element before it's composed** — cheaper to correct a button's hit target in isolation than after it's used forty times across the app
