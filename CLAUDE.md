# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

This is the marketing/landing page website for the **Healthlings** app
(https://healthlings.app) — a friendly health-habits app (fasting first) with
streaks, badges and a family of "Ling" hatchling characters. The app itself
lives in the private repo `koraybalci/healthlings` (locally `C:\src\healthlings`).

The site is a Jekyll 4.3.3 static site adapted from `pokergrinder-www` (same
Applin/Themeix template, same Gulp asset pipeline). Deployed via **GitHub Pages
legacy build** from `main` — GitHub runs Jekyll itself; no CI workflow.

## Commands

```bash
bundle exec jekyll serve --livereload   # Local dev server → http://localhost:4000/
bundle exec jekyll build                # Build static site to _site/
```

On **Windows**, start the server in the background via PowerShell:
```powershell
Start-Process -NoNewWindow -FilePath "bundle" -ArgumentList "exec jekyll serve --livereload"
```
To stop it: `Get-Process ruby | Stop-Process`

### Asset pipeline (rarely needed)
CSS/JS are pre-compiled and committed (`assets/css/app.min.css`,
`assets/js/build.min.js`). The Gulp pipeline (see `gulpfile.js`, needs
`npm install`) only matters if you change SCSS — for small tweaks, the brand
recolor was done directly in the compiled CSS (PG purple `#5f2167` →
Healthlings terracotta `#a1552f`; also updated in
`assets/scss/base/variables.scss` for a future proper rebuild).

## Structure

- **`index.html`** — homepage; just includes `services.html`
- **`_data/services.yml`** — the six feature cards (title, description, icon,
  optional phone-frame screenshot)
- **`_includes/header.html`** — hero copy + (future) store badges;
  `header_logo.html` — egg logo + wordmark
- **`privacy.html`** — the app's privacy policy (local-only data; keep in sync
  with what the app actually does — it's referenced from the Play Store listing)
- **`assets/images/screen-*.png`** — app screenshots, captured from the seeded
  web build (`flutter run -d web-server --web-port 8090 --target lib/main_seeded.dart`
  in the app repo, then the `serve-web` skill's CDP screenshot script)
- **`CNAME`** — healthlings.app custom domain

## Pending (re-enable when the app ships on Google Play)

1. **Store badges** in `_includes/header.html` — official badge images are in
   `assets/images/` (`Google-Play-Button.png`, `App-Store.png`); follow Google's
   badge guidelines (plain `<img>` in `<a>`, alt "Get it on Google Play", 60px)
2. **Mobile store redirect** in `_layouts/default.html` — copy the script from
   pokergrinder-www with `id=com.healthlings.app`
3. **Google Analytics** in `_layouts/default.html` — needs a new GA4 property
   for healthlings.app; never reuse PokerGrinder's `G-SVJ1Z292LE`
