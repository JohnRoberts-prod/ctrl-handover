# CTRL Project Handover
*Last updated: 2026-06-06 UTC*
*Session ended: Fixed audio sync issue on Reframe AI Video 1 — v7c.mp4 is the clean final build, on Google Drive, ready to upload to YouTube.*

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

This session was entirely focused on the **Reframe AI YouTube channel** (Video 1 — Pulp Fiction diner scene remade as Studio Ghibli animation). The session recovered from a previous 1M context API error (the 'bunny ears session') and continued where it left off. We added a proper story ending to the video, fixed two separate audio bugs that caused the music to cut out on YouTube, and produced a clean final build: `reframe-ai-video-1-v7c.mp4`.

---

## CURRENT BUILD STATE

### Completed this session

- **Recovered bunny ears session** — extracted prior context from JSONL transcript. Two outstanding tasks: title fix + missing ending.
- **create-reframe-v1-v5.cjs** — 10-scene script (7 original + 3 ending scenes). Script ID: `rf_mq1hvexk_420d2`
- **Ending scenes added (scenes 8-10):**
  - Scene 8 (Pumpkin/Roger): walks the diner with a big black bin bag, patrons drop valuables in — *"Wallets, watches — in the bag. Don't be shy."*
  - Scene 9 (Honey Bunny/Laura): spots police lights through the window — *"Cops! Go go go!"*
  - Scene 10 (Pumpkin/Roger): both burst out the diner door laughing, bin bag swinging — *"Ha!"*
- **Title card fixed**: "What if Pulp Fiction was animated?" — Playfair Display bold, no emoji
- **make-reframe-v1-v5c.cjs** — FINAL assembler with two critical audio fixes
- **reframe-ai-video-1-v7c.mp4** — 51s, 30MB. On Drive: G:\My Drive\reframe-ai-video-1-v7c.mp4

### Two audio bugs diagnosed and fixed

**Bug 1 — Music cuts out between dialogue on YouTube:**
Each rendered clip's audio was 2s shorter than the clip video (clipDur = audioDur + TAIL_SECS, audio file only covers audioDur). After concatenating 10 clips, the audio stream had 2s holes at every clip boundary. The amerge mixing filter stops producing output when any input has no data — music cut at every gap.
Fix: Added `[1:a]apad[aout]` to both branches of renderMuxedClip so audio fills the full clip duration before concat.

**Bug 2 — Audio/video sync drift (introduced by post-fix attempt):**
Trying to repair audio via multiple ffmpeg remix steps shifted audio timestamps.
Fix: Fix the source (apad at render time), not the output.

**Music mix rule:**
Never use amix — normalises by dividing by n_inputs, music drops to -21dBFS during silence, wiped by YouTube loudness processing.
Always use amerge+pan: `[voice][music]amerge=inputs=2[merged]` + `[merged]pan=stereo|c0=c0+c2|c1=c1+c3[aout]`

### In progress / next steps

1. Upload reframe-ai-video-1-v7c.mp4 to YouTube (@ReframeAI) and confirm music is continuous
2. Write YouTube title, description, hashtags for Video 1
3. Begin Video 2 from the 20 ideas in design/reframe-ai-channel.md
4. Apply the apad fix to all future make scripts as the new standard

---

## FILES CREATED OR MODIFIED THIS SESSION

```
D:\AI Work\Control-Centre\create-reframe-v1-v5.cjs       — 10-scene DB script (the good one)
D:\AI Work\Control-Centre\make-reframe-v1-v5c.cjs         — FINAL make script: apad fix + amerge+pan
D:\AI Work\Control-Centre\v1-v5-voices.json               — voice mapping (scriptId: rf_mq1hvexk_420d2)
D:\AI Work\Control-Centre\create-reframe-v1-v4.cjs        — superseded (bad ending)
D:\AI Work\Control-Centre\make-reframe-v1-v4.cjs          — superseded
D:\AI Work\Control-Centre\make-reframe-v1-v5.cjs          — superseded (had audio bugs)
D:\AI Work\Control-Centre\remix-music-v5.cjs              — dead end, do not use
D:\AI Work\Control-Centre\remix-music-v5b.cjs             — dead end, do not use
D:\AI Work\YouTube\reframe-ai\skills\skill-reframe-ai-workflow.md — major update: full audio bug docs
D:\AI Work\YouTube\reframe-ai\SESSION_STATE.md            — updated
D:\AI Work\YouTube\reframe-ai\LEARNINGS.md               — 7 new entries
G:\My Drive\reframe-ai-video-1-v7c.mp4                   — FINAL VIDEO (30MB, 51s)
```

---

## REFRAME AI VIDEO 1 — TECHNICAL DETAILS

**Script ID:** `rf_mq1hvexk_420d2`  
**Make script:** `make-reframe-v1-v5c.cjs`  
**Voices:**
- George `JBFqnCBsd6RMkjVDRZzb` — narrator
- Roger `CwhRBWXzGAHq8TQ4Fs17` — Pumpkin
- Laura `FGY2WhTYpPnrIDTdsKH5` — Honey Bunny

**TTS endpoint:** `POST /api/video-pipeline/tts`  
Body: `{ text, scriptId: "{scriptId}_line_{n}", voiceId, provider: "elevenlabs" }`

**Honey Bunny must always have bunny ears in every Veo prompt** — this was a happy Veo accident that became a character feature, now locked in permanently.

**Key audio rules going forward:**
- `renderMuxedClip` must include `[1:a]apad[aout]` in complexFilter (both hasSub branches)
- Music mix: amerge+pan only, music at -10dB
- Never use amix for music

---

## OPEN ISSUES

- v7c not yet tested on YouTube — first real confirmation the apad fix works after upload
- YouTube channel @ReframeAI may still need to be set up

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Only use v7c.** v7, v7-fixed, v7b are all broken (music or sync issues).
- **Dead-end scripts:** remix-music-v5.cjs and remix-music-v5b.cjs. Do not use.
- Raw concatenated video (before music) is cached at: `D:\AI Work\.cache\video-pipeline\rf_mq1hvexk_420d2\rf_mq1hvexk_420d2.mp4`
- All 10 Veo clips have video_path set in DB — future rebuilds need zero Veo spend.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`

---

## OTHER ACTIVE PROJECTS

- **CTRLPro** — hospitality SaaS, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video
- **Mobile Games** — BatonDrop submitted to Play Store; WordDrop + Cavernborn in planning
- **Reframe AI** — THIS SESSION. Video 1 done. 19 more scripts ready to produce.
- **RoadToCtrl** — solopreneur YouTube channel, video ideas auto-appended each /afk