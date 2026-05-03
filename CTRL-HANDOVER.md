# BatonDrop — Session Handover
*Last updated: 2026-05-03 UTC*
*Session ended: Building feature graphic HTML tool for Play Store — user about to open it in Chrome*

---

## PROJECT CONTEXT

This is the BatonDrop mobile game — a React Native Android game being prepared for Google Play launch.

- **App code:** - **Backend:** Cloudflare Worker + Turso DB at - **Brand assets:** - **Backend URL:** https://batondrop-backend.johnroberts-prod.workers.dev
- **Play Console:** Internal testing track, v1.2 (versionCode 4) uploaded

---

## WHAT WE WERE BUILDING THIS SESSION

This was a long session covering Play Store submission setup, Google Sign-In implementation, v1.2 build, app icon update, and UI cleanup. The session ended while building an HTML feature graphic tool that composites the real BatonDrop logo and icon onto the AI-generated feature-picture.png background at 1024x500px for Play Store submission.

---

## COMPLETED THIS SESSION

### Google Sign-In (full implementation)
-  — new file, configureGoogleSignIn, googleSignIn (v13+ API), googleSignOut, AsyncStorage for linked email
-  — added linkGoogleAccount, unlinkGoogleAccount, signInWithGoogleAccount
-  — full rewrite with ACCOUNT section card: unlinked state shows Google sign-in button, linked state shows email + green dot + unlink button
-  — added configureGoogleSignIn() call, passes playerToken to OptionsScreen
- Web Client ID set: 
### Backend
-  — adds google_id TEXT column + unique index to players table
-  — verifyGoogleIdToken via Google tokeninfo endpoint
-  — handleGoogleSignIn, handleGoogleLink, handleGoogleUnlink
-  — 3 new routes: POST /api/auth/google-signin, google-link, google-unlink
-  — GOOGLE_WEB_CLIENT_ID added to Env interface
- GOOGLE_WEB_CLIENT_ID Cloudflare secret set, migration 011 applied to Turso, backend deployed

### Migration tooling
-  — rewritten to track applied migrations via _migrations table
-  — one-time script to backfill _migrations with 001-010

### App build
-  — versionCode 4, versionName 1.2 (versionCode 3 was already consumed)
-  — version text updated to v1.2
- All mipmap-* folders updated with IconKitchen icons

### UI cleanup
-  — DEV button wrapped in __DEV__ check (hidden in release)
-  — name entry prompt removed entirely; hasName/playerName/onSaveName removed; RankRevealState simplified
-  — removed getPlayerName/setPlayerName imports, lbPlayerName state, onSaveName wiring
-  — added YOU amber badge tag next to current player's row; added nameCell wrapper style
-  — UnlockModal removed entirely (import, state, handlers, JSX); UnlockedTier type import removed; progression still silently gates HowToPlay cards

### Feature graphic
-  — HTML design tool at 1024x500px with real logo, icon, two phone mockups using real screenshots
- Screenshots copied: screenshot-gameplay.png, screenshot-gameover.png to ui/ folder

---

## IN PROGRESS / NEXT STEPS

1. **Android OAuth client** — needs creating in Google Cloud Console (type: Android, package name: com.batondrop, SHA-1 from Play Console → your app → Setup → App signing → SHA-1 certificate fingerprint). Without this, Google Sign-In won't work on a real device.

2. **Test Google Sign-In** — rebuild app after Android OAuth client created, test link/unlink flow on real device.

3. **Feature graphic** — open  in Chrome, check the layout (original AI image as background with real logo + icon overlaid + two phone mockups with real screenshots), then hit Download PNG. Upload to Play Store → Store listing → Feature graphic.

4. **Fix ctrlplay.co.uk/privacy 403** — Cloudflare Bot Fight Mode is likely blocking Google's crawler. Check Security → Bots in Cloudflare dashboard. Play Store rejects URLs it can't access.

5. **Add /delete-account page** to ctrlplay.co.uk — Google requires a dedicated URL explaining how to request account deletion, not just the privacy policy.

6. **Send for Play Store review** — once store listing assets are complete (screenshots, feature graphic, icon uploaded, privacy URL working, delete account URL working).

7. **Clean up fake bot accounts** — players named KXTYQH and player1 were created by Google's pre-launch report bots when the internal test AAB was uploaded. Delete from Turso directly.

---

## OPEN ISSUES

- Google Sign-In not yet end-to-end tested (Android OAuth client missing)
- ctrlplay.co.uk returns 403 to Google — blocks Play Store submission
- Fake player accounts (KXTYQH, player1) on leaderboard from pre-launch bots
- Phone lost coins when release build installed over debug — one-time issue (encrypted storage cleared on signing key change), not a bug

---

## ARCHITECTURE DECISIONS

- Google Sign-In: @react-native-google-signin/google-signin (standard, not GPGS). ID tokens verified server-side via Google's tokeninfo endpoint — works in Cloudflare Workers without JWKS complexity.
- Guest UUID stays as player primary key. google_id is a separate column. Cross-device recovery looks up by google_id.
- Email stored only in AsyncStorage locally — not sent to backend.
- UnlockModal removed permanently — too annoying, unlocks are silent.
- Leaderboard name entry removed — signup display name is used everywhere, no per-score name entry.
- __DEV__ flag gates debug UI in MainMenuScreen.

---

## IMPORTANT FILE LOCATIONS

\
---

## HOW TO RUN

Backend (local dev): App (Metro): App (emulator): Release build: Deploy backend: Run migration: 