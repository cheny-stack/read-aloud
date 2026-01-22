# Repository Guidelines

## Project Structure & Module Organization

This is a browser extension (Chrome/Chromium + Firefox) built with plain HTML/CSS/JS.

- `manifest.json`: extension entrypoint and permissions.
- `background.js`: service worker/event page bootstrap (loads modules via `importScripts`).
- `js/`: primary logic (messaging, content extraction, player/options, TTS engines).
- `css/`, `img/`, `sound/`: UI styles and assets.
- `_locales/`: i18n message catalogs.
- `docs/`: docs and screenshots referenced from `README.md`.
- `page-scripts/`: scripts published for external loading (see `npm run sync-page-scripts`).

## Build, Test, and Development Commands

- `npm ci`: install dependencies from `package-lock.json`.
- `npm run package`: create `build/package.zip` containing the extension bundle (requires a `zip` command on your PATH).
- `npm run sync-page-scripts`: maintainer workflow to sync `page-scripts/` (requires `rsync` + AWS CLI credentials).

Local run is manual:
- Chrome: `chrome://extensions` → enable Developer mode → “Load unpacked” → select repo root.
- Firefox: `about:debugging` → “This Firefox” → “Load Temporary Add-on” → select `manifest.json`.

## Coding Style & Naming Conventions

- Match existing style in `js/`: 2-space indentation, semicolons are generally omitted, and functions/variables use `camelCase`.
- Prefer small, focused modules in `js/` and keep UI-specific code near its page script (e.g., `options.html` + `js/options.js`).
- Keep user-facing strings in `_locales/*/messages.json` rather than hardcoding.

## Testing Guidelines

There is no automated test suite in this repo today. Validate changes by:
- Loading the unpacked extension and exercising the relevant UI/flows.
- Verifying i18n strings render correctly and no console errors appear.
- Checking behavior in at least one Chromium browser; add Firefox checks for APIs that differ.

## Commit & Pull Request Guidelines

- Commits commonly use a short type prefix like `fix:` / `feat:` and an imperative subject (keep it specific).
- PRs should include: what changed + why, steps to reproduce/test, and screenshots for UI changes (options/player/popup).
- If touching permissions, networking, or premium/credential flows, call it out explicitly in the PR description.

