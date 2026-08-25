# Quicktest

Free, worldwide, browser-only device tests: webcam, microphone, speaker, keyboard, screen, and touchscreen. No backend, no database, no accounts — every test runs on-device using native browser APIs (getUserMedia, Web Audio API, KeyboardEvent, TouchEvent). This is what makes it low-competition to build (nobody needs a server) but genuinely useful worldwide (device testing isn't tied to language, currency, or region).

## File / route structure
```
/                     → index.html            (hub, links to every test)
/webcam-test          → webcam-test/index.html
/microphone-test      → microphone-test/index.html
/speaker-test         → speaker-test/index.html
/keyboard-test        → keyboard-test/index.html
/screen-test          → screen-test/index.html
/touchscreen-test     → touchscreen-test/index.html
/cps-test             → cps-test/index.html
/gamepad-test         → gamepad-test/index.html
/about                → about/index.html
/privacy              → privacy/index.html
/contact              → contact/index.html
/styles.css           → shared stylesheet
/sitemap.xml
/robots.txt
/vercel.json
```
Each folder's `index.html` is served at the clean URL automatically — this is native static folder routing, no framework needed. `vercel.json` sets `cleanUrls: true` so the `.html` extension is never exposed, and adds a `Permissions-Policy` header scoping camera/mic access to same-origin only.

## Data structure
Each tool page defines its own small, local data structure rather than a shared database, since nothing needs to persist:

- **Keyboard test**: `LAYOUT` — an array of rows, each row an array of `[keyCode, label, sizeClass]` tuples, rendered into a visual keyboard and matched against real `KeyboardEvent.code` values.
- **Screen test**: `COLORS` — an array of `{name, hex, text}` swatch objects.
- **CPS test**: timer-driven click counter using `performance.now()` and `requestAnimationFrame`; mode buttons set a `duration` (1/5/10/60s).
- **Gamepad test**: polls `navigator.getGamepads()` every frame; `BUTTON_LABELS` maps the standard Gamepad API button index layout to human-readable names, axes 0–3 drive the two stick canvases.
- **Hub page**: tool tiles are static HTML (kept static, not JS-rendered, so search engines can crawl every link directly — this matters more here than user-experience polish, since organic search is the whole traffic plan).

If you later want analytics on which tests are most used, the cleanest addition is a single `events` table (tool, timestamp, country from IP) in a lightweight store like Vercel KV or Supabase — not required for the site to function.

## Deploying to Vercel
1. Push this folder to a GitHub repo.
2. In Vercel: **New Project → Import** the repo. Framework preset: **Other** (static).
3. Vercel auto-detects `vercel.json`. No build command needed — it's static files.
4. Add your custom domain under **Project → Settings → Domains**.
5. Before going live, do a find-and-replace of `quicktest.example.com` in `sitemap.xml` and `robots.txt` with your real domain.

## Domain name suggestions
Check availability before committing — these are suggestions, not confirmed available:
- `quicktest.io` / `quicktest.app` — clean, short, matches the brand exactly
- `checkmydevice.com` — more descriptive, good secondary/redirect domain
- `devicecheck.io` — backup if quicktest.io is taken

## Ad monetization
- Leaderboard slot (top of every page) + native in-content slot (mid-page) are already placed as `.ad-slot` divs — replace with your AdSense/Ezoic `<ins>` snippets once approved.
- These pages have unusually high engagement time (people actively test for 30–90 seconds rather than bouncing instantly), which tends to help viewability-based ad revenue.
- Apply to Google AdSense once the site has some organic traffic and all policy pages (privacy, about) are live — both are included above.

## Honest expectations on traffic
I can't give you real visit numbers — I don't have analytics access to this or any live site, and any specific number I gave you would be a guess dressed up as data. What I can tell you: "webcam test," "mic test online," and "keyboard tester" are real, recurring, worldwide search terms with moderate-to-low competition compared to saturated niches (finance, health, AI tools) — precedent sites like webcammictest.com and keyboardtester.com rank on long-established backlinks, not superior tooling, so a fast, clean, multi-tool version has a real shot at picking off long-tail traffic. Actual numbers will depend entirely on how well you can rank in search, which takes months and links, not just good code.
