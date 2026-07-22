# Remotion in a Next.js project

This skill assumes Remotion is added to an **existing** Next.js project
("brownfield" install, per [Remotion's own docs](https://www.remotion.dev/docs/brownfield))
rather than a project scaffolded from scratch with `create-video`.

## One-time setup (Gate 0)

### Packages

```bash
npm install remotion @remotion/cli
npm install @remotion/media @remotion/transitions
# optional, only if needed by this production:
npm install @remotion/captions        # captions (Gate 4 step 4)
npm install @remotion/google-fonts    # non-system fonts (Gate 3)
npm install -D @remotion/eslint-plugin
```

Use whatever package manager the project already uses (`yarn`/`pnpm`) - the
equivalent commands are `yarn add ...` / `pnpm add ...`.

### Folder + entry point

Create a `remotion/` folder at the project root, next to `app/`/`src/`:

```ts
// remotion/Root.tsx
import { Composition } from "remotion";

export const RemotionRoot: React.FC = () => {
  return (
    <>
      {/* One <Composition> is added here per production, at Gate 6 */}
    </>
  );
};
```

```ts
// remotion/index.ts
import { registerRoot } from "remotion";
import { RemotionRoot } from "./Root";

registerRoot(RemotionRoot);
```

Check `tsconfig.json` doesn't have a `paths` alias that would shadow the
bare `remotion` package import with this local folder - Remotion's docs
call this out explicitly as a common brownfield-install mistake.

### Public folder

Remotion resolves local assets (`staticFile()`) through the project's
`public/` folder by default - the same `public/` Next.js already serves.
No `remotion.config.ts` `setPublicDir()` override is needed unless the
project's `public/` folder lives somewhere non-standard.

### Confirm it boots

```bash
npx remotion studio remotion/index.ts
```

### ESLint (optional but recommended)

```json
{
  "plugins": ["@remotion"],
  "overrides": [
    {
      "files": ["remotion/**/*.{ts,tsx}"],
      "extends": ["plugin:@remotion/recommended"]
    }
  ]
}
```

## Core primitives this skill relies on

### Animation - always frame-driven

```tsx
import { useCurrentFrame, useVideoConfig, interpolate } from "remotion";

const frame = useCurrentFrame();
const { fps } = useVideoConfig();

const opacity = interpolate(frame, [0, 2 * fps], [0, 1], {
  extrapolateRight: "clamp",
});
```

CSS transitions/animations and Tailwind animation classes are **forbidden**
- they do not render correctly in Remotion. Every value that changes over
time must come from `useCurrentFrame()`, `interpolate()`, or `spring()`.

`spring()` gives organic motion; store named presets in `shared/theme.ts`
rather than inventing config per scene:

```ts
// shared/theme.ts
export const motion = {
  enter: { damping: 200 },              // smooth, no bounce
  emphasis: { damping: 20, stiffness: 200 }, // snappy, minimal bounce
  playful: { damping: 8 },              // bouncy entrance
} as const;
```

```tsx
import { spring, useCurrentFrame, useVideoConfig } from "remotion";
import { motion } from "../shared/theme";

const scale = spring({ frame, fps, config: motion.enter });
```

### Sequencing scenes

Use `<Series>` for scenes with no visual transition between them, or
`<TransitionSeries>` (from `@remotion/transitions`) when a scene should
fade/slide/wipe into the next:

```tsx
import { TransitionSeries, linearTiming } from "@remotion/transitions";
import { fade } from "@remotion/transitions/fade";
import { slide } from "@remotion/transitions/slide";

<TransitionSeries>
  <TransitionSeries.Sequence durationInFrames={scenes[0].durationInFrames}>
    <Scene01 />
  </TransitionSeries.Sequence>
  <TransitionSeries.Transition
    presentation={fade()}
    timing={linearTiming({ durationInFrames: 15 })}
  />
  <TransitionSeries.Sequence durationInFrames={scenes[1].durationInFrames}>
    <Scene02 />
  </TransitionSeries.Sequence>
</TransitionSeries>;
```

Other presentations: `slide({direction: "from-left" | "from-right" | "from-top" | "from-bottom"})`,
`wipe()`, `flip()`, `clockWipe()`. Timing: `linearTiming({durationInFrames})`
or `springTiming({config})`.

**Important - total duration accounting:** a transition overlaps the two
scenes it sits between, so the composition's total `durationInFrames` is
the sum of every scene's `durationInFrames` **minus** the sum of every
transition's duration (via `timing.getDurationInFrames({fps})`), not a
plain sum. Compute this explicitly when setting the root `<Composition>`'s
`durationInFrames` at Gate 6 - don't just add up the scenes.

### Narration audio - one root-level track

```tsx
import { Audio } from "@remotion/media";
import { staticFile } from "remotion";

<Audio src={staticFile(`video/${slug}/narration.mp3`)} />;
```

Place this **once**, at the top level of `Composition.tsx`, outside the
`<TransitionSeries>` - not once per scene. It plays continuously from
frame 0, and scene visuals (whose durations were derived from the same
alignment in Gate 4) line up against it automatically.

### Setting composition duration from the audio (alternative to summing scenes)

If you'd rather derive the root composition's duration directly from the
rendered narration file instead of summing scene durations, use
`calculateMetadata` with [Mediabunny](https://www.remotion.dev/docs) (a
separate `mediabunny` package):

```bash
npm install mediabunny
```

```ts
import { Input, ALL_FORMATS, UrlSource } from "mediabunny";
import type { CalculateMetadataFunction } from "remotion";

const getAudioDuration = async (src: string) => {
  const input = new Input({ formats: ALL_FORMATS, source: new UrlSource(src, { getRetryDelay: () => null }) });
  return input.computeDuration();
};

export const calculateMetadata: CalculateMetadataFunction<Props> = async ({ props }) => {
  const durationInSeconds = await getAudioDuration(staticFile(`video/${props.slug}/narration.mp3`));
  return { durationInFrames: Math.ceil(durationInSeconds * fps) };
};
```

Either approach is valid; the scene-summing approach is preferred by
default because it's already derived from data you have in `scenes.json`
with no extra dependency - reach for `calculateMetadata`/Mediabunny mainly
if scene durations get hand-edited after the fact and could drift from the
audio length.

### Fonts

```bash
npm install @remotion/google-fonts   # or @remotion/fonts for local font files
```

```ts
// shared/theme.ts
import { loadFont } from "@remotion/google-fonts/Inter";

export const { fontFamily } = loadFont("normal", {
  weights: ["400", "700"],
  subsets: ["latin"],
});
```

Load fonts once in `shared/theme.ts`, not per scene component.

### Assets

Anything in `public/` is referenced with `staticFile()`:

```tsx
import { Img, staticFile } from "remotion";
<Img src={staticFile("video/{slug}/logo.png")} />;
```

Remote URLs (`https://...`) can be used directly, without `staticFile()`.

### Captions (only if requested - see elevenlabs-narration-sync.md step 4)

```tsx
import { createTikTokStyleCaptions } from "@remotion/captions";
import { captions } from "../captions"; // precomputed at Gate 4

const { pages } = createTikTokStyleCaptions({
  captions,
  combineTokensWithinMilliseconds: 1200,
});
```

Render each page in a `<Sequence>` positioned from `page.startMs`, and
highlight the active word by comparing `token.fromMs`/`token.toMs` against
the current absolute time - see the Remotion captions docs for the full
word-highlighting pattern.

## Rendering and reviewing

```bash
# Live preview / build scenes (Gate 5)
npx remotion studio remotion/index.ts

# Render a single still frame for the Gate 5 self-audit
npx remotion still remotion/index.ts {compositionId} out.png --frame={frameNumber}

# Final render (Gate 6)
npx remotion render remotion/index.ts {compositionId} public/video/{slug}/final.mp4
```

`{compositionId}` is the `id` given to the `<Composition>` registered in
`Root.tsx` for this production (this skill uses the production's `slug`).

## Beyond this skill

This file covers only what the video-remotion pipeline needs directly. For
anything else Remotion can do - 3D content (Three.js), charts/data
visualisation, Lottie animations, Tailwind integration, video trimming/
speed/pitch, GIFs, measuring text/DOM nodes, transcribing existing audio -
consult [remotion.dev/docs](https://www.remotion.dev/docs) or the official
Remotion API skill (https://github.com/remotion-dev/skills) directly rather
than guessing at an API.
