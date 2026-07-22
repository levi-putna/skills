# Audio-first narration script

The script exists to be **spoken by ElevenLabs**, not read on screen. Write
for the ear: a real person explaining something, with natural flow and human
pacing. On-screen text/captions are a secondary layer added later - never
write the script as if it were the on-screen copy.

**Word budget and beat count scale with the brief's approved
`targetLengthSeconds` (Gate 1) - there is no fixed cap.** A short target
(15-20s) has roughly 40-50 spoken words of body VO and no room for a slow
wind-up; a ~1-minute target has room for ~130-150 words and a few more
beats; a multi-minute target has room for real scene-setting and several
distinct sections. Whatever the length, the opening line still has to work
as the hook itself - a longer runtime buys more room *after* the hook, not
permission to lead into one a few seconds later.

## What "natural" means here

| Aim | Avoid |
|-----|--------|
| Conversational, direct address ("you'll", "don't") | Brochure / marketing-carousel copy |
| Contractions | Stiff formal prose |
| Short beats + a longer explainer sentence | Same-length staccato lines that march |
| Breath space after the hook, the problem, the fix, and the takeaway | One continuous paragraph wall |
| Ideas you can say in one breath per beat | Nested clauses, stacked jargon |

## Flow and pace

The number of beats a script can afford scales with the approved target
length - pick the shape that fits `targetLengthSeconds`, don't force a
15-second shape into a 3-minute brief or pad a thin idea out to fill one:

- **Short target (roughly ≤30s)**: three or four beats, no more. Compress
  to the single idea this video is for.
- **Around the ~1-minute default**: five to seven beats - room for a short
  context/problem beat before the payoff, and a slightly fuller landing,
  but still one throughline, not a list of unrelated points.
- **Multi-minute target**: organise into a small number of clearly-labelled
  sections (each with its own mini hook → show it → land arc), rather than
  one long undifferentiated ramble. Chapter/section labels on screen (see
  [content-formula.md](content-formula.md)'s label system for one way to do
  this) help a viewer track where they are.

Whatever the length, the shape itself stays:

1. **Hook** - the opening line itself must establish why this matters and
   grab attention on frame one. There's no separate "lead-in" before it,
   regardless of overall length.
2. **Show it** - the problem, the feature, or the fix. For a short target
   this is one beat; for a longer target this can be several beats/sections
   covering distinct points, provided they still serve one throughline.
3. **Contrast or confirmation** (optional) - a beat (or, at longer lengths,
   more than one) that sharpens the point (before→after, or a "why it
   matters" aside).
4. **Land** - a closing line/beat. A short target has room for exactly one
   closing line; a longer target can afford a brief recap of the key
   points before the final line. If a short-target script wants a
   multi-point takeaway list, that's a sign the brief needs to be narrowed
   (revisit the Gate 1 scope) rather than talking faster to cram it in.

Pace like speech, not typesetting:

- Prefer **punctuation and sentence rhythm** for most pauses (commas, full
  stops, colons). **Never use an em dash (—)** in narration, on-screen
  copy, or anywhere else this skill writes - use a spaced hyphen (` - `),
  a comma, or a full stop instead (hard rule in the main SKILL.md).
- Use ElevenLabs `<break time="0.3s" />` / `0.5s` deliberately, scaled to
  length - as a rough feel, roughly one natural breath point every 10-15
  seconds of runtime (after the hook, between beats/sections, before the
  final line) rather than a fixed count. A short script (≤20s) has room for
  at most one or two; every fraction of a second of silence is a much
  bigger proportion of the total runtime there than in a longer piece.
- Vary rhythm: mix a punchy line with a slightly longer one. Don't write
  every sentence the same length.
- Aim ~130-150 spoken words per minute. Compute the body-VO word budget from
  the approved target: roughly `targetLengthSeconds / 60 * 140` words (e.g.
  ~40-50 words for a 20s target, ~130-150 for a 1-minute target, ~400-450
  for a 3-minute target). Treat this as a real budget, not a suggestion -
  count words before presenting the script at Gate 2, and leave a little
  headroom for breath-point pauses rather than spending literally every
  second on speech.

## Speakable wording (ElevenLabs-friendly)

- Write words the way they're said. Prefer "jane at example dot com" over a
  raw email address; describe code intent in plain language rather than
  reading punctuation/syntax aloud.
- **Never read code, selectors, file paths, or symbol-heavy syntax aloud.**
  Describe intent in plain language ("set the environment variable for your
  API key" rather than reading `ELEVENLABS_API_KEY=sk-...` character by
  character). Point denser detail to on-screen text/captions instead.
- Expand awkward digits/symbols into speech ("two pixels", "twenty
  twenty-six", "v4" → "version four").
- No tongue-twister jargon chains - split or paraphrase.
- Each scene's `narration` slice (see Gate 3) must be a **complete speakable
  unit** that still flows into the next when the full script is joined - no
  mid-clause cliffhangers unless the following scene finishes the thought.

## Tone and voice

This skill does not assume a house tone. Audience/tone is confirmed once at
Gate 1 (the planning brief) - who's the audience, how formal/casual, first-
or second-person address, and whether it's teaching a concept, walking
through a UI, or demoing a feature end-to-end. Read that decision from
`brief.md` rather than re-asking at Gate 2, and keep it consistent for the
rest of the production - don't drift tone scene to scene.

## Gate 2 self-check (before presenting)

- [ ] No em dashes (—) anywhere in the script - use commas, full stops,
      colons, parentheses, or a spaced hyphen (` - `) instead
- [ ] Sounds like a person talking, not a page excerpt
- [ ] The opening line works as the hook itself - no lead-in before it
- [ ] The shape (hook → show it → contrast/land, or whatever fits this
      content) is clear by ear alone
- [ ] Has breathing room scaled to length (punctuation and/or break tags
      roughly every 10-15s of runtime, not more)
- [ ] No code/selector/path read-through
- [ ] **Word count matches the approved `targetLengthSeconds`** at
      ~130-150 wpm (roughly `targetLengthSeconds / 60 * 140` words) - don't
      assume the 20s-era 40-50 word figure applies unless that's actually
      the approved target
- [ ] Carries one clear throughline - a short target with a multi-point
      takeaway is a sign the brief is too big for that length; narrow the
      scope or revisit the target with the user instead of rushing it
- [ ] Tone is consistent with what was confirmed with the user
