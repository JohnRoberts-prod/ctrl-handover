# CTRL Project Handover
*Last updated: 2026-05-25 13:40 UTC*
*Session ended: After installing Stack Attack v0.14 (Special Bars Phase 1-3) APK to Pixel 8 Pro, plus a planning discussion about a CTRL installer package (NOT started — explicitly held).*

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

**HEADS UP:** This session was 100% on the **Stack Attack** mobile game (CTRL Play
title), not the CTRL Control-Centre app. No CTRL source code was touched. The
final ~25% of the session was a planning conversation about packaging CTRL as
an installer — that work was scoped but explicitly NOT started ("don't start
anything until I say so"). See the dedicated section below.

---

## WHAT WE WERE BUILDING THIS SESSION

**Stack Attack Special Bars system, Phase 1-3 of the spec** at
`D:\AI Work\Mobile-Games\games\stackattack\skill-stackattack-special-bars.md`.

Six per-world special bars (Ghost Line / Stone Lock / Repair Kit / Mirror /
Steel Beam / Wildcard) that transform the current sliding bar for a single
drop only. Uses earned permanently at L1/L25/L50 of each world (1/2/3 uses).
Restored to earned max at the start of every level attempt.

Shipped: types + persistent gameStore state + UI row + engine landing
modifiers + GameScreen tap dispatcher + Stone-Lock-freeze coordination +
Ghost-Line guide overlay + release APK v0.14 + install to phone.

Skipped this session (per spec build order, Phase 4-6 ~3-5 hours): per-type
bar visual transforms during slide, landing-text overlays, sound, real PNG
icons (using single-character glyphs as placeholders).

---

## CURRENT BUILD STATE

### Recently completed (this session)
- [Stack Attack] `types/specialBars.ts` — 6 configs, unlock helpers, wildcard resolver
- [Stack Attack] gameStore — `specialBarUnlocks` persisted; granted on win via `recordLevelResult`; `debugUnlockAllSpecials` action
- [Stack Attack] `SpecialBarButton.tsx` + `SpecialBarRow.tsx` — 6-button horizontal row with world colours / glyphs / count badges / locked/active states
- [Stack Attack] `engine/game.ts` `dropSlider` — accepts optional `activeBar`, applies per-type landing modifiers
- [Stack Attack] `GameScreen.tsx` — row rendered between header + HUD, tap dispatcher, Stone-Lock freeze coordinated with bounce useEffect, Ghost-Line guide overlay (80px white bar tracking slider X), wildcard resolved via `resolvedBarRef` before engine call
- [Stack Attack] `LevelSelectScreen.tsx` DEBUG button now also calls `debugUnlockAllSpecials()`
- [Stack Attack] **Bug fix**: Stone Lock thaw timeout now also clears `activeBar` so subsequent specials can be activated before drop
- [Stack Attack] Release APK `stackattack-v0.14-special-bars.apk` (139 MB, arm64+x86_64) built and installed to Pixel 8 Pro (serial `38011FDJG00520`)
- [Stack Attack] SESSION_STATE.md, LEARNINGS.md, skill-stackattack-special-bars.md (addendum), and memory project_stackattack.md all updated
- [Discussion] CTRL installer packaging — scoped 3 approaches, agreed on sibling-copy strategy at `D:\AI Work\CTRL-Installer\` but **no work started**

### In progress right now
Nothing — Special Bars Phase 1-3 shipped, APK installed. CTRL installer planning was the last topic and is on hold pending the user's go-signal.

### Pending / next steps

**Stack Attack (high priority):**
1. **Verify untested Special Bars mechanics on phone** — Repair Kit landing widening, Mirror landing widening, Wildcard random resolve fires correct type's effect, counter ×0 disabled state, uses-restore-on-replay, real-win unlock grant
2. **Phase 4 of spec — bar visual transforms during slide** (~3h). Extend `<StackBlock>` with `activeSpecial?` prop + per-type overlay layer. Repair Kit / Mirror / Wildcard currently have no in-flight visual cue.
3. **Phase 5 of spec — landing text overlays** (+15% REPAIRED, MIRRORED, FULL WIDTH!).
4. **Phase 6 of spec — sound design**.
5. **Real PNG icons** for special bar buttons (placeholder glyphs in v0.14).

**CTRL installer (held — awaiting go-signal):**
6. Copy `D:\AI Work\Control-Centre\` → `D:\AI Work\CTRL-Installer\Control-Centre\` (source only, NO node_modules / data files)
7. Fresh `npm install` in the copy
8. Verify it boots in dev mode (expect interesting failures around hardcoded paths)
9. Scope the installer work properly and give a real time estimate
10. Then begin actual installer work (electron-builder / NSIS / Inno Setup choice TBD)

**Carried from previous session:**
11. SA world picker icons — batch at `D:\AI Work\Mobile-Games\skills\batches\stackattack-world-icons.json`
12. W6 selector `150/120` mismatch — apply CF's `visibleCount` fix

---

## ALL MODULES — STATUS (CTRL Control-Centre — NOT touched this session)

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | unchanged | last known: working |
| Claude Tab | src/frontend/src/modules/claude-tab/ | unchanged | last known: working |
| Gmail/Google | src/frontend/src/modules/gmail/ | unchanged | not yet wired to ProjectSelector |
| Tasks | src/frontend/src/modules/tasks/ | unchanged | retiring to Discord per CLAUDE.md |
| Projects | src/frontend/src/modules/projects/ | unchanged | ProjectSelector wired (Apr 26) |
| Finance | src/frontend/src/modules/finance/ | unchanged | ProjectSelector wired (Apr 26) |
| Trading | src/frontend/src/modules/trading/ | unchanged | last known: working |
| GitHub | src/frontend/src/modules/github/ | unchanged | ProjectSelector wired (Apr 26) |
| Cloudflare | src/frontend/src/modules/cloudflare/ | unchanged | not yet wired to ProjectSelector |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | unchanged | not yet wired to ProjectSelector |
| Settings | src/frontend/src/modules/settings/ | unchanged | last known: working |
| Admin | src/frontend/src/modules/admin/ | unchanged | live ProjectsSection (Apr 26) |

Source of truth for CTRL state: `D:\AI Work\Control-Centre\STATUS.md` (26 Apr 2026).

---

## FILES CREATED OR MODIFIED THIS SESSION

```
D:/AI Work/Mobile-Games/games/stackattack/app/src/types/specialBars.ts  [NEW]
  - 6 SpecialBarType configs (ghost_line / stone_lock / repair_kit / mirror / steel_beam / wildcard)
  - SPECIAL_BAR_ORDER, WORLD_TO_SPECIAL maps
  - earnedUsesForLevel(levelInWorld), grantUnlockForCompletion(world, levelInWorld, current)
  - resolveWildcard() random non-wildcard pick

D:/AI Work/Mobile-Games/games/stackattack/app/src/state/gameStore.ts
  - Added specialBarUnlocks: SpecialBarUnlocks to GameState
  - DEFAULT_UNLOCKS as initial state
  - recordLevelResult: calls grantUnlockForCompletion on win (stars > 0)
  - resetAll: clears unlocks
  - debugUnlockAllSpecials action (all to 3 uses)
  - partialize: includes specialBarUnlocks for persistence

D:/AI Work/Mobile-Games/games/stackattack/app/src/components/SpecialBarButton.tsx  [NEW]
  - 48x44 button, world-coloured border, placeholder glyph
  - Count badge top-right (x N)
  - Padlock icon when locked
  - Press scale + active pulse Reanimated animations

D:/AI Work/Mobile-Games/games/stackattack/app/src/components/SpecialBarRow.tsx  [NEW]
  - Horizontal row of 6 buttons centred, 6px gap
  - Renders SpecialBarButton with unlocks/usesRemaining/activeBar/onActivate props

D:/AI Work/Mobile-Games/games/stackattack/app/src/engine/game.ts
  - Imported SpecialBarType
  - dropSlider gained optional activeBar: SpecialBarType | null = null parameter
  - Landing modifiers applied AFTER base placement:
    - ghost_line forces isPerfect = overlap > 0
    - repair_kit adds 15% of slider width, recentered, capped at initialBlockWidth
    - mirror halves width lost, recentered
    - steel_beam enforces 60% min of initialBlockWidth

D:/AI Work/Mobile-Games/games/stackattack/app/src/screens/GameScreen.tsx
  - Added imports: SpecialBarRow, special-bar types, resolveWildcard, configs
  - usesRemaining + activeBar + resolvedBarRef + stoneLockFrozenRef + stoneLockTimeoutRef + stoneLockFrozenTick local state
  - Reset hooks on levelId / specialBarUnlocks change + cleanup on unmount
  - Bounce useEffect skips when stoneLockFrozenRef.current; depends on stoneLockFrozenTick
  - Drop handler reads resolvedBarRef ?? activeBar, passes to dropSlider, clears state after
  - handleSpecialBarTap dispatcher: decrement uses, set activeBar, per-type activation
    (steel_beam resets slider width; stone_lock freezes 2s; wildcard resolves; others sit)
  - <SpecialBarRow> rendered between Zone 1 header and Zone 2 HUD
  - Ghost-line guide line: 80px white horizontal bar above tower top, tracks slider X

D:/AI Work/Mobile-Games/games/stackattack/app/src/screens/LevelSelectScreen.tsx
  - DEBUG button onPress now calls debugStarAllLevels() + debugUnlockAllSpecials()

D:/AI Work/Mobile-Games/games/stackattack/builds/stackattack-v0.14-special-bars.apk  [NEW]
  - 139 MB, arm64-v8a + x86_64, BUILD SUCCESSFUL in 7m 7s

D:/AI Work/Mobile-Games/games/stackattack/SESSION_STATE.md  [REWRITTEN]
D:/AI Work/Mobile-Games/games/stackattack/LEARNINGS.md  [appended 6 entries]
D:/AI Work/Mobile-Games/games/stackattack/skill-stackattack-special-bars.md  [appended IMPLEMENTATION ADDENDUM]
C:/Users/admin/.claude/projects/d--AI-Work/memory/project_stackattack.md  [updated]
```

---

## RECENT GIT COMMITS

CTRL Control-Centre is still **not a git repository**. No commits from CTRL itself.
Stack Attack also not git-tracked — archived via direct file copy to `builds/` per-version.

batondrop was the only linked repo with new changes (none this session) — auto-pushed during Step 0.5 of this /afk run.

---

## OPEN ISSUES / KNOWN BUGS

### Stack Attack (v0.14)
- **Phase 4 of spec deferred** — Repair Kit / Mirror / Wildcard have NO in-flight visual cue during slide. Bar renders as world's normal StackBlock shape. Player sees the effect only on landing (wider block than expected). This is expected per build order, not a bug.
- **Placeholder glyph icons** (G/L/R/M/S/?) on buttons — real PNG icons not yet generated
- **Not yet verified live**: Repair Kit landing math, Mirror landing math, Wildcard random resolve, counter ×0 visual state, uses-restore-on-replay
- 9 pre-existing TS errors carried from previous session (`ScreenWrapper.absoluteFillObject`, `tokens.quart`, `RouteProp` import location, `DEFAULT_CONFIG` export, tabBarStyle on stack screens) — not introduced by this session, not blocking Metro/runtime
- LevelCard BLUEPRINT image appears transparent on Chaos LevelSelect screen (carried; not blocking)
- W6 world-selector pill shows `150/120` (count > max); same getWorldStars vs visibleCount mismatch fixed in CF (carried)

### Environment
- `adb shell uiautomator dump` still returns "failed to stat" on this emulator; workaround used: screenshot + sharp pixel scan
- Background `uiautomator` FATAL exceptions on emulator (unrelated to SA, some other tool spamming uiautomator dump)

---

## KEY DECISIONS MADE THIS SESSION

- **Phase 1-3 only in v0.14** — types + state + UI + engine wiring. Visual transforms / landing text / sound are explicit follow-up phases per the spec's own build order. Lets us validate mechanics before investing in animation polish.
- **Bar visual cues stay minimal in v0.14**: Ghost Line guide line ✓, Steel Beam width change ✓ (size IS the cue), Stone Lock motion stop ✓ (motion stop IS the cue). Repair Kit / Mirror / Wildcard have no in-flight cue — effect visible only on landing. Acceptable for mechanics testing; needs Phase 4 for production feel.
- **Stone Lock thaw auto-clears `activeBar`** (design call not in spec). Reasoning: Stone Lock's entire effect IS the freeze; once thawed nothing's "still active" so player can chain another special before dropping. Important UX fix — without this, freeze-without-drop blocks subsequent specials indefinitely.
- **Bonus blocks always render as W1 girder** regardless of `activeSpecial` (carried from yesterday). Per-special visual transforms will be a SEPARATE overlay concern from the world/bonus theme.
- **Placeholder glyph icons** acceptable for v0.14 — real icons are drop-in replacement when assets land.
- **CTRL installer: sibling-copy strategy** at `D:\AI Work\CTRL-Installer\Control-Centre\`, no in-place edits. Data files (.vault / .ctrl-data.db / .ctrl-config.json) NOT copied — clean-slate first-run. Install target is a separate physical computer, so runtime isolation collapses to "don't touch the live source tree". Plan agreed but execution deferred.

---

## CTRL INSTALLER PLANNING (scoped this session, NOT started)

User goal: package CTRL as an installer that ONLY runs on a separate physical computer.
Live development tree on this machine must stay completely untouched.

### Agreed approach
- Copy `D:\AI Work\Control-Centre\` to `D:\AI Work\CTRL-Installer\Control-Centre\` (sibling)
- Copy source files only; EXCLUDE `node_modules/`, `dist/`, `.git/`
- Do NOT copy data files (`.vault`, `.ctrl-data.db`, `.ctrl-config.json`) — copy assumes zero data on first run
- Fresh `npm install` in the copy
- Verify it boots in dev mode (expect failures from hardcoded paths surfacing — that's a useful first signal of portability)
- THEN scope the installer work properly (installer format, code signing, etc.) and quote a real time estimate
- THEN begin installer work in the copy. Live tree never touched.

### Unknowns I flagged
- **What "installer" means**: Windows `.exe` setup wizard (NSIS / Inno Setup) vs Electron-wrapped desktop app vs portable zip + launcher script — each is hours apart in effort
- **How portable the codebase actually is**: zero-hardcoding rule says it should be portable already; need to grep for `D:\AI Work\`, `JohnRoberts-prod`, `johnbenjaminroberts@gmail.com`, etc. to see what survived
- **Code signing**: Windows SmartScreen warns red on unsigned `.exe` from "unknown publisher" — for personal use OK to click through, for CTRL Personal product later need ~£200/year OV cert
- **Rough estimate** (huge error bars): Smallest viable 4-6h, proper NSIS installer +3-5h, Electron-wrapped with auto-update another 6-10h

### Status
**HELD** — user explicitly said "don't start anything until I say so". Next session: ask for the go-signal, then begin step 1 (the copy).

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None — no CTRL backend work this session.

---

## DATABASE CHANGES THIS SESSION

None — no CTRL DB work this session.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Stack Attack is the active mobile project.** Open `D:\AI Work\Mobile-Games\games\stackattack\SESSION_STATE.md` first.
- **v0.14 is installed on the user's Pixel 8 Pro right now.** They have a working copy with placeholder glyphs and partial visual cues. Real first-impression test of Special Bars happens away from the dev machine.
- **Special Bars mechanics partially verified on emulator only**: Steel Beam, Stone Lock, Ghost Line. Repair Kit / Mirror / Wildcard still unverified live — top of the testing list for next session.
- **CTRL installer is queued but on hold** pending the user's go-signal. Don't start the copy without explicit "go".
- **Test loop is emulator-first** for SA (rule locked 2026-05-23). Release APK includes x86_64 for that reason — don't strip it.
- **Metro on 8081** for SA; CF uses 8081 too — kill Metro before switching games.
- **Coord-finding via screenshot + sharp** works when `uiautomator dump` is broken. Native pixels are 1080x2400 — don't trust chat display dims.
- **batondrop was auto-pushed during this /afk** (no new changes — just a sync push). Other "linked" projects in the DB aren't git repos so silently skipped — expected.
- **CTRL Control-Centre source tree is NOT git-tracked.** When the installer work starts, `git init` will be the first step (per scoped plan in previous /afk handover, also still valid).

---

## HOW TO START THE SYSTEM (CTRL)

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

## HOW TO RESUME STACK ATTACK WORK

```bash
# 1. Latest APK already installed on Pixel 8 Pro
# 2. Run from source with Metro hot reload:
cd "D:/AI Work/Mobile-Games/games/stackattack/app"
npx react-native start --reset-cache    # Metro on 8081
# Other shell:
adb shell am force-stop com.stackattack
adb shell monkey -p com.stackattack -c android.intent.category.LAUNCHER 1

# 3. To test Special Bars cleanly: in LevelSelect, tap the small yellow
#    DEBUG button (top-right) — it stars everything AND maxes special-bar
#    unlocks to 3 of each.
```

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLPro** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, website live at bedbouncer.co.uk, Kickstarter prep
- **Mobile Games** — multiple titles in flight:
  - **BatonDrop** — v2.0.0 in Google Play production review
  - **WordDrop** — GDD + brand done, build pending
  - **Cavernborn** — dark fantasy idle RPG, RN app not yet initialised
  - **Stack Attack** — RN 0.85 app, v0.14 built + installed to phone today (this session)
  - **Colour Flood** — RN 0.85.2 app, v0.17 last build
  - **Reflex Ring** — scaffolded 2026-05-18, no build yet
  - **Word Chain** — RN 0.85 shell scaffolded, gameplay not wired
