# PartSnap

A Progressive Web App (PWA) for photographing and cataloguing spare car parts. Capture a photo, optionally remove the background using an on-device AI model, enter a part ID or SKU, and save the image — all from a mobile browser with no app store required.

---

## Features

- **Camera capture** — uses the rear camera by default, with a flip button to switch
- **AI background removal** — powered by `@imgly/background-removal`, runs entirely in the browser with no data sent to any server
- **Auto background removal** — optional toggle that triggers removal automatically as soon as a photo is taken; persists between sessions
- **Part ID / SKU entry** — filenames are sanitised automatically and saved as `.jpg` (original) or `.png` (background removed)
- **PWA install** — installs to the home screen on Android and desktop Chrome via the in-app install button
- **Offline fallback** — service worker caches assets so the app shell loads without a connection after first visit
- **Auto-updates** — new versions activate immediately on next launch with no user action required

---

## File Structure

```
SnapCarPart.html   — main app (single file)
sw.js              — service worker
manifest.json      — PWA manifest
```

---

## Dependencies

All loaded from CDN at runtime — no build step or npm required.

| Library | Purpose |
|---|---|
| `@imgly/background-removal@1.7.0` | AI background removal (ONNX, runs in-browser) |
| Google Fonts — Syne, JetBrains Mono | Typography |

---

## Local Development

Serve over HTTP — the app will not work opened directly as a `file://` URL due to browser security restrictions (service worker, camera, CORS).

The simplest way is Python's built-in server:

```bash
cd /path/to/project
python -m http.server 8080
```

Then open `http://localhost:8080/SnapCarPart.html` in Chrome.

---

## Deployment

Copy all three files to your web server:

```
SnapCarPart.html
sw.js
manifest.json
```

The app must be served over **HTTPS** for the service worker and camera to work on mobile.

---

## Updating

When you make changes, bump the cache version in `sw.js`:

```js
const CACHE = 'partsnap-v1.10';
```

On next launch, the browser detects the changed `sw.js`, installs the new service worker, activates it immediately via `skipWaiting()`, clears old caches, and serves the updated files. Users do not need to do anything.

---

## Background Removal Notes

- The AI model is downloaded from `staticimgly.com` on first use (several MB)
- After first download, the model is cached by the browser and loads quickly on subsequent uses
- Processing happens entirely on-device — no photos are uploaded anywhere
- Output is saved as PNG to preserve the transparent background
- If removal fails, the original JPEG is kept and the user is notified

---

## Service Worker (`sw.js`)

```js
const CACHE = 'partsnap-v1.9';
self.addEventListener('install', e => self.skipWaiting());
self.addEventListener('activate', e => e.waitUntil(
  caches.keys().then(keys =>
    Promise.all(keys.filter(k => k !== CACHE).map(k => caches.delete(k)))
  ).then(() => clients.claim())
));
self.addEventListener('fetch', e => {
  e.respondWith(fetch(e.request).catch(() => caches.match(e.request)));
});
```

The fetch strategy is **network-first** — the cache is used only as an offline fallback, ensuring users always get the latest files when online.

---

## Browser Support

| Browser | Install | Camera | Background Removal |
|---|---|---|---|
| Chrome Android | ✅ | ✅ | ✅ |
| Chrome Desktop | ✅ | ✅ (webcam) | ✅ |
| Safari iOS | ⚠️ Add to Home Screen only | ✅ | ✅ |
| Firefox | ❌ No PWA install | ✅ | ✅ |
