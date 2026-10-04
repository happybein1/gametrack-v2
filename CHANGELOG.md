# gametrack-v2 — Changelog

Moved out of the repo-wide `CLAUDE.md` on 2026-09-25 to keep that file focused on rules rather than history. Entries below are transcribed verbatim (substance unchanged) from what was previously in `CLAUDE.md`'s per-version table for this app; only pure markdown table syntax (the row-closing ` |`) was stripped. Newest version first.

---

## v2.6

sw `gametrack-v2-6`; **suspected bots are now tagged in GA4.** A best-effort check (automation flag `navigator.webdriver`, headless browsers such as HeadlessChrome, and crawler/HTTP-client user agents; GA4 already drops the IAB known-bot list by itself) makes the GA4 config send `traffic_type=bot` plus the user property `bot_suspect=true`. Nothing is blocked: the hits still arrive, tagged, so a second GA4 "Internal traffic" data filter for the value `bot` can be tested and then activated, and false positives can be reviewed first. The owner's `internal` tag takes priority if both apply. Regular visitors send exactly what they sent before. Verification: inline script parse check only (the bot pattern was not run against real traffic).

---

## v2.5

sw `gametrack-v2-5`; **the owner's own visits can now be excluded from GA4.** This app only reads the owner-traffic flag that IntentFlow/ShopTools set when signed in with the owner's account (cookie `yv_internal` shared across `*.yeahvibe.com`); on a device carrying the flag the GA4 config sends `traffic_type=internal` and the user property `internal_user=true`, so GA4's built-in "Internal traffic" data filter (once set to Active) and the `Internal User` custom dimension can exclude them. No visible change for anyone else; nothing is sent differently on a device without the flag. This app has no sign-in of its own, so it relies on the flag having been set once in that browser by signing in to IntentFlow or ShopTools. Verification: inline script parse check only; not checked against a live GA4 property.

---

## v2.4

sw `gametrack-v2-4` (bumped from stale `gametrack-v1-7`, a leftover from the v1→v2 rewrite); **fixed a real bug**: the app had a fully valid `manifest.json` (all icons present) and a fully valid `sw.js`, but the service worker was never registered anywhere in `index.html` — found via full-repo PWA audit prompted by the QuickTimer incident. Added the standard auto-reload registration `<script>` block right after the manifest `<link>` tag

