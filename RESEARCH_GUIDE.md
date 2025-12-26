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
Focus on **sonic character** and **what it sounds like**, not just what it does. Avoid marketing language.

**AVOID these phrases:**
- "Comes with...", "Includes...", "Access to..."
- "Revolutionary", "Ultimate", "Professional"
- Feature counts ("1,250+ models", "40,000+ sounds")

Describe the SOUND and CHARACTER, then mention key features that affect the tone.

**Good:**
> Emulation of BOSS CE-1 (1976) - world's first chorus pedal, originally built into Jazz Chorus amp. Uses bucket-brigade delay circuit for warm, organic modulation. Dual mode: Classic (gentle) and Dual (intense stereo).

*Why it's good: Describes what it is, the sonic character (warm, organic), and the key feature (dual mode) that affects sound.*

**Bad:**
> A chorus effect plugin with various controls for modulation.

*Why it's bad: Generic, could describe any chorus plugin.*

**Also Bad:**
> Revolutionary AI Machine Modeling technology for capturing the exact sound of any amp. Comes with 1,250+ Premium Tone Models plus access to 40,000+ free models on ToneNET community.

*Why it's bad: Marketing language, feature count focus, doesn't describe what it SOUNDS like.*

### Use Cases
Balance **WHAT**, **WHEN**, and **WHY**. Describe professional applications with enough context to understand the scenario. Avoid generic descriptions that could apply to any similar plugin.

**Good:**
```json
[
  "Classic 80s-style chorus on clean guitars and synths",
  "Subtle widening of vocals without obvious modulation",
  "Adding movement to static synth pads",
  "Creating dimension on floating ambient guitars"
]
```

*Why it's good: Specific applications with context (80s-style, subtle widening, etc.). You understand WHEN and WHY to use it.*

**Bad:**
```json
[
  "Use on guitars",
  "Good for vocals",
  "Try on synths"
]
```

*Why it's bad: Too generic, no context. Could apply to hundreds of plugins.*

**Also Bad:**
```json
[
  "Recording direct guitar with studio-quality amp simulation",
  "Accessing thousands of professional amp tones instantly",
  "Creating custom Tone Models of your own rig"
]
```

*Why it's bad: Too generic (first one applies to ALL amp sims), feature descriptions (second/third), not actual use cases.*

### Things to Try
**Must be SPECIFIC and ACTIONABLE.** Include settings, modes, or signal chains. Reference Andrew's influences when relevant. Avoid vague suggestions or generic feature highlights.

**Good:**
```json
[
  "Dual mode with slower speed on power chords for new wave thickness",
  "Classic mode extremely subtle on lead vocals (NIN-style)",
  "Use on room mic bus for dimension without reverb",
  "Stack with tape delay for Peter Gabriel atmospheric guitar tones"
]
```

*Why it's good: Specific modes/settings, concrete techniques, references influences.*

**Bad:**
```json
[
  "Try it on drums",
  "Experiment with different settings",
  "Use it creatively"
]
```

*Why it's bad: Vague, no specific guidance, could say this about any plugin.*

**Also Bad:**
```json
[
  "Browse ToneNET for user-created tones by searching your favorite artists",
  "Use TONEX Modeler to capture your favorite pedal chain",
  "Explore vintage and modern amp sounds without hardware"
]
```

*Why it's bad: Feature descriptions, not techniques. "Browse the library" is not an actionable mixing technique.*

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

## Quality Review Process

When you provide research to Claude Code, it can run a **quality review agent** that checks for common issues:

**The review agent checks for:**
- Marketing language ("comes with", "includes", "access to")
- Feature counts instead of sonic descriptions
- Generic use cases that apply to all similar plugins
- Vague "things to try" without specific settings/techniques

**The agent will:**
1. Flag any issues found
2. Suggest specific improvements for each issue
3. Give an overall assessment: PASS or NEEDS WORK

**Important:** The review is **advisory only**. You (the user) make the final decision:
- Accept the research as-is (override the review)
- Request re-research based on feedback
- Manually edit the JSON

The goal is to maintain high quality while preserving flexibility.
