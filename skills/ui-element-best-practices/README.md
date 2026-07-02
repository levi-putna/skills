# UI Element Best Practices

**Element-level UX best practices, grounded in the Laws of UX (lawsofux.com).**

Where `ui-ux-best-practices` reviews a whole screen or flow, this skill zooms in on a single element — a button, a form, a modal, a table — and checks it against a specific, named UX law instead of a style opinion.

## When to use

- Building or extending a single component (buttons, forms, nav, modals, cards, tables, states, notifications, tooltips)
- Reviewing an AI-generated element that "looks fine" but has a specific, nameable UX problem
- During `component-development`, before marking an element complete
- As supporting detail when `ui-ux-best-practices` flags a specific element on a composed screen

## Grounded in the Laws of UX

Every rule below cites a specific law (Fitts's Law, Hick's Law, Jakob's Law, Miller's Law, Doherty Threshold, and others — see [lawsofux.com](https://lawsofux.com)) rather than a subjective preference. If a rule can't be traced to a mechanism, it isn't in this skill.

## Without vs. with — side by side

### Buttons & CTAs

*Laws: Fitts's Law (target size/distance), Hick's Law (choice count), Von Restorff Effect (the one that stands out gets noticed)*

<table>
<tr><th>❌ Without this skill</th><th>✅ With this skill</th></tr>
<tr valign="top">
<td>

```html
<div class="actions">
  <button class="btn">Save</button>
  <button class="btn">Cancel</button>
  <button class="btn">Delete</button>
  <button class="btn">Duplicate</button>
  <button class="btn">Archive</button>
</div>
```
Five same-weight buttons. The user has to read all five to find "Save." Delete looks identical to Save.

</td>
<td>

```html
<div class="actions">
  <button class="btn btn-primary">Save changes</button>
  <button class="btn btn-ghost">Cancel</button>
  <MoreActionsMenu>
    <MenuItem>Duplicate</MenuItem>
    <MenuItem>Archive</MenuItem>
    <MenuItem danger>Delete</MenuItem>
  </MoreActionsMenu>
</div>
```
One obvious primary action (Von Restorff), two visible choices instead of five (Hick's Law), destructive action moved out of the direct line of a mis-tap.

</td>
</tr>
</table>

### Forms & Inputs

*Laws: Miller's Law (7±2 chunking), Doherty Threshold (<400ms feedback), Postel's Law (accept flexible input)*

<table>
<tr><th>❌ Without this skill</th><th>✅ With this skill</th></tr>
<tr valign="top">
<td>

```html
<form>
  <!-- 18 fields, no grouping -->
  <input placeholder="First name">
  <input placeholder="Last name">
  <input placeholder="Email">
  <input placeholder="Phone">
  <!-- ...14 more ... -->
  <button>Submit</button>
</form>
```
One long list. Validation only runs on submit, so a typo in field 3 isn't caught until the very end.

</td>
<td>

```html
<form>
  <fieldset>
    <legend>Contact details</legend>
    <input label="First name">
    <input label="Last name">
    <input label="Email" onBlur={validate} />
  </fieldset>
  <fieldset>
    <legend>Shipping address</legend>
    ...
  </fieldset>
  <button>Continue</button>
</form>
```
Fields chunked into groups of ≤7 (Miller's Law) with per-group headings. Validation fires on blur, well under 400ms (Doherty Threshold), so errors surface immediately, not at the end.

</td>
</tr>
</table>

### Navigation & Menus

*Laws: Jakob's Law (familiar patterns), Miller's Law (item count), Serial Position Effect (first/last recalled best)*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Pattern** | Custom edge-swipe nav on desktop that no other product uses | Standard top bar or left rail — matches what users already know from every other app |
| **Item count** | 12 flat top-level items | 6 top-level items; the rest live in submenus |
| **Ordering** | Most-used destination buried 7th in the list | Most-used destinations placed first or last (Serial Position Effect) |
| **Current state** | No visual indication of where the user is | Active item always visibly marked |

### Modals & Dialogs

*Laws: Zeigarnik Effect (unclear exits nag at users), Von Restorff Effect (isolation), Tesler's Law (don't offload complexity unnecessarily)*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Exit path** | No visible close button; only way out is clicking outside and hoping | Explicit Cancel/Close plus Escape key, always visible |
| **Purpose** | A full secondary page's worth of content crammed into a modal | Modal reserved for a single focused task; longer content gets its own view |
| **Confirmation copy** | "Are you sure?" | "Delete 12 items? This can't be undone." |
| **Focus handling** | Focus stays on the page behind the modal | Focus moves into the modal on open, traps while open, returns to the trigger on close |

### Cards & Content Groups

*Laws: Law of Common Region, Law of Proximity, Law of Similarity*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Boundaries** | Every block of content wrapped in its own bordered, shadowed card — cards inside cards | A boundary only appears where it communicates a real grouping; page background does the rest |
| **Field spacing** | Uniform gap between every element, inside and outside the card | Fields inside a card sit closer together than the gap to the next card (Law of Proximity) |
| **Consistency** | Each "user card" instance has fields in a different order | Same card type always shows fields in the same structure (Law of Similarity) |

### Tables & Data Grids

*Laws: Law of Prägnanz (simplify), Miller's Law (limit visible columns), Law of Uniform Connectedness*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Columns** | All 15 available columns shown at once with heavy gridlines | Task-relevant columns shown by default; rest behind a column picker |
| **Related data** | Value and its unit in separate, unconnected columns | Value and unit visually grouped so they read as one unit |
| **Mobile** | Font shrunk until it barely fits, table still scrolls in both directions | Deliberate mobile strategy: frozen key column, stacked cards, or explicit priority columns |

### Empty, Loading, and Error States

*Laws: Doherty Threshold (perceived performance), Peak-End Rule (endings are remembered), Aesthetic-Usability Effect*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Empty** | Blank area, or "No data" | "No projects yet — create your first one" with the create action right there |
| **Loading** | Generic spinner regardless of wait length | Skeleton matching the eventual layout for anything over ~1s |
| **Error** | "Error 500" | "Couldn't load your projects. [Retry]" |

### Notifications & Toasts

*Laws: Von Restorff Effect, Serial Position Effect, Goal-Gradient Effect*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Frequency** | A toast fires for every autosave, every few seconds | Toasts reserved for information the user didn't already expect |
| **Position** | Appears in a different corner depending on which feature triggered it | Fixed position every time, so users learn where to glance |
| **Progress** | Static "Uploading..." with no indication of how much is left | Progress shown ("3 of 10 files"), so proximity to done increases motivation to wait (Goal-Gradient Effect) |
| **Destructive undo** | No way to undo a destructive action once toasted | Undo affordance in the toast for reversible actions |

### Tooltips & Progressive Disclosure

*Laws: Fitts's Law (target size), Doherty Threshold (delay), Tesler's Law (push complexity to where it's needed)*

| | ❌ Without this skill | ✅ With this skill |
|---|---|---|
| **Trigger size** | Tiny 12px "?" icon | Hit target meets the same 44x44px minimum as any control |
| **Access** | Hover-only, invisible to keyboard users | Keyboard/focus-triggered equivalent always present |
| **Content** | Repeats the visible label verbatim | Adds information the label doesn't already convey |
| **Advanced options** | All shown to every user by default | Disclosed on demand (accordion, "Show more") |

## Fits with

1. `design-system` defines the tokens (spacing, colour, radius) these rules assume
2. `component-development` builds the element — check it against the matching section here before marking it complete
3. `component-testing` verifies states and accessibility mechanically
4. `ui-ux-best-practices` reviews the composed screen once elements are integrated

## Example prompt

```text
Review this modal component against UI element best practices — focus states, exit path, and whether it should be a modal at all.
```

## Install

```bash
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill ui-element-best-practices --global
```
