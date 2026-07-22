---
name: documenting-components
id: 156b74fa-6759-4f30-a953-df3bc15056cb
version: 1.0.0
author: Levi Putna
repo: https://github.com/levi-putna/skills
description: >-
  Write or update the contract documentation for a single UI component as a
  Markdown file that sits next to its source, e.g. `button.tsx` +
  `button.md`. Covers everything a consumer needs to use the component
  correctly: purpose, props/events API, behaviour, accessibility, states,
  variants, and usage examples. Use when asked to "document this
  component", "write component docs", "generate a component spec/contract",
  "add a README/spec for this element", or when a component's `.md` file is
  missing or out of date with its code.
---

# Documenting components

Produces a single Markdown file that documents one component's contract —
what it does, its full props/events API, behaviour, accessibility, and how
to use it — and keeps it living next to the code it describes.

## Where the doc lives

Same directory as the component, same base filename, `.md` extension:

```
src/ui/elements/button/button.tsx
src/ui/elements/button/button.md      <- this skill produces this file
```

If the component's main file is an `index.tsx` inside a component-named
folder, name the doc after the folder/component instead of `index.md` (e.g.
`button/index.tsx` → `button/button.md`), since `index.md` doesn't identify
the component in a file listing.

One doc per component. For a compound component exported as several
sub-parts from one folder (`Dialog.Root`, `Dialog.Trigger`,
`Dialog.Content`), write one doc for the whole compound unit, named after
the folder, with an **Anatomy** section (see template) covering the parts —
don't split into per-part files.

## Reference template (source of truth)

```
.skills/documenting-components/references/component-doc-template.md
```

**Re-read this file at the start of every run** rather than relying on
memory — it's maintained independently of this skill's instructions. It's a
skeleton with HTML-comment guidance (`<!-- ... -->`) inside each section:
strip every comment from the final output and either fill the section in or
delete it entirely if it doesn't apply to this component. Never leave a
placeholder, empty table, or unresolved comment in the finished doc.

## Workflow

### 1. Resolve the target

Identify the component's primary source file from the user's request (a
path, a component name, or "the component I just built"). If ambiguous
(multiple components match, or the target exports several components with
no clear primary one), ask before proceeding.

### 2. Gather ground truth before writing anything

Read, in this order:

1. **The primary implementation file, in full** — prop/type definitions
   (interface, type alias, or inferred from a schema/variants config),
   default values, exported sub-components, internal state derivations
   (e.g. a `loading` prop that also sets `disabled`).
2. **Variant/style config**, if styling is driven by a separate map (e.g.
   `cva`, `class-variance-authority`, a `styles.ts`/tokens object) rather
   than inline — this is the real source for the Variants/Sizes tables, not
   the prose in a design doc.
3. **Co-located tests and stories** (`*.test.tsx`, `*.stories.tsx`,
   `*.spec.tsx`) — confirm documented states/variants actually have
   coverage or a live example, and mine them for realistic usage snippets
   rather than inventing examples from scratch.
4. **Sibling files in the same folder** — other exports, hooks, or
   sub-components this one depends on or composes with (for
   `Dependencies` and `Related`).
5. **Any project-level design doc** (`DESIGN.md`, a design-system guide,
   Storybook config for a story ID) — use it for token names, named
   UX-law/rule citations, and the Storybook path, but never let it override
   what the code actually does (see step 4 below).

Do not start writing the Markdown file until this reconnaissance is done.
Every fact in the API, States, Variants, and Sizes tables must trace back
to something you actually read in code — never infer or guess a prop,
default, or variant that isn't there.

### 3. Updating an existing doc

If a `.md` file already exists for this component, read it first:

- **Tables** (Props, Events, States, Variants, Sizes) are always
  re-derived fresh from current code. Code is ground truth — silently
  correct a stale table rather than asking permission.
- **Prose** (Overview, Behaviour, Out of scope, Related) written by a
  human may capture intent that isn't derivable from code alone. Keep it
  if it's still accurate; update it if code has clearly moved past it;
  flag it to the user rather than silently rewriting if you're unsure
  which is true.
- If the doc and the design spec/doc disagree with the implementation,
  don't pick one and "fix" the other — document what the code actually
  does and note the discrepancy (see the `Usage` section's note pattern in
  the template) so the user can reconcile it deliberately.

### 4. Write the file

Follow the template's section order and the section guide below. Omit any
section the template marks as optional when it doesn't apply (no
`Variants` table for a component with no variant prop, no `Anatomy` for a
non-compound component, no `Events` subsection for a component with no
callbacks). Don't pad an inapplicable section with "N/A" — delete it.

## Section guide

| Section | Source of truth | Notes |
| --- | --- | --- |
| Metadata block (Type/Path/Dependencies/Status/Storybook/Source) | Folder structure, imports, Storybook config | `Status` defaults to `draft` for a newly written doc; only mark `stable` if the user says so or the component has been in use with no open questions. Drop the `Storybook` line entirely if the project has no Storybook. |
| Overview | Component's own doc comment (if any) + how it's actually used in stories/call sites | One place only for "what is this and when do I reach for it" — don't repeat the same sentence in a separate "Purpose" and "When to use" section. |
| Anatomy *(compound components only)* | Sub-component exports | One line per exposed part and what it's responsible for. |
| API → Props | Prop types/interface | Every prop, including ones spread from a base primitive (`...props`) — collapse those into one `...props` row rather than enumerating native HTML attributes. |
| API → Events | Callback prop signatures | Include the condition under which each does/doesn't fire (e.g. suppressed while disabled/loading) — that's part of the contract, not just the type. |
| Behaviour | Code logic + design-system rules the component implements | State the *rule*, not just the fact — e.g. "loading implies disabled" plus *why* if a UX rationale is named in the design doc. |
| Accessibility | Rendered semantics (native element vs `role=`), keyboard handlers, ARIA attributes in code | Verify against code, don't assume a primitive library "just handles it" — name what it actually does. |
| States | Prop combinations / CSS state selectors (`:disabled`, `data-state=`, etc.) | Only states with a real, distinguishable visual or behavioural effect. |
| Variants | Variant config (`cva` map or equivalent) | Table of every variant value with its resolved tokens/classes. |
| Sizes | Size config | Same treatment as Variants. |
| Usage | Stories + real call sites | Pick one example per meaningfully different use case (not one per prop permutation); comment *why* you'd reach for that case. |
| Testing notes | Gaps between the contract above and what's easy to assert from types alone | Skip anything already obvious from the Props/Events tables; this section is for derived-state and conditional-firing behaviour worth a dedicated test. |
| Out of scope | What the component deliberately doesn't do, plus the correct alternative | Prevents future prop creep by naming the boundary explicitly. |
| Related | Sibling/alternative components in the same design-system tier | Omit if there are none. |

## Quality checklist before finishing

- Every prop/event/variant/size in the doc exists in the code you read; nothing is invented.
- No section is left with a placeholder, an empty table, or a stray HTML comment from the template.
- No inapplicable section (Variants/Sizes/Anatomy/Events/Related) was kept "for completeness" — it was deleted.
- Overview and behaviour notes don't duplicate each other or restate the prop table in prose.
- If the doc disagrees with an existing design spec doc, that's called out, not silently resolved.
- The file is saved next to the component using its base filename, not a generic `README.md`.

## Out of scope

- Writing or modifying the component's implementation — this skill only documents an existing contract. If the component doesn't exist yet, say so and ask whether to build it first (a separate concern).
- Generating actual test files — the `Testing notes` section is guidance for a human or a separate test-authoring pass, not executable test code.
- Setting up Storybook or writing stories — reference an existing story ID if one exists; don't create one.
- Documenting an entire component library/folder in one pass without being asked — confirm scope first if the request is ambiguous between "this component" and "all components in this folder".
