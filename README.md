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
| [planning](./skills/planning/) | Turn an approved design into a TDD implementation plan with exact tasks. | [Install guide](./skills/planning/README.md#install) |
| [executing-plans](./skills/executing-plans/) | Execute a plan task-by-task with verification and review checkpoints. | [Install guide](./skills/executing-plans/README.md#install) |

### Design-to-ship workflow

Install all three for structured feature work (interactive picker):

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --global
```

Or install individually:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill brainstorming --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill planning --global
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill executing-plans --global
```

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
