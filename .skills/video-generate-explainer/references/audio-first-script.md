# Audio-first narration script

The script exists to be **spoken by ElevenLabs**, not read on screen. Write
for the ear: a real person explaining something, with natural flow and human
pacing. On-screen text/captions are a secondary layer added later - never
write the script as if it were the on-screen copy.

**This skill's format is capped at 20 seconds total.** That's roughly
40-50 spoken words of body VO at a natural pace (see the word-budget math
below) - there is no room for a slow wind-up. The opening line has to work
as the hook itself, not lead into one a few seconds later.

## What "natural" means here

| Aim | Avoid |
|-----|--------|
| Conversational, direct address ("you'll", "don't") | Brochure / marketing-carousel copy |
| Contractions | Stiff formal prose |
| Short beats + a longer explainer sentence | Same-length staccato lines that march |
| Breath space after the hook, the problem, the fix, and the takeaway | One continuous paragraph wall |
| Ideas you can say in one breath per beat | Nested clauses, stacked jargon |

## Flow and pace

At ≤20 seconds, most explainer shapes compress to three or four beats, not
six - pick the ones that carry the single idea this video is for, don't
force every beat below into a piece this short:

1. **Hook** - the opening line itself must establish why this matters and
   grab attention on frame one. There's no separate "lead-in" before it.
2. **Show it** - the problem, the feature, or the fix, whichever single
   idea this video exists to land. Concrete, visual, short sentences.
3. **Contrast or confirmation** (optional, only if it fits in budget) - a
   brief beat that sharpens the point (before→after, or a one-line "why it
   matters").
4. **Land** - one short closing line. No multi-point takeaway list at this
   length - if there's more than one point, this is a sign the brief needs
   to be narrowed (revisit the Gate 1 scope), not that the script needs to
   talk faster.

Pace like speech, not typesetting:

- Prefer **punctuation and sentence rhythm** for most pauses (commas, full
  stops, em-dashes).
- Use ElevenLabs `<break time="0.3s" />` / `0.5s` **sparingly** - at most
  once or twice in a script this short (e.g. after the hook, before the
  final line). A 20-second video has no room for the 3 breaks a longer
  script might use; every fraction of a second of silence is a real
  proportion of the total runtime here.
- Vary rhythm: mix a punchy line with a slightly longer one. Don't write
  every sentence the same length.
- Aim ~130-150 spoken words per minute. For a 20s video that's roughly
  **40-50 words of body VO** - treat this as a hard budget, not a
  suggestion; count words before presenting the script at Gate 2.

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

- [ ] Sounds like a person talking, not a page excerpt
- [ ] The opening line works as the hook itself - no lead-in before it
- [ ] The shape (hook → show it → contrast/land, or whatever fits this
      content) is clear by ear alone
- [ ] Has breathing room (punctuation and/or a break tag or two, not more)
- [ ] No code/selector/path read-through
- [ ] **Word count is within the ~40-50 word body-VO budget** for a 20s cap
      (scale down proportionally if the user agreed to a shorter target)
- [ ] Carries exactly one idea - if it needs a multi-point takeaway, the
      brief is too big for this format; narrow it instead of rushing it
- [ ] Tone is consistent with what was confirmed with the user
