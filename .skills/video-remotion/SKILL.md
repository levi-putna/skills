---
name: video-remotion
id: 90ae116e-4348-4c1a-b646-308b70c522b7
version: 1.0.0
author: Levi Putna
repo: https://github.com/levi-putna/skills
description: >-
  End-to-end pipeline for producing a narrated, multi-scene explainer video
  with Remotion inside a Next.js project: audio-first script → scene plan →
  shared theme/components → ElevenLabs narration with timestamp alignment →
  build each scene as a real Remotion component (previewed live in Remotion
  Studio - no separate storyboard-image step) → assemble with Series/
  TransitionSeries timed exactly to the narration → one deterministic render
  (no clip concatenation). Use when asked to create an explainer video,
  product walkthrough video, UI/feature explainer video, or narrated demo
  video using Remotion, or to revise a scene in an existing Remotion video
  production.
dependencies:
  - type: env
    name: ELEVENLABS_API_KEY
    required: true
    description: Needed for narration via the ElevenLabs text-to-speech API.
    instructions: >-
      Create a key at https://elevenlabs.io, then add
      ELEVENLABS_API_KEY=... to the project .env file.
  - type: env
    name: ELEVENLABS_VOICE_ID
    required: true
    description: The ElevenLabs voice used for narration (keep it consistent per project).
  - type: env
    name: ELEVENLABS_MODEL_ID
    required: false
    description: Override the ElevenLabs TTS model. Defaults to eleven_multilingual_v2.
---

# Video: Remotion Explainer

Produce a narrated, multi-scene video with [Remotion](https://www.remotion.dev)
(React components rendered to video) inside a Next.js project, with **user
approval at every gate**.

This is the code-native sibling of a storyboard/AI-video pipeline: instead of
prompting a generative video model per scene, you **write the scene as a
React component** and render it deterministically. That changes the pipeline
in three important ways versus a Veo/Sora-style approach - read this before
assuming the old mental model:

1. **Continuity is structural, not descriptive.** You don't write a
   paragraph describing the desk/background/props so a black-box model can
   *try* to keep them consistent. You put the recurring visual in **one
   shared component** and every scene imports it. It is pixel-identical by
   construction. See [references/design-and-continuity.md](references/design-and-continuity.md).
2. **Duration is exact, not quantised.** There's no "durations must be 4s or
   8s" constraint. A scene's `durationInFrames` is computed directly from
   how long its narration actually takes to say, per the ElevenLabs
   alignment timestamps. See [references/elevenlabs-narration-sync.md](references/elevenlabs-narration-sync.md).
3. **Storyboarding and clip generation collapse into one step.** Remotion
   Studio *is* the storyboard - it's an exact, instant, editable preview of
   the real output. There's no separate low-fidelity sketch step, and no
   "regenerate and hope it obeys the prompt this time" - you edit code and
   it hot-reloads.

Defaults: aim for ~60-90s (confirm target length with the user if the request
implies a longer walkthrough). **16:9** unless the user asks for a vertical
format. Aesthetic direction (palette, type, motion language) is **not
prescribed by this skill** - Gate 3 defines it per-project.

## Hard rules

1. **Stop at every gate.** Present output; wait for approval or revision
   requests before moving on.
2. **End every gate with the status block** (This step + Files + Progress
   checklist). Never omit it.
3. **All animation is driven by `useCurrentFrame()`.** CSS transitions/
   animations and Tailwind animation classes are forbidden - they do not
   render correctly. See [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md).
4. **Narration is one continuous ElevenLabs synthesis**, not one call per
   scene - this keeps the speech cadence natural across scene boundaries.
   Scene timings are *derived from* that single alignment, never the
   other way around.
5. **Never redefine a recurring visual per-scene.** If two or more scenes
   share an element (a mock browser chrome, a card, a logo, a cursor), it
   lives once in `shared/` and every scene imports it.
6. **Self-audit with a real rendered still before presenting a scene** - use
   `npx remotion still` to capture the scene's start/mid/end frames and check
   them against [references/design-and-continuity.md](references/design-and-continuity.md)
   before showing it at a gate. This is cheap and exact in Remotion; there's
   no excuse to skip it the way a paid per-generation model pipeline might.

## Prerequisites

| Variable | Required | Purpose |
|----------|----------|---------|
| `ELEVENLABS_API_KEY` | Yes | Narration synthesis + timestamp alignment |
| `ELEVENLABS_VOICE_ID` | Yes | Consistent VO voice for this project |
| `ELEVENLABS_MODEL_ID` | No | Default `eleven_multilingual_v2` |

Remotion itself has no API key - it's an npm dependency. Gate 0 (below)
checks/installs it. See [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
for the exact packages and Next.js folder conventions this skill assumes.

## Working directory (per production)

Every production gets its own folder, named by a slug derived from the
video's title:

```
remotion/
  index.ts                          # registerRoot() - created once, shared by all productions
  Root.tsx                          # registers one <Composition> per production - created once, one line added per production
  productions/{slug}/
    script.md                       # Gate 1
    scenes.json                     # Gate 2, timing fields filled at Gate 4
    Composition.tsx                 # Gate 6 - assembles scenes into the final timeline
    scenes/
      01-{name}.tsx                 # Gate 5
      02-{name}.tsx
    shared/
      theme.ts                      # Gate 3
      {SharedComponent}.tsx          # Gate 3
    audio/
      alignment.json                # Gate 4 - intermediate, not needed at render time
public/
  video/{slug}/
    narration.mp3                   # Gate 4 - Remotion assets resolve through the public folder, so it lives here even though visitors never fetch it directly
    final.mp4                       # Gate 6 output
```

`remotion/` sits at the project root next to `app/`/`src/`, per the standard
"add Remotion to an existing Next.js project" setup - see
[references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md).
Confirm the slug with the user if it's ambiguous from the title.

## Gate status block (required)

Close every gate message with:

1. **This step** - 1-2 sentences on what was done / what to review.
2. **Files** - markdown links to files created or updated in *this* gate only.
3. **Progress checklist** - exact format; mark completed `[x]`, pending `[ ]`,
   and append `← Current` on the gate awaiting approval.

```
### This step
{Short description of what was produced and what you want reviewed.}

### Files
- [{label}]({path})
- …

Video production progress:
- [x] Gate 0 - Remotion project setup confirmed
- [x] Gate 1 - Script approved
- [ ] Gate 2 - Scene plan approved  ← Current
- [ ] Gate 3 - Theme + shared components approved
- [ ] Gate 4 - ElevenLabs narration + timing sync approved
- [ ] Gate 5 - Scene components approved
- [ ] Gate 6 - Final render approved
```

## Pipeline (gates)

### Gate 0 - Project setup

1. Check whether the project already has a Remotion entry point
   (`remotion/index.ts` calling `registerRoot`). If yes, skip to step 3.
2. If not, scaffold it per [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md):
   install the required packages, create `remotion/index.ts` and
   `remotion/Root.tsx`, and confirm `npx remotion studio remotion/index.ts`
   boots. Do this once per project, not once per production.
3. Create `remotion/productions/{slug}/` and `public/video/{slug}/`.
4. No status block needed if this was a no-op (entry point already existed) -
   just note it in the Gate 1 message. If you scaffolded anything, show what
   was added before moving on.

### Gate 1 - Script

1. Confirm the topic, target length, and audience/tone with the user if not
   already clear from the request (this skill does not assume a house tone -
   ask once, then keep it consistent for the whole production).
2. Write `script.md` as an **audio-first VO script**. Read
   [references/audio-first-script.md](references/audio-first-script.md) in
   full before writing - it covers pacing, breath points, ElevenLabs
   `<break>` tag usage, and speakable wording (no code/selectors read aloud).
3. **Read the script aloud yourself** (or silently mouth it). If it sounds
   like copy on a page, rewrite until it sounds like someone talking.
4. **Present script** (full text or clear excerpt + path). **End with the
   gate status block. Wait.**

### Gate 2 - Scene plan

1. Break the approved narration into scenes **on spoken beats**, not
   arbitrary word counts. Do not guess durations yet - final
   `durationInFrames` is computed from real audio in Gate 4.
2. Preserve the approved script's wording and cadence - don't tighten copy
   into stiffer on-screen slogans when splitting it.
3. Write `scenes.json`. Per scene:

   ```json
   {
     "id": "03",
     "title": "Problem - tabbing blind",
     "component": "scenes/03-TabbingBlind.tsx",
     "narration": "You hit Tab. Then Tab again. Nothing highlights.",
     "keyPoint": "The one idea this scene must land - nameable in ~1 second",
     "onScreenText": "",
     "transitionIn": { "type": "fade", "durationInFrames": 15 },
     "visualNotes": "Free-text plan for what the scene shows - just planning notes, not a rigid prompt contract. The component code is the real spec once Gate 5 starts."
   }
   ```

   Top-level fields: `slug`, `title`, `aspectRatio`, `fps`, `width`, `height`.
   Do **not** add per-scene continuity/consistency fields (background
   description, prop list, palette notes) - that's handled once, structurally,
   at Gate 3. See [references/design-and-continuity.md](references/design-and-continuity.md)
   for what belongs in a scene plan versus what belongs in shared code.
4. Concatenated `narration` fields (space-joined, in order) must equal the
   approved body script exactly - this string is what gets sent to
   ElevenLabs in Gate 4, and scene timings are derived by locating each
   scene's substring inside the full alignment.
5. Apply the **one idea, one focus** discipline from
   [references/design-and-continuity.md](references/design-and-continuity.md):
   `keyPoint` must be nameable in about a second. Split a scene if it's
   carrying two ideas.
6. **Present the scene table** (id, title, keyPoint, one-line narration
   excerpt). **End with the gate status block. Wait.**

### Gate 3 - Theme + shared components

1. Scan `scenes.json` for anything that appears in more than one scene (a
   mock UI chrome, a card, a cursor, a logo, a recurring layout shell) and
   list it before writing any code.
2. Create `shared/theme.ts` - design tokens (colour palette, font choices via
   `@remotion/google-fonts` or local fonts, spacing scale, motion presets
   e.g. named `spring()` configs for "enter", "exit", "emphasis"). This is
   the *only* place these values are defined; scenes import from it, never
   hardcode a hex value or font name inline.
3. Create one component per recurring element in `shared/` (e.g.
   `shared/BrowserChrome.tsx`, `shared/Cursor.tsx`). Keep them dumb/reusable -
   props in, no scene-specific logic.
4. If the project already has its own design tokens/component library
   (check for an existing `theme`/`tokens` module or a design-system skill's
   output), prefer wiring `theme.ts` to re-export from there over inventing a
   parallel palette.
5. Render a quick still of one shared component in isolation if useful to
   confirm it looks right (`npx remotion still`, per
   [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)).
6. **Present the theme + shared component list** (link each file). **End
   with the gate status block. Wait.**

### Gate 4 - ElevenLabs narration + timing sync

Full mechanics (API call, alignment→scene-timing math, optional captions) are
in [references/elevenlabs-narration-sync.md](references/elevenlabs-narration-sync.md).
Read it before this gate; it is not optional prep.

1. Concatenate all scene `narration` fields (space-joined) and call
   ElevenLabs' timestamped-synthesis endpoint once for the full body script.
2. Save the decoded audio to `public/video/{slug}/narration.mp3` and the raw
   alignment response to `productions/{slug}/audio/alignment.json`.
3. Walk the alignment to find each scene's `audioStartSeconds`/
   `audioEndSeconds` by locating its narration substring in the full text,
   then write `durationInFrames = round((end - start) * fps)` back into
   `scenes.json` for every scene (add a small lead-out, e.g. 3-5 frames, so
   a scene doesn't cut the instant speech stops - don't add lead-in, the
   next scene's audio is already playing).
4. If the user asked for captions, generate the `Caption[]` array from the
   same alignment now (see the reference doc) and save it to
   `productions/{slug}/captions.ts`. Skip this step if captions weren't
   requested.
5. **Listen to the narration** (or check word timing sanity from the
   alignment) - if it rushes or mispaces, revise the script at Gate 1/2 and
   re-run this gate before approving.
6. **Present the timing table** (scene id → narration excerpt →
   start/end seconds → `durationInFrames`) plus a link to the audio.
   **End with the gate status block. Wait before building scenes.**

### Gate 5 - Scene components

Build one Remotion component per scene under `scenes/`, in order.

1. Each component imports what it needs from `shared/theme.ts` and
   `shared/*.tsx` - never redefines a recurring visual locally.
2. Drive all motion from `useCurrentFrame()` / `interpolate()` / `spring()` -
   see [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
   for the animation primitives this skill relies on (timing curves,
   sequencing, transitions, text animations, captions, fonts, assets). For
   anything not covered there, consult the Remotion docs directly
   (https://www.remotion.dev/docs) or the official `remotion` skill
   (https://github.com/remotion-dev/skills) - don't guess at an unfamiliar
   API.
3. **Self-audit before presenting** - render stills at the scene's start,
   middle, and end frame:

   ```bash
   npx remotion still remotion/index.ts {slug} out/{sceneId}-start.png --frame={startFrame}
   ```

   Look at each still and check it against the relevance / one-focus /
   hierarchy / UI-layout-realism checklist in
   [references/design-and-continuity.md](references/design-and-continuity.md).
   Fix the component and re-render the still if it fails - this is a code
   edit, not a re-roll, so there's no reason to present a failing scene.
4. **Present each scene** (link the stills, or a short screen recording if
   available) as you go, or batch a few scenes together if they're
   straightforward - use judgement, but don't build all scenes silently and
   present them all at once for a long production. **End with the gate
   status block after the batch you're presenting. Wait.**

### Gate 6 - Assemble + render

1. Build `Composition.tsx`: a `<TransitionSeries>` (or `<Series>` if no
   scene uses a transition) that lays out every scene in order using the
   `durationInFrames` computed at Gate 4, plus **one** root-level
   `<Audio src={staticFile('video/{slug}/narration.mp3')} />` - not one
   audio element per scene. See
   [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
   for `<TransitionSeries>`/`<Audio>` usage and how transition overlap
   affects total duration.
2. Register the composition once in `Root.tsx`:
   `<Composition id="{slug}" component={Composition} .../>` with
   `durationInFrames` set to the sum of scene durations minus transition
   overlaps (or computed via `calculateMetadata` from the narration file's
   duration - see the reference doc for both approaches).
3. Render:

   ```bash
   npx remotion render remotion/index.ts {slug} public/video/{slug}/final.mp4
   ```

4. **Present** the final file with a link. Note total duration, scene count,
   and voice used. **End with the gate status block** (all gates `[x]` when
   approved). Wait for final sign-off.

## Revisions

Because everything is code, a revision never means "regenerate and hope it's
right this time":

| Change requested | What to do |
|---|---|
| "Make the error bigger" / "less clutter" / "different motion" | Edit that scene's `.tsx` directly. Re-render a still to confirm, then re-run the Gate 6 render command. No other scene is touched. |
| "Match the previous scene more" / "hard cut instead" | Adjust that scene's `transitionIn` in `scenes.json` and the corresponding `<TransitionSeries.Transition>` in `Composition.tsx`. |
| Wording of the narration changes | Update `script.md` + the affected scene's `narration` in `scenes.json`, then **redo Gate 4** (re-synthesize, since timings shift), then re-render. |
| A shared/recurring element needs to change | Edit the one component in `shared/` - every scene using it updates automatically. Never patch the same visual separately in each scene file. |
| Voice-over only (same script) | Redo Gate 4 with the new voice/model, keep scene components as-is, re-render. |

Present the updated still/render and wait for approval before moving on -
same as any other gate, just scoped to the one thing that changed.

## Do not

- Skip approval gates, or present a gate without the status block.
- Call ElevenLabs once per scene - it must be one synthesis call for the
  full body script, so cadence flows naturally across scene boundaries.
- Snap scene durations to a fixed bucket (e.g. "must be 4 or 8 seconds") -
  that constraint belonged to a fixed-duration generative video model, not
  to Remotion. Use the exact duration the narration needs.
- Redefine a recurring visual element inside more than one scene file.
- Use CSS transitions/animations or Tailwind animation classes for motion.
- Present a scene without rendering and checking a still against
  [references/design-and-continuity.md](references/design-and-continuity.md) first.
- Invent a house aesthetic (this skill is intentionally style-agnostic) -
  confirm direction with the user at Gate 3 instead of defaulting to one.
- Quietly re-render an already-approved final without being asked.

## Additional resources

- Script pacing + speakable wording: [references/audio-first-script.md](references/audio-first-script.md)
- Continuity, relevance, hierarchy, UI-layout-realism checklist: [references/design-and-continuity.md](references/design-and-continuity.md)
- ElevenLabs timestamped synthesis, scene-timing math, captions: [references/elevenlabs-narration-sync.md](references/elevenlabs-narration-sync.md)
- Next.js project setup, packages, Remotion primitives used by this skill: [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
- Remotion docs: https://www.remotion.dev/docs
- Official Remotion API skill (deeper reference for anything not covered above - 3D, charts, Lottie, Tailwind, video trimming, etc.): https://github.com/remotion-dev/skills
