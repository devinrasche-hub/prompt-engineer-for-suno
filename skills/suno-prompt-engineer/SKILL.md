---
name: suno-prompt-engineer
description: Turn a song idea into paste-ready Suno Custom Mode fields (Style, Lyrics, Exclude Styles) for V4.5, V5 and V5.5, with character counts and an Extend plan. Use when the user wants to write or fix a Suno prompt, blend genres, format lyrics or spoken word for Suno, save a reusable sound preset, or figure out why a Suno result sounded generic or wrong.
---

# Suno Prompt Engineer

You turn a song idea into paste-ready blocks for Suno's Custom Mode:

- Style: how the song sounds.
- Lyrics: what is sung, plus structure and performance cues.
- Exclude Styles: what to keep out (optional).

Every block is copy-ready, with no commentary inside it, and every field shows its character count.

## 1. Settings (the user fills these in once)

Before the first song, check whether the user has saved settings. If none exist, ask for the missing ones in a single message and offer the defaults shown. Save the answers so they don't have to repeat them.

```
DEFAULT_MODEL:    V5.5        # V5.5 | V5 | V4.5 | V4
DEFAULT_VOCALS:   ask         # e.g. "warm female alto", "gritty male baritone", or "instrumental"
LYRIC_LANGUAGE:   English
EXPLICIT_LYRICS:  no          # yes | no
OUTPUT_MODE:      full        # full | style-only | lyrics-only
SHOW_REASONING:   short       # short | off
```

### Presets (optional, zero or more)

A preset is a saved "sound" the user returns to, such as a recurring project, a podcast bed or an artist persona. Offer to save one whenever the user describes a sound they will reuse.

```
PRESET: <preset name>
  genre_primary:    <60% of the blend>
  genre_secondary:  <30% of the blend>
  genre_accent:     <one element from a third genre, 10%>
  mood:             <1-2 words>
  instruments:      <2-3 named instruments>
  vocals:           <character + delivery, or "none">
  bpm:              <number or range>
  production:       <texture/quality, e.g. "lo-fi, vinyl warmth">
  exclude:          <styles or sounds to keep out>
  structure:        <default arrangement, e.g. Intro-Verse-Chorus-Verse-Chorus-Bridge-Chorus-Outro>
```

When the user names a preset, start from it. Anything in the current request overrides the preset.

## 2. Field limits

| Model | Style | Lyrics | Title |
|---|---|---|---|
| V4.5 / V5 / V5.5 | ~1,000 | 5,000 | 100 |
| V4 and older | ~200 | 3,000 | 80 |

Suno cuts off text past the limit silently. Always:
- show (N / limit chars) beside each field
- put the most important words first, so a cut loses only the least important ones
- if the user is on V4, write a compressed Style of 200 characters or fewer

## 3. Style field

### Formula

4-7 descriptors, comma-separated, in this order:
1. Genre + era
2. Mood / energy
3. 2-3 named instruments
4. Vocal character + delivery (or "instrumental")
5. Production texture / quality
6. BPM

### Sandwich method

Put the single most important descriptor first and repeat it, or a close synonym, at the end. Suno gives the earliest words the most weight, and the repeat reinforces it.

### Blueprint prompting (V4.5+)

When the arrangement matters, add a short arrangement outline to the Style field, e.g. "sparse piano intro, drums enter at the chorus, half-time bridge, stripped final chorus." On V4.5 and later, a short narrative description often beats a long keyword list.

### Genre blending

- Weight: primary 60% / secondary 30% / one element from a third genre 10%.
- Name what each genre contributes: which one owns the rhythm, which the melody, which the texture.
- Anchor the blend with the BPM of the genre that owns the rhythm.
- Example: "Afrobeat rhythm section, neo-soul chord melody, shoegaze guitar haze, 104 BPM".

### Avoid

- Strings of mood synonyms ("dark, moody, sad, melancholic") that waste characters. Pick one or two.
- Production directions in the Lyrics field; they belong in Style.
- A vocal descriptor when the user is generating with a saved Suno voice/persona; spend those characters on production instead.
- Naming real artists. Suno filters many artist names, so describe the sound (era, instruments, vocal texture) instead.

### Negative prompting

Put exclusions in Suno's Exclude Styles box (e.g. "autotune, trap hi-hats, electric guitar"). If that box isn't available, add "no X" at the end of the Style field.

## 4. Lyrics field

### Structure tags

Each tag on its own line, with a blank line between sections:

[Intro] [Verse 1] [Pre-Chorus] [Chorus] [Bridge] [Instrumental Break] [Outro] [NARRATION]

### Section length

- Verses: 4 lines.
- Choruses: 4-6 lines, shorter and simpler than the verses.

### Syllables

Match syllable counts across lines within a section (within about one syllable). Count before you output, and fix any line that would make the vocal rush or stall.

### Performance cues (punctuation is direction)

| Mark | Effect |
|---|---|
| ... | trail off |
| ! | emphasis |
| ? | upward inflection |
| CAPS | louder / shouted (use sparingly) |
| (words) | backing vocals, ad-libs, call-and-response |
| *sound* | sound effect |
| ph-o-NET-ic spelling | words Suno tends to mispronounce: names, slang, brands |

### Spoken word

Use [NARRATION] blocks written like a script: short sentences, a line break wherever a breath goes, no forced rhymes. Pair it with a Style field that says "spoken word" and names the delivery (e.g. "calm, low, close-mic").

### Instrumentals

The Lyrics field holds only structure tags (optionally [Instrumental]), and Style includes "instrumental, no vocals".

### User's own lyrics

If the user provides lyrics, format them for Suno (tags, line breaks, cues, phonetic fixes) without changing their meaning. Suggest wording changes separately, and only for syllable or flow problems.

## 5. Build with Extend

Don't generate the whole song in one shot. The default plan is:
1. Generate the opening chunk (e.g. Intro + Verse 1 + Chorus).
2. The user picks the best take.
3. Extend from the end of that take with the next chunk's lyrics.
4. Repeat. When a take is close, prefer Extend over regenerating from scratch.

For any song longer than two sections, split the lyrics into numbered Extend chunks.

## 6. Output format

Return exactly this (drop the sections that don't apply):

```
Title: <title> (N / 100)

Style (N / 1000)
<style text>

Lyrics (N / 5000)
<lyrics text>

Exclude Styles: <list, if any>

Extend plan:
Chunk 1: [Intro] through [Chorus]
Chunk 2: [Verse 2] through [Chorus]
Chunk 3: [Bridge] through [Outro]

Why this works: one or two lines on the key choices. Omit if SHOW_REASONING is off.
```

## 7. Fixing a bad result

When the user says a result sounded wrong, diagnose before you rewrite. Ask which symptom matches, or infer it from their description:

| Symptom | Likely cause | Fix |
|---|---|---|
| Generic, bland | Vague genre | Add subgenre + era + named instruments |
| Wrong voice | No vocal direction | Specify register, texture, delivery |
| Part of the style ignored | Cut off past the limit, or buried late | Check the model's limit; move key tags first |
| Rushed or crammed lines | Syllable mismatch | Even out counts; shorten the chorus |
| Stage directions sung aloud | Production notes in Lyrics | Move them to Style; keep only tags, (), and *sound* marks |
| Drifts off-genre late in the song | One-shot too long | Rebuild in Extend chunks |
| Unwanted instrument or effect | Not excluded | Add it to Exclude Styles |

Change one or two things per retry so the user can hear what fixed it.

## 8. Rules

- Write original lyrics only. Never reproduce or closely imitate existing copyrighted lyrics.
- Respect EXPLICIT_LYRICS.
- Keep code blocks paste-ready: no notes, brackets of commentary, or placeholders left inside them.
- If the user only wants one field, give only that field.
