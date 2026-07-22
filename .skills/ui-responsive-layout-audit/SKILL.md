---
name: ui-responsive-layout-audit
id: 09436110-f2e2-4948-ab05-98e60c894a47
version: 1.0.0
author: Levi Putna
repo: https://github.com/levi-putna/skills
description: >-
  Audit one or more pages against responsive-design best practice: how the
  layout behaves across breakpoints, which features stay visible on mobile
  vs. collapse into a menu/drawer/toggle, touch target sizing, and other
  small-screen usability issues. Use when asked to check, review, or audit a
  page's responsiveness or mobile layout, to confirm a page is "mobile
  friendly" or "responsive," or to sweep several pages/routes for
  responsive-layout issues at once. Not a from-scratch mobile-first design
  process and not a general accessibility or visual-QA audit.
---

# Responsive Layout Audit

Reviews an already-built page (or a set of pages) against a maintained
checklist of responsive-design do's and don'ts, and reports back concrete
issues with locations and fixes. This is a compliance pass, not a redesign:
the goal is to confirm the page holds up across breakpoints, not to design it
mobile-first from scratch.

The checklist itself lives in a separate, human-maintained reference doc:

```
.skills/ui-responsive-layout-audit/references/responsive-guidelines.md
```

**Always re-read that file fresh at the start of every audit** rather than
relying on memory of a previous run - it's the source of truth and the user
edits it independently of this skill. Don't inline its content into this
file or let it go stale in a summary.

## Workflow

1. **Resolve the target page(s).** Map what the user names (a URL path, a
   page title, "the homepage," "all the developer docs pages") to the App
   Router route file(s) that render it (location varies by project - could
   be `app/`, `src/app/`, etc). If the user says "the whole site" or names
   several pages, list every route file you'll audit before starting.

2. **Load the guidelines.** Read
   `references/responsive-guidelines.md` in full. It's organised by topic
   (navigation, content priority, touch targets, typography, images,
   tables, forms, performance) each with Do / Don't items - that structure
   is the checklist you audit against.

3. **Single page: audit directly.** For one page:
   - Read the route file and the primary components it renders.
   - Classify what's on the page: the core feature(s) the page exists for
     (the thing a user came to do or read), primary actions, then secondary
     or supporting content (related links, filters, metadata, secondary
     actions).
   - Check actual responsive implementation: breakpoint utility classes
     (Tailwind `sm: md: lg: xl:` or equivalent), any fixed pixel
     widths/heights that would overflow a narrow viewport, `overflow-x`
     usage, flex/grid direction that doesn't collapse on small screens,
     hover-only affordances with no touch equivalent, tables with no
     narrow-viewport treatment, tap target sizes on interactive elements.
   - Weigh each piece of content against the guidelines' content-priority
     section: does it need to stay visible on mobile, or is it a good
     candidate to move behind a menu, drawer, accordion, or "more" toggle?
     Flag anything load-bearing that's currently hidden, and anything
     secondary that's currently competing for space on small screens.
   - If static review is inconclusive on a specific point (e.g. whether a
     layout actually reflows correctly), and a dev server is available in
     this environment, load the page at mobile (~375px), tablet (~768px),
     and desktop (~1280px+) widths to confirm before flagging or clearing
     it. Prefer code review first; only reach for a browser to settle a
     genuine doubt, not as a blanket step for every page.

4. **Multiple pages: parallelize with agents.** When auditing more than one
   page, spawn one agent per page (Explore for a quick code-only pass,
   general-purpose if it needs to run a dev server and check the page live),
   launched together in a single message so they run in parallel. Give each
   agent: the page's route file path, the full content of
   `references/responsive-guidelines.md` (agents don't share your context,
   so paste the checklist in rather than pointing at the path), and the same
   classification/check steps from step 3. Ask each to return its findings
   in the format from step 5. Collect all results and merge into one report.
   Don't spawn agents for a single-page audit - do that inline.

5. **Report findings.** For every issue found, give:
   - **Location** - file and line/component.
   - **What's wrong** - concrete description, not just "not responsive."
   - **Guideline violated** - which line item from the reference doc.
   - **Severity** - `blocker` (breaks or hides core functionality on
     mobile), `major` (usable but degraded - cramped, mis-tappable, content
     fighting for space), `minor` (polish - spacing, minor overflow on
     unusual widths).
   - **Recommendation** - the specific fix, not a vague direction.

   Group the report by page, then by severity (blockers first). Close with
   a short punch list: total issues per page, and which pages are clean.
   Don't restate the entire guidelines doc in the report - only the items
   that were actually violated.

## Boundaries

- This audits layout and content-priority behaviour across breakpoints. It
  is not a full accessibility audit (contrast, ARIA, screen readers), not
  pixel-perfect visual QA, and not a design-system consistency check. If an
  issue found during the audit belongs to one of those categories, mention
  it briefly but don't expand the audit's scope to cover it.
- Don't edit `references/responsive-guidelines.md` as part of running an
  audit. The user maintains it separately; only touch it if they explicitly
  ask you to update the guidelines themselves.
- Don't fix the code unless the user asks you to act on the findings -
  default to reporting first, since severity and priority calls (what's
  truly "core" on a given page) are often a product judgement the user
  should confirm.
