# CLAUDE.md

## Project Overview

Baby Kick Tracker — a single-file vanilla JavaScript PWA for tracking baby kicks during pregnancy. All data is stored in browser localStorage with no backend. Deployed as a static site on Vercel at https://babykicks.vercel.app.

## Architecture

The entire application lives in **one HTML file** (`index.html`, ~618 lines) containing embedded CSS and JavaScript. There are no frameworks, no build tools, no npm packages, and no external dependencies.

```
index.html        # Full application (CSS + HTML + JS)
sw.js             # Service worker (network-first caching)
manifest.json     # PWA manifest
vercel.json       # Vercel static deployment config
README.md         # Project documentation
```

### index.html structure

- Lines 11–281: CSS (embedded `<style>`)
- Lines 284–328: HTML markup
- Lines 330–616: JavaScript (`<script>`)

## Tech Stack

- Vanilla JavaScript (ES6+), no frameworks
- HTML5 Canvas for scatter plot chart (custom implementation)
- localStorage for persistence (key: `"babykicks"`)
- Service Worker for offline/PWA support
- Vercel for static hosting (no build step)

## Development

**No build process.** Open `index.html` in a browser to run locally. Edit files directly.

There is no package.json, no test framework, and no linter configured.

To deploy: push to main and Vercel auto-deploys, or just serve the files statically.

## Code Conventions

- **Indentation:** 2 spaces
- **Strings:** Single quotes in JS, double quotes in HTML attributes
- **Semicolons:** Yes
- **Naming:** `camelCase` for functions/variables, `SCREAMING_SNAKE_CASE` for constants
- **Data format:** Kicks stored as an array of ISO 8601 timestamp strings in localStorage
- **Rendering:** A single `render()` function re-renders all UI (stats, log, chart) on every state change
- **Date display:** Uses `toLocaleDateString()` / `toLocaleTimeString()` for locale-aware formatting
- **Inline event handlers:** Used in HTML (`onclick=`)

## Key Functions (index.html)

| Function | Purpose |
|----------|---------|
| `getKicks()` / `saveKicks()` | Read/write kicks array from localStorage |
| `render()` | Re-render all UI: stats, kick log, chart |
| `drawChart()` | Custom Canvas scatter plot of kick times |
| `exportCSV()` / `importCSV()` | CSV backup and restore |
| `deleteKick()` | Remove a single kick by index |
| `clearAll()` | Delete all data (with confirmation) |
| `openPastKick()` / `savePastKick()` | Modal for adding historical kicks |

## Service Worker (sw.js)

- Cache name: `babykicks-v1`
- Strategy: Network-first, fallback to cache
- Cached assets: `/`, `/index.html`, `/manifest.json`
- On activate: old cache versions are purged

## Design Principles

- Zero external dependencies — everything uses built-in Web APIs
- Privacy-first — no data leaves the browser
- Single-file simplicity — avoid splitting into multiple files unless truly necessary
- Mobile-first responsive design
- Color scheme: `#b35a7a` (mauve pink) primary, `#fef7f0` (cream) background

## Common Pitfalls

- When adding new cached assets, update the `urlsToCache` array in `sw.js` **and** bump the cache version name
- All kicks are ISO strings; always use `new Date().toISOString()` when recording
- The `render()` function must be called after any data mutation to update the UI
- No router — it's a single-page, single-view app
