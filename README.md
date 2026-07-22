# Skills

Personal library of [Agent Skills](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) — reusable, agent-native instructions — managed with [dot-skills](https://www.npmjs.com/package/dot-skills).

`.skills/` is the single source of truth. Every skill lives there once and gets symlinked out to whichever coding agent needs it (Cursor, Claude Code, etc.) via `dot-skills link`. Edit a skill in one place; every agent sees the update immediately.

These skills assume the target project is a **Next.js (App Router)** codebase — file/path conventions, framework-specific APIs, and setup steps are written against that assumption rather than kept generic across frameworks.

## Install

```sh
# Everything in this repo
npx dot-skills add levi-putna/skills

# A single skill
npx dot-skills add levi-putna/skills/<skill-name>

# Browse what's here before installing
npx dot-skills list levi-putna/skills
```

Add `--global` to install into `~/.dot-skills/skills/` instead of the current project.

## Working in this repo

```sh
npx dot-skills init          # first time: creates .skills/, links into agents you pick
npx dot-skills link          # re-link after adding/renaming a skill
npx dot-skills list          # list local skills
npx dot-skills doctor        # check declared dependencies (env vars / CLI tools)
```

See [`.skills/creating-skills/SKILL.md`](.skills/creating-skills/SKILL.md) for how to author a new skill, and [`.skills/importing-skills/SKILL.md`](.skills/importing-skills/SKILL.md) for migrating existing agent-native instructions (`.cursorrules`, `AGENTS.md` sections, etc.) into this format.

## Structure

```
.skills/
  <category>-<skill-name>/
    SKILL.md        # required — frontmatter + instructions
    README.md        # optional — human-facing docs
    references/       # optional — long-form docs loaded only when needed
    scripts/          # optional — helper scripts
    assets/           # optional — templates/files used in output
```

`dot-skills` (and every consuming agent) only scans the immediate children of `.skills/` — no nested category folders. Skills are grouped instead by a **category prefix** on the folder name, e.g. `ui-`, `nextjs-`, `database-`, `seo-`. Keep prefixes short and consistent; introduce a new one only when a real cluster of skills justifies it.

## Skills

Each skill below can be installed on its own — copy its command and run it in the target project.

### UI

#### [ui-laws-audit](.skills/ui-laws-audit/)

Audit a page/route/component against research-backed UX laws, Web Interface Guidelines, and design distinctiveness (catches generic/"AI slop" aesthetics); scored report with blocking/major/minor issues and fixes.

```sh
npx dot-skills add levi-putna/skills/ui-laws-audit
```

#### [ui-responsive-layout-audit](.skills/ui-responsive-layout-audit/)

Audit pages against responsive-design practice — breakpoints, mobile vs. collapsible content, touch targets — with a severity-ranked report.

```sh
npx dot-skills add levi-putna/skills/ui-responsive-layout-audit
```

#### [documenting-components](.skills/documenting-components/)

Write or update a component's contract doc as a Markdown file next to its source (`button.tsx` + `button.md`) — purpose, props/events API, behaviour, accessibility, states, variants, and usage examples, all derived from the actual code.

```sh
npx dot-skills add levi-putna/skills/documenting-components
```

### SEO

#### [seo](.skills/seo/)

Review or fix on-page/technical SEO — metadata, canonical URLs, structured data, sitemap/robots, headings, alt text — reusing the project's existing helpers.

```sh
npx dot-skills add levi-putna/skills/seo
```

### Video

#### [video-remotion](.skills/video-remotion/)

End-to-end pipeline for a narrated, multi-scene explainer video built with Remotion: audio-first script → scene plan → shared theme/components → ElevenLabs narration with timestamp sync → scene components → single deterministic render. Gate-by-gate approval workflow.

```sh
npx dot-skills add levi-putna/skills/video-remotion
```

## License

MIT
