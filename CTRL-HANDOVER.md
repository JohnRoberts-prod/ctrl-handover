# CTRL Project Handover
*Last updated: 2026-05-19 (late evening) UTC*
*Session ended: completed the Reflex Ring playable prototype + APK (4th and final casual-game prototype), Open Design tab embedded in CTRL, and ctrlplay-website foundation*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db` — WAL mode + synchronous=NORMAL already on

Plus sibling repos: `D:\AI Work\Mobile-Games\` (RN games + Vite prototypes), `D:\AI Work\BedBouncer\`, `D:\AI Work\ctrlplay-website\` (new this session), `D:\AI Work\open-design\` (cloned this session).

---

## WHAT WE WERE BUILDING THIS SESSION

This was a long mixed-bag session. Five distinct workstreams landed:

1. **Reflex Ring prototype** — full Vite + React + TS playable prototype + Capacitor APK, fourth (and final) casual-game in the portfolio after Colour Flood and Stack Attack earlier in the day. All 11 wireframe screens + 3 shared subsystems wired to a real ring-rotation engine.
2. **Open Design tab in CTRL** — a new 5th sub-tab inside the existing Design module that spawns `pnpm tools-dev` in the sibling `D:\AI Work\open-design\` repo and embeds it via iframe on localhost:3000. Includes new backend service, routes, WebSocket log stream, and frontend panel with 5 visible states.
3. **ctrlplay-website foundation** — new repo at `D:\AI Work\ctrlplay-website\` with `public/games.json`, landing page, Cloudflare `_headers`, and full deploy README. Ready to push to GitHub + connect to Cloudflare Pages.
4. **BatonDrop extra_life fix** — user reported the "extra life" baton was appearing far too often despite "extremely rare" copy. Weight 100 → 12, plus skip-spawn-at-full-lives. Committed + pushed to `ctrl-batondrop`.
5. **Open Design installed** — cloned `nexu-io/open-design`, ran `pnpm install` (12 min), confirmed it loads in CTRL's iframe.

Final activity: user invoked `/afk`. No work in flight.

---

## CURRENT BUILD STATE

### Recently completed (this session)

- **Reflex Ring prototype** — `Mobile-Games/games/reflexring/app/` (Vite app), APK at `Mobile-Games/games/reflexring/builds/reflexring-debug-2026-05-19.apk` (~3.9MB). Engine in `src/engine/game.ts` is pure functions (tick / tap / scoring / tier escalation). Ring renders as SVG via `RingCanvas.tsx`. All 11 wireframe screens implemented (Splash, Home, Game with Classic/Sprint/Daily modes, Game-over, Leaderboard, Settings, More Games, Tutorial, Daily Login modal, Achievement toast).
- **Open Design tab in CTRL** — `src/backend/src/services/open-design.service.ts` (singleton service, mirrors MetroService), `src/backend/src/routes/open-design.routes.ts` (start/stop/status + `OPEN_DESIGN_WS_PATH`), wired into `server.ts`. Frontend at `src/frontend/src/modules/design/components/OpenDesign/` (4 files: panel, service, css, index).
- **ctrlplay-website bootstrapped** — landing page, games.json with BatonDrop seeded, Cloudflare Pages `_headers` (5min cache on games.json, 30d immutable on icons, CORS open), full README.
- **BatonDrop extra_life rebalance** — three TS files touched in `Mobile-Games/games/batondrop/app/src/`, auto-committed + pushed.
- **Open Design installed** at `D:\AI Work\open-design\` and verified running inside CTRL's iframe.
- **`--no-open` flag** added to OpenDesignService default `startCommand` so the dev server doesn't auto-launch a duplicate browser window.

### In progress right now
- **Nothing functional.** All five workstreams ended cleanly.

### Pending / next steps

1. **Install Reflex Ring APK on phone** — built but phone disconnected before adb install. Reconnect, `adb devices`, then `adb install -r "D:\AI Work\Mobile-Games\games\reflexring\builds\reflexring-debug-2026-05-19.apk"`.
2. **Build + ship BatonDrop v1.9.7** with the extra_life fix. Bump versionCode 20→21, versionName 1.9.6→1.9.7, `cd app/android && ./gradlew bundleRelease`, upload signed AAB to Play Console internal testing → production.
3. **Push `ctrlplay-website` to GitHub** as `JohnRoberts-prod/ctrlplay-website`, connect to Cloudflare Pages (build cmd empty, output dir `public`).
4. **(Carried over) Vertex AI vault keys** — add `vertex_project_id=ctrl-493720` and `vertex_region=us-central1` via Settings → Vault. From the earlier session.
5. **Word Chain prototype** — only remaining casual-game prototype not yet built. Same Vite + React + TS + Capacitor pipeline as the other three. Port 5183.
6. **Retrofit Colour Flood + Stack Attack `MoreGames.tsx`** to use the shared `crossPromo.service.ts` per CROSS-PROMO-SYSTEM.md — DEFERRED by user: "leave that, apply next time we change those games."

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | Working | Widget grid rebuilt earlier session |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Working | Sidebar panel |
| Gmail/Google | src/frontend/src/modules/gmail/ | Working | setState bug fixed earlier session |
| Tasks | src/frontend/src/modules/tasks/ | Working | Rebuilt Monday-style earlier session |
| Finance | src/frontend/src/modules/finance/ | Working | |
| Trading | src/frontend/src/modules/trading/ | Working | |
| GitHub | src/frontend/src/modules/github/ | Working | |
| Cloudflare | src/frontend/src/modules/cloudflare/ | Working | |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | Working | Dual-location resolver earlier session |
| Design | src/frontend/src/modules/design/ | Working | **NEW: 5th sub-tab "Open Design" embeds the sibling repo** |
| Settings | src/frontend/src/modules/settings/ | Working | Vertex AI status still red pending vault keys |
| Admin | src/frontend/src/modules/admin/ | Working | ProjectCard rebuild earlier session |

---

## FILES CREATED OR MODIFIED THIS SESSION

### CTRL repo (`D:\AI Work\Control-Centre\`)
```
src/backend/src/services/open-design.service.ts        — NEW
src/backend/src/routes/open-design.routes.ts           — NEW
src/backend/src/server.ts                              — added Open Design import, route mount, WS server
src/frontend/src/modules/design/components/OpenDesign/OpenDesignPanel.tsx  — NEW
src/frontend/src/modules/design/components/OpenDesign/openDesign.service.ts — NEW
src/frontend/src/modules/design/components/OpenDesign/open-design.css     — NEW
src/frontend/src/modules/design/components/OpenDesign/index.ts            — NEW
src/frontend/src/modules/design/Design.tsx             — 5th tab + Palette import + saved-tab parser update
SESSION_STATE.md                                       — fully rewritten
LEARNINGS.md                                           — 4 new entries
```

### ctrlplay-website (`D:\AI Work\ctrlplay-website\`)
```
README.md                — NEW (Cloudflare Pages setup + game-launch procedure)
.gitignore               — NEW
public/index.html        — NEW (landing page)
public/games.json        — NEW (BatonDrop seeded as the only live entry)
public/_headers          — NEW (Cloudflare cache rules + CORS)
public/icons/README.md   — NEW (icon spec + per-game checklist)
```

### BatonDrop (`D:\AI Work\Mobile-Games\games\batondrop\`)
```
app/src/constants/game.ts        — extra_life weight 100 → 12 with lock comment
app/src/engine/BatonSpawner.ts   — pickSpecial(level, livesAtCap), buildBaton + spawnBaton signature update
app/src/engine/GameEngine.ts     — loadLane() passes lives >= LIVES_PER_RUN to spawnBaton
SESSION_STATE.md                 — fully rewritten
```

### Reflex Ring (`D:\AI Work\Mobile-Games\games\reflexring\`)
```
design/wireframes/             — NEW (Claude Design bundle + SOURCE.md)
app/ (entire app)              — NEW (Vite + React + TS + Capacitor)
builds/reflexring-debug-2026-05-19.apk  — NEW (~3.9MB)
builds/README.md               — NEW (sideload instructions)
SESSION_STATE.md               — fully rewritten
```

### Sibling repo (cloned this session)
```
D:/AI Work/open-design/        — git clone https://github.com/nexu-io/open-design (4442 files)
                                  pnpm install completed (12 min)
```

---

## RECENT GIT COMMITS

BatonDrop (the one repo that pushed this session — others use folder-only layout):
```
09a91b5  Auto-backup: 2026-05-19         <- extra_life fix
b704d89  Bump to v2.0.0 (versionCode 21)
cc316f3  How to Play: guard catch impact against multi-fire
865def9  How to Play: animated Basics demo using real game assets
6154366  Loading screen: replace spinner with pulsing logo + amber halo
```

---

## OPEN ISSUES / KNOWN BUGS

- **Reflex Ring APK not installed on phone yet** — phone disconnected during build. Reconnect + adb install when ready.
- **BatonDrop fix not yet shipped** — three TS files committed but no new AAB built. Player-facing fix needs Play Store push.
- **Vertex AI vault keys still missing** — from earlier session. Two vault entries pending, then Vertex pathway goes live for the £225 GCP credit.
- **Pre-existing TS errors in `GoogleSignInService.ts`** in BatonDrop — unrelated to this session's work, surfaced during tsc check.
- **Cross-promo retrofit deferred** for Colour Flood + Stack Attack `MoreGames.tsx`. Both currently use hardcoded game lists; the shared `crossPromo.service.ts` pattern from `CROSS-PROMO-SYSTEM.md` should replace them at next per-game touch.

---

## KEY DECISIONS MADE THIS SESSION

- **Bold Arcade is the locked CTRL Play house style** — three independent Claude Design sessions (Colour Flood -> Stack Attack -> Reflex Ring) all recommended Variation B. Pattern is provable.
- **Open Design integrates as a sub-tab** of the existing Design module, not a separate top-level page. Keeps "Design" as one mental concept.
- **`--no-open` in Open Design startCommand** — added after the dev server kept auto-launching a duplicate browser window alongside the CTRL iframe.
- **extra_life weight = 12** (user chose from a 3-option menu). Targets ~1% of all batons at L20+ to match "extremely rare" copy.
- **extra_life smart spawn** — skip the RARE pool entirely when `lives === LIVES_PER_RUN`. When it appears, it always means something.
- **Cross-promo retrofit DEFERRED** to next per-game touch (user call).
- **CTRL DB already has WAL + synchronous=NORMAL + 32MB cache** — no perf tuning needed.
- **Casual prototype pattern is reproducible** — Vite + React + TS + Capacitor + same folder shape works for any of the four casual games. Ports 5180/5181/5182 used, 5183 reserved for Word Chain.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
GET  /api/open-design/status   -> installed + running + responding + port + path + pid + startedAt
POST /api/open-design/start    -> spawn pnpm tools-dev --no-open in D:/AI Work/open-design
POST /api/open-design/stop     -> taskkill /T /F the process tree
WS   /api/open-design/logs     -> rolling 200-line log buffer streamed via WebSocket
```

All routes gated by `requireVaultUnlocked`. Service follows the exact shape of `MetroService` (singleton, taskkill /T /F on Windows, HTTP probe for "responding" status).

---

## DATABASE CHANGES THIS SESSION

None. No new migrations, no schema changes, no new tables. SQLite already had WAL on and 93 indexes covering the hot query paths.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. **Open Design starts via the new tab**, not from a terminal. If it won't start, the first place to look is the WebSocket log stream (visible in the "starting" view) — likely either pnpm not on PATH or port 3000 in use. Service falls back to safe defaults if `.ctrl-config.json` has no `openDesign` key.
2. **Open Design iframe loads localhost:3000.** If the iframe is blank but the "open in browser" button works, it's an X-Frame-Options header on Open Design's dev server. Fix lives in `D:\AI Work\open-design\` (NOT in CTRL), per the spec.
3. **BatonDrop is LIVE on Play Store** (approved 2026-05-19 earlier). The extra_life fix is committed but not shipped — needs versionCode bump + signed AAB build + Play Console upload before users see it. 3-layer keystore backup pattern documented in `project_keystore_backups.md` memory.
4. **ctrlplay-website is a fresh repo** at `D:\AI Work\ctrlplay-website\` — has `.gitignore` but no `.git` yet. Init + push when ready.
5. **Reflex Ring engine** is fully spec-locked: ±2° forgiveness, 12° zone minimum, 0.45s speed floor, `onPointerDown` for tap. Don't change those without re-reading the GDD.
6. **Pre-existing BatonDrop TS errors** in `GoogleSignInService.ts` are not from this session. They've been there for weeks. Don't waste time on them unless asked.
7. **The `nexu-io/open-design` repo is real** — 4442 files, pnpm 10.33.2. pnpm install reports two harmless warnings (missing `.EXE` for `od` CLI bin, ignored build scripts for core-js/sharp/etc.) — those are fine.
8. **WSL Bash on Windows can mangle backslashes in `node -e "..."`** — when scripting Windows path operations, write to a temp `.cjs` file via the Write tool, then `node /path/to/file.cjs`. The afk-push-repos.cjs script in `/tmp` uses this pattern.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

To run Reflex Ring locally instead of via APK:
```
cd D:\AI Work\Mobile-Games\games\reflexring\app && npm run dev
```
Same for Colour Flood (5180) and Stack Attack (5181).

To start Open Design separately (it is just a sibling repo):
```
cd D:\AI Work\open-design && pnpm tools-dev --no-open
```

---

## PROJECTS OUTSIDE CTRL (for full context)

- **BatonDrop** — LIVE on Google Play (com.batondrop, approved 2026-05-19). extra_life fix committed locally, needs versionCode bump + signed AAB before next ship.
- **Reflex Ring / Stack Attack / Colour Flood** — Vite browser prototypes + sideloadable debug APKs. Ports 5180-5182. Not yet ported to RN 0.85 (the production target).
- **Word Chain** — folder scaffold only. Pattern proven by the other three casuals; ~half-day to clone the pipeline.
- **BedBouncer** — ESP32 smart alarm, brand portal complete, website live at bedbouncer.co.uk. Quiet this session.
- **CtrlPro / Homeland / Personal** — paused per user instruction earlier. No work this session.
- **ctrlplay-website** — NEW this session. Public-facing landing page + cross-promo registry. Ready to push to GitHub + connect to Cloudflare Pages.
- **open-design** — sibling tool repo (nexu-io/open-design). Cloned + installed this session. CTRL embeds it via iframe but never owns its code.
