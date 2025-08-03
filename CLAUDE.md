# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Common Development Commands

### Building and Packaging
- `npm run package` - Creates a distributable extension package as `build/package.zip`
- `npm run sync-page-scripts` - Syncs page scripts to AWS S3 and copies messaging.js to page-scripts directory

### Testing
No automated test suite is configured. Testing requires manual verification by loading the extension in Chrome/Firefox developer mode.

## High-Level Architecture

Read Aloud is a cross-browser text-to-speech extension with the following core architecture:

### Extension Structure
- **Manifest V3 Extension**: Uses service worker architecture (`background.js`) with content scripts
- **Multi-TTS Engine Support**: Supports browser native TTS, cloud providers (Amazon Polly, Google Wavenet, IBM Watson, OpenAI, Azure), and Google Translate
- **Content Script Injection**: Dynamically loads specialized content handlers based on website detection

### Key Components

#### Core Files
- `background.js` - Service worker entry point that imports core modules
- `js/defaults.js` - Central configuration, settings management, voice selection logic, and utility functions
- `manifest.json` - Extension configuration with permissions for TTS, storage, and web access

#### Content Script System
- `js/content.js` - Main content script with universal document handling
- `js/content/` - Site-specific content handlers for:
  - Google Docs, Slides, Drive documents
  - Kindle books, PDF viewers
  - Khan Academy, Pearson, Norton educational platforms
  - Archive of Our Own, WebNovel fiction sites
  - ChatGPT and other specialized platforms

#### TTS Engine Architecture
- `js/tts-engines.js` - Engine implementations for all supported TTS providers
- Voice selection priority: offline → native → free cloud → premium cloud
- Automatic language-based voice selection with dialect preferences

#### UI Components
- `popup.html/js` - Extension popup interface
- `options.html/js` - Settings and voice configuration
- `player.html/js` - Audio playback controls
- `pdf-viewer.html` - Integrated PDF reading interface

### Development Patterns

#### Settings Management
All settings use Chrome storage API through centralized functions in `defaults.js`:
- `getSettings(names)` - Retrieve settings
- `updateSettings(items)` - Update settings  
- `observeSetting(name)` - Reactive setting observation using RxJS

#### Message Passing
Uses Chrome extension messaging API with destination-based routing:
- Service worker communication via `bgPageInvoke(method, args)`
- Content script communication via `registerMessageListener()`
- Cross-frame communication for complex documents

#### Website Content Detection
Content handlers are dynamically selected in `content.js` based on:
- Hostname matching (e.g., `docs.google.com`, `read.amazon.com`)
- URL pattern matching (e.g., PDF files, specific paths)
- DOM element detection (e.g., PDF embeds, specific UI elements)

#### Voice and Language Handling
- Automatic language detection from document metadata
- Voice preference system with per-language defaults
- Fallback chain for voice selection when preferred voices unavailable
- Support for premium voice authentication and billing

### Extension Permissions
- `activeTab` - Read/modify current tab content
- `tts` - Native browser text-to-speech
- `storage` - Settings persistence
- `identity` - Authentication for premium voices
- `offscreen` - Audio playback without user interaction
- Host permissions for Google Translate and cloud TTS services

### Internationalization
- Supports 9 languages with message files in `_locales/`
- Uses Chrome i18n API for UI localization
- Automatic UI language detection and text direction support