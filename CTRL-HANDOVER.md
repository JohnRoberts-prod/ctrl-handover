# CTRL Project Handover
*Last updated: 2026-06-27 UTC*
*Session ended: switching active project to CTRLSignage after a long multi-project session (CTRLSignage operator module → Reframe AI SCP-597 video → CTRL LoRA module → Resolve MCP research).*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session. John Roberts is the developer.
Read this entire document before responding. When John returns, he will paste in any files/context.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

---

## WHAT WE WERE BUILDING THIS SESSION

A long session spanning four threads. (1) Finished the **CTRLSignage operator module** in CTRL.
(2) Built and iterated a **Reframe AI SCP-597 horror short** pipeline — which John ultimately
**rejected** for having no persistent character and no story. (3) That rejection drove building a
**LoRA character module** into CTRL (fal.ai FLUX) to fix AI-video character consistency, and we
**validated a turntable→dataset technique** that bootstraps a trained character from a single image.
(4) Researched **DaVinci Resolve MCP** for proper editing/grading vs ffmpeg. John then said to
switch the active project to CTRLSignage and run /afk.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- **CTRLSignage operator module** (CTRL): `src/frontend/src/modules/signage/` (Signage.tsx 4 tabs +
  components + signage.css `sig-` prefix); `src/backend/src/services/signage.service.ts` +
  `routes/signage.routes.ts` proxying `https://api.ctrlsignage.co.uk/operator/*` with Bearer
  `signage_operator_key`. `/api/signage` registered behind requireVaultUnlocked. tsc clean.
- **CTRL LoRA module** (fal.ai FLUX) — full build, tsc clean:
  - DB migration 51 → `lora_characters` table.
  - `services/fal.service.ts` — fal queue REST (Authorization: Key), dependency-free store-only ZIP
    writer, R2 hosting (reuses kling.service R2 path), `trainLora`/`pollTraining`/`generateWithLora`.
  - `services/lora-bootstrap.service.ts` — "from one description": master (white bg) → Veo turntable
    → ffmpeg 12 frames @1024 → trainLora; background job, status preparing→training→ready.
  - `routes/lora.routes.ts` — `/api/lora` train, list, refresh, generate, bootstrap, delete (vault-guarded).
  - `src/frontend/src/modules/characters/` — Characters tab (UserSquare, build group), "From a
    description" + "Upload images" modes, generate panel.
- **Backend fix**: `/api/video-pipeline/tts` now passes `modelId` through (enables `eleven_v3`
  emotion tags `[whispered]`/`[trembling]`; default multilingual_v2 ignores them).
- **Validated** turntable→LoRA dataset on an SCP creature (free test) — identity held through rotation.

### In progress right now (PENDING USER ACTIONS — do these first next CTRL session)
- **Set `fal_api_key` in the vault** (from fal.ai) — LoRA module needs it.
- **`/restartctrl`** — loads migration 51, the new `/api/lora` + `/api/signage` routes, the tts fix,
  and the rebuilt frontend (Characters + Signage tabs).
- Then: open **Characters → From a description**, train the first character (an SCP creature or a
  helmeted soldier).

### Pending / next steps
- **Rebuild SCP-597 story-first** around John's 7-beat scene (see KEY DECISIONS) using trained,
  persistent LoRA soldiers + real dialogue. Lock script + storyboard in TEXT before generating.
- **DaVinci Resolve MCP**: John to decide free (hiteshK03) vs Studio (lordhoell/samuelgursky),
  confirm installed, then install + POC test (import clip → 2-clip timeline → grade → render).
- **CTRLSignage**: set Stripe production secrets + create £2/screen price + register webhook.

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | modules/home/ | working | |
| Claude Tab | modules/claude-tab/ | working | pty session persists across tabs |
| Gmail/Google | modules/gmail/ | working | |
| Tasks | modules/tasks/ | working | |
| Finance | modules/finance/ | working | |
| Trading | modules/trading/ | working | |
| GitHub | modules/github/ | working | |
| Cloudflare | modules/cloudflare/ | working | |
| Brand Toolkit | modules/brand-toolkit/ | working | |
| Design | modules/design/ | working | Nano photo/img2img, Ideogram edit, Kling video |
| Video Pipeline | modules/video-pipeline/ | working | tts now supports modelId |
| Signage | modules/signage/ | **NEW this session** | operator console; needs restart to appear |
| Characters (LoRA) | modules/characters/ | **NEW this session** | needs fal_api_key + restart |
| Settings / Admin | modules/settings,admin/ | working | |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
# CTRL backend
src/backend/src/db/migrate.ts — added migration 51 (lora_characters table)
src/backend/src/services/fal.service.ts — NEW: fal.ai FLUX LoRA (train/generate, ZIP, R2)
src/backend/src/services/lora-bootstrap.service.ts — NEW: description -> master -> turntable -> frames -> train
src/backend/src/routes/lora.routes.ts — NEW: /api/lora/*
src/backend/src/services/signage.service.ts — NEW: operator API proxy
src/backend/src/routes/signage.routes.ts — NEW: /api/signage/*
src/backend/src/routes/video-pipeline.routes.ts — /tts now passes modelId through
src/backend/src/server.ts — registered /api/lora and /api/signage (vault-guarded)

# CTRL frontend
src/frontend/src/modules/characters/{Characters.tsx,index.ts,characters.css} — NEW
src/frontend/src/services/lora.service.ts — NEW
src/frontend/src/modules/signage/{Signage.tsx,index.ts,signage.css,components/*} — NEW
src/frontend/src/services/signage.service.ts — NEW
src/frontend/src/core/{nav.config.ts,shell.types.ts,AppShell.tsx} — registered signage + characters tabs

# Reframe AI (separate project, D:\AI Work\YouTube\reframe-ai\)
Videos/scp-597-mother/build-scp597.cjs — NEW: SCP video pipeline (stills-first, Veo, batched)
Videos/lora-test/test-turntable.cjs — NEW: turntable dataset validation
skills/{skill-scp-film.md,skill-ue-meshy-film.md,ue-meshy-poc-test.md} — filed from inbox
CLAUDE.md, LEARNINGS.md — updated

# CTRLSignage (D:\AI Work\CTRLSignage\) — earlier in session
app/src/utils/stripe.ts, app/src/routes/billing.ts, app/src/routes/portal/billing.ts — NEW Stripe
website/src/pages/billing/BillingPage.tsx + App.tsx + Sidebar.tsx — billing page
website/src/pages/schedules/SchedulesPage.tsx — layout name fix
SESSION_STATE.md — updated

# Shared
D:\AI Work\skills\skill-ai-video-cost-and-loras.md — NEW reusable skill
D:\AI Work\roadtoctrl\knowledge\video-ideas.md — 3 new ideas prepended
```

---

## RECENT GIT COMMITS

CTRL (`D:\AI Work\Control-Centre`) is not a git repo — no commit history. Changes live on disk only.
Best-effort /afk repo backup pushed: batondrop. Other linked repos skipped (not git / no remote).

---

## OPEN ISSUES / KNOWN BUGS

- **eleven_v3 voices need a backend restart** — the /tts modelId passthrough is on disk but the
  running backend won't honour it until restarted. If voices come out flat, that's why.
- **Gemini img2img returns 1:1 with a thin white border** — always overscan-crop
  (`crop=in_w*0.96:in_h*0.96,scale=1080:1920`) or white bars bake into the clip.
- **Gemini text-to-image blocks aggressive flesh prompts** — use img2img EDIT of an approved image instead.
- LoRA module untested live (needs fal_api_key + restart). Migration 51 applies on next start.

---

## KEY DECISIONS MADE THIS SESSION

- **Cost discipline (AI video):** Kling/Pixazo = $0.70/clip cash; rerolls burned ~$25. RULE: approve
  the cheap still BEFORE paying to animate. **Veo is the default animator** (free GCP credit until
  ~23 Jul 2026); paid Kling only for flesh Veo's filter refuses.
- **Character consistency via LoRA, not "hide the face":** train a FLUX LoRA per character. Bootstrap
  the training set from ONE image via a **Veo turntable** (validated). Helmeted soldiers = best for
  humans (kills face-drift AND lip-sync).
- **SCP-597 rebuild brief (John's spec):** a tight 7-beat 2-soldier scene — Mother breaks containment
  -> two soldiers frantic ("She's out, we have to stop her" / "What do we do?!") -> both firing round a
  doorway -> one grabbed and dragged in by a tentacle -> the other screams his name -> beat, then runs
  screaming -> the taken soldier walks back out, tentacle fused to him, whispers "...Mother." Persistent
  characters + real dialogue + the morph as the scare. Lock script in text first.
- **LoRA now vs UE5 later:** LoRA route for volume/speed/cost now; UE5+Meshy for premium long-form
  later; they coexist (endgame hybrid = UE render -> AI stylization pass).
- **Resolve MCP** for proper edit/grade/audio of hero videos; keep ffmpeg for fast automated volume.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
# LoRA (fal.ai) — all behind requireVaultUnlocked
GET    /api/lora/characters                 list trained characters
POST   /api/lora/characters                 train from uploaded images (multipart)
POST   /api/lora/bootstrap                  train from a description (turntable pipeline)
POST   /api/lora/characters/:id/refresh     poll fal training status
POST   /api/lora/generate                   generate a still from a trained character
DELETE /api/lora/characters/:id             delete

# CTRLSignage operator (proxy to api.ctrlsignage.co.uk/operator/*) — vault-guarded
GET  /api/signage/usage | /tenants | /tenants/:id | /devices | /waitlist
POST /api/signage/tenants/:id/seats | /suspend | /reactivate | /devices/:id/deactivate
```

---

## DATABASE CHANGES THIS SESSION

- **Migration 51** (`src/backend/src/db/migrate.ts`): new table `lora_characters`
  (id, name, trigger_word, status [preparing|training|ready|failed], request_id, lora_url,
  thumb_url, error, created_at, updated_at). Applies automatically on next backend start.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **DO FIRST:** set `fal_api_key` in vault, then `/restartctrl`. Nothing LoRA works until both are done.
- The SCP-597 first cut (29.67s, in `G:\My Drive\SCP-597 Review\scp597-silent.mp4`) was **rejected** —
  do not ship it. The rebuild is story-first around the 7-beat scene above, using trained characters.
- Reusable assets worth keeping: the Mother creature sheet + the tentacle look, the hook frame, the
  squad-charge and soldier-drag clips. The new characters (LoRA) replace the inconsistent humans.
- The turntable bootstrap is the headline new capability — "describe a character, get a trained,
  reusable identity." Test it first on an SCP creature (creatures hold best).
- Skill files to read next video session: `D:\AI Work\skills\skill-ai-video-cost-and-loras.md`,
  `reframe-ai/skills/skill-scp-film.md`, `reframe-ai/skills/skill-ue-meshy-film.md`.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```
Or: backend `npm run dev:backend`, frontend `npm run dev:frontend`, terminal server in
`src/terminal-server`. Use `/restartctrl` to restart cleanly (clears Vite cache).

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLSignage** — *now the active project.* API live at api.ctrlsignage.co.uk, Stripe billing built
  (needs prod secrets), operator module in CTRL built. Player Android app = skeleton only.
- **Reframe AI** — SCP-597 video rebuild pending (story-first, LoRA characters).
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video.
- **Mobile Games** — BatonDrop in production; others in build.
