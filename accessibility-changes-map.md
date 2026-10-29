# Mapping Segregation in Iowa City: Accessibility and Library Updates

File: `segregation-map/index.html` (replaces `index_1.html`). Targets WCAG 2.1 AA. Not tested in a browser or screen reader.

## Library updates

| Item | Before | After | Notes |
|---|---|---|---|
| Leaflet | Bundled inside mapbox.js 3.2.1 (Leaflet 1.0.x, 2017) | **Leaflet 1.9.4** from unpkg, with SRI hashes | Page only used standard Leaflet (`L.map`, `L.tileLayer`, `L.geoJson`), so mapbox.js was not needed |
| Mapbox Assembly CSS/JS | Loaded | Removed | No Assembly classes were used |
| d3 v4 | Loaded for `d3.queue` and `d3.json` | Removed; replaced with `fetch` + `Promise.all`, plus an error message if data fails to load | d3.queue no longer exists in later d3 versions |
| jQuery | 3.1.1 (known vulnerabilities) | **3.7.1** | Still loaded because the filter-button click handlers use it |
| Font Awesome | 4.7, 5.10.2 and v4 shims (three stylesheets) | **5.15.4**, one stylesheet | The one icon used (`fas fa-hourglass-half`) is FA5 |
| Plugins (`easy-button.js`, `leaflet-tag-filter-button.js` and their CSS) | Local copies | **Unchanged: I could not download or see them** | See below |

**Plugins:** I had no network access and wasn't given the files, so I couldn't update them. Replace them yourself:
- Leaflet-EasyButton: latest release is 2.4.0 (`npm i leaflet-easybutton`).
- leaflet-tag-filter-button: check npm or GitHub for the newest version.

After replacing them, test the decade filter. Both plugins are old and I can't confirm they work with Leaflet 1.9.4. The page itself is written to work with them. I also created a Data table button without the EasyButton plugin so it keeps working if the plugins break.

## Accessibility changes

| Element | What was wrong | What changed | WCAG |
|---|---|---|---|
| `<html>` | No `lang` | `lang="en"` | 3.1.1 |
| Viewport meta | `maximum-scale=1, user-scalable=no` blocked pinch-zoom | `width=device-width, initial-scale=1` | 1.4.4 |
| Page structure | No heading, no landmark | Hidden `<h1>`, map in `<main>` | 1.3.1, 2.4.1 |
| Skip link | None | "Skip to data table alternative" | 2.4.1 |
| **Map data** | Parcel details only reachable by mouse click on small polygons | **New "Data table" button** opens a dialog with both datasets as tables (address, subdivision, year, decade), with a decade filter. Dialog has focus trap, Escape, restores focus | 1.1.1, 2.1.1 |
| Map container | Unlabeled | `role="group"` and a label describing keyboard controls | 4.1.2 |
| Decade filter button | Icon only, no accessible name | `aria-label` and `title`; icon marked `aria-hidden` | 4.1.2, 2.4.6 |
| Legend | Gray (#555) text at 12px on 70% transparent white; swatches color-only | Dark text, 14px, 95% opaque, swatch outline, `role="group"`; titles are real `<h2>`s | 1.4.3, 1.4.11 |
| Layer control, zoom buttons | 70% transparent backgrounds over the map | 95% opaque | 1.4.3 |
| Focus visibility | None defined | Blue focus ring | 2.4.7 |
| Animations | Ignored motion settings | `prefers-reduced-motion` disables Leaflet zoom/fade animation and CSS transitions | 2.3.3 |
| Popup content | Data inserted into HTML unescaped | Escaped (also a security fix) | n/a |
| Bugs | `resLabels` was an undeclared global; `type="image/jpg"` is not a MIME type | Removed; now `image/jpeg` | n/a |

## Not fixed: needs a decision

1. **Parcel polygons themselves are not keyboard-focusable.** There may be hundreds or thousands, which would create an unusable tab sequence. The data table is the keyboard alternative.
2. **Color-only encoding on the map** (black/brown, red/orange at half opacity). The legend and table carry the meaning in text. Hatching patterns would fix the map itself.
3. **Filter plugin menu keyboard support is unknown.** I couldn't read the plugin. If its tag list is not operable by keyboard, the table's decade filter is the alternative.
4. **Map dragging is disabled after clicking a filter button** until the next map click. I kept this original behavior, but it can confuse users, so consider removing it.
5. **Map imagery** (Carto tiles) has no text alternative beyond the table.
6. **`css/` and `js/` plugin files, `images/icon.jpg` and `data/*.geojson`** were not provided and not reviewed.
7. **Property names** (`B_occ`, `REST`, `Decade`, etc.) are assumed from your code. Check the table output against the data.
