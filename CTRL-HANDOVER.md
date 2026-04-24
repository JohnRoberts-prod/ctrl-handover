# CTRL Project Handover
*Last updated: 2026-04-25 — End of evening session*
*Session ended: BatonDrop Phaser 3 build — full gameplay loop running, sprite sizing fix, all skill/knowledge files updated, then /afk*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

There are TWO active projects:

**CTRL Control Centre** — Personal business OS (on hold this session)
Codebase: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Database: SQLite at `D:\AI Work\.ctrl-data.db`

**BatonDrop** — Mobile arcade game (ACTIVE this session)
Codebase: `D:\AI Work\Mobile-Games\games\batondrop\src\`
Dev server: http://localhost:3000 (Vite)
Tech: Phaser 3 + TypeScript + Capacitor

---

## WHAT WE WERE BUILDING THIS SESSION — BATONDROP

We built the complete Phaser 3 BatonDrop game from scratch. The full gameplay loop is running:
Boot → Preload → Menu → Game → HUD → GameOver. All core systems are implemented.

This session covered:
- Building all game systems from scratch (scenes, objects, systems — full list below)
- Balance calibration: researched mobile session data (median 5-6 min) to tune difficulty curve
- Moving real Nano Banana 2 baton sprites into src/assets/batons/ and renaming them correctly
- Fixing oversized baton sprites: AI sprites are 512px+ raw; added setDisplaySize(54, 200) in Baton.ts and Menu.ts
- Updating STATUS.md, KNOWLEDGE_BASE_GAMING.md, skill-batondrop-workflow.md, and memory files

Final fix before /afk: Menu.ts createHangingBatons() used hardcoded batonH=144 and no setDisplaySize.
Fixed by importing BATON_DISPLAY from AssetManifest and calling setDisplaySize(BATON_DISPLAY.w, BATON_DISPLAY.h).

---

## CURRENT BATONDROP BUILD STATE

### Complete and working (zero TypeScript errors)
- src/config/AssetManifest.ts — ATLAS, FRAMES, PH, BG, AUDIO, BATON_IMAGES, BATON_DISPLAY, LANE_BATON_KEYS
- src/config/GameBalance.ts — tunable constants, research-calibrated speed curve + level formula
- src/config/BatonTypes.ts — all 7 baton definitions + weighted random picker
- src/scenes/Boot.ts — placeholder texture generation (6 baton colours, hit zone, symbol, particle)
- src/scenes/Preload.ts — progress bar, loads real baton sprites, graceful missing-file handling
- src/scenes/Menu.ts — perspective runway, hanging baton sway, PLAY + 3 buttons, correct sizing
- src/scenes/ComingSoon.ts — stub for unbuilt scenes
- src/scenes/Game.ts — core gameplay, registry init, input handling, game-over listener
- src/scenes/HUD.ts — parallel overlay scene, purely reactive (reads registry only, never writes)
- src/scenes/GameOver.ts — score display, PLAY AGAIN / MENU buttons
- src/systems/DifficultySystem.ts — level/speed/gap helpers, checkLevelUp()
- src/systems/ScoreSystem.ts — scoring, combo, fever timer (owned here not HUD), loseLife()
- src/objects/Baton.ts — Phaser Container, pool-managed, correct setDisplaySize
- src/objects/BatonPool.ts — 20 pre-allocated batons, acquire/release
- src/objects/HitZone.ts — per-lane visual, judgement text flash, pulse on approach
- src/objects/LaneManager.ts — sway, pre-drop tell, Quad.easeIn drop, hit detection, replacement
- src/main.ts — Phaser.Game config, all scenes registered
- src/assets/batons/ — 6 real sprites (blue, red, yellow, green, purple, cyan) from Nano Banana 2

### NOT yet built (priority order)
1. Particle effects on hit (PERFECT = big burst, GOOD = medium, LATE = small)
2. Screen shake on miss / life lost
3. Level-up transition animation (flash + new lane slides in)
4. Sound integration (placeholder beeps wired to events, real SFX later)
5. Ghost baton mechanics (hover 1s mid-screen via delayedCall tween pause)
6. Bounce baton mechanics (squash on impact, bounce, tap second pass only)
7. Chain baton mechanics (longer baton, hold tap for duration)
8. Background switching per level range (9 themes, load commented lines in Preload.ts)
9. Continue screen (watch ad / 300 coins for 3 lives)
10. Coin award system + HUD coin display
11. SaveSystem (@capacitor/preferences)
12. Economy/shop system (power-ups, cosmetic shop)
13. AdMob integration (@capacitor-community/admob)
14. Daily challenge mode
15. Leaderboard (local first, online when backend built)

---

## BATONDROP ARCHITECTURE — KEY DECISIONS LOCKED

### Speed multiplier direction (bug fixed this session)
speedMultiplier > 1 = FASTER = SHORTER duration. Always DIVIDE:
  dropDurMs = difficulty.dropDurationMs / def.speedMultiplier
Never multiply — that makes speed batons slower (duration x 2 = twice as long).

### Baton sizing rule (critical)
Always call setDisplaySize(BATON_DISPLAY.w, BATON_DISPLAY.h) on every baton sprite.
BATON_DISPLAY = { w: 54, h: 200 } in AssetManifest.ts.
AI sprites are 512px+ raw — without this they fill the entire screen.
Applies in: Baton.prepare(), Menu.createHangingBatons(), any future scene showing batons.

### Timing math
T_STAR_FRAC = sqrt((HZ_CY - HANG_Y) / (EXIT_Y - HANG_Y)) = approx 0.844
perfectTime = scene.time.now + dropDurMs * T_STAR_FRAC
delta = tapTime - perfectTime  (negative=early, positive=late)
Windows: +-80ms perfect, +-150ms good, +-200ms late. Outside = silently ignored (not a miss).
Miss deadline fires at T_STAR_FRAC * dropDurMs + 200ms + 16ms after drop start.

### Fever timer
Owned by ScoreSystem. HUD only reacts to EVT_FEVER_START/EVT_FEVER_END registry events.
Never put gameplay timers in HUD.

### HUD
Purely reactive — reads registry only, never writes game state. Subscribes via:
  registry.events.on('changedata', this.onRegistryChange, this)

### Cross-scene state
Phaser registry with REG constants as keys. Events fired as one-shot values:
  registry.set(REG.EVT_LEVEL_UP, level)

### Object pool
BatonPool pre-allocates 20 Batons at scene init via scene.add.existing().
Never create/destroy during gameplay — only prepare()/deactivate().

### Phaser Container children
Use scene.make.image() not scene.add.image() for Container children.
scene.add.* adds to display list directly. scene.make.* creates without adding — Container manages them.

### Spawn gap
Measured from drop START not landing. Allows multiple batons in-flight at high levels.

### Balance
Level 1: 2200ms drop, 1857ms reaction window (very forgiving)
Level 10: 1804ms drop, 1523ms reaction (comfortable)
Level 20: 1450ms drop, 1224ms reaction (challenging)
Level 50: 739ms drop, 624ms reaction (extreme)
Level formula: 5000 * level + 400 * level * (level + 1) — quadratic, per-level grows by 800pts
Most players die levels 3-6 (~3-6 min total). Level 15 = elite territory (~26 min).

---

## FILES MODIFIED THIS SESSION

```
src/config/AssetManifest.ts         — Added BATON_IMAGES, BATON_DISPLAY, updated LANE_BATON_KEYS
src/config/GameBalance.ts           — Full rewrite: quadratic level formula, calibrated speed curve
src/scenes/Preload.ts               — Added BATON_IMAGES load loop for real sprites
src/scenes/Menu.ts                  — Fixed baton sizing: import BATON_DISPLAY + setDisplaySize call
src/objects/Baton.ts                — setDisplaySize(BATON_DISPLAY.w, BATON_DISPLAY.h) in prepare()
src/assets/batons/baton-blue.png    — Moved and renamed from files/brand/game-sprites/batons/
src/assets/batons/baton-red.png     — Moved and renamed
src/assets/batons/baton-yellow.png  — Moved and renamed
src/assets/batons/baton-green.png   — Moved and renamed
src/assets/batons/baton-purple.png  — Moved and renamed
src/assets/batons/baton-cyan.png    — Moved and renamed
STATUS.md                           — Full rewrite: complete built list, priority todo list
KNOWLEDGE_BASE_GAMING.md            — Section 15 updated: checked off built, remaining list
skills/skill-batondrop-workflow.md  — Added Phaser 3 Architecture section with all key decisions
```

Memory files updated:
```
memory/project_batondrop.md  — Full current build state + architecture decisions
memory/build-lessons.md      — Added 4 Phaser-specific lessons
```

---

## CTRL CONTROL CENTRE STATE (not worked on this session)

Next build priority: Email intelligence UI (Tasks 2h-2l).
All email intelligence backend is complete.

Working in CTRL:
- App shell, sidebar, topbar, Claude panel
- VaultGate UI, Google OAuth, real Gmail loading
- Gmail tab, Claude tab (xterm.js terminal), Home/Finances tabs (basic)
- Email intelligence backend: rules engine, Gmail Pub/Sub webhook, importance scoring
- Trading tab: Alpaca connected, 6-tab layout, overview + strategies tabs

Next skill files to build (in order):
1. skill-email-intelligence.md — UI tasks 2h-2l (DO NEXT)
2. skill-notes.md — Notes tab using ClickUp Docs v3 API
3. skill-github.md — needs design session first
4. skill-settings.md — trading/perplexity/clickup sections
5. skill-clickup.md — workspace auto-setup

Pending manual tasks:
- Push CTRL to GitHub (critical path — do first)
- Create ctrl-trading-agent GitHub repo (separate, private)
- Create ClickUp workspace manually at clickup.com (name: CTRL)
- Alpaca paper account signup if not done
- Perplexity API key generation

Known issues:
- PowerShell truncates long pasted commands — save as .ps1 and run
- Port 3001 EADDRINUSE on restart — kill with netstat + taskkill
- Gmail watch expires every 7 days — auto-renewal not yet built

---

## CLICKUP NOTE

ClickUp is not connected in CTRL yet. The vault does not have clickup_api_key or clickup_workspace_id set.
The workspace must be created manually at clickup.com first, then connected via Settings → Integrations → ClickUp in CTRL.

BatonDrop tasks to create in ClickUp once connected (priority order):
P1: Particle effects on hit
P1: Screen shake on miss/life lost
P1: Level-up transition animation
P1: Sound integration (beeps → real SFX)
P2: SaveSystem (@capacitor/preferences)
P2: Coin award system + HUD coin display
P3: Ghost baton mechanics
P3: Bounce baton mechanics
P3: Chain baton mechanics
P3: Background switching per level range
P4: Continue screen (ad + coin option)
P4: Economy/shop system
P4: AdMob integration
P5: Daily challenge
P5: Leaderboard

---

## BATONDROP ASSETS STILL NEEDED

Generate via Nano Banana 2 in CTRL Design Tab → Sprites tab.
Master baton prompt locked in: D:\AI Work\Mobile-Games\games\batondrop\skills\skill-batondrop-workflow.md

Still needed:
- Symbol overlays: skull, speed (down arrows x2), slow (snail), ghost outline, bounce (up/down arrows), chain link
- Hit zone sprite (resting + pulse states)
- Particle burst texture
- All 9 background scenes (perspective runway, vanishing point centre-top)
- App icon (512x512, baton on dark bg, amber accent, no text)
- Feature graphic (1024x500, gameplay + logo)
- 5 Play Store screenshots

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. Ghost, Bounce, and Chain batons are defined in BatonTypes.ts but have no special behaviour.
   Until mechanics are built, set their weight to 0 in pickBatonKind (weighted random).
   They behave as standard drops if weight > 0 and unlock level is reached.

2. Baton sprites may have white backgrounds (not transparent — Nano Banana 2 default).
   If they look blocky in game: apply blend mode on bodySprite.setBlendMode(Phaser.BlendModes.ADD),
   or request new transparent versions. Not yet tested on device.

3. BATON_DISPLAY = { w: 54, h: 200 }. At 6 lanes (450px canvas), lane width = 75px.
   Each baton leaves ~10px padding each side. May need adjustment after physical device testing.

4. KNOWLEDGE_BASE_GAMING.md section 4 level table shows old formula (500 * level^1.4).
   Actual implemented formula is the quadratic one in GameBalance.ts. Section 4 is reference only.

5. Sprite colour-to-lane mapping: LANE_BATON_KEYS[0]=blue, [1]=red, [2]=yellow, [3]=green, [4]=purple, [5]=cyan.
   If wrong colours show in wrong lanes, check this array in AssetManifest.ts.

---

## HOW TO START BATONDROP

cd D:\AI Work\Mobile-Games\games\batondrop\src
npm run dev
Opens at http://localhost:3000

## HOW TO START CTRL

D:\AI Work\START-ALL.bat
Or manually:
- Backend:  cd D:\AI Work\Control-Centre && npm run dev:backend
- Frontend: cd D:\AI Work\Control-Centre && npm run dev:frontend
- Terminal: cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev

---

*Next priority: Particle effects on hit, then screen shake on miss.*
*BatonDrop: D:\AI Work\Mobile-Games\games\batondrop\src\*
*CTRL: D:\AI Work\Control-Centre\*
