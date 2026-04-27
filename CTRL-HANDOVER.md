# BatonDrop Project Handover
Last updated: 2026-04-27 UTC
Session ended: Mobile sound fix. react-native-sound installed and deployed. Sound on physical device not yet confirmed.

## HOW TO USE THIS DOCUMENT
You are Claude web browser picking up a BatonDrop development session.
John Roberts is the developer. Read this entire document before responding.
Codebase: D:\AI Work\Mobile-Games\games\batondrop\app
Stack: React Native 0.85.2 + TypeScript, Android (Pixel 8 Pro + emulator), newArchEnabled=true

## WHAT WE WERE DOING
Fixing sound on mobile. react-native-track-player was broken (128KB patch mess, no sound on device). Switched to react-native-sound 0.13.0 (bridge-compatible with New Architecture). MP3s moved from assets/ to res/raw/. build-phone.ps1 convenience script created. Final build was installing when /afk called.

## WHAT WAS COMPLETED THIS SESSION
- Removed react-native-track-player + patch file
- Installed react-native-sound@0.13.0
- Rewrote MusicPlayer.ts: lazy-loads 3 tracks, sequences them in a loop, skipped guard prevents infinite loop on load failure
- Gutted MusicService.ts (RNTP boilerplate, now empty stub)
- Cleaned index.js (removed RNTP service registration)
- Created src/types/react-native-sound.d.ts (no @types package exists)
- Fixed TS error in GameScreen.tsx (redundant quality !== 'miss' after early return)
- Copied MP3s to android/app/src/main/res/raw/
- Created build-phone.ps1 (bundle + installDebug in one command)
- Previous session: level gate 4+level formula, exponential decay speed curves, baton visual continuity
- Git commit 881dcb3 pushed

## IN PROGRESS
Sound on phone not yet confirmed. Build was running at /afk.

## NEXT STEPS
1. Confirm sound works on phone (unplug after install, open app)
2. If no sound: check logcat for [MusicPlayer] warnings
3. If working: particle effects on hit (next priority)
4. Then: screen shake on miss
5. Then: level-up transition animation

## KEY FILES
- app/src/services/MusicPlayer.ts — music sequencer (react-native-sound)
- app/src/types/react-native-sound.d.ts — local TS type declaration
- app/src/screens/GameScreen.tsx — calls musicPlayer.start/stop
- app/src/engine/GameEngine.ts — hit detection, lives
- app/src/constants/game.ts — speed curves, level gates
- app/src/components/FallingBaton.tsx — animated baton
- app/src/components/HangingRow.tsx — hanging batons (bottom-half clip)
- app/build-phone.ps1 — phone build script

## KEY DECISIONS
- react-native-sound over RNTP: bridge-compat with newArchEnabled=true
- MP3s in res/raw/ not assets/: Sound.MAIN_BUNDLE only works with res/raw on RN 0.85
- No @types package: use local declaration file
- Infinite loop guard: playTrack(index, skipped) bails if skipped >= track count

## HOW TO BUILD FOR PHONE
cd "D:\AI Work\Mobile-Games\games\batondrop\app"
.\build-phone.ps1
Unplug phone before opening app.

## EMULATOR BUILD
Window 1: cd app && npx react-native start --reset-cache
Window 2: set JAVA_HOME to Android Studio jbr, cd app\android, .\gradlew.bat app:installDebug

## CRITICAL: PHYSICAL DEVICE + NEW ARCHITECTURE
Images do NOT load via Metro on physical device. Must use build-phone.ps1 (bundled APK). Unplug before opening.

## GAME DESIGN QUICK REFERENCE
- 4 lanes L1-10, 5 lanes L11-25, 6 lanes L26+
- Level gate: 4+level hits (5 for L1, 6 for L2...)
- Speed: exponential decay 0.878 fall, 0.88 spawn
- 3 lives, combo, fever at 20 consecutive hits
- 7 baton types: Standard, Speed, Slow, Skull (no tap), Ghost, Bounce, Chain
- PERFECT +-80ms, GOOD +-150ms, OK +-200ms, outside = combo break only, exit = life lost

## OTHER PROJECTS
- CTRL: personal business OS, local React+Node app, actively built
- CTRLPro: hospitality SaaS, planning phase
- BedBouncer: ESP32 smart alarm, Kickstarter prep
- ChessMusic/WordDrop: concept phase