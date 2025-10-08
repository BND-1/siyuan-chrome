# Repository Guidelines

## Project Structure & Module Organization
- `manifest.json` defines permissions, points to `background.js`, sets the options page, and must be version-bumped for every store upload.
- `background.js` registers context menus and bridges messaging to `content.js`, which handles DOM scraping and clipboard writes; shared parsing lives in `lib/Readability.js`.
- UI assets (`options.html`, `popup.js`, `icon.png`) sit at the repository root for direct packaging.
- Localized strings live under `_locales/<lang>/messages.json`; keep keys mirrored across locales before releases.

## Build, Test, and Development Commands
- Enable Developer Mode in `chrome://extensions` and load this folder unpacked for rapid iteration.
- Run `zip -r dist/siyuan-chrome.zip manifest.json background.js content.js popup.js options.html lib _locales icon.png` to produce a store-ready bundle.
- After edits, refresh the unpacked extension or call `chrome.runtime.reload()` from the service worker console to pull in changes.

## Coding Style & Naming Conventions
- JavaScript uses four-space indentation, single quotes, and no trailing semicolons.
- Prefer small helper functions (e.g., `siyuanShowTip*`) and camelCase identifiers; keep DOM IDs in kebab-case.
- Place any third-party modules in `lib/` to avoid build tooling; comment only for non-obvious browser quirks.

## Testing Guidelines
- Manual verification is required: reload the extension, clip a selection, capture a full page via Readability, and confirm in-page tips render.
- Swap Chrome’s language to validate `_locales` strings and watch the console for `chrome.runtime.lastError`.

## Commit & Pull Request Guidelines
- Follow the emoji prefix convention (e.g., `:art: Polish popup spacing #123`) and bundle locale edits plus manifest bumps with related features.
- Pull requests should outline user impact, document manual verification steps, and include screenshots or recordings for UI updates.

## Security & Configuration Tips
- Never hardcode SiYuan API tokens; store them through `chrome.storage.sync`.
- Review requested permissions and host access in `manifest.json` before release, and document any changes in the PR.
