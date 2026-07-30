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

## Deployment status

**Live** at https://healthlings.app with HTTPS enforced (custom domain DNS
verified and cert issued, 2026-07-05). If the domain ever needs re-pointing or
GitHub Pages shows a stuck "DNS check in progress," the fix is: Settings →
Pages → clear the custom domain field, Save, then re-type it and Save again —
this forces GitHub to redo verification instead of retrying a stuck check.
DNS itself (A/AAAA records at the registrar, CNAME for `www`) does not need
touching once set up correctly.

App is **live on Google Play** as of 2026-07-19 (`com.healthlings.app`) and on
the **iOS App Store** as of 2026-07-31 (Apple ID `6787569159`). Both store
badges, both mobile redirects, and GA4 (`G-FN9KC79VDZ`) are wired up in
`_includes/header.html` / `_layouts/default.html`.

## Notes

Store ids live in `_config.yml` (`playstore_id`, `appstore_id`) rather than
inline — the App Store badge, the iOS mobile redirect and the hero download
copy all key off `appstore_id` being non-empty, so blanking it reverts the site
to Android-only.

### Universal Links / App Links

`.well-known/apple-app-site-association` claims **only `/link/*`** and
explicitly excludes everything else, so `/`, `/privacy` and the rest keep
opening in the browser rather than launching the app. Team id `9WVA37WPMA`.
Note the mobile redirect in `_layouts/default.html` would otherwise fight a
broad claim — keep the two consistent if the claimed paths ever widen.

`.well-known/assetlinks.json` lists **two** fingerprints for
`com.healthlings.app`: the Play app-signing cert (from Play Console → Test and
release → Setup → App signing) and the upload cert from `healthlings.jks` in
the app repo, so locally-built APKs verify too. Unlike the AASA file,
assetlinks.json can't scope paths — the `/link/*` restriction has to live in
the Android intent-filter.

**Not yet functional.** As of 2026-07-31 the app declares no
`com.apple.developer.associated-domains` entitlement, no `autoVerify`
intent-filter, no deep-link package, and `MaterialApp` in `lib/src/app.dart`
has no route table — so both files are inert until the app side lands. They're
harmless to serve in the meantime.
