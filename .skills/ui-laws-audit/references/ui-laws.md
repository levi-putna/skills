# UI/UX Laws (audit checklist)

Editable catalogue of research-backed UX laws used by the `ui-laws-audit` skill.
Source inspiration: [Twisted Brackets — Laws of UX](https://www.twistedbrackets.com/ui-laws).

**How to maintain this file**
- Add, remove, or reword laws freely — keep the `LAW-xxx` IDs stable once referenced in reports.
- Each law has: `id`, category, name, definition, and `audit-for` (what to look for on a page).
- Mark a law `enabled: false` to skip it in audits without deleting it.
- Severity hint: `blocking` (fail the page if broken), `major`, or `minor`.

---

## Interaction & Speed

### LAW-001 — Fitts's Law
- **enabled:** true
- **severity:** blocking
- **definition:** The time to acquire a target is a function of the target's size and distance — bigger, closer targets are faster and easier to hit.
- **audit-for:** Primary actions ≥24px hit area (mobile ≥44px); important controls are not tiny or far from related context; expand hit area when visual size is small.

### LAW-002 — Hick's Law
- **enabled:** true
- **severity:** major
- **definition:** Decision time increases with the number and complexity of choices — more options mean slower decisions.
- **audit-for:** Choice overload on first viewport or key flows; progressive disclosure; sensible defaults; group or hide secondary options.

### LAW-003 — Jakob's Law
- **enabled:** true
- **severity:** major
- **definition:** Users spend most of their time on other products, so they expect yours to work the same way.
- **audit-for:** Familiar patterns for nav, search, forms, modals, and destructive confirms; avoid inventing novel interaction models without strong reason.

### LAW-004 — Doherty Threshold
- **enabled:** true
- **severity:** major
- **definition:** Productivity and engagement rise when a system responds to input in under ~400ms.
- **audit-for:** Feedback within ~400ms (optimistic UI, spinners after delay, skeletons); no silent hangs; mutations feel snappy.

### LAW-005 — Forcing Function
- **enabled:** true
- **severity:** blocking
- **definition:** For irreversible actions, make the safe choice the loud default primary; demote the destructive choice to a quiet, warning-tinted link-style action on the opposite side of the dialog.
- **audit-for:** Destructive dialogs do not put Delete as the default/primary next to Cancel; safe action is primary; destructive is de-emphasised and separated.

### LAW-006 — Focus Ring
- **enabled:** true
- **severity:** blocking
- **definition:** A focus indicator is the keyboard equivalent of a mouse cursor — visible for keyboard/non-pointer input, follows the DOM, and stays trapped in modals until close.
- **audit-for:** `:focus-visible` rings present; no `outline: none` without replacement; focus trap/return in dialogs; focus order matches visual order.

---

## Memory & Attention

### LAW-007 — Miller's Law
- **enabled:** true
- **severity:** major
- **definition:** Working memory holds roughly 7 (±2) items at once.
- **audit-for:** Long undifferentiated lists of controls/nav items; chunking into groups of ~5–9; progressive disclosure for dense UIs.

### LAW-008 — Multi-Step Chunking
- **enabled:** true
- **severity:** major
- **definition:** Break long forms into short meaning-grouped steps with visible progress, per-step validation, and persisted state — not one endless page.
- **audit-for:** Long forms split into steps; progress indicator; validate before advance; state survives refresh/back where expected.

### LAW-009 — Serial Position Effect
- **enabled:** true
- **severity:** minor
- **definition:** People remember the first and last items in a list best; middle items worst.
- **audit-for:** Most important actions/info at start or end of lists/menus; critical CTAs not buried mid-list.

### LAW-010 — Zeigarnik Effect
- **enabled:** true
- **severity:** minor
- **definition:** Unfinished or interrupted tasks are remembered better than completed ones — open loops hold attention.
- **audit-for:** Incomplete flows show progress/resume; drafts and in-progress states are visible; don't leave users without a way to finish.

### LAW-011 — Peak-End Rule
- **enabled:** true
- **severity:** major
- **definition:** People judge an experience mostly by its peak intensity and how it ends, not the average of every moment.
- **audit-for:** Success/empty/error endings feel clear and intentional; painful peaks (errors, waits) are softened; celebration or clear next step at completion.

---

## Perception & Gestalt

### LAW-012 — Law of Prägnanz
- **enabled:** true
- **severity:** minor
- **definition:** People perceive ambiguous or complex shapes in the simplest form possible.
- **audit-for:** Visual noise, competing shapes, unclear icons; simplify compositions so the intended reading is the simplest one.

### LAW-013 — Law of Proximity
- **enabled:** true
- **severity:** major
- **definition:** Objects near each other are perceived as more related than objects farther apart.
- **audit-for:** Labels close to their fields; related controls grouped; unrelated groups separated by clear spacing.

### LAW-014 — Law of Common Region
- **enabled:** true
- **severity:** major
- **definition:** Elements sharing a clear boundary (border, background, container) are perceived as one group.
- **audit-for:** Related content shares a region; unrelated content not falsely boxed together; cards/sections used only when grouping aids understanding.

### LAW-015 — Law of Similarity
- **enabled:** true
- **severity:** major
- **definition:** Elements that look similar are perceived as related or as performing the same function.
- **audit-for:** Same-looking controls imply same behaviour; primary vs secondary visually distinct; don't style links like buttons inconsistently.

### LAW-016 — Law of Uniform Connectedness
- **enabled:** true
- **severity:** minor
- **definition:** Elements connected by a line, arrow, or shared connector are perceived as more related than mere proximity or similarity.
- **audit-for:** Flows, steppers, and related pairs use connectors where relationship matters; avoid orphaned connectors.

### LAW-017 — Von Restorff Effect
- **enabled:** true
- **severity:** major
- **definition:** When several similar items are present, the one that differs is most likely remembered.
- **audit-for:** One clear visual standout for the primary CTA/state; don't make everything "special"; accent reserved for the winner.

### LAW-018 — Aesthetic-Usability Effect
- **enabled:** true
- **severity:** minor
- **definition:** People perceive more aesthetically pleasing designs as easier to use, whether or not they actually are.
- **audit-for:** Polish doesn't hide usability debt; beauty supports clarity (contrast, hierarchy, spacing) rather than decorating broken flows.

### LAW-019 — Hierarchy Through Contrast
- **enabled:** true
- **severity:** major
- **definition:** Visual hierarchy comes from contrast in weight and colour, not only escalating font size — heading and body can share size if weight/colour separate them.
- **audit-for:** Clear primary/secondary/tertiary text; contrast via weight/colour/space; avoid flat same-weight walls of text.

### LAW-020 — Tabular Numerals
- **enabled:** true
- **severity:** major
- **definition:** Numeric values in tables should be right-aligned and set in tabular (fixed-width) figures so digit columns align.
- **audit-for:** Tables/comparisons use `font-variant-numeric: tabular-nums` (or mono); numbers right-aligned where comparing.

### LAW-021 — Subgrid Alignment
- **enabled:** true
- **severity:** minor
- **definition:** When cards in a grid hold different-length content, align title/body/actions to shared parent row tracks (e.g. CSS subgrid) instead of each card owning its own rows.
- **audit-for:** Card grids with uneven content still align titles and action rows across the grid.

### LAW-022 — Scrollbar Continuity
- **enabled:** true
- **severity:** minor
- **definition:** A scrollable panel's scrollbar should be thin, on-brand, and left visible — not OS-bulky default only, and not fully hidden.
- **audit-for:** Nested scroll areas have usable visible scroll affordance; don't hide scrollbars with no alternative cue.

### LAW-023 — 60-30-10 Rule
- **enabled:** true
- **severity:** minor
- **definition:** Split colour into ~60% neutral background/surfaces, ~30% muted secondary for structure, ~10% saturated accent for the one action/state that should win the eye.
- **audit-for:** Accent colour not sprayed everywhere; one dominant accent job per view; neutrals carry most of the UI.

---

## Motivation & Complexity

### LAW-024 — Goal-Gradient Effect
- **enabled:** true
- **severity:** minor
- **definition:** Motivation to complete a task increases as people near the goal — effort accelerates near the finish.
- **audit-for:** Progress indicators that show closeness to done; last steps feel lighter; don't hide how much remains.

### LAW-025 — Tesler's Law
- **enabled:** true
- **severity:** major
- **definition:** Every process has irreducible complexity — the only question is whether the system or the user absorbs it.
- **audit-for:** Complexity pushed onto the user (manual formatting, remembering IDs, repeating data); system should default, infer, and prefill where safe.

### LAW-026 — Postel's Law
- **enabled:** true
- **severity:** major
- **definition:** Be liberal in what you accept from users, and conservative in what you send back (robustness principle).
- **audit-for:** Accept free text then validate; trim whitespace; flexible input formats; don't block paste/typing; outputs are clean and consistent.

### LAW-027 — Inline Validation
- **enabled:** true
- **severity:** blocking
- **definition:** Validate a field on blur (when the user leaves it), not on every keystroke and not only at submit. Clear that field's error the instant the user edits again.
- **audit-for:** Errors on blur or after submit attempt; no per-keystroke nagging before first leave; editing clears the field error immediately; errors sit next to fields.
