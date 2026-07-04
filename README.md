# Tappy Tap Bird — Android publishing guide

Everything in this folder is ready to go. Once your Google Play Console
account is approved, follow these steps in order.

## What's in this folder

- `index.html` — the game itself
- `manifest.json` — web app manifest (makes it an installable PWA)
- `service-worker.js` — offline support
- `icon-96.png` / `icon-144.png` / `icon-192.png` / `icon-512.png` — app icons
- `icon-maskable-512.png` — adaptive icon variant for Android
- `twa-manifest.json` — config template for the Android wrapper (Bubblewrap)

---

## Step 1 — Host the game online

Android's TWA wrapper needs a real public HTTPS URL — it can't point at a
file on your computer. Pick one (all free):

**Easiest: GitHub Pages**
1. Create a new GitHub repo, e.g. `tappy-tap-bird`
2. Upload all the files in this folder to the repo root (`index.html`,
   `manifest.json`, `service-worker.js`, all the `.png` icons)
3. In the repo settings, go to **Pages** → set source to the `main` branch,
   root folder
4. Your game will be live at `https://your-username.github.io/tappy-tap-bird/`

**Alternatives:** Netlify or Vercel — drag-and-drop the folder, both give you
an HTTPS URL in seconds, no GitHub required.

Once it's live, double check in a phone browser that:
- The game loads and is playable
- Chrome's "Add to home screen" prompt appears (confirms the manifest works)

## Step 2 — Update the TWA config

Open `twa-manifest.json` and replace every instance of
`your-domain-or-username.github.io` with your actual hosted domain, and
`com.yourname.tappytapbird` with a unique package ID (this is permanent
once published — can't be changed later, so pick something you're happy
with, like `com.yourname.tappybird`).

## Step 3 — Install Bubblewrap

Bubblewrap is Google's official CLI for turning a PWA into an Android app.
On your own computer (needs Node.js installed):

```
npm install -g @bubblewrap/cli
```

## Step 4 — Generate the Android project

In a new empty folder, run:

```
bubblewrap init --manifest https://your-username.github.io/tappy-tap-bird/manifest.json
```

It will ask a series of questions (package name, app name, colors) —
the answers from `twa-manifest.json` are your reference. It downloads a
JDK and Android SDK automatically if you don't have them.

## Step 5 — Build the app bundle

```
bubblewrap build
```

This produces an `app-release-bundle.aab` file — the format Google Play
requires for new apps. It also generates a signing key
(`android.keystore`) — **back this up somewhere safe**. If you lose it,
you can't update your app on the same listing ever again.

## Step 6 — Verify Digital Asset Links (important)

TWAs need to prove you own both the app and the website, or Android will
show browser address-bar chrome instead of running fullscreen.

1. Bubblewrap generates a file called `assetlinks.json`
2. Upload it to your hosted site at exactly:
   `https://your-username.github.io/.well-known/assetlinks.json`
3. Re-run `bubblewrap validate` to confirm it's wired up correctly

## Step 7 — Upload to Play Console

1. In Play Console, create a new app → fill in the store listing
   (description, screenshots — I can help generate these)
2. Go to **Production** (or **Internal testing** first, recommended) →
   create a new release
3. Upload the `.aab` file from Step 5
4. Fill out the required content rating questionnaire, privacy policy
   URL, and data safety form
5. Submit for review

Google's review for a simple game like this typically takes anywhere
from a few hours to a couple of days.

---

## Notes

- The game already saves high scores locally per-device via
  `localStorage` — no backend needed
- `display: "fullscreen"` in the manifest means no browser UI at all once
  installed — it'll look fully native
- If you want a privacy policy page (required by Play Console even for
  simple games with no data collection), I can draft one — just ask
