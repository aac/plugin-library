# Plugin Research Guide

This guide is for Claude Web when researching plugins for Andrew's Logic Pro plugin library.

## Your Job

Research plugins and output structured JSON. You don't need to know anything about the HTML file or how the data gets integrated—just focus on producing high-quality research.

## What to Research

For each plugin, gather:

1. **Manual URL** - Direct link to PDF or web manual
2. **Product URL** - Official product page
3. **Description** - 2-3 sentences covering:
   - What hardware/software it emulates (if applicable)
   - Key sonic character
   - What makes it special or unique
4. **Use Cases** - 4 common applications (professional context, genre-agnostic)
5. **Things to Try** - 4 specific, actionable experiments tailored to Andrew's context

## Andrew's Context

Tailor "things to try" to his situation:
- **Genre**: Rock/alternative production
- **Influences**: Peter Gabriel, Nine Inch Nails, Queens of the Stone Age
- **Workflow**: Solo artist (writes, performs, records alone in Logic Pro)
- **Focus**: Practical production and mixing techniques and creative sound design

## Quality Standards

### Description
Be factual and specific. Focus on character. Include details about key features.

**Good:**
> Emulation of BOSS CE-1 (1976) - world's first chorus pedal, originally built into Jazz Chorus amp. Uses bucket-brigade delay circuit for warm, organic modulation. Dual mode: Classic (gentle) and Dual (intense stereo).

**Bad:**
> A chorus effect plugin with various controls for modulation.

### Use Cases
Professional applications, not beginner tips.

**Good:**
```json
[
  "Classic 80s-style chorus on clean guitars and synths",
  "Subtle widening of vocals without obvious modulation",
  "Adding movement to static synth pads",
  "Creating dimension on floating ambient guitars"
]
```

**Bad:**
```json
[
  "Use on guitars",
  "Good for vocals",
  "Try on synths"
]
```

### Things to Try
Specific settings and techniques, referencing Andrew's influences where relevant.

**Good:**
```json
[
  "Dual mode with slower speed on power chords for new wave thickness",
  "Classic mode extremely subtle on lead vocals (NIN-style)",
  "Use on room mic bus for dimension without reverb",
  "Stack with tape delay for Peter Gabriel atmospheric guitar tones"
]
```

**Bad:**
```json
[
  "Try it on drums",
  "Experiment with different settings",
  "Use it creatively"
]
```

## Variant Groups

Some plugins are variations of the same hardware. When researching these, note the sonic differences between variants:

- **1176**: Rev A (aggressive/punchy), AE (anniversary, 2:1 ratio), LN Rev E (lower noise/refined)
- **LA-2A**: LA-2 (original, slowest), Gray (warmer), Silver (fastest, brightest)
- **Pultec**: EQP-1A (lows/highs), MEQ-5 (mids), HLF-3C (filters)

## Where to Find Plugins Needing Research

Check `research-queue.json` in this repo. It lists all unresearched plugins with any known URLs.

## Output Format

Output complete plugin data as JSON. This works for both existing plugins (updates) and new plugins (inserts).

```json
{
  "plugins": [
    {
      "id": "plugin-alliance-bx-cleansweep-v2",
      "name": "bx_cleansweep V2",
      "vendor": "Plugin Alliance",
      "type": "effect",
      "category": "filter",
      "compatible": true,
      "manualUrl": "https://www.plugin-alliance.com/media/bx_cleansweep_V2_Manual.pdf",
      "productUrl": "https://www.plugin-alliance.com/en/products/bx_cleansweep_v2.html",
      "description": "Simple but musical high-pass and low-pass filter designed for cleaning up tracks before mixing. Features gentle 6dB/octave slopes that remove rumble and hiss without affecting the body of the sound.",
      "useCases": [
        "Removing low-end rumble from vocals and guitars",
        "Taming harsh high frequencies on bright sources",
        "Cleaning up room mic bleed before processing",
        "Gentle master bus filtering for final polish"
      ],
      "thingsToTry": [
        "High-pass at 80-100Hz on rhythm guitars to make room for bass",
        "Low-pass at 12-15kHz on distorted guitars to reduce fizz",
        "Use on drum overheads to control cymbal harshness",
        "Subtle high-pass on bass guitar to tighten low end without losing weight"
      ],
      "variantGroup": null,
      "notes": null
    }
  ]
}
```

**Field reference:**
- `id`: kebab-case identifier (e.g., `vendor-plugin-name`)
- `type`: `effect`, `instrument`, or `MIDI-controlled effect`
- `category`: See existing categories in the app, or suggest a new one
- `compatible`: Apple Silicon compatibility (true/false)
- `variantGroup`: Only if part of a variant family (e.g., `"1176"`)

## Handoff to Claude Code

Once you have your research JSON, tell the user:

> Here's the plugin data for [plugin names]. Give this to Claude Code to add to plugin-library.html.

Claude Code will update existing plugins or add new ones as needed.
