---
name: ui-audit-laws
id: 95fb87d0-ee36-47dd-8eac-a577f3670fe6
version: 1.2.0
author: Levi Putna
repo: https://github.com/levi-putna/skills
description: >-
  Audit a page, route, screen, or component against UX laws, Web Interface
  Guidelines, and design distinctiveness: score compliance, flag
  MUST/SHOULD/NEVER and law violations, catch generic/"AI slop" aesthetics
  (cliché gradients, default fonts, cookie-cutter layouts), and suggest concrete
  fixes. Use when asked to UI audit, UX audit, review UI laws, check
  accessibility/interaction guidelines, score a page against Fitts/Hick/focus
  rings/forms rules, review against Vercel web-interface-guidelines, or check
  whether a design looks generic/ AI-generated/distinctive.
---

# UI Laws Audit

Review a UI surface against four maintainable checklists, then return a scored audit with flagged issues and concrete remediations.

## Source of truth (edit these)

| File | Role |
|------|------|
| [references/ui-laws.md](references/ui-laws.md) | Research-backed UX laws (`LAW-001`…`LAW-027`) — enable/disable, tweak definitions |
| [references/agents-rules.md](references/agents-rules.md) | Concise **MUST / SHOULD / NEVER** rules |
| [references/web-interface-guidelines.md](references/web-interface-guidelines.md) | Expanded Web Interface Guidelines (rationale + how-to) |
| [references/design-distinctiveness.md](references/design-distinctiveness.md) | Aesthetic direction checks (`DESIGN-001`…`DESIGN-008`) — typography, colour, motion, composition; flags generic/"AI slop" defaults on brand-facing surfaces |

Always **Read** all four before auditing. Do not invent rules that are not in those files (or clearly implied by them). Prefer citing rule IDs / section headings so the user can edit the lists and re-run.

## Inputs

Resolve what to audit from the user message (ask only if ambiguous):

- **Target** — route path, page file(s), component path, URL, or “current page”
- **Scope** (default: full page) — `full` | `above-fold` | `component` | `flow` (e.g. form submit)
- **Viewport** (default: desktop + call out mobile risks) — note mobile ≥44px targets, 16px inputs, safe areas

Read the relevant source files (and related CSS/layout). If a live URL is given and browser tools are available, snapshot the page; otherwise audit from code.

## Workflow

### 1. Load checklists

Read:

1. `references/ui-laws.md` — skip any law with `enabled: false`
2. `references/agents-rules.md`
3. `references/web-interface-guidelines.md` — use for remediation detail when a concise rule fails
4. `references/design-distinctiveness.md` — use its "AI-slop tells" evidence bank for remediation detail when a `DESIGN-xxx` item fails

### 2. Map applicability

For each enabled law, MUST/SHOULD/NEVER rule, and design-distinctiveness item, mark:

- **Applicable** — the UI contains the pattern (forms, dialogs, tables, animation, etc.), or — for `DESIGN-xxx` items specifically — the surface is brand-facing/marketing/consumer-facing and meant to carry a designed point of view
- **N/A** — pattern absent; for `DESIGN-xxx` items, also mark N/A on utility-first surfaces (internal tools, dense dashboards, admin/back-office software) where restrained, generic-safe design is the correct choice, not a flaw

Only applicable items affect the score.

### 3. Inspect and verdict

For each applicable item, assign:

| Verdict | Meaning |
|---------|---------|
| **Pass** | Meets the rule/law |
| **Partial** | Intent present but incomplete or inconsistent |
| **Fail** | Clear violation |

Severity:

- **Blocking** — MUST/NEVER fail, or law marked `severity: blocking`
- **Major** — SHOULD fail, or law/design item `severity: major`
- **Minor** — polish / law/design item `severity: minor`

No `DESIGN-xxx` item is blocking — a generic aesthetic is a missed opportunity, never grounds to cap the score band the way an accessibility/interaction failure is.

When failing, pull remediation language from `web-interface-guidelines.md` (matching section), the law’s `audit-for` field, or the "AI-slop tells" evidence bank in `design-distinctiveness.md`. Suggest a **specific** change (file/component, what to add/remove/change) — not generic advice.

### 4. Score

```
score = round( 100 × (passes + 0.5×partials) / applicable )
```

Where `applicable` = count of applicable laws + applicable MUST/SHOULD/NEVER rules + applicable design-distinctiveness items (each bullet/item is one item). N/A excluded.

Grade band (report both number and band):

| Score | Band |
|------:|------|
| 90–100 | Excellent |
| 75–89 | Good |
| 60–74 | Needs work |
| 0–59 | Poor |

**Hard cap:** if any **blocking** Fail remains, the reported band cannot be higher than **Needs work**, even if the numeric score is higher — note the capped band and the uncapped score.

### 5. Write the audit report

Use this structure (Australian English):

```markdown
# UI Laws Audit — {target}

**Score:** {n}/100 ({band}){; capped due to blocking failures if applicable}
**Scope:** {scope} · **Viewport notes:** …

## Summary
{2–4 sentences: overall verdict and the highest-impact fixes}

## Blocking issues
For each: ID · title · evidence · suggested fix

## Major issues
…

## Minor issues
…

## Law results
| ID | Law | Verdict | Notes |
|----|-----|---------|-------|
| LAW-001 | Fitts's Law | Pass / Partial / Fail / N/A | … |

## Guideline results (MUST / SHOULD / NEVER)
Grouped by section from agents-rules.md. List only Fail and Partial by default; mention Pass counts in a one-liner ("Interactions: 12 pass, 2 fail").

## Design distinctiveness results
| ID | Item | Verdict | Notes |
|----|------|---------|-------|
| DESIGN-001 | Committed Point of View | Pass / Partial / Fail / N/A | … |

If every `DESIGN-xxx` item is N/A (utility surface), replace this section with a one-liner: "Design distinctiveness: N/A — utility-first surface, restrained/generic-safe design is correct here."

## Recommended changes (priority order)
1. {blocking first}
2. …
```

Issue IDs:

- Laws: `LAW-xxx`
- Concise rules: `WIG-{section-slug}-{n}` e.g. `WIG-keyboard-1` (stable within the report; re-derive from current file order on re-runs)
- Design distinctiveness: `DESIGN-xxx`

### 6. Offer next steps

After the report, ask whether to:

1. Implement the blocking/major fixes in code
2. Re-audit after changes
3. Adjust a law/rule in the reference files (user edits the maintainable lists)

Do **not** start implementing unless the user asks.

## Decision rules while auditing

- **MUST / NEVER** from `agents-rules.md` outrank taste and SHOULD, and both outrank every `DESIGN-xxx` item — a beautiful, distinctive interface that fails keyboard access or focus rings still fails.
- Prefer native semantics before ARIA; cite the guideline when recommending ARIA.
- If code and live UI disagree, trust what users will experience (live) and note the code drift.
- Project design-system conventions win on aesthetics; laws and MUST rules still apply to behaviour and accessibility.
- Do not fail marketing pages for missing form patterns that are N/A.
- Do not fail a deliberately restrained/minimal design against `DESIGN-xxx` items just because it's plain — the fail case is *unconsidered default*, not *intentional restraint*. When genuinely unsure which it is, say so in Notes rather than guessing a verdict.
- Do not score `DESIGN-xxx` items at all on utility-first surfaces (internal tools, dashboards, admin software) — mark them N/A per the applicability rule in `design-distinctiveness.md`.
- Use the ellipsis character `…` in suggestions that involve loading/follow-up labels, per the guidelines.

## Out of scope

- Choosing or implementing a new creative direction (fonts, palette, motion concept, layout system) — this audit flags genericness/distinctiveness against `design-distinctiveness.md` and cites evidence; picking the actual replacement direction is a creative decision for the user/designer to make, then optionally re-audit
- Backend performance beyond what the UI can show (loading, optimistic UI, CLS)
- Rewriting the reference lists unless the user asks to update the skill’s checklists

