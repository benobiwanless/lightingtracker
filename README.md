# Strikewatch

A clean, ad-free, single-file lightning strike radar — dark map, aging-color strike markers, live ticker log, no trackers, no popups. Built to run entirely as a static site (perfect for GitHub Pages).

## Host it on GitHub Pages

1. Create a new repo on GitHub (or use an existing one).
2. Add `index.html` to the repo root (or to a `/docs` folder — your choice).
3. Push it: `git add index.html && git commit -m "Add strikewatch" && git push`
4. In the repo: **Settings → Pages → Build and deployment → Source: Deploy from a branch**, pick `main` and the `/ (root)` or `/docs` folder.
5. Your site will be live at `https://yourusername.github.io/your-repo-name/` within a minute or two.

No build step, no dependencies to install — it's one HTML file that pulls Leaflet from a CDN.

## Why it doesn't connect to Blitzortung directly, like lightningmaps.org does

lightningmaps.org is Blitzortung.org's own official display site, and it talks to Blitzortung's raw detector network over a private websocket protocol. Blitzortung's data-usage policy explicitly says third-party apps have to run **their own server** to relay that data to their own users, rather than pointing every visitor's browser straight at Blitzortung's servers. GitHub Pages only hosts static files — there's no server to run there — so wiring this site directly into Blitzortung's feed would both violate that policy and be fairly fragile (their internal protocol isn't publicly documented and can change without notice).

So instead, this site ships with two modes:

### 1. Demo feed (default, no setup)
Simulated storm cells that spawn and drift through real-world lightning-dense regions (Lake Maracaibo, the Congo Basin, the Gulf Coast, the Bay of Bengal, the Amazon). It's not live data, but it behaves like real weather and the whole UI — aging strike colors, the ticker, the counters — works exactly as it would with a real feed. Good for a portfolio piece or a placeholder while you decide on a real data source.

### 2. Live API mode (optional)
Wired up for **OpenWeather's Lightning API**, which is a documented, key-based, ToS-friendly product (no scraping involved). Click the settings gear → "Live API" → paste in your own key. Notes:
- Get a key at [openweathermap.org](https://openweathermap.org) — check their current pricing, lightning data isn't necessarily on the free tier.
- The API is capped at a 50 km radius around one point, so this mode watches one location rather than the whole globe.
- The key lives only in your browser's `localStorage` and is sent directly to OpenWeather — since this is a static site with no backend, the key is visible in the browser's network tab. Don't reuse a key you care about protecting; a scoped/free key is fine for a hobby project like this.

### If you want a real *global* live feed later
That requires a small always-on backend you control (a cheap VPS, a Fly.io/Render free instance, even a Raspberry Pi at home) that connects to a licensed data source and re-publishes it to your static site over a simple WebSocket or SSE endpoint — satisfying the "run your own server" requirement. The `fetchLiveStrikes()` function in `index.html` is written so you can swap in whatever endpoint you end up with; it just needs to return `{lat, lon, id, time}` objects.

## Customizing
- Colors, fonts, and the age-gradient are all CSS custom properties at the top of the `<style>` block.
- Map tiles are CARTO's free dark basemap — swap the `tileLayer` URL for any other Leaflet-compatible tile provider.
- `MAX_AGE` (in the script) controls how long a strike stays on screen before it's removed — currently 15 minutes.
