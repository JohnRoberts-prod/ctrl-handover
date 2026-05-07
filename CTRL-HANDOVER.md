# Cavernborn — Game Dev Handover
Last updated: 2026-05-07 UTC
Session ended: Resolved Skia v2 font loading blocker. Rewrote GameText.tsx with react-native-svg. Rebuild pending.

## HOW TO USE THIS DOCUMENT

You are picking up a Cavernborn mobile game development session.
John Roberts is the developer. Read this entire document before responding.

Project: Cavernborn — Dark fantasy idle RPG for Android (iOS later)
Studio: CTRL Play (ctrlplay.games)
Tech: React Native 0.85.3 + TypeScript + @shopify/react-native-skia + react-native-reanimated + react-native-svg
App folder: D:\AI Work\Mobile-Games\games\Cavernborn\app\
Skill file: D:\AI Work\Mobile-Games\games\Cavernborn\skills\skill-cavernborn.md

## WHAT WE WERE BUILDING THIS SESSION

Continued resolving the Skia v2 custom font loading blocker from last session.
Ran logcat diagnostic that confirmed root cause: Skia.Data.fromBytes() works (skData: OK)
but Skia.Typeface.MakeFreeTypeFaceFromData(skData) returns NULL — a Skia v2.6.2 Android bug.
Tried a 5th approach (copy font to document dir + file URI) which also failed at the same step.
Final fix: rewrote GameText.tsx to use react-native-svg instead of Skia for text rendering.
react-native-svg was installed. The app has NOT yet been rebuilt with the new code.

## CURRENT BUILD STATE

### Resolved this session
- Root cause confirmed: MakeFreeTypeFaceFromData returns NULL on Skia v2.6.2 Android (FreeType bug)
- GameText.tsx completely rewritten using react-native-svg (Svg + SvgText components)
- react-native-svg installed
- LEARNINGS.md, SESSION_STATE.md, skill-cavernborn.md all updated

### In progress right now
GameText.tsx rewritten but NOT rebuilt or tested.
User needs to run: npx react-native run-android from the app\ directory.
Then check logcat for absence of [GameText] errors and gold numbers on screen.

### Pending next steps
1. Rebuild and test with react-native-svg
2. Verify fontFamily names match: FredokaOne-Regular, Cinzel-Bold
3. Phase 2: Navigation + HomeScreen + CurrencyBar

## THE SKIA FONT PROBLEM — FULL HISTORY

Five approaches tried, all failed:
1. require() — Image.resolveAssetSource returns invalid URI for TTF files
2. { uri: 'file:///android_asset/fonts/...' } — Skia.Data.fromURI rejects android_asset scheme
3. fetch('file:///android_asset/...') — OkHttp does not support android_asset URIs
4. RNFS.readFileAssets + Uint8Array — bytes load correctly (305871/305836) but MakeFreeTypeFaceFromData returns NULL
5. RNFS.copyFileAssets to doc dir + file:// URI — same MakeFreeTypeFaceFromData NULL

Diagnostic confirmed: skData OK, typeface NULL. FreeType inside Skia v2.6.2 rejects the data.

FIX: react-native-svg uses Android platform font system.
Fonts in android/app/src/main/assets/fonts/ auto-register with Android TypeFace on app start.
fontFamily prop matches TTF filename without extension: FredokaOne-Regular, Cinzel-Bold.

## GAMETEXT.TSX — NEW IMPLEMENTATION

File: app/src/components/GameText.tsx
Uses Svg + SvgText from react-native-svg (NOT Skia Text).
Two SvgText layers: stroke layer (fill=none, strokeWidth=sw*2) then fill layer (stroke=none).
fontFamily: 'FredokaOne-Regular' or 'Cinzel-Bold' (matches filename without .ttf).
estimateWidth: text.length * size * 0.62 heuristic.
centered prop uses textAnchor='middle' and tx = w/2.

## PHASE 1 BUILD STATUS

- src/components/GameText.tsx — REWRITTEN with SVG, UNTESTED on device
- src/components/FloatingNumber.tsx — built (wraps GameText in Animated.View)
- src/components/TreasureChest.tsx — working (Skia placeholder visuals)
- src/components/CaveCanvas.tsx — working (Skia geometry, placeholder tiles)
- src/components/HeroSprite.tsx — working (Skia coloured circles, bob animation)
- src/hooks/useAFKRewards.ts — built, needs runtime test
- src/screens/AFKScreen.tsx — built, assembles all components
- App.tsx — wired to AFKScreen
- android/app/src/main/assets/fonts/ — FredokaOne-Regular.ttf + Cinzel-Bold.ttf (DO NOT MOVE)
- android/app/src/main/AndroidManifest.xml — AdMob test ID configured with tools:replace

## KEY PACKAGES

@shopify/react-native-skia@2.6.2 — Canvas geometry (CaveCanvas, TreasureChest, HeroSprite)
react-native-svg — text rendering (GameText, FloatingNumber)
react-native-reanimated@4.3.0 — animations
react-native-worklets — required separately alongside Reanimated v4
react-native-fs — still installed (was used for font loading attempts)
react-native-encrypted-storage — offline timestamp (useAFKRewards)
react-native-purchases — RevenueCat IAP (installed, not yet wired)
react-native-google-mobile-ads — AdMob (App ID configured)
@react-navigation/* — navigation (installed, not yet used)
@reduxjs/toolkit react-redux — state management (installed, not yet used)

## ANDROID BUILD GOTCHAS

1. AdMob crashes without APPLICATION_ID — already fixed in AndroidManifest.xml
   Test App ID: ca-app-pub-3940256099942544~3347511713

2. Reanimated v4 needs react-native-worklets separately — already installed

3. Emulator stale lock:
   Stop-Process -Name "qemu-system-x86_64" -Force -ErrorAction SilentlyContinue
   Remove-Item "$env:USERPROFILE\.android\avd\Pixel_7.avd\*.lock" -Force

4. Fonts for SVG fontFamily must be in android/app/src/main/assets/fonts/ — already there

## DESIGN

bg: #0D0A1A | bg2: #1A1230 | gold: #C8A030 | goldBright: #FFD700
Rarity: common #8A8A8A | uncommon #27AE60 | rare #4A90D9 | epic #9B59B6 | legendary #FFD700 | mythic #FF4444
Fonts: FredokaOne-Regular (numbers) | Cinzel-Bold (hero/boss names)

OP formula: (ATK*2.5)+(INT*2.5)+(DEX*2.0)+(DEF*1.5)+(HP*0.5)+(LUCK*1.0)+bonuses
Gold per hit = OP * 0.002 | Scrolls: 1 per 10 hits | Offline cap: 12 hours

## HOW TO RUN

cd D:\AI Work\Mobile-Games\games\Cavernborn\app
npx react-native run-android

Logcat: & "$env:LOCALAPPDATA\Android\Sdk\platform-tools\adb.exe" logcat -s ReactNativeJS
Emulator: Pixel 7 AVD | ANDROID_HOME: C:\Users\admin\AppData\Local\Android\Sdk
