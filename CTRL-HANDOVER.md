# BatonDrop Session Handover
Last updated: 2026-05-03 UTC
Session ended: investigating leaderboard scoring bug

## PROJECT
BatonDrop React Native Android game.
App: D:\AI Work\Mobile-Games\games\batondrop\app
Backend: Cloudflare Worker at batondrop-backend.johnbenjaminroberts.workers.dev
Backend code: D:\AI Work\Mobile-Games\games\batondrop\backend\src

## COMPLETED THIS SESSION
1. Lane scaling bug fix (v1.5 / versionCode 7)
   - buildLaneRegistry now calculates dynamic boxSize = min(80, floor((screenWidth-32-(n-1)*8)/n))
   - TapBox accepts size prop
   - GameScreen passes registry.boxSize to TapBox and uses it for boxCenterY
   - Files: LaneRegistry.ts, TapBox.tsx, GameScreen.tsx, build.gradle, OptionsScreen.tsx
   - AAB built: android/app/build/outputs/bundle/release/app-release.aab
   - NOT yet uploaded to Play Store

2. Leaderboard scoring bug - INVESTIGATION INCOMPLETE
   Problem: scores register correctly in game but don't appear on leaderboard after runs
   The lane scaling changes do NOT affect scoring - confirmed by code review
   Most likely cause: startRun() failing silently -> sessionToken = null -> submitScore never called
   Score flow: startGame calls startRun(playerToken) -> stores sessionToken in ref
   On game over: if (token && sessionToken) submitScore() else local fallback only
   To diagnose: add console.log('sessionToken:', sessionToken) in finalizeRunRef around line 170
   Backend score.ts looks correct - session token validation, score insert, coin award

## NEXT STEPS (priority order)
1. Diagnose leaderboard bug - add logging to confirm sessionToken is populated
2. Upload v1.5 AAB to Play Store (versionCode 7)
3. Create Android OAuth client in Google Cloud Console (Android type, com.batondrop, SHA-1 from Play Console)
4. Test Google Sign-In end-to-end
5. Fix ctrlplay.co.uk/privacy 403 (Cloudflare Bot Fight Mode)
6. Add /delete-account page to ctrlplay.co.uk
7. Feature graphic: open feature-graphic.html in Chrome, download PNG, upload to Play Store
8. Delete fake bot accounts (KXTYQH, player1) from Turso

## OPEN ISSUES
- Leaderboard not updating - root cause unconfirmed (likely sessionToken = null)
- Google Sign-In untested - Android OAuth client missing
- ctrlplay.co.uk/privacy 403 - blocks Play Store submission
- Fake leaderboard players from Google pre-launch bots

## VERSION HISTORY
- versionCode 4: v1.2
- versionCode 5: v1.3
- versionCode 6: v1.4
- versionCode 7: v1.5 (lane scaling fix, NOT yet on Play Store)

## BUILD COMMAND
cd "D:/AI Work/Mobile-Games/games/batondrop/app/android"
JAVA_HOME="C:/Program Files/Android/Android Studio/jbr" ./gradlew bundleRelease
