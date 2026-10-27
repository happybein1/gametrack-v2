# gametrack-v2 — Changelog

Moved out of the repo-wide `CLAUDE.md` on 2026-09-25 to keep that file focused on rules rather than history. Entries below are transcribed verbatim (substance unchanged) from what was previously in `CLAUDE.md`'s per-version table for this app; only pure markdown table syntax (the row-closing ` |`) was stripped. Newest version first.

---




## v2.10

**Smaller cookie banner** (owner, 2026-10-27: "make the cookie banner smaller"). The shared yv-consent banner (`_shared/consent.js`) is more compact: shorter text in en/fr/nl/es ("May we use Google Analytics cookies to see which parts get used? No ads, never sold." + privacy link), 440 px wide instead of 520, 13 px text, tighter padding, 36 px buttons. Same choice and behaviour. Service-worker cache bumped.

## v2.9

**No reload on the first visit** (owner, 2026-10-27: IntentFlow's first open "not so fluid, many screens popping up and changing very fast"). The page reloaded itself whenever a service worker took control, including the very first visit, when there is nothing to update (the page already came from the network). Now `window.yvHadSW` remembers whether a worker already controlled the page at load, and the `activated` / `controllerchange` reloads only fire when it did: updates still arrive with a reload, a first visit no longer blinks. The install banner (`yv-install` v2) now also waits while another first-open screen is showing (cookie banner, welcome or tour, any open dialog), so a first visit shows one thing at a time. Service-worker cache bumped.

## v2.8

**Install banner on first open** (owner, 2026-10-27: every app should offer installing it). The shared `yv-install` snippet (`_shared/install.js`, rolled out by `_shared/apply-install.py`) is inlined before `</body>`: on Android/desktop Chrome, Edge and Samsung Internet an "Install" button opens the browser's own install prompt; on iPhone/iPad a hint says "tap Share, then Add to Home Screen"; both link to the YeahDev install video. Shown until the app is installed or the banner is closed (localStorage `yvInstallDone`), never when the app already runs installed (standalone, iOS home screen, Capacitor/TWA). Texts in en/fr/nl/es from the browser language. Service-worker cache bumped.

## v2.7

sw `gametrack-v2-7`; **cookie consent banner; Google Analytics now loads only after "Accept".** Until the visitor answers, nothing is sent to Google and no analytics cookies are set. The choice is remembered for 180 days in a cookie shared across the whole registrable domain (so one answer covers every *.yeahvibe.com app) with a localStorage copy as fallback; "Decline" is as prominent as "Accept"; `window.yvConsentOpen()` (or opening the page with `#cookie-settings`) lets the visitor change their mind, and withdrawing after accepting switches analytics off at once and deletes the `_ga` cookies. Built on Google Consent Mode v2 (default: all denied). Banner text in English, Dutch, French and Spanish by page language. Snippet source: `_shared/consent.js` (inlined into the page; rolled out by `_shared/apply-consent.py`). No data-model change. Events fired before consent are dropped, not replayed after Accept.

**Verification:** consent logic exercised in a simulated browser (jsdom): first visit shows the banner and loads nothing; Accept loads Google and stores the choice; return visit loads straight away with no banner; reopening and declining switches analytics off; a choice older than 180 days asks again. Not yet checked on a phone.

---

## v2.6

sw `gametrack-v2-6`; **suspected bots are now tagged in GA4.** A best-effort check (automation flag `navigator.webdriver`, headless browsers such as HeadlessChrome, and crawler/HTTP-client user agents; GA4 already drops the IAB known-bot list by itself) makes the GA4 config send `traffic_type=bot` plus the user property `bot_suspect=true`. Nothing is blocked: the hits still arrive, tagged, so a second GA4 "Internal traffic" data filter for the value `bot` can be tested and then activated, and false positives can be reviewed first. The owner's `internal` tag takes priority if both apply. Regular visitors send exactly what they sent before. Verification: inline script parse check only (the bot pattern was not run against real traffic).

---

## v2.5

sw `gametrack-v2-5`; **the owner's own visits can now be excluded from GA4.** This app only reads the owner-traffic flag that IntentFlow/ShopTools set when signed in with the owner's account (cookie `yv_internal` shared across `*.yeahvibe.com`); on a device carrying the flag the GA4 config sends `traffic_type=internal` and the user property `internal_user=true`, so GA4's built-in "Internal traffic" data filter (once set to Active) and the `Internal User` custom dimension can exclude them. No visible change for anyone else; nothing is sent differently on a device without the flag. This app has no sign-in of its own, so it relies on the flag having been set once in that browser by signing in to IntentFlow or ShopTools. Verification: inline script parse check only; not checked against a live GA4 property.

---

## v2.4

sw `gametrack-v2-4` (bumped from stale `gametrack-v1-7`, a leftover from the v1→v2 rewrite); **fixed a real bug**: the app had a fully valid `manifest.json` (all icons present) and a fully valid `sw.js`, but the service worker was never registered anywhere in `index.html` — found via full-repo PWA audit prompted by the QuickTimer incident. Added the standard auto-reload registration `<script>` block right after the manifest `<link>` tag

