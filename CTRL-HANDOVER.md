# CTRL Project Handover
*Last updated: 2026-05-24 17:35 UTC*
*Session ended: After building Stack Attack release APK v0.13 (per-world bar geometry + world bg in game).*

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

**HEADS UP:** This session was 100% on the **Stack Attack** mobile game (one of
the CTRL Play mobile titles), not the CTRL Control-Centre app itself. No CTRL
code was touched. Sections below for CTRL reflect the LAST known state from
STATUS.md (26 Apr 2026), not anything that changed this session.

---

## WHAT WE WERE BUILDING THIS SESSION

Per-world theming for the Stack Attack game screen. Three jobs:

1. **GameScreen background per world** — `<ScreenWrapper>` already supported
   a `worldId` prop and rendered the world's background image, but GameScreen
   wasn't passing it. Now derives `world` from `levelId` (50-slot stride) and
   threads it through.

2. **StackBlock bar shape per world** — block bars were all the same I-beam
   girder with a per-world recolour. User wanted distinct shapes: "we have
   girders for construction, we need other themed bars". Refactored into a
   dispatcher over six per-world body components, each with its own Skia
   geometry (girder/stone/neon strip/metal panel/crystal/magma-vein basalt).

3. **Release APK v0.13 built** — arm64-v8a + x86_64 (emulator + phone),
   139 MB, at `D:\AI Work\Mobile-Games\games\stackattack\builds\stackattack-v0.13-world-themed-bars.apk`.

Verified live on the emulator: Level 251 (Chaos world) renders Chaos bg +
magma-vein basalt bars + "CHAOS" subtitle. Done.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- [Stack Attack] GameScreen per-world background + StackBlock world prop wired through
- [Stack Attack] StackBlock refactored to 6 per-world Skia body components
- [Stack Attack] Header subtitle now reads from `getWorldTheme(world).worldName` instead of hardcoded `CONSTRUCTION`
- [Stack Attack] Release APK `stackattack-v0.13-world-themed-bars.apk` built (arm64+x86_64, 139 MB)
- [Stack Attack] SESSION_STATE.md, LEARNINGS.md, skill-stackattack-screen-polish.md (addendum), and memory project_stackattack.md all updated

### In progress right now
Nothing — all three deliverables shipped and verified.

### Pending / next steps (Stack Attack)
1. **Generate SA world picker icons** — batch JSON ready at
   `D:\AI Work\Mobile-Games\skills\batches\stackattack-world-icons.json` (6 prompts
   for Build/Ruins/Arcade/Space/Multiverse/Chaos). User needs to run through CTRL
   Design module, then drop PNGs into `app/src/assets/world-icons/`. Then wire into
   `app/src/components/WorldSelector.tsx` the same way Colour Flood was.
2. **LevelCard BLUEPRINT image rendering** — on the Chaos LevelSelect screen the
   level cards show stars + numbers but the BLUEPRINT image asset appears
   transparent. Numbers + stars are still legible against the world bg so not
   blocking, but check `app/src/assets/level-select/blueprint.png` path/load.
3. **W6 selector counter mismatch** — pill shows `150/120` (more stars than max).
   Same `getWorldStars` vs `visibleCount` mismatch fixed in CF — apply the same
   `visibleCount` param fix in `app/src/screens/LevelSelectScreen.tsx`.

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
D:/AI Work/Mobile-Games/games/stackattack/app/src/screens/GameScreen.tsx
  - Imported getWorldTheme + WorldId from useWorldTheme
  - Derived `world` from `levelId` and `worldName` from `getWorldTheme(world)`
  - Wrapped with `<ScreenWrapper worldId={world}>`
  - Passed `world={world}` to slider and tower StackBlock usages
  - TowerBlockView signature gained `world: WorldId` prop
  - Header subtitle changed from `CONSTRUCTION` to `{worldName.toUpperCase()}`

D:/AI Work/Mobile-Games/games/stackattack/app/src/components/StackBlock.tsx
  - Full rewrite: dispatcher of 6 per-world body components
  - <ConstructionBody>, <RuinsBody>, <ArcadeBody>, <SpaceBody>,
    <MultiverseBody>, <ChaosBody>
  - WorldScheme `stripe` field renamed `accent` for neutrality
  - Bonus blocks hardcoded to W1 girder regardless of world

D:/AI Work/Mobile-Games/games/stackattack/builds/stackattack-v0.13-world-themed-bars.apk
  - NEW release build, 139 MB, arm64-v8a + x86_64

D:/AI Work/Mobile-Games/games/stackattack/SESSION_STATE.md
  - Overwritten with current state

D:/AI Work/Mobile-Games/games/stackattack/LEARNINGS.md
  - 6 new entries (GameScreen world derivation, StackBlock dispatcher pattern,
    bonus blocks stay W1, adb uiautomator flakiness, screenshot coord scanning,
    Metro --reset-cache cold time)

D:/AI Work/Mobile-Games/games/stackattack/skill-stackattack-screen-polish.md
  - New addendum: "Per-world bar geometry (2026-05-24)" — pattern, WorldScheme
    shape, how to add a new world, wiring from GameScreen

C:/Users/admin/.claude/projects/d--AI-Work/memory/project_stackattack.md
  - Updated description + body to reflect 2026-05-24 work
```

---

## RECENT GIT COMMITS

CTRL Control-Centre is **not a git repository** (no `.git` directory). No commits
to report from CTRL itself. Stack Attack also not in the project_repos table —
it's archived via direct file copy to `builds/` per-version, not via git.

batondrop was the only linked repo with new changes — auto-pushed during Step 0.5
of this /afk run.

---

## OPEN ISSUES / KNOWN BUGS

### Stack Attack
- LevelCard BLUEPRINT image appears transparent on Chaos LevelSelect screen
- W6 world-selector pill shows `150/120` (count > max) — `getWorldStars` uses
  full 50-level engine range while max uses visible `levelCount` (40 for W6)
- 8 pre-existing TS errors (not from this session, not blocking Metro/runtime):
  - `ScreenWrapper.absoluteFillObject` (should be `absoluteFill`)
  - `tokens.quart` (no such Easing property)
  - `RouteProp` imported from `@react-navigation/native-stack` (lives in `/native`)
  - `DEFAULT_CONFIG` not exported from engine/game
  - 2x `tabBarStyle` on stack screens (only valid on tab screens)
- `adb shell uiautomator dump` returns "failed to stat" on this emulator —
  workaround used: `adb exec-out screencap -p` then sharp pixel scan for orange
  button centres

### Environment
- Background `uiautomator` FATAL exceptions on emulator (rapid-fire, ephemeral
  PIDs) — unrelated to SA, some other tool is spamming `uiautomator dump`

---

## KEY DECISIONS MADE THIS SESSION

- **Per-world bar SHAPES, not just recolours** — each world body gets its own
  Skia geometry. Adding a new world = add one `<XxxBody>` component + one
  dispatcher line. No changes to GameScreen needed.
- **Bonus block stays as the W1 gold girder** across all worlds so it reads
  as "golden version of the familiar shape" rather than mutating per world.
- **Release APK includes arm64-v8a + x86_64** — emulator-first testing rule
  (locked 2026-05-23) requires x86_64; arm64 stays for phone testing.
- **WorldScheme.stripe renamed to WorldScheme.accent** — neutral name since
  each world uses it differently (hazard stripes / mortar / neon / LED / spine / magma).

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None — no CTRL backend work this session.

---

## DATABASE CHANGES THIS SESSION

None — no CTRL DB work this session.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Stack Attack is the active project.** If you walk in to "continue from
  last session" without context, default to opening
  `D:\AI Work\Mobile-Games\games\stackattack\SESSION_STATE.md` first.
- **Test loop is emulator-first** (rule locked 2026-05-23). Release APK
  includes x86_64 for that reason — don't strip it for size.
- **Metro is on port 8081** for Stack Attack (CF uses 8081 too — they conflict
  if both run at once). When switching games, kill Metro first.
- **Coord-finding via screenshot + sharp** works reliably when `uiautomator
  dump` is broken. Pattern: `adb exec-out screencap -p > tmp.png`, then
  `node -e "sharp(...).raw().toBuffer..."` with RGB threshold for the button
  colour. Confirmed working this session.
- **adb tap coords are NATIVE pixels (1080x2400)** — chat screenshot display
  size is misleading. Always use sharp to find the button centre before
  tapping.
- **Don't trust the chat's "displayed at 900x2000 multiply by 1.20" hint**
  blindly — multiple wrong-Y taps this session before pivoting to sharp.
- **batondrop was auto-pushed to GitHub** during this /afk. Other "linked"
  projects in the DB aren't git repos so were silently skipped — that's
  expected behaviour.

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

```
# 1. Install latest APK on emulator (or phone)
adb install -r "D:/AI Work/Mobile-Games/games/stackattack/builds/stackattack-v0.13-world-themed-bars.apk"

# 2. Or run from source with Metro hot reload
cd "D:/AI Work/Mobile-Games/games/stackattack/app"
npx react-native start --reset-cache    # Metro on 8081
# in another shell:
adb shell am force-stop com.stackattack
adb shell monkey -p com.stackattack -c android.intent.category.LAUNCHER 1
```

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLPro** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, website live at bedbouncer.co.uk, Kickstarter prep
- **Mobile Games** — multiple titles in flight:
  - **BatonDrop** — v2.0.0 in Google Play production review
  - **WordDrop** — GDD + brand done, build pending
  - **Cavernborn** — dark fantasy idle RPG, RN app not yet initialised
  - **Stack Attack** — RN 0.85 app, v0.13 built today (this session)
  - **Colour Flood** — RN 0.85.2 app, v0.17 last build
  - **Reflex Ring** — scaffolded 2026-05-18, no build yet
  - **Word Chain** — RN 0.85 shell scaffolded, gameplay not wired
