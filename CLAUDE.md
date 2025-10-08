# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Chrome browser extension for SiYuan (思源笔记) that allows users to clip web content and save it directly to their SiYuan instance. The extension supports both text selection clipping and full-page readability captures.

## Core Architecture

### Extension Structure
- **manifest.json**: Defines permissions, service worker, and content scripts. Uses Manifest V3 with minimum Chrome version 91.
- **background.js**: Service worker that handles context menus, message routing, and SiYuan API communication.
- **content.js**: Content script injected into all pages for DOM manipulation, clipboard operations, and Readability integration.
- **popup.js**: Options page logic for configuration management and template customization.
- **lib/Readability.js**: Mozilla's Readability library for extracting clean content from web pages.
- **options.html**: Extension popup/options interface with i18n support.

### Key Features
- Context menu integration for text selection and image clipping
- Full-page content extraction using Readability.js
- Template-based content formatting with customizable variables
- Multi-language support via Chrome i18n API
- Asset downloading (images) with configurable options
- Experimental features for enhanced content detection

## Development Workflow

### Local Development
1. Load unpacked extension via `chrome://extensions` → Developer Mode → Load Unpacked
2. Point to the repository folder
3. Refresh the extension after code changes or use `chrome.runtime.reload()` from service worker console

### Building for Release
```bash
# Create distribution package
mkdir -p dist
zip -r dist/siyuan-chrome.zip manifest.json background.js content.js popup.js options.html lib _locales icon.png
```

### Version Management
- Bump `version` in manifest.json for every store upload
- Follow semantic versioning (currently at 1.13.7)
- Update all locale files simultaneously when adding new i18n keys

## Code Conventions

### JavaScript Style
- Four-space indentation
- Trailing semicolons omitted
- Single quotes by default
- CamelCase for variables and functions
- Kebab-case for DOM IDs

### File Organization
- Keep all files at root level for easy packaging (except lib/ and _locales/)
- Bundle plain JavaScript modules in `lib/` to avoid build tooling
- Separate concerns: background (menus/messaging), content (DOM/clipboard), popup (options/templates)

### Message Handling
- Use `chrome.runtime.onMessage` with named function dispatch
- Include error handling with `chrome.runtime.lastError`
- Follow pattern: check `request.func` then execute corresponding handler

## Internationalization

### Adding New Strings
1. Add keys to `_locales/en/messages.json` first
2. Copy to all other locale files (zh_CN, zh_TW, ja, etc.)
3. Access via `chrome.i18n.getMessage("key_name")` in JavaScript
4. Use in HTML via `__MSG_key_name__`

### Supported Languages
- English (en) - primary/reference
- Simplified Chinese (zh_CN)
- Traditional Chinese (zh_TW)
- Japanese (ja)
- French (fr)
- German (de)
- Spanish (es)
- Italian (it)
- Russian (ru)
- Polish (pl)
- Hebrew (he)
- Arabic (ar)

## Configuration Management

### Stored Settings
- SiYuan URL and API token (sync storage)
- Default save path and tags
- Asset downloading preferences
- Template customization
- Experimental feature toggles

### Template System
- Default template includes title, URL, excerpt, date/time, and content
- Available variables: `${title}`, `${url}`, `${siteName}`, `${date}`, `${time}`, `${tags}`, `${excerpt}`, `${content}`
- Supports conditional expressions: `${condition ? "true" : "false"}`
- Templates stored in `chrome.storage.sync`

## API Integration

### SiYuan API Endpoints
- `/api/clip/clipper` for clipping operations
- `/api/file/getFile` for asset downloading
- Authentication via API token from SiYuan settings

### Error Handling
- Network connectivity checks
- Token validation
- Kernel availability verification
- User-friendly tip messages via i18n

## Testing Guidelines

### Manual Testing Checklist
- Test text selection clipping on various sites
- Verify full-page readability extraction
- Confirm asset downloading works correctly
- Validate all experimental features
- Test with different Chrome languages
- Verify context menu functionality

### Debugging
- Use browser console for `chrome.runtime.lastError` monitoring
- Check service worker logs for background script issues
- Content script debugging via page console
- Storage inspection via `chrome://extensions/` → Details → Storage

## Security Considerations

- API tokens stored via `chrome.storage.sync` (encrypted by Chrome)
- No hardcoded credentials
- Minimal permissions requested (activeTab, scripting, contextMenus, clipboardWrite, storage)
- Host permissions limited to all URLs for content script injection
- Content Security Policy considerations for inline scripts

## Commit Guidelines

Follow the existing convention:
- Emoji-coded scope: `:art:`, `:bug:`, `:sparkles:`
- Concise summary with GitHub issue references
- Examples: `:art: Improve HTML table clipping #15883`, `:bug: Fix clipping jitter #123`

## Store Release Process

1. Update version in manifest.json
2. Test all features thoroughly
3. Create zip package
4. Upload to Chrome Web Store and Edge Addons
5. Update GitHub release notes