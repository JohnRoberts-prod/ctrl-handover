# BatonDrop Project Handover
*Last updated: 2026-04-28 UTC*
*Session ended: Design upgrade — background image wired in, emulator grey screen issue, PC rebooting*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a BatonDrop development session.
John Roberts is the developer. Read this entire document before responding.

App location: `D:\AI Work\Mobile-Games\games\batondrop\app\`
Stack: React Native 0.85.2 + TypeScript, Android
New Architecture enabled: newArchEnabled=true
GitHub: https://github.com/JohnRoberts-prod/ctrl-batondrop (commit 8ddc1e0)

---

## WHAT WE WERE BUILDING THIS SESSION

BatonDrop visual/design upgrade. Target: make the game look like a mockup collage at
`D:\AI Work\Mobile-Games\games\batondrop\brand\assets\hero-images\WhatsApp Image 2026-04-15 at 18.56.25.jpeg`
(4-screen collage showing atmospheric stone runway, glowing batons, green buttons, game over screen).

We redesigned the HUD, wired in an atmospheric background image, fixed baton z-order so they
appear behind the top bar, added bestScore tracking, and redesigned the idle screen START button.
Session ended while troubleshooting an Android emulator grey screen — PC was rebooting to fix it.

---

## CURRENT BUILD STATE

### Completed this session
- `src/components/HUD.tsx` — Full rewrite: 3-column layout (SCORE left / LVL centre / BEST right),
  level modifier text (QUICK/FAST!/BLAZING!/INSANE! based on level), thick 20pt glowing amber
  progress bar with shine + leading edge glow, lives hearts in right column, bestScore prop
- `src/components/HangingRow.tsx` — Removed overflow:hidden clip, slot full BATON_H (200pt),
  spriteWrapper.top=0, batons hang 35% behind the HUD bar (bar masks their tops)
- `src/screens/GameScreen.tsx` — ImageBackground wraps all three states (idle/playing/gameover),
  bestScore state added + updated on hits and game_over, hangingTop = hudHeight - 70pt,
  HangingRow renders BEFORE HUD in JSX (z-order fix), idle screen has large blue START button
  pinned to bottom
- `src/screens/GameOverScreen.tsx` — Container background rgba(0,0,0,0.55), atmosphere shows through
- `src/assets/backgrounds/background.png` — NEW atmospheric stone cobblestone runway image

### NOT YET TESTED
Everything above is committed and pushed but NOT visually tested — emulator was broken.

### Pending next steps (in order)
1. Confirm emulator boots after PC reboot (see emulator fix below)
2. `cd app && npx react-native start --reset-cache`
3. `npx react-native run-android` or `adb reverse tcp:8081 tcp:8081` then open app
4. Visually test: background, HUD layout, baton-behind-bar effect, progress bar, START button
5. Redesign Game Over screen to match mockup (RETRY|MENU green buttons side by side, dark WATCH AD button)
6. Generate remaining UI assets in Nano Banana 2: coin icon, settings gear, sound on/off, pause, home
7. Wire coin icon into HUD and game over screen

---

## EMULATOR FIX (grey screen issue)

**Problem:** Pixel_7 emulator shows grey screen, never boots Android.
**Fix applied this session:**
Edited `C:\Users\admin\.android\avd\Pixel_7.avd\config.ini`:
- `hw.gpu.mode=swiftshader_indirect` (was: auto) — pure software rendering, bypasses GPU
- `fastboot.forceColdBoot=yes` (was: no) — forces cold boot, ignores corrupted snapshot
PC was rebooted.

**After reboot, start emulator with:**
```
"C:\Users\admin\AppData\Local\Android\Sdk\emulator\emulator.exe" -avd Pixel_7 -no-snapshot-load
```

If still grey after reboot: delete the AVD in Android Studio Device Manager and recreate
with a new x86_64 Google APIs system image (API 33 or 34).

---

## FILES MODIFIED THIS SESSION

```
app/src/components/HUD.tsx              — Full rewrite, new layout, thick progress bar, bestScore prop
app/src/components/HangingRow.tsx       — Removed clip, full baton height, z-order fix
app/src/screens/GameScreen.tsx          — ImageBackground, bestScore, hangingTop, idle START button
app/src/screens/GameOverScreen.tsx      — Transparent background rgba(0,0,0,0.55)
app/src/assets/backgrounds/background.png — NEW background image
D:\AI Work\Mobile-Games\SESSION_STATE.md — Updated
D:\AI Work\Mobile-Games\LEARNINGS.md    — 6 new entries
```

---

## ASSET STATUS

| Asset | Status | Notes |
|-------|--------|-------|
| Baton sprites (6 colours) | Done | app/src/assets/batons/ |
| Special effect icons (9) | Done, live on pads | app/src/assets/effects/ |
| Background image | Done, wired in, UNTESTED | app/src/assets/backgrounds/background.png |
| Coin icon | Not generated yet | Nano Banana 2 prompt written |
| Settings/gear icon | Not generated yet | Nano Banana 2 prompt written |
| Sound on/off icons | Not generated yet | — |
| Pause icon | Not generated yet | — |
| Home icon | Not generated yet | — |

---

## DESIGN DECISIONS MADE

- Level complete screens: NOT being built — levels are continuous, no interruption
- Background: Gothic stone cobblestone runway, amber torches, purple/blue atmospheric glow
- Buttons: Blue for START, GREEN for RETRY/MENU/CONTINUE (matching mockup). Amber phased out.
- Game Over screen: needs redesign — RETRY|MENU side by side green, WATCH AD dark full-width
- bestScore: in-session only (no AsyncStorage persistence yet)
- Effect icons: live on tap pads. When a special baton is incoming, icon shows on that pad.

---

## NANO BANANA 2 PROMPTS WRITTEN (not yet generated)

Coin icon prompt (for economy display):
```
STYLE: Glossy 3D arcade coin, premium cartoonish
SUBJECT: Single gold coin face-on, slight 3D tilt. Bold star embossed centre. Strong highlight.
DIMENSIONS: 256x256 transparent PNG
COLOUR PALETTE: Rich gold #FFD700, bright highlight #FFFACD, dark rim #B8860B
NOTES: Floating coin only, transparent background. Readable at 24x24pt.
AVOID: Background, shadow below, text on face, photo style
```

Settings gear, sound on/off, pause, home — all: white 3D embossed, glossy, transparent bg, 256x256.

---

## GAME ARCHITECTURE QUICK REFERENCE

- Hit detection: position-based via FallingBaton forwardRef, quality from visual Y vs boxCenterY
- PERFECT +-80ms, GOOD +-150ms, OK +-200ms, outside = combo break only, miss = baton exits screen
- Speed: `dropDurMs = difficulty.dropDurationMs / speedMultiplier` (DIVIDE not multiply)
- Lanes: 4 at L1-10, 5 at L11-25, 6 at L26+
- Level gate: 4+level hits to advance
- Speed decay: 0.878 fall duration, 0.88 spawn interval (exponential, Tetris-like)
- Music: react-native-sound 0.13.0, MP3s in android/app/src/main/res/raw/
- Baton BATON_H=200pt, BATON_W=54pt
- HUD bar height approx 100-120pt, batons hang 35% (70pt) behind it
- Images on physical device: MUST use bundled APK (build-phone.ps1), not Metro

---

## HOW TO BUILD

**Emulator:**
1. Start emulator (see command above)
2. `cd "D:\AI Work\Mobile-Games\games\batondrop\app"`
3. `npx react-native start --reset-cache`
4. In another terminal: `npx react-native run-android`
5. After any Metro restart: `adb reverse tcp:8081 tcp:8081`

**Physical Pixel 8 Pro (ID: 38011FDJG00520):**
```
cd "D:\AI Work\Mobile-Games\games\batondrop\app"
.\build-phone.ps1
```
Unplug phone BEFORE opening app.

---

## ALL PROJECTS (context)

- **CTRL** — Personal business OS, React+Node+SQLite local web app. Project Spine phases 1-5 done.
- **CTRLPro** — Hospitality SaaS, planning phase, no build yet, first client conversation needed.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video.
- **BatonDrop** — THIS PROJECT. Tap-timing mobile game. Mechanics solid, design upgrade in progress.
