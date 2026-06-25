# CTRL Project Handover
*Last updated: 2026-06-25 UTC*
*Session ended: SCP-3000 v3 build script completed and skill rewritten as runbook. Switching focus to CTRLSignage.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

---

## WHAT WE WERE BUILDING THIS SESSION

Two workstreams this session:

**1. SCP-3000 v3 video (Reframe AI)**
Complete rebuild of the SCP-3000 Anantashesha video using the correct Veo + Kling tool split. The v2 had used Kling for all 5 clips, which was wrong — Kling should only be used for continuous chains where the last frame of clip N becomes the first frame of clip N+1. Independent scenes (ops room, deck aftermath) should use Veo. The build script has been rewritten and the SCP skill updated as a fully self-contained runbook. The script is ready to run but has NOT been executed yet.

**2. CTRLSignage subscriber loading (CTRL)**
The Subscribers module was failing with "Unknown subscriber source" for CTRLSignage. Fixed by: (a) restarting pm2 so the backend picked up the ctrlsignage SOURCES entry, (b) generating a new Cloudflare OPERATOR_KEY, (c) pushing it to Cloudflare Workers with `wrangler secret put OPERATOR_KEY --env production`, (d) storing it in the CTRL vault as `signage_operator_key`. CTRLSignage waitlist now loads 2 test entries correctly.

---

## CURRENT BUILD STATE

### Completed this session

- `D:\AI Work\YouTube\reframe-ai\Videos\scp-3000-eel\v3\build-scp3000-v3.cjs` — rewritten with correct Veo + Kling routing and all API calls using verified response shapes
- `D:\AI Work\YouTube\reframe-ai\skills\skill-scp-video.md` — fully rewritten as self-contained runbook with complete API docs for all 6 endpoints, ffmpeg two-pass docs, cost guide, full checklist
- `D:\AI Work\Control-Centre\src\frontend\src\modules\subscribers\Subscribers.tsx` — fixed hint message to show `signage_operator_key` for CTRLSignage source (not `ctrlsignage_admin_secret`)
- CTRLSignage OPERATOR_KEY: generated, pushed to Cloudflare, stored in vault — subscribers loading

### In progress right now

SCP-3000 v3 build script is ready but not yet run. Audio files (TTS x7, SFX x3, music) were generated in a previous aborted run and exist on disk. The build will skip phases 1-3 and proceed from Phase 4 (starting image for Kling clip 2).

### Pending / next steps

1. Run SCP-3000 v3 build: `node build-scp3000-v3.cjs` from `D:\AI Work\YouTube\reframe-ai\Videos\scp-3000-eel\v3\`
2. Watch the video — confirm creature chain holds identity across clips 2-4
3. If v3 > v2, publish v3 and delete v1 (NS5YcJu4bUI)
4. **NEXT FOCUS: CTRLSignage system build** — see architecture at `D:\AI Work\CTRLSignage\design\CTRL-SIGNAGE-ARCHITECTURE.md`

---

## KEY TECHNICAL FACTS FOR THE NEXT SESSION

### SCP-3000 v3 tool routing
```
Clip 1 (8s): Veo — ops room (independent scene)
Clip 2 (5s): Kling — diver + creature (chain start, needs starting image)
Clip 3 (5s): Kling — body section passes (chain from clip 2 last frame)
Clip 4 (5s): Kling — hauled to surface (chain from clip 3 last frame)
Clip 5 (8s): Veo — deck aftermath (independent scene)
```

### API response shapes (CRITICAL — get these wrong and the build fails silently)

| Endpoint | Method | Response shape |
|----------|--------|---------------|
| /api/video-pipeline/tts-timed | POST JSON | { success, data: { audioPath } } |
| /api/video-pipeline/sfx | POST JSON | { success, data: { audioPath } } |
| /api/video-pipeline/music | POST JSON | { success, data: { audioPath } } |
| /api/design/photo | POST JSON | { ok, jobId } — NOT { success, data } |
| /api/design/job/:id | GET | { ok, status:'done'\|'failed', url } — status is 'done' not 'completed' |
| /api/video-generation/generate | POST **multipart** | { success, data: { generationId } } |
| /api/video-generation/status/:id | GET | { success, data: { status:'complete'\|'failed', outputPath } } — 'complete' not 'completed' |
| /api/video-studio/generate | POST JSON | { success, data: { jobId } } |
| /api/video-studio/jobs/:id | GET | { success, data: { status:'completed'\|'failed', videoPath } } — 'completed' not 'complete' |
| /api/video-studio/extract-frame | POST JSON | { success, data: { framePath } } |
| /api/social/youtube/videos | POST **multipart** | { success, data: { videoId } } — always upload PRIVATE |

**Veo requires multipart/form-data** (not JSON). Use the `apiPostMultipart()` helper in the build script.
**PROJECT_ID for Veo** = `'reframe-ai'` (from projects table — Reframe AI project ID).
**CHANNEL_ID for YouTube** = `'d21ea761-a020-499d-8db3-99b1a2d4a52a'` (CTRL UUID, not a UCxxx).

### xfade offsets for mixed clip durations
```
CLIP_DURATIONS = [8, 5, 5, 5, 8]  // Veo=8s, Kling=5s
FADE_DUR = 0.12
offsets = [7.88, 12.76, 17.64, 22.52]
Formula: cumulative += duration[i] - fadeDur for each transition
```

### ElevenLabs limits
- Max SFX duration: 30s. Never exceed.
- Concurrent limit: 3. Use semaphore(2) in build scripts.
- Error: 429 concurrent_limit_exceeded

### ffmpeg two-pass assembly
- Pass 1 (xfade): CapCut ffmpeg at `C:\Users\admin\AppData\Local\CapCut\Apps\8.7.0.3685\ffmpeg.exe` — outputs mjpeg AVI (NOT h264_mf — crashes at portrait resolution)
- Pass 2 (encode): @ffmpeg-installer at `D:\AI Work\Control-Centre\node_modules\@ffmpeg-installer\win32-x64\ffmpeg.exe` — outputs h264 MP4
- Pass 3: composite silent.mp4 + mixed-audio.mp3 → final.mp4

### CTRLSignage system state
- Vault key: `signage_operator_key` = `154cf3938992bdd5c9bc9d3b5bc7f26e39fd1938cd5ed7e1`
- Cloudflare OPERATOR_KEY set on production Worker
- Subscribers UI working — 2 waitlist test entries showing
- Architecture: `D:\AI Work\CTRLSignage\design\CTRL-SIGNAGE-ARCHITECTURE.md`
- Status: Steps 1+2 complete (foundation + auth core). Next: Step 3 (portal auth) + Step 4 (media upload)
- Open decisions (from memory): product name/domain, DB (D1 vs Postgres), operator auth approach

---

## FILES CREATED OR MODIFIED THIS SESSION

```
D:\AI Work\YouTube\reframe-ai\Videos\scp-3000-eel\v3\build-scp3000-v3.cjs
  — Complete rewrite: Veo for clips 1+5, Kling chain for clips 2-4, all API calls corrected

D:\AI Work\YouTube\reframe-ai\skills\skill-scp-video.md
  — Complete rewrite as self-contained runbook: 14 sections, full API reference, build checklist

D:\AI Work\YouTube\reframe-ai\SESSION_STATE.md
  — Updated with current build state

D:\AI Work\YouTube\reframe-ai\LEARNINGS.md
  — 8 new entries: Veo/Kling routing, API response shapes, xfade offsets, CTRLSignage vault key

D:\AI Work\roadtoctrl\knowledge\video-ideas.md
  — 2 new video ideas prepended for this session

D:\AI Work\Control-Centre\src\frontend\src\modules\subscribers\Subscribers.tsx
  — Fixed hint message: signage_operator_key (not ctrlsignage_admin_secret) for ctrlsignage tab
```

---

## VIDEOS MADE SO FAR (Reframe AI)

| SCP | Version | YouTube ID | Status |
|-----|---------|-----------|--------|
| 682x999 | v1 | nTZQbF1lT7g | Live |
| 682x999 | v2 | FDxTGUxXex8 | Live — did well |
| 3000 | v1 | NS5YcJu4bUI | Private — DELETE (narration format, wrong) |
| 3000 | v2 | J-Kgmf4tQH8 | Private — pending review, may be superseded by v3 |
| 3000 | v3 | — | Build script ready, not yet run |

---

## OPEN ISSUES / KNOWN BUGS

- SCP-3000 v1 (NS5YcJu4bUI) still on YouTube as private — needs deletion
- SCP-3000 v3 not yet built or uploaded
- No Remotion captions on any SCP video yet
- CTRLSignage Steps 3+4 not yet built
- 3 open decisions on CTRLSignage before next session (see architecture doc)

---

## KEY DECISIONS MADE THIS SESSION

1. **Veo for independent scenes, Kling for continuous chains** — never mix this up again
2. **SCP skill is now the source of truth** — all API docs, response shapes, gotchas, checklist in one file
3. **Build scripts must use apiPostMultipart() for Veo** — not apiPost()
4. **Status strings differ by tool**: Veo='complete', Kling='completed', Images='done'

---

## IMPORTANT CONTEXT FOR NEXT SESSION (CTRLSignage)

The next session focuses on CTRLSignage. Key context:
- Architecture doc at `D:\AI Work\CTRLSignage\design\CTRL-SIGNAGE-ARCHITECTURE.md`
- Steps 1+2 complete: Cloudflare stack scaffolded, ECDSA auth core built
- Next: Step 3 (portal auth — operator login for the management portal) + Step 4 (media upload to R2)
- 3 open decisions to resolve before building: product name/domain, D1 vs Postgres for DB, operator auth approach
- Operator secret key: `signage_operator_key` in vault, pushed to Cloudflare production
- Subscribers loading in CTRL (2 test entries)

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npx pm2 start ecosystem.config.js` (or `npm run dev:backend`)
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLSignage** — digital signage SaaS, Cloudflare stack, Steps 1+2 done, next: portal auth
- **Reframe AI** — @ReframeAI YouTube channel, SCP-3000 v3 building
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video
- **CTRLPro** — hospitality SaaS, planning phase, one potential client, no Lane7 connection
- **Mobile Games** — BatonDrop in Google Play review, others in development
