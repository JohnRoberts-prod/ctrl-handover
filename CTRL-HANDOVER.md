# CTRL Project Handover
*Last updated: 2026-05-10 (UTC)*
*Session ended: Just shipped Social Media module + YouTube Phase 1 (multi-channel, upload, asset management). Also fixed BedBouncer GSC sitemap and a longstanding `app.locals.db` bug. Then user hit a Veo model-not-found error — fix is to use the fast variant.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.
When John returns, he will paste in any files or context from the web session.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db` (user_version = 37)

---

## WHAT WE WERE BUILDING THIS SESSION

A full Social Media module rebuild. Started as a foundation cut (DB table + brand selector + native account placeholders). Then user pivoted: tear out all old tabs, replace with platform tabs (YouTube/Facebook/Instagram/TikTok), build YouTube first. Then expanded again: multi-channel (BedBouncer + CTRL.play), video upload, thumbnail/banner management.

Also: BedBouncer GSC sitemap discovery error — diagnosed as `robots.txt` returning 500 (file was in .gitignore so never deployed). Fixed and pushed live.

Caught a pre-existing bug: `req.app.locals.db` is referenced by 3 routes but `server.ts` never assigned `app.locals.db = db`. Surfaced when user tried Veo video generation.

Final blocker before /afk: user hit Veo `model not found` error with `veo-3.0-generate-preview` — needs to switch to `veo-3.0-fast-generate-preview` (fast variant, no audio).

---

## CURRENT BUILD STATE

### Recently completed (this session)

- **Migration v37** — `social_accounts` table (per-brand platform connections, FK to projects)
- **`social-accounts.service.ts`** — CRUD for connected platform accounts
- **YouTube multi-channel auth** (`youtube-auth.service.ts`) — vault keys `youtube_tokens__<channelId>`, separate from main Google OAuth
- **YouTube routes** — `/api/auth/youtube/{start,callback,disconnect/:channelId}` and `/api/social/youtube/*` (channels list, channel KPIs, videos, upload, update, delete, thumbnail, banner)
- **`youtube.service.ts`** — channelId-aware. Implements streaming for video upload (resumable protocol, 1GB cap), thumbnail set, two-step banner upload+update
- **Frontend Social.tsx** — rewritten, 4 platform tabs (YouTube live + 3 placeholders)
- **Hot-pink dashboard skin** — scoped to `.soc-root` so it doesn't bleed
- **YouTubeTab + sub-views** — channel pills, sub-tabs (Overview/Videos/Upload/Branding), `AddChannelModal`, XHR-based upload progress bars
- **BedBouncer robots.txt** — committed to bedbouncer-website repo, deployed live, GSC sitemap now discoverable
- **`app.locals.db = db`** one-line fix in `server.ts` — unblocks video-generation, clickup, creative routes

### In progress right now

Nothing mid-flow. Both ends typecheck clean. User is blocked on Veo model name (immediate fix: switch to `veo-3.0-fast-generate-preview`).

### Pending / next steps

1. **Veo "pro" model name lookup** — `veo-3.0-generate-preview` returns 404 from v1beta. Either Google renamed it or it requires a different API version. Hit `https://generativelanguage.googleapis.com/v1beta/models?key=...` to find the current audio-enabled Veo model and update `VEO_MODELS.pro` in `video-generation.service.ts`.
2. **Restart backend** — `app.locals.db` fix is at startup time
3. **Add CTRL.play YouTube channel** via Social Media → YouTube → Add channel (BedBouncer was added during the session and works)
4. **Phase 2 — YouTube Analytics API** for time-series charts (different scope `yt-analytics.readonly`, different host)
5. **Phase 3 — Meta integration** (FB Page + IG Business). CTRL Business Portfolio already set up. Mirror the YT multi-token pattern.
6. **Stage 9 archetype dashboards** — Pre-launch Waitlist (BedBouncer), Cash Flow, finish SEO Health Check
7. **OAuth callback error surfacing** — currently `?social_youtube=error&detail=...` lands silently on home page, ~10-min fix to add a global toast handler

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|---|---|---|---|
| Home | src/frontend/src/modules/home/ | Stable | |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Stable | |
| Gmail/Google | src/frontend/src/modules/gmail/ | Stable | Uses GoogleAuthService (single-token) |
| Tasks | src/frontend/src/modules/tasks/ | Stable | Google Tasks sync |
| Projects | src/frontend/src/modules/projects/ | Stable | Spine model, 10 projects |
| Finance | src/frontend/src/modules/finance/ | Stable | Double-entry |
| Trading | src/frontend/src/modules/trading/ | Stable | |
| **Social Media** | src/frontend/src/modules/social/ | **Active build** | Renamed from "Social". 4 platform tabs. YT live, FB/IG/TT placeholders |
| Analytics | src/frontend/src/modules/analytics/ | Stable | SQLite-first since v34 |
| Subscribers | src/frontend/src/modules/analytics/tabs/SubscribersTab.tsx | Stable | |
| GitHub | src/frontend/src/modules/github/ | Stable | |
| Brand Toolkit | src/frontend/src/modules/brand/ | Stable | |
| Design (Veo) | src/frontend/src/modules/design/ | Needs backend restart + model rename | |
| Knowledge | src/frontend/src/modules/knowledge/ | Stable | |
| Cloudflare | src/frontend/src/modules/cloudflare/ | Stable | |
| Game Shop | src/frontend/src/modules/game-shop/ | Stable | |
| BedBouncer | src/frontend/src/modules/bed-bouncer/ | Stable | ESP32 dashboard |
| Settings | src/frontend/src/modules/settings/ | Stable | |
| Admin | src/frontend/src/modules/admin/ | Stable | |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
NEW  src/backend/src/services/social-accounts.service.ts   — CRUD for social_accounts
NEW  src/backend/src/services/youtube-auth.service.ts      — per-channel OAuth tokens
NEW  src/backend/src/routes/youtube-auth.routes.ts         — /api/auth/youtube/{start,callback,disconnect}
MOD  src/backend/src/services/youtube.service.ts           — channelId-aware + upload + thumbnail + banner
MOD  src/backend/src/services/google-auth.service.ts       — added youtube.readonly scope (kept for compat)
MOD  src/backend/src/routes/social.routes.ts               — YT routes + /accounts CRUD
MOD  src/backend/src/db/migrate.ts                         — migration v37 (social_accounts)
MOD  src/backend/src/server.ts                             — mount yt-auth + app.locals.db = db

NEW  src/frontend/src/modules/social/tabs/PlatformPlaceholder.tsx
NEW  src/frontend/src/modules/social/youtube/OverviewView.tsx
NEW  src/frontend/src/modules/social/youtube/VideosView.tsx
NEW  src/frontend/src/modules/social/youtube/UploadView.tsx
NEW  src/frontend/src/modules/social/youtube/BrandingView.tsx
NEW  src/frontend/src/modules/social/youtube/AddChannelModal.tsx
NEW  src/frontend/src/modules/social/youtube/format.ts
MOD  src/frontend/src/modules/social/Social.tsx            — full rewrite, 4 platform tabs
MOD  src/frontend/src/modules/social/social.css            — hot-pink rewrite, scoped tokens
MOD  src/frontend/src/modules/social/tabs/YouTubeTab.tsx   — channel pills + sub-tabs
MOD  src/frontend/src/services/social.service.ts           — YT API + upload helpers + SocialApiError
MOD  src/frontend/src/core/nav.config.ts                   — "Social" → "Social Media"

DEL  src/frontend/src/modules/social/tabs/OverviewTab.tsx
DEL  src/frontend/src/modules/social/tabs/QueueTab.tsx
DEL  src/frontend/src/modules/social/tabs/ComposeTab.tsx
DEL  src/frontend/src/modules/social/tabs/AccountsTab.tsx
DEL  src/frontend/src/modules/social/tabs/AnalyticsTab.tsx
DEL  src/frontend/src/modules/social/NativeAccountsSection.tsx
DEL  src/frontend/src/modules/social/platformMeta.ts

NEW  D:\AI Work\Control-Centre\skills\skill-social-media.md  — module bible

NEW  D:\AI Work\BedBouncer\robots.txt                        — committed + deployed live
MOD  D:\AI Work\BedBouncer\.gitignore                        — added !robots.txt to whitelist
```

---

## RECENT GIT COMMITS

CTRL repo (`D:\AI Work\Control-Centre`) is **not** under git. Don't try `git log` there.

BedBouncer-website (`D:\AI Work\BedBouncer`):
```
639b7d5  Add robots.txt — fixes GSC sitemap discovery       (this session)
c850795  Add Meta Pixel for ad tracking
c3783ac  Remove analytics dashboard from public site — belongs in CTRL
9e4b781  Add marketing analytics dashboard and enhanced GA4 event tracking
03ce832  Migrate primary domain to bedbouncer.com, add SEO structured data and sitemap
018f401  Remove inline onclick handlers for CSP compliance
```

(Worker.js has an uncommitted simplification — removes redundant .co.uk redirect; unrelated to anything we shipped, leave for next BedBouncer session.)

BatonDrop also pushed during /afk repo-push step.

---

## OPEN ISSUES / KNOWN BUGS

1. **Veo model `veo-3.0-generate-preview` returns 404 from v1beta.** Either Google renamed it or restricted access. Workaround: use `veo-3.0-fast-generate-preview` (fast variant, no audio). Proper fix: query `/v1beta/models` and update VEO_MODELS.pro.
2. **bedbouncer.com 404→500.** Any missing static asset returns 500 instead of 404. Cause: `env.ASSETS.fetch(request)` in `worker.js` failing for missing files. Doesn't affect SEO now that robots.txt + sitemap are real files. Fix when BedBouncer worker.js is touched next.
3. **OAuth callback errors invisible.** If `/api/auth/youtube/callback` errors, redirect goes to `/?social_youtube=error&detail=<reason>` but the home page doesn't read this query param. ~10-min fix.
4. **Discord build still parked.** Waiting on John to set up Discord server + bot token + channel IDs. The skill (`skill-discord.md`) is ready.
5. **CTRLPro repo has uncommitted changes.** Auto-commit failed during /afk push step. Investigate next CTRLPro session.

---

## KEY DECISIONS MADE THIS SESSION

- **One Google OAuth = one YouTube channel.** Multi-channel = multiple OAuth tokens, stored per channelId in vault.
- **Reuse `social_accounts` table** for the channel registry. Composite unique on (brand, platform, external_id) so re-auth updates rather than duplicating.
- **Full `youtube` scope** (read+write), not `youtube.readonly`. Required for upload, thumbnail, banner.
- **Buffer integration removed from UI** but service files kept on disk. If posting becomes a need: build inside each platform tab using the native API, OR re-add Buffer as a separate sub-tab (don't put it at the top level again).
- **Hot-pink tokens scoped to `.soc-root`** only — don't pollute global tokens.css.
- **Surgical fix for `app.locals.db`** instead of refactoring the 3 affected routes to use `getDb()` directly.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
GET    /api/social/accounts                     list social_accounts (filterable by ?brand= and ?platform=)
GET    /api/social/accounts/:id
POST   /api/social/accounts
PATCH  /api/social/accounts/:id
DELETE /api/social/accounts/:id

GET    /api/social/youtube/channels             list connected YT channels
GET    /api/social/youtube/channel?channelId=   channel KPIs
GET    /api/social/youtube/videos?channelId=    recent uploads with stats
POST   /api/social/youtube/videos               multipart upload (1GB cap)
PATCH  /api/social/youtube/videos/:videoId      update title/description/tags/privacy
DELETE /api/social/youtube/videos/:videoId
POST   /api/social/youtube/videos/:videoId/thumbnail   multipart image
POST   /api/social/youtube/banner               multipart banner image

GET    /api/auth/youtube/start?brandId=         redirect to Google consent
GET    /api/auth/youtube/callback               exchange code, upsert social_accounts row
POST   /api/auth/youtube/disconnect/:channelId  revoke + delete
```

---

## DATABASE CHANGES THIS SESSION

- **Migration v37** — `social_accounts` table:
  ```sql
  id, brand_project_id (FK), platform, account_handle, external_account_id,
  vault_token_key, metadata_json, status, connected_at, last_sync_at, created_at
  -- composite unique: (brand_project_id, platform, external_account_id)
  ```
- Vault: per-channel YouTube tokens at `youtube_tokens__<channelId>` (encrypted, AES-256-GCM)

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Restart backend FIRST.** The `app.locals.db = db` fix is at startup time. Without restart, Veo video generation still 500s with "Cannot read properties of undefined (reading 'prepare')".
- **For Veo right now: use `veo-3.0-fast-generate-preview`** (fast model, no audio). The "pro" id `veo-3.0-generate-preview` returns 404 from v1beta. Look up the current pro/audio model name via `https://generativelanguage.googleapis.com/v1beta/models?key=<gemini_api_key>` next time.
- **Google Cloud OAuth client (CTRL Local, `924938588291-ip28...`)** has both redirect URIs registered: `/api/auth/google/callback` AND `/api/auth/youtube/callback`. Don't delete either.
- **YouTube quota is 10k units/day default**. Upload = 1,600 units → ~6 uploads/day total. Bulk upload sessions need a quota increase request in Cloud Console.
- **YouTube scope upgrade** (`youtube.readonly` → full `youtube`) is in `google-auth.service.ts:SCOPES` AND in the new YT-specific flow's SCOPES. The main Google connection in CTRL won't auto-pick up the new scope until John reconnects via Settings → Google.
- **BedBouncer site has a 5xx-on-missing-file bug** but `robots.txt` and `sitemap.xml` are now real files, so SEO is fine. Don't try to "fix" the symptom by removing requests; fix the worker.js binding when next touched.
- **Buffer service files (`buffer.service.ts`, `social.routes.ts:/test|/integrations|/posts|/analytics`) still exist** but no UI surfaces them. If posting becomes a need don't re-add Buffer at the top level — fold into platform tabs.
- **Veo prompt for upcoming BedBouncer/BatonDrop YouTube Short** was discussed: bouncer character + on-screen question, then BatonDrop gameplay. Settings: model fast variant, aspect `9:16`, duration `5s`. Trim to ~1.8s, append ~1.2s gameplay = 3s Short.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

---

## PROJECTS OUTSIDE CTRL (for full context)

- **BedBouncer** — ESP32 smart alarm, Kickstarter prep. Site at bedbouncer.com (live, GSC sitemap fixed today). Brand assets in `D:\AI Work\BedBouncer\files\brand\`. Two YouTube channels planned: BedBouncer (added this session) + CTRL.play (still to add).
- **CTRLPro** — hospitality SaaS dashboard, planning phase, first client conversation pending. Has uncommitted local changes (auto-commit failed).
- **Mobile Games** — BatonDrop active (auto-pushed today), WordDrop in dev, Cavernborn planning.
- **CTRL** product family — YOUR CTRL (this build, never sold) → CTRL PERSONAL (future £79/£9mo) → CTRLPRO (future £199-999/mo SaaS).
- **Lane7 hard line** — never connect any work here to John's Lane7 employer. Refer to CLAUDE.md.
