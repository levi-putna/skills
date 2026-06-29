---
name: refining-docs
description: >-
  Refine, tune, and keep technical documentation consistent. Use to improve an
  existing doc set (from the technical-documentation skill or elsewhere) — fill
  gaps, check cross-document consistency, add or update Mermaid diagrams, re-tune
  a locked vs agent-owned decision, or tighten the writing. Use when the user
  wants to review, update, or polish technical docs rather than write them from scratch.
---

# Refining Docs

Improve technical documentation that already exists. The authoring pass (technical-documentation skill) gets docs on disk; this skill is for everything after — tuning decisions, filling gaps, keeping the set consistent as the project evolves, and tightening the prose.

Announce at start: "I'm using the refining-docs skill to refine the technical docs."

Make focused, reviewable changes. Do not rewrite a whole doc set unprompted, and do not silently change a 🔒 human-locked decision.

## Prerequisites

- At least one existing document to refine (typically in `docs/technical/`, but any `.md` works)
- A sense of what the user wants improved — if unclear, ask before editing

## Modes

Pick the mode from what the user asks. Several can run together.

| Mode | Trigger | What it does |
|---|---|---|
| **Gap analysis** | "what's missing?", "is this complete?" | Find undefined decisions, missing sections, untestable requirements, ❓ tags left open |
| **Consistency** | "do these agree?", after a change | Cross-check docs against each other — stack, entities, endpoints, terminology |
| **Diagrams** | "add a diagram", "update the flow" | Add or update Mermaid diagrams; verify they render; add captions |
| **Decision tuning** | "lock this", "let the agent decide X" | Change a decision's owner tag (🔒/🤖/❓) and propagate the consequence |
| **Tighten** | "clean this up", "too verbose" | Improve clarity and structure without changing meaning |

If the user is vague ("improve the docs"), default to **gap analysis + consistency** and report findings before editing.

## The flow

### 1. Load and orient

1. Read the target doc(s). If the user named one doc but it cross-references others, read those too — refinement is often about the *set*, not one file.
2. Restate what you're about to do in one line, and which mode(s) apply.

### 2. Diagnose before editing

Run the relevant mode's checks and **report findings first** — don't jump straight to edits. Present as a short list the user can react to:

> "Three things in `tech-stack.md`:
> 1. ❓ The database choice is still open.
> 2. `architecture.md` names Postgres but `tech-stack.md` says 'TBD' — inconsistent.
> 3. The caching row has no rationale.
>
> Want me to fix all three, or just some?"

**✋ Checkpoint — let the user steer before changing files.**

### 3. Apply changes

- Edit in place; preserve the document's existing structure and voice
- Keep edits scoped to what was agreed
- When changing a decision owner, update every place it appears (the stack table, the ADR, anything downstream that depended on it)
- Never flip a 🔒 human-locked decision without the user explicitly asking

### 4. Re-check consistency

After any edit, re-run a quick consistency pass across the set:

- Stack named in `architecture.md` matches `tech-stack.md`
- Entities in `data-model.md` cover what `requirements.md` needs
- Endpoints in `api-contracts.md` map to requirements
- No new ❓ introduced; no contradictions created
- Diagrams still valid Mermaid, each with a caption

### 5. Summarise

Report what changed, file by file, in a few lines. Flag anything still open:

> "Updated `tech-stack.md` (database → Postgres, 🔒) and `architecture.md` to match. One thing left for you: the cache TTL in `nfr.md` is still a placeholder."

## Standards (inherited from authoring)

Keep refined docs to the same bar the technical-documentation skill sets:

- Every technical decision tagged 🔒 human-locked, 🤖 agent-discretion, or ❓ needs-decision — and ❓ is a flag to resolve, not to leave
- Diagrams in **Mermaid**, fenced as ` ```mermaid `, each with a one-line caption
- Tables for decision matrices, field lists, and requirement sets
- NFR targets measurable (numbers, not "fast")
- Each doc readable standalone
- No TBD/TODO left behind after a refinement that touched that section

## Principles

- **Diagnose, then edit** — surface findings and get direction before changing files
- **Scoped changes** — refine what was asked; no drive-by rewrites
- **Protect locked decisions** — 🔒 changes only on explicit request
- **The set over the file** — a change to one doc often ripples; keep the whole set consistent
- **Preserve voice** — improve the docs, don't replace the author's intent

## Anti-patterns

- Rewriting a whole document when the user asked for one fix
- Editing before reporting what's wrong
- Silently changing a human-locked decision
- Fixing one doc and leaving the set contradicting itself
- Adding diagrams with no caption or invalid syntax
