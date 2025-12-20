# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A single-file HTML application for browsing Andrew's Logic Pro plugin collection (70 plugins, after removing 2 unowned plugins). The tool helps him quickly find plugins, learn about ones he hasn't explored, and track which manuals he's read.

**Current State:**
- Interface complete with filtering (custom CSS dropdowns), search, detail panels, and variant grouping
- Research status: **23/70 plugins researched** (32%) with complete descriptions, use cases, and "things to try"
- All researched plugins are Universal Audio plugins
- Remaining: 47 plugins (Plugin Alliance, Softube, IK Multimedia, and others)

## Architecture

**Single-file design:** Everything lives in `plugin-library.html` - HTML structure, CSS styles (using Tailwind CDN), JavaScript logic, and plugin data are all embedded in one file.

**Data structure:** Plugin metadata lives in a JavaScript object called `PLUGIN_DATA` near line 193 within the `<script>` tag. This object contains:
- `plugins[]`: Array of plugin objects with metadata
- `categories{}`: Category ID to display name mapping
- `lastUpdated`: Timestamp

**State management:** Simple in-memory state using vanilla JavaScript:
- `pluginData`: Working copy of PLUGIN_DATA (deep cloned)
- `selectedPlugin`: Currently viewed plugin in detail panel
- `hasChanges`: Tracks unsaved modifications (manual read status)

**UI sections:**
- Sidebar (lines 117-200): Filters (custom dropdowns) and save button
- Main grid (lines 205-213): Card-based plugin display
- Detail panel (lines 216-279): Expandable right-side plugin details

**Custom Dropdowns:** The app uses custom CSS-styled dropdowns (not native `<select>` elements) with:
- Square corners (border-radius: 4px)
- Custom animations and hover states
- Click-outside-to-close behavior
- State managed in `customSelects` object

## Plugin Data Schema

Each plugin object in `PLUGIN_DATA.plugins[]`:

```javascript
{
  "id": "vendor-plugin-name",           // kebab-case unique identifier
  "name": "Display Name",               // human-readable name
  "vendor": "Vendor Name",              // manufacturer
  "type": "effect|instrument|MIDI-controlled effect",
  "category": "compressor|eq|reverb|etc",
  "compatible": true,                   // Apple Silicon compatibility
  "manualRead": false,                  // user has read manual
  "manualUrl": "https://...",          // PDF or web manual
  "productUrl": "https://...",         // product page
  "description": "2-3 sentence summary",
  "useCases": ["use case 1", ...],     // 3-5 common applications
  "thingsToTry": ["suggestion 1", ...], // 2-4 specific experiments
  "variantGroup": "1176",              // optional - links related plugins
  "notes": null                        // additional info
}
```

## Updating Plugin Data

**Process:**
1. Read the current HTML file
2. Parse the `PLUGIN_DATA` object (it's valid JSON assigned to a const)
3. Modify the relevant plugin entries
4. Write the complete HTML file back with updated `PLUGIN_DATA`

**Important:** The entire file must be rewritten since data is embedded. The file is approximately 400-500 lines total.

## Research Workflow

When researching plugins:

1. Find manual URLs (see MAINTENANCE.md for provided links by vendor)
2. Use web fetching to read manuals/product pages
3. Extract concise descriptions, use cases, and "things to try"
4. Tailor "things to try" to Andrew's context:
   - Rock/alternative production (Peter Gabriel, NIN, QOTSA influences)
   - Solo artist workflow (writing, performing, recording alone)
   - 6-track solo rock EP project
   - Practical mixing techniques and creative sound design

**Style guidelines:**
- Descriptions: 2-3 sentences focusing on character and what makes it special
- Use cases: 3-5 common applications, be specific
- Things to try: 2-4 concrete experiments (e.g., "Use on drum bus with 4:1 ratio, slow attack" not "Try it on drums")
- Keep it reference-focused, not documentation-replacement

## Variant Groups

Plugins sharing a `variantGroup` are variations of the same hardware. The UI links them in the detail panel. When researching, note differences:

- **1176**: Rev A (aggressive/punchy), AE (anniversary), LN Rev E (lower noise/refined)
- **LA-2A**: LA-2 (original), Gray (smoother), Silver (brighter)
- **Pultec**: EQP-1A (lows/highs), MEQ-5 (mids), HLF-3C (filters)

## Manual URL Sources

MAINTENANCE.md contains manual URLs organized by vendor:
- Universal Audio: Help site with comprehensive manual index
- Plugin Alliance: Direct PDF links
- Softube: Product pages with manual links
- Tokyo Dawn Labs: Online documentation

## UI Implementation Details

**Filtering system (lines 255-274):**
- Text search across name and vendor
- Dropdown filters for vendor, category, type
- Checkbox filters for compatibility, manual read status, research status
- All filters combine with AND logic

**Card rendering (lines 276-324):**
- Color-coded category tags (categoryColors object at line 200)
- Visual indicators for compatibility, manual read, and research status
- Selected state with blue ring

**Detail panel (lines 326-464):**
- Shows full plugin metadata
- Variant navigation buttons
- Manual read checkbox (persisted to local state, saved via download)
- External links to product pages and manuals

**Save mechanism (lines 466-476):**
- Downloads updated plugin data as JSON
- No server-side persistence (client-only application)
- Warns before navigation if unsaved changes exist

## Key Functions

- `loadPlugins()`: Initialize on page load
- `getFilteredPlugins()`: Apply all active filters
- `renderPlugins()`: Update card grid based on filters
- `showDetail(plugin)`: Display detail panel for selected plugin
- `saveChanges()`: Download JSON of modified plugin data

## Development Notes

- No build process or dependencies - just open the HTML file in a browser
- Uses Tailwind CDN (no local CSS compilation)
- File protocol compatible (no server required)
- All state is client-side, changes only persist via JSON download

## Workflow: Research & Data Integration

**IMPORTANT:** There are two separate workflows documented in `RESEARCH_WORKFLOW.md`:

1. **Claude.ai (Web)**: Research only
   - Searches for manuals and product pages
   - Extracts descriptions, use cases, and "things to try"
   - Outputs JSON formatted plugin data
   - Does NOT touch the HTML file

2. **Claude Code (Local)**: Data integration
   - Merges new research JSON into existing HTML
   - Preserves CSS changes and checkbox state
   - Edits `PLUGIN_DATA` in place using regex replacement
   - NEVER regenerates the entire HTML file

### Critical Rules for Updating Plugin Data

**When merging new plugin research:**

1. Read the current HTML file
2. Extract `PLUGIN_DATA` using regex: `const PLUGIN_DATA = (\{.+?\});`
3. Parse it as JSON
4. Merge new plugin data (descriptions, URLs, use cases, things to try)
5. Preserve `manualRead` checkbox state for all plugins
6. Convert updated data to compact JSON
7. Replace ONLY the `PLUGIN_DATA` object using regex substitution
8. Write the file back (preserves all CSS and HTML)

**What NOT to do:**
- Don't regenerate the entire HTML
- Don't lose the custom CSS dropdown styling
- Don't reset `manualRead` checkbox states
- Don't use native `<select>` elements (use custom dropdowns)

See `RESEARCH_WORKFLOW.md` for complete merge scripts and quality standards.
