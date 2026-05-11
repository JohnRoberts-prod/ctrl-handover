# CTRL Project Handover
*Last updated: 2026-05-11 ~21:10 UTC*
*Session ended: ready to initialise the CrunchBall React + Vite + TypeScript prototype. Name decision locked (CrunchBall over GridBrawl). Vite init command is queued but NOT executed yet.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a Claude Code session. John Roberts is the developer. Read this entire document before responding.

The work touched **three projects** today:
1. **CTRL trading** — discussion only, multi-phase build plan, no code, blocked on task creation (wrong Google account)
2. **WordDrop** — rack visual partially landed (Daily Puzzle works, Gameplay still uses a floating placeholder rack)
3. **CrunchBall** — brand new mobile game, scaffolded today, full GDD in repo, 14 Phase 1 tasks created, name locked, next step is `npm create vite@latest`

When John returns he will almost certainly continue with CrunchBall (the most recent thread). If not, the other two are still open.

---

## WHAT WE WERE BUILDING THIS SESSION

### Workstream 1 — CTRL trading "Strategies" tab
John asked why the tab doesn't show newly discovered strategies, win rates, enable/disable, or weighting. I flagged the gap: the strategies table has one seeded row (`momentum-sector`), no discovery engine, no allocator, no trade-to-strategy attribution, no autonomous executor. John picked **"Full systematic engine"** as the scope. I wrote a 6-phase plan (schema → trade attribution → discovery → allocator → executor → UI). John asked to capture as CTRL tasks for later. **Task creation failed** — the "CTRL" Google Tasks list John created sits on a different Google account than the one CTRL/MCP are authenticated against. **The 6-phase plan is in the handover doc only, not in any task system yet.**

### Workstream 2 — WordDrop rack visual
Goal: Scrabble-style wooden tile holder at the top of the board for both Daily Puzzle and infinite Gameplay. Built `TileRack` component, integrated into `PuzzleBoard`. **Daily Puzzle now shows a proper wooden rack** (rim + channel + tiles in channel). Gameplay still has a floating `<TileRack>` above `<GameBoard>` as a placeholder — not integrated into the wood frame yet. Pending work fully scoped in `WordDrop/SESSION_STATE.md`.

### Workstream 3 — CrunchBall (new mobile game) ← latest, this is where the session ended
John asked to create CrunchBall mirroring BatonDrop's CTRL setup. Scaffolded everything: folder tree, stub docs, project-switching skill (`/project-crunchball`), CTRL DB entry. Then John dropped the full GDD (working title in the doc: "GridBrawl"). I populated CRUNCHBALL-GDD.md, PRODUCT.md, theme-asset-prompts.md, skill-crunchball-workflow.md. Created 14 Phase 1 prototype tasks + 1 Vite init task in Google Tasks (default list, prefixed `[CrunchBall]`). John picked CrunchBall as the final name (not GridBrawl). The current next step is initialising the Vite project under `app/`.

---

## CURRENT BUILD STATE

### Recently completed this session
- **WordDrop:**
  - `src/components/TileRack.tsx` — NEW. Linear-gradient wooden rack (NOT SVG — SVG version did not render visibly).
  - `src/components/PuzzleBoard.tsx` — integrated `<TileRack>`, added `RACK_RIM_RATIO=0.45`, recomputed `previewH`, tiles at `top: rimExtra`.
  - `src/screens/Gameplay.tsx` — floating `<TileRack>` above `<GameBoard>` (placeholder), bottom picker removed.
  - `SESSION_STATE.md` + `LEARNINGS.md` written.
- **CrunchBall:**
  - Folder tree mirroring BatonDrop (`app/`, `backend/`, `brand/`, `files/`, `knowledge/`, `skills/`, `src/`).
  - All 5 doc files populated from GDD: CRUNCHBALL-GDD.md (21 sections), PRODUCT.md, theme-asset-prompts.md, SESSION_STATE.md, LEARNINGS.md.
  - `skills/skill-crunchball-workflow.md` — tech stack, build order, hard design rules, gotchas.
  - `C:\Users\admin\.claude\skills\project-crunchball\SKILL.md` — switching skill (`/project-crunchball` is live).
  - CTRL DB entry: `crunchball | CrunchBall | parent=mobile-games | shortCode=CR | folderPath=D:\AI Work\Mobile-Games\games\crunchball\ | status=planning`. Description + tagline + brandName set.
  - Mobile-Games CLAUDE.md updated with CrunchBall in Current Games table (also added Cavernborn, Homeland, bumped WordDrop to "In development").
  - 14 Phase 1 build tasks + 1 Vite init task created in default Google Tasks list, all prefixed `[CrunchBall]`. Duplicates from MCP retries cleaned up. Name-decision task marked complete.

### In progress right now
- **CrunchBall Vite init.** The command is queued: `cd "D:/AI Work/Mobile-Games/games/crunchball/app" && npm create vite@latest . -- --template react-ts && npm install`. John was asked "kick it off now?" — left mid-decision.
- **WordDrop GameBoard rack integration.** Floating placeholder above GameBoard; needs the same surgery as PuzzleBoard (rack zone inside wood frame, `cellTop` offset, falling-tile entry Y adjustment).

### Pending / next steps
**CrunchBall (active thread):**
1. Run Vite init (above).
2. Start P1.1 — Grid rendering. 11×18 portrait grid. Start with `GRID` constant + static SVG render, then add pinch/zoom + drag pan.
3. Continue Phase 1 sequence in GDD §21 (14 tasks total).
4. Create `#crunchball` Discord channel + add channel ID to `.ctrl-config.json` (deferred).

**WordDrop:**
1. Mirror PuzzleBoard's rack surgery on GameBoard. See `WordDrop/SESSION_STATE.md` for exact steps and the falling-tile-entry adjustment needed.
2. Decide on column-fill indicators (keep or remove for board parity with PuzzleBoard).
3. Remove the floating `<TileRack>` + `topRackWrap` from Gameplay.tsx.

**CTRL trading systematic engine:**
1. Phase 1 = schema changes (new columns + 2 new tables). See "Phase plan" in this doc.
2. The 6 phases are documented in the previous handover (commit `757d009`) and in chat only — never captured as tasks. Find a way to persist (different Google account, or use CTRL's own tasks endpoint at `POST /api/tasks/...`, or just leave in this doc).

---

## ALL MODULES — STATUS (CTRL itself, unchanged this session)

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Trading | src/frontend/src/modules/trading/ | partial | UI fine. Underlying engine empty — one seeded strategy, no orders attributed. See systematic engine plan. |
| Projects | src/frontend/src/modules/projects/ | working | CrunchBall added via POST /api/projects (shortCode CR). |
| Other CTRL modules | n/a this session | unchanged | |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
WordDrop:
  src/components/TileRack.tsx                   — NEW. Linear-gradient wooden rack.
  src/components/PuzzleBoard.tsx                — TileRack integrated; RACK_RIM_RATIO.
  src/screens/Gameplay.tsx                      — floating TileRack added (placeholder).
  SESSION_STATE.md                              — NEW.
  LEARNINGS.md                                  — NEW.

CrunchBall (all NEW):
  D:\AI Work\Mobile-Games\games\crunchball\
    CRUNCHBALL-GDD.md                           — full GDD (21 sections).
    GRIDBRAWL_HANDOVER.md                       — original handover (kept as source).
    PRODUCT.md                                  — product positioning + roadmap.
    SESSION_STATE.md                            — initial state.
    LEARNINGS.md                                — initialised.
    theme-asset-prompts.md                      — visual direction + asset checklist.
    skills/skill-crunchball-workflow.md         — tech stack, build order, gotchas.

  C:\Users\admin\.claude\skills\project-crunchball\
    SKILL.md                                    — project-switching skill (mirrors batondrop).

  D:\AI Work\Mobile-Games\CLAUDE.md             — Current Games table updated.

CTRL: none modified this session.
```

---

## RECENT GIT COMMITS

CrunchBall folder is not a git repo. WordDrop has staged but uncommitted state from earlier scaffolding. CTRL repo not touched.

---

## OPEN ISSUES / KNOWN BUGS

- **CTRL "Strategies" tab is misleading.** UI implies it shows active strategies with win rates, but the underlying data has only one seeded strategy that no orders are attributed to. Until phases 1+2 of the systematic engine land, the tab is decorative.
- **WordDrop GameBoard rack incomplete.** Floating placeholder must be replaced with an integrated rack zone.
- **CrunchBall Vite init not yet run.** App folder is empty.
- **CTRL trading engine has no tasks anywhere.** Plan exists in chat + this doc only.
- **MCP task_create duplication.** I observed the MCP creating tasks 2–3 times for some calls. Cleaned up the visible duplicates; worth double-checking nothing else got duplicated.
- **Discord `#crunchball` not created.** Channel needs manual creation on Discord, then `.ctrl-config.json` update under `discord.channels.crunchball`.

---

## KEY DECISIONS MADE THIS SESSION

### CrunchBall
- **Final name: CrunchBall.** Locked. (Original design handover used "GridBrawl" as a working title — kept the original `GRIDBRAWL_HANDOVER.md` file as the source artifact.)
- **CTRL DB id:** `crunchball`. **ShortCode:** `CR` (CB was taken by Cavernborn).
- **Folder layout:** mirrors BatonDrop exactly.
- **Tech stack:** React + Vite + TypeScript for prototype. Unity for production. Firebase or Supabase backend (Phase 2 decision).
- **All match state validated server-side** (anti-cheat principle).
- **Hard design rules (never violate):** turnover is sacred, uncontested = automatic, probability shown before commit, no pay-to-win, no permanent character death, AI never cheats, server validates all state.

### WordDrop
- **TileRack uses `react-native-linear-gradient`, not `react-native-svg`.** SVG version with `<Defs>` + `url(#id)` did not render visibly in RN 0.85 + this project's svg lib when inside an absolute-positioned parent. Do NOT revert.
- **Rack height = cell + cell × 0.45** (rim extra). Tiles sit at `top: rimExtra` (below rim, in channel).
- **In Infinite mode, the rack at top eventually replaces the bottom picker.** Player taps a tile in the top rack to commit.

### CTRL trading (plan only, no code)
- Full systematic engine, paper-only until existing 30-day + 55% win-rate gate flips.
- Caps: 25% per strategy, 5% per position, min 10 trades before allocator trusts.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None new. Used existing endpoints:
- `GET /api/projects/flat` — read projects table
- `POST /api/projects/` — create CrunchBall record
- `PATCH /api/projects/:id` — update CrunchBall description/tagline/brandName

---

## DATABASE CHANGES THIS SESSION

- **Added one row to the projects table:** `crunchball` (parentId: `mobile-games`, status: `planning`, shortCode: `CR`).

No schema changes.

---

## CTRL TRADING — FULL SYSTEMATIC ENGINE PHASE PLAN (uncommitted)

For future implementation. Caps confirmed: max 25% capital per strategy, max 5% per position, min 10 trades before allocator trusts.

1. **Data model.** Add to `trading_strategies`: `status` (proposed|active|paused|retired), `discovered_by`, `signal_definition` (JSON), `auto_weight`, `manual_weight`, `current_weight`, `max_position_pct`, `min_sample_size`, `created_at`. New tables: `trading_strategy_trades`, `trading_signals`.
2. **Trade attribution.** Order placement accepts optional `strategy_id`. Position-close hook writes to `trading_strategy_trades`, increments parent counters. Trade Log UI gets a strategy column.
3. **Discovery routine.** Uses existing routine framework + Claude + Perplexity. Inserts proposed strategies. Posts to Discord `#ctrl-trading`.
4. **Capital allocator.** Nightly. Score = win_rate × avg_return × recency_decay. Min-sample → small fixed seed. Hard cap 25%. Respects `auto_weight=0`.
5. **Executor.** Cron during market hours. Reads pending signals from enabled strategies. Sizes by `equity × current_weight × confidence`, capped at `max_position_pct`. Places via Alpaca. Marks signal executed. Existing live-trading gate stays.
6. **UI.** Sortable columns, allocation bar chart, approve/reject for proposed, edit modal for signal_definition + caps, new SignalsTab.

---

## CRUNCHBALL — TASK STATE IN GOOGLE TASKS

All in default list (johnbenjaminroberts's list), prefixed `[CrunchBall]`:

**Done:**
- Decide final project name → resolved: **CrunchBall**.

**Open:**
- Initialise React + Vite + TypeScript prototype in app/ ← **next**
- P1.1 Grid rendering (isometric, 11×18, touch pan/zoom)
- P1.2 Character placement + movement
- P1.3 Tackle zone visualisation
- P1.4 Radial action menu
- P1.5 Ball mechanics (pickup, carry, loose ball bounce)
- P1.6 Block system + block dice
- P1.7 Pass system + deviation
- P1.8 Turnover detection
- P1.9 Injury system
- P1.10 Kick-off sequence + random events
- P1.11 Turn timer (60s)
- P1.12 Rookie AI
- P1.13 Win/loss/TD detection
- P1.14 Post-match injury report

Each task has notes pointing to relevant GDD section.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **CrunchBall is the active thread.** Vite init is the next command. App folder is empty — anything you create there is fresh.
- **GDD is the source of truth** for CrunchBall. Read `CRUNCHBALL-GDD.md` before making any design decision. Hard design rules in §20 are non-negotiable.
- **Original design handover** is `GRIDBRAWL_HANDOVER.md` in the same folder — keep it as the historical source artifact, not as a working doc.
- **Project-switching skill `/project-crunchball` is live** — invoking it loads CrunchBall context.
- **WordDrop emulator and Metro were left running** at session end (port 8082, emulator-5554 connected, Daily Puzzle rack visible). Probably stopped by now or will be cleared if the machine restarted.
- **Don't waste time on RN/Metro build setup on Windows** — captured in `WordDrop/LEARNINGS.md`. Key gotchas: `gradlew.bat` lives in `android/` not project root; `adb reverse tcp:8081 tcp:8082` required because the installed debug build defaults to 8081; uninstall app before reinstalling if "insufficient storage".
- **CTRL trading discussion is parked** — the 6-phase plan in this doc is the authoritative reference until it gets implemented or properly tracked.
- **MCP task creation can duplicate.** Always list tasks after a batch create to verify count.

---

## HOW TO START THE SYSTEM

CTRL:
```
D:\AI Work\START-ALL.bat
```
Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

CrunchBall (once Vite is initialised):
```
cd D:\AI Work\Mobile-Games\games\crunchball\app
npm run dev
# default http://localhost:5173
```

WordDrop (active in WordDrop sessions):
```
cd D:\AI Work\Mobile-Games\games\worddrop\WordDrop
npm start -- --port 8082 --reset-cache
# in another terminal:
adb reverse tcp:8081 tcp:8082
adb reverse tcp:8082 tcp:8082
adb shell am start -n com.worddrop/.MainActivity
```

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CrunchBall** — active. Tactical sports brawler. Real-time turn-based. Unity production, React prototype. GDD locked. 14 Phase 1 tasks open.
- **WordDrop** — active. Scrabble-style tile drop. Daily Puzzle rack done. Infinite mode rack still placeholder.
- **BatonDrop** — established. Drop-reflex casual game. Not touched this session.
- **CTRLPro / UnifyBI** — hospitality SaaS, planning. First client conversation pending.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video.
- **Cavernborn** — dark fantasy idle RPG, RN not yet initialised.
- **Homeland** — planning.
- **Chess Music** — concept.
