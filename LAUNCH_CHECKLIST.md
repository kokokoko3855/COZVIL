# COZVIL – Launch checklist (v2.0.0-beta.2)

Legend: ✅ done in the client · 🟡 partly / template only · ⛔ needs a server or a human (not possible in the client)

## 1. Safety & moderation
- ✅ Age gate on account creation (birth month/year). Bands: child <13, teen 13–15 (EU/DE-like locale), adult. Guest = age unknown.
- ✅ Child/teen: quick-chat only; PvP + raids off by default; parental PIN set at creation for children.
- ✅ Parental/Safety panel (Settings → Safety): chat off/quick/filtered, PvP & raid toggles, new-player shield, spending limits, play-time reminder.
- ✅ Chat filter EN/TR/DE (word stems + exact words, leetspeak/spacing tricks), PII blocker (email, phone, URLs, social handles), rate limit, duplicate/caps/spam control, block list, mute, report with reasons, safety tips. SMP typed chat goes through the gate.
- ✅ Client never sends personal info anywhere (no network code).
- ⛔ Real age verification, verifiable parental consent (COPPA / GDPR-K), server-side filtering, report review, bans.
- ⛔ Moderator tooling and a contact address for reports.

## 2. Legal & privacy
- 🟡 Privacy Policy and Terms of Service in-game (EN/TR/DE) – **TEMPLATES, need legal review**.
- ✅ Data export (JSON download) and delete-my-account (local data).
- ✅ Analytics consent screen (default OFF); nothing is tracked.
- 🟡 Impressum placeholder (DE) – ⛔ needs real company/contact data (`COMPANY_PLACEHOLDER` in code).
- ✅ Credits & licenses page; age-rating note page.
- ⛔ Hosting logs / data-processing agreement, GDPR contact, DPO if required, cloud-side deletion.

## 3. Purchases (gem shop)
- ✅ Gem Shop (boosts, VIP, cosmetics, premium pets, battle pass) uses **earned gems only**. No paid randomness / loot boxes anywhere; every price visible; confirm dialog before every spend; 5-minute undo (refund) window.
- ✅ Spending safety: per-day/per-week gem limits (`spendCheck`/`spendRecord`), parental gate (math question + parent PIN) before spending when the age band is child/teen, locked by default for children.
- ✅ `IAP` adapter is a stub (`IAP.live=false`, returns `iap_soon`). Gem packs are shown disabled as "coming soon". Restore-purchases and refund buttons are stubs. No payment data is ever collected or stored.
- ⛔ Real IAP (StoreKit / Play Billing, server receipt validation), App Store / Play review, IAP products, tax, regional pricing, EU consumer-law withdrawal text.

## 4. Fairness / anti-cheat
- ✅ Intent-based design documented (MULTIPLAYER_SERVER.md); client rate limiters in place; save checksums.
- ⛔ Server-authoritative simulation.

## 5. Reliability
- ✅ **Phase-1 save migration**: the three original story slots keep their storage keys (`cozyVillageWars.v1`, `cozvil.slot2`, `cozvil.slot3`); they load as story saves ("Old village save") with every field kept (resources, buildings, crops, inventory, creatures, character, houses, story progress). Verified with a real phase-1 build in a persistent browser profile (`mig.js`, 44 checks).
- ✅ Autosave + backup slot (restore in Settings → Privacy), save schema version (3) and migration, checksum recovery with toast.
- ✅ Offline banner, reconnect stub, global error log + error screen with "copy bug report", version on title screen (2.0.0-beta.1).
- ⛔ Crash reporting service (opt-in).

## 6. Accessibility
- ✅ Text size, colorblind filters (deuteranopia/protanopia/tritanopia), reduce shake, reduce flashing, sound captions, high contrast.
- ✅ PC controls: auto-detects keyboard+mouse, remappable keys (Controls screen, F1), click-to-mine, right-click place/block, wheel zoom, 1-9 hotbar, map (M), fullscreen (F11), 16:9 frame. See DESKTOP_WRAP.md for Electron/Tauri.

## 7. Store readiness
- ✅ Age-rating note (text; suggested ~PEGI 7 / ESRB E10+ – cartoon fantasy violence; **final rating must come from IARC questionnaire** ⛔).
- ✅ Icon set, splash screen, PWA manifest, service worker (installable + offline from GitHub Pages; SW only registers on http/https).
- ⛔ iOS splash images per device size, store screenshots, store listing text, privacy "nutrition labels", developer accounts.

## Extra files (besides index.html)
`manifest.json`, `sw.js`, `icons/icon-192.png`, `icons/icon-512.png`, `icons/icon-maskable-512.png`, `icons/apple-touch-icon.png`, `icons/icon-1024.png`, `icons/favicon-32.png`, `MULTIPLAYER_SERVER.md`, `LAUNCH_CHECKLIST.md`, `DESKTOP_WRAP.md`, `desktop/electron/main.js`, `desktop/electron/package.json`.
**Important:** all of these must be uploaded together with index.html for PWA install/offline to work.
- ✅ Offline raid protection (client side): raids/sieges are refused and running raids are aborted while the owner is away; 30 s grace on return; toggle in Safety settings. The real server must enforce the same rule (see MULTIPLAYER_SERVER.md).
