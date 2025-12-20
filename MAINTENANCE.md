# Plugin Library Maintenance Guide

## Project Overview

This is a single-file HTML tool for browsing Andrew's Logic Pro plugin collection. The goal is to have researched descriptions, use cases, and manual links for each plugin so he can quickly find the right tool and learn about plugins he hasn't explored yet.

## Current State

- **72 plugins** extracted from screenshots, organized by vendor
- **Interface complete**: filtering, search, detail panel, manual-read tracking, variant grouping
- **Research status**: Skeleton data only - descriptions, use cases, and links are empty
- **Two plugins to remove**: `plugin-alliance-ampeg-v4b` and `plugin-alliance-schoeps-double-ms` (Andrew doesn't own these)

## File Structure

Everything lives in a single HTML file: `plugin-library.html`

The plugin data is embedded as a JavaScript object called `PLUGIN_DATA` near the top of the `<script>` tag. When you make updates, you're editing this object and outputting a new HTML file.

## Data Schema

Each plugin object:

```json
{
  "id": "vendor-plugin-name",
  "name": "Display Name",
  "vendor": "Vendor Name",
  "type": "effect|instrument|MIDI-controlled effect",
  "category": "compressor|eq|reverb|etc",
  "compatible": true,
  "manualRead": false,
  "manualUrl": "https://...",
  "productUrl": "https://...",
  "description": "2-3 sentence summary of what this plugin is and its character",
  "useCases": [
    "Common use case 1",
    "Common use case 2"
  ],
  "thingsToTry": [
    "Specific suggestion for Andrew's rock/alternative production style",
    "Another concrete experiment"
  ],
  "variantGroup": "1176",  // Optional - groups related plugins
  "notes": null
}
```

## Research Workflow

### For each plugin:

1. **Find the manual** - Andrew provided a spreadsheet with many manual URLs. Use web_search if needed.
2. **Fetch and read the manual/product page** - Use web_fetch to get the content
3. **Extract**:
   - A concise description (what it is, what makes it special)
   - 3-5 common use cases
   - 2-4 "things to try" tailored to rock/alternative production
   - Product page URL
   - Manual URL (PDF or web)

### Manual URLs Andrew Provided

**Universal Audio** (from his spreadsheet):
- PolyMAX Synth: https://help.uaudio.com/hc/en-us/articles/10207494851220-PolyMAX-Synth-Manual
- Century Tube Channel Strip: https://help.uaudio.com/hc/en-us/articles/11912560531860-Century-Tube-Channel-Strip-Manual
- Oxide Tape Recorder: https://help.uaudio.com/hc/en-us/articles/11914703390740-Oxide-Tape-Recorder-Manual
- Waterfall Rotary Speaker: https://help.uaudio.com/hc/en-us/articles/12735041523988-Waterfall-Rotary-Speaker-Manual
- Electra 88 Vintage Keys: https://help.uaudio.com/hc/en-us/articles/15388071477652-Electra-88-Vintage-Keys-Manual
- Verve Analog Machines Essentials: https://help.uaudio.com/hc/en-us/articles/25190283956500-Verve-Analog-Machines-Manual
- Moog Minimoog: https://help.uaudio.com/hc/en-us/articles/360041479272-Moog-Minimoog-Manual
- Ravel Grand Piano: https://help.uaudio.com/hc/en-us/articles/360041911111-Ravel-Grand-Piano-Manual
- 1176 Classic Limiter Collection: https://help.uaudio.com/hc/en-us/articles/4419447352980-UA-1176-Classic-Limiter-Collection-Manual
- Teletronix LA-2A Leveler Collection: https://help.uaudio.com/hc/en-us/articles/4419496124180-Teletronix-LA-2A-Leveler-Collection-Manual
- Pure Plate Reverb: https://help.uaudio.com/hc/en-us/articles/4419497304340-Pure-Plate-Reverb-Manual
- Galaxy Tape Echo: https://help.uaudio.com/hc/en-us/articles/4419513003668-Galaxy-Tape-Echo-Manual
- Opal Morphing Synth: https://help.uaudio.com/hc/en-us/articles/4421163605908-Opal-Morphing-Synthesizer-Manual
- Waterfall B3 Organ: https://help.uaudio.com/hc/en-us/articles/4421163858836-Waterfall-B3-Organ-Manual
- Pultec Passive EQ Collection: https://help.uaudio.com/hc/en-us/articles/7183176266260-Pultec-Passive-EQ-Collection-Manual
- Studio D Chorus: https://help.uaudio.com/hc/en-us/articles/7360582279828-Studio-D-Chorus-Manual
- Brigade Chorus: https://help.uaudio.com/hc/en-us/articles/7360583462036-Brigade-Chorus-Manual
- UAD Manual Index: https://help.uaudio.com/hc/en-us/sections/4573072157460-Native-UAD-Plug-In-Manuals

**Plugin Alliance** (from his spreadsheet):
- Ampeg SVT-VR Classic: https://www.plugin-alliance.com/media/Ampeg_SVT_VR_Classic_Manual.pdf
- bx_cleansweep V2: https://www.plugin-alliance.com/media/bx_cleansweep_V2_Manual.pdf
- bx_rockrack V3 Player: https://www.plugin-alliance.com/media/bx_rockrack_V3_Manual.pdf
- bx_solo: https://www.plugin-alliance.com/media/bx_solo_Manual.pdf
- bx_subfilter: https://www.plugin-alliance.com/media/bx_subfilter_Manual.pdf
- bx_townhouse Buss Compressor: https://www.plugin-alliance.com/media/bx_townhouse_Buss_Compressor_Manual.pdf
- elysia niveau filter: https://www.plugin-alliance.com/media/elysia_niveau_filter_Manual.pdf
- LX480 Essentials: https://www.plugin-alliance.com/media/LX480_Essentials_Manual.pdf
- SPL Free Ranger: https://www.plugin-alliance.com/media/SPL_Free_Ranger_Manual.pdf
- THE OVEN: https://www.plugin-alliance.com/media/THE_OVEN_Manual.pdf

**Softube** (product pages with manual links):
- Drawmer S73: https://www.softube.com/index.php?id=drawmers73
- Marshall Plexi Classic: https://www.softube.com/index.php?id=plexiclassic
- Saturation Knob: https://www.softube.com/index.php?id=satknob
- TSAR-1R Reverb: https://www.softube.com/index.php?id=tsar1r
- Tube Delay: https://www.softube.com/index.php?id=tubedelay

**Tokyo Dawn Labs**:
- TDR Nova: https://docs.tokyodawn.net/nova-manual/

## Variant Groups

Some plugins are variations of the same hardware. They share a `variantGroup` and the UI links them together. When researching these, note the differences between variants:

- **1176 group**: Rev A (aggressive, punchy), AE (anniversary edition), LN Rev E (lower noise, more refined)
- **LA-2A group**: LA-2 (original), Gray (smoother), Silver (brighter)
- **Pultec group**: EQP-1A (lows/highs), MEQ-5 (mids), HLF-3C (filters)

## Style Notes

Andrew's context:
- Making a 6-track solo rock EP
- Influences: Peter Gabriel, Nine Inch Nails, Queens of the Stone Age
- Did all writing, performing, recording himself in Logic Pro
- Working with a mix engineer (ixi) in Austin

So "things to try" suggestions should lean toward:
- Rock/alternative production techniques
- Solo artist workflows
- Creative sound design
- Practical mixing moves

## Updating the File

1. Parse the current `PLUGIN_DATA` object from the HTML
2. Update the relevant plugin entries with researched data
3. Output a new complete HTML file with the updated `PLUGIN_DATA`

The entire HTML file needs to be output each time since the data is embedded. The file is ~400 lines of HTML/JS plus the data blob.

## Pending Tasks

1. **Remove two plugins**: `plugin-alliance-ampeg-v4b` and `plugin-alliance-schoeps-double-ms`
2. **UI tweaks requested**:
   - Make card corners more square (less rounded)
   - Replace native `<select>` dropdowns with custom CSS dropdown menus
3. **Research all plugins** - Start with Universal Audio (24 plugins, largest batch)

## Tips

- Batch by vendor - they often share documentation patterns
- UA's help site is comprehensive - the manual index has most links
- Plugin Alliance PDFs are usually straightforward
- For "things to try", be specific: "Use on drum bus with 4:1 ratio, slow attack" not "Try it on drums"
- Keep descriptions concise - this is a reference tool, not documentation
