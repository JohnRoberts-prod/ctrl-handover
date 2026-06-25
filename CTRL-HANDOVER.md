# CTRL Project Handover
*Last updated: 2026-06-25 UTC*
*Session ended: SCP-3000 Anantashesha v2 built and uploaded to YouTube Studio (private). Running /afk.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

This session was entirely focused on the Reframe AI YouTube channel (@ReframeAI), not the CTRL codebase.

---

## WHAT WE WERE BUILDING THIS SESSION

Rebuilt SCP-3000 "Anantashesha" from scratch in the correct acted scene format. V1 (narration/documentary) was wrong and scrapped. V2 uses 3-voice character dialogue (DR. CHEN/George, TECH RIVERS/Adam, D-CLASS 3841/Sam), 5 ElevenLabs SFX tracks, dark ambient music, and 6 Kling clips via two-pass ffmpeg. Uploaded private to YouTube ID J-Kgmf4tQH8. Three build failures fixed along the way. John raised a cost concern: Kling is ~$5.60-7 per video, unsustainable pre-monetisation.

---

## CURRENT BUILD STATE

### Completed this session
- Rewrote skill-scp-video.md: acted scene format, build pipeline gotchas, cost rules
- New scene script: D:\AI Work\YouTube\reframe-ai\Videos\scp-3000-eel\v2\script-v2.md
- New build script: D:\AI Work\YouTube\reframe-ai\Videos\scp-3000-eel\v2\build-scp3000-v2.cjs
- Fixed 3 build failures (ElevenLabs SFX 30s max, concurrent semaphore, amix normalize param)
- SCP-3000 v2 uploaded private: YouTube ID J-Kgmf4tQH8

### In progress
Nothing. Build complete. Pending John's review in YouTube Studio.

### Next steps
1. Review scp3000-v2-final.mp4 in YouTube Studio
2. Fix audio if needed: delete mixed-audio.mp3 and re-run build script (clips all cached)
3. Add Remotion captions (movie subtitle format) - not yet done
4. Delete wrong v1 upload (NS5YcJu4bUI) once v2 approved
5. Build reusable clip library BEFORE next SCP video (see cost section)
6. Agree next SCP number (354 Red Pool or 3199 are candidates)

---

## THE COST PROBLEM - MUST ADDRESS BEFORE NEXT BUILD

Kling spend per video: ~$5.60-7 (kling-video-o1 at ~$0.14/s, 40s footage).
V1 (wrong format, scrapped) was ~$5 wasted. Channel not yet monetised.

Fix before next build:
1. Reusable clip library at D:\AI Work\YouTube\reframe-ai\Videos\_clip-library\
   - Foundation monitoring room (generic - all SCPs share it)
   - Submarine airlock interior (generic)
   - Submarine exterior (generic)
   Generate ONCE, reuse forever. Most clips per video become free.
2. 5s clips only (no 10s). The 2x 10s clips in v2 cost as much as 4x 5s clips.
3. kling-video-o1 for creature shots only; cheaper model for environments.
Target: under $3 per video.

---

## TECHNICAL REFERENCE

Two-pass ffmpeg assembly (permanent pattern):
  Pass 1: CapCut ffmpeg - xfade - mjpeg AVI
    Path: C:\Users\admin\AppData\Local\CapCut\Apps\8.7.0.3685\ffmpeg.exe
    Codec: -c:v mjpeg -q:v 2 (NOT h264_mf - crashes at 1080x1920 portrait)
  Pass 2: @ffmpeg-installer - transcode mjpeg - libx264 silent MP4
    Path: D:\AI Work\Control-Centre\node_modules\@ffmpeg-installer\win32-x64\ffmpeg.exe
  Pass 3: composite silent MP4 + mixed-audio.mp3 - final MP4

ElevenLabs gotchas:
  SFX max: 30s per request. Cap ambient at 22s.
  Concurrent limit: 3. Use semaphore(2) in build scripts.
  amix normalize=0 NOT supported in @ffmpeg-installer ffmpeg.
  Fix: amix=inputs=N:duration=first[amixed];[amixed]volume=N[aout]

Voice casting (locked):
  Foundation Senior: George - JBFqnCBsd6RMkjVDRZzb
  Foundation Tech:   Adam   - pNInz6obpgDQGcFmaJgB
  D-Class:           Sam    - yoZ06aMxZJJ28mfd3POQ
  Field Agent:       Antoni - ErXwobaYiN019PkySvjV

CTRL API endpoints for SCP builds:
  POST /api/video-pipeline/tts-timed  { text, voiceId, scriptId? } - audioPath, durationSeconds, words
  POST /api/video-pipeline/sfx        { prompt, durationSeconds, filename? } - audioPath
  POST /api/video-pipeline/music      { prompt, lengthMs, forceInstrumental? } - audioPath
  POST /api/video-studio/generate     { image, prompt, duration, model } - jobId
  GET  /api/video-studio/jobs/:jobId  - status, videoPath
  POST /api/design/photo              { prompt } - jobId
  GET  /api/design/job/:id            - status, url (/api/design/gallery-file/<filename>)
  POST /api/social/youtube/videos     multipart: channelId, title, privacyStatus, video
  Gallery URL: /api/design/gallery-file/<f> - D:\AI Work\.cache\generated-images\<f>

---

## SCP VIDEOS LOG

SCP 682x999 Tickle Incident v1:  nTZQbF1lT7g  (live, 3 clips 15s)
SCP 682x999 Tickle Incident v2:  FDxTGUxXex8  (live, 5 clips 25s - did well)
SCP 3000 Anantashesha v1:        NS5YcJu4bUI  (private - WRONG FORMAT - delete)
SCP 3000 Anantashesha v2:        J-Kgmf4tQH8  (private - pending review)

---

## OPEN ISSUES

- SCP-3000 v2 has no captions yet (Remotion pass not done)
- Wrong v1 upload (NS5YcJu4bUI) needs deleting
- Kling cost ~$5.60/video - clip reuse library needed before next build

---

## KEY DECISIONS THIS SESSION

- SCP videos = acted scenes, NEVER narrated documentaries
- Two-pass ffmpeg is the permanent assembly pattern
- ElevenLabs semaphore = 2 in all future build scripts
- Clip reuse library agreed - implement before next SCP video
- build-scp3000-v2.cjs is the reference template for future SCP builds

---

## OTHER ACTIVE PROJECTS (context)

- CTRLSignage: Cloudflare signage SaaS, steps 1+2 done, step 3 (portal auth) next
- BedBouncer: website live at bedbouncer.co.uk, Kickstarter 2026
- Mobile Games: BatonDrop in Google Play review; WordDrop + Cavernborn in planning
- CTRLPro: hospitality SaaS, planning phase

---

## HOW TO START THE SYSTEM

D:\AI Work\START-ALL.bat
Or: npm run dev:backend + npm run dev:frontend in D:\AI Work\Control-Centre
