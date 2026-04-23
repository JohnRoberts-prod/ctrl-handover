# CTRL Project Handover
*Last updated: 23 April 2026 — session end*
*Session ended: discussing swapping @imgly/background-removal for Remove.bg API after user found it too aggressive on product photos*

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

This session was entirely focused on the Design module (Creative Studio). We replaced Replicate entirely with Google Gemini (Nano Banana 2) for both Sprites and the new Recompose feature. We also added: Ideogram Edit (inpainting) with an in-browser mask painting tool, img2img scene recomposition via Gemini, optional reference image upload for sprite generation, and a scissors button on gallery cards for background removal. The session ended mid-task: we added @imgly/background-removal for the scissors button but the user found it too aggressive (removing parts of the subject), so the next step is to replace it with the Remove.bg API.

---

## CURRENT BUILD STATE

### Recently completed (this session)

- **Trading TS fixes** — fixed `bar.intradayChangePct` -> `bar?.changeFromOpen` in trading-briefing.service.ts; removed `notional` from INSERT in trading-scheduler.service.ts
- **Trading RoutinesTab rewrite** — added Built-in Briefings panel with Run Now buttons (morning/midday), added Perplexity questions editor per routine, added `perplexity_queries` to TradeRoutine type
- **Ideogram Edit (inpainting)** — POST /api/design/edit route, editImage() service, full in-browser mask painting tool (MaskEditorModal)
- **Recompose mode** — POST /api/design/img2img route, third mode button in Images tab toggle (Generate / Edit / Recompose)
- **Replaced Replicate with Gemini (Nano Banana 2)** — geminiGenerateImage() replaces replicatePost(); vault key changed from replicate_api_token to gemini_api_key; CreativeStatus.replicate renamed to CreativeStatus.gemini throughout
- **Reference image for Sprites** — optional upload zone in Sprites tab; base64 passed to Gemini as inline_data alongside prompt
- **Background removal scissors button** — @imgly/background-removal v1.7.0 installed; Scissors icon on all image/sprite gallery cards

### In progress right now

**Remove.bg API integration** — scissors button currently uses @imgly/background-removal (ISNet model, too aggressive on product photos). Agreed to swap for Remove.bg API. NOT yet implemented. Plan:
- Add POST /api/design/remove-bg backend route (calls remove.bg API with vault key remove_bg_api_key)
- Update GalleryCard handleRemoveBg() to POST to /api/design/remove-bg instead of using the browser library
- Can uninstall @imgly/background-removal from frontend once done

### Pending / next steps

1. Replace @imgly/background-removal with Remove.bg API (immediate next task)
2. User needs gemini_api_key from aistudio.google.com added to vault
3. Veo 3.1 for video — discussed replacing Kling AI (same gemini_api_key), not implemented
4. Email intelligence UI (Tasks 2h-2l) — next major module per STATUS.md

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | Basic | Needs redesign |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Working | xterm.js WebSocket terminal |
| Gmail/Google | src/frontend/src/modules/gmail/ | Working | Real data loading |
| Tasks | src/frontend/src/modules/tasks/ | Not built | |
| Projects | src/frontend/src/modules/projects/ | Not built | |
| Finance | src/frontend/src/modules/finance/ | Basic | Needs redesign |
| Trading | src/frontend/src/modules/trading/ | Working | 6-tab layout, Alpaca, RoutinesTab updated this session |
| GitHub | src/frontend/src/modules/github/ | Stub | |
| Cloudflare | src/frontend/src/modules/cloudflare/ | Working | |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | Working | |
| Design | src/frontend/src/modules/design/ | Updated this session | Sprites+Recompose on Gemini, Edit on Ideogram, scissors BG removal |
| Settings | src/frontend/src/modules/settings/ | Partial | |
| Admin | src/frontend/src/modules/admin/ | Not built | |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
src/backend/src/services/creative.service.ts — Replaced replicatePost() with geminiGenerateImage(); generateSprite() uses Gemini + optional referenceImage; generateImg2Img() uses Gemini; getCreativeStatus() returns gemini instead of replicate
src/backend/src/services/trading-briefing.service.ts — Fixed bar.intradayChangePct -> bar?.changeFromOpen
src/backend/src/services/trading-scheduler.service.ts — Removed notional from INSERT
src/backend/src/routes/creative.routes.ts — Added POST /img2img; updated /sprite for referenceImageData; fallback status uses gemini
src/frontend/src/services/design.service.ts — CreativeStatus replicate->gemini; added img2img(); generateSprite() accepts referenceImageData/referenceMimeType
src/frontend/src/modules/design/Design.tsx — Recompose mode; Nano Banana 2 models; reference image upload for sprites; GalleryCard scissors button; removed strength slider; all status.replicate -> status.gemini
src/frontend/src/modules/design/design.css — Added .design-upload-zone--sm
src/frontend/src/modules/trading/tabs/RoutinesTab.tsx — Full rewrite: Built-in Briefings panel, Perplexity questions editor
src/frontend/src/modules/trading/trading.css — Added briefings-panel, perplexity-section styles
src/frontend/src/services/trading.service.ts — Added perplexity_queries to TradeRoutine; added triggerMorningBriefing() and triggerMiddayCheck()
```

---

## RECENT GIT COMMITS

No git history (repo not yet pushed to GitHub)

---

## OPEN ISSUES / KNOWN BUGS

- **Background removal too aggressive** — @imgly/background-removal ISNet clips edges on product photos. Replace with Remove.bg API — agreed but not done.
- **Gemini API key not yet in vault** — Sprites and Recompose disabled until user adds gemini_api_key from aistudio.google.com
- **Recompose returns data URI** — Gemini returns base64 not CDN URL. Stored as data URI in gallery/localStorage. Can hit quota with many large images. Acceptable for local app.
- **@imgly/background-removal still installed** — should be uninstalled once Remove.bg is wired in

---

## KEY DECISIONS MADE THIS SESSION

- **Replaced Replicate with Gemini (Nano Banana 2)** — one gemini_api_key covers sprites and recomposition; ~$0.045-0.08/image; Nano Banana 2 = gemini-3.1-flash-image-preview
- **Ideogram stays for Generate and Edit modes** — best text rendering, best inpainting
- **Remove.bg for background removal** — 50 free/month, ~$0.02/image, accurate for product photos; route through backend
- **Veo 3.1 discussed for video** — would replace Kling AI, same gemini_api_key; not yet built
- **Gemini returns data URIs not CDN URLs** — stored directly in gallery state and localStorage

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

- `POST /api/design/img2img` — Gemini scene recomposition; body: prompt, imageData, mimeType, model?
- `POST /api/design/edit` — Ideogram v3 inpainting; body: prompt, imageData, mimeType, maskData, maskMimeType + style options
- `POST /api/design/sprite` — updated to accept referenceImageData + referenceMimeType (optional)

---

## DATABASE CHANGES THIS SESSION

None.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- The scissors (Remove BG) button in GalleryCard calls handleRemoveBg() which currently uses @imgly/background-removal. Replace this with a fetch to POST /api/design/remove-bg.
- Remove.bg API: POST https://api.remove.bg/v1.0/removebg, header X-Api-Key, send image as FormData with image_file field, returns binary PNG. Vault key: remove_bg_api_key.
- The MaskEditorModal canvas uses alpha:false context to produce RGB PNG — Ideogram Edit requires this. Do not change.
- All three image modes (Generate/Edit/Recompose) in the Images tab share imagePrompt state but call different generate functions.
- Trading RoutinesTab built-in briefings panel is separate from user routines. perplexity_queries stored as JSON string in SQLite.

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

- **UnifyBI** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video. John was testing Nano Banana 2 and Ideogram for lifestyle imagery of the alarm clock.
- **Mobile Games** — planning phase, game concepts to be decided
