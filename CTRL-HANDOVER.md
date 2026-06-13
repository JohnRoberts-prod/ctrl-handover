# CTRL Project Handover
*Last updated: 2026-06-13 UTC*
*Session ended: /afk called after a long Cavernborn visual rebuild + an email-triage handover package + the monthly /insights report*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.
When John returns, he will paste in any files or context from the web session.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

> NOTE: This session did almost no work *inside* the CTRL app itself. The bulk
> was **Cavernborn** (mobile game, `D:\AI Work\Mobile-Games\games\Cavernborn`)
> plus a **read-only extraction** of CTRL's email-triage subsystem into a
> handover package. No CTRL module code changed. The CTRL module table below is
> carried forward from the last known state, not re-verified this session.

---

## WHAT WE WERE BUILDING THIS SESSION

Three things, in order of effort:
1. **Cavernborn — full visual game rebuild.** The earlier "playable test" was a
   menu/stats dashboard; John rejected it ("text based not visual"). Rebuilt the
   entire UI to the real spec (`CAVERNBORN-ASSETS-v1.md` §E2/§E3): AFK-Journey
   style — 5 tabs, animated cave home, tap-to-kill floor run, warden boss fights
   replaying the real combat sim, paper-doll hero detail, town shops, codex.
   Then wired in **open-licensed art** (game-icons.net CC-BY + Dungeon Crawl
   Stone Soup CC0) so it actually looks like a game. Real MMKV persistence added.
2. **Email-triage handover package** — produced a complete rebuild package for
   CTRL's email triage system at
   `D:\AI Work\Control-Centre\handover\email-triage-handover\` (architecture,
   prompts, schema, all source verbatim, settings, gotchas). Read-only on CTRL.
3. **/insights report** + a mid-session switch to the new **Claude Fable 5**
   model (and a Claude Code CLI update 2.1.143 -> 2.1.175 to surface it).

---

## CURRENT BUILD STATE

### Recently completed (this session)
- Cavernborn: full visual UI under `Mobile-Games/games/Cavernborn/app/src/ui/`
  (theme, assets registry, fx, nav, GameRoot, 11 screens) — tsc clean, verified
  on the Pixel_7 emulator screen-by-screen, save persists across restart.
- Cavernborn: `src/ui/art.ts` manifest + 87 game-icons + 58 DCSS sprites in
  `src/assets/`, `CREDITS.md`, attribution in Settings.
- CTRL: `handover/email-triage-handover/` package (33 files, secret-scanned).

### In progress right now
- Nothing half-written. Cavernborn is at a stable, playable, art-placeholder
  milestone. Next is content/polish, not a broken state.

### Pending / next steps
- Cavernborn: AI-generated final art to replace placeholders (per-key swap in
  art.ts); cave backgrounds still programmatic; chest system (Profile v2),
  spell pages/sockets, expeditions, FTUE/story panels; fonts (Cinzel/Fredoka).
- CTRL proper: unchanged — Project Spine Phase 6 (browser verify) + wire
  remaining tabs (Gmail, CRM, Knowledge, Brand Toolkit, Cloudflare) per STATUS.md.

---

## ALL MODULES — STATUS (CTRL app — carried forward, not re-verified this session)

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | working | |
| Claude Tab | src/frontend/src/modules/claude-tab/ | working | |
| Gmail/Google | src/frontend/src/modules/gmail/ | working | email triage engine documented in handover/ package |
| Tasks | src/frontend/src/modules/tasks/ | working | Google Tasks 2-way via outbox |
| Projects | src/frontend/src/modules/projects/ | working | ProjectSelector wired |
| Finance | src/frontend/src/modules/finance/ | working | |
| Trading | src/frontend/src/modules/trading/ | working | |
| GitHub | src/frontend/src/modules/github/ | working | |
| Cloudflare | src/frontend/src/modules/cloudflare/ | working | |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | working | |
| Settings | src/frontend/src/modules/settings/ | working | |
| Admin | src/frontend/src/modules/admin/ | working | EmailRulesSection manages keyword_groups |

---

## FILES CREATED OR MODIFIED THIS SESSION

Cavernborn (`D:\AI Work\Mobile-Games\games\Cavernborn\`):
```
app/src/ui/theme.ts            — design tokens, rarity colours, 10 cave-theme palettes
app/src/ui/assets.tsx          — placeholder asset registry -> real-art (Image+tint) wiring
app/src/ui/art.ts              — generated manifest of 87 icons + 58 sprites
app/src/ui/fx.tsx              — damage pops, bursts, shimmer, shake, card flips
app/src/ui/nav.tsx             — tab + overlay-stack nav model
app/src/ui/GameRoot.tsx        — top bar, boss-ready ribbon, 5 tabs, overlay stack
app/src/ui/store.ts            — React binding, MMKV/memory store, resetProfile
app/src/ui/game/controller.ts  — tap-to-kill floor run (exact curve math), shop actions
app/src/ui/screens/*.tsx       — CavernHome, CaveSelect, FloorRun, Wardens, WardenFight,
                                 Heroes, HeroDetail, Rescue, Town, Stash, Codex, Modals
app/src/assets/icons/*.png     — 87 game-icons.net icons (CC-BY)
app/src/assets/sprites/*.png   — 58 DCSS sprites (CC0), 4x upscaled
app/metro.config.js            — reverted (MMKV installed, stub removed)
app/babel.config.js            — reanimated -> worklets plugin
app/CREDITS.md                 — art attribution
app/App.tsx                    — renders GameRoot
LEARNINGS.md                   — +10 entries (RN/Windows, APK, Metro, MMKV, ffmpeg, assets)
SESSION_STATE.md               — appended visual-rebuild + art-pipeline section
```
CTRL (`D:\AI Work\Control-Centre\`):
```
handover/email-triage-handover/  — 33-file rebuild package (docs + verbatim source + SCHEMA.sql)
```
RoadToCtrl: `knowledge/video-ideas.md` — 2 new ideas (game rebuild; free-asset tactic).
Memory: `project_cavernborn.md`, `feedback_metro_static_require.md` updated.

---

## RECENT GIT COMMITS

Control-Centre is not a git repo (no history). Linked game repos pushed via /afk:
`batondrop` pushed to JohnRoberts-prod/ctrl-batondrop. Cavernborn has a local
git repo but no linked remote in `project_repos`, so it was NOT pushed —
worth adding a remote if you want auto-backup.

---

## OPEN ISSUES / KNOWN BUGS

- Cavernborn art is placeholder-tier (DCSS pixel sprites upscaled 4x) — reads as
  roguelike, not the painterly AFK-Journey finish in the design doc. Intentional
  interim state.
- Cavernborn Metro must be running (`npx react-native start` in app/) for the
  app to load JS on the emulator; the x86_64 APK is installed but dev-mode.
- Gmail `users.watch` registration for Pub/Sub push is NOT in the CTRL codebase
  and expires ~weekly; hourly poll covers the gap (see handover GOTCHAS #14).

---

## KEY DECISIONS MADE THIS SESSION

- Cavernborn ships visual-first (sprites in a scene), never a management-UI
  skeleton. The asset registry abstracts art so generated art can swap in later
  per-key without touching screens.
- Use open-licensed assets (CC-BY/CC0) as the placeholder tier — commercial-safe,
  free, demoable today — over either programmer-art or premature AI generation.
- MMKV v3 (not v4 — v4 needs nitro peer); real on-device persistence now on.
- CTRL email triage stays rules-only (no per-email LLM) — documented why in the
  handover PROMPTS.md (Claude Code session quota math makes per-email LLM
  unworkable).

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None (no CTRL backend changes).

---

## DATABASE CHANGES THIS SESSION

None to CTRL. Cavernborn uses its own local MMKV profile blob, not the CTRL DB.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- To run Cavernborn: start the emulator (cold-boot if black —
  `emulator -avd Pixel_7 -no-snapshot-load`), `npx react-native start` in
  `Mobile-Games/games/Cavernborn/app`, then launch `com.cavernbornapp`.
  Rebuild APK only if native deps change: `cd android && .\gradlew.bat
  :app:assembleDebug -PreactNativeArchitectures=x86_64` then `adb install -r`.
  `npx react-native run-android` is broken on Windows — don't use it.
- The email-triage handover package is self-contained and secret-free; hand it
  to another instance to rebuild that subsystem.
- John switched this session to Claude Fable 5 (game-building model) and updated
  Claude Code to 2.1.175.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

---

## PROJECTS OUTSIDE CTRL (for full context)

- **Cavernborn** — dark-fantasy idle RPG (RN + Skia + Reanimated). Visual game
  now playable on emulator with open-licensed placeholder art + real persistence.
- **CTRLPro / UnifyBI** — hospitality SaaS dashboard, planning phase.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video.
- **Mobile Games** — BatonDrop (production), Stack Attack, WordDrop, Colour
  Flood, Cavernborn, others.
