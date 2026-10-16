# Prompt Engineer for Suno

Turn any song idea into paste-ready Suno Style and Lyrics fields that don't get silently cut off.

Give it a song idea, a vibe, or your own lyrics, and it returns copy-ready Style, Lyrics and Exclude Styles fields for Suno V4.5-V5.5, with character counts and a plan for building the song with Extend. It handles genre blending, spoken word, performance cues and syllable matching. When a generation sounds off, it figures out why before rewriting. Save your recurring sounds as presets and reuse them in one line.

## Install
Skills-only plugin. Layout: `plugin.json` at the root and `skills/suno-prompt-engineer/SKILL.md`.

## Example prompts
- "Rainy-night drive song: 80s synthwave meets lo-fi hip-hop, breathy female vocals, about 95 BPM."
- "Here are my lyrics. Format them for Suno as calm spoken word over jazzy piano, and split them into Extend chunks."
- "My last Suno take sounded generic and the singer rushed the chorus. Here's the prompt I used. Fix it."

## Testing instructions
1. Install the plugin and start a new chat. Confirm the skill `suno-prompt-engineer` is available.
2. Send the first example prompt. Expect a Title, a Style block with a character count under 1000, a Lyrics block with structure tags and a count under 5000, and an Extend plan.
3. Send the third example prompt. Expect it to diagnose the symptom before rewriting, and to change only one or two things.
4. Ask it to save a preset, then reuse it by name in a new request.
No credentials are needed. This plugin does not call Suno or any external service.

## License
MIT
