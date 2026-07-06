---
name: ui-design-laws
description: >-
  Apply the established canon of UI/UX design laws (Fitts's, Hick's, Jakob's,
  Miller's, Gestalt grouping principles, and others) as a law-first checklist
  when designing or reviewing a UI element. Use before or while building any
  element to ground sizing, grouping, wording, and interaction decisions in
  named, research-backed principles instead of visual taste. Complements
  ui-element-best-practices (organized by element) and ui-ux-best-practices
  (organized by screen/flow).
---

# UI Design Laws

Run every UI element through the established body of UI/UX design laws before calling it done. Where `ui-element-best-practices` starts from the element and looks up the laws that apply, this skill starts from the law and checks every element against it — useful as a fast pre-flight pass, or when a design decision needs to cite a mechanism, not a preference.

Announce at start: "I'm using the ui-design-laws skill to check this against established UI design laws."

## Purpose

Design decisions drift into taste ("this feels cleaner") when no one names the mechanism behind them. This skill gives a fixed reference of named laws — each with a one-line definition, what to actually do about it, and what breaking it looks like — so a review can say "this violates the Law of Proximity" instead of "this looks off."

## Where this sits

1. **Before building an element** — run the relevant law group as a design constraint, not an afterthought
2. **During `component-development`** — a fast checklist pass before the deeper element-specific review in `ui-element-best-practices`
3. **During `ui-ux-best-practices`** — cite the specific law when a composed screen's hierarchy, grouping, or flow feels off
4. **During manual review** — "check this element/screen against the UI design laws"

Use `ui-element-best-practices` when you already know the element type (button, form, modal, ...) and want the full set of rules for it. Use this skill when you want to sweep every element against one law at a time, or when you need a named mechanism to justify a change.

## How to use this

1. Identify what's being reviewed: a single element, a group of elements, or a full screen.
2. Work through the law groups below in order — Grouping & Perception first (does the eye parse this correctly at all?), then Attention & Memory, then Interaction Cost, then Motivation & Complexity.
3. For each law, answer the "Ask" question. If the answer points to a problem, note it with the law name, not a vague description.
4. Report using the format in **Required output**.

## Grouping & perception (Gestalt principles)

How the eye parses a layout before the user reads a single word.

| Law | Definition | Apply it | Ask | Violation looks like |
|---|---|---|---|---|
| **Law of Proximity** | Objects near each other are perceived as one group | Related fields/controls sit closer to each other than to unrelated ones | Do items that belong together sit closer than items that don't? | Uniform gap everywhere — the eye can't tell where one group ends and the next begins |
| **Law of Similarity** | Visually similar elements are perceived as the same kind of thing | Same element type (card, button style, icon) looks identical wherever it repeats | Does every instance of "the same thing" actually look the same? | Three "user card" instances with fields in three different orders |
| **Law of Common Region** | Elements inside a shared boundary are perceived as one group | A border/background/shadow is used only where it marks a real group | Does every visual boundary correspond to an actual grouping? | Every block of content wrapped in its own card regardless of whether it's a distinct group ("card soup") |
| **Law of Uniform Connectedness** | Elements visually connected (shared background, connecting line, aligned edge) are perceived as more related than unconnected elements, even at equal distance | Connect a value to its unit, a label to its control, a step to its number, with shared visual treatment | Are related pairs visually tied together, or just placed near each other and hoped for? | A number and its unit of measure sit in separate table columns with no visual link |
| **Law of Prägnanz** | People resolve ambiguous or complex shapes to the simplest, most orderly interpretation available | Default to the simplest layout that still conveys the structure; simplify before decorating | If you removed every non-essential visual element, would the structure still read correctly? | A 15-column table with heavy gridlines around every cell instead of alignment and light dividers |
| **Figure-Ground** | The eye separates a scene into a foreground object and a background it sits on | The primary content is the figure; chrome, backgrounds, and decoration recede | Is it instantly obvious what's content and what's background/chrome? | A decorative background pattern competing for attention with the actual content sitting on it |

## Attention & memory

What the user notices, holds onto, and forgets.

| Law | Definition | Apply it | Ask | Violation looks like |
|---|---|---|---|---|
| **Miller's Law** | Working memory holds about 7±2 items at once | Chunk long lists/forms/nav into groups of ≤7 with a heading per group | Could a user hold everything on screen in their head at once, or does it need chunking? | An 18-field form with no section grouping |
| **Serial Position Effect** | Items at the start and end of a sequence are remembered best; the middle fades | Put the most important nav items, list entries, or options first or last, never buried mid-list | Is the most important item positioned where it'll actually be remembered? | The most-used destination sits 7th in a 12-item menu |
| **Von Restorff Effect** | The one item that visually differs from its surroundings is the one most noticed and remembered | Make exactly one thing per view stand out — the primary action, the critical alert | Is there exactly one obviously-different element, or does everything compete equally? | Five same-weight buttons in a row, none of which reads as "the one to click" |
| **Zeigarnik Effect** | Unfinished or interrupted tasks are remembered better than completed ones — open loops nag at attention | Give every flow (especially modals) one unambiguous way to finish or exit; don't leave the loop open | Does the user always know how to close out this task, or are they left holding an open loop? | A modal with no visible close button and no clear "done" state |
| **Selective Attention / Cognitive Load** | Attention is a limited resource; every simultaneous decision or visual competes for the same budget | Reduce what's on screen to what the current task needs; move the rest behind disclosure | How many things is this view asking the user to notice or decide on at once? | A dashboard with twelve equally-weighted metrics and no indication which one matters right now |

## Interaction cost

What it costs the user, physically and cognitively, to act.

| Law | Definition | Apply it | Ask | Violation looks like |
|---|---|---|---|---|
| **Fitts's Law** | Time to reach a target is a function of its size and distance — bigger, closer targets are faster and more reliably hit | Frequent/important targets are large and close; minimum 44x44px hit area with breathing room from neighbors | Is the target big enough and close enough for how often/urgently it's used? | A tiny 12px icon is the only way to trigger a common action |
| **Hick's Law** | Decision time increases (roughly logarithmically) with the number and complexity of choices offered | Limit visible choices to what the user must decide right now; move the rest into a menu or later step | Could the number of visible options be cut without losing a needed choice? | Six top-level actions of equal visual weight forcing the user to read all of them before acting |
| **Jakob's Law** | Users spend most of their time on other products and expect yours to behave the same way | Default to the platform-conventional pattern (nav position, gesture, control shape) unless there's a specific reason to deviate | Would a first-time user already know how this works from using other products? | A custom edge-swipe navigation pattern on a desktop web app that no other product uses |
| **Doherty Threshold** | User engagement and productivity rise sharply when system response is under ~400ms | Give feedback (inline validation, optimistic UI, a loading indicator) within ~400ms of any action | Does the user see a response to their action before ~400ms passes? | Form validation that only reports errors after a full-page submit-and-reload |
| **Postel's Law** | Be liberal in what you accept from users, conservative in what you show or send back | Accept flexible input formats (dates, phone numbers, casing) and normalize server-side rather than rejecting on formatting | Is valid user input being rejected for a formatting reason the system could just normalize? | A phone field that rejects "(555) 123-4567" because it wants "5551234567" |

## Motivation & complexity

What keeps the user going, and who absorbs the system's inherent complexity.

| Law | Definition | Apply it | Ask | Violation looks like |
|---|---|---|---|---|
| **Goal-Gradient Effect** | Motivation to finish a task increases as the perceived distance to the goal shrinks | Show progress toward completion (steps done, percent, items remaining) on any multi-step flow | Does the user see how close they are to done, or just a static "in progress"? | An upload/import spinner with no indication of how much is left |
| **Peak-End Rule** | An experience is judged mostly by its most intense moment and how it ends, not its average | Give deliberate design attention to error states, empty states, and the last step of any flow | Is the ending of this flow (success, error, or empty state) as considered as the middle? | A generic "Error 500" as the last thing the user sees after filling out a long form |
| **Tesler's Law** | Every process has a fixed amount of irreducible complexity — the only question is whether the system or the user absorbs it | Push complexity into the system (defaults, normalization, smart handling) rather than onto the user (more fields, more steps, more decisions) | Is the user being asked to do work the system could reasonably do for them? | Asking the user to manually pick a timezone the system could already infer |
| **Aesthetic-Usability Effect** | Users perceive more aesthetically pleasing designs as easier to use, even when usability is unchanged | Don't rely on polish to excuse a confusing flow — fix the flow, then let visual quality reinforce trust | Would this still work if it were unstyled — or is aesthetics hiding a real usability gap? | A confusing multi-step checkout redeemed only by nice visual polish, not by being any clearer |
| **Occam's Razor / Pareto Principle** | The simplest solution that satisfies the requirement is usually correct; a small fraction of features/paths account for most usage | Design and optimize for the common path first; keep rare paths simple even if less polished | Is this solving the common case simply, or has it been generalized for edge cases nobody hits often? | A configuration screen with 30 rarely-touched options given the same prominence as the 3 everyone uses |

## Required output

When used as a review, return:

```markdown
## Design Laws Review: [element/screen name]

**Verdict:** Pass | Needs revision | Blocked

### Laws checked
- [Law] — ✅ / ⚠️ / ❌ — one-line note

### Issues found
1. **[Severity] [Law]** Description of what's wrong
   - Recommended fix:

### What works
- ...
```

## Principles

- **Name the mechanism, not the vibe** — every note here cites a specific law; if you can't name it, it's a style opinion, not a finding
- **Perception before attention before interaction before motivation** — fix grouping/hierarchy first; polish and motivation-layer details come last
- **Laws are defaults, not mandates** — deviate deliberately and say why, don't deviate by accident
- **One law at a time** — sweeping a whole screen against a single law catches things an element-by-element review misses, and vice versa
