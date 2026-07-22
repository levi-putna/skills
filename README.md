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

See [`.skills/dotskills-create-skill/SKILL.md`](.skills/dotskills-create-skill/SKILL.md) for how to author a new skill, and [`.skills/dotskills-import-skill/SKILL.md`](.skills/dotskills-import-skill/SKILL.md) for migrating existing agent-native instructions (`.cursorrules`, `AGENTS.md` sections, etc.) into this format.

## Structure

```
.skills/
  <domain>-<action>-<topic>/
    SKILL.md        # required — frontmatter + instructions
    README.md        # optional — human-facing docs
    references/       # optional — long-form docs loaded only when needed
    scripts/          # optional — helper scripts
    assets/           # optional — templates/files used in output
```

`dot-skills` (and every consuming agent) only scans the immediate children of `.skills/` — no nested category folders. Every skill name follows dot-skills' `<domain>-<action>-<topic>` naming convention (lowercase, hyphen-separated, no other punctuation) — see [`.skills/dotskills-create-skill/SKILL.md`](.skills/dotskills-create-skill/SKILL.md) for the full rules and examples. A skill's name is part of its public interface once installed elsewhere via `dot-skills add`, so don't rename one lightly after publishing it.

## Skills

Each skill below can be installed on its own — copy its command and run it in the target project.

### UI

#### [ui-audit-laws](.skills/ui-audit-laws/)

Audit a page/route/component against research-backed UX laws, Web Interface Guidelines, and design distinctiveness (catches generic/"AI slop" aesthetics); scored report with blocking/major/minor issues and fixes.

```sh
npx dot-skills add levi-putna/skills/ui-audit-laws
```

#### [ui-audit-responsiveness](.skills/ui-audit-responsiveness/)

Audit pages against responsive-design practice — breakpoints, mobile vs. collapsible content, touch targets — with a severity-ranked report.

```sh
npx dot-skills add levi-putna/skills/ui-audit-responsiveness
```

#### [component-document-contract](.skills/component-document-contract/)

Write or update a component's contract doc as a Markdown file next to its source (`button.tsx` + `button.md`) — purpose, props/events API, behaviour, accessibility, states, variants, and usage examples, all derived from the actual code.

```sh
npx dot-skills add levi-putna/skills/component-document-contract
```

### SEO

#### [seo-audit-page](.skills/seo-audit-page/)

Review or fix on-page/technical SEO — metadata, canonical URLs, structured data, sitemap/robots, headings, alt text — reusing the project's existing helpers.

```sh
npx dot-skills add levi-putna/skills/seo-audit-page
```

### Video

#### [video-generate-explainer](.skills/video-generate-explainer/)

End-to-end pipeline for a narrated UI/product-style explainer or example video built with Remotion (default ~1 minute, no fixed length cap - Gate 1 sets the target from content; Veo clip limits apply only to individual generated clips, not the overall production): audio-first script → scene plan (flagging any scene that could use Veo-generated or real video) → shared theme/components + background strategy (including feature/page reconstruction and subtle camera zoom/focus) → ElevenLabs narration with timestamp sync → scene components/generated clips → single deterministic render → automated critic pass on brief fit, UI/animation clarity, and script delivery. Gate-by-gate approval workflow. Supports reconstructing an existing app page/feature in Remotion so it reads like a polished screen recording. Not for realistic/live-action video or literal live-app captures.

```sh
npx dot-skills add levi-putna/skills/video-generate-explainer
```

## License

MIT
