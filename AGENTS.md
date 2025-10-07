# Repository Guidelines

## Project Structure & Module Organization
- `manifest.json` defines permissions, service worker entry (`background.js`), and options page; bump its `version` for every store upload.
- `background.js` wires the context menus and messaging; `content.js` performs DOM harvesting and clipboard actions; shared logic lives in `lib/Readability.js`.
- UI assets (`options.html`, `popup.js`, `icon.png`) sit at the root for easy packaging.
- `_locales/*/messages.json` houses i18n strings; mirror new keys across every locale before shipping.

## Build, Test, and Development Commands
- For rapid iteration, load the repo via `chrome://extensions` → Developer Mode → Load Unpacked and point to this folder.
- Package a release build with `zip -r dist/siyuan-chrome.zip manifest.json background.js content.js popup.js options.html lib _locales icon.png`; upload the zip to the stores.
- After edits, refresh the unpacked extension or run `chrome.runtime.reload()` from the service worker console to pick up changes.

## Coding Style & Naming Conventions
- JavaScript uses four-space indentation, trailing semicolons omitted, and single quotes by default.
- Favor small helper functions (see `siyuanShowTip*`) and camelCase identifiers; keep DOM IDs kebab-cased.
- Comments document non-obvious behaviour, especially locale-dependent or browser API workarounds.
- When adding dependencies, bundle plain JavaScript modules in `lib/` to avoid build tooling.

## Testing Guidelines
- Manual verification is required: reload the extension, clip selections, send full-page readability captures, and confirm tips render in-page.
- Validate i18n by switching Chrome language and checking `_locales` strings, especially new keys.
- Use the browser console to watch for `chrome.runtime.lastError` logs and fix regressions before submitting.

## Commit & Pull Request Guidelines
- Follow the existing convention: emoji-coded scope (`:art:`, `:bug:`) plus a concise summary, and append related GitHub issues (e.g., `Fix clipping jitter #123`).
- Keep commits focused; include locale updates and manifest bumps alongside the feature that needs them.
- Pull requests must describe the user impact, outline manual verification steps, and attach screenshots or screen recordings when UI changes occur.

## Security & Configuration Tips
- Never hardcode SiYuan API tokens; the options page stores them via `chrome.storage.sync`.
- Review requested permissions before release to avoid unexpected prompts, and document any new host access in the PR.
