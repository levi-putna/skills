<!--
Component contract documentation template.

How to use this file:
- Copy the structure below into `<component>.md` next to the component's source file.
- Every HTML comment (like this one) is instructional — remove it once you've filled in
  or deliberately omitted the section it annotates. None should survive into the final doc.
- Sections marked "(optional — omit if ...)" should be deleted entirely, heading included,
  when they don't apply. Don't leave an empty table or a lone "N/A" in their place.
- Never fill in a value you didn't verify against the actual source code, types, or config.
-->

# {ComponentName}

**Type**: <!-- e.g. Primitive | Element | Composite | Pattern | Layout | Hook. Use this
project's existing design-system tiers if it has them; otherwise a short description like
"Element (pure UI — props in, callbacks out)" is fine. -->
**Path**: `<!-- directory containing the component, e.g. src/ui/elements/button/ -->`
**Dependencies**: <!-- other components/hooks/libraries this one composes or requires;
"None" if it's self-contained -->
**Status**: draft <!-- draft | stable | deprecated -->
**Storybook**: `<!-- story ID, e.g. Elements/Base/Button — delete this line if the project
has no Storybook -->`
**Source**: `<!-- path to the primary implementation file -->`

## Overview

<!-- 1-3 sentences: what the component does and why it exists. This is the only place
that states the component's purpose — don't repeat it verbatim elsewhere in the doc. -->

**Use when:** <!-- the situations this is the right component for -->

**Avoid when:** <!-- situations that look similar but call for something else; name the
alternative component so the reader isn't left guessing -->

## Anatomy

<!-- Optional — omit entirely for a single-element component. Include only for a compound
component that exposes multiple parts (e.g. `Dialog.Root` / `Dialog.Trigger` /
`Dialog.Content`). One line per exposed part and what it's responsible for. -->

| Part | Responsibility |
| --- | --- |
| `Component.Part` | |

## API

### Props

<!-- Every prop from the component's actual type definition. Collapse native attributes
spread via `...props` into a single row rather than enumerating each one. -->

| Prop | Type | Required | Default | Description |
| --- | --- | --- | --- | --- |
| `prop` | `type` | yes/no | `default` | What it controls, and any constraint on its value. |

### Events

<!-- Optional — omit this subsection if the component has no callback props. -->

| Event | Signature | When it fires |
| --- | --- | --- |
| `onX` | `(arg: T) => void` | Trigger condition, and any state that suppresses it (e.g. "not fired while disabled"). |

## Behaviour

<!-- Rules that aren't obvious from the prop table alone: derived state (e.g. "loading
implies disabled"), interaction constraints ("at most one per group"), cross-references to
named design-system rules/tokens/UX laws this component implements, edge cases. State the
rule, not just the fact, and cite a source (design doc, code comment) where one exists
rather than restating it generically. -->

## Accessibility

- **Role**: <!-- native element, or the ARIA role it renders with -->
- **Keyboard**: <!-- keys that operate it and what each does -->
- **ARIA**: <!-- attributes it sets or requires the consumer to set, e.g. aria-busy,
  aria-expanded, aria-invalid, aria-label requirements -->
- **Focus**: <!-- focus behaviour: focus-visible styling, focus trapping/restoration,
  initial focus target, if any -->

## States

<!-- Only states with a real, distinguishable visual or behavioural effect. -->

| State | Trigger | What the user sees |
| --- | --- | --- |
| Default | — | |

## Variants

<!-- Optional — omit if the component has no variant-style prop. Source this from the
actual variant config (e.g. a `cva` map), not from a design doc's description of intent. -->

| Variant | Tokens / classes | When to use |
| --- | --- | --- |

## Sizes

<!-- Optional — omit if the component has no size prop. Same treatment as Variants. -->

| Size | Tokens / classes | When to use |
| --- | --- | --- |

## Usage

<!-- One example per meaningfully different use case, not one per prop permutation. Base
examples on real stories/call sites where they exist rather than inventing new ones.
Comment each example with *why* you'd reach for that variant/state. -->

```tsx
```

<!-- Optional — include only if something here genuinely conflicts with an existing design
spec/doc. Don't silently resolve the conflict by picking one; surface it instead. -->
> **Note:** {design doc / spec} says X; the implementation actually does Y. This doc
> reflects the implementation — reconcile the spec when convenient.

## Testing notes

<!-- Focus on behaviour the API tables can't capture alone: derived/disabled states,
conditional callback firing, accessibility assertions. Skip anything already obvious from
## API. Omit the whole section if there's nothing beyond what the tables already cover. -->

| Scenario | Why it matters | Suggested approach | Priority |
| --- | --- | --- | --- |
| | | | high / medium / low |

## Out of scope

<!-- What this component deliberately does not do, and the correct alternative for each —
prevents future prop creep by naming the boundary explicitly. -->

- 

## Related

<!-- Optional — omit if there are no sibling/alternative components worth linking. One
clause per link on how it differs from this component. -->

- 
