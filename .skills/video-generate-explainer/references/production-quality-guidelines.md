# Production quality guidelines

General practice for making a short explainer/example video actually feel
professionally produced, not just technically correct. These are things a
video production manager would flag on review - apply them by default,
but they're guidelines, not gates; call out a deliberate deviation rather
than silently skipping one.

## The first 1-2 seconds are the hook

At under 20 seconds total, there is no slow build-up budget. Whatever the
first scene shows must be legible and interesting on frame one - not a
blank canvas that fades something in over a second, not a logo-only cold
open with nothing else happening, unless the brief is specifically a
logo/brand sting. Check this concretely at Gate 8 ("brief fit"): would
someone scrolling past stop on frame one?

## Captions, by default

Short-form video like this is very often watched muted (social feeds,
autoplay previews, embedded on a page next to other content). Default to
**on** for burned-in captions using the `@remotion/captions` pipeline this
skill already supports (Gate 5, step 5) unless the user explicitly says
otherwise. Don't add this silently, though - confirm it at Gate 5 the same
way you'd confirm anything else, since it does add a visual layer the user
might not want for every project (e.g. a purely ambient/brand piece with no
literal narration to caption).

## Audio mixing between narration and clip-native audio

The continuous ElevenLabs narration is the backbone track. Any clip that
carries its own audio (a Veo `generateAudio: true` clip, or a real
user-supplied clip with sound) needs a deliberate mixing decision, not a
default mute-or-don't:

- **Under narration**: mute the clip's audio (the default rule in the main
  SKILL.md). Two simultaneous voices/soundscapes reads as a mistake, not a
  choice.
- **During a wordless beat** (no narration playing over that scene): the
  clip's native audio may play, but duck it in on entry and out on exit
  (a short fade, not a hard cut) so it doesn't pop against the narration's
  silence on either side.
- **Never** let a clip's audio be louder, in perceived loudness, than the
  narration was during narrated scenes - that's the track the whole video
  is paced around.

## Loudness consistency

Aim for a consistent perceived loudness across the whole timeline - the
narration shouldn't jump in volume between scenes, and any clip audio that
is allowed to play (per the mixing rule above) should be leveled to sit
comfortably next to it, not louder or noticeably quieter. If the tooling
in use doesn't do formal LUFS metering, at minimum listen straight through
once at Gate 8 specifically checking for volume jumps between scenes - a
jump is usually a sign a clip's native audio wasn't attenuated to match the
narration.

## Safe margins, even within an approved format

Gate 1 should already have surfaced every format this production actually
needs to render (16:9, 9:16, etc. - see Gate 1/4/7 in the main SKILL.md).
Safe margins are a second, cheaper line of defence on top of that: even
within a single approved format, and doubly so if a viewer might later
crop/reuse the delivered file somewhere the production wasn't explicitly
built for, keep the scene's single most important element (the thing named
in `keyPoint`) roughly centred and inside the middle ~80% of the frame
width rather than pinned to a far edge. This isn't a hard rule (a
deliberately edge-anchored composition can be a valid choice) - just don't
do it by accident.

**If a 9:16 format is destined for TikTok/Reels/Shorts/Stories**, this
general ~80% rule isn't tight enough - those apps overlay their own UI
(captions, username, like/share rail) over specific bands of the frame
regardless of what the video contains. Use
[references/multi-format-layout.md](references/multi-format-layout.md)'s
platform safe-zone bands for that format instead, and see the same file
for how a shared component should actually reflow (not just rescale)
between a 16:9, 1:1/4:5, and 9:16 layout.

## Export and delivery

- Export each approved format's `final.mp4`/`final-{formatId}.mp4` at the
  resolution the brief specified (don't silently downscale to save render
  time).
- Export a poster frame (`poster.png`/`poster-{formatId}.png`, Gate 7
  step 5) from the strongest single on-screen moment - usually mid-way
  through the hook or the clearest UI moment - for use as a thumbnail/video
  poster attribute. Don't default to frame 0 without checking it's actually
  representative, and don't assume the same frame works for every format if
  their layouts genuinely differ.
- If a new platform/format is needed after Gate 1 already closed, treat it
  as a Revisions-table change (main SKILL.md) - add it to the brief and
  `scenes.json`, verify shared components actually adapt to it (Gate 4),
  and render/register it (Gate 7) - rather than a from-scratch second
  production.

## Cost and iteration awareness

Component scenes are free to iterate - edit the `.tsx`, re-render a still,
done. Generated video/image calls are not: they cost real money and real
wall-clock time per call. Keep that asymmetry in mind when deciding what to
flag as `generated-video`/AI-image at all (Gate 3/4), and don't treat a
generation call with the same "just try it and see" attitude as a code
edit - see the cost-awareness note in video-image-generation.md.

## Accessibility

Even for a short piece, write (or reuse) a one-line plain-text description
of what the video shows and says - useful as alt text wherever the video is
embedded, and a cheap way to double-check the "brief fit" axis at Gate 8
(if you can't summarise it in one accurate sentence, the brief probably
didn't land clearly).

## Don't over-produce

None of the above is licence to add motion, texture, or audio layers the
brief doesn't call for. A clean, quiet, single-focus video that nails the
one idea it set out to explain beats a busier one with more "production
value" bolted on. When in doubt, prefer the plainer version and let Gate 8
catch it if that plainness reads as unfinished rather than intentional.
