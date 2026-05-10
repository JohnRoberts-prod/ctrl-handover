# WordDrop / CTRL Session Handover
*Last updated: 2026-05-10 (Mobile-Games / WordDrop)*
*Session ended: just shipped Daily Puzzle v1 with the Scrabble-rack design — user invoked /afk after the rack pts fix landed on emulator*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up the WordDrop build session.
John Roberts is the developer. Read this entire document before responding.

**This session was about WordDrop, not CTRL.** WordDrop is a mobile word game (Tetris-meets-Scrabble) being built under `D:\AI Work\Mobile-Games\games\worddrop\`. CTRL is John's separate productivity-stack project at `D:\AI Work\Control-Centre\` — untouched this session.

WordDrop tech:
- React Native 0.85 + TypeScript
- Metro port 8082 (8081 is BatonDrop)
- Premium SVG outlined text via `react-native-svg` (NO Skia)
- Bundled font: Luckiest Guy at `android/app/src/main/assets/fonts/LuckiestGuy-Regular.ttf`
- JSON persistence via `react-native-fs` (AsyncStorage NOT installed)
- Release APK ~70MB; debug ~179MB (debug often won't fit on the emulator)

---

## WHAT WE WERE BUILDING THIS SESSION

Multi-day push to make WordDrop v1.1 — the new direction per `WORDDROP-HANDOVER.md`. By the time /afk was called we had:

1. Fixed 7 Endless-mode bugs (collision detection, fall animation, ghost piece spawn, fall speed × 2, CHOOSE COLUMN headline, gold/pulse arrows + haptic, etc.).
2. Built the daily-challenges engine, library persistence, and skip-letter power-up state.
3. Done a full premium design pass — DisplayText component + redesigned MainMenu, GameOver, HUD, DailyChallenges and WordLibrary screens.
4. Built **Daily Puzzle mode v1** from scratch — new screen, new state machine, new sized-by-param engine, new wood-framed PuzzleBoard with a Scrabble-style rack at the top.
5. Iterated the puzzle UX in response to user feedback: slowed pacing, added 'between' pause, capped board size, added rack metaphor.

The last working state has the **rack design live and rendering correctly on emulator** — tiles A/T/E sitting in a wooden holder, active E falling into column 1 of a 3×3 board, points showing as 1.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- **Phase 1 — Endless mode bug fixes**: collision detection (`useGame.tryMove`), fall animation re-aim (`GameBoard.FallingTileAnimated`), ghost piece spawn-to-centre on `PICK_LETTER`, fall speed doubled, CHOOSE COLUMN headline below board, gold pulsing arrow buttons with Vibration haptic.
- **Phase 3 — daily challenges**: 16-challenge pool, deterministic per-day shuffle picks 5, per-player storage, modifiers feed `extraLetters` + `skipCharges` back into the game.
- **Phase 5 — word library**: unique-word store at `word_library.json`, deduped by uppercase, best score + last-seen.
- **Design pass**: `src/components/DisplayText.tsx` (SVG outlined, 13 variants, Luckiest Guy font). Redesigned MainMenu (massive WORD DROP logo + single PLAY + three icon-chips), GameOver (96pt score reveal + compact 4-stat strip + two CTAs), Gameplay HUD (DisplayText for score/level/combo), DailyChallenges (kill eyebrows + big numbers), WordLibrary (3-stat hero strip + rare-letter tints).
- **Phase 4 v1 — Daily Puzzle mode**: `src/screens/DailyPuzzle.tsx` + `src/hooks/usePuzzleGame.ts` + `src/components/PuzzleBoard.tsx` + `src/game/puzzle.ts` + `src/game/puzzleEngine.ts`. 3×3 board, Scrabble rack with N upcoming tiles, no DROP button, auto-clear on last letter, perfect-clear detection, retry overlay.
- **Crash fix**: added `VIBRATE` permission to `AndroidManifest.xml` + try/catch around `Vibration.vibrate(10)` — release build was crashing on tile move.
- **Menu chip rename**: DAILY -> PUZZLE so users can find the new mode.

### In progress right now
- **Rack animation polish**: tiles currently SNAP to new contents when one drops; user accepted v1 without the slide animation but it's the obvious next polish step. Each rack slot needs an `Animated.Value` for `translateX` that animates left when contents shift, with the rightmost slot fading in.
- **Daily Puzzle is shipping with one bundled seed** (CAT/ARE/TEA) by user instruction. `src/game/puzzle.ts:PUZZLE_POOL_3X3` is a one-item array. Date picker is in place so adding more seeds = automatic rotation.

### Pending / next steps
1. Slide animation for rack contents on `LOCK_FALLING` (visual polish).
2. Word-clear animation pass — currently only a gold glow on cells about to clear.
3. Decide: more 3×3 seeds, or jump to 4×4..7×7? `puzzleEngine.ts` is already size-parameterised.
4. **Phase 2 (deferred)** — Cloudflare Worker `word-drop-api` for real leaderboard. Source needs writing; deploy needs John's Cloudflare auth.
5. **Daily Challenges screen is orphan** — engine works, no menu entry. Decide where it lives (settings? separate chip?).
6. **`com.worddrop` developer verification** in Play Console still pending (admin task).
7. **12+ closed-test testers** needed — 14-day clock running since the placeholder v1.0 shipped earlier in the session.

---

## DAILY PUZZLE MODE — HOW IT WORKS

This is the new MAIN game mode. Replaces the original Daily Challenges spot in the menu.

**Flow:**
- Player taps PUZZLE chip on MainMenu.
- Screen loads today's seed via `getTodayPuzzle()` (date-seeded picker over `PUZZLE_POOL_3X3`).
- Wood-framed 3×3 board renders with a Scrabble rack at the top (1 preview row).
- Rack shows the next N upcoming letters as actual Scrabble tiles in a wooden holder.
- One tile drops from the rack into the playable area (fall duration 5000ms).
- Player moves with left/right arrow buttons. NO DROP button.
- Tile auto-locks when fall timer fires. 'Between' phase pauses for 900ms.
- Rack contents shift left, new tile appears at the rightmost slot. Next falls.
- When sequence is exhausted (9 letters for 3×3), phase -> 'auto_clearing' -> `scanPuzzleWords` -> `clearPuzzleWords`.
- Result overlay: PERFECT CLEAR + score + cleared words, or TRY AGAIN + remaining count. RETRY restarts same seed.
- Daily progress (attempts, bestScore, perfectCleared) persisted in `daily_puzzle_progress.json`. Resets midnight UTC.

**State machine** (`src/hooks/usePuzzleGame.ts`):
```
falling --LOCK_FALLING--> between --SPAWN_NEXT--> falling
                                                      |
                                               last letter?
                                                      v
                                              auto_clearing --AUTO_CLEAR--> done
```

**Rack computation in DailyPuzzle.tsx:**
- 'falling' phase: `rack = sequence.slice(seqIndex+1, seqIndex+1+size)`
- 'between' phase: `rack = sequence.slice(seqIndex, seqIndex+size)`  (next-to-fall sits in slot 0)
- 'auto_clearing'/'done': rack is all nulls

---

## FILES CREATED OR MODIFIED THIS SESSION

(All paths relative to `D:\AI Work\Mobile-Games\games\worddrop\WordDrop\`)

```
android/app/src/main/AndroidManifest.xml — added VIBRATE permission
android/app/src/main/assets/fonts/LuckiestGuy-Regular.ttf — copied from BatonDrop
src/assets/fonts/LuckiestGuy-Regular.ttf — copied from BatonDrop (asset link path)
src/theme.ts — added 'display' font field (LuckiestGuy-Regular)

src/game/engine.ts — UNCHANGED (Endless mode still 5×5)
src/game/storage.ts — NEW. JSON-on-disk helpers via react-native-fs.
src/game/challenges.ts — NEW. 16-challenge pool, daily picker, progress, modifiers.
src/game/library.ts — NEW. Unique word library store.
src/game/puzzle.ts — NEW. Seed pool (1 seed CAT/ARE/TEA), date-seeded picker, per-day progress.
src/game/puzzleEngine.ts — NEW. Sized-by-param helpers (emptyPuzzleBoard, puzzleGhostRow, scanPuzzleWords, etc.).

src/hooks/useGame.ts — REWRITTEN. Bug fixes (collision, fall anim, spawn reset) + challenge/library wiring + skip-letter state.
src/hooks/usePuzzleGame.ts — NEW. Daily Puzzle state machine.

src/components/DisplayText.tsx — NEW. SVG outlined display text (13 variants, Luckiest Guy).
src/components/GameBoard.tsx — Bug 1 fix (FallingTileAnimated re-aims on toY change).
src/components/PuzzleBoard.tsx — NEW. Wood-framed board with Scrabble-style rack.
src/components/Tile.tsx — UNCHANGED.

src/screens/MainMenu.tsx — REDESIGN. Massive WORD DROP logo + single PLAY slab + three icon-chips (PUZZLE/RANKS/LIBRARY).
src/screens/Gameplay.tsx — Bug 4/8 (gold pulsing arrows + haptic + CHOOSE COLUMN headline) + DisplayText HUD + skip button.
src/screens/GameOver.tsx — REDESIGN. Dramatic 96pt score + compact stat strip + two CTAs.
src/screens/DailyChallenges.tsx — REDESIGN. Big done/total hero + simplified rows. Wired to getDailyChallenges().
src/screens/WordLibrary.tsx — REDESIGN. Stats hero strip + rare-letter tints. Wired to getLibrary().
src/screens/DailyPuzzle.tsx — NEW. Today's date + 3×3 board + rack + arrows + result overlay.

App.tsx — DAILY route now renders DailyPuzzle (was DailyChallenges).
```

---

## RECENT GIT COMMITS

WordDrop's local git repo has **no commits yet** — everything is staged on the 'main' branch. There's no upstream remote. Treat the entire codebase as "uncommitted draft" for now. `git status` shows all files as `A` or `AM`.

---

## OPEN ISSUES / KNOWN BUGS

1. **No rack slide animation** — tiles snap to new contents on LOCK_FALLING. Functional but not polished.
2. **No word-clear animation** — auto-clear just shows a gold glow on clearing cells then they vanish. Should sparkle/burst.
3. **Daily Challenges screen orphan** — engine works, no menu entry since DAILY chip was repurposed.
4. **Endless mode move-crash fix unverified end-to-end** — VIBRATE permission + try/catch added; user hasn't confirmed it actually fixed it in a full Endless playthrough.
5. **Emulator disk pressure** — 89%+ full. Debug APK install fails. Workflow forced to release builds, which lose Metro hot reload.
6. **Settings + HowToPlay screens** — not touched this session. Likely look out of place against the new design pass.
7. **Leaderboard screen** — not in the design pass scope (user said "all five screens" but meant MainMenu/GameOver/HUD/Daily/Library). Still has the old typography.

---

## KEY DECISIONS MADE THIS SESSION

- **SVG over Skia** for outlined display text. `react-native-svg` is already installed; Skia would mean a new native dep + rebuild risk on a project that already had install-flakiness.
- **Reuse Luckiest Guy from BatonDrop** instead of bundling a new font.
- **Don't touch `engine.ts`** when adding Daily Puzzle — write a parallel `puzzleEngine.ts` with sized-by-param helpers, leave Endless mode's 5×5 hardcoding alone.
- **One bundled seed for v1** (user instruction). The picker handles 1+ pool.
- **One preview row, not two** (user instruction). Originally I built 2; user redesigned to a single Scrabble-rack row.
- **Cap cell size at 130px** so the 3×3 board doesn't sprawl. `min(width-bound, height-bound, 130)`.
- **PUZZLE_FALL_MS = 5000ms + BETWEEN_MS = 900ms + SETTLE_MS = 700ms**. Initial 2400ms was "way too fast" per user.
- **DAILY chip renamed to PUZZLE** — user couldn't find the new mode under the old label.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None — WordDrop is fully client-local right now. Phase 2 (Cloudflare Worker `word-drop-api`) is deferred.

---

## DATABASE CHANGES THIS SESSION

None on the CTRL side. WordDrop uses JSON file persistence via `react-native-fs`:
- `daily_challenges.json` — { date, challenges[] }
- `daily_puzzle_progress.json` — { date, attempts, bestScore, perfectCleared }
- `word_library.json` — LibraryWord[]

All stored under React Native's `DocumentDirectoryPath`.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Endless mode currently uses a 5×5 board with a pick-from-5 letter mechanic.** This contradicts the GDD's stated 7×12 falling-letter design. The handover bug list was originally written assuming 7×12, but on inspection most bugs DID still apply to the 5×5 pick-then-fall implementation. Don't refactor to 7×12 without an explicit user decision.
- **Daily Puzzle is the new MAIN mode** per WORDDROP-HANDOVER.md. The DAILY (now PUZZLE) chip on the menu goes here. Endless is now secondary.
- **The challenge engine is wired into useGame already** — DROP events call `recordDrop` and `recordWords`; game-end calls `recordGameEnd`. Skip charges + extra-letter modifier are read on START via `modifiersFrom(challenges)`.
- **Vibration crashes without permission** — always add to AndroidManifest when using `Vibration` in RN. Defensive try/catch is cheap.
- **Test on release APK** because debug doesn't fit on emulator. Build with `cd android && ./gradlew assembleRelease`, install with `adb install -r app-release.apk`. Force-stop + relaunch via `adb shell am force-stop com.worddrop && adb shell monkey -p com.worddrop -c android.intent.category.LAUNCHER 1`.
- **Screenshots via adb in Git Bash get path-mangled** — use PowerShell tool or set `MSYS_NO_PATHCONV=1`.

---

## HOW TO START THE SYSTEM

WordDrop development (Android emulator):
```
cd "D:\AI Work\Mobile-Games\games\worddrop\WordDrop"
npm start -- --port 8082               # Metro on 8082
# In another shell:
cd android && ./gradlew installDebug   # OR assembleRelease + adb install
```

Release-only verify loop (when emulator is tight on space):
```
cd android && ./gradlew assembleRelease
adb install -r app/build/outputs/apk/release/app-release.apk
adb shell am force-stop com.worddrop
adb shell monkey -p com.worddrop -c android.intent.category.LAUNCHER 1
```

CTRL itself was not touched this session. To start CTRL: `D:\AI Work\START-ALL.bat`.

---

## PROJECTS OUTSIDE WORDDROP

- **CTRL** — untouched this session. Lives at `D:\AI Work\Control-Centre`.
- **BatonDrop** — released v1.8 with Google sign-in fix earlier; got the GameText premium text system that we later mirrored into WordDrop as DisplayText.
- **Cavernborn** — uninstalled from emulator to free space for WordDrop installs.
- **BedBouncer** — Kickstarter prep, needs product video. Untouched this session.
- **UnifyBI / CTRLPro** — planning. Untouched.
