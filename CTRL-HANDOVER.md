# CTRL Project Handover
*Last updated: 2026-05-31 UTC*
*Session ended: CTRL trading self-learning — NEW_STRATEGY command parser added*

---

## HOW TO USE THIS DOCUMENT

You are Claude picking up a multi-project development session for John Roberts.
Today covered: Stack Attack (Play Store submission), Colour Flood (full feature build + Play Store), CTRL trading (self-learning strategy discovery).

---

## WHAT WE WERE BUILDING WHEN /AFK WAS CALLED

CTRL trading module — self-learning strategy discovery. The trading bot runs 5 routines daily (Pre-Market, Market Decision, Midday Review, Political Watch, EOD) and was placing real trades but had no mechanism to register new strategies it discovered. Built:
1. POST /api/trading/strategies endpoint
2. NEW_STRATEGY command parser in trading-scheduler.service.ts (same pattern as TRADE_ORDER)
3. Context update so the bot can emit NEW_STRATEGY: {"name":"...","description":"...","risk_level":"...","works_best":"..."}

---

## STACK ATTACK STATUS

- AAB versionCode 2, package com.stackattacker, release-signed
- Uploaded to Play Console internal testing
- Phone uninstalled (waiting for Play Store)
- Needs: 12 testers + 14 days before production
- Needs: feature graphic via /process-batch stackattack-feature-graphic
- Keystore: android/app/stackattack-release.keystore + D:/AI Work/.keystores/
- Keystore pass: 27449bfb1f04ff1c356aac2b411d320b
- AdMob real IDs already set (App ~9636978172, Unit /3071569826)

---

## COLOUR FLOOD STATUS

- AAB versionCode 1, package com.colourflood, release-signed
- Uploaded to Play Console internal testing
- Phone uninstalled (waiting for Play Store)
- AdMob real IDs: App ca-app-pub-4375702454097791~5689004621, Unit /6696788368
- Leaderboard Worker live: https://colourflood-leaderboard.johnbenjaminroberts.workers.dev
- D1 DB: 90d1c7db-5550-461f-915f-b38ac9ee51c8
- Needs: feature graphic via /process-batch colourflood-feature-graphic
- Keystore: android/app/colourflood-release.keystore + D:/AI Work/.keystores/
- Keystore pass: 8bb1d2f0c6de8577df85412d2b56904f

---

## CTRL TRADING STATUS

- Backend was down earlier, restarted, vault needs unlocking after restart
- NEW_STRATEGY parser added to trading-scheduler.service.ts
- POST /api/trading/strategies route added
- trading-context.service.ts updated with NEW_STRATEGY format instructions
- 5 routines ran 25-29 times each, last run 2026-05-26 — resume 2026-06-02
- 3 strategies currently in DB; bot will add more on next routine runs
- 10 filled orders in DB (AMD, LMT, NVDA, XOM, SMCI, ARM, INTC)

---

## FILES MODIFIED THIS SESSION

```
SA: android/app/build.gradle — com.stackattacker, release signing, versionCode 2
SA: android/keystore.properties — NEW
SA: mipmap-anydpi-v26/ic_launcher.xml — NEW adaptive icon XML
SA: mipmap-*/ic_launcher*.png — Icon Kitchen icons

CF: PlayScreen.tsx — DELETED
CF: PlayStack.tsx, types.ts — Play removed from stack
CF: GameScreen.tsx — lives removed, level text enlarged
CF: GameOverScreen.tsx — worldId fix, daily screen, score submit
CF: HomeScreen.tsx — coins/streak removed, daily moved up, modals wired
CF: SettingsScreen.tsx — Remove Ads deleted
CF: LevelSelectScreen.tsx — GestureDetector swipe, compact layout
CF: LeaderboardScreen.tsx — real API, 4 tabs (Stars/Efficiency/Level/Daily)
CF: NameEntryScreen.tsx — NEW
CF: HowToPlayScreen.tsx — NEW
CF: AdService.ts — NEW AdMob interstitial
CF: LeaderboardService.ts — NEW full leaderboard + daily API
CF: gameStore.ts — playerName, hasSetName, hasSeenHowToPlay added
CF: useWorldTheme.ts — worldId param
CF: ScreenWrapper.tsx — worldId prop
CF: WorldSelector.tsx — height 64px fixed
CF: App.tsx — mobileAds init
CF: app.json, AndroidManifest.xml — AdMob IDs
CF: android/app/build.gradle — release signing
CF: backend/ — NEW Cloudflare Worker + D1

CTRL: trading.routes.ts — POST /api/trading/strategies
CTRL: trading-scheduler.service.ts — NEW_STRATEGY parser
CTRL: trading-context.service.ts — NEW_STRATEGY instructions
CTRL: trading.service.ts (frontend) — createStrategy() method
ctrlplay-website: privacy.html — effective date 2026-05-31
```

---

## OPEN ISSUES

- CTRL vault locked after backend restart — unlock before using trading/vault features
- SA + CF need 12 testers + 14 days for Play Store production
- Feature graphics not generated for either game (batches exist in skills/batches/)
- Add keystore passwords to CTRL vault

---

## KEY DECISIONS

- SA: applicationId=com.stackattacker (Play Store), namespace=com.stackattack (Java) — independent
- Keystores in android/app/ not android/ — file() resolves to app module dir
- CF daily: levelId=0 sentinel, /daily/submit endpoint, not /submit
- Trading: bot emits NEW_STRATEGY like TRADE_ORDER, scheduler auto-registers
- Profanity: bad names become Anonymous on score submit, 400 on /player/name

---

## HOW TO START CTRL

pm2 restart ctrl-backend (then unlock vault in CTRL UI)

Or: cd D:\AI Work\Control-Centre && npm run dev (runs all three services)

---

## OTHER PROJECTS

- BatonDrop: v2.0.0 in Play Store production review since 2026-05-17
- WordDrop: paused
- ctrlplay.games/privacy: effective date 2026-05-31 pushed live
