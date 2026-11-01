# Desktop wrap (Windows / macOS / Linux)

The game is a single offline HTML file, so it wraps easily. Nothing here is published for you.

## Option A – Electron (included)
1. `mkdir desktop/electron/app` and copy `index.html`, `manifest.json`, `sw.js`, `icons/` into it.
2. `cd desktop/electron && npm install && npm start` to test; `npm run dist` builds installers.
3. F11 toggles fullscreen. Saves live in the app's own localStorage (per-user profile folder).

## Option B – Tauri (smaller, ~10 MB)
`npm create tauri-app`, set `frontendDist` to the folder holding `index.html`, `npm run tauri build`.

## Keyboard / mouse
The game auto-detects mouse + keyboard (and switches back to touch if you tap).
WASD/arrows move, Space or left-click mine/attack (hold), right-click place/block, 1–9 hotbar,
mouse wheel zoom, Shift+wheel switch slot, E bag, M map, F1 rebind keys, Esc menu, F (default) = block, F11 fullscreen.
Rebinding is stored in the profile (`pc` key in meta) and never touches save slots.

## Notes
- Service worker only registers over http(s); inside Electron's file:// it is skipped, which is fine.
- Steam/itch.io: zip the Electron build; no payments or accounts are implemented in the game
  (the gem shop is earned-gems only; real IAP adapter is a "coming soon" stub).
