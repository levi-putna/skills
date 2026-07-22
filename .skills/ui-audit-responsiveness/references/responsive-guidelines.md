# Responsive Layout Guidelines

Maintained checklist for the `ui-audit-responsiveness` skill. Edit this file
directly to add, remove, or reword guidelines - the skill re-reads it on
every run, so changes take effect immediately without touching `SKILL.md`.

Each item is written as a Do/Don't so it's checkable against a real page,
not a general principle. Add a short "why" only when the reason isn't
obvious from the item itself.

## 1. Breakpoints & fluid layout

- **Do** write layout mobile-first: base styles target the narrowest
  viewport, `min-width` queries add complexity as the viewport grows.
  Max-width-only overrides tend to leave small screens as an afterthought.
- **Do** use fluid grids and flexible widths (`%`, `fr`, `flex`, `grid`) as
  the default, not fixed pixel widths for containers or columns.
- **Do** use `clamp()` for type and spacing that needs to scale smoothly
  between breakpoints, instead of a hard jump at each one.
- **Do** reach for container queries when a component's layout should
  depend on the space it's actually given (e.g. a card in a sidebar vs. a
  card in a full-width grid), not just the viewport as a whole.
- **Don't** hardcode pixel widths/heights on containers, images, or text
  blocks that can end up wider than a narrow viewport - this is the most
  common cause of horizontal scroll on mobile.
- **Don't** rely on a single desktop layout with `overflow-x: auto` as the
  mobile fallback unless the content is genuinely a horizontal-scroll
  pattern (a carousel, a wide table with sticky first column) - it's a
  workaround, not a responsive layout.
- **Do** confirm there is a `<meta name="viewport" content="width=device-width, initial-scale=1">` tag (or Next.js `viewport` export equivalent) on every page - without it, mobile browsers render at a fake desktop width and none of the responsive CSS applies as intended.

## 2. Navigation & wayfinding

- **Do** keep primary navigation reachable in one tap/click at every
  breakpoint, even when it's collapsed into a menu.
- **Do** collapse navigation into a hamburger menu, drawer, or bottom tab
  bar when there isn't room to show every item, rather than shrinking items
  until they're unreadable or letting the nav wrap awkwardly.
- **Do** use navigation patterns people already know (hamburger menu, tab
  bar, bottom sheet). Novel navigation gestures have to be discovered before
  they can be used, and mobile is the worst place to spend that learning
  cost.
- **Do** pair a persistent primary call-to-action (or sticky bottom bar) with
  a collapsed hamburger menu for secondary navigation, rather than burying
  the one action that matters most inside the same collapsed menu as
  everything else.
- **Don't** hide navigation items that are frequently used or
  conversion-critical behind a second-level menu just to declutter - a
  collapsed hamburger menu already costs discoverability; don't compound it
  with extra nesting for the items people need most.
- **Don't** change the navigation's structure or item order between
  breakpoints - people build a mental map of where things live; a reshuffled
  mobile nav breaks that map instead of just resizing it.

## 3. Content priority & progressive disclosure

This is the core judgement call of the audit: what has to stay visible on a
small screen, and what can move behind a menu, drawer, accordion, or "show
more" toggle.

- **Keep visible on mobile:** the reason the user is on this page (the
  primary content or task), the primary action for that task, and anything
  required to complete it without extra navigation.
- **Good candidates to collapse:** secondary/related content, filters and
  sort controls (as a "Filters" toggle or bottom sheet), metadata that
  supports but isn't the content itself, rarely-used settings, and anything
  that exists to aid discovery of other pages rather than the current one.
- **Do** use progressive disclosure deliberately - show the minimum needed
  to act or understand the page, and reveal the rest on demand - rather than
  either cramming everything onto the screen or hiding things that should
  stay visible.
- **Do** default multi-panel desktop layouts (e.g. list + detail,
  sidebar + content) to a single-column, one-thing-at-a-time flow on mobile,
  with clear back navigation between panels.
- **Don't** simply hide desktop content on mobile with `display: none`
  and call it responsive if that content was load-bearing (a required form
  field, the primary CTA, a warning/error state) - hidden-but-required
  content is a broken flow, not a simplified one.
- **Don't** move an action behind a toggle or "more" menu just because
  screen space is tight if that action is the one most people on this page
  are trying to take - shrink or restyle it instead of hiding it.

## 4. Touch targets & input

- **Do** size interactive elements at minimum 44×44px (Apple HIG) where
  practical, and never smaller than the WCAG 2.2 (SC 2.5.8) floor of
  24×24px with at least 8px of spacing between adjacent targets.
- **Do** give tap targets real spacing from each other, especially in
  toolbars, icon rows, and table row actions - cramped icon-only controls
  are the most common source of mis-taps.
- **Do** provide a touch-usable equivalent for anything that currently only
  works on `:hover` (tooltips, hover-reveal menus, hover-only action
  buttons on a card/row).
- **Don't** rely on hover states to reveal information or actions with no
  tap/focus equivalent - there is no hover on a touchscreen.
- **Don't** place primary actions where a thumb can't comfortably reach them
  on a phone (e.g. top-right corner of a tall page); keep frequent actions
  reachable in the lower two-thirds of the viewport on mobile where the
  layout allows it.

## 5. Typography & readability

- **Do** keep body text at a minimum ~16px on mobile - anything smaller
  forces zooming and hurts legibility, and Safari will auto-zoom on focus
  for form inputs below 16px, which itself is a usability problem.
- **Do** keep line length readable (roughly 45-75 characters) at every
  breakpoint - full-bleed text on a wide viewport with no max-width is as
  much a responsive issue as overflow on a narrow one.
- **Don't** truncate headings or labels with `text-overflow: ellipsis` as a
  substitute for letting them wrap - only truncate when the full text is
  genuinely available elsewhere (a tooltip, the detail view).

## 6. Images & media

- **Do** use responsive image techniques (`srcset`/`sizes`, or the
  framework's image component) so a phone downloads a phone-sized image, not
  a scaled-down desktop asset.
- **Do** set explicit `width`/`height` (or `aspect-ratio`) on images and
  embeds so the layout doesn't jump as they load (cumulative layout shift).
- **Do** lazy-load offscreen images and embeds, especially below-the-fold
  content on long mobile pages.
- **Don't** let fixed-size embeds (maps, videos, iframes) overflow a narrow
  viewport - wrap them in a responsive container (`aspect-ratio` + `max-width:
  100%`) instead of a hardcoded pixel size.

## 7. Tables & dense data

- **Do** give wide tables an explicit small-screen treatment: horizontal
  scroll with a visible scroll affordance, a collapsed/priority-column view,
  or reflowing each row into a stacked card.
- **Do** keep the most important column(s) visible/pinned (e.g. a sticky
  first column) if the table must scroll horizontally, so a scrolled row is
  still identifiable.
- **Don't** let a data-dense table simply overflow the viewport with no
  affordance indicating it's scrollable - people will assume the table is
  just cut off.

## 8. Forms

- **Do** stack form fields in a single column on mobile - multi-column
  desktop forms should collapse rather than shrink each column past
  usability.
- **Do** use the right `inputmode`/`type` (`email`, `tel`, `numeric`, etc.)
  so mobile keyboards match the expected input.
- **Do** keep validation errors visible next to their field after the
  layout reflows - an error anchored to a desktop-only position can end up
  detached from its field on mobile.
- **Don't** rely on placeholder text as a label substitute - it disappears
  on focus/input, which is worse on mobile where the on-screen keyboard
  already eats vertical space and re-orienting is costlier.

## 9. Performance on mobile

- **Do** treat mobile as the default performance target, not desktop -
  Google's indexing and ranking evaluate the mobile version of a page, and
  mobile networks/devices are usually the slower, more constrained case.
- **Do** budget and check Core Web Vitals (LCP, CLS, INP) against the
  mobile-simulated profile, not just desktop.
- **Don't** ship desktop-only heavy assets (large hero images/video,
  unthrottled animation) to mobile viewports without a lighter variant or a
  `prefers-reduced-motion`/viewport-based fallback.

## 10. Checking a page

When verifying a page live rather than by code review alone, check at
minimum:

| Viewport | Approx. width | What to look for |
|---|---|---|
| Mobile | ~375px | Single-column reflow, nav collapsed correctly, no horizontal scroll, core content and primary action visible without extra taps |
| Tablet | ~768px | Layout transition point - no orphaned in-between states (e.g. nav collapsed but content still in a cramped multi-column grid) |
| Desktop | ~1280px+ | Full layout, but confirm content doesn't over-stretch (unbounded line length, oversized empty space) |

Also check: rotating mobile to landscape doesn't break the layout, and safe
areas (notches, home indicator) aren't covering content on devices that need
`env(safe-area-inset-*)`.
