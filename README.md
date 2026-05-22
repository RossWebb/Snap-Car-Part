# PartSnap

A mobile-first PWA for photographing and cataloguing spare parts.
Installable on Android and desktop via the browser's Add to Home Screen prompt.

## Usage

1. Open the app and allow camera access
2. Frame the spare part and tap the shutter
3. Enter the Part ID / SKU
4. Tap **Save** — the photo downloads as `{PART-ID}.jpg`

## Install

Visit the GitHub Pages URL in Chrome on Android or desktop.
The browser will offer an install prompt automatically.

## Deploy

Hosted via GitHub Pages. Any push to `main` updates the live app.
The Service Worker enables offline use after first load.

## Stack

Plain HTML/CSS/JS — no build step, no dependencies.
