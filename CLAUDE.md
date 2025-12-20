# CLAUDE.md

Instructions for Claude Code when working on this project.

## Project Overview

A single-file HTML application for browsing Andrew's Logic Pro plugin collection. The tool helps him find plugins, learn about ones he hasn't explored, and track which manuals he's read.

## Architecture

**Single-file design:** Everything lives in `plugin-library.html`:
- HTML structure
- CSS styles (Tailwind CDN + custom styles)
- JavaScript logic
- Plugin data (embedded as `PLUGIN_DATA` object)

**Data location:** `PLUGIN_DATA` is a JavaScript object near line 285 within the `<script>` tag. It contains:
- `plugins[]` - Array of plugin objects
- `categories{}` - Category ID to display name mapping
- `lastUpdated` - Timestamp

**State management:** Vanilla JavaScript:
- `pluginData` - Working copy of PLUGIN_DATA (deep cloned on load)
- `selectedPlugin` - Currently viewed plugin in detail panel
- `hasChanges` - Tracks unsaved modifications

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
  "manualUrl": "https://...",           // PDF or web manual
  "productUrl": "https://...",          // product page
  "description": "2-3 sentence summary",
  "useCases": ["use case 1", ...],      // 3-5 common applications
  "thingsToTry": ["suggestion 1", ...], // 2-4 specific experiments
  "variantGroup": "1176",               // optional - links related plugins
  "notes": null                         // additional info
}
```

## Adding Plugin Data

When you receive plugin JSON from Claude Web, upsert it into `PLUGIN_DATA.plugins`:

- **If plugin exists** (matching `id`): Update fields, but preserve `manualRead`
- **If plugin is new**: Add the complete object to the array

The research JSON includes all fields:
```json
{
  "plugins": [
    {
      "id": "vendor-plugin-name",
      "name": "Plugin Name",
      "vendor": "Vendor Name",
      "type": "effect",
      "category": "compressor",
      "compatible": true,
      "manualUrl": "...",
      "productUrl": "...",
      "description": "...",
      "useCases": [...],
      "thingsToTry": [...],
      "variantGroup": null,
      "notes": null
    }
  ]
}
```

For new plugins, set `manualRead: false`. If it's a new category, add it to the `categories` object.

## UI Structure

**Sidebar** (left):
- Search input
- Custom CSS dropdown filters (vendor, category, type)
- Checkbox filters (compatibility, manual read, researched)
- Save button

**Main grid** (center):
- Card-based plugin display
- Color-coded category tags
- Status indicators (compatible, manual read, researched)

**Detail panel** (right, toggleable):
- Full plugin metadata
- Variant navigation buttons
- Manual read checkbox
- External links

## Key Functions

| Function | Purpose |
|----------|---------|
| `loadPlugins()` | Initialize on page load |
| `getFilteredPlugins()` | Apply all active filters |
| `renderPlugins()` | Update card grid |
| `showDetail(plugin)` | Display detail panel |
| `saveChanges()` | Download JSON of modified data |
| `createCustomSelect()` | Initialize dropdown filters |

## Custom Dropdowns

The app uses custom CSS-styled dropdowns (not native `<select>` elements):
- Styled with `.custom-select-*` classes
- State managed in `customSelects` object
- Click-outside-to-close behavior

## Development Notes

- No build process - just open HTML in browser
- Uses Tailwind CDN (no local CSS compilation)
- File protocol compatible (no server required)
- Changes persist only via JSON download

## Related Files

- `RESEARCH_GUIDE.md` - Instructions for Claude Web when researching plugins
- `research-queue.json` - Plugins that still need research (with known URLs)
