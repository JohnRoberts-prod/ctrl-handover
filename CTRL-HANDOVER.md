# BatonDrop - Session Handover
Last updated: 2026-04-27 UTC

## NEXT STEPS (after PC restart)
1. Open Android Studio -> Device Manager -> press Play on Pixel emulator
2. Confirm: & "C:\Users\admin\AppData\Local\Android\Sdk\platform-tools\adb.exe" devices
3. Start Metro: cd "D:\AI Work\Mobile-Games\games\batondrop\app" && npx react-native start --reset-cache
4. Build: $env:JAVA_HOME="C:\Program Files\Android\Android Studio\jbr"; $env:ANDROID_HOME="C:\Users\admin\AppData\Local\Android\Sdk"; cd "D:\AI Work\Mobile-Games\games\batondrop\app\android"; .\gradlew.bat app:installDebug
5. Open BatonDrop from emulator launcher. adb reverse NOT needed for emulator.

## THIS SESSION

Two tasks done:
1. MECHANICS REWRITE - fixed all hits registering as miss. Root cause: runOnJS race condition. Fix: pre-compute perfectTimeMs = Date.now() + arrivalDuration synchronously in FallingBaton useEffect before animation starts. Store in GameEngine.batonPerfectTimes Map.
2. BRAND PASS - Luckiest Guy arcade font, 3D tap pads (face+shadow+highlight), amber HUD, dark navy background, BATON/DROP title, 3D amber PLAY button.

## FILES CHANGED
app/src/constants/fonts.ts - NEW
app/src/constants/colours.ts - REWRITTEN (brand palette)
app/src/components/TapBox.tsx - REWRITTEN (3D pad + press animation)
app/src/components/HUD.tsx - REWRITTEN (Luckiest Guy, amber border)
app/src/screens/GameOverScreen.tsx - REWRITTEN (arcade style)
app/src/screens/GameScreen.tsx - REWRITTEN (idle screen + PLAY button)
app/src/engine/GameEngine.ts - REWRITTEN (proper hit detection)
app/src/components/FallingBaton.tsx - REWRITTEN (sync onArrival)
android/app/src/main/assets/fonts/LuckiestGuy-Regular.ttf - NEW
android/app/src/main/assets/fonts/JetBrainsMono-Regular.ttf - NEW

## ENVIRONMENT
JAVA_HOME: C:\Program Files\Android\Android Studio\jbr
ANDROID_HOME: C:\Users\admin\AppData\Local\Android\Sdk
adb: C:\Users\admin\AppData\Local\Android\Sdk\platform-tools\adb.exe
RN 0.85, Metro 0.84.3, port 8081

## TEST AFTER RESTART
- Luckiest Guy font on title/score
- 3D pad press (6px translateY)
- Tap baton = not always miss
- Miss = lose 1 heart, 3 misses = game over

## WHAT COMES NEXT
- Confirm game works on emulator
- Special batons (skull/star)
- Replace placeholder sprites with Nano Banana 2 assets
- AdMob integration
- Play Store pipeline