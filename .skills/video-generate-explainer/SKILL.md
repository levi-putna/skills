---
name: video-generate-explainer
id: 90ae116e-4348-4c1a-b646-308b70c522b7
version: 2.3.0
author: Levi Putna
repo: https://github.com/levi-putna/skills
description: >-
  End-to-end pipeline for producing a narrated explainer/example video with
  Remotion inside a Next.js project, built around UI/product-style visuals.
  Target length defaults to around 1 minute but is fully flexible - Gate 1
  sets the real target from the actual content, and the Remotion production
  itself has no fixed length cap. Any short-clip limit (Veo's fixed
  4/6/8-second generations) only ever applies to an individual
  AI-generated video clip used inside the timeline, never to the overall
  production. Pipeline: an upfront planning-brief gate (topic,
  theme, target length, target platform(s), and aspect ratio/format(s) -
  Remotion can target more than one format from a single production) →
  audio-first script → scene plan (flagging any scene that could use
  generated or real video) → shared theme/components (reusing/promoting
  from a cross-production component library, with video-specific guidance
  for charts/data viz and UI/UX rendering) + background strategy →
  ElevenLabs narration with timestamp alignment → build each scene as a
  Remotion component, a short Veo-generated clip, or (rarely) a
  user-supplied real clip for an intro/outro → assemble with
  Series/TransitionSeries timed exactly to the narration → one deterministic
  render per format → an automated critic pass on brief fit, UI/animation
  clarity, and script delivery before sign-off. Use when asked to create an
  explainer video, product/feature teaser, UI walkthrough clip, marketing
  example, or demo snippet using Remotion, whether that's a punchy
  15-20 second teaser or a multi-minute walkthrough. Do NOT use this skill
  for realistic/live-action video productions or anything where the primary
  visual is real camera footage rather than UI/code-driven graphics -
  screen-recorded tutorials, documentary-style long-form footage, or any
  production whose primary visual isn't UI/code-driven graphics are a
  different problem this skill does not solve.
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
  - type: env
    name: AI_GATEWAY_API_KEY
    required: false
    description: >-
      Needed only if the approved scene plan uses Veo-generated video (Gate 3)
      or an AI-generated static background/support image (Gate 4). Not needed
      for a production that only uses Remotion components on a plain or
      CSS/React-generated background. If not set, generated-video scenes and
      AI-generated images are unavailable - the pipeline falls back to
      component scenes, user-supplied real-video (if provided), and blank or
      CSS/React-generated backgrounds only; flag this to the user at Gate 0/1
      rather than discovering it mid-pipeline.
    instructions: >-
      Create a key in the Vercel dashboard under AI Gateway → API Keys, then
      add AI_GATEWAY_API_KEY=... to the project .env file. OIDC via
      `vercel env pull` also works if the project is linked to Vercel.
  - type: env
    name: AI_GATEWAY_VIDEO_MODEL
    required: false
    description: >-
      Overrides the video-generation model used for generated-video scenes.
      Defaults to google/veo-3.1-generate-001. Has no effect unless
      AI_GATEWAY_API_KEY is also set and a generated-video scene is approved.
  - type: env
    name: AI_GATEWAY_IMAGE_MODEL
    required: false
    description: >-
      Overrides the image-generation model used for generated background/
      support images. Defaults to openai/gpt-image-2. Has no effect unless
      AI_GATEWAY_API_KEY is also set and an AI-generated image is approved.
---

# Video: Generate Explainer

Produce a narrated explainer/example video with
[Remotion](https://www.remotion.dev) (React components rendered to video)
inside a Next.js project, with **user approval at every gate**. This is for
**UI/product-style** videos - feature teasers, "how it works" clips,
marketing examples, demo snippets, fuller product walkthroughs - not for
realistic/live-action productions. If the ask is fundamentally a real-camera
video (an interview, a testimonial, b-roll editing), this is the wrong
skill. Length is not what determines fit here: a 15-second teaser and a
5-minute walkthrough both belong in this skill as long as the visuals are
UI/code-driven rather than real camera footage.

This is the code-native sibling of a storyboard/AI-video pipeline: instead of
prompting a generative video model for every shot, you **write most scenes
as a React component** and render deterministically, reaching for a
generative video model (Veo, via AI Gateway) or real footage only where a
code-drawn scene genuinely can't do the job. That changes the pipeline in a
few important ways versus a pure Veo/Sora-style approach - read this before
assuming the old mental model:

1. **Continuity is structural, not descriptive.** You don't write a
   paragraph describing the desk/background/props so a black-box model can
   *try* to keep them consistent. You put the recurring visual in **one
   shared component** and every scene imports it. It is pixel-identical by
   construction. See [references/design-and-continuity.md](references/design-and-continuity.md).
2. **Duration is exact, not quantised, for component scenes.** A component
   scene's `durationInFrames` is computed directly from how long its
   narration actually takes to say, per the ElevenLabs alignment timestamps.
   Generated/real video scenes are the exception - their duration is fixed by
   the clip itself (see Gate 3 and Gate 6). See
   [references/elevenlabs-narration-sync.md](references/elevenlabs-narration-sync.md).
3. **Storyboarding and clip generation collapse into one step for component
   scenes.** Remotion Studio *is* the storyboard - it's an exact, instant,
   editable preview of the real output, with no "regenerate and hope it
   obeys the prompt this time" loop. Generated-video scenes don't get this
   for free - each generation costs money and takes minutes, so they're used
   sparingly and only after explicit approval (Gate 3).
4. **A critic pass closes the loop.** Because every scene is dense with a
   specific job to do, a final automated review (Gate 8) checks the finished
   video against the brief, UI/animation clarity, and script delivery before
   it's ever presented as done - see
   [references/critic-review.md](references/critic-review.md).

Defaults: **target length defaults to around 1 minute, but there is no
fixed cap on the overall Remotion production.** Gate 1 sets the real target
from the actual content rather than forcing everything into one bucket - a
single punchy feature teaser might land at 15-20 seconds, a fuller
walkthrough with several distinct points might genuinely need several
minutes. If the discussed content clearly implies something shorter or
longer than the 1-minute default, say so explicitly at Gate 1 and set that
as the real target - don't quietly force-fit content into the default, and
don't quietly let a video balloon past what was agreed either. **If the
resulting target is likely to run past 5 minutes, flag it plainly at
Gate 1**: more narration means more characters sent to ElevenLabs per
synthesis call, which draws down more of the account's audio-generation
credits/quota - this is a cost heads-up for the user to confirm, not a
reason to cut content that genuinely needs the length.

**A "short clip" limit (e.g. Veo's fixed 4/6/8-second generations) only
ever describes a single `generated-video` scene, never the production as a
whole.** The overall Remotion production's runtime is driven purely by the
approved target length and the narration/scene timings within it - it is
never capped by, or confused with, the duration of any individual
AI-generated clip used inside it. If a `generated-video` beat needs to span
longer than one clip's fixed duration, generate multiple short clips back to
back for that beat rather than trying to force one long clip - and keep
preferring a Remotion component over generated video wherever the shot can
be drawn in code, regardless of how long the overall production is.

**Aspect ratio(s) are decided at
Gate 1, not assumed.** Default to a single 16:9 composition if the user has
no specific platform in mind, but Remotion can render **multiple aspect
ratios from one production** (e.g. a 16:9 for a website and a 9:16 for
social, both from the same script/narration/shared components) - always ask
rather than silently picking one. If a 9:16 format's destination is a
specific app (TikTok, Reels, Shorts, Stories), note that now - it changes
the safe-zone budget shared components need to respect, see
[references/multi-format-layout.md](references/multi-format-layout.md).
Aesthetic direction (palette, type, motion language) is **not prescribed by
this skill** - Gate 4 defines it per-project.

## Hard rules

1. **Stop at every gate.** Present output; wait for approval or revision
   requests before moving on.
2. **End every gate with the status block** (This step + Files + Progress
   checklist). Never omit it.
3. **The planning brief (Gate 1) always runs first and is never skipped**,
   even when the request seems fully specified - confirm topic, theme,
   length, platform(s), and format(s) explicitly rather than assuming.
   Every later gate reads from the approved brief instead of re-deriving or
   re-asking these.
4. **Every approved format is rendered from the same script, narration, and
   shared components** - never a separate rewritten production per aspect
   ratio. A format-specific layout difference belongs inside a scene's
   component (reading actual dimensions from `useVideoConfig()`), not a
   parallel scene plan.
5. **Total runtime matches the brief's approved target length (Gate 1) -
   there is no fixed cap on the overall production.** Check this at Gate 3
   (planned) and again at Gate 7 (actual, from real timings). If it's
   drifted meaningfully from what was approved, tighten/expand the script or
   go back to Gate 1 and re-confirm the target with the user - don't let it
   silently drift either direction unaddressed.
6. **All component-scene animation is driven by `useCurrentFrame()`.** CSS
   transitions/animations and Tailwind animation classes are forbidden - they
   do not render correctly. See [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md).
7. **Narration is one continuous ElevenLabs synthesis**, not one call per
   scene - this keeps the speech cadence natural across scene boundaries.
   Scene timings are *derived from* that single alignment, never the
   other way around (except fixed-duration video-clip scenes - see Gate 5).
8. **Never redefine a recurring visual per-scene.** If two or more scenes
   share an element (a mock browser chrome, a card, a logo, a cursor), it
   lives once in `shared/` and every scene imports it.
9. **Self-audit with a real rendered still before presenting a scene** - use
   `npx remotion still` to capture the scene's start/mid/end frames and check
   them against [references/design-and-continuity.md](references/design-and-continuity.md)
   before showing it at a gate.
10. **Real/generated video is the exception, not the default.** Prefer a
    Remotion component for anything that can be drawn in code. Generated
    (Veo) or real video may only be used where flagged and approved at
    Gate 3 - never introduced unilaterally at Gate 6. This preference holds
    regardless of the production's overall length - a longer target means
    more (or longer) component scenes, not a shift toward generated video.
    Where a `generated-video` beat needs to span longer than one Veo clip's
    fixed duration, chain multiple clips rather than defaulting to generated
    video for more of the timeline.
11. **Mute clip-native audio under the narration by default.** A
    Veo-generated clip or a real user-supplied clip may carry its own audio
    (dialogue, ambience, generated sound). Unless a scene is explicitly a
    VO-silent beat (e.g. a wordless logo outro), mute the clip's audio track
    so it doesn't collide with the continuous narration. See
    [references/video-image-generation.md](references/video-image-generation.md).
12. **The critic gate (8) is mandatory and loops until it passes**, up to 3
    fix-and-recheck iterations - see
    [references/critic-review.md](references/critic-review.md). If it still
    hasn't passed after 3 iterations, stop and hand the specific unresolved
    finding to the user instead of looping silently.
13. **Never present a rendered video without the critic report from the
    same iteration.** They're always shown together at Gate 8.
14. **Charts/data visuals default to plain React/SVG; visx is only for
    genuinely complex data viz, and its animation must still be
    frame-driven.** Never use `@visx/xychart`'s `Animated*` components or
    `react-spring` - they're wall-clock-driven, not `useCurrentFrame()`-
    driven, and break deterministic rendering the same way CSS animation
    does. See [references/data-visualization.md](references/data-visualization.md).
15. **Check `remotion/shared/` (the cross-production library) before
    writing a new shared component**, and only promote a production's
    component into it once a second real production actually needs it -
    never fork a shared component per-production. See
    [references/reusable-components.md](references/reusable-components.md).
16. **Flag likely audio-generation cost before locking in a long target.**
    If Gate 1's confirmed (or clearly emerging) target length is likely to
    exceed 5 minutes, say so explicitly before finalising the brief - more
    narration means more of the account's ElevenLabs credits/quota consumed
    per synthesis call. This is a heads-up for the user to weigh in on, not
    a gate that blocks a longer production.

## Prerequisites

| Variable | Required | Purpose | If not set |
|----------|----------|---------|------------|
| `ELEVENLABS_API_KEY` | Yes | Narration synthesis + timestamp alignment | The pipeline cannot run at all - narration is core to every production, not an optional feature. Stop and ask the user to add it before Gate 1. |
| `ELEVENLABS_VOICE_ID` | Yes | Consistent VO voice for this project | Same as above - stop and ask before Gate 1. |
| `ELEVENLABS_MODEL_ID` | No | Default `eleven_multilingual_v2` | Falls back to the default model; no feature loss. |
| `AI_GATEWAY_API_KEY` | Only if a generated-video or generated-image scene is approved | Vercel AI Gateway auth (or OIDC via `vercel env pull`) | **`generated-video` scenes and AI-generated background/support images are unavailable.** The production must stick to `component` scenes, `real-video` (only if the user supplies their own clip), and blank or CSS/React-generated backgrounds. Say this explicitly at Gate 0/1 (see below) rather than letting the user discover it when a Gate 3 idea can't be built. |
| `AI_GATEWAY_VIDEO_MODEL` | No | Default `google/veo-3.1-generate-001` | Only relevant when `AI_GATEWAY_API_KEY` is set and a `generated-video` scene is approved; otherwise falls back to the default model with no effect. |
| `AI_GATEWAY_IMAGE_MODEL` | No | Default `openai/gpt-image-2` | Only relevant when `AI_GATEWAY_API_KEY` is set and an AI-generated image is approved; otherwise falls back to the default model with no effect. |

Remotion itself has no API key - it's an npm dependency. Gate 0 (below)
checks/installs it. `@visx/*` (charts, see
[references/data-visualization.md](references/data-visualization.md)) also
has no API key - it's a plain npm dependency with no feature gating. See
[references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
for the exact packages and Next.js folder conventions this skill assumes,
and [references/video-image-generation.md](references/video-image-generation.md)
for the AI Gateway video/image calls.

## Working directory (per production)

Every production gets its own folder, named by a slug derived from the
video's title:

```
remotion/
  index.ts                          # registerRoot() - created once, shared by all productions
  Root.tsx                          # registers one <Composition> per approved format, per production - created once, one block added per production
  shared/                           # cross-production library - created once, project-wide (Gate 0)
    theme.ts                        # base brand tokens; production theme.ts files extend this
    Intro.tsx / Outro.tsx           # promoted once a second production needs the same open/close
    charts/                         # promoted chart primitives (see data-visualization.md)
  productions/{slug}/
    brief.md                        # Gate 1 - topic, theme, length, platform(s)/format(s), audience/tone
    script.md                       # Gate 2
    scenes.json                     # Gate 3 (includes the approved `formats` array from the brief), timing fields filled at Gate 5
    Composition.tsx                 # Gate 7 - assembles scenes into the final timeline, shared across every format
    scenes/
      01-{name}.tsx                 # Gate 6 - component scenes only, laid out to adapt across every approved format
      02-{name}.tsx
    shared/
      theme.ts                      # Gate 4 - includes the background-strategy decision
      {SharedComponent}.tsx          # Gate 4
    audio/
      alignment.json                # Gate 5 - intermediate, not needed at render time
    critic/
      report-{n}.md                 # Gate 8 - one per critic iteration
public/
  video/{slug}/
    narration.mp3                   # Gate 5 - Remotion assets resolve through the public folder, so it lives here even though visitors never fetch it directly
    generated/{sceneId}.mp4         # Gate 6 - Veo-generated clips, if any
    source/{name}.mp4               # Gate 6 - user-supplied real footage, if any (provided by the user, not generated)
    generated/{name}.png            # Gate 4 - AI-generated background/support images, if any
    final.mp4                       # Gate 7 output - if only one format was approved
    final-{formatId}.mp4            # Gate 7 output - one per format instead of final.mp4, if more than one format was approved
    poster.png / poster-{formatId}.png  # Gate 7 - a still frame per output file, for use as a video poster/thumbnail
```

`remotion/` sits at the project root next to `app/`/`src/`, per the standard
"add Remotion to an existing Next.js project" setup - see
[references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md).
Confirm the slug with the user if it's ambiguous from the title. See
[references/reusable-components.md](references/reusable-components.md) for
what belongs in `remotion/shared/` versus a production's own `shared/`.

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
- [x] Gate 0 - Project and video setup
- [x] Gate 1 - Planning brief (topic, length, platform(s), format(s))
- [x] Gate 2 - Script
- [ ] Gate 3 - Scene plan + visual plan  ← Current
- [ ] Gate 4 - Theme + shared components + background
- [ ] Gate 5 - Narration and audio generation
- [ ] Gate 6 - Scenes built (components / generated / real video)
- [ ] Gate 7 - Assemble + render per format (target length check)
- [ ] Gate 8 - Critic review + final sign-off
```

## Pipeline (gates)

### Gate 0 - Project and video setup

1. Check whether the project already has a Remotion entry point
   (`remotion/index.ts` calling `registerRoot`). If yes, skip step 2.
2. If not, scaffold it per [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md):
   install the required packages, create `remotion/index.ts` and
   `remotion/Root.tsx`, and confirm `npx remotion studio remotion/index.ts`
   boots. Do this once per project, not once per production - it doesn't
   need a slug/title yet, so it runs before Gate 1.
3. Check whether `remotion/shared/` (the cross-production library) already
   exists. If not, create an empty `remotion/shared/theme.ts` seeded only
   with whatever base tokens the project already has elsewhere (per Gate 4
   step 6) - don't invent brand tokens speculatively here. This folder
   fills in gradually as components get promoted into it (see
   [references/reusable-components.md](references/reusable-components.md)),
   not all at once at Gate 0.
4. Do **not** install the `ai` package, AI Gateway, or `@visx/*` yet - only
   do that at whichever later gate first needs a generated video/image or a
   chart (keeps this step a no-op for productions that end up simple).
5. **Check required/conditional env vars now and carry the result into
   Gate 1** - don't wait until a later gate to discover one is missing:
   - `ELEVENLABS_API_KEY`/`ELEVENLABS_VOICE_ID` are required for the whole
     skill. If either is missing, stop here and ask the user to add it
     before proceeding to Gate 1 - there is no fallback.
   - `AI_GATEWAY_API_KEY` is optional but gates two features:
     `generated-video` scenes (Gate 3) and AI-generated background/support
     images (Gate 4). If it is **not** set, note this plainly in the Gate 1
     message: generated-video and AI-generated images won't be offered as
     options for this production, and Gate 3/4 will default to component
     scenes/real-video-if-supplied and blank or CSS/React-generated
     backgrounds instead. This is informational, not a blocker - most
     productions don't need it.
6. No status block needed if this was a no-op (entry point already existed) -
   just note it, plus the env var check from step 5, in the Gate 1 message.
   If you scaffolded anything, show what was added before moving on.

### Gate 1 - Planning brief

This is the first gate for *this production* (Gate 0 was a one-time,
project-wide setup step) and it always runs, even if the request already
sounds fully specified - confirm every field below out loud rather than
silently assuming one. Nothing else in the pipeline starts until this is
approved.

1. Confirm with the user:
   - **Topic/subject** - what the video is about (the product, feature, or
     concept), plus a working title to derive the production `slug` from.
   - **Theme/angle** - the core message or hook this video exists to land
     (e.g. "problem → fix", "feature highlight", "brand moment"). This is
     the *content* theme, not the visual design theme - palette/type/motion
     direction is a separate decision at Gate 4.
   - **Target length** - ask if the user has a specific length in mind. If
     not, propose a target based on the actual scope of content just
     discussed rather than defaulting blindly: a single feature/teaser idea
     usually fits well under a minute (even down to ~15-20s for an
     individual `generated-video` beat, see below), while a fuller
     walkthrough with several distinct points genuinely needs more room.
     **Default to around 1 minute** only when the content doesn't clearly
     point to something shorter or longer. There is **no fixed cap** on the
     overall production's runtime - the goal is a target that actually fits
     the content, never forcing content into a preset bucket or padding a
     thin idea out to hit one. **If the resulting target is likely to run
     past 5 minutes**, say so explicitly now: longer narration means more
     characters sent to ElevenLabs per synthesis call, which draws down more
     of the account's audio-generation credits/quota - confirm the user is
     comfortable with that before locking in the target. Record the agreed
     number in `brief.md` as `targetLengthSeconds` (or a stated range) -
     every later gate reads this instead of assuming any fixed default.
   - **Target platform(s)/placement** - where this will be used (a landing
     page hero, a social post, an in-app tour, an ad, etc.). This drives
     both the aspect ratio(s) below and whether it'll typically be watched
     with sound on or muted.
   - **Aspect ratio(s)/format(s)** - derive candidates from the platform(s)
     above, but confirm explicitly. **Remotion can render multiple aspect
     ratios from one production** - the same script, narration, and shared
     theme/components can drive more than one `<Composition>` (e.g. a 16:9
     master for the website and a 9:16 cut for social, both timed off the
     same narration). Ask whether more than one format is actually needed
     rather than defaulting to a single output; default to one 16:9
     composition only if the user has no specific platform in mind. Present
     this table of standard options as part of that question (add a row
     only if the platform(s) above genuinely need something outside these
     three, e.g. a 4:5 feed variant - see
     [references/multi-format-layout.md](references/multi-format-layout.md)):

     | Use Case | Aspect Ratio | Resolution | Notes |
     |---|---|---|---|
     | Standard video (YouTube, presentations, TV) | 16:9 | 1920×1080 (1080p), 3840×2160 (4K) | The default for most video content. |
     | Social media (square) | 1:1 | 1080×1080 | Good for Instagram feed, LinkedIn, Facebook. |
     | Mobile / Shorts / Reels / TikTok | 9:16 | 1080×1920 | Full-screen vertical video - see [references/multi-format-layout.md](references/multi-format-layout.md) for the platform safe zones a video in this format needs to respect. |

     Default to 1080p for a chosen 16:9 row unless the user specifically
     needs 4K (e.g. a large-screen/broadcast placement) - confirm rather
     than assuming the higher resolution.
   - **Audience/tone** - who's watching, how formal/casual, first- or
     second-person address (this skill does not assume a house tone - ask
     once here, then keep it consistent for the whole production).
2. Derive the `slug` from the confirmed title and confirm it with the user
   if it's ambiguous. Create `remotion/productions/{slug}/` and
   `public/video/{slug}/` now that the slug is settled.
3. Write the confirmed answers to `productions/{slug}/brief.md`: topic,
   theme/angle, target length, platform(s) mapped to format(s), and
   audience/tone. Every later gate reads from this file rather than
   re-asking. Record the approved format(s) as the same
   `Use Case | Aspect Ratio | Resolution | Notes` table presented in step 1,
   filtered to just the chosen row(s) plus each row's `{ id, width, height }`
   (e.g. `id: "9x16", width: 1080, height: 1920`) so `scenes.json`/`Root.tsx`
   can consume it directly at Gate 3/7.
4. **Present the brief** (every field above, the resolved slug, and the
   final format table from step 3). **End with the gate status block. Wait.**

### Gate 2 - Script

1. Read the approved brief (`productions/{slug}/brief.md`) - topic, theme,
   length, and audience/tone are already settled at Gate 1; don't re-ask
   them. If anything changes while writing the script (e.g. the idea turns
   out too big for the agreed length), flag it and update `brief.md` rather
   than silently drifting from what was approved.
2. Write `script.md` as an **audio-first VO script**. Read
   [references/audio-first-script.md](references/audio-first-script.md) in
   full before writing - it covers the pacing/word-budget math (scaled to
   the brief's approved target length), the required opening hook, breath
   points, ElevenLabs `<break>` tag usage, and speakable wording (no code/
   selectors read aloud). If the brief is naturally a contrast (a timing/
   spacing/threshold that's easy to get wrong, a before/after, a
   right-way-vs-wrong-way), read
   [references/content-formula.md](references/content-formula.md) too - it's
   a reusable shape (isolate one variable, show both sides, let a concrete
   number be the payload) for exactly this kind of explainer, sized to fit
   the brief's approved target length. Not every brief needs it - skip it
   for a straight feature reveal with nothing to contrast against.
3. **Read the script aloud yourself** (or silently mouth it) and time it. If
   it sounds like copy on a page, or clearly runs past the brief's target
   length at a natural pace, rewrite/cut until it doesn't.
4. **Present script** (full text + estimated spoken duration). **End with the
   gate status block. Wait.**

### Gate 3 - Scene plan + visual plan

1. Break the approved narration into scenes **on spoken beats**, not
   arbitrary word counts. Do not guess durations yet for component scenes -
   final `durationInFrames` is computed from real audio in Gate 5.
2. Preserve the approved script's wording and cadence - don't tighten copy
   into stiffer on-screen slogans when splitting it.
3. For **every** scene, decide `visualType`:
   - `component` (default) - a Remotion component built in Gate 6. Prefer
     this whenever the scene can plausibly be drawn in code (UI mockups,
     text, diagrams, icons, motion graphics).
   - `generated-video` - a Veo-generated clip via AI Gateway. Only propose
     this where a code-drawn scene genuinely can't deliver the shot (e.g. a
     photoreal establishing moment, an abstract/organic visual metaphor
     that would look worse as a mockup). **Requires `AI_GATEWAY_API_KEY`**
     (see Prerequisites) - if it wasn't set per the Gate 0 check, don't
     propose this option; use `component` (or, if the user can supply
     footage, `real-video`) instead.
   - `real-video` - real, user-supplied footage. Only propose this for a
     brief intro/outro moment (e.g. a logo sting, a live product hero shot
     the user already has) - **never** as the primary explanatory content,
     and never invent/source real footage yourself.
4. **If any scene proposes `generated-video` or `real-video`, flag it
   explicitly and separately in this gate's presentation** - don't bury it
   in the scene table. Call out per flagged scene: why a component won't
   work, the estimated cost/time impact (generated video costs money and
   takes minutes per clip - see [references/video-image-generation.md](references/video-image-generation.md)),
   and for `real-video`, exactly what file the user needs to provide. If a
   `generated-video` beat needs to span longer than one Veo clip's fixed
   duration (4/6/8s - see [references/video-image-generation.md](references/video-image-generation.md)),
   plan **multiple clips end-to-end** for that beat rather than one
   artificially stretched clip, and fold the extra generation(s) into the
   cost/time call-out. **The user must explicitly approve each flagged scene
   before Gate 6 acts on it** - approving the scene plan as a whole is not
   sufficient if these weren't individually called out.
5. Write `scenes.json`. Per scene:

   ```json
   {
     "id": "03",
     "title": "Problem - tabbing blind",
     "visualType": "component",
     "component": "scenes/03-TabbingBlind.tsx",
     "narration": "You hit Tab. Then Tab again. Nothing highlights.",
     "keyPoint": "The one idea this scene must land - nameable in ~1 second",
     "onScreenText": "",
     "transitionIn": { "type": "fade", "durationInFrames": 15 },
     "visualNotes": "Free-text plan for what the scene shows - just planning notes, not a rigid prompt contract. The component code is the real spec once Gate 6 starts."
   }
   ```

   For a flagged `generated-video` or `real-video` scene, add a
   `videoSource` object instead of (or alongside, for a fallback) a
   `component` path:

   ```json
   {
     "id": "01",
     "title": "Cold open - logo sting",
     "visualType": "real-video",
     "narration": "",
     "keyPoint": "Brand recognition, wordless",
     "videoSource": {
       "type": "real",
       "sourceFile": "public/video/{slug}/source/logo-sting.mp4",
       "mute": false,
       "approvedAt": "Gate 3"
     },
     "transitionIn": null,
     "visualNotes": "User-supplied clip, used as-is; trimmed to fit if longer than needed."
   }
   ```

   ```json
   {
     "id": "02",
     "title": "Abstract data flow",
     "visualType": "generated-video",
     "narration": "Every request flows through one place.",
     "keyPoint": "Centralised routing, shown abstractly",
     "videoSource": {
       "type": "generated",
       "prompt": "Abstract glowing lines converging into a single bright node, dark background, smooth camera push-in",
       "durationSeconds": 4,
       "mute": true,
       "approvedAt": "Gate 3"
     },
     "transitionIn": { "type": "fade", "durationInFrames": 15 },
     "visualNotes": "Narration plays over the muted clip."
   }
   ```

   Top-level fields: `slug`, `title`, `fps`, `targetDurationSeconds`
   (carried from the brief's approved target length - no fixed cap), and
   `formats` - carried over verbatim from the approved
   `productions/{slug}/brief.md`, e.g.:

   ```json
   "formats": [
     { "id": "16x9", "aspectRatio": "16:9", "width": 1920, "height": 1080 },
     { "id": "9x16", "aspectRatio": "9:16", "width": 1080, "height": 1920 }
   ]
   ```

   `fps` and every scene's `durationInFrames` are shared across all formats
   (narration timing doesn't change per aspect ratio) - only the visual
   layout differs, and that's handled in each scene's component reading
   `useVideoConfig()` at Gate 6, not by duplicating scenes per format. Do
   **not** add per-scene continuity/consistency fields (background
   description, prop list, palette notes) for component scenes either -
   that's handled once, structurally, at Gate 4. See
   [references/design-and-continuity.md](references/design-and-continuity.md)
   for what belongs in a scene plan versus what belongs in shared code.
6. Concatenated `narration` fields (space-joined, in order, skipping empty
   strings on wordless video scenes) must equal the approved body script
   exactly - this string is what gets sent to ElevenLabs in Gate 5, and
   scene timings are derived by locating each scene's substring inside the
   full alignment.
7. Apply the **one idea, one focus** discipline from
   [references/design-and-continuity.md](references/design-and-continuity.md):
   `keyPoint` must be nameable in about a second. Split a scene if it's
   carrying two ideas - but remember the brief's approved target length;
   splitting must not push the total meaningfully over that budget, so cut
   elsewhere (or revisit the target with the user) if it would.
   If Gate 2 used the comparison formula, carry its on-screen label system
   (chapter/state pill, two-tone headline, optional mechanism caption - see
   [references/content-formula.md](references/content-formula.md)) into
   `onScreenText`/`visualNotes` now, with the qualitative tag and literal
   value for every variant shown, not just the correct one.
8. Sum the planned scene lengths (narration-derived estimate for component
   scenes, `durationSeconds` for video scenes) and confirm the total is
   **close to the brief's approved `targetLengthSeconds`**. If it's drifted
   meaningfully in either direction, cut/add a scene or tighten the script
   now, before Gate 4/5/6 work is built on top of it.
9. **Present the scene table** (id, visualType, title, keyPoint, one-line
   narration excerpt) **and the flagged real/generated-video call-outs from
   step 4 separately, requiring explicit approval**. **End with the gate
   status block. Wait.**

### Gate 4 - Theme + shared components + background

1. Scan `scenes.json` for anything that appears in more than one scene (a
   mock UI chrome, a card, a cursor, a logo, a recurring layout shell) and
   list it before writing any code. **First check `remotion/shared/`** (the
   cross-production library, not just this production's own `shared/`) for
   an existing component or chart primitive that already does the job -
   reuse it (adding a prop if needed) before writing a new one. See
   [references/reusable-components.md](references/reusable-components.md).
2. Create `productions/{slug}/shared/theme.ts` - design tokens (colour
   palette, font choices via `@remotion/google-fonts` or local fonts,
   spacing scale, motion presets e.g. named `spring()` configs for "enter",
   "exit", "emphasis"). **Import and extend** `remotion/shared/theme.ts`'s
   base tokens rather than redefining them (see
   [references/reusable-components.md](references/reusable-components.md)
   for the extension pattern) - this is the *only* place production-level
   values are defined; scenes import from it, never hardcode a hex value or
   font name inline.
3. **Decide the background strategy** and record it (as a short comment plus
   the actual implementation) in `shared/theme.ts` or a
   `shared/Background.tsx`, whichever fits. Pick one, per-project (a
   per-scene override is fine if a scene's `visualNotes` calls for it):
   - **Blank/solid** - a plain colour (usually from the palette) or a
     transparent/white canvas. Default choice; use it unless the brief
     specifically benefits from more visual texture.
   - **CSS/React-generated** - a background built from code (gradient,
     grid, geometric pattern, particles driven by `useCurrentFrame()`).
     Prefer this over a generated image whenever the look is achievable in
     code - it's free to iterate and pixel-consistent across every scene
     that uses it.
   - **AI-generated static image** - only when the desired look (a
     textured/organic backdrop, a photoreal environment) isn't practically
     achievable in CSS/React. **Requires `AI_GATEWAY_API_KEY`** (see
     Prerequisites) - if it wasn't set per the Gate 0 check, this option
     isn't available; use blank or CSS/React-generated instead. Otherwise,
     generate it once via `AI_GATEWAY_IMAGE_MODEL` and reuse the same file
     across every scene that needs it - never regenerate per scene. Follow
     [references/video-image-generation.md](references/video-image-generation.md)
     for size/quality/background-colour parameters so the result actually
     composites correctly behind foreground UI content.
4. Create one component per recurring element in `shared/` (e.g.
   `shared/BrowserChrome.tsx`, `shared/Cursor.tsx`). Keep them dumb/reusable -
   props in, no scene-specific logic.
5. **If more than one format was approved at Gate 1**, build these shared
   components to adapt rather than assuming one fixed frame size: read the
   actual dimensions from `useVideoConfig()` (width/height/aspect ratio)
   instead of hardcoding pixel positions, and use proportional/flex layout
   so the same component reads correctly in both a landscape and a portrait
   frame. Read [references/multi-format-layout.md](references/multi-format-layout.md)
   for the concrete techniques (orientation branching, proportional sizing,
   reflowing arrangement rather than just scaling it, plus the platform
   safe-zone budget for a 9:16 destined for TikTok/Reels/Shorts/Stories). If
   a recurring element's layout genuinely can't adapt (e.g. a wide
   horizontal browser-chrome mockup has no sane portrait equivalent), say so
   now and agree a per-format variant of that one component with the user -
   don't silently let it clip or squash in the secondary format.
6. If the project already has its own design tokens/component library
   (check for an existing `theme`/`tokens` module or a design-system skill's
   output), prefer wiring `theme.ts` to re-export from there over inventing a
   parallel palette.
7. If any scene needs a chart/graph/data graphic, plan it here rather than
   improvising at Gate 6: decide plain React/SVG vs. visx per
   [references/data-visualization.md](references/data-visualization.md),
   and if the project is likely to produce more than one video, consider
   whether an intro/outro or chart primitive belongs in `remotion/shared/`
   from the start rather than this production's own `shared/` - see the
   "prime shared-library candidates" section of
   [references/reusable-components.md](references/reusable-components.md).
   Any scene with UI mockup chrome should also be planned against
   [references/ui-ux-video-guidelines.md](references/ui-ux-video-guidelines.md)
   (oversizing for legibility, stripping chrome to what the scene needs,
   explicit state-change signals) before it's built at Gate 6.
8. Render a quick still of one shared component (and the background, if any)
   in isolation to confirm it looks right - in every approved format if
   there's more than one, not just the primary (`npx remotion still`, per
   [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)).
9. **Present the theme + shared component list + background choice and why**
   (link each file, including any generated background image, and any
   component sourced from or promoted to `remotion/shared/`). **End with
   the gate status block. Wait.**

### Gate 5 - Narration and audio generation

Full mechanics (API call, alignment→scene-timing math, optional captions) are
in [references/elevenlabs-narration-sync.md](references/elevenlabs-narration-sync.md).
Read it before this gate; it is not optional prep.

1. Concatenate all **component-scene and narrated video-scene** `narration`
   fields (space-joined, skipping wordless scenes) and call ElevenLabs'
   timestamped-synthesis endpoint once for the full body script.
2. Save the decoded audio to `public/video/{slug}/narration.mp3` and the raw
   alignment response to `productions/{slug}/audio/alignment.json`.
3. Walk the alignment to find each narrated scene's `audioStartSeconds`/
   `audioEndSeconds` and write `durationInFrames = round((end - start) * fps)`
   back into `scenes.json` for every **component** scene (add a small
   lead-out, e.g. 3-5 frames, so a scene doesn't cut the instant speech
   stops - don't add lead-in, the next scene's audio is already playing).
   For `generated-video`/`real-video` scenes, `durationInFrames` instead
   comes from the clip's actual length (fixed at 4/6/8s for Veo, or the
   trimmed length of a real clip) - if a wordless video scene sits between
   two narrated scenes, its duration is just the clip length, not derived
   from any alignment.
4. **Recompute the total runtime** (sum of all scene durations, minus
   transition overlaps) and confirm it's still close to the brief's
   approved `targetLengthSeconds` now that real timings exist, not just the
   Gate 3 estimate. If it's drifted meaningfully, tighten/cut here before
   building scenes in Gate 6 - or, if the content genuinely needs the extra
   room, revisit the target with the user rather than force-cutting.
5. If the user asked for captions, generate the `Caption[]` array from the
   same alignment now (see the reference doc) and save it to
   `productions/{slug}/captions.ts`. Prefer defaulting to **on** for this
   format even if not explicitly requested - see
   [references/production-quality-guidelines.md](references/production-quality-guidelines.md)
   for why - but confirm with the user rather than silently deciding.
6. **Listen to the narration** (or check word timing sanity from the
   alignment) - if it rushes or mispaces, revise the script at Gate 2/3 and
   re-run this gate before approving.
7. **Present the timing table** (scene id → narration excerpt →
   start/end seconds → `durationInFrames`) plus the confirmed total runtime
   and a link to the audio. **End with the gate status block. Wait before
   building scenes.**

### Gate 6 - Scenes built

Build each scene under `scenes/` (or generate/place its clip), in order,
per its `visualType` from Gate 3.

**`component` scenes:**

1. Each component imports what it needs from `shared/theme.ts` and
   `shared/*.tsx` - never redefines a recurring visual locally.
2. Drive all motion from `useCurrentFrame()` / `interpolate()` / `spring()` -
   see [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
   for the animation primitives this skill relies on. For anything not
   covered there, consult the Remotion docs directly
   (https://www.remotion.dev/docs) or the official `remotion` skill
   (https://github.com/remotion-dev/skills) - don't guess at an unfamiliar
   API.
3. **Self-audit before presenting** - render stills at the scene's start,
   middle, and end frame:

   ```bash
   npx remotion still remotion/index.ts {slug} out/{sceneId}-start.png --frame={startFrame}
   ```

   If more than one format was approved at Gate 1, render this still for
   the primary format plus whichever secondary format is most visually
   different (usually the vertical one, if there is one) - that's where a
   layout that only worked in the primary aspect ratio actually shows up.
   Look at each still and check it against the relevance / one-focus /
   hierarchy / UI-layout-realism checklist in
   [references/design-and-continuity.md](references/design-and-continuity.md).
   For a scene with a chart/graph, also check it against
   [references/data-visualization.md](references/data-visualization.md); for
   a scene with UI mockup chrome, also check it against
   [references/ui-ux-video-guidelines.md](references/ui-ux-video-guidelines.md);
   for a multi-format production, also check it against
   [references/multi-format-layout.md](references/multi-format-layout.md)'s
   self-audit (reflow vs. just scaling, platform safe zones for a 9:16
   destined for TikTok/Reels/Shorts/Stories).
   Fix the component and re-render the still if it fails.

**`generated-video` scenes (only if approved at Gate 3):**

1. Call the video-generation model per
   [references/video-image-generation.md](references/video-image-generation.md)
   using `AI_GATEWAY_VIDEO_MODEL` (default `google/veo-3.1-generate-001`)
   and the scene's `videoSource.prompt`. If more than one format was
   approved at Gate 1, generate one clip **per format's aspect ratio**
   (Veo's `aspectRatio` parameter) rather than stretching/cropping a single
   generation to fit a second format - note this in the Gate 3 cost/time
   call-out, since it multiplies the generation cost by the format count.
2. Save the returned clip(s) to
   `public/video/{slug}/generated/{sceneId}.mp4` (or
   `{sceneId}-{formatId}.mp4` if more than one).
3. Build a thin scene component that renders it with `OffthreadVideo` from
   `"remotion"` (not `<Video>` or `@remotion/media`'s video component - use
   `OffthreadVideo` for reliable frame-accurate rendering), muted per
   `videoSource.mute`, trimmed/positioned to the scene's `durationInFrames`,
   and selecting the right clip for the format currently rendering (via
   `useVideoConfig()`'s width/height, or a `formatId` prop threaded down
   from `Composition.tsx`).
4. **Self-audit**: watch the clip (or check start/mid/end stills the same
   way as a component scene) against the same design-and-continuity
   checklist - a generated clip that doesn't match the brief or looks
   inconsistent with the rest of the video should be regenerated with a
   revised prompt **once** before presenting; don't loop indefinitely
   re-rolling generations, and don't burn additional generations without
   flagging the retry to the user if the first one is fundamentally wrong
   for the brief (return to Gate 3 instead).

**`real-video` scenes (only if approved at Gate 3):**

1. Confirm the user has placed their file at
   `public/video/{slug}/source/{name}.mp4` (ask if it's missing - never
   substitute a generated or stock clip for a scene the user asked to use
   their own footage for).
2. Build a thin scene component rendering it with `OffthreadVideo`, muted
   per `videoSource.mute`, trimmed to fit `durationInFrames`.
3. If the clip includes its own audio that's meant to be heard (e.g. an
   intro sting with sound, `mute: false`), make sure it doesn't overlap a
   narrated segment - see the audio-mixing guidance in
   [references/production-quality-guidelines.md](references/production-quality-guidelines.md).
4. If more than one format was approved at Gate 1 and the real clip's
   native aspect ratio doesn't match a secondary format, agree the fallback
   with the user now (centre-crop, or letterbox on a background from the
   Gate 4 strategy) rather than silently stretching/distorting it - real
   footage can't be regenerated in a different aspect ratio the way a
   `generated-video` scene can.

**Presenting:**

**Present each scene** (link the stills/clip, or a short screen recording if
available) as you go, or batch a few scenes together if they're
straightforward - use judgement, but don't build all scenes silently and
present them all at once. **End with the gate status block after the batch
you're presenting. Wait.**

### Gate 7 - Assemble + render

1. Build **one** `Composition.tsx`: a `<TransitionSeries>` (or `<Series>` if
   no scene uses a transition) that lays out every scene in order using the
   `durationInFrames` computed at Gate 5/6, plus **one** root-level
   `<Audio src={staticFile('video/{slug}/narration.mp3')} />` - not one
   audio element per scene. This same component is reused for every
   approved format; it must not fork per format. See
   [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
   for `<TransitionSeries>`/`<Audio>` usage and how transition overlap
   affects total duration.
2. Register **one `<Composition>` per approved format** in `Root.tsx`, all
   pointing at the same `Composition` component, each with that format's
   `width`/`height` from `brief.md`/`scenes.json` and the same
   `durationInFrames` (identical across formats, since it's derived from
   narration + fixed clip lengths, not from aspect ratio):

   ```tsx
   {formats.map((format) => (
     <Composition
       key={format.id}
       id={`${slug}-${format.id}`}
       component={ProductionComposition}
       width={format.width}
       height={format.height}
       durationInFrames={totalDurationInFrames}
       fps={fps}
     />
   ))}
   ```

   If only one format was approved, this collapses to the single
   `id="{slug}"` composition as before - don't add format suffixing for a
   single-format production.
3. **Check total runtime against the brief's approved `targetLengthSeconds`**
   (`durationInFrames / fps`) - this is the same number for every format, so
   check it once. There is no fixed cap; if it's drifted meaningfully from
   what was approved, stop - go back to Gate 2/3 to tighten/expand content,
   or back to Gate 1 to re-confirm the target with the user, rather than
   shipping something that silently doesn't match the brief.
4. Render **every** approved format:

   ```bash
   # single format:
   npx remotion render remotion/index.ts {slug} public/video/{slug}/final.mp4

   # multiple formats - once per format id:
   npx remotion render remotion/index.ts {slug}-{formatId} public/video/{slug}/final-{formatId}.mp4
   ```

5. Export a poster frame per rendered file (a representative still, usually
   the strongest on-screen moment - it can differ per format if the layout
   genuinely differs) to `public/video/{slug}/poster.png` (or
   `poster-{formatId}.png`) via `npx remotion still`.
6. **Do not present this as final yet** - proceed directly into Gate 8. Note
   the render's existence in the Gate 8 message rather than stopping here to
   wait (the critic pass happens before the user sees the "final" claim).

### Gate 8 - Critic review

Full rubric and loop mechanics are in
[references/critic-review.md](references/critic-review.md) - read it before
running this gate.

1. Review the actual rendered `final.mp4` (the primary format if more than
   one was rendered - plus the approved brief/script/scene plan) against
   three axes: **brief fit** (does it match what was approved at
   Gates 1-3, including the approved `targetLengthSeconds` and the
   platform(s)/format(s) confirmed in `brief.md`), **UI & animation
   clarity** (per the
   design-and-continuity checklist, applied to the whole video, not just
   individual stills), and **script delivery professionalism** (does the
   narration read as a competent, well-paced professional VO, not rushed or
   robotic-sounding).
2. **If more than one format was rendered**, additionally spot-check every
   secondary format specifically for layout breakage (clipped content,
   crowded safe margins, a shared component that didn't actually adapt) per
   [references/multi-format-layout.md](references/multi-format-layout.md)'s
   self-audit - this is a distinct failure mode multi-format introduces and
   belongs under the "UI & animation clarity" axis; the narration/timing
   axes are shared across formats and don't need re-checking per format.
3. Write a critic report to `productions/{slug}/critic/report-{n}.md`:
   pass/fail per axis, and for any failure, the concrete fix and which gate
   owns it.
4. **If any axis fails**: apply the fix at the owning gate (re-edit a scene,
   re-cut the script, adjust a transition, etc. - use the Revisions table
   below), redo Gate 7 (re-render every format), and re-run this gate.
   **Cap this loop at 3 iterations.** If it still hasn't passed after 3,
   stop and hand the specific unresolved finding to the user instead of
   continuing to loop.
5. Once all three axes pass (or the loop cap is hit), **present the final
   critic report together with every rendered format and its poster
   frame** - never one without the other. Note total duration, scene
   count, voice used, format(s) delivered, and how many critic iterations
   it took.
6. **End with the gate status block** (all gates `[x]` when approved). Wait
   for final sign-off.

## Revisions

Because most of this is code, a revision rarely means "regenerate and hope
it's right this time" - the exceptions are the generated/real video scenes,
which are genuinely re-shoot/re-generate operations:

| Change requested | What to do |
|---|---|
| "Make the error bigger" / "less clutter" / "different motion" (component scene) | Edit that scene's `.tsx` directly. Re-render a still to confirm, then re-run the Gate 7 render command. No other scene is touched. |
| "Match the previous scene more" / "hard cut instead" | Adjust that scene's `transitionIn` in `scenes.json` and the corresponding `<TransitionSeries.Transition>` in `Composition.tsx`. |
| Wording of the narration changes | Update `script.md` + the affected scene's `narration` in `scenes.json`, then **redo Gate 5** (re-synthesize, since timings shift), then re-render. |
| A shared/recurring element or the background needs to change | Edit the one component in `shared/` - every scene using it updates automatically. Never patch the same visual separately in each scene file. |
| Voice-over only (same script) | Redo Gate 5 with the new voice/model, keep scene components as-is, re-render. |
| A generated-video scene's clip is wrong | Revise the prompt and regenerate **that one clip** - never re-generate scenes that already passed. If the fix needs a fundamentally different shot, treat it as a Gate 3 change (re-flag, re-approve) rather than blindly re-rolling. |
| A real-video scene's clip needs to change | Ask the user for the replacement file; never substitute your own generated or stock footage for a scene they specifically asked to use real footage for. |
| Critic flags an issue at Gate 8 | Follow the report's "which gate owns it" pointer, fix there, redo Gate 7, re-run Gate 8 - don't patch around the symptom at the render step. |
| A new platform/format is needed after Gate 1 was approved (e.g. "we also need a vertical cut now") | Update `brief.md` and `scenes.json`'s `formats` array, check every shared component (Gate 4) actually adapts to it, register the new `<Composition>` in `Root.tsx`, render it (Gate 7), and run it through Gate 8 - script/narration/timing are unaffected and don't need redoing. |
| A format needs to be dropped | Remove it from `brief.md`/`scenes.json`, remove its `<Composition>` registration, stop rendering/shipping it - no other change needed. |

Present the updated still/render and wait for approval before moving on -
same as any other gate, just scoped to the one thing that changed.

## Do not

- Skip approval gates, or present a gate without the status block.
- Skip or shortcut the Gate 1 planning brief because the request "already
  sounds specific enough" - confirm topic, theme, length, platform(s), and
  format(s) explicitly every time.
- Assume a single aspect ratio without asking - Remotion can target more
  than one from a single production, so silently picking one forecloses
  that option without the user ever being asked.
- Apply the ~1-minute default without reasoning about it - Gate 1 should
  size the target from the actual content discussed, not apply the default
  blindly regardless of scope.
- Ship a video whose runtime drifts meaningfully from the brief's approved
  `targetLengthSeconds` without flagging it - if the content genuinely grew
  or shrank, update the target at Gate 1 rather than letting it silently
  drift either direction.
- Stretch a single `generated-video` clip artificially long, or lean on
  generated video for more of the timeline just because the overall
  production is longer - chain multiple fixed-duration Veo clips for a
  longer beat, and keep preferring Remotion components regardless of the
  production's overall length.
- Call ElevenLabs once per scene - it must be one synthesis call for the
  full body script, so cadence flows naturally across scene boundaries.
- Introduce a `generated-video` or `real-video` scene that wasn't explicitly
  flagged and approved at Gate 3.
- Leave a generated/real clip's native audio unmuted underneath narration
  unless that's a deliberate, reviewed choice.
- Snap component-scene durations to a fixed bucket (e.g. "must be 4 or 8
  seconds") - that constraint only applies to the fixed-duration
  generated-video clips themselves, not to component scenes.
- Redefine a recurring visual element inside more than one scene file.
- Write a separate scene plan, script, or narration pass per format -
  every approved format shares Gates 1-3/5, diverging only in the layout
  code a shared component renders at Gate 4/6 and the render/registration
  step at Gate 7.
- Stretch or distort a generated/real video clip to force it into a format
  it wasn't made for - crop, letterbox, or regenerate at the right aspect
  ratio instead (see Gate 6).
- Use CSS transitions/animations or Tailwind animation classes for motion.
- Present a component scene without rendering and checking a still against
  [references/design-and-continuity.md](references/design-and-continuity.md) first.
- Regenerate a Veo clip repeatedly without limit - each generation costs
  money and time; if the first attempt is fundamentally wrong, go back to
  Gate 3 rather than re-rolling.
- Invent a house aesthetic (this skill is intentionally style-agnostic) -
  confirm direction with the user at Gate 4 instead of defaulting to one.
- Present a "final" render without the Gate 8 critic report alongside it.
- Loop the critic gate more than 3 iterations without surfacing the
  unresolved finding to the user.
- Quietly re-render an already-approved final without being asked.
- Reach for `@visx/*` (or any charting library) for something a plain
  `div`/SVG could draw - see [references/data-visualization.md](references/data-visualization.md).
- Use `@visx/xychart`'s `Animated*` components or add `react-spring` as a
  dependency - their animation is wall-clock-driven, not
  `useCurrentFrame()`-driven, and breaks deterministic rendering.
- Cram more than a handful of series/categories into one chart "for
  completeness" - cut to what the narration actually references, or split
  across scenes.
- Fork a shared component per-production "just this once" - add a prop to
  the shared component instead, or keep it production-specific until a
  second production genuinely needs it.
- Promote a component to `remotion/shared/` speculatively before a second
  production actually needs it.
- Propose a `generated-video` scene or an AI-generated background/support
  image without first confirming `AI_GATEWAY_API_KEY` is set (Gate 0) - if
  it isn't, those options aren't available; don't discover this mid-Gate 6
  after a scene was already approved around them.

## Additional resources

- Script pacing, hook discipline, and speakable wording (word budget scales with the approved target length): [references/audio-first-script.md](references/audio-first-script.md)
- Comparison content formula (isolate one variable, label both sides, borrowed motion values) for contrast-driven briefs: [references/content-formula.md](references/content-formula.md)
- Continuity, relevance, hierarchy, UI-layout-realism checklist: [references/design-and-continuity.md](references/design-and-continuity.md)
- ElevenLabs timestamped synthesis, scene-timing math, captions: [references/elevenlabs-narration-sync.md](references/elevenlabs-narration-sync.md)
- Next.js project setup, packages, Remotion primitives (incl. `OffthreadVideo`): [references/remotion-nextjs-setup.md](references/remotion-nextjs-setup.md)
- Veo video generation + AI-generated image guidelines via AI Gateway: [references/video-image-generation.md](references/video-image-generation.md)
- Gate 8 critic rubric and loop mechanics: [references/critic-review.md](references/critic-review.md)
- Production-quality guidelines (hooks, captions, audio mixing, safe margins, export/delivery): [references/production-quality-guidelines.md](references/production-quality-guidelines.md)
- Charts/graphs/data viz - React/SVG vs. visx, frame-driven chart animation, categories, attention/simplicity guidelines: [references/data-visualization.md](references/data-visualization.md)
- Rendering UI/UX in video - dos and don'ts for legibility, state signals, pacing: [references/ui-ux-video-guidelines.md](references/ui-ux-video-guidelines.md)
- Responsive layout across 16:9/1:1-4:5/9:16, orientation-based reflow, and platform (TikTok/Reels/Shorts/Stories) safe zones for multi-format productions: [references/multi-format-layout.md](references/multi-format-layout.md)
- Cross-production shared component library - what belongs in `remotion/shared/`, theme extension, promotion criteria: [references/reusable-components.md](references/reusable-components.md)
- Remotion docs: https://www.remotion.dev/docs
- Official Remotion API skill (deeper reference for anything not covered above - 3D, charts, Lottie, Tailwind, video trimming, etc.): https://github.com/remotion-dev/skills
