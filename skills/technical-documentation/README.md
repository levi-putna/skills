# technical-documentation

Turn an approved design into a production-ready set of technical documents an AI agent can build from and a human can review.

This is the step between **brainstorming** (the *what and why*) and **planning** (the TDD task list). It produces the *how*: architecture, data model, tech-stack decisions, API contracts, NFRs, and engineering guidelines — as standalone `.md` files with Mermaid diagrams and decision tables.

```
brainstorming  →  technical-documentation  →  planning  →  executing-plans
docs/designs/      docs/technical/             docs/plans/
```

## What it does

- **Reads what exists first** — the brainstorming spec, README, any product docs — and reconciles rather than overwrites.
- **Scales the doc set to the project.** A CLI tool gets four docs; a SaaS gets all eight. It proposes the subset and confirms before drafting.
- **Tags every technical decision** as 🔒 human-locked, 🤖 agent-discretion, or ❓ needs-decision — so it's always explicit whether the human or the agent owns a choice. No project leaves the skill with open decisions.
- **Reviews section by section**, then a final read-the-files gate before handing off to planning.
- **Diagrams in Mermaid, decisions in tables** — both render on GitHub and stay editable by humans and agents.

## The documents

| Doc | Owner | Purpose |
|---|---|---|
| `product-brief.md` | Human | Vision, users, problem, success metrics |
| `requirements.md` | Human | Features, user stories, acceptance criteria, out-of-scope |
| `architecture.md` | AI-drafted, human-tuned | System overview, components, data flow (Mermaid) |
| `tech-stack.md` | Mixed | Every tech choice with owner tag + ADRs |
| `data-model.md` | AI-drafted, human-tuned | Entities, relationships (Mermaid ER) |
| `api-contracts.md` | AI-drafted, human-tuned | Endpoints, payloads, errors |
| `nfr.md` | Human-guided | Performance, security, scale, accessibility — measurable |
| `engineering-guidelines.md` | Mixed | Conventions, testing — seeds `CLAUDE.md`/`AGENTS.md` |

## When to use it

- Starting a new project or major feature that an AI agent will build
- You have a design or rough requirements and need them turned into buildable technical docs
- After brainstorming, before planning

To tune or extend the docs later, use the **refining-docs** skill.

## Install

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill technical-documentation --global
```

Or as part of the full design-to-ship workflow:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```
