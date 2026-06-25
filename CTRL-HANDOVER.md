# CTRL Project Handover
*Last updated: 2026-06-25 UTC*
*Session ended: Completing /afk after building CTRLSignage handover + next-chat paste text*

---

## HOW TO USE THIS DOCUMENT

You are Claude Code (or Claude web) picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173 (port varies — check pm2 logs)
Terminal server: node-pty WebSocket on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`
CTRL runs on pm2 — NEVER use npm run dev manually. Use /restartctrl if CTRL is down.

---

## WHAT WE WERE BUILDING THIS SESSION

Two workstreams:

1. **Reframe AI — SCP-049 plague doctor video:** completed 5-clip Kling-only narrated video (Format B), fixed jump cuts with true last-frame extraction (`ffmpeg -sseof -0.1 -i clip.mp4 -vframes 1 frame.jpg`), mixed TTS + horror music, uploaded private to YouTube (ID: SFY6M_NDCqI).

2. **CTRL — Meshy AI integration:** built full Meshy 3D model generation into the Design module. Backend service + routes + frontend panel all TypeScript-clean. Needs vault key `meshy_api_key` added before it will work.

UE5 MCP was also wired up (unreal-mcp added to Claude settings.json pointing at localhost:8137) but UE5 was still installing at session end.

---

## CURRENT BUILD STATE

### Recently completed

- **SCP-049 Plague Doctor video** — 25.3s, 5 Kling clips, ElevenLabs TTS narration, horror ambient music. YouTube private ID: SFY6M_NDCqI.
- **Meshy backend** — `src/backend/src/services/meshy.service.ts` (text-to-3D, image-to-3D, poll, download) + `src/backend/src/routes/meshy.routes.ts`. Registered in server.ts.
- **Meshy frontend** — `src/frontend/src/modules/design/MeshyPanel.tsx`, "Meshy 3D" tab in Design.tsx, CSS in design.css.
- **skill-scp-video.md** — Format B (narrated Kling-only chain) fully documented.
- **/restartctrl skill** — `C:\Users\admin\.claude\skills\restartctrl\SKILL.md` created and registered in CLAUDE.md.
- **UE5 MCP config** — `unreal-mcp` added to `C:\Users\admin\.claude\settings.json` (port 8137).

### In progress

Nothing half-done in code. Meshy is complete but untested (needs vault key).

### Next steps (ordered)

1. Add vault key `meshy_api_key` in CTRL Settings tab (value from meshy.ai dashboard)
2. UE5 setup: enable Python Editor Script Plugin + Editor Scripting Utilities, start MCP server on 8137, restart Claude Code
3. CTRLSignage Steps 3+4 — portal auth (React app + Google OAuth) + media upload (R2 presigned PUT + Queue thumbnail). Full handover: `D:\AI Work\CTRLSignage\NEXT-SESSION-HANDOVER.md`
4. SCP-3000 v3 — build script at `D:\AI Work\YouTube\reframe-ai\Videos\scp-3000-eel\v3\build-scp3000-v3.cjs` (upload step was failing with ECONNRESET)

---

## FILES CREATED OR MODIFIED THIS SESSION

```
src/backend/src/services/meshy.service.ts       NEW — Meshy API service
src/backend/src/routes/meshy.routes.ts          NEW — /api/meshy/* routes
src/backend/src/server.ts                       MODIFIED — meshy router added
src/frontend/src/modules/design/MeshyPanel.tsx  NEW — Meshy tab UI
src/frontend/src/modules/design/Design.tsx      MODIFIED — Meshy 3D tab added
src/frontend/src/modules/design/design.css      MODIFIED — meshy CSS classes
SESSION_STATE.md                                UPDATED
LEARNINGS.md                                    UPDATED (pm2, Meshy)
D:\AI Work\YouTube\reframe-ai\skills\skill-scp-video.md   UPDATED — Format B added
D:\AI Work\YouTube\reframe-ai\LEARNINGS.md               UPDATED
D:\AI Work\roadtoctrl\knowledge\video-ideas.md            UPDATED — 2 new video ideas
C:\Users\admin\.claude\skills\restartctrl\SKILL.md        NEW
C:\Users\admin\.claude\CLAUDE.md                          MODIFIED — restartctrl registered
C:\Users\admin\.claude\settings.json                      MODIFIED — unreal-mcp added
D:\AI Work\CTRLSignage\NEXT-SESSION-HANDOVER.md           NEW — full CTRLSignage session handover
D:\AI Work\NEXT-CHAT-CONTEXT.md                           NEW — paste-in text for new chat window
```

---

## OPEN ISSUES / KNOWN BUGS

- Meshy tab needs `meshy_api_key` in vault before it will work
- UE5 MCP not connected yet (UE5 still installing, plugins not enabled)
- CTRL frontend port unstable — stray Vite processes may occupy 5173-5178. Use /restartctrl.
- pm2 ctrl-backend had 93 restarts from earlier in session — seems stable now
- SCP-3000 v3 upload step fails with ECONNRESET
- SCP-049 mask colour inconsistency (white/black shifts) — Kling API has no character reference. Acceptable per John.

---

## KEY DECISIONS MADE THIS SESSION

- **Meshy vault key name:** `meshy_api_key`
- **CTRLSignage operator vault key:** `signage_operator_key`
- **Gloomstomper uses AI video with character reference** on Kling website, NOT Unreal Engine
- **Format B for humanoid SCPs:** 5-clip Kling-only chain, single narrator, hard cuts, no xfade
- **True last-frame rule:** always `ffmpeg -sseof -0.1 -i clip.mp4 -vframes 1 frame.jpg`. Never mid-clip.
- **CTRL runs on pm2:** never `npm run dev`. /restartctrl handles full clean restart.
- **CTRLSignage domain:** ctrlsignage.co.uk (locked 2026-06-23)

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
POST /api/meshy/generate/text   — start text-to-3D (objectPrompt, stylePrompt, artStyle, targetFormats, enablePbr)
POST /api/meshy/generate/image  — start image-to-3D (imageUrl, texturePrompt, targetFormats, enablePbr, poseMode)
GET  /api/meshy/jobs/:jobId     — poll job (status, progress, modelUrls, localPaths, thumbnailUrl, error)
All routes behind requireVaultUnlocked middleware.
```

---

## DATABASE CHANGES THIS SESSION

None — no migrations this session.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. **Run /restartctrl** first if CTRL seems down
2. **Before testing Meshy:** add `meshy_api_key` to vault in CTRL Settings
3. **CTRLSignage session:** read `D:\AI Work\CTRLSignage\NEXT-SESSION-HANDOVER.md` first
4. **New chat window paste-in:** contents of `D:\AI Work\NEXT-CHAT-CONTEXT.md`
5. **Audio codec gotcha:** always `-c:a libmp3lame` for .mp3 output, never `-c:a aac`
6. **Confirm before paid API calls** — Meshy, ElevenLabs, Veo, Kling all cost money

---

## HOW TO START THE SYSTEM

CTRL runs on pm2:
```powershell
# If pm2 is not running at all:
cd "D:\AI Work\Control-Centre"
& "C:\Users\admin\AppData\Roaming\npm\pm2.cmd" start ecosystem.config.js

# For a clean restart:
# Use /restartctrl skill in Claude Code
```

Verify: http://localhost:3001/api/health

---

## PROJECTS OUTSIDE CTRL

- **CTRLSignage** — Cloudflare signage SaaS, Steps 1+2 done. Next: portal auth + media upload. `D:\AI Work\CTRLSignage\`
- **Reframe AI** — SCP video pipeline (Format B). `D:\AI Work\YouTube\reframe-ai\`
- **BedBouncer** — ESP32 alarm, Kickstarter prep. `D:\AI Work\BedBouncer\`
- **CTRLPro** — Hospitality SaaS, planning phase. `D:\AI Work\CtrlPro\`
- **BatonDrop** — Mobile game, v2.0.0 in Google Play review. `D:\AI Work\Mobile-Games\games\batondrop\`
- **RoadToCtrl** — Solopreneur YouTube channel. `D:\AI Work\roadtoctrl\`
