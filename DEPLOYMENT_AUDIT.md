# Deployment audit — possiblygta5web

Updated 2026-10-13. The current custom domain is `gta5on.nxtdev.xyz`; it was not changed.

## Applied setup fixes

- Added `index.html` as a copy of the intended browser page, `homepage.html`.
- Copied the available runtime JavaScript into the URL prefix hard-coded in the page, `/b/8b0b5899ed/`: `loader.js`, `game.js`, `io_worker.js`, and `wgpu_worker.js`.
- Added `_headers` for a compatible static host such as Cloudflare Pages. This file is not honoured by GitHub Pages.
- Preserved all original source files, `CNAME`, and the root versions of the scripts.

## Still required before the game can launch

1. **Game engine binary:** `/b/8b0b5899ed/game.wasm` is not included.
2. **Audio module:** `/b/8b0b5899ed/audio-worklet.js` is not included.
3. **Shader files:** the GPU worker requests `/b/8b0b5899ed/shaders/index.json`, per-shader `.wgsl` files, and associated constants. Those runtime assets are not included.
4. **Title artwork:** `homepage.html` references `/b/8b0b5899ed/title/art/*.webp`, which is absent.
5. **Game data:** `data-manifest.json` lists 5,814 game-data files. Their actual contents aren't in this repository; the project README describes a separate data collection of about 19.7 GB.
6. **Data API:** `io_worker.js` makes POST requests to `/data/batch`. That endpoint must read the authorized game-data files and return binary ranges. Static GitHub Pages cannot implement this endpoint.
7. **Browser isolation:** the app requires `Cross-Origin-Opener-Policy: same-origin` and `Cross-Origin-Embedder-Policy: require-corp`. The added `_headers` file is for Cloudflare Pages-style hosting, not GitHub Pages.

## Safe next step

Keep the existing custom domain unchanged while preparing a compatible backend. A Cloudflare Pages deployment can apply `_headers` to static assets, but the `/data/batch` function must add the same headers itself and use an appropriately configured object-storage binding. Do not point the domain at a replacement host until the frontend, data API, shaders, and authorized game data have been validated together.

The existing `serve_local.py` shows the expected isolation headers, range handling, and batch-read protocol for local use.
