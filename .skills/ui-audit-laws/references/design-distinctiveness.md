# Design Distinctiveness (aesthetic audit checklist)

Editable catalogue of aesthetic/creative-direction checks used by the `ui-audit-laws` skill.
Adapted for auditing (not generating) from the [Frontend Design skill](https://github.com/davila7/claude-code-templates/blob/main/cli-tool/components/skills/creative-design/frontend-design/SKILL.md) (davila7/claude-code-templates).

**How to maintain this file**
- Add, remove, or reword items freely — keep the `DESIGN-xxx` IDs stable once referenced in reports.
- Each item has: `id`, category, name, definition, and `audit-for` (what to look for on a page).
- Mark an item `enabled: false` to skip it in audits without deleting it.
- Severity hint: `major` or `minor`. Nothing here is `blocking` — a generic-looking interface is a missed opportunity, not a broken one. Interaction/accessibility failures in `ui-laws.md` and `agents-rules.md` still take priority.
- **Applicability matters most for this file.** These checks are for brand-facing, marketing, consumer, or portfolio surfaces meant to carry a designed point of view. Mark every item **N/A** for utility-first surfaces — internal admin tools, dense dashboards, developer tooling, enterprise back-office software — where restrained, unobtrusive, "get out of the way" design is the *correct* choice, not a flaw. Restraint and genericness are different things: a deliberately minimal interface that still passes these items (a considered type scale, an intentional neutral palette) is not the same as a default, unconsidered one.

---

## Aesthetic Direction

### DESIGN-001 — Committed Point of View
- **enabled:** true
- **severity:** major
- **definition:** A strong interface commits to a legible aesthetic direction (e.g. brutally minimal, maximalist, retro-futuristic, organic, luxury/refined, playful, editorial, brutalist/raw, art deco, soft/pastel, industrial) rather than defaulting to an undirected, generic look.
- **audit-for:** Can you name the direction in one phrase? If the honest answer is "default framework/template starter with no direction," that's a fail. A clearly minimal or restrained page that reads as intentional still passes.

### DESIGN-002 — Memorable Differentiation
- **enabled:** true
- **severity:** minor
- **definition:** At least one deliberate choice makes the interface memorable rather than interchangeable with any other page built on the same stack.
- **audit-for:** One standout element (a distinctive type pairing, a signature motion moment, an unusual layout choice, a considered illustration/texture style) — not everything trying to stand out at once, and not nothing.

## Typography

### DESIGN-003 — Distinctive Type Pairing
- **enabled:** true
- **severity:** minor
- **definition:** Typography choices support the aesthetic direction rather than defaulting, unconsidered, to whatever the framework/template shipped with.
- **audit-for:** On brand/marketing surfaces, a distinctive display/heading face paired deliberately with a readable body face. Flag a page that uses only the framework default (commonly Inter, Roboto, Arial, or unmodified system-ui) with no pairing or weight/scale consideration *when the page is meant to carry brand personality*. Do not flag Inter/system-ui/Roboto on utility surfaces (dashboards, internal tools) where a neutral, highly legible face is the right call.

## Colour & Theme

### DESIGN-004 — Token-Driven Cohesion
- **enabled:** true
- **severity:** major
- **definition:** Colour is driven by a small set of design tokens/CSS variables applied consistently, not scattered hard-coded hex/rgb values invented per component.
- **audit-for:** Search for repeated raw colour literals across components vs a shared token/variable set; inconsistent near-duplicate colours (e.g. three slightly different blues) doing the same semantic job.

### DESIGN-005 — Dominant-Accent Discipline (avoid clichés)
- **enabled:** true
- **severity:** major
- **definition:** A considered palette has a clear dominant/neutral base with a deliberate, sparingly-used accent (see LAW-023, 60-30-10) — and avoids the specific colour clichés that read as generic/AI-generated on sight.
- **audit-for:** The single most common tell: a purple/violet gradient hero on a plain white/light background with no other distinguishing choice. Also flag: pastel-gradient blobs behind a glassmorphism card as the only background treatment, and palettes where every colour competes for attention with none reading as dominant.

## Motion

### DESIGN-006 — Purposeful, Orchestrated Motion
- **enabled:** true
- **severity:** minor
- **definition:** Motion is deliberate: either one well-orchestrated moment (e.g. a staggered page-load reveal) or targeted micro-interactions that clarify cause/effect — not scattered generic transitions applied uniformly to everything, and not a completely static, motion-free experience on a page meant to feel crafted.
- **audit-for:** Presence of any considered entrance/hover/scroll motion on brand surfaces; absence of default "everything fades in the same way" component-library motion with no orchestration (stagger, sequencing, easing that matches the change). This checks *intent*, not technical correctness — see `agents-rules.md`/`web-interface-guidelines.md` Animation sections for the compositor-friendly/`prefers-reduced-motion` technical rules, which still apply on top of this.

## Spatial Composition

### DESIGN-007 — Non-Generic Layout
- **enabled:** true
- **severity:** minor
- **definition:** Layout shows a deliberate compositional choice (asymmetry, overlap, diagonal flow, grid-breaking elements, controlled density or generous negative space) rather than defaulting unconsidered to the same predictable template.
- **audit-for:** The specific cliché to flag: a centred hero + three equal-width rounded-corner shadow "feature cards" + centred footer, with no other compositional idea, presented as if it were a finished creative direction rather than a placeholder. This is distinct from LAW-013/014/015 (Gestalt grouping) — those check whether grouping reads *correctly*; this checks whether the composition was *considered* at all.

## Backgrounds & Visual Detail

### DESIGN-008 — Atmosphere & Depth
- **enabled:** true
- **severity:** minor
- **definition:** Backgrounds and surfaces build some atmosphere/depth (gradient mesh, noise/grain, geometric pattern, layered transparency, deliberate shadow, decorative border) appropriate to the direction, rather than uniformly flat solid colour with zero textural interest on a surface meant to feel designed.
- **audit-for:** On marketing/brand surfaces, any deliberate background treatment beyond a flat single colour. Flat is fine and often correct on utility surfaces (N/A there) or when flatness is itself the stated direction (e.g. "brutally minimal") — the fail case is flat-by-default with no other sign of intent anywhere else on the page either.

---

## AI-slop tells (evidence bank)

Concrete, commonly-cited signs an interface looks AI-generated/default rather than designed. Cite these as evidence when failing the items above — they are not separately scored, to avoid double-counting the same issue under multiple IDs.

- Purple-to-blue (or purple-to-pink) gradient hero on a white/light background, with no other distinguishing choice.
- Every heading, body, and UI label rendered in the same unmodified default font (commonly Inter, Roboto, or system-ui) with no pairing or hierarchy beyond size.
- A centred hero followed by exactly three equal-width `rounded-xl` cards with a soft drop shadow, repeated as the entire page structure.
- Glassmorphism cards over a blurred pastel-gradient blob background as the sole atmospheric treatment.
- Generic stock-style icon set (outline icons in circles) with no relationship to the product's actual content or voice.
- Zero motion anywhere on a page whose direction calls for delight, or the opposite: identical fade/slide transitions applied uniformly to every element with no sequencing.
- Copy and layout that could be pasted onto an unrelated product with zero changes needed — nothing on the page is specific to this product's content or audience.
