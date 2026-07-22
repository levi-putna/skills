# Reusable components across productions

`shared/` inside one production (Gate 4) solves continuity **within**
that video - a card defined once and reused by every scene in it. This
doc covers the layer above that: a **cross-production** library so an
intro/outro, a chart primitive, or a house cursor style doesn't get
rewritten from scratch (and drift slightly) every time a new production
starts.

## Two tiers of `shared/`

```
remotion/
  shared/                          # cross-production - created once, project-wide (Gate 0)
    theme.ts                       # base brand tokens: palette, type, base motion presets
    Intro.tsx                      # parameterised open (title/logo/duration as props)
    Outro.tsx                      # parameterised close (CTA/logo as props)
    Cursor.tsx                     # house pointer style, if the project does UI walkthroughs often
    charts/
      LineChart.tsx                # themed visx-based chart primitives (see data-visualization.md)
      BarChart.tsx
      Counter.tsx
  productions/{slug}/
    shared/
      theme.ts                     # production tokens - imports & extends remotion/shared/theme.ts
      {ProductionSpecificComponent}.tsx   # only used by this one production
    ...
```

- **`remotion/shared/`** - project-wide, survives across every
  production. Only things that are genuinely reused, or obviously will
  be (an intro/outro built to the same brand spec every time), live here.
- **`productions/{slug}/shared/`** - exactly what Gate 4 already
  describes: this production's own recurring elements, plus its
  `theme.ts` extending the base tokens above.

## Theme extension, not duplication

A production's `theme.ts` should **import and extend** the base tokens,
not redefine them from scratch:

```ts
// remotion/shared/theme.ts - project-wide base
export const baseColors = {
  bg: "#0B0B0E",
  fg: "#F5F5F7",
  accent: "#5B8CFF",
} as const;

export const baseMotion = {
  enter: { damping: 200 },
  emphasis: { damping: 20, stiffness: 200 },
} as const;
```

```ts
// productions/{slug}/shared/theme.ts - this production's tokens
import { baseColors, baseMotion } from "../../shared/theme";

export const colors = {
  ...baseColors,
  highlight: "#FFD166", // production-specific accent, layered on top of brand base
} as const;

export const motion = baseMotion;
```

If the project already has its own design-token/component library
outside `remotion/` (per Gate 4 step 6), `remotion/shared/theme.ts` is the
right place to re-export from it once, so every production inherits it
through the same extension pattern rather than each one wiring its own
re-export.

## Using the shared library (Gate 4, step 1)

Before writing anything production-specific, check `remotion/shared/`
(components and `charts/`) for something that already does the job.
Reusing it - even if it needs a new prop to fit this scene - is always
preferred over writing a near-duplicate in the production's own
`shared/`.

## Promoting a component to the shared library

Only promote when there's a **real, current** second production that
needs it - not speculatively "this might be useful later" (same
reuse-don't-preempt logic as the rest of this skill). When that happens:

1. Move the file from `productions/{slug}/shared/` to `remotion/shared/`
   (or `remotion/shared/charts/` for a chart primitive).
2. Generalise it: replace anything production-specific (copy, a
   hardcoded value) with a prop. It should read as a general-purpose
   component with sane defaults, not a copy of one production's exact
   scene.
3. Update the original production to import it from the new shared
   location instead of its own `shared/`.
4. Note the promotion in that gate's presentation (what moved, why, and
   which productions now depend on it) so it's a visible, reviewed
   change, not a silent refactor.

**Never fork a shared component per-production** "just this once" -
if a one-off variation is genuinely needed, add a prop to the shared
component instead of duplicating the file. A shared component that's
been forked twice has stopped being a shared component.

## Prime shared-library candidates

Call these out explicitly at Gate 4 if the project is going to produce
more than one video - they're the components most worth building
generically from the start rather than waiting for a second production
to force the promotion:

- **Intro/outro** - a branded open/close usually wants to look identical
  across every video for the same product; build it parameterised
  (title/logo/CTA as props) in `remotion/shared/` from the first
  production rather than treating it as this production's one-off.
- **Chart/graph primitives** - see
  [data-visualization.md](data-visualization.md). A small set of themed
  chart components (line, bar, counter) in `remotion/shared/charts/`
  keeps every video's data visuals looking like the same design system
  instead of each production inventing its own chart style.
- **A house cursor/pointer** for any project that regularly does UI
  walkthrough-style explainers.
- **Camera focus / punch-in helper** (`CameraFocus` or equivalent) once
  the project does feature walkthroughs or typing/click demos often - see
  [camera-zoom-focus.md](camera-zoom-focus.md). Keep focus presets in the
  shared theme; don't re-invent ease-in/hold/ease-out per production.
- **Natural typing helper** - copy
  [assets/typing.ts](../assets/typing.ts) into
  `productions/{slug}/shared/typing.ts`, then promote to
  `remotion/shared/typing.ts` once a second production needs it - see
  [natural-typing.md](natural-typing.md). Default ~35 chars/sec via
  `typedText`; always character-wise, never reinvented per scene.

Everything else - a scene-specific card, a one-off diagram - stays in
that production's own `shared/` until (and unless) a second production
actually needs it too.
