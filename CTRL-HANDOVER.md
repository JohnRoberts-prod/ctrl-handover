# CTRL Project Handover
*Last updated: 2026-06-19 UTC*
*Session ended: just re-ran the CTRL codebase audit and listed remaining work; /afk called.*

---

## HOW TO USE THIS DOCUMENT
You are Claude web picking up a CTRL session. John Roberts is the developer. Read it all first.
CTRL: `D:\AI Work\Control-Centre\` — Backend Node/Express/TS :3001 · Frontend React/Vite :5173 · Terminal node-pty :3002 · SQLite `D:\AI Work\.ctrl-data.db`.
Full live backlog: `D:\AI Work\TASKS.md`. Session state: `Control-Centre\SESSION_STATE.md`.

---

## WHAT WE WERE BUILDING THIS SESSION
Mostly **Reframe AI Shorts production** (a big methodology overhaul) plus **CTRL email module** work and a **codebase audit + fixes**. Core CTRL changes: email **priority scoring (P1-P4)**, a **vault dev-lock fix**, a **vitest test suite**, and doc/route tidy-up. No new UI modules; changes were backend + skills/docs.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- **Email priority (P1-P4)** — derived on READ via SQL CASE in `routes/email-intelligence.routes.ts` (`?priority=`, `?sort=priority`). Zero tokens. P1 = `has_action AND (is_important OR is_starred)`.
- **Inbox cleanup** — trashed 282 notification emails via per-id PATCH (bulkTrashBySenders is broken — see issues).
- **Tests** — `src/backend/test/vault.test.ts` + `finance.test.ts`, 10 passing (`cd src/backend && npm test` / `npx vitest run`).
- **Vault dev-lock** — `VaultService.tryDevAutoUnlock()` auto-unlocks on boot from `VAULT_DEV_PASSWORD` (gitignored `src/backend/.env`, currently BLANK placeholder).
- **Border-string dedup** — one `BORDER_SUFFIX` constant in `creative.service.ts`.
- **Cleanup** — 39 one-off `.cjs` → `scripts/oneoff/`; STATUS.md refreshed; routes documented in `knowledge/recent-backend-additions.md`.
- **Reframe AI** — rewrote skill-til-info-video.md, skill-reframe-ai-workflow.md, design/reframe-ai-channel.md (v3), CLAUDE.md Reframe section → all video-first/hook-engine.

### In progress / next steps
1. **Roger Milla** Short — test clip + 3-voice audition done (Drive); needs voice pick + video-first build.
2. **Split `Design.tsx`** (1186 lines) — the one giant file worth it (migrate.ts deferred: DB-corruption risk).
3. **Email draft-reply (part 2)** — wire `draft-response` to `gmail_*` tools (priority scoring was part 1).
4. **Maradona → YouTube**; **Costco re-cut** (clean + hook engine).
5. More tests: email SAFE-MODE (auto-classify never trashes), double-entry zero-sum once built.

---

## ALL MODULES — STATUS
| Module | Location | Status | Notes |
|---|---|---|---|
| Home | src/frontend/src/modules/home/ | stable | |
| Claude Tab | src/frontend/src/modules/claude-tab/ | stable | |
| Gmail/Email | src/frontend/src/modules/gmail/ | active | priority scoring added backend; UI could surface P1 |
| Tasks | src/frontend/src/modules/tasks/ | stable | Google Tasks |
| Projects | src/frontend/src/modules/projects/ | stable | |
| Finance | src/frontend/src/modules/finance/ | building | pence-integer; double-entry not built yet |
| Trading | src/frontend/src/modules/trading/ | stable | |
| GitHub | src/frontend/src/modules/github/ | stable | |
| Cloudflare | src/frontend/src/modules/cloudflare/ | stable | |
| Brand/Design | src/frontend/src/modules/design/ | active | Design.tsx 1186 lines — split candidate |
| Settings | src/frontend/src/modules/settings/ | stable | vault unlock lives here |
| Admin | src/frontend/src/modules/admin/ | stable | |

---

## FILES CREATED OR MODIFIED THIS SESSION
```
src/backend/src/routes/email-intelligence.routes.ts — added derived priority (CASE), ?priority=, ?sort=priority
src/backend/src/services/vault.service.ts          — added tryDevAutoUnlock()
src/backend/src/server.ts                          — call tryDevAutoUnlock on listening; import VaultService
src/backend/src/services/creative.service.ts       — BORDER_SUFFIX constant (deduped x3)
src/backend/src/services/video-generation.service.ts — (earlier) Veo personGeneration allow_all/allow_adult
src/backend/test/vault.test.ts, finance.test.ts    — new (10 tests); vitest.config.ts; package.json test scripts
src/backend/.env                                   — VAULT_DEV_PASSWORD= placeholder (blank)
STATUS.md, knowledge/recent-backend-additions.md, knowledge/cowork-plugins-integration.md — docs
skills/ (5 new Cowork-adapted), SESSION_STATE.md, LEARNINGS.md — knowledge
scripts/oneoff/ — 39 moved .cjs ; .gitignore — stray .db
```

---

## RECENT GIT COMMITS
Control-Centre is NOT a git repo (no history). Changes are on disk only — consider `git init` + backup.

---

## OPEN ISSUES / KNOWN BUGS
- `VAULT_DEV_PASSWORD` is a BLANK placeholder — vault still locks on every backend edit until John sets it.
- `bulkTrashBySenders` (cache/email-cache.service) returns 0: it JOINs `email_labels` label_id='INBOX' which the system never populates (uses the `is_archived` flag). Trash via per-id `PATCH /api/gmail/messages/:id {action:'trash'}`.
- Giant files: migrate.ts 2176, Design.tsx 1186, AccountsTab 968, finance.service 921, email-intelligence.routes 807.
- ClickUp integration is deprecated (Discord replaced it) but still mounted — kept intentionally until the Discord migration completes; do NOT extend it.

---

## KEY DECISIONS MADE THIS SESSION
- Email priority derived on READ (no schema change, zero tokens).
- Vault dev-unlock via env var (opt-in, never prod) — chosen over caching the key to disk.
- migrate.ts deliberately NOT split (append-only, low impact, high split risk).
- Reframe AI: VIDEO-FIRST beats stills (retention data); clean language mandatory; Reddit told in-character first-person.

---

## BACKEND API ENDPOINTS ADDED/CHANGED THIS SESSION
- `GET /api/email/list` — now returns `priority` (1-4); supports `?priority=N` and `?sort=priority`.
- (earlier this session) `POST /api/design/photo`, `POST /api/video-pipeline/tts-timed`, `POST /api/drive/upload`.

---

## DATABASE CHANGES THIS SESSION
None. Priority is computed on read (no migration). No new tables/columns.

---

## IMPORTANT CONTEXT FOR NEXT SESSION
- Set `VAULT_DEV_PASSWORD` in `src/backend/.env` FIRST — otherwise every backend edit locks the vault and ~17 services break until manual unlock (Settings → Vault).
- Video gen runs on **Vertex** (`ctrl-493720`, `us-central1`) — only the stable `veo-3.0-*` line exists; the `veo-3.1-*-preview` IDs from `/api/video/models` 404 on Vertex.
- Reframe AI source of truth: `D:\AI Work\YouTube\skill-shorts-hook-engine.md` (frame one + retention). Pipeline: `skill-til-info-video.md`.

---

## HOW TO START THE SYSTEM
```
D:\AI Work\START-ALL.bat
```
Or: Backend `npm run dev:backend` · Frontend `npm run dev:frontend` · Terminal `cd src/terminal-server && npm run dev`.

---

## PROJECTS OUTSIDE CTRL
- **Reframe AI** (YouTube Shorts) — video-first overhaul this session; Roger Milla half-built.
- **BedBouncer** — Kickstarter prep; site review done, needs a product demo video; confirm £89 pricing.
- **CTRLPro** — hospitality SaaS; positioning: a vertical "role pack" vs Anthropic Cowork.
- **Mobile Games** — BatonDrop (repo pushed), WordDrop, etc.
