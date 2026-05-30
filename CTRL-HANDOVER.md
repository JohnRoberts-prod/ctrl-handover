# CTRL Project Handover
*Last updated: 2026-05-30 UTC*
*Session ended: Stack Attack polish + AdMob done, moving to Colour Flood*

---

## HOW TO USE THIS DOCUMENT

You are Claude picking up a mobile games session for John Roberts.
Read this entire document before responding.

Active project this session: **Stack Attack** (React Native 0.85 bare)
Location: `D:AI WorkMobile-Gamesgamesstackattackapp`
Package: `com.stackattack`

John's next focus: **Colour Flood**
Location: `D:AI WorkMobile-Gamesgamescolourflood`

---

## WHAT WE WERE BUILDING THIS SESSION

Stack Attack UI polish and AdMob integration. Fixed GameOver always showing wrong world
background, removed a dead lives system, enlarged the level header text, removed a fake
Remove Ads button and replaced a non-functional daily challenge with a Coming Soon badge,
and fully wired AdMob with real production IDs. Session ended with a built APK that has
not yet been installed because the phone was not connected.

---

## CURRENT BUILD STATE

### Completed this session

- **GameOver world background** — `app/src/screens/GameOverScreen.tsx`
  Always showed Construction theme. Fixed: worldForLevel(levelId).world passed to ScreenWrapper.

- **Remove Ads button removed** — `app/src/screens/SettingsScreen.tsx`
  Entire Site Supplies section deleted. Was an Alert stub, never real IAP.

- **Daily Challenge → Coming Soon** — `app/src/screens/HomeScreen.tsx`
  Removed lock/unlock mechanic. Static COMING SOON badge now shown.

- **AdMob integrated** — full production
  Package: react-native-google-mobile-ads@^16.3.3
  App ID: ca-app-pub-4375702454097791~9636978172
  Interstitial unit: ca-app-pub-4375702454097791/3071569826
  AdService.ts fires interstitial every 5 levels (win or lose)
  AdActivity theme override for Android 15 edge-to-edge fix
  SDK initialised in App.tsx; showInterstitialIfDue() in GameOverScreen useEffect

- **Lives system removed** — `app/src/screens/GameScreen.tsx`
  LivesDisplay, lives store sub, spendLife(), life-lost toast — all deleted.
  Mechanic: miss the bar = instant game over. Right-side slot = width-48 spacer.

- **Level text enlarged** — `app/src/screens/GameScreen.tsx`
  Was TYPE.label. Now FONTS.numberBold 28px #f5c520 gold.

### In progress right now

Built APK at: `app/android/app/build/outputs/apk/release/app-release.apk`
NOT YET INSTALLED. Phone (38011FDJG00520) not connected at session end.
FIRST THING next Stack Attack session: plug phone in and install.

---

## FILES MODIFIED THIS SESSION

```
app/src/screens/GameOverScreen.tsx  — worldId prop added to ScreenWrapper
app/src/screens/SettingsScreen.tsx  — Site Supplies section removed
app/src/screens/HomeScreen.tsx      — Daily Challenge replaced with Coming Soon badge
app/src/screens/GameScreen.tsx      — Lives removed; level text enlarged to 28px gold bold
app/src/services/AdService.ts       — NEW: interstitial every 5 levels
app/App.tsx                         — mobileAds().initialize() + preloadAds()
app/app.json                        — AdMob android_app_id added
app/android/.../AndroidManifest.xml — AdMob App ID meta-data + AdActivity theme
app/android/.../values/styles.xml   — AdActivityTheme fallback
app/android/.../values-v35/styles.xml — AdActivityTheme edge-to-edge opt-out
app/package.json                    — react-native-google-mobile-ads added
```

---

## OPEN ISSUES

- App icon: user suspects upside down. Claude viewed all mipmap PNGs — looks correct
  (tower up, figure at bottom). Needs fresh physical device check after install.
- 9 pre-existing TS errors in GameScreen.tsx — not blocking, from previous session.

---

## KEY DECISIONS

- No lives — miss = instant game over. Lives were dead design debt.
- AdMob every 5 levels — matches BatonDrop pattern, fire-and-forget.
- Daily Challenge Coming Soon — real implementation needs backend. Not worth faking.

---

## STACK ATTACK CONTEXT

60 levels across 6 worlds (10 per world). World 1 = Construction.
Gameplay: slider bounces L/R, tap to drop. Miss entirely = game over.
Special Bars system Phase 1-3 done: Ghost Line / Stone Lock / Repair Kit / Mirror / Steel Beam / Wildcard.
Leaderboard: Cloudflare Worker + D1 at stackattack-leaderboard.johnbenjaminroberts.workers.dev
Navigation: bottom tabs (Site/Build/Records/Blueprint) + BuildStack (LevelSelect→Game→GameOver)

---

## COLOUR FLOOD — NEXT PROJECT

Location: `D:AI WorkMobile-Gamesgamescolourflood`
RN 0.85.2 production app. "Colour" spelling locked (British English). v0.6, arm64-only.
READ SESSION_STATE.md in that folder before touching anything.

---

## IMPORTANT CONTEXT

- AdMob first build is slow (~17 min / 836 tasks). APK already built — do not rebuild unless changed.
- Phone serial: 38011FDJG00520. Always: adb -s 38011FDJG00520 install -r <apk>
- BatonDrop is the reference for all patterns. Publisher: ca-app-pub-4375702454097791

---

## OTHER PROJECTS

- BatonDrop: v2.0.0 submitted to Google Play 2026-05-17, awaiting review.
- CTRL Control Centre: local web app, D:AI WorkControl-Centre
- BedBouncer: bedbouncer.co.uk live, pre-Kickstarter.
- CTRLPro: planning phase, first client conversation pending.