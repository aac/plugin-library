# Plugin Library Research & Maintenance Workflow

**Last Updated**: December 19, 2024  
**For**: Andrew's Logic Pro plugin library tool  
**Status**: 23/72 plugins researched

---

## 🚨 CRITICAL PROBLEMS WITH CURRENT APPROACH

### Problem 1: CSS Gets Lost
The HTML has custom CSS modifications that keep getting lost when data is updated:
- Custom `.custom-select` dropdown styling with animations
- `border-radius: 4px` on all rounded elements (not Tailwind's rounded-lg)
- Custom dropdown JavaScript with click-outside behavior

**Why it happens**: When updating PLUGIN_DATA, the entire HTML sometimes gets regenerated and CSS is lost.

### Problem 2: Checkbox State Gets Lost
- User checks "I've read the manual" → stored in memory
- User clicks "Save Changes" → downloads `plugins.json`
- When HTML is updated with new research → **all checkbox state is reset**
- User must manually merge saved JSON back into new HTML

### Problem 3: Error-Prone Manual Process
Repeatedly making the same mistakes:
- Loading wrong file (uploads vs outputs)
- Only replacing plugins array instead of full object
- Not verifying updates actually worked
- Accidentally overwriting CSS changes

---

## ✅ RECOMMENDED SOLUTION: Split Workflow

### **Claude.ai (Web)**: Research Only
- Does web searches
- Reads manuals/product pages
- Formats research as JSON
- **Does NOT touch the HTML file**

### **Claude Code (Laptop)**: Data Integration
- Merges new research into existing HTML
- Preserves CSS changes (edits in place)
- Preserves checkbox state
- Handles all file manipulation safely

---

## Research Workflow for Claude.ai

### Step 1: Research Plugins

Use web_search and web_fetch to gather:

**Required Info:**
- Manual URL (if available)
- Product page URL
- Description (2-3 sentences)
  - What it emulates/models
  - Key sonic character
  - Unique features
- Use Cases (4 items)
  - Professional context
  - Genre-agnostic where possible
  - When/where to use it
- Things to Try (4 items)
  - **Specific, actionable techniques**
  - Include settings/modes
  - Tailored to rock/alternative production
  - Reference Andrew's influences (NIN, Peter Gabriel, QOTSA)

**Research Quality Standards:**

**Description Example:**
```
Emulation of BOSS CE-1 (1976) - world's first chorus pedal, originally 
built into Jazz Chorus amp. Uses bucket-brigade delay circuit for warm, 
organic modulation. Dual mode: Classic (gentle) and Dual (intense stereo).
```

**Use Cases Example:**
```
- Classic 80s-style chorus on clean guitars and synths
- Subtle widening of vocals without obvious modulation
- Adding movement to static synth pads
- Creating dimension on floating ambient guitars
```

**Things to Try Example:**
```
- Dual mode with slower speed on power chords for new wave thickness
- Classic mode extremely subtle on lead vocals (NIN-style)
- Use on room mic bus for dimension without reverb
- Stack with tape delay for Peter Gabriel atmospheric guitar tones
```

### Step 2: Format as JSON

Output research in this exact format:

```json
{
  "batch_name": "Universal Audio Compressors",
  "date": "2024-12-19",
  "plugins": [
    {
      "id": "uad-1176-rev-a",
      "manualUrl": "https://help.uaudio.com/...",
      "productUrl": "https://www.uaudio.com/...",
      "description": "Original 'Bluestripe' 1176 (1967) with aggressive, colorful compression. Features 20μs attack time and Class-A output stage. Most aggressive variant with maximum attitude and harmonic coloration.",
      "useCases": [
        "Aggressive vocal compression for rock and alternative",
        "Punchy drum room compression",
        "Parallel compression for adding excitement",
        "Slamming drum overheads"
      ],
      "thingsToTry": [
        "All-buttons-in mode for explosive parallel drum compression",
        "Fast attack (7) + slow release (1) for aggressive vocal attitude",
        "Use on room mics then blend for huge drum sound",
        "Try on bass guitar DI for aggressive pick attack enhancement"
      ]
    }
  ]
}
```

### Step 3: Provide to User

Format as a code block Andrew can copy and give to Claude Code:

````markdown
## Research Complete: [Plugin Names]

**Plugins researched**: 5  
**Vendor**: Universal Audio  
**Category**: Compressors

### For Claude Code:

```json
{
  "batch_name": "...",
  "plugins": [...]
}
```

**Claude Code command:**
```
Merge these plugin updates into plugin-library.html, preserving all CSS and checkbox state.
```
````

---

## Claude Code Integration Workflow

This section is for Claude Code to follow:

### Step 1: Load Existing Data

```python
import json
import re

# Load current HTML
with open('plugin-library.html', 'r') as f:
    html = f.read()

# Extract current PLUGIN_DATA
match = re.search(r'const PLUGIN_DATA = (\{.+?\});', html, re.DOTALL)
current_data = json.loads(match.group(1))

# Keep track of checkbox state
checkbox_state = {
    p['id']: p.get('manualRead', False) 
    for p in current_data['plugins']
}
```

### Step 2: Merge New Research

```python
# Load new research from user
new_research = json.loads(user_provided_json)

# Create lookup for new data
new_data_by_id = {p['id']: p for p in new_research['plugins']}

# Merge into existing
for plugin in current_data['plugins']:
    plugin_id = plugin['id']
    
    # Preserve checkbox state
    plugin['manualRead'] = checkbox_state.get(plugin_id, False)
    
    # Merge new research if available
    if plugin_id in new_data_by_id:
        new = new_data_by_id[plugin_id]
        plugin['manualUrl'] = new.get('manualUrl', plugin.get('manualUrl'))
        plugin['productUrl'] = new.get('productUrl', plugin.get('productUrl'))
        plugin['description'] = new['description']
        plugin['useCases'] = new['useCases']
        plugin['thingsToTry'] = new['thingsToTry']

# Update lastUpdated
current_data['lastUpdated'] = new_research['date']
```

### Step 3: Update HTML (Preserving Everything)

```python
# Convert to compact JSON
data_json = json.dumps(current_data, separators=(',', ':'))

# Replace ONLY the PLUGIN_DATA object
pattern = r'const PLUGIN_DATA = \{.+?\};'
replacement = f'const PLUGIN_DATA = {data_json};'
html = re.sub(pattern, replacement, html, flags=re.DOTALL)

# Save (overwrites in place - preserves CSS!)
with open('plugin-library.html', 'w') as f:
    f.write(html)
```

### Step 4: Verify

```python
# Verify research was added
with open('plugin-library.html', 'r') as f:
    content = f.read()
    
    # Check for a known phrase from new research
    if 'aggressive, colorful compression' in content:
        print("✓ Research successfully merged")
    
    # Check CSS is still there
    if '.custom-select {' in content:
        print("✓ CSS preserved")
    else:
        print("✗ WARNING: CSS was lost!")
    
    # Count researched plugins
    match = re.search(r'const PLUGIN_DATA = (\{.+?\});', content, re.DOTALL)
    data = json.loads(match.group(1))
    researched = sum(1 for p in data['plugins'] if p.get('description'))
    print(f"✓ {researched}/72 plugins researched")
```

---

## Research Progress Tracking

### Current Status
- **Total plugins**: 72
- **Researched**: 23 (31.9%)
- **Remaining**: 49

### By Vendor
| Vendor | Total | Researched | Remaining |
|--------|-------|------------|-----------|
| Universal Audio | 26 | 23 | 3 |
| Plugin Alliance | 13 | 0 | 13 |
| Softube | 5 | 0 | 5 |
| IK Multimedia | 8 | 0 | 8 |
| Others | 20 | 0 | 20 |

### Completed Plugins

**Universal Audio (23)**:
- 1176 Rev A, 1176 AE, 1176 LN Rev E
- LA-2, LA-2A Gray, LA-2A Silver
- API 2500
- Pultec EQP-1A, MEQ-5, HLF-3C
- Helios Type 69
- Galaxy Tape Echo, Pure Plate Reverb, Oxide Tape Recorder
- Brigade Chorus, Studio D Chorus
- Verve Analog Machines
- PolyMAX, Moog Minimoog, Opal Morphing Synth
- Waterfall B3 Organ, Ravel Grand Piano
- Century Tube Channel Strip

### Next Batches
1. Universal Audio (remaining 3)
2. Plugin Alliance (13 plugins)
3. Softube (5 plugins)
4. IK Multimedia (8 plugins)

---

## File Structure Reference

```
Local Files (Andrew's laptop):
├── plugin-library.html          # WORKING FILE (has CSS changes)
└── plugins.json                 # Exported checkbox state (if saved)

Repository:
├── /mnt/user-data/uploads/
│   └── plugin-library.html      # ORIGINAL (outdated, no CSS changes)
└── /mnt/user-data/outputs/
    ├── plugin-library.html      # CURRENT (but may be outdated)
    ├── RESEARCH_WORKFLOW.md     # This document
    └── MAINTENANCE.md           # Original maintenance guide
```

**Important**: The canonical version is Andrew's local `plugin-library.html`. The version in outputs may be behind.

---

## Common Pitfalls to Avoid

### For Claude.ai:
- ❌ Don't try to update the HTML file  
- ❌ Don't worry about CSS or checkbox state  
- ✅ Just research and format JSON  

### For Claude Code:
- ❌ Don't regenerate the entire HTML  
- ❌ Don't forget to preserve checkbox state  
- ❌ Don't use overly strict regex patterns  
- ✅ Always verify CSS is still present after updates  

---

## Example: Complete Research Session

### Claude.ai Output:

````markdown
## Research Complete: Universal Audio Effects Bundle

**Plugins researched**: 3  
**Date**: 2024-12-19

### For Claude Code:

```json
{
  "batch_name": "UA Effects - Remaining",
  "date": "2024-12-19",
  "plugins": [
    {
      "id": "uad-electra-88",
      "manualUrl": "https://help.uaudio.com/hc/en-us/articles/electra-88",
      "productUrl": "https://www.uaudio.com/uad-plugins/electra-88.html",
      "description": "Collection of 6 vintage electric pianos from 1960s-70s including Wurlitzer 200A, Rhodes Mk I, Hohner Clavinet D6. Each instrument meticulously sampled with mechanical noises and authentic amp modeling.",
      "useCases": [
        "Classic rock and soul electric piano tones",
        "Funky clavinet parts for rhythm guitar alternatives",
        "Warm Rhodes for ballads and jazz fusion",
        "Bright Wurlitzer for indie rock and vintage pop"
      ],
      "thingsToTry": [
        "Wurlitzer through tremolo and spring reverb for dreamy indie textures",
        "Clavinet with envelope filter for Parliament-style funk",
        "Rhodes with chorus and tape delay for ambient atmospheric beds",
        "Stack multiple pianos and detune slightly for huge retro sound"
      ]
    }
  ]
}
```

**Claude Code command:**
```
Merge these plugin updates into plugin-library.html, preserving all CSS and checkbox state.
```
````

---

## Future Improvements

### Short Term:
- [ ] Split workflow implemented and tested
- [ ] Claude Code script verified
- [ ] Research remaining 49 plugins

### Long Term:
- [ ] Add localStorage for checkbox persistence
- [ ] Import/export full state as JSON
- [ ] Backup system before each update
- [ ] Version control integration

---

## Quick Reference

### Research Checklist (per plugin):
- [ ] Find manual URL
- [ ] Find product page URL  
- [ ] Read manual for unique features
- [ ] Identify sonic character
- [ ] Note professional use cases
- [ ] Create 4 actionable "things to try"
- [ ] Format as JSON
- [ ] Verify JSON syntax

### Quality Standards:
- **Descriptions**: Factual, specific, 2-3 sentences
- **Use Cases**: Professional context, not beginner tips
- **Things to Try**: Specific settings, Andrew's genres/influences
- **Accuracy**: Verify info from official sources

---

**End of Document**
