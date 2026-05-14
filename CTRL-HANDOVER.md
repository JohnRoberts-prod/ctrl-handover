# CTRL Project Handover
*Last updated: 2026-05-14 UTC*
*Session ended: After building Vertex AI as a switchable alternative provider to Google AI Studio for Gemini image gen + Veo video gen.*

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

This session had two main threads of work, both centred on the Google/Gmail tab and the Gemini API:

**Part 1 — Gmail tab performance + reliability fixes.** The "initial open is slow" problem was a server-side `COUNT(*)` query running on every list fetch; dropped that + reduced initial page size 75→25 + debounced SSE-triggered refetches. Stopped backlog status polling when idle. Made the Gmail tab stay mounted across tab switches (display:none pattern from the Claude tab) so returning to Google doesn't re-fetch from scratch.

**Part 2 — Vertex AI integration.** John has £225 of unused GCP Free Credit expiring 2026-07-23, but it doesn't apply to the AI Studio Gemini API (`generativelanguage.googleapis.com`). Solution: add Vertex AI as a switchable alternative provider. Built it as a manual per-service toggle (Gemini, Veo) with vault-stored service account credentials, OAuth via `google-auth-library`, and a Settings UI section to flip between providers + initial vault setup form. Plus added retry-with-backoff to all Gemini API calls so 503s auto-recover (was triggered by repeated 503 errors mid-session — Gemini's image preview model is unstable).

---

## CURRENT BUILD STATE

### Recently completed (this session)

1. **Gmail/Email perf fixes** in `src/backend/src/routes/email-intelligence.routes.ts` and `src/frontend/src/modules/gmail/Gmail.tsx`:
   - Dropped `COUNT(*)` from `/api/email/list`. Fetches `limit + 1`, derives `hasMore` from row count. Response shape changed from `{ emails, total, nextOffset }` to `{ emails, nextOffset }`.
   - Initial page size 75 → 25.
   - SSE refetches debounced 1.5s.
   - Backlog polling now only runs when `backlog?.status === 'running'` (was polling forever every 3s).

2. **Gmail tab stays mounted** in `src/frontend/src/core/AppShell.tsx`:
   - Wrapped `<PageTerminal><Gmail /></PageTerminal>` in a `display: activeTab === 'google' ? 'flex' : 'none'` div, mirroring the Claude tab pattern at lines 70-73.
   - Trade-off documented: SSE listener stays connected in background, but that's a feature not a bug.

3. **Gemini API retry logic** in `src/backend/src/services/creative.service.ts` `geminiGenerateImage`:
   - 4 attempts (1 initial + 3 retries) on 429/500/502/503/504/network errors
   - Exponential backoff 1s → 2s → 4s
   - Honours `Retry-After` header if present
   - Logs each retry: `[Gemini/{provider}] {status} on attempt {n}/4, retrying in {delay}ms`

4. **Vertex AI integration** (NEW FILES + EDITS):
   - `src/backend/src/services/vertex-auth.service.ts` (NEW) — OAuth via `google-auth-library@10.6.2`. Reads service account JSON, project ID, region from vault. JWT cached after first load. Exports `getVertexContext()`, `vertexModelUrl()`, `isVertexConfigured()`, `resetVertexAuth()`.
   - `src/backend/src/services/ai-provider.service.ts` (NEW) — reads/writes `aiProvider.gemini` and `aiProvider.veo` in `.ctrl-config.json`. Defaults both to 'aistudio'.
   - `src/backend/src/services/creative.service.ts` — `geminiGenerateImage` now dispatches by provider. API key param removed (function fetches it internally when on aistudio path). Callers `generateSprite` and `generateImg2Img` simplified — no more API key lookup at the call site.
   - `src/backend/src/services/video-generation.service.ts` — `buildVeoAuth()` helper builds provider-aware submit/poll URLs + headers. Veo response shape handles three cases: AI Studio URI, Vertex inline base64, Vertex GCS URI (the last throws "not supported"). Helper `computeOutputPath()` shared between download paths. `saveVeoBytes()` writes inline base64 directly to disk.
   - `src/backend/src/routes/ai-providers.routes.ts` (NEW) — `GET /api/ai-providers` returns config + vertexReady status; `POST /api/ai-providers/:service` flips a provider.
   - `src/backend/src/server.ts` — wires the new router at `/api/ai-providers` behind `requireVaultUnlocked`.
   - `src/frontend/src/services/ai-providers.service.ts` (NEW) — typed API client.
   - `src/frontend/src/modules/settings/components/AIProvidersSection.tsx` (NEW) — Settings UI: Vertex setup status, inline form to paste service account JSON / project ID / region (writes 3 vault keys via `vaultApi.setKey`), per-service toggle buttons (locked when Vertex not configured), tips reminding about the £225 credit.
   - `src/frontend/src/modules/settings/Settings.tsx` — wired new section + icon (Cpu).

5. **Type checks clean** on both backend and frontend (`tsc --noEmit`).

### In progress right now
Nothing half-built — all changes are coherent and type-check clean.

### Pending / next steps when John returns
1. **Restart backend:** `pm2 restart ctrl-backend` — the retry logic AND Vertex AI integration both require restart (PM2 has `watch: false`).
2. **Set up Vertex in Settings → AI Providers:**
   - Cloud Console → IAM → Service Accounts → create with role "Vertex AI User"
   - Keys → JSON → paste into Settings UI
   - Project ID: `ctrl-493720`
   - Region: `europe-west2`
3. **Flip Gemini → Vertex AI** and generate a sprite. Watch the £225 credit balance start ticking down at console.cloud.google.com → Billing → Credits.
4. **Test Veo on Vertex (optional)** — works for inline base64 (8-second clips under ~20MB). Larger videos need GCS support which isn't implemented yet.

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | working | (untouched this session) |
| Claude Tab | src/frontend/src/modules/claude-tab/ | working | (untouched this session) |
| Gmail/Google | src/frontend/src/modules/gmail/ | **improved this session** | perf fixes, stays mounted across tab switches |
| Tasks | src/frontend/src/modules/tasks/ | working | (untouched) |
| Projects | src/frontend/src/modules/projects/ | working | (untouched) |
| Finance | src/frontend/src/modules/finance/ | working | (untouched) |
| Trading | src/frontend/src/modules/trading/ | working | (untouched) |
| GitHub | src/frontend/src/modules/github/ | working | (untouched) |
| Cloudflare | src/frontend/src/modules/cloudflare/ | working | (untouched) |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | working | indirectly affected: brand image generation flows through `creative.service.ts` which can now use Vertex |
| Settings | src/frontend/src/modules/settings/ | **new section added** | AI Providers section + vault setup form |
| Admin | src/frontend/src/modules/admin/ | working | (untouched) |
| Design | src/frontend/src/modules/design/ | indirectly affected | sprite/img2img both go through `creative.service.ts` — Vertex toggle applies here |
| Video gen | src/frontend/src/modules/video-generation/ | indirectly affected | Veo provider toggle applies here |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
NEW FILES
src/backend/src/services/vertex-auth.service.ts
    OAuth wrapper around google-auth-library. Loads service account JSON from vault,
    mints + caches access tokens. Exports getVertexContext, vertexModelUrl, isVertexConfigured.

src/backend/src/services/ai-provider.service.ts
    Reads/writes aiProvider.{gemini,veo} in .ctrl-config.json. Defaults to 'aistudio'.

src/backend/src/routes/ai-providers.routes.ts
    GET / POST /api/ai-providers — read status + flip per-service provider.

src/frontend/src/services/ai-providers.service.ts
    Typed API client for the routes above.

src/frontend/src/modules/settings/components/AIProvidersSection.tsx
    Settings UI: Vertex status, inline vault setup form, per-service toggles, tips.

skills/skill-ai-providers.md
    Full reference doc for the AI provider switch.

MODIFIED FILES
src/backend/src/services/creative.service.ts
    Added retry-with-backoff loop. Added provider-aware dispatch for geminiGenerateImage.
    Removed apiKey param from internal function — now fetched inside on aistudio path.

src/backend/src/services/video-generation.service.ts
    Added buildVeoAuth helper for provider-aware URL+headers.
    Handles 3 response shapes: AI Studio URI, Vertex base64 inline, Vertex GCS (errors).
    Shared computeOutputPath helper. New saveVeoBytes for inline writes.
    Removed dead getApiKey function.

src/backend/src/routes/email-intelligence.routes.ts
    /api/email/list: dropped COUNT(*), fetch limit+1, return { emails, nextOffset }.

src/backend/src/server.ts
    Wired aiProvidersRouter at /api/ai-providers.

src/frontend/src/modules/gmail/Gmail.tsx
    Removed emailTotal state. Page size 75->25. Footer button text simplified.
    SSE refetches debounced 1.5s. Backlog polling gated on status==='running'.

src/frontend/src/core/AppShell.tsx
    Gmail tab now stays mounted via display:none pattern (matches Claude tab).

src/frontend/src/services/email-intel.service.ts
    listEmails return type: removed `total` field.

src/frontend/src/modules/settings/Settings.tsx
    Wired AIProvidersSection + Cpu icon + 'ai-providers' section.

DEPENDENCY
src/backend/package.json
    Added: google-auth-library@10.6.2
```

---

## RECENT GIT COMMITS

`D:/AI Work/Control-Centre` is NOT a git repo. All session work lives only on local disk. The linked project repos (BedBouncer, BatonDrop) that ARE git repos were auto-committed and pushed at /afk time.

---

## OPEN ISSUES / KNOWN BUGS

1. **Veo on Vertex with GCS storage not implemented.** If a future Veo call ever returns a `gcsUri` (which would only happen if `parameters.storageUri` is set in the request — which we never do), the path throws "Veo returned a GCS URI; download from gs:// is not yet supported". For now this is impossible to hit because we never set `storageUri`. Future enhancement only if users want videos > ~20MB.

2. **First Vertex call after backend restart is slow** (1-2s extra) — JWT must sign + exchange for OAuth token. Subsequent calls reuse cached token. Not a bug, just a one-time latency.

3. **Existing 503 issue from earlier in the session** — `gemini-3.1-flash-image-preview` is genuinely overloaded on Google's side. Retry logic will mask most cases now, but if all 4 attempts fail it's a real upstream outage. Moving to Vertex AI for Gemini may have lower 503 rate (less contention) but isn't guaranteed.

4. **Tier 1 of Gemini API limits still apply** until John has spent $100 USD cumulative across both AI Studio AND Vertex. Higher tier = higher rate limits. The £225 credit doesn't accelerate this — tier is based on spend, not credit.

---

## KEY DECISIONS MADE THIS SESSION

1. **Manual provider switch over auto-fallback or smart cost routing.** Reason: simpler to reason about which provider actually ran each call. User asked for this explicitly via AskUserQuestion.

2. **Inline base64 response for Vertex Veo, not GCS.** Avoids needing a Cloud Storage bucket setup. 8-second 720p clips fit under the 20MB inline limit.

3. **`.ctrl-config.json` for the provider flag, not the database.** It's a simple boolean-ish flag with no relational ties. Direct JSON read/write is fine.

4. **`google-auth-library` (official) over hand-rolling JWT+OAuth.** ~50 lines of fiddly RSA-SHA256 + token caching avoided. v10.6.2 returns `Headers` object from `getRequestHeaders()`.

5. **Removed `apiKey` parameter from `geminiGenerateImage`.** Function now fetches from vault internally on aistudio path. Cleaner call sites.

6. **PM2 `watch: false` stays as-is.** Code changes require manual `pm2 restart ctrl-backend`. Hot reload would risk mid-request restarts.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
GET  /api/ai-providers              -> { config: { gemini, veo }, vertexReady: boolean }
POST /api/ai-providers/gemini       -> body { provider: 'aistudio' | 'vertex' }
POST /api/ai-providers/veo          -> body { provider: 'aistudio' | 'vertex' }
```

All three require vault unlocked (mounted behind `requireVaultUnlocked`).

The existing `/api/email/list` route changed its response shape:
- Old: `{ emails, total, nextOffset }`
- New: `{ emails, nextOffset }` — `total` field removed

Only the Gmail.tsx frontend consumes this and was updated in lockstep.

---

## DATABASE CHANGES THIS SESSION

None. No migrations, no schema changes.

---

## CONFIG FILE CHANGES

`.ctrl-config.json` gains an `aiProvider` key on first toggle:

```json
{
  "aiProvider": {
    "gemini": "aistudio",
    "veo":    "aistudio"
  }
}
```

Values: `'aistudio'` or `'vertex'`. Default is `'aistudio'` if the key is missing.

---

## VAULT KEYS ADDED (CONCEPTUALLY)

Three new vault keys exist conceptually — they're set via the Settings UI when user runs through the Vertex setup form:

| Key | Type | Purpose |
|-----|------|---------|
| `vertex_service_account` | JSON string | Full GCP service account key file content |
| `vertex_project_id` | string | e.g. `ctrl-493720` (the ID string, NOT the project number) |
| `vertex_region` | string | e.g. `europe-west2` (UK) or `us-central1` (widest model coverage) |

None of these are written until user goes through the form. The `isVertexConfigured()` check requires all three.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. **Backend must be restarted to load any of this.** Both the retry logic and the Vertex integration are dormant until `pm2 restart ctrl-backend` is run. John was warned about this in chat.

2. **The retry logic ALONE was added before the full Vertex integration.** Even if user doesn't want to set up Vertex, the retry-with-backoff against AI Studio will help recover from the 503s that triggered this whole thread.

3. **The Settings UI lock behaviour:** when Vertex is not configured (`vertexReady: false`), the per-service toggle buttons are disabled. User MUST set up Vertex via the form before they can flip a toggle. Prevents flipping to a non-functional state.

4. **Cavernborn Comedy Baton video prompts** — earlier in the session John was reviewing Veo prompts from `D:\AI Work\Mobile-Games\games\batondrop\files\brand\documents\CAVERNBORN-COMEDY-PROMPTS.md`. Prompts 1, 2, and 3 were extracted with settings. The character animation/expression issues he flagged on the first videos suggest the production notes' suggestions are needed: `"exaggerated comedy expression"` and `"the expression holds for 2 full seconds"` added to prompts. He'd planned to send stills for visual critique.

5. **The skill file `skills/skill-ai-providers.md`** is the authoritative reference for the new integration. Read it before touching anything related to Gemini API calls, Vertex AI, or AI provider switching.

6. **No git history exists for Control-Centre.** Session work is on disk only. If anything goes wrong, there's no rollback — just edit forward.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or via PM2:
- `pm2 start D:\AI Work\Control-Centre\ecosystem.config.cjs`
- `pm2 restart ctrl-backend` (after code changes)
- `pm2 status` to see all three processes (ctrl-backend, ctrl-terminal, ctrl-frontend)

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLPro** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, website live at bedbouncer.co.uk, needs product video
- **Mobile Games:**
  - **BatonDrop** — drop-reflex casual game (React Native), v1.7 shipped, in development
  - **WordDrop** — word puzzle idle game (React Native 0.85.2), GDD complete, ready to build
  - **Cavernborn** — dark fantasy idle RPG (React Native + Skia + Reanimated), planning, RN project not yet initialised
- **Personal** — AFK / dev environment / handover system

---

*This handover doc was generated by Claude Code at session end. The user types `/afk` and this file is pushed to GitHub at `JohnRoberts-prod/ctrl-handover` for Claude web to fetch.*
