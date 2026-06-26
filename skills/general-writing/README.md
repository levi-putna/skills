# general-writing

Rewrite or draft text in clear, natural language. This skill guides an agent to produce prose that reads like a person wrote it, not a language model.

Use it when you want copy edited, simplified, rewritten, or drafted from scratch with a direct, human tone.

## What it does

The agent will:

- Use clean, simple language and avoid ornate words unless the context needs them
- Spell out acronyms on first use unless they are already widely understood (API, URL, HTML, etc.)
- Strip filler transitions, AI clichés, excessive hedging, and vague adjectives
- Mix short and long sentences for natural rhythm
- Favour concrete details over generic language
- Vary paragraph length and structure
- Return only the rewritten text with no explanation

See [SKILL.md](./SKILL.md) for the full set of rules the agent follows.

## When to use it

Good fits:

- Rewriting AI-generated drafts so they sound human
- Simplifying overly formal or jargon-heavy copy
- Editing blog posts, emails, docs, or marketing text
- Drafting new content with a natural, slightly informal tone

## Install

Uses [@levi-putna/agent-kit](https://github.com/levi-putna/agent-kit) via `npx` — no global install required.

### Global install

Available across all projects via Claude Code and the Claude desktop app:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill general-writing --global
```

Skills are written to `~/.claude/skills/`.

### Project install

Install into the current project for detected agents (Cursor, Claude Code, Windsurf, etc.):

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills --skill general-writing --project
```

Skills are written to `.agents/skills/` with symlinks from each agent directory. An `agent-kit-lock.json` is created so teammates can reproduce the setup.

### Interactive install

Pick skills from a list instead of specifying the name:

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills
```

### Install from a specific branch

```sh
npx @levi-putna/agent-kit@latest add levi-putna/skills@develop --skill general-writing
```

### Manual install

Copy the skill folder into your agent's skills directory:

| Agent | Path |
|---|---|
| Claude Code (global) | `~/.claude/skills/general-writing/` |
| Claude Code (project) | `.claude/skills/general-writing/` |
| Cursor | `.cursor/skills/general-writing/` |
| Windsurf | `.windsurf/skills/general-writing/` |

Copy the entire `general-writing` folder including `SKILL.md`.

## Example

**Before:**

> Furthermore, it is worth noting that our multifaceted platform allows users to delve into a tapestry of features that unlock the potential of modern workflows in today's world.

**After:**

> Our platform covers a lot of ground. You get task management, team chat, and file sharing in one place, which saves most teams from juggling three or four separate tools.

## Uninstall

```sh
npx @levi-putna/agent-kit@latest remove general-writing
```
