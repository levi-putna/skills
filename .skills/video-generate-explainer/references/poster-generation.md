# Poster / thumbnail generation (gpt-image-2)

At Gate 7, every rendered format needs a **purpose-designed poster**
(`poster.png` / `poster-{formatId}.png`) - not a random mid-video still.
A Remotion frame can be a useful *reference* for composition or colour, but
the shippable poster is generated with **OpenAI `gpt-image-2`** via AI
Gateway (`AI_GATEWAY_IMAGE_MODEL`, default `openai/gpt-image-2`), one image
per approved format/platform style.

> Call mechanics, auth, size limits, and quality tiers live in
> [video-image-generation.md](video-image-generation.md). This file is the
> **what to design and how to prompt** layer for posters specifically.

## When this runs

- **Gate 7, after** each format's `final.mp4` / `final-{formatId}.mp4` is
  rendered.
- **Requires** `AI_GATEWAY_API_KEY` (same as other generated images). If it
  is not set, fall back to `npx remotion still` from the strongest on-screen
  moment and say so plainly when presenting at Gate 8 - don't invent a
  poster workflow that can't run.
- Generate **once per format**, reuse the file everywhere that format needs
  a poster/thumbnail. Don't regenerate "the same" poster per critic
  iteration unless Gate 8 (or the user) specifically rejected the poster.

## What a poster is for (vs a video frame)

| Job | Poster | Mid-video Remotion still |
|-----|--------|--------------------------|
| Sell the click / set expectation before play | Yes - primary job | No |
| Match the production's theme (palette, mood, product feel) | Yes | Incidental |
| Survive tiny preview sizes (YouTube sidebar, feed grid) | Must | Often fails |
| Exact UI chrome / readable dense labels | Prefer Remotion still or composite | Strong |

Design the poster as a **standalone marketing still** that still feels like
it belongs to *this* video - same theme tokens, same hook idea - not a
screenshot dump and not a generic stock "tech abstract".

## Inputs to gather before prompting

Read these before writing a single prompt - don't invent a second brief:

1. **`brief.md`** - topic, theme/angle (the content hook), audience/tone,
   target platform(s), approved format(s).
2. **`shared/theme.ts`** - palette, type mood, motion/visual language
   (translate to plain English in the prompt: "deep navy background,
   single electric-teal accent, clean sans geometric UI feel" - not hex
   dumps alone).
3. **Script hook** - the opening idea the thumbnail must promise (one
   claim, one contrast, one number - not the whole script).
4. **Format/platform** - which channel this file will sit on (drives size,
   safe zones, and composition rules below).
5. **Optional Remotion reference still** - one strong frame
   (`npx remotion still`) used only as *visual brief* for colour/subject;
   do not ship that frame as the poster unless generation is unavailable.

## Channel styles (pick the row that matches the brief)

Generate **one poster per approved format**, using the matching style row.
If one production ships both YouTube long-form and Reels, that is two
prompts and two files - not one image stretched.

### YouTube long-form (16:9)

| Spec | Guidance |
|------|----------|
| Canvas | Prefer `1536x1024` or `1920x1088` (multiples of 16); ship/export targeting **1280×720** (YouTube's standard custom thumbnail). Stay under ~2 MB when delivering JPG/PNG for upload. |
| Reads at | ~168×94 (sidebar) and ~320×180 (mobile home) - design for the tiny size, not the full canvas. |
| Safe zone | Keep face/focal subject + any text in the **centre ~70%**. Leave the **bottom-right** clear - YouTube's duration badge covers it. |
| Text | **3–5 words max**, huge, bold, high contrast. If you can't read it when the image is shrunk to a few centimetres wide, cut words or enlarge. |
| Focal point | **One** dominant subject. For this skill's UI/product videos: a single oversized UI fragment, a bold before/after split, or one hero number - not a full dense screen capture. |
| Contrast | High. Test mentally against both light and dark YouTube chrome. Low-contrast greys disappear in the feed. |

### YouTube Shorts / TikTok / Reels / Stories (9:16)

| Spec | Guidance |
|------|----------|
| Canvas | `1024x1536` or `1080x1920`-class portrait size via gpt-image-2's free-size rules (edges multiples of 16). |
| Dual crop | Profile grids crop to roughly the **centre square / 4:5**. Put the hook subject + text in the **centre band**; treat extreme top/bottom as background-only. |
| UI overlays | Same discipline as the video itself ([multi-format-layout.md](multi-format-layout.md)): keep critical content out of top ~200px, bottom ~340px, and the right ~120px like/share rail when the poster will also be used as an in-app cover. |
| Text | Even shorter than YouTube long-form when possible (2–4 words). Vertical covers are scanned as icons in a grid. |
| Focal point | Large, centred, simple. A busy full-UI mock will turn to mush at grid size. |

### Feed / LinkedIn / square-ish (1:1 or 4:5)

| Spec | Guidance |
|------|----------|
| Canvas | `1024x1024` (1:1) or a tall square near `1080x1350` (4:5 - prefer when the platform allows; more feed real estate). |
| Crop risk | Feeds and link previews may round corners or crop edges - keep the subject **centred**, not edge-anchored. |
| Text | Short headline + optional tiny brand mark. Avoid paragraph copy. |
| Tone | Slightly more "card in a feed" than cinematic YouTube - still theme-aligned, not a screenshot of the whole video. |

### Website / landing hero poster (matches the video embed)

| Spec | Guidance |
|------|----------|
| Canvas | Match the video's aspect (usually 16:9 at `1536x1024`+). |
| Role | HTML `poster` attribute / og preview - should look like a polished first frame of the *brand*, not a clickbait thumbnail. |
| Text | Optional. Often stronger with **no** big clickbait words if the page already has a headline beside the player - avoid duplicating the page H1. |
| Detail | Can afford slightly richer UI detail than a YouTube sidebar thumb, but still one clear focal idea. |

## Theme alignment (non-negotiable)

The poster must feel like the same production as Gate 4:

- **Palette** - name the background, primary surface, and one accent from
  `theme.ts` in the prompt. Don't let the model invent a second brand.
- **Mood** - match audience/tone from the brief (calm product teaching vs
  punchy social hook).
- **Subject** - pull from the video's actual visual language (browser
  chrome, chart, comparison labels, product surface) so the click isn't
  bait-and-switch.
- **Not a collage of every scene** - one idea, same discipline as
  [design-and-continuity.md](design-and-continuity.md)'s one-focus rule.

If Gate 4 used a generated background image, you may reference that file's
look in the prompt ("same soft navy field and diagonal light as the video
background") but still generate a **new** poster composition - don't just
re-export the background.

## What to focus on in the prompt

Write prompts as a **art direction brief**, not a keyword salad. Cover:

1. **Channel + aspect** - "YouTube thumbnail, 16:9, mobile-first, reads at
   tiny size"
2. **Single hook** - the one claim/number/contrast from the brief
3. **Primary subject** - what occupies ~40%+ of the frame
4. **Theme palette and lighting** - concrete, from `theme.ts`
5. **Typography intent** - either "no text" (then composite later) or exact
   short copy in quotes with "huge bold sans-serif, high contrast"
6. **Safe-zone constraints** - e.g. "keep bottom-right empty for duration
   badge", "subject centred for profile-grid crop"
7. **Negatives** - "no watermarks, no fake YouTube UI chrome, no tiny
   unreadable labels, no cluttered multi-panel dashboard, no extra logos"

### Prompt skeleton

```text
{Channel} poster/thumbnail, {aspect}, designed to read clearly when tiny.
Theme: {palette + mood from theme.ts}.
Subject: {one oversized focal idea tied to the video hook}.
Hook text (optional): "{3-5 words}" in huge bold high-contrast sans-serif,
placed in the safe zone ({channel-specific}).
Composition: {centred / split before-after / big number + UI fragment}.
Avoid: watermarks, platform UI chrome, busy full-screen UI, tiny text,
clutter, unrelated stock metaphors.
```

### Domain-specific subject choices (this skill)

Prefer subjects that match UI/product explainers:

| Strong | Weak |
|--------|------|
| One oversized control/card with a clear state | Full app screenshot at readable desktop density |
| Before/after split with a single variable labelled | Three unrelated features in one frame |
| One huge number / metric as the hero | Paragraph of feature bullets |
| Abstract theme field + one product silhouette | Generic "AI brain / neon network" stock cliché |

Faces and exaggerated emotion convert on many YouTube niches - **only use
them if the brief/brand actually uses people**. Don't force a stock smiling
presenter onto a product UI brand that never shows humans.

## Text strategy with gpt-image-2

`gpt-image-2` can render short bold headlines well enough for many posters,
but exact brand typefaces and pixel-perfect kerning are still unreliable.

- **Default for social/YouTube CTR posters:** put the **3–5 word hook in the
  image prompt** so the file is self-contained for upload.
- **If brand type must be exact:** generate a **text-free** thematic visual,
  then composite the real type in a small Remotion `Poster` composition (or
  any design tool) and export that as `poster.png`. Say which path you took
  when presenting at Gate 8.
- **Never** ask the model for dense UI copy, URLs, long sentences, or tiny
  labels - those fail the "reads when tiny" test even when spelled right.

## Generation call (poster-specific)

Reuse the same `generateImage` pattern as
[video-image-generation.md](video-image-generation.md); only the prompt,
size, and quality choice change:

```ts
import { experimental_generateImage as generateImage } from "ai";
import { writeFileSync } from "fs";

const model = process.env.AI_GATEWAY_IMAGE_MODEL ?? "openai/gpt-image-2";

const result = await generateImage({
  model,
  prompt: posterPrompt, // built from the skeleton above
  size: posterSize,     // e.g. "1536x1024" | "1024x1536" | "1024x1024"
  providerOptions: {
    openai: {
      quality: "high", // posters are the hero still - prefer high over medium
      background: "opaque",
    },
  },
});

writeFileSync(
  `public/video/${slug}/poster-${formatId}.png`, // or poster.png if single format
  Buffer.from(result.image.base64, "base64"),
);
```

- **Quality:** `high` is appropriate here (the image *is* the hero asset).
  Use `medium` only for a cheap draft while iterating the prompt with the
  user; don't ship `low`.
- **One regenerate max** if text is garbled or the subject missed the brief;
  then simplify the prompt or switch to text-free + composite. Same cost
  discipline as other generated assets.

## Self-check before presenting (Gate 7 → 8)

- [ ] One poster file per approved format, correct aspect for that channel
- [ ] Theme palette/mood matches `theme.ts` / the video (not a second brand)
- [ ] Single focal idea tied to the brief's hook
- [ ] Text ≤ ~5 words (or intentionally text-free) and legible when shrunk
- [ ] Channel safe zones respected (YouTube duration badge; vertical
      centre crop; feed centring)
- [ ] No fake platform chrome, watermarks, or cluttered full-UI dump
- [ ] Fallback to Remotion still only when AI Gateway is unavailable - and
      that fallback still picks the strongest moment, not frame 0

## Relationship to other references

- Call setup / sizes / quality tiers:
  [video-image-generation.md](video-image-generation.md)
- Format safe zones inside the *video*:
  [multi-format-layout.md](multi-format-layout.md)
- Export expectations:
  [production-quality-guidelines.md](production-quality-guidelines.md)
- Theme continuity:
  [design-and-continuity.md](design-and-continuity.md)
