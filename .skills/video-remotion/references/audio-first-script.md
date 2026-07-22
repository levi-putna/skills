# Audio-first narration script

The script exists to be **spoken by ElevenLabs**, not read on screen. Write
for the ear: a real person explaining something, with natural flow and human
pacing. On-screen text/captions are a secondary layer added later - never
write the script as if it were the on-screen copy.

## What "natural" means here

| Aim | Avoid |
|-----|--------|
| Conversational, direct address ("you'll", "don't") | Brochure / marketing-carousel copy |
| Contractions | Stiff formal prose |
| Short beats + a longer explainer sentence | Same-length staccato lines that march |
| Breath space after the hook, the problem, the fix, and the takeaway | One continuous paragraph wall |
| Ideas you can say in one breath per beat | Nested clauses, stacked jargon |

## Flow and pace

A generic shape that works for most explainer/demo videos - adapt the beats
to what's actually being explained, don't force content into a shape that
doesn't fit:

1. **Hook** - one plain sentence establishing why this matters. Then breathe.
2. **Show the problem / current pain** - concrete, visual. Short sentences OK.
3. **Beat** - let the problem land before the fix.
4. **Show the better version / the feature / the fix** - parallel structure
   to the problem, so the contrast is obvious.
5. **Takeaway** - the one thing to remember, spoken naturally, not a bullet
   dump. If there are multiple points, use spoken ordinals ("One… Two…
   Three…"), not visual bullets.
6. **Land** - one short closing line.

Pace like speech, not typesetting:

- Prefer **punctuation and sentence rhythm** for most pauses (commas, full
  stops, em-dashes).
- Use ElevenLabs `<break time="0.4s" />` / `0.8s` / `1.2s` **sparingly**, for
  beats the voice must not rush: after the hook, between problem→fix, before
  a takeaway list, before the final line. Don't stack breaks. Cap ~3s total.
- Vary rhythm: mix a punchy line with a slightly longer one. Don't write
  every sentence the same length.
- Aim ~130-150 spoken words per minute. For a ~60s video, that's roughly
  90-130 words of body VO; scale proportionally for a longer target.

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
- Each scene's `narration` slice (see Gate 2) must be a **complete speakable
  unit** that still flows into the next when the full script is joined - no
  mid-clause cliffhangers unless the following scene finishes the thought.

## Tone and voice

This skill does not assume a house tone. Confirm once at Gate 1 (unless the
user already specified it): who's the audience, how formal/casual, first-
or second-person address, and whether it's teaching a concept, walking
through a UI, or demoing a feature end-to-end. Keep that choice consistent
for the rest of the production - don't drift tone scene to scene.

## Gate 1 self-check (before presenting)

- [ ] Sounds like a person talking, not a page excerpt
- [ ] The shape (hook → problem → fix → takeaway → land, or whatever fits
      this content) is clear by ear alone
- [ ] Has breathing room (punctuation and/or a few break tags)
- [ ] No code/selector/path read-through
- [ ] Roughly matches the target duration at a natural pace
- [ ] Tone is consistent with what was confirmed with the user
