# SESSION HANDOVER — read this in full before doing anything
*Written: 2026-06-21 · For: the next Claude Code session, continuing the CTRL Personal product build*

---

## 0. READ FIRST — two hard rules (do not break these)

1. **The live CTRL system at `D:\AI Work\Control-Centre\` is OFF-LIMITS for product work.** It is John's daily driver. Never edit it, never `npm install` it, never run/build the standalone product on this PC.
2. **THIS PC IS SOURCE-EDITING ONLY for the product.** Do **NOT** run `npm install`, `tsc`, `build`, or the app on this machine. The previous session broke John's live CTRL by running `npm install` in the product fork — the CPU spike + duplicate processes destabilised the live backend. **All install / build / run / test happens in the Windows Sandbox (now enabled) or a VM — never on the host.** If you need to type-check or build, do it in the Sandbox.

---

## 1. IMMEDIATE: is John's live CTRL back up?
If CTRL is still dropping/broken: the cause is **duplicate CTRL instances fighting over port 3001** (from a reboot + a stray `npm install`). **Fix = one clean restart:** close all CTRL terminal/cmd windows, then run **`D:\AI Work\START-ALL.bat`** (it kills ports 3001/3002/5173/5174 and starts one clean instance: backend 3001, frontend 5173, terminal 3002, vault auto-unlocks → integrations + MCP reconnect). **John's data is intact and fully backed up** at `Z:\Work\AIBackup\ctrl-backup-2026-06-21\` (605 MB DB + WAL + vault + config). Nothing was corrupted — it was only a process/port tangle.

Also: **delete `D:\CTRL-Product\node_modules`** if still present (`rmdir /s /q D:\CTRL-Product\node_modules`) — it should not exist on this machine.

---

## 2. WHAT WE'RE BUILDING (the project)
Turn CTRL into **CTRL Personal** — a sellable, self-installing, **plugin-based** Windows desktop product (Mac later). Uses the customer's **local Claude** (no API key/bill). **Free 3-month beta** via website signup (no billing); production later on **Lemon Squeezy** (paid, plan-gated plugins).

**Two source docs (read them):**
- `D:\AI Work\Control-Centre\skills\skill-release-installer.md` — Electron/NSIS installer, Cloudflare license server, setup wizard, module gating, clean-VM testing.
- `D:\AI Work\Control-Centre\skills\skill-plugin-architecture.md` — **each module is a self-contained plugin** (local `.js` bundles from Cloudflare R2, checksum-verified, dynamic `import()` at startup). New features ship as plugins, not new installers. Plugins are isolated — one never affects another.

**Folder/sandbox architecture (agreed):** the app installs to **Program Files** (immutable, agent can't edit it). All user data lives under **`%CTRL`** = a user-chosen root (set in the wizard → `config.filesystem.root`): `%CTRL/{.ctrl-config.json,.vault,ctrl-data.db,projects/,documents/,plugins/,.cache/}`. The Claude agent the product runs is **sandboxed to `%CTRL` only** (existing `SANDBOX_ROOT` + `validate-path` middleware; vault/db are on a deny-list even inside it).

---

## 3. THE PLAN (authoritative governance doc)
**`D:\CTRL-Product\PROJECT-PLAN.md`** — read it. It has the phases, the **per-run security+code check gate**, the clean-room-per-module QA, risk register, and decision log.

- **Phase 0 ✅ done:** product fork created at **`D:\CTRL-Product\`** (separate repo, package name `ctrl-personal`, fresh git, isolated `.env` → ports 3011/3012/5183 + own `.data` root, no secrets). Live system backed up to NAS.
- **Phase 1 ← IN PROGRESS (next):** clean personal data + config-ify paths to `config.filesystem.root`. The audit script exists: `D:\CTRL-Product\scripts\audit-personal-data.js`. **~31 files** to fix: `John Roberts`→`config.user.name` (6), hardcoded `D:/AI Work` paths→`config.filesystem.root` (16), `johnbenjaminroberts`→config email (10), `JohnRoberts-prod`→config github (4), `ctrlplay`→config branding (19). `Lane7` = 0 (clean). Also remove the `?? 'D:/AI Work'` fallbacks and John's personal seed/migrate project rows. Exit gate: audit=0, type-check clean (in Sandbox), first clean commit.
- Phases 2-10: Cloudflare (license + plugin registry) → Electron shell+wizard → plugin engine (prove with a hello-world plugin first) → modules as plugins one-at-a-time → packaging + clean-VM QA → website/beta signup → beta → production (Lemon Squeezy) → Mac.

**Open decisions (not blocking):** beta length (default per-signup 90 days), production pricing tiers, which modules in the beta (default all).

---

## 4. WHAT ELSE HAPPENED THIS SESSION (context)
- Built a **Network/UniFi dashboard** INTO the live CTRL (real changes in `D:\AI Work\Control-Centre`): backend `services/unifi.service.ts` + `routes/unifi.routes.ts` (mounted `/api/unifi` in `server.ts`), config `integrations.unifi.host=192.168.1.1`, vault key `unifi_api_key`; frontend `modules/network/` + `services/unifi.service.ts` + nav/AppShell/shell.types wiring. These are **uncommitted** in the live system (Control-Centre is not a git repo). This is legit work John wanted — leave it; just be aware it's there.
- Diagnosed John's home network (UniFi): root issues = single AP coverage (U6 Pro was removed), **2.4GHz-only main SSID `mywifi`** forcing congestion, and a **100M powerline uplink** to the upstairs switch (the real bottleneck). Added an IW-HD AP. Recommended fixes given (5GHz on mywifi, min-RSSI, MoCA/Cat6 to replace powerline, fix Plex port-7 cable).
- Shipped Reframe AI videos (Eiffel, Vietnam Real-vs-AI), HeyGen avatar integration in CTRL's Design module, BedBouncer SEO. (All in the LIVE system — done/stable.)

---

## 5. HOW TO RESUME
You (next session) should: confirm John's live CTRL is back (Section 1), then continue **Phase 1** of the product build **in `D:\CTRL-Product` only**, doing any install/typecheck **in the Windows Sandbox, not on the host**. Follow `D:\CTRL-Product\PROJECT-PLAN.md` and its per-run gate.

Full prior transcript (if deep detail needed): `C:\Users\admin\.claude\projects\d--AI-Work\28bf5273-ea29-486f-b0e1-afe2f6560b55.jsonl`
