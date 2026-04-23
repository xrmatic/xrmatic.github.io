---
layout: default
title: Bookmarks Reel — Privacy Policy
description: Privacy policy for the Bookmarks Reel Chrome extension. No data leaves your device.
permalink: /privacy
---

# Bookmarks Reel — Privacy Policy

*Effective date: 22 April 2026 · Version 1.1.0*

---

> **Summary.** Bookmarks Reel is a Chrome extension that builds a local screenshot gallery of your bookmarks. Screenshots, bookmark metadata, and settings are stored only in your browser profile on your device. **No data is sent to the developer or any third party.** There is no analytics, telemetry, advertising, or tracking.

---

## 1. Who we are

"Bookmarks Reel" ("the extension", "we", "us") is an open-source Chrome extension published by its individual developer ("developer"). This policy describes how the extension handles information when you install and use it.

Questions about this policy: [bobchang711@gmail.com](mailto:bobchang711@gmail.com).

## 2. Scope

This policy covers the Bookmarks Reel Chrome extension. It does not cover any website you visit while using the extension, nor any third-party service a bookmarked page may load in its own right.

## 3. What information the extension accesses

The extension uses Chrome APIs to access the following information on your device, solely to provide the features described in Section 5.

| Information | Source | Why it is accessed |
|---|---|---|
| Your bookmark tree (titles, URLs, folder structure, date added, bookmark IDs) | Chrome `bookmarks` API | To know which pages to screenshot and to render the gallery. |
| Content of bookmarked pages, as rendered in your browser | A temporary capture window opened by the extension | To take a screenshot via `chrome.tabs.captureVisibleTab`. |
| URL and title of the active tab | Chrome `tabs` API | Only when the opt-in "visit capture" feature is enabled, to decide whether to screenshot the current page. |
| Per-page signals that indicate a login form (password input presence, autocomplete hints, form action, title keywords, common auth-framework DOM markers) | A read-only script injected via `chrome.scripting.executeScript` | To skip capturing pages that appear to be sign-in screens. |
| Browser idle state | Chrome `idle` API | To hold scheduled captures until the browser is not in active use. |
| Display geometry (screen bounds) | Chrome `system.display` API | To position the capture window inside a visible screen. |

> **Automatic screenshots may include authenticated content.** When the extension opens a bookmarked URL in its capture window, Chrome reuses your logged-in profile. That means automatic screenshots of a bookmarked page that you are signed in to may include content only visible when authenticated (for example, an inbox, an account page, or a dashboard). Section 6 describes the controls that limit this, and Section 8 describes how to turn each feature off.

## 4. What information the extension stores

All of the following is stored **only** in Chrome's on-device extension storage (`chrome.storage.local`) in your current browser profile.

- Compressed JPEG screenshots of your bookmarked pages, keyed by bookmark ID.
- A lightweight metadata index of saved screenshots (timestamps only) used to enforce storage caps efficiently.
- Your extension settings: crawl interval, idle threshold, visit-capture interval, maximum images per bookmark, maximum image age, maximum total storage, login-page-skip toggle, excluded-capture-domains list, per-bookmark auto and visit toggles, popup layout preferences.
- Bookkeeping used by scheduled captures: the cursor into your bookmark list, a pending-new-bookmark capture queue, and a flag indicating whether a scheduled crawl is waiting for the browser to become idle.

## 5. How the extension uses information

Information described in Sections 3 and 4 is used solely to provide the extension's user-facing features:

- Render a searchable, sortable gallery of your bookmarks with their most recent screenshots.
- Refresh those screenshots on a schedule you configure.
- Capture a first screenshot when you add a new bookmark (while auto-crawl is enabled).
- Optionally capture your active tab while you browse a bookmark's domain, when you have explicitly enabled that feature for that bookmark.
- Skip capturing pages that match the login-page heuristic, when the "Skip login pages" setting is on (default: on).
- Skip capturing any page whose domain matches your excluded-capture-domains list.
- Prune old screenshots to stay within the storage cap you configure.

The extension does not use any of this information to build user profiles, deliver advertising, or train models. It is never transmitted to the developer or to any third party.

## 6. Safeguards for sensitive content

- **Default sensitive-domain exclusion list.** The extension ships with a preset list of domains (including major webmail, cloud storage, and banking services) whose automatic captures are skipped unless you remove them from the list.
- **User-editable exclusion list.** You can add any domain (and its subdomains are covered automatically) to the exclusion list on the Settings page.
- **Login-page heuristic.** Before capturing a page, the extension checks for visible password fields, autocomplete hints (`current-password`, `new-password`, `one-time-code`), login-related URL patterns and page titles, form actions referencing sign-in, and common auth-framework DOM markers. When any signal matches, the capture is skipped.
- **Per-bookmark opt-outs.** Every bookmark in the gallery has individual "Auto" and "Visit" toggles so you can disable capture for specific bookmarks without disabling the feature globally.
- **Global pause.** A single switch in the popup and Settings disables all automatic capture.

These safeguards reduce the risk of capturing sensitive or authenticated content but cannot guarantee elimination of it. Please review the default exclusion list when you install the extension, and enable visit capture only on bookmarks where you are comfortable with periodic screenshots of pages you visit on that domain.

## 7. Third parties, network requests, and transfers

The extension does not transmit your screenshots, bookmarks, URLs, titles, settings, or any other data to the developer or any third party. There is no backend server operated by the developer.

The only outbound network requests the extension itself makes are:

- **To each bookmarked URL when it is time to screenshot that bookmark.** This is the same request your browser would make if you opened the page yourself; it is sent directly to the site you bookmarked, not to the developer.
- **To Google's public favicon service** (`https://www.google.com/s2/favicons` via Chrome's built-in `_favicon` feature) to display the small site icons next to each card in the gallery. Only the bookmark's origin is included in this request. Google's use of that request is governed by Google's privacy policy.

The extension does not sell your data, share it with third parties for their own purposes, or use it for any purpose unrelated to the extension's single purpose. No data is transferred internationally by the extension, because no data leaves your device.

## 8. Your controls

- **Pause auto-crawl** globally from the popup or Settings.
- **Disable Auto or Visit capture** per bookmark from the gallery card.
- **Edit the excluded-capture-domains list** in Settings.
- **Adjust the capture schedule, idle threshold, storage cap, and retention** in Settings.
- **Clear all saved screenshots** with one click in Settings.
- **Uninstall** the extension from `chrome://extensions` — Chrome removes all of the extension's locally stored data when it is uninstalled.

## 9. Data retention

Screenshots are retained locally until any of the following occurs:

- They exceed the per-bookmark image limit you configure (oldest is removed).
- They exceed the maximum age you configure (default: 30 days); an hourly cleanup pass deletes older screenshots.
- Total storage exceeds the cap you configure (default: 50 MB, maximum: 500 MB); the oldest 25% of screenshots are pruned.
- You click "Clear All Screenshots" in Settings.
- You uninstall the extension.

## 10. Security

The extension follows Chrome Manifest V3 security defaults: no remote code is loaded, no `eval` is used, and the Content Security Policy is the MV3 default with only `'wasm-unsafe-eval'` relaxed so a bundled WebAssembly image-compression module can run. The `<all_urls>` host permission is declared as an optional host permission and is requested from you on demand, only when you first start a capture feature.

All data lives in Chrome's on-device extension storage, which is scoped to your browser profile.

## 11. Children

The extension is not directed at children under 13 and does not knowingly collect information from them. No information is collected by the developer from any user.

## 12. Chrome Web Store Limited Use disclosure

Bookmarks Reel's use of information received from Chrome APIs adheres to the [Chrome Web Store User Data Policy](https://developer.chrome.com/docs/webstore/program-policies/user-data-faq/), including the Limited Use requirements.

- Information read from Chrome APIs — bookmark tree, tab URL and title, page screenshots, idle state, and display geometry — is used solely to provide the user-facing features described above.
- That information is stored only in local Chrome extension storage on the user's device.
- That information is not transferred to the developer or any third party, is not sold, is not used for advertising, and is not used to train generalized machine-learning models.

## 13. Changes to this policy

Material changes will be reflected by bumping the version shown in the header of this page and updating the effective date. The current version of this policy is bundled with the corresponding release of the extension, so installing or updating the extension gives you a copy of the policy in effect at that time.

## 14. Contact

Questions or privacy concerns: [bobchang711@gmail.com](mailto:bobchang711@gmail.com). The in-product link to this policy is available on the extension's Settings page.

---

*Bookmarks Reel · Privacy policy v1.1.0 · Last updated 22 April 2026.*
