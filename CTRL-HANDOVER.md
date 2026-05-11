# CTRL Project Handover
*Last updated: 2026-05-11 ~13:55 UTC*
*Session ended: mid-build on WordDrop's Scrabble tile-holder rack visual. Daily Puzzle's rack is working on the emulator. Gameplay (infinite mode) still has a placeholder floating rack that needs to be integrated into `GameBoard` the same way as `PuzzleBoard`.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a Claude Code session. John Roberts is the developer. Read this entire document before responding. The bulk of this session was on **WordDrop** (a React Native mobile game in `D:\AI Work\Mobile-Games\games\worddrop\WordDrop\`), not on CTRL itself — though there was a sizeable CTRL trading discussion at the start that produced a phased build plan but no code yet.

When John returns he will probably want to either (a) carry on integrating the rack into WordDrop's GameBoard so both game modes look identical, or (b) revisit the CTRL trading "systematic engine" plan.

---

## WHAT WE WERE BUILDING THIS SESSION

Two unrelated workstreams in one session:

1. **CTRL trading tab discussion (no code).** John asked about the Strategies section — why it doesn't show newly discovered strategies, win rates, enable/disable toggles, or performance-based weighting. I flagged the gap: the existing strategies table has *one* seeded row (`momentum-sector`), no discovery engine, no allocator, no trade-to-strategy attribution, and no autonomous executor. John picked **"Full systematic engine"** as the scope. I wrote a phased plan (Phase 1 schema → 2 trade attribution → 3 discovery → 4 allocator → 5 executor → 6 UI). John then asked to capture the phases as CTRL tasks for later; tried to create them in a Google Tasks list called "CTRL" but the list is on a different Google account than the one CTRL/MCP are authenticated against. **The task creation never happened.** Phases are documented below — they need to be turned into tasks (or implemented) at some point.

2. **WordDrop rack visual (the bulk of the session).** Goal: add a Scrabble-style wooden tile holder at the top of the game board for both modes. Built a new `TileRack` component, integrated it into `PuzzleBoard` (Daily Puzzle). Encountered several Windows/RN/Metro build issues along the way. **Daily Puzzle's rack now works** — wooden rim, channel, tiles sitting in the channel, tile falls from the rack into the playable area. **Gameplay (infinite mode) is NOT done** — the rack is currently a floating `<TileRack>` above `<GameBoard>`, not integrated into the wood frame. Next session needs to mirror the PuzzleBoard surgery on GameBoard.

---

## CURRENT BUILD STATE — WORDDROP

### Recently completed this session
- `src/components/TileRack.tsx` — NEW. Wooden Scrabble holder component. **Uses plain Views + `react-native-linear-gradient`**, NOT SVG. (Original SVG version did not render visibly; see learning below.)
- `src/components/PuzzleBoard.tsx` — integrated `<TileRack>` into the rack zone at the top of the board. Added `RACK_RIM_RATIO = 0.45`. Updated `dimsForSize` so `previewH = cell + rimExtra` and `totalRowsEquiv = size + 1 + RACK_RIM_RATIO` in the height-budget formula. Tiles position at `top: rimExtra` (below the wooden rim, in the channel).
- `src/screens/Gameplay.tsx` — added a floating `<TileRack>` above `<GameBoard>` as a placeholder (during PICK phase only). Removed the bottom "rack" view that previously held the picker (PickTimer kept). Added `topRackWrap` style.

### In progress — half-finished
- **GameBoard does not have an integrated rack zone yet.** The floating `<TileRack>` in `Gameplay.tsx` is a stopgap so John could see *something* on screen. To match Daily Puzzle, the rack must be inside the wood `<LinearGradient>` frame, above the play surface, with `cellTop()` offset and the falling-tile entry Y adjusted.

### Pending / next steps (ordered)
1. Mirror `PuzzleBoard`'s rack zone in `GameBoard.tsx`:
   - Increase the surface height by `previewH + dividerH`.
   - Render the rack zone at top:0 of surface, height = previewH.
   - Render `<TileRack width={BOARD_W} height={previewH} topRimHeight={rimExtra} showEndCaps={false} />` inside it.
   - Offset all `cellTop(r)` returns by `+ previewH + dividerH`.
   - Adjust `fallFrom` so falling tiles enter from y=0 of the rack zone (currently `fallFrom = -(CELL_SIZE + CELL_GAP) * 2 - 10`).
2. Decide what populates the GameBoard rack: the 3 picker choices during PICK phase, then empty / falling tile during FALL phase. Pass as a `rack` prop similar to PuzzleBoard's.
3. Remove the floating `<TileRack>` block + `topRackWrap` from `Gameplay.tsx`. Replace with passing the data to GameBoard.
4. Decide whether to keep GameBoard's column-fill indicators (the coloured bars at the top). PuzzleBoard does not have them — to make boards identical, either remove from GameBoard or add to PuzzleBoard. John flagged this as a question.
5. Visually verify falling-tile animation still starts visually from inside the rack on Infinite mode.
6. Re-take screenshots of both modes side by side to confirm parity.

---

## CURRENT BUILD STATE — CTRL TRADING

No code written this session. The plan exists only in chat. **Capture as 6 tasks somewhere John can find them next time.**

### Phase plan (paste into tasks or a tracker)
1. **Data model.** Add columns to `trading_strategies`: `status` (proposed|active|paused|retired), `discovered_by`, `signal_definition` (JSON), `auto_weight`, `manual_weight`, `current_weight`, `max_position_pct`, `min_sample_size`, `created_at`. New tables: `trading_strategy_trades`, `trading_signals`.
2. **Trade attribution.** Order placement accepts optional `strategy_id`. Position-close hook writes to `trading_strategy_trades`, increments parent strategy's win/trade/return counters. Trade Log UI gets a strategy column.
3. **Discovery routine.** Use existing routine framework + Claude + Perplexity. Inserts proposed strategies. Posts to Discord `#ctrl-trading`.
4. **Capital allocator.** Nightly job. Score = win_rate × avg_return × recency_decay. Min-sample threshold gets a small fixed seed allocation. Hard cap 25% per strategy. Respects `auto_weight=0`.
5. **Executor.** Cron during market hours. Reads pending signals from enabled strategies. Sizes by `account_equity × current_weight × signal_confidence`, capped at `max_position_pct`. Places via Alpaca. Marks signal executed. Existing 30-day + 55% win-rate live-trading gate stays.
6. **UI.** Sortable strategy columns (win rate, return, weight, trades). Allocation bar chart. Inline approve/reject for proposed strategies. Edit modal for signal_definition + caps + manual_weight override. New SignalsTab for the pending signals queue.

### Caps confirmed in plan (not in code yet)
- Max 25% capital per strategy
- Max 5% per single position
- Min 10 trades before allocator trusts a strategy

### Stuck on task creation
John tried to create a Google Tasks list called "CTRL" but the CTRL backend + the ctrl-tools MCP both authenticate against `johnbenjaminroberts@gmail.com` and only see 3 lists (BedBouncer, CTRL Pro, johnbenjaminroberts's list). The new "CTRL" list is on a different Google account. Either: (a) recreate the list on the right account, or (b) drop the 6 tasks into the default list with a `[CTRL Trading Engine]` prefix.

---

## ALL MODULES — STATUS (CTRL, unchanged this session)

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Trading | src/frontend/src/modules/trading/ | partial | Tabs exist (Overview/Bot/Strategies/TradeLog/Routines/Research). StrategiesTab UI is fine. **Underlying engine is empty** — one seeded strategy, no discovery/allocator/executor. See phase plan above. |
| Other CTRL modules | n/a this session | unchanged | |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
WordDrop:
  src/components/TileRack.tsx            — NEW. Linear-gradient wooden rack
  src/components/PuzzleBoard.tsx         — TileRack integrated into rack zone;
                                           RACK_RIM_RATIO added; tiles at top:rimExtra
  src/screens/Gameplay.tsx               — floating TileRack added above GameBoard
                                           (PLACEHOLDER); bottom picker rack removed;
                                           topRackWrap style added
  SESSION_STATE.md                       — NEW. Session state for next pickup
  LEARNINGS.md                           — NEW. Build/RN gotchas captured

CTRL: none modified this session.
```

---

## RECENT GIT COMMITS

WordDrop repo has no commits yet — the entire codebase is staged in the initial commit but never finalised. CTRL repo status not checked this session (no CTRL code changed).

---

## OPEN ISSUES / KNOWN BUGS

- **GameBoard rack integration is incomplete.** Floating placeholder in Gameplay.tsx must be replaced.
- **Two distinct visual treatments live in the codebase.** PuzzleBoard has the new integrated rack; GameBoard does not. They look different right now.
- **Column-fill indicators** at top of GameBoard don't exist on PuzzleBoard. Decision needed.
- **Daily Puzzle rack edge phases** (`auto_clearing`, `done`) — rack returns null in those phases, should just show an empty wooden channel; not verified visually yet.
- **CTRL trading "Strategies" tab is misleading.** UI looks like it shows active strategies + win rates, but the underlying data has only one seeded strategy that no orders are attributed to. `paper_trades` / `paper_wins` / `paper_total_return` stay at 0 indefinitely. Until Phase 1+2 land, the tab is decorative.

---

## KEY DECISIONS MADE THIS SESSION

- **TileRack uses `react-native-linear-gradient`, not `react-native-svg`.** The original SVG version (with `<Defs>` + `<LinearGradient id>` + `url(#id)` fill refs) did not render visibly in RN 0.85 + the project's svg version when used inside an absolute-positioned parent. Rewrote with plain Views + LinearGradient. **Do not revert.**
- **Rack height formula:** `previewH = cell + cell * 0.45` (rim extra). Cell-budget calc treats rack as `1.45` rows so totalRows in `dimsForSize` accounts for the extra vertical room.
- **Tiles sit BELOW the rim in the channel** (`top: rimExtra`), not centered vertically.
- **In Infinite mode, the rack at top replaces the bottom picker entirely.** Player taps a tile in the top rack to commit it.
- **Slot count is dynamic per mode + bonuses earned** — not a fixed constant.
- **CTRL trading: full systematic engine, paper-only until the existing 30-day + 55% win-rate gate flips.** Live gate stays. Caps: 25% per strategy, 5% per position, 10-trade minimum for allocator trust.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None. (Discussion-only on CTRL; WordDrop is local-only React Native.)

---

## DATABASE CHANGES THIS SESSION

None. Phase 1 of the trading plan describes schema changes but they have NOT been written or migrated.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Don't waste time on Metro / build setup again.** Key Windows gotchas captured in `WordDrop/LEARNINGS.md`. Notable ones:
  - `npx react-native run-android` fails on Windows because the CLI looks for `gradlew.bat` in cwd, not `android/`. Always: `cd android && ./gradlew.bat app:installDebug -PreactNativeDevServerPort=8082`.
  - Installed APKs may be release builds with no Metro connection. Uninstall via `adb uninstall com.worddrop` first if reinstalling.
  - Emulator can be out of storage — uninstall before installing.
  - `adb reverse tcp:8081 tcp:8082` is required because the installed debug build defaults to port 8081 but Metro is on 8082.
  - Metro can wedge with `0/1` stuck bundle requests; kill process holding port 8082 and restart.
- **The emulator should still be running** (`emulator-5554` connected at end of session) with the Daily Puzzle rack visible and working.
- **Metro is running in the background** on port 8082 (task id `bwgyp8v9o`) unless John has shut down the machine.
- **TileRack API:** accepts `slotCount`, `slotSize`, optional `width`, optional `height`, `showEndCaps` (default true; pass `false` when embedding inside a parent frame), optional `topRimHeight`. Children render in the channel.
- **PuzzleBoard renders tile slots SEPARATELY from TileRack.** TileRack draws only the wooden frame; the parent positions actual tiles absolutely on top using `cellLeft(i)` so they line up with the playable columns. Pass no `children` to TileRack in this integration; render tiles in a sibling `rackSlots` View.
- **The CTRL "Strategies" tab gives a false impression of capability.** Until phases 1-2 land, no real data flows. Worth pinning a note on the tab or hiding it until backed by a working engine.

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

WordDrop (active this session):
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

- **WordDrop** — active this session. Scrabble-style tile drop game. 3x3 Daily Puzzle + Infinite mode. Brand: warm parchment + gold + wood. RN 0.85.
- **CTRLPro / UnifyBI** — hospitality SaaS dashboard. Planning phase. First client conversation pending.
- **BedBouncer** — ESP32 smart alarm. Kickstarter prep. Needs product video.
- **Mobile Games portfolio** — BatonDrop (active), WordDrop (active this session), Cavernborn (planning, RN not initialised).
