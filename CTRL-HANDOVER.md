# CTRL Project Handover
*Last updated: 2026-05-12 — end of session*
*Session ended: just deployed a CTRL backend fix for Gmail-tab unresponsiveness; backend has NOT been restarted yet so the fix is on disk but not live.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.
When John returns, he will paste in any files or context from the web session.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

---

## WHAT WE WERE BUILDING THIS SESSION

This was a *two-project* session. Most of it was BatonDrop (mobile game, separate
codebase at `D:\AI Work\Mobile-Games\games\batondrop\`). CTRL came in at the end.

**BatonDrop** — shipped the BATONDROP-AUTH-REBUILD: deployed the v2 auth Cloudflare
worker (replaces Google-on-every-launch with local-UID-as-source-of-truth + Google
as recovery). Fixed several bugs that surfaced from emulator testing. Built signed
AAB v20, ready for Play Console upload.

**CTRL** — diagnosed a Gmail-tab "emails not showing / opening crashes CTRL"
report. Root cause: `EmailIntelService.enrichPending` was a synchronous loop over
1000 emails after every full Gmail sync, blocking the Node event loop for
~10 seconds at a time. During those windows every HTTP endpoint timed out.
Fixed: made async, yields every 25 iterations.

---

## CURRENT BUILD STATE

### Recently completed (this session)

**CTRL:**
- `src/backend/src/services/email-intel.service.ts` — `enrichPending` now async,
  awaits `setImmediate` every 25 iterations.
- `src/backend/src/services/sync/gmail-sync.service.ts` — caller updated to handle
  the now-async return (with `.catch` for safety).

**BatonDrop (separate codebase):**
- `backend/src/handlers/auth.ts` — `isValidUid` regex relaxed from
  `/^[a-zA-Z0-9\-]{8,64}$/` to `/^[a-zA-Z0-9_:\-]{8,128}$/` to accept legacy
  `guest:xxx` and `google:sub` IDs at /login-uid.
- Worker deployed to production (versions 02ad0888-… then 72ff260e-…).
- `app/src/auth/AuthManager.ts` — `email: linkRes.email ?? result.email` →
  `email: linkRes.email || result.email`. The `??` was preventing fall-through
  on empty string (the bug behind "first sign-in doesn't show as linked").
- `app/src/screens/ShopScreen.tsx` — `ready` check now also true when
  `id.startsWith('default_')`. Three of four `default_*` items have R2
  asset_keys but the game has bundled fallbacks regardless, so the shop was
  showing GET on items the user clearly already had.
- `app/android/app/build.gradle` — `versionCode 18 → 19 → 20`, versionName
  stays at `1.9.6`.
- Signed AAB built at
  `app/android/app/build/outputs/bundle/release/app-release.aab` (~130 MB).

### In progress right now

- **CTRL backend has NOT been restarted** since the enrichPending fix was made.
  John needs to restart it to verify the Gmail tab is now responsive.
- **BatonDrop emulator is still running v19** (with the bugs above). The v20
  AAB is built, but `installRelease` was interrupted (John switched to dealing
  with CTRL).
- **AAB v20 has NOT been uploaded to Play Console yet.**

### Pending / next steps

1. (CTRL) Restart backend, open Gmail tab, verify labels and messages load
   promptly. The cache architecture is correct — read path was never the
   bottleneck, just enrichment was.
2. (BatonDrop) `cd D:\AI Work\Mobile-Games\games\batondrop\app\android &&
   ./gradlew installRelease` to put v20 on the emulator. Test:
   first-time Google Sign-In on Options shows linked badge immediately (no
   second press); Shop default items show ACTIVE/USE not GET.
3. (BatonDrop) Upload `app-release.aab` to Play Console closed-testing.
4. (BatonDrop pre-existing) Diagnose leaderboard scoring not updating after
   runs — was the previous session's open issue, hasn't been touched.

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | working | |
| Claude Tab | src/frontend/src/modules/claude-tab/ | working | |
| Gmail/Google | src/frontend/src/modules/gmail/ | **fix pending restart** | enrichPending event-loop block fixed; need to restart backend |
| Tasks | src/frontend/src/modules/tasks/ | working | |
| Projects | src/frontend/src/modules/projects/ | working | |
| Finance | src/frontend/src/modules/finance/ | working | |
| Trading | src/frontend/src/modules/trading/ | working | not touched |
| GitHub | src/frontend/src/modules/github/ | working | not touched |
| Cloudflare | src/frontend/src/modules/cloudflare/ | working | not touched |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | working | not touched |
| Settings | src/frontend/src/modules/settings/ | working | not touched |
| Admin | src/frontend/src/modules/admin/ | working | not touched |

---

## FILES CREATED OR MODIFIED THIS SESSION

CTRL:
```
src/backend/src/services/email-intel.service.ts — enrichPending now async + yields setImmediate every 25 iterations
src/backend/src/services/sync/gmail-sync.service.ts — caller handles async return with .catch
SESSION_STATE.md — overwritten
LEARNINGS.md — appended
```

BatonDrop (separate repo at `D:\AI Work\Mobile-Games\games\batondrop\`):
```
backend/src/handlers/auth.ts — isValidUid regex now accepts ':' and '_'
app/src/auth/AuthManager.ts — `||` not `??` for email fallback in linkGoogleAccount return
app/src/screens/ShopScreen.tsx — ready check includes id.startsWith('default_')
app/src/services/GoogleSignInService.ts — touched then reverted (had TS issues, original code is back)
app/android/app/build.gradle — versionCode 18 → 19 → 20
SESSION_STATE.md — overwritten
LEARNINGS.md — appended
```

Built artifact:
```
D:\AI Work\Mobile-Games\games\batondrop\app\android\app\build\outputs\bundle\release\app-release.aab
(versionCode 20, versionName 1.9.6, ~130 MB, signed with release keystore)
```

---

## RECENT GIT COMMITS

CTRL has no .git in `D:\AI Work\Control-Centre\` itself.

BatonDrop app repo (`D:\AI Work\Mobile-Games\games\batondrop\app\`):
```
cf7643c Auto-backup: 2026-05-12     ← /afk just pushed this with today's changes
29a6cb0 v1.9.5: auth flow gated, Skia GameText migration, UI polish
f060aba Auto-backup: 2026-05-09
845778a v1.5: lane scaling fix + leaderboard investigation
d637f8a Auto-backup: 2026-05-03
b2730ef Checkpoint: colour fix, debug modal groups, package installs
e4e746c Guard handleBatonExit against stale animation callbacks
ae6a2a8 Fix chain: droppedLaneRef tracks replacement lane across all baton-done paths
d977a2a Redesign drop logic: chain-based spawning, sequential startup, bouncer fix
61fa63d WIP: pre-redesign checkpoint — backup before drop logic overhaul
```

BatonDrop backend (Cloudflare Worker) — deployed twice this session:
```
72ff260e-edd9-448c-ac89-4ccb5d345588   ← current production (with regex fix)
02ad0888-5a15-4247-846a-a1b66332fda1   ← initial v2 deploy (before regex fix)
```

---

## OPEN ISSUES / KNOWN BUGS

1. **`EmailCacheService.listLabels()` returns `{unread: 0, total: 0}` for every
   label** (cosmetic, no per-label counter query yet). If user complains about
   missing unread badges in Gmail, this is why.

2. **`GoogleSignInService.ts` has two pre-existing TypeScript errors** in the
   BatonDrop app: the `noSavedCredentialFound` branch references
   `GoogleSignin.createAccount`, which only exists on `GoogleOneTapSignIn` per
   the v16 type defs. Code apparently works at runtime — the createAccount
   fallback may simply never be hit because `signIn()` (Original Google Sign
   In) presents the picker every time. Not investigated further; not currently
   a user symptom.

3. **Backend still has live `/api/auth/google-signin` legacy route** in
   BatonDrop, which creates `google:sub` player IDs with colons — that's why
   the regex fix was needed today. Per comment in handlers/auth.ts:209-211 the
   legacy routes "will be removed in a follow-up release once v1.9.6 is
   stable". Don't remove yet; closed-testing players on v1.9.5 still hit them.

4. **BatonDrop leaderboard scoring not updating after runs** — pre-existing,
   from previous session, not investigated this session. Most likely cause per
   prior notes: `startRun()` failing silently → `sessionTokenRef.current`
   stays null → backend submission skipped.

---

## KEY DECISIONS MADE THIS SESSION

- **CTRL: never block the Node event loop.** Standard pattern for any large
  sync-DB loop in CTRL backend is now `await new Promise(r => setImmediate(r))`
  every 25 iterations. better-sqlite3 is synchronous; without yields, the
  event loop is fully blocked and every HTTP request times out. Apply this
  pattern proactively to any future bulk-DB pass.

- **CTRL: don't refactor the Gmail read path.** Routes already correctly read
  from `EmailCacheService` (local SQLite). The architecture is what John
  expected. The bottleneck was always enrichment.

- **BatonDrop: backend-only fix for "Invalid uid" / recover / switch.** The
  regex change unlocked all three flows without needing a new app build.

- **BatonDrop: Shop default items always-ready beats auto-download.** First
  attempt was to auto-download owned-but-not-on-disk assets. John clarified
  the game already renders defaults from bundled fallbacks, so downloading is
  wasteful. Reverted to a one-line `id.startsWith('default_')` check.

- **BatonDrop: bump versionCode pre-emptively.** Bumped 18 → 19 → 20 across
  two app builds today. "When unsure if a versionCode has been uploaded to
  Play, bump to be safe" is the standing rule (Play rejects re-upload).

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None added in CTRL.

In BatonDrop backend, the v2 routes (`/api/auth/create`, `/login-uid`,
`/link-google-v2`, `/recover-google`, `/switch-account`) were already in
source from the previous session — this session's work was deploying them
and fixing the regex that gated `/login-uid` and downstream calls.

---

## DATABASE CHANGES THIS SESSION

None.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **The CTRL backend fix is on disk but NOT LIVE.** Run a backend restart
  before doing anything Gmail-related, or you'll see the same hangs.

- **The BatonDrop AAB v20 is built and signed but NOT uploaded.** It sits at
  `app/android/app/build/outputs/bundle/release/app-release.aab`. John wants
  to install on the emulator first, verify the Sign-In + Shop fixes, then
  upload to Play Console closed-testing. Suggested release notes: "Fixes
  account-loading issues caused by Google Sign-In running on every launch.
  Your account is now tied to this device by default. Link Google in Options
  to back it up across devices."

- **The BatonDrop emulator is still on v19.** Running tests on the emulator
  RIGHT NOW would test the OLD code, not the fixes from this session.

- **BatonDrop's worker deploy URL is
  `batondrop-backend.johnbenjaminroberts.workers.dev`.** It's already on the
  fixed version (72ff260e). No backend redeploy needed for the next session
  unless new backend changes go in.

- **`wrangler deploy --dry-run` validates the build but does NOT publish.**
  Caused 30s of confusion this session ("I deployed it but the worker still
  returns 404"). Always run `npm run deploy` (no flag) for the real ship.

- **BatonDrop has a pre-existing TS error in GoogleSignInService.ts.** Don't
  panic if you see it — the file ships fine because Babel doesn't strict-check
  TS. If you need to touch that file, look up the proper One-Tap APIs in
  `@react-native-google-signin/google-signin` v16 type defs first.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

For BatonDrop emulator install:
- `cd D:\AI Work\Mobile-Games\games\batondrop\app\android && ./gradlew installRelease`

For BatonDrop backend redeploy:
- `cd D:\AI Work\Mobile-Games\games\batondrop\backend && npm run deploy`

---

## PROJECTS OUTSIDE CTRL (for full context)

- **BatonDrop** — mobile game, active build, AAB v20 ready for Play upload.
  Main focus of today's session. Workers deployed, app build ready.
- **CTRLPro / UnifyBI** — hospitality SaaS dashboard, planning phase, first
  client conversation pending.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video.
- **Mobile Games (other)** — WordDrop, Cavernborn — planning/early dev.
