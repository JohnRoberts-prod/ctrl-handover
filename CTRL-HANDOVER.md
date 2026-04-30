# CTRL Project Handover
*Last updated: 2026-04-30 — end of BatonDrop + CTRLplay website session*
*Session ended: Completed CTRLplay website content rewrite — all four HTML files corrected for factual accuracy*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a development session for John Roberts.
John Roberts is a solo developer building mobile games (BatonDrop) and the CTRL personal OS.
Read this entire document before responding.

Primary game project: D:\AI Work\Mobile-Games\games\batondrop
App source: D:\AI Work\Mobile-Games\games\batondrop\app\src
CTRLplay website: D:\AI Work\Mobile-Games\CTRLplay website

CTRL codebase (separate system): D:\AI Work\Control-Centre
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Database: SQLite at D:\AI Work\.ctrl-data.db

---

## WHAT WE WERE BUILDING THIS SESSION

This was a BatonDrop mobile game + CTRLplay website session covering ten distinct areas.
The final task (completed) was rewriting all CTRLplay website content to be factually accurate.
The original site had wrong baton descriptions, listed ChessMusic as a CTRLplay game, referenced a
leaderboard that does not exist yet, and showed a wrong Founded year.

Before the website work, the session fixed several game bugs: a swap baton double-offset visual bug,
a freeze+swap crash, the react-native-sound Android audio path (res/raw/ not assets/), and added a
level selector to the debug modal.

---

## COMPLETED THIS SESSION

### BatonDrop App

- Music: Removed 3 old track references (brain_dance, cloud_dancer, equatorial_complex). Single track: defaultsound. Cleaned from MusicPlayer, ThemeContext, themes.ts, OptionsScreen.
- Audio fix: defaultsound.mp3 copied to android/app/src/main/res/raw/ (react-native-sound with null basePath reads res/raw/ via getIdentifier(), NOT assets/ on Android)
- build-phone.ps1: Fixed to not delete .mp3 files from res/raw/ during clean step
- Level selector: Added startLevel stepper (down/up arrows, default 1, max 30) to DebugOptionsModal. Field added to DebugConfig type and LevelSystem.initialLevelState().
- Swap baton fix: handleSwap in GameScreen no longer calls setActiveBaton. This was causing laneX prop to change mid-animation causing double-offset. Fix: only call engine.updateBatonLane + setFallingLanes.
- Freeze+swap fix: pickDifferentLane() now takes forbidden[] list. When freeze is active, forbidden = all lanes except frozen lane. Falls back to same lane if no candidates. Also fixed hardcoded boxCount bug in BatonSpawner normal mode.
- Persistence contract: Added PERSISTENCE_CONTRACT.md. HMAC mismatch now recovers value instead of resetting coins to 0. Storage keys marked with warning comments.
- FallingBaton swap animation: Corrected to proper 3-phase (fall to pause point, slide X, resume to arrivalY, then exit). isSwap guard checks swapToBoxIndex !== targetBoxIndex so same-lane fallback runs as standard baton.

### CTRLplay Website — D:\AI Work\Mobile-Games\CTRLplay website

- index.html: Removed ChessMusic card entirely. Catalogue 03 to 02. Three games to Two games. WordDrop badge In dev to Soon, button Early stage to Coming soon, index 03 to 02. BatonDrop description fixed (removed "in sequence"). Founded 2025 to 2026.
- games/batondrop.html: Corrected 6 wrong baton descriptions:
  - Ghost: "phases between lanes" was wrong. Now: fades to invisible before catch zone.
  - Bouncer: "hits a wall, jumps lanes" was wrong. Now: falls, bounces above pads, springs up, drops through catch zone. Tapping early is a miss.
  - Fast Forward: "next batons inherit tempo" was wrong. Now: this baton falls at 1.5x speed only.
  - Slow Motion: "clear a backlog" was misleading. Now: falls at half speed, gift baton.
  - Swap: "pads rearrange, lane 1 where lane 4 was" was wrong. Now: slides sideways to new lane mid-fall. Tap where it ends up.
  - Double: "two batons fall together" was wrong. Now: catch it for 2x score on this hit.
  - Magnet: "pulls toward last-tapped lane" was wrong. Now: upgrades hit quality (ok becomes good, good becomes perfect).
  - Freeze: "baton freezes mid-air, tap when it thaws" was wrong. Now: catch it and next 5 drops all land in same lane.
  - Spec Internet row: removed "global leaderboards" (not built yet)
  - Spec Status: changed to "In development. Early release."
- contact.html: Removed ChessMusic row. WordDrop In dev to Soon.
- privacy.html: Removed ChessMusic from scope. Removed all leaderboard references (collection bullet, use bullet, Supabase row, retention paragraph, delete rights clause).

---

## IN PROGRESS

Nothing. Session ended cleanly after website content rewrite.

---

## NEXT STEPS FOR BATONDROP

1. Verify CTRLplay website in browser — check 2-game grid layout on index.html looks correct
2. HowToPlayScreen on device — verify batons render at 54x200 with resizeMode=cover (was unconfirmed from previous session)
3. Full QA pass across all screens
4. First Play Store internal test build
5. Google Play store listing + privacy policy URL (must match website URL)
6. Global leaderboard — discussed but not built. Requires backend (Supabase), score submission API, leaderboard UI in GameOverScreen.

---

## KEY TECHNICAL FACTS — BATONDROP

Stack: React Native 0.85 + React 19 + TypeScript strict + react-native-reanimated 4.x

react-native-sound Android: null basePath reads from res/raw/ via getIdentifier(). File must be at android/app/src/main/res/raw/[name].mp3. The assets/ folder does NOT work with null basePath.

Swap baton animation rule: laneX prop is captured at mount for translateX delta. NEVER update activeBaton.targetBoxIndex in GameScreen — it changes laneX prop mid-animation, double-offsetting the baton. Only call engine.updateBatonLane() and setFallingLanes() in handleSwap.

Freeze+swap: buildBaton(level, targetBoxIndex, frozenLane) passes frozenLane through to pickDifferentLane(exclude, boxCount, forbidden). If frozen, forbidden = all lanes except frozenLane. Falls back to same lane if no valid candidates (no visible swap).

Persistence contract: Storage keys in SaveService, SettingsService, ThemeStore, ProgressionService are FROZEN. Never rename them — player data will be lost on update. See PERSISTENCE_CONTRACT.md in app root.

HMAC mismatch recovery: IntegrityService.getCoinBalance() recovers the raw stored value and re-signs it on keychain loss. Does NOT reset to 0.

Baton fall speeds (L1 base 2000ms):
- Standard: 2000ms
- Fast Forward: Math.round(baseFall / 1.5) = approximately 1333ms
- Slow Motion: Math.round(baseFall * 2) = 4000ms

Special baton pools:
- Common (all levels): ghost, bomb, fast_forward, slow_motion, bouncer
- Regular (level 10+): swap, double, magnet, shrink, freeze
- Rare (level 20+): mirror, blind

Box count per level: 2 boxes at L1-4, 3 at L5-9, 4 at L10+

---

## CTRL SYSTEM STATE (separate from BatonDrop)

CTRL is John's personal business OS — React + Express + SQLite, runs locally on port 5173/3001.
Status as of last CTRL session (26 April 2026):

Project Spine COMPLETE (phases 1-5). All DB migrations applied (24-27).
ProjectSelector wired into Projects, Finance, GitHub tabs.
Phase 6 (browser verify + remaining tab wiring) still pending.

Tabs working: Gmail, Claude terminal, Home (basic), Finance (basic), Trading (Alpaca live), Projects (spine)
Tabs pending wire-up: Knowledge, CRM, Brand Toolkit, Cloudflare

---

## FILES MODIFIED THIS SESSION

BatonDrop app:
- app/src/services/MusicPlayer.ts — DEFAULT_TRACKS to defaultsound only, removed TRACK_DISPLAY_NAMES export
- app/src/contexts/ThemeContext.tsx — fallback musicTracks: defaultsound
- app/src/config/themes.ts — MUSIC_ASSETS.default: defaultsound
- app/src/screens/OptionsScreen.tsx — removed entire MUSIC TRACK section and all related styles
- android/app/src/main/res/raw/defaultsound.mp3 — NEW: copied from assets/ for react-native-sound
- app/build-phone.ps1 — fixed raw dir clean to preserve .mp3 files
- app/src/types/index.ts — added startLevel: number to DebugConfig
- app/src/engine/LevelSystem.ts — initialLevelState(level = 1) accepts optional start level
- app/src/engine/GameEngine.ts — uses debugConfig?.startLevel ?? 1; loadLane passes frozenLane
- app/src/screens/GameScreen.tsx — handleSwap removed setActiveBaton call; startGame uses startLevel
- app/src/components/DebugOptionsModal.tsx — added startLevel stepper (arrows, 1-30)
- app/src/components/FallingBaton.tsx — swap animation corrected (3-phase); isSwap guard added
- app/src/engine/BatonSpawner.ts — pickDifferentLane with forbidden[]; boxCountForLevel(level) throughout; frozenLane param
- app/src/security/IntegrityService.ts — HMAC mismatch recovers value, re-signs (not reset to 0)
- app/PERSISTENCE_CONTRACT.md — NEW: documents all frozen storage keys

CTRLplay website:
- CTRLplay website/index.html — removed ChessMusic, fixed counts, WordDrop status, Founded year
- CTRLplay website/games/batondrop.html — corrected 6 baton descriptions, fixed spec table
- CTRLplay website/contact.html — removed ChessMusic, WordDrop status fix
- CTRLplay website/privacy.html — removed ChessMusic, removed all leaderboard references

---

## OPEN ISSUES

- HowToPlayScreen baton rendering — resizeMode=cover fix applied but not confirmed on device
- Global leaderboard — discussed but not built (no backend, no UI)
- Google Play listing not created yet — website is ready, Play Store alpha track is next step
- ChessMusic CSS variables still in batondrop.html and index.html (dead code, harmless)

---

## KEY DECISIONS MADE THIS SESSION

- react-native-sound: use res/raw/ on Android, not assets/ — confirmed by tracing Android Kotlin source
- Swap baton: never update laneX-contributing props mid-animation (no setActiveBaton in handleSwap)
- Freeze+swap: forbidden lane list passed to pickDifferentLane — cleaner than changing engine freeze logic
- HMAC recovery: always recover + re-sign on keychain loss, never default to 0
- CTRLplay website: ChessMusic is NOT a CTRLplay game — removed from all pages
- Leaderboard: not built yet — removed from all marketing and privacy copy until actually shipped

---

## HOW TO START THE SYSTEMS

BatonDrop (React Native):
  cd D:\AI Work\Mobile-Games\games\batondrop\app
  npx react-native start   (Metro bundler)
  In another terminal: cd android && ./gradlew app:installDebug

Phone build (physical device connected via USB):
  cd D:\AI Work\Mobile-Games\games\batondrop\app
  powershell -ExecutionPolicy Bypass -File build-phone.ps1

CTRL system:
  D:\AI Work\START-ALL.bat

---

## OTHER PROJECTS (for full context)

- CTRLplay — game studio brand. Website at D:\AI Work\Mobile-Games\CTRLplay website. BatonDrop is live (alpha), WordDrop coming soon.
- CTRL — personal business OS. Active build. Last session: Project Spine complete, remaining tab wiring pending.
- CTRLPro — hospitality SaaS. Planning phase. One potential first client.
- BedBouncer — smart alarm Kickstarter. Working prototype exists. Needs product video to relaunch.
