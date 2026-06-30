# Skills — Spec-Driven Development Harness

Agent skills for **doc-first, AI-built software**: you maintain the specification and locked decisions; the agent implements, tests, and ships — with controls that prevent full-app rewrites when requirements change.

Developed by [Levi Putna](https://github.com/levi-putna). Installed via [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit).

[![GitHub](https://img.shields.io/github/stars/levi-putna/skills?style=social)](https://github.com/levi-putna/skills)

```sh
npx @levi-putna/agent-kit@latest list levi-putna/skills
```

---

## The idea

Traditional development: you write code, docs lag behind.

This harness inverts that for agentic workflows:

| You (human) | Agent |
|---|---|
| Explore ideas and approve designs | Draft architecture and contracts |
| Own `docs/technical/` — requirements, NFRs, locked stack choices | Build test-first within those constraints |
| Approve impact reports and plans before code changes | Execute plans in small, verified batches |
| Review PRs and tune decisions (🔒 / 🤖) | Verify end-to-end; fix until green |

**`docs/technical/` is the living source of truth.** Tests enforce it. Dated plans and impact reports record what changed and when. This is **spec-driven development (SDD)** with **executable specifications** (TDD + e2e) — the same spirit as BDD and contract-first APIs, adapted for AI agents.

---

## Quick start

### Install the full harness

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```

### Pick your path

**New project?** Start here:

> "Help me brainstorm a [feature]. Then write technical docs and build it."

Skills invoked: `brainstorming` → `technical-documentation` → `planning` → `project-setup` → `executing-plans` → `shipping`

**Existing app, changed the spec?** Start here:

> "I updated requirements.md. Analyse the impact and implement the delta."

Skills invoked: `refining-docs` → `conformance-check` → `reconciling-changes` → `planning` → `executing-plans` → `shipping`

**Plan already written?** Start here:

> "Implement the plan at docs/plans/2026-06-29-feature.md with checkpoints."

Skills invoked: `executing-plans` (which drives `test-driven-development` and `end-to-end-testing`)

---

## The harness

Seventeen skills chain into five phases. Review gates sit between each hand-off — the agent stops and waits for your approval unless you say to run through.

```
  DESIGN                    CHANGE CONTROL              PLAN                 BUILD                         SHIP
  ──────                    ──────────────              ────                 ─────                         ────

  brainstorming             refining-docs                                      executing-plans               shipping
       │                         │                         planning                 │
       ▼                         ▼                              │                   ├─ test-driven-development
  technical-documentation   CHANGELOG.md                       │                   │  (RED → GREEN → REFACTOR)
  docs/designs/             docs/technical/                    ▼                   │
       │                         │                    project-setup*                │
       │                         │                    design-system*                ├─ ui-ux-best-practices
       │                         │                    component-library*            │  (clean UX review)
       │                         │                                                   ├─ component-development
       │                         │                                                   │  (audit → extend/create → test)
       │                         ├──── conformance-check ────┤                     │
       │                         │         (audit)           │                     ├─ component-testing
       │                         └──── reconciling-changes ──┘                     │  (verify + regression)
       │                              (impact report)                              │
       │                                    │                                      ▼
       │                                    │                              end-to-end-testing
       │                                    │                                      │
       └────────────────────────────────────┴──────────────────────────────────────┘
                                                    conformance-check (post-build)

  * project-setup — greenfield only (scaffold repo before first build)
  * design-system — once per project (define tokens, standards, component rules)
  * component-library — once per project (set up component infrastructure)
```

### Phase summary

| Phase | Skills | Output |
|---|---|---|
| **Design** | brainstorming, technical-documentation, refining-docs | `docs/designs/`, `docs/technical/` |
| **Change control** | conformance-check, reconciling-changes | Impact reports, drift audits in `docs/plans/` |
| **Plan** | planning, project-setup, design-system, component-library | Dated TDD task lists in `docs/plans/`, design tokens |
| **Build** | executing-plans, test-driven-development, ui-ux-best-practices, component-development, component-testing, end-to-end-testing | Working, tested code and reusable components |
| **Ship** | shipping | Branch, commits, PR |

### The build loops

**Inner loop** — `test-driven-development`: one requirement at a time, failing test first, minimal code, refactor.

**UX review loop** — `ui-ux-best-practices`: when screens or composed views are designed, review hierarchy, whitespace, responsiveness, accessibility, user flow, and AI-generated UI antipatterns.

**Element loop** — `component-development` + `component-testing`: when UI elements are needed, build them as reusable components in isolation first, then integrate.

**Outer loop** — `end-to-end-testing`: run the real app against acceptance criteria; on failure, file fix tasks and send control back to the inner loop.

### Component-first development

**Rule: Never create one-off UI components.** When a feature needs UI elements, the agent must:

1. **Check design system** — Verify component follows design tokens and standards
2. **Audit existing components** — Search for similar components that could be extended
3. **Decide: extend or create** — Use decision framework from design system
4. **If extending** — Test backward compatibility, ensure existing usage unaffected
5. **If creating new** — Document why existing components insufficient
6. **Build with design tokens** — Use semantic colors, spacing scale, typography tokens
7. **Test before integrate** — Verify in isolation before using in application

This ensures:
- All UI elements use design system tokens (consistent brand)
- Components are reusable and tested in isolation
- No component proliferation (dozens of similar variants)
- Backward compatibility maintained when extending
- Visual regression and accessibility verified before integration

---

## All skills

### Standalone utility

#### [general-writing](./skills/general-writing/)

Polish prose — clear, natural language without AI clichés. Not part of the build pipeline; use anywhere you need better copy.

**Prompt:** *"Rewrite this README section to sound more natural."*

#### [mermaid-diagrams](./skills/mermaid-diagrams/)

Create professional Mermaid diagrams — themed styling, semantic colours, layout rules, and captions. Complements technical-documentation; use when visuals need to look polished, not like default black-line exports.

**Prompt:** *"Draw a sequence diagram for the checkout flow with proper styling."*

---

### UI Component Development

**Core principle:** UI elements are built as reusable components first, never created inline. Component development follows a strict workflow: **design system** → **audit** → **extend or create** → **test** → **integrate**.

#### [ui-ux-best-practices](./skills/ui-ux-best-practices/)

Review and guide UI/UX work for clean, professional, low-noise interfaces. Checks hierarchy, whitespace, responsive behaviour, accessibility, user flow, and common AI-generated UI antipatterns.

|| |
|---|---|
| **When** | Before building pages/views; after composing components into screens; before shipping UI-heavy work |
| **Creates** | UI/UX review findings, anti-slop checks, required revisions |
| **Invoked by** | planning and executing-plans for UI-heavy views, or manually during design review |
| **Prevents** | Busy/noisy UI, generic AI slop, weak task flow, poor responsive behaviour |

**Prompt:** *"Review this dashboard for clean UI/UX, whitespace, responsive design, and AI-generated UI antipatterns."*

#### [design-system](./skills/design-system/)

Define design foundation: brand identity, design tokens, color palette, typography, spacing, and component standards. Creates the single source of truth for all UI decisions.

|| |
|---|---|
| **When** | Once per project (before any UI work); when establishing design standards |
| **Creates** | Design tokens, color palette, typography scale, component decision framework |
| **Invoked by** | project-setup (greenfield), or manually before component work |
| **Hands off to** | component-library |
| **Prevents** | Component proliferation, inconsistent styling, unclear extension rules |

**Prompt:** *"Define the design system including brand colors, typography, and component standards."*

**Key output:** `docs/design/design-system.md` with:
- Semantic color tokens (primary, destructive, etc.)
- Typography and spacing scales
- Component creation vs. extension guidelines
- Backward compatibility requirements

#### [component-library](./skills/component-library/)

Set up component library showcase infrastructure with Storybook 8 or custom in-app gallery. Creates the foundation for component-driven development.

|| |
|---|---|
| **When** | Once per project (greenfield); before first UI component |
| **Creates** | Storybook config, category structure, documentation |
| **Invoked by** | project-setup (greenfield), or manually when needed |
| **Hands off to** | component-development |

**Prompt:** *"Set up a component library showcase for this project."*

#### [component-development](./skills/component-development/)

Build UI components in isolation following CDD best practices. Creates components with stories, variants, interaction tests, and documentation.

|| |
|---|---|
| **When** | Any time UI elements are needed; invoked during executing-plans when plans reference new components |
| **Creates** | Component + story file + tests in `components/` |
| **Follows** | shadcn/ui composition patterns, Vercel AI SDK patterns |
| **Invoked by** | executing-plans (when UI elements needed), or manually |
| **Hands off to** | component-testing (verification), then back to calling skill |

**Prompt:** *"Create a UserCard component with avatar, name, role, and expand/collapse."*

**Integration rule:** When `executing-plans` encounters a task that requires new UI elements, it must:
1. Pause the current task
2. Confirm `design-system` and `component-library` outputs exist
3. Invoke `component-development` to audit existing components and decide whether to extend or create
4. Invoke `component-testing` to verify behaviour, accessibility, visual regression, and backwards compatibility
5. Resume task and integrate the tested element

#### [component-testing](./skills/component-testing/)

Add comprehensive testing to components: interaction tests, visual regression, accessibility audits, and unit tests.

|| |
|---|---|
| **When** | After component-development (before integration) |
| **Creates** | Interaction tests, visual regression config, unit tests, CI setup |
| **Verifies** | User behaviour, visual appearance, WCAG compliance |
| **Invoked by** | component-development (after element created), or manually |
| **Hands off to** | Calling skill (e.g., executing-plans) or end-to-end-testing |

**Prompt:** *"Add comprehensive tests to the UserCard component."*

---

### Design and documentation

#### [brainstorming](./skills/brainstorming/)

Think through fuzzy ideas like a colleague. Surfaces trade-offs, writes a design spec. No code.

| | |
|---|---|
| **When** | New feature, greenfield project, requirements unclear |
| **Writes** | `docs/designs/YYYY-MM-DD-<name>.md` |
| **Hands off to** | technical-documentation, or planning for small well-understood changes |

**Prompt:** *"I want to build a notification system but I'm not sure about the architecture. Let's brainstorm."*

#### [technical-documentation](./skills/technical-documentation/)

Turn an approved design into buildable technical docs — requirements with REQ-IDs, architecture, data model, API contracts, NFRs, UI requirements, and engineering guidelines.

| | |
|---|---|
| **When** | After brainstorming; before first build |
| **Writes** | `docs/technical/*.md` |
| **Key mechanic** | Every tech decision tagged 🔒 human-locked · 🤖 agent-discretion · ❓ needs-decision |
| **Hands off to** | planning (greenfield), or conformance-check (brownfield) |

**Prompt:** *"The design spec is approved. Write the technical documentation set."*

#### [refining-docs](./skills/refining-docs/)

Tune docs that already exist — gap analysis, consistency, diagrams, decision ownership, changelog entries. Diagnose before editing; never silently change 🔒 decisions.

| | |
|---|---|
| **When** | Docs need updating; after decisions change; before impact analysis |
| **Writes** | Updates in place to `docs/technical/`; appends to `CHANGELOG.md` |
| **Hands off to** | conformance-check → reconciling-changes (when behaviour changed) |

**Prompt:** *"Review requirements.md for gaps and update the CSV export section."*

---

### Change control

These skills are what make brownfield work safe — they stop the agent rewriting the whole app when you edit the spec.

#### [conformance-check](./skills/conformance-check/)

Audit docs against code (and vice versa). Finds untested requirements, orphan tests, 🔒 stack violations, contract drift.

| | |
|---|---|
| **When** | Before a delta; after a build; "does code match the spec?" |
| **Writes** | Conformance report in `docs/plans/` |
| **Hands off to** | refining-docs (doc gaps), reconciling-changes (code behind spec) |

**Prompt:** *"Run a conformance check — do our tests cover every REQ in requirements.md?"*

#### [reconciling-changes](./skills/reconciling-changes/)

Analyse what changed in `docs/technical/` since a baseline. Classify each change (ADD / MODIFY / REMOVE / CLARIFY), map to files and tests, define **frozen scope**.

| | |
|---|---|
| **When** | After doc updates on an existing app; before delta planning |
| **Writes** | `docs/plans/YYYY-MM-DD-<name>-impact.md` |
| **Hands off to** | planning (delta mode) — only after you approve the impact report |

**Prompt:** *"Requirements changed since last release. Produce an impact report before we touch code."*

---

### Plan and build

#### [planning](./skills/planning/)

Turn specs into executable TDD task lists — exact paths, complete test code, verification steps.

| Mode | When | Scope |
|---|---|---|
| **Greenfield** | New project or subsystem | All requirements in scope |
| **Delta** | Brownfield, after impact report | Only ADD/MODIFY/REMOVE items; frozen scope explicit |

| | |
|---|---|
| **Writes** | `docs/plans/YYYY-MM-DD-<name>.md` |
| **Hands off to** | executing-plans |

**Prompt:** *"Write a delta plan from the impact report — only touch what's listed."*

#### [project-setup](./skills/project-setup/)

Scaffold a greenfield repo from technical docs — structure, `package.json`, `node:test`, tooling, CI, seed `AGENTS.md`.

| | |
|---|---|
| **When** | Once, before first build on a new repo |
| **Skip when** | Codebase already exists |

**Prompt:** *"Scaffold the project from docs/technical/ so we can start building."*

#### [executing-plans](./skills/executing-plans/)

Orchestrator. Loads a plan, works in batches of ~3 tasks, checkpoints with you, enforces scope on delta work. **Automatically invokes component-development when tasks require new UI elements.**

| | |
|---|---|
| **When** | A plan exists and you're ready to build |
| **Drives** | test-driven-development (per task), component-development (when UI elements needed), component-testing (verify elements), end-to-end-testing (when tasks done), conformance-check (post-build on deltas) |
| **Hands off to** | shipping |

**Prompt:** *"Execute docs/plans/2026-06-29-export.md — checkpoint after each batch."*

**Component-first integration:** When a task requires UI elements:
1. Check if component exists in `components/`
2. If missing, pause task and invoke **component-development**
3. Invoke **component-testing** to verify
4. Resume task and integrate tested elements
5. Never create one-off UI components inline

#### [test-driven-development](./skills/test-driven-development/)

The inner build engine. RED → GREEN → REFACTOR with `node:test`. Tests named with REQ-IDs for traceability.

| | |
|---|---|
| **When** | Any code being written; invoked by executing-plans |
| **Rule** | No production code without a failing test first |

#### [end-to-end-testing](./skills/end-to-end-testing/)

Verify the **running** app against acceptance criteria. API/CLI via `node:test`; web UI via Playwright. Failure → fix tasks → back to TDD.

| | |
|---|---|
| **When** | After unit/functional suite is green |
| **Maps to** | REQ-IDs in `requirements.md` |

---

### Ship

#### [shipping](./skills/shipping/)

Pre-flight checks, conventional commits, changelog, PR. Never ships a red or unverified build.

| | |
|---|---|
| **When** | E2e green; you want a PR or release |
| **Requires** | Green unit tests + green e2e |

**Prompt:** *"E2e passed. Open a PR for this branch."*

---

## Workflows in detail

### Greenfield — idea to shipped software

```
1. brainstorming          →  docs/designs/2026-06-29-feature.md
2. technical-documentation →  docs/technical/ (requirements, architecture, stack, …)
3. planning (greenfield)  →  docs/plans/2026-06-29-feature.md
4. project-setup          →  scaffolded repo
      └─ design-system (if UI needed)          →  Design tokens, component standards
      └─ component-library (if UI needed)      →  Storybook infrastructure
5. executing-plans        →  builds in batches
      ├─ test-driven-development                per task
      ├─ ui-ux-best-practices                   review composed views and flows
      ├─ component-development                  when UI elements needed
      │    ├─ audit existing components         check for reuse/extension
      │    ├─ extend or create                  follow design system rules
      │    └─ component-testing                 verify before integration
      └─ end-to-end-testing                     when tasks complete
6. shipping               →  PR
```

**Component integration:** If a task in the plan requires UI elements:
- `executing-plans` identifies the gap
- Component audit checks for existing similar components
- Decision: extend existing OR create new (per design system rules)
- If extending: test backward compatibility
- If creating: document why existing insufficient
- `component-testing` verifies before integration
- Resumes task and integrates the tested elements

### Brownfield — doc change to deployed delta

This is the **day-to-day loop** once an app exists. You edit docs; the agent changes only what the impact report allows.

```
1. refining-docs          →  update docs/technical/ + CHANGELOG.md
2. conformance-check      →  know existing drift before planning
3. reconciling-changes    →  impact report + frozen scope  ✋ approve
4. planning (delta)       →  tasks for changed REQ-IDs only  ✋ approve
5. executing-plans        →  baseline tests green first; surgical edits
      ├─ test-driven-development
      ├─ component-development              when new UI elements needed
      │    └─ component-testing             verify before integration
      ├─ end-to-end-testing
      └─ conformance-check                  post-build audit
6. shipping               →  PR
```

### UI-focused workflow — building components

When working primarily on UI elements:

```
1. design-system          →  define tokens, standards, component rules
2. ui-ux-best-practices   →  review target flow for hierarchy, clarity, and anti-slop risks
3. component-library      →  set up showcase infrastructure (once)
4. component-development  →  audit → extend or create UserCard with stories
5. component-testing      →  interaction + visual + a11y tests
6. component-development  →  audit → extend or create ProductCard with stories
7. component-testing      →  interaction + visual + a11y tests
8. ui-ux-best-practices   →  review composed page/view before final integration
9. (integrate into app)   →  use <UserCard /> and <ProductCard /> in pages
10. end-to-end-testing    →  verify in context
11. shipping              →  PR
```

**Rule:** Components are always created in isolation before integration. Never build UI inline in pages.

**Audit rule:** Before creating any component, MUST check if similar component exists that could be extended.

### Skill picker — "I want to…"

| Goal | Skill(s) |
|---|---|
| Explore a fuzzy idea | brainstorming |
| Write technical docs from a design | technical-documentation |
| Fix, tune, or extend existing docs | refining-docs |
| Check if code matches the spec | conformance-check |
| See what a doc change affects | reconciling-changes |
| Get a task list to implement | planning |
| Scaffold a new repo | project-setup |
| Define design tokens and standards | design-system |
| Review UI/UX quality and anti-slop risks | ui-ux-best-practices |
| Set up component showcase | component-library |
| Build from a plan | executing-plans (auto-invokes component skills when UI needed) |
| Build UI components | component-development (auto-invoked by executing-plans) |
| Test UI components | component-testing (auto-invoked after component-development) |
| Ship verified work | shipping |
| Improve prose (any context) | general-writing |
| Draw or polish Mermaid diagrams | mermaid-diagrams |

---

## Documentation layout

Everything lives under `docs/` at the project root:

```
docs/
  designs/                    # Point-in-time — what and why at decision time
    2026-06-29-checkout.md

  technical/                  # Living source of truth (no dates in filenames)
    requirements.md           # REQ-001, REQ-001-AC1, acceptance criteria
    architecture.md
    tech-stack.md             # 🔒 / 🤖 / ❓ on every decision
    data-model.md
    api-contracts.md
    nfr.md
    engineering-guidelines.md # Seeds AGENTS.md / CLAUDE.md
    CHANGELOG.md              # REQ-level doc change history

  plans/                      # Point-in-time — tasks, impact, audits
    2026-06-29-checkout.md              # implementation plan
    2026-07-01-export-impact.md         # impact report
    2026-07-02-conformance-report.md    # drift audit
```

| Folder | Lifecycle | Who writes |
|---|---|---|
| `docs/designs/` | Historical snapshot | brainstorming |
| `docs/technical/` | **Always current** | technical-documentation, refining-docs |
| `docs/plans/` | Historical snapshot | planning, reconciling-changes, conformance-check |

### Traceability spine

REQ-IDs link the whole pipeline:

```
requirements.md          REQ-042, REQ-042-AC1
       ↓
CHANGELOG.md             MODIFY REQ-042
       ↓
impact report            MODIFY REQ-042 → src/export.ts, tests
       ↓
delta plan               Task 3: REQ-042-AC1
       ↓
test name                test('REQ-042-AC1: includes header row', …)
       ↓
conformance report       REQ-042 ✅ covered
```

### Key concepts

**REQ-IDs** — Stable identifiers on every requirement and acceptance criterion. Never rename; mark deprecated or removed instead.

**Decision ownership** — In `tech-stack.md`:
- 🔒 **human-locked** — agent must not change without explicit approval
- 🤖 **agent-discretion** — agent may choose and revise during build
- ❓ **needs-decision** — must be resolved before hand-off (never left open)

**Frozen scope** — Delta plans and impact reports list files, modules, and requirements that must **not** change. Existing tests guard against scope creep.

**Living vs dated** — Edit `docs/technical/` in place. Never date those filenames. Plans and designs get dated filenames because they record a moment in time.

---

## Controls against full rewrites

| Control | Mechanism |
|---|---|
| Stable REQ-IDs | Same ID from doc → plan → test → audit |
| Impact report | reconciling-changes defines scope before code |
| Frozen scope | Explicit "do not touch" in delta plans |
| Delta-only tasks | planning (delta) covers changed REQs only |
| Green baseline | executing-plans requires green suite before delta edits |
| REQ-ID test names | test-driven-development — traceable, auditable |
| Design system | Single source of truth for all UI decisions, prevents component proliferation |
| Component audit | MUST check existing components before creating new, document decision |
| Component-first | No inline UI — component-development creates reusable elements |
| Backward compatibility | Extensions must not break existing usage, verified by tests |
| Batch scope check | executing-plans reports out-of-plan file touches |
| E2e gate | end-to-end-testing verifies real behaviour |
| Post-build audit | conformance-check catches drift |
| Agent rules | engineering-guidelines.md → AGENTS.md non-negotiables |

---

## Install

**Full harness** (interactive picker):

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```

**Individual skills:**

```sh
# Design & docs
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill technical-documentation --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill refining-docs --global

# UI component development
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill design-system --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill ui-ux-best-practices --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-library --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-development --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill component-testing --global

# Change control
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill conformance-check --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill reconciling-changes --global

# Plan & build
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill project-setup --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill test-driven-development --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill end-to-e nd-testing --global

# Ship & utility
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill shipping --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill general-writing --global
```

Use `--project` instead of `--global` to install into a single repo. Add `--agent cursor` or `--agent claude` to target a specific agent.

---

## Repository structure

```
skills/
  brainstorming/
    SKILL.md          # Agent reads this
    README.md         # Human install guide
  technical-documentation/
  refining-docs/
  design-system/
  ui-ux-best-practices/
  component-library/
  component-development/
  component-testing/
  conformance-check/
  reconciling-changes/
  planning/
  project-setup/
  executing-plans/
  test-driven-development/
  end-to-end-testing/
  shipping/
  general-writing/
  mermaid-diagrams/
```

Each skill folder name is the `--skill` identifier. `SKILL.md` has YAML frontmatter (`name`, `description`) plus step-by-step instructions. The agent uses `description` to decide when to activate a skill.

---

## License

MIT
