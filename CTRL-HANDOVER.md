# CTRL Project Handover
*Last updated: 2026-05-09 (post-/afk)*
*Session ended: BatonDrop v1.7 release AAB built and ready for closed-testing upload to Play Store. John uploads manually.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up John Roberts' work-in-progress.
Read this entire document before responding when John pastes his message.

**This session was NOT CTRL development** — it was BatonDrop (mobile game) bug-fix +
release prep. The handover is structured around that. CTRL is a separate codebase that
wasn't touched today.

Repo locations:
- BatonDrop (today's work): `D:\AI Work\Mobile-Games\games\batondrop\`
- BatonDrop app (RN root): `D:\AI Work\Mobile-Games\games\batondrop\app\`
- BatonDrop backend (Cloudflare Worker): `D:\AI Work\Mobile-Games\games\batondrop\backend\`
- CTRL (untouched today): `D:\AI Work\Control-Centre\`

---

## WHAT WE WERE BUILDING THIS SESSION

Three reported BatonDrop bugs:
1. Google sign-in didn't work for new players — `signIn()` silently returning null
2. AdMob full-screen ad close-X hidden behind the system bar on Android 15+
3. HUD score "100" / "1,000" rendering as "10" / "100" (trailing digit clipped)

Root causes were independent. Fixed all three. Plus a full GCP OAuth setup that had
never been done correctly (Android OAuth clients were missing, consent screen still in
Testing). Then bumped to v1.7 / versionCode 9, added ProGuard rules so the release
build doesn't strip the new sign-in flow, built a signed release AAB.

John is uploading the AAB to Play Store closed testing manually (he has 18 testers on
that track). No production push yet — testers verify release-mode behaviour first.

---

## CURRENT BUILD STATE

### Recently completed (this session)

**Code changes:**
- `app/src/services/GoogleSignInService.ts` — `createAccount()` fallback for `noSavedCredentialFound` + diagnostic logging on failure
- `app/src/components/HUD.tsx` — removed `adjustsFontSizeToFit` from `scoreText` (the actual cause)
- `app/android/app/src/main/AndroidManifest.xml` — override AdMob `AdActivity` with `tools:replace="android:theme"` -> custom `AdActivityTheme`
- `app/android/app/src/main/res/values/styles.xml` — fallback `AdActivityTheme` for API < 35
- `app/android/app/src/main/res/values-v35/styles.xml` — `AdActivityTheme` and `AppTheme` with `android:windowOptOutEdgeToEdgeEnforcement="true"`
- `app/android/app/proguard-rules.pro` — keep rules for `androidx.credentials.*`, `com.google.android.libraries.identity.googleid.*`, `com.reactnativegooglesignin.*`, `com.google.android.gms.auth.*`
- `app/android/app/build.gradle` — versionCode 8 -> 9, versionName 1.6 -> 1.7

**GCP setup (via Playwright in a debug Chrome instance):**
- OAuth consent screen -> published to **Production** (was Testing — only test users could sign in)
- Created 3 Android OAuth clients in project `ctrl-493720`:
  - `BatonDrop Android (debug)` — SHA-1 `5E:8F:16:06:2E:A3:CD:2C:4A:0D:54:78:76:BA:A6:F3:8C:AB:F6:25`
  - `BatonDrop Android (upload)` — SHA-1 `D3:9B:4D:B5:5F:A4:8C:C7:EA:C1:78:5F:71:57:5A:2F:07:DB:62:29`
  - `BatonDrop Android (Play App Signing)` — SHA-1 `8C:69:AC:CD:C6:CB:35:D4:2C:B6:6C:DF:4C:FF:9F:FF:5A:2F:C0:C2`

**Release build:**
- `gradlew bundleRelease` succeeded. AAB at:
  `D:\AI Work\Mobile-Games\games\batondrop\app\android\app\build\outputs\bundle\release\app-release.aab`
- 124 MB, signed with the release keystore.
- John is uploading this manually to Play Console -> Closed testing.

### In progress right now
- John is uploading the AAB to Play Store closed testing.
- After testers confirm release-mode behaviour matches debug, he'll promote to Production
  from inside the closed-testing track (one click in Play Console).

### Pending / next steps
1. Verify release-mode sign-in / ads / scoring on the 18-tester closed track. ProGuard
   rules should cover everything but this is the first release with the new sign-in
   flow + AdActivity override.
2. Promote v1.7 to Production via Play Console (after a day on closed testing).
3. Verify `ctrlplay.co.uk/privacy` is reachable to Google's crawler. Older learning
   flagged a 403 from Cloudflare Bot Fight Mode — must be fixed before any future
   Production submissions Google reviews.
4. Clean up bot/ghost player accounts on the leaderboard in Turso (pre-launch report
   bots create fake players each upload).
5. WordDrop dev (untouched today) — see Mobile-Games/SESSION_STATE.md for that backlog.

---

## FILES CREATED OR MODIFIED THIS SESSION

```
app/src/services/GoogleSignInService.ts
  - createAccount() fallback for noSavedCredentialFound (first-timer flow)
  - console.warn on non-success / unexpected error so failures aren't silent
app/src/components/HUD.tsx
  - removed adjustsFontSizeToFit from scoreText (actual cause of trailing-zero clip)
app/android/app/src/main/AndroidManifest.xml
  - added <activity name="com.google.android.gms.ads.AdActivity"> with
    tools:replace="android:theme" -> @style/AdActivityTheme
app/android/app/src/main/res/values/styles.xml
  - added AdActivityTheme extending @android:style/Theme.Translucent (no opt-out
    attribute, fallback for API < 35)
app/android/app/src/main/res/values-v35/styles.xml
  - defined AdActivityTheme and AppTheme overrides with
    android:windowOptOutEdgeToEdgeEnforcement="true"
app/android/app/proguard-rules.pro
  - added keeps: reactnativegooglesignin.*, androidx.credentials.*,
    com.google.android.libraries.identity.googleid.*, gms.auth.*, gms.common.*
app/android/app/build.gradle
  - versionCode 8 -> 9, versionName "1.6" -> "1.7"
```

Knowledge files (auto-maintained):
```
D:\AI Work\Mobile-Games\SESSION_STATE.md           - overwritten with current state
D:\AI Work\Mobile-Games\LEARNINGS.md               - appended 8 new entries (2026-05-09)
D:\AI Work\Mobile-Games\skills\skill-google-signin-android.md  - NEW cross-game skill
D:\AI Work\Mobile-Games\games\batondrop\skills\skill-batondrop-workflow.md
                                                   - appended "v1.7 RELEASE FIXES" section
```

---

## OPEN ISSUES / KNOWN BUGS

- **Release AAB is unverified on device.** Debug build was tested and confirmed working
  (sign-in, ads, score all OK). Release uses ProGuard with `minifyEnabled = true`.
  Release-only regressions are possible if any class wasn't covered by the new keep
  rules. First closed-testing install is the real test.
- **Old leaderboard ghost accounts.** Multiple guest accounts with the same display name
  can exist server-side from past test installs. Doesn't break anything but is confusing
  UX. Could prune via Turso if it becomes a problem.
- **Privacy URL crawler check pending.** `ctrlplay.co.uk/privacy` previously returned 403
  to Google's crawler (Cloudflare Bot Fight Mode). Must verify before Production.

---

## KEY DECISIONS MADE THIS SESSION

- **Score fix:** drop `adjustsFontSizeToFit`, don't try to compensate with padding. The
  auto-fit was the actual cause; the natural size easily fits.
- **Ad fix:** `windowOptOutEdgeToEdgeEnforcement` on AppTheme alone is insufficient because
  AdActivity ships its own hardcoded `@android:style/Theme.Translucent`. Override with
  `tools:replace="android:theme"` in our manifest to inject a custom theme that opts out.
- **Sign-in fix:** v13+ of `@react-native-google-signin/google-signin` uses Android
  Credential Manager. `signIn()` only returns previously-saved credentials. First-timers
  need `createAccount()`. Both must be in the app code.
- **OAuth setup:** publish consent screen to Production (not Testing) for non-sensitive
  scopes. Three Android OAuth clients required — one per signing key (debug, upload,
  Play App Signing).
- **ProGuard:** the GoogleSignIn library doesn't ship a consumer-rules.pro, so consumers
  must add their own keep rules or release builds break silently.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None. The auth/google-signin endpoints already existed and were correct
(`backend/src/handlers/auth.ts` — `handleGoogleSignIn`, `handleGoogleLink`,
`handleGoogleUnlink`, plus `services/googleSignIn.ts` with `verifyGoogleIdToken` via
`oauth2.googleapis.com/tokeninfo`).

---

## DATABASE CHANGES THIS SESSION

None. The `players.google_id` column already existed.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Don't blanket-uninstall to retest.** If John reinstalls the debug APK after a release
  install (or vice versa), encrypted storage is wiped (different signing keys) and the
  guest credential is lost — new player created. With Google sign-in now working, this
  is recoverable, but it'll show as duplicate "Bob" rows on the leaderboard (one per
  abandoned guest UUID per name).
- **The ProGuard rules might not be exhaustive.** If sign-in works on debug but
  not release on the closed-testing track, look for additional `MissingClass` complaints
  in the build log and add corresponding `-keep` or `-dontwarn` lines.
- **Three different SHA-1s, three OAuth clients.** Don't accidentally collapse them or
  get them confused. Debug keystore SHA-1 is for `installDebug` builds via
  `build-phone.ps1`. Upload keystore SHA-1 is for the AAB you upload. Play App Signing
  SHA-1 is for what end users actually run on their phones (Google re-signs your AAB).
  Sign-in needs a registered Android client for whichever key signed the binary the user
  is running.
- **The `cmd.exe /c gradlew.bat ...` wrapper swallowed gradle output.** When using the
  Bash tool to run gradle on Windows, invoke `./gradlew.bat` directly (it works under
  Git Bash) instead of wrapping in cmd.
- **GCP Playwright drove the OAuth setup.** Debug Chrome was launched manually with
  `--remote-debugging-port=9222 --user-data-dir=...`, John signed in to Google Cloud
  Console + Play Console, and Playwright drove the rest. Same flow works for
  WordDrop / Cavernborn when they need OAuth setup.

---

## WHAT'S COMING UP

- **WordDrop dev** — design phase complete (GDD + brand updated 2026-05-04), no code yet.
  See `D:\AI Work\Mobile-Games\games\worddrop\` and SESSION_STATE.md for the 14-phase
  build order.
- **Cavernborn** — even earlier, RN project not initialised.
- **CTRL Centre** — not touched today, separate codebase at
  `D:\AI Work\Control-Centre\`.

---

## HOW TO START THE SYSTEM

For BatonDrop (today's work):
```
cd "D:\AI Work\Mobile-Games\games\batondrop\app"
.\build-phone.ps1     # debug APK + install on connected phone
# or
cd "D:\AI Work\Mobile-Games\games\batondrop\app\android"
./gradlew.bat bundleRelease    # signed release AAB for Play Store
```

CTRL (unchanged today):
```
D:\AI Work\START-ALL.bat
```

---

## PROJECTS OUTSIDE MOBILE GAMES (for full context)

- **CTRL** — local web app for John's working day, active build, untouched today
- **CTRLPro** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video, website live at bedbouncer.co.uk
