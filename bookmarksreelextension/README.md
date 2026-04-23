# Bookmarks Reel — Chrome Extension

A Manifest V3 Chrome extension that builds a **local screenshot gallery of your bookmarks**. Screenshots are captured in the background, compressed via a bundled WebAssembly filter + JPEG encoding, and stored entirely on your device. Nothing is transmitted to a developer server.

## What it does

- **Scheduled background captures** run while your browser is idle, on a configurable interval, in small batches (default: 3 bookmarks per run, every 60 minutes, once the browser has been idle for 60 seconds).
- **New-bookmark capture** queues an initial screenshot whenever you add a bookmark.
- **Visit capture (opt-in, off by default)** can take a screenshot of your active tab while you browse pages on a bookmark's domain, rate-limited by a separate interval.
- **Gallery popup** shows the captures as a responsive grid with search, sort, and column controls. Hovering a card cycles through every saved screenshot for that bookmark.
- **Storage is bounded.** Oldest screenshots are pruned automatically when you hit your configured cap; an hourly cleanup deletes screenshots past the maximum age.

## Privacy summary

- All screenshots and settings live in `chrome.storage.local`. Nothing is sent to the developer or any third party.
- The only outbound requests are to each bookmarked URL when it is time to screenshot it, and to Google's public favicon service for the little site icons shown in the gallery.
- `<all_urls>` is declared as an **optional** host permission and is only requested the first time you trigger a capture feature (via a user gesture in the popup).
- A login-page heuristic and a user-editable sensitive-domain exclusion list (prefilled with common webmail, cloud, and finance domains) reduce the chance of accidentally screenshotting authenticated content, but cannot eliminate it entirely. The full privacy disclosure lives in [privacy.html](privacy.html) and the Chrome Web Store listing copy is in [docs/chrome-web-store-listing.md](docs/chrome-web-store-listing.md).

## Capture window behaviour

Because Chrome requires any window opened via `chrome.windows.create` to have at least 50 % of its bounds on a visible display, the capture window is positioned in the **bottom-right corner of your primary display**, unfocused so it does not steal focus. You will briefly see it open, load a bookmark, and close again during crawls. One shared capture window is reused across a batch to avoid window flicker.

## Install (developer mode)

1. Clone or download this repository.
2. (Optional) Rebuild the WASM module if you have changed `wasm/compress.wat` — see below. A precompiled `wasm/compress.wasm` is checked in.
3. Open `chrome://extensions`, enable **Developer mode**.
4. Click **Load unpacked** and select the repository folder.
5. The first time you click **Crawl All** or a per-bookmark capture button, Chrome will prompt you to grant host access to all sites. This permission is required for capture and is deliberately requested on demand rather than at install time.

## Settings

All settings live on the Options page (gear icon in the popup header, or `chrome://extensions` → Bookmarks Reel → Details → Extension options).

| Setting | Range | Default |
|---|---|---|
| Auto-crawl enabled | on / off | on |
| Crawl interval | 5–1440 minutes | 60 |
| Idle threshold | 15–600 seconds | 60 |
| Visit capture interval | 0–1440 minutes (0 disables) | 0 |
| Skip login pages | on / off | on |
| Max images per bookmark | 1–50 | 5 |
| Max image age | 1–365 days | 30 |
| Max total storage | 5–500 MB | 50 |
| Excluded capture domains | free-form list (one per line) | 15 common sensitive defaults |

## Permissions

| Permission | Reason |
|---|---|
| `bookmarks` | Read the user's bookmark tree. |
| `tabs`, `windows` | Open a temporary capture window; read active tab URL/title for visit capture; receive load-state events. |
| `idle` | Gate scheduled captures on browser idle. |
| `system.display` | Place the capture window inside a visible display (required after Chrome's bounds-validation changes). |
| `storage`, `unlimitedStorage` | Persist screenshots and settings locally. `storage.local` has a ~10 MB default; `unlimitedStorage` is required because the user-facing cap goes up to 500 MB. |
| `alarms` | Schedule periodic crawl, cleanup, and manual-crawl delays. |
| `scripting` | Inject a read-only function into each candidate page to detect login markers. |
| `<all_urls>` (optional host) | Load bookmarked URLs in the capture window and call `captureVisibleTab` on them. Requested on demand via a user gesture in the popup. |

## Architecture

```
popup.html / popup.js / popup.css        Gallery UI; messages background for data.
options.html / options.js / options.css  Settings, storage meter, clear-all.
privacy.html                              Bundled privacy policy page.
background.js                             Service worker: scheduling, capture pipeline,
                                          storage management, login detection, IPC.
wasm/
  compress.wat                            WASM source (WebAssembly Text format).
  compress.wasm                           Compiled binary (checked in).
  CLAUDE.md                               Notes for future contributors.
icons/
  icon.svg                                Vector source used in in-extension UI.
  icon.png                                Raster source (single size).
docs/
  chrome-web-store-listing.md             Store listing + disclosure copy.
  superpowers/                            Development plans and notes (excluded from ship).
CLAUDE.md                                 Contributor guidance and project conventions.
```

> **Note for Web Store submission.** The `icons/` folder must contain `icon16.png`, `icon32.png`, `icon48.png`, and `icon128.png` (raster) for Chrome Web Store validation — the CWS rejects SVG icon references. Update `manifest.json` to reference them consistently. The packaged upload should exclude `.claude/`, `.vs/`, `docs/`, `CLAUDE.md`, and `wasm/CLAUDE.md`.

## Capture + compression pipeline

```
chrome.tabs.captureVisibleTab (JPEG 85%)
  → fetch → blob → createImageBitmap
  → OffscreenCanvas down-scaled to ≤ 960 × 600
  → WASM applyCompressionFilter (quantise R/G/B to 8 levels each)
  → convertToBlob (JPEG 25%)
  → base64 → data URL stored in chrome.storage.local
```

Typical result: ~99 % size reduction vs. the raw JPEG, preserving enough structure for thumbnails to remain recognisable. If the WASM module fails to load the pipeline falls back to canvas-only encoding.

## Storage schema

```
chrome.storage.local keys:
  settings                   Settings object (see DEFAULT_SETTINGS in background.js)
  screenshots_{bookmarkId}   Screenshot[] newest-first; { url, title, dataUrl, timestamp }
  screenshot_index           { [bookmarkId]: [{ ts: number }] } — lightweight metadata
                             used by enforceStorageCap without loading image data
  crawl_schedule_state       Scheduled-crawl cursor + pending-idle state
  pending_new_bookmark_captures  IDs queued for first-time capture
```

The index is updated every time screenshots are added, removed, or cleared. If the index is ever missing (e.g. migrating from an older version), `enforceStorageCap` rebuilds it from the raw screenshot keys.

## Building the WASM module

The compiled binary (`wasm/compress.wasm`) is checked in. To rebuild from source:

```bash
npm install -g wabt
wat2wasm wasm/compress.wat -o wasm/compress.wasm
```

`wasm/compress.wat` exports `applyCompressionFilter(ptr, len)`. It iterates over the raw RGBA pixel data held in shared linear memory and rounds each R, G, and B channel to the nearest multiple of 32 (alpha is left untouched). This reduces the unique-colour count from 16 million to 512, dramatically improving JPEG compression ratios while remaining recognisable.

## Development notes

- Reload the unpacked extension from `chrome://extensions` after JS/CSS changes; fully remove and re-add on manifest changes.
- Inspect the service worker via `chrome://extensions` → Bookmarks Reel → *Inspect views: service worker*.
- Inspect the popup with right-click → *Inspect* on the popup.
- Running `chrome.alarms.create(CRAWL_ALARM, { delayInMinutes: 0.1 })` is the usual way to trigger a scheduled crawl on demand while debugging.

## Privacy policy

See [privacy.html](privacy.html) for the full user-facing policy. The Chrome Web Store disclosure copy — including the Limited Use statement and per-permission justifications — lives in [docs/chrome-web-store-listing.md](docs/chrome-web-store-listing.md).
