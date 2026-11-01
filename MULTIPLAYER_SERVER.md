# COZVIL – Multiplayer / Server notes (design doc)

Status: **no real server exists yet.** The SMP ("Shared Survival Server") runs locally with AI bots. Online is shown as "Online (coming soon)".

## Adapters (all in index.html)
- `smpNet` → `LocalAdapter` (now) / `OnlineAdapter` (stub, rejects with "Online (coming soon)"). `smpNet.reconnect()` / `smpNet.status` do backoff reconnect stubs.
- `SMP_PROTO` message types: `join, pos, set, chat, trade, raid, ally, leave`. The client already sends `join` (with `{uid,name,band}`), `chat`, `raid` through `smpNet.send`.
- `Auth` → `LocalAuth` (now) / `OnlineAuth` (stub). Methods: `list, current, create, login, logout, remove, verifyParent, identity`. `Auth.identity()` returns `{uid, name, look, band}`; uid is `local:<id>` or `guest:<id>`. A real server should replace this with its own account id (OAuth / Sign in with Apple / email).
- Local profiles: username + optional PIN. The PIN is salted + SHA-256 hashed (2000 rounds) with lockout – it is a **device lock, not a password**. No credentials leave the device.

## Server-authoritative design (required before real online play)
- Client sends **intents** (`move`, `mine x,y`, `place x,y,id`, `attack target`, `trade offer`, `chat text`); the server validates and returns results. The client must never send "I now have 99 diamonds".
- Server owns: world seed + block edits, inventories, village ownership, hp/hunger, combat resolution, raids, trade settlement, relationships/alliances.
- Rate limits: the client already uses `RateLimiter` (chat 6 msgs/20 s, generic `ACTION_RL` 40 actions/5 s). Use the same or stricter numbers server-side.
- Local save integrity: every save has a cyrb53 checksum (`#ck`) plus a backup slot. This only detects accidental corruption / casual edits – it is **not** anti-cheat; local saves are trusted only in offline modes.
- Moderation (server side): re-run the profanity/PII filter on the server (client lists: EN/TR/DE in `22c_safety`), store reports (`reason`, last N messages), block/mute lists synced per account, shadow-mute repeat offenders, human review queue.
- Minors: server must enforce chat ceiling by age band (child <13 / teen 13–15 in EU => quick-chat only), parental consent before online play, PvP/raid default off, spending limits.
- Offline raid protection: the server must not resolve raids against offline players (or only against a shielded copy). New players get a shield for the first 3 days (`shieldActive`).
  - Client reference rule (implemented in `22c_safety.js`): `smpIncoming({t:'raid'|'siege'})` returns `{ok:false,reason}` when the target is `offline` (tab hidden/app backgrounded, setting `offProt` on), in a 30 s `grace` window after coming back, `shield`ed (first 3 days), or has `raids_off`. Going away also aborts any raid in progress with no loot lost. The server must apply the same rule authoritatively (presence via heartbeat, not the client's word).
- Privacy: collect nothing beyond account id + gameplay state; analytics is opt-in (default OFF, `Analytics` stub sends nothing).


## Update: 20-player SMP, social layer, shop (this build)
**Layout.** A world has up to **20 players** (you + 19 bots locally), one village each, spread on a ring around spawn (about 300 tiles). World seed + block edits are persisted per save slot (`S.sb`, mp slots 6-8).

**Message types** (`SMP_PROTO.msgs`): `join, pos, set, chat, trade, raid, ally, leave` plus `war, peace, clan, dm, pact, emote, report`. Suggested payloads:
- `chat {ch:'g'|'clan'|'dm', to:<uid|null>, text}` - `ch` selects channel (global / clan / direct), `to` is the recipient for `dm`. Server re-filters text and enforces the age-band chat ceiling.
- `war {target}` / `peace {target}` - declare / offer peace. A war must be accepted state on the server; sieges only resolve while both sides are online or the defender has a shield.
- `raid`/`siege {target, units}` - intent only; the server resolves combat. Offline raid protection: the server must not resolve against offline players (shielded copy only).
- `clan {op:'create'|'join'|'leave'|'invite', id}`, `pact {with, kind:'trade'|'defence'|'nap'}`, `emote {id}`, `report {uid, reason, last:[messages]}`.
- `trade {offer}` settles server-side atomically.

**Shop / IAP adapter.** `IAP` is the single seam for real money: `IAP.live=false`, `IAP.buy(pack)` currently returns `iap_soon`. A real implementation must (1) call StoreKit / Play Billing, (2) send the receipt to the server, (3) server validates and credits gems, (4) the client never credits gems itself. In-game spending of earned gems (`shopBuy`) is local-only today; move gem balance and boosts server-side for online play. Safety already in the client: spend limits, parental gate, 5-minute undo, no loot boxes - the server must enforce the same limits.
