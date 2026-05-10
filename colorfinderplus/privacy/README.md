# Color Finder Plus Privacy Policy

Effective date: May 9, 2026 · Version 1.0.0

* * *

> Summary. Color Finder Plus is a Chromium-based browser extension that helps you inspect colors on webpages. Visible page imagery, sampled colors, and your extension settings are processed locally in your browser profile on your device. No data is sent to the developer or to any third party. There is no analytics, telemetry, advertising, or tracking.

* * *

## 1. Who we are

"Color Finder Plus" ("the extension", "we", "us") is a browser extension published by its developer. This policy describes how the extension handles information when you install and use it.

Questions about this policy should be directed using the support contact you provide in the store listing or support page for the extension.

## 2. Scope

This policy covers the Color Finder Plus extension itself. It does not cover any website you visit while using the extension, or any third-party service that a website may load in its own right.

## 3. What information the extension accesses

The extension uses browser APIs to access the following information on your device, solely to provide the features described in Section 5.

| Information | Source | Why it is accessed |
| --- | --- | --- |
| The visible contents of the current tab | `chrome.tabs.captureVisibleTab` | To sample visible pixels for the color picker and region-based color extraction tools. |
| The active tab and its window | `chrome.tabs` API | To determine which tab to inspect and which browser window to capture from. |
| Supported webpage DOM content | Content scripts running on supported pages | To display the picker overlay, magnifier, and selection box on the page you choose to inspect. |
| Your extension settings and recent color history | `chrome.storage.local` | To preserve side panel preferences, picker zoom, and recent results on your device. |

> Color sampling may include visible authenticated content if you choose to inspect a page while signed in. For example, if you run the picker on a dashboard or account page that is visible in your browser, the extension samples the pixels currently shown on screen. That processing stays local to your browser and device.

## 4. What information the extension stores

All stored data is kept only in the browser's on-device extension storage (`chrome.storage.local`) in your current browser profile.

- Recent picked colors
- Current selection results from the box-selection tool
- Generated palette suggestions derived from selected colors
- Side panel settings such as density, font sizes, section visibility, result limits, and picker zoom

The extension does not store full-page screenshots in persistent remote storage. The visible-tab capture is used locally in memory for color analysis.

## 5. How the extension uses information

Information described in Sections 3 and 4 is used solely to provide the extension's user-facing features:

- Pick a visible webpage color and show its Hex and RGB values
- Display a magnified preview around the cursor while picking
- Let you drag a selection box and extract visible colors from the selected region
- Sort extracted colors by frequency
- Generate 4, 8, 16, and 32-color palette suggestions
- Remember your recent picks and extension settings locally

The extension does not use this information to build user profiles, deliver advertising, train models, or perform analytics. It is never transmitted to the developer or to third parties.

## 6. Permissions and access

The extension currently uses the following permissions and access:

- `tabs`: to identify the active tab and capture the visible tab image for local pixel sampling
- `storage`: to save local settings and recent color history
- `sidePanel`: to present the extension user interface
- `scripting`: to ensure the page overlay tools can run when needed
- `<all_urls>` host access: to allow the picker overlay and selection tools to operate on supported websites

The extension does not run on restricted browser-internal pages such as `chrome://`, `edge://`, extension-management pages, or other pages blocked by the browser.

## 7. Third parties, network requests, and transfers

The extension does not transmit screenshots, color selections, settings, browsing history, or any other user data to the developer or to any third party. There is no backend server operated by the developer.

The extension does not include analytics SDKs, advertising SDKs, tracking scripts, or telemetry services.

No user data is sold, shared, rented, or transferred for third-party marketing or profiling purposes.

## 8. Your controls

You control when the extension interacts with a page:

- Start the picker only when you want to inspect a visible pixel
- Start the box-selection tool only when you want to analyze a visible page region
- Clear current results from the side panel
- Adjust side panel density, font sizes, picker zoom, and result limits from the Options page
- Uninstall the extension from the browser's extension manager

When the extension is uninstalled, browser-managed local extension storage is removed by the browser.

## 9. Data retention

Recent colors and settings remain in local extension storage until one of the following happens:

- You clear results inside the extension
- You change settings or recent results are overwritten by newer ones
- You clear browser extension storage through your browser profile
- You uninstall the extension

## 10. Security

Because the extension operates locally in your browser, the security of stored settings and recent color history depends on the security of your device, browser profile, and browser installation.

## 11. Changes to this policy

We may update this Privacy Policy from time to time. Any changes will be reflected by updating the effective date and version at the top of this page.

## 12. Contact

Questions or privacy concerns: bobsdevattic@gmail.com. The in-product link to this policy is available on the extension's Settings page.
