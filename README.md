# Skills

A collection of agent skills developed by [Levi Putna](https://github.com/levi-putna), made available for general use and various work contexts.

[![GitHub](https://img.shields.io/github/stars/levi-putna/skills?style=social)](https://github.com/levi-putna/skills)

Skills are installed with [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit). Browse everything in this repo:

```sh
npx @levi-putna/agent-kit@latest list levi-putna/skills
```

---

## Skills

| Skill | Purpose | Install |
|---|---|---|
| [general-writing](./skills/general-writing/) | Rewrite or draft text in clear, natural language. Strips AI clichés, filler, and overly formal phrasing. | [Install guide](./skills/general-writing/README.md#install) |
| [brainstorming](./skills/brainstorming/) | Think through ideas with a trusted colleague before building. Writes design specs. | [Install guide](./skills/brainstorming/README.md#install) |
| [technical-documentation](./skills/technical-documentation/) | Turn an approved design into a production-ready set of technical docs (architecture, data model, tech-stack decisions) an AI agent can build from. | [Install guide](./skills/technical-documentation/README.md#install) |
| [refining-docs](./skills/refining-docs/) | Refine and tune an existing doc set — gap analysis, consistency checks, diagrams, decision tuning. | [Install guide](./skills/refining-docs/README.md#install) |
| [planning](./skills/planning/) | Turn an approved design into a TDD implementation plan with exact tasks. | [Install guide](./skills/planning/README.md#install) |
| [project-setup](./skills/project-setup/) | Scaffold a new project from its technical docs — structure, `node:test`, tooling, CI. | [Install guide](./skills/project-setup/README.md#install) |
| [executing-plans](./skills/executing-plans/) | Orchestrate the build task-by-task, driving the TDD loop and e2e verification. | [Install guide](./skills/executing-plans/README.md#install) |
| [test-driven-development](./skills/test-driven-development/) | The core build loop — red-green-refactor with `node:test` until every requirement passes. | [Install guide](./skills/test-driven-development/README.md#install) |
| [end-to-end-testing](./skills/end-to-end-testing/) | Verify the running app against requirements; retrigger the build loop on failure. | [Install guide](./skills/end-to-end-testing/README.md#install) |
| [shipping](./skills/shipping/) | Ship verified work — branch hygiene, conventional commits, changelog, PR. | [Install guide](./skills/shipping/README.md#install) |

### Design-to-ship workflow

The skills chain into a spec-driven pipeline that takes an idea all the way to shipped, verified software — each step writing a reviewable artifact and handing off to the next. Test-driven development is the core build loop, with `node:test` for unit and functional tests and an end-to-end gate that retriggers the build until the app actually works.

```
 DESIGN ───────────────────────────►  PLAN ──►  BUILD ─────────────────────────────────►  SHIP
 brainstorming → technical-documentation → planning → project-setup → executing-plans → shipping
 docs/designs/   docs/technical/           docs/plans/  scaffold repo    │                  PR
                 ▲                                                        │
                 └─ refining-docs (tune the docs)                        ▼
                                                    ┌─ test-driven-development (inner loop)
                                                    │     RED → GREEN → REFACTOR (node:test)
                                                    │            ▲
                                                    │  ✗ fail    │ re-enter build
                                                    └─ end-to-end-testing (outer verify) ─┘
                                                          ✓ pass → shipping
```

- **Living source of truth** lives in `docs/technical/`; **dated history** in `docs/designs/` and `docs/plans/`.
- **`test-driven-development`** is the inner loop — every requirement gets a failing `node:test` first, then code, until green.
- **`end-to-end-testing`** is the outer gate — if the running app fails its acceptance criteria, it files fix tasks and sends control back into the build loop.

Install all of them for structured feature work (interactive picker):

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```

Or install individually:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill technical-documentation --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill refining-docs --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill project-setup --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill test-driven-development --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill end-to-end-testing --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill shipping --global
```

### Documentation layout

The pipeline skills read and write to a single `docs/` directory at the project root. The layout separates **living** documents (always reflect the current system) from **point-in-time** records (a decision captured on a date):

```
docs/
  designs/      # brainstorming specs — point-in-time, dated   2026-06-29-checkout.md
  technical/    # technical-documentation — living, canonical  architecture.md, data-model.md …
  plans/        # planning — point-in-time, dated              2026-06-29-checkout.md
```

| Folder | Written by | Lifecycle | Filenames |
|---|---|---|---|
| `docs/designs/` | brainstorming | Point-in-time — the *what and why* at decision time | `YYYY-MM-DD-<name>.md` |
| `docs/technical/` | technical-documentation, refining-docs | **Living** — the canonical source of truth, kept current | `<doc-name>.md` (no date) |
| `docs/plans/` | planning | Point-in-time — the task list for one piece of work | `YYYY-MM-DD-<name>.md` |

Rules of thumb:

- **`docs/technical/` is the source of truth.** No dates in its filenames — `architecture.md` is *always* the current architecture. `planning` reads it before building; `refining-docs` keeps it accurate as the project evolves.
- **`designs/` and `plans/` are history.** Dated filenames make it clear they record a moment, not the present state.
- Use the user's preferred location if they specify one; otherwise default to these paths.

---

## Repository structure

```
skills/
  general-writing/
    SKILL.md          # required — agent instructions
    README.md         # human-readable docs and install guide
  my-skill/
    SKILL.md
    README.md
```

---

## Anatomy of a Skill

Each skill is a folder under `skills/`. The folder name becomes the skill identifier used with `--skill` when installing.

```
skills/
  my-skill/
    SKILL.md          # required — read by the agent
    README.md         # optional — human docs (installed but not read by the agent)
    helper.sh         # optional — supporting files installed with the skill
```

### SKILL.md

The file the agent reads. It has two parts: YAML frontmatter and a markdown body.

**Frontmatter** tells the agent when to activate the skill:

```yaml
---
name: my-skill
description: >-
  What this skill does and when to use it. Be specific — the agent uses this
  to decide whether to apply the skill to a given task.
---
```

| Field | Required | Purpose |
|---|---|---|
| `name` | Yes | Skill identifier. Should match the folder name. |
| `description` | Yes | Trigger text. Describe what the skill does and the scenarios where it applies. |

**Body** contains the instructions the agent follows once the skill is active. Structure it however fits the task — common sections include:

- A brief overview of the goal
- Step-by-step workflow
- Rules, constraints, or standards to follow
- Output format (what to return and what to leave out)

See [general-writing/SKILL.md](./skills/general-writing/SKILL.md) for a working example.

### README.md

Human-readable documentation for the skill. Covers purpose, usage examples, and install options. This file is copied during install alongside `SKILL.md`, but agents load `SKILL.md` by convention and ignore `README.md`.

### Supporting files

You can include scripts, templates, or config files alongside `SKILL.md` in the skill folder. `@levi-putna/agent-kit` installs all top-level files in the folder when a skill is added. Nested subdirectories are not fetched.

### What gets installed

When someone runs `npx @levi-putna/agent-kit@latest add levi-putna/skills --skill my-skill`, every file in `skills/my-skill/` is copied to the agent's skills directory — typically `~/.claude/skills/my-skill/` (global) or `.agents/skills/my-skill/` (project). The agent reads `SKILL.md`; other files are available as reference or for scripts the skill invokes.

---

## License

MIT
