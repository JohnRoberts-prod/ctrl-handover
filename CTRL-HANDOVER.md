# CTRL Project Handover
*Last updated: 2026-04-26 — BedBouncer Arduino V2 session*
*Session ended: Debugging white TFT screen on Arduino Mega — screen not responding to any forced ID*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a session. John Roberts is the developer.
Read this entire document before responding.

CTRL codebase: D:\AI Work\Control-Centre\
BedBouncer project: D:\AI Work\BedBouncer\
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Database: SQLite at D:\AI Work\.ctrl-data.db

---

## WHAT WE WERE BUILDING THIS SESSION

Built BedBouncer Arduino V2 firmware from scratch as a 5-file sketch with brand-accurate
display (charcoal/stone/amber palette), state machine (SLEEP/ALARM_FIRING/SILENCED/SET_TIME),
preset alarms (06:00-08:00), and partial screen redraws. After getting it to compile cleanly,
uploaded to hardware and hit a white screen issue. Tried forced IDs 0x9486, 0x9488, 0x7796,
0x9341 and reseated shield with no result. Session ended while diagnosing display ID.

---

## CURRENT BUILD STATE

### Completed this session

- D:\AI Work\BedBouncer\src\v2\BedBouncer_V2\Colors.h - RGB565 brand colour constants
- D:\AI Work\BedBouncer\src\v2\BedBouncer_V2\TimeUtils.h - RTC helpers
- D:\AI Work\BedBouncer\src\v2\BedBouncer_V2\Touch.h - TouchPoint struct, getTouch(ts), inRect()
- D:\AI Work\BedBouncer\src\v2\BedBouncer_V2\Display.h - all TFT draw functions
- D:\AI Work\BedBouncer\src\v2\BedBouncer_V2\BedBouncer_V2.ino - full state machine (compiles clean)
- CTRL Design module: disk-based image gallery (previous session)

### In progress

TFT screen not responding on hardware. Current forced ID in BedBouncer_V2.ino setup(): 0x9341
Red fill test (0xF800) also produced no response. Shield reseated - still white.
readID() returns garbage value 0x8686.

### Next steps (in order)

1. Run File > Examples > MCUFRIEND_kbv > diagnose_TFT_support on the Mega
   Watch screen for any colour change. Check Serial Monitor for which ID responds.
   If zero response - likely hardware damage to shield or Mega header pins.
2. Update forced ID in BedBouncer_V2.ino line ~162 to whatever diagnose finds
3. Remove red debug fill (lines 166-167 in setup()) once screen confirmed working
4. Test full state machine: sleep -> alarm fires -> stand up -> good morning -> re-entry re-fires
5. Calibrate touch coordinates in Touch.h getTouch() once screen works

---

## ALL MODULES - CTRL STATUS

Module       | Status  | Notes
-------------|---------|------
Home         | Basic   | Needs redesign
Claude Tab   | Working | xterm.js WebSocket terminal
Gmail        | Working | Real data, email intelligence
Projects     | Working | Project Spine phases 1-5 complete
Finance      | Basic   | Needs redesign
Trading      | Working | Alpaca connected, 6-tab layout
GitHub       | Stub    | ProjectSelector wired, Octokit not built
Cloudflare   | Working | Reference module pattern
Design       | Working | Disk-based gallery fixed
Settings     | Partial | Trading section only
Admin        | Working | Projects section live

---

## KEY DECISIONS THIS SESSION

- V2 firmware: 5 files (Colors / TimeUtils / Touch / Display / main .ino)
- TouchPoint/getTouch/inRect MUST live in Touch.h only - Arduino redefinition errors otherwise
- Brand colours: COL_BG charcoal #1C1C1E, COL_STONE #F5F0EB, COL_AMBER #C17D3C, COL_DUSK #8B7B9E, COL_SAGE #4A6741
- Arc depletes clockwise, switches dusk to amber at <=60 min remaining
- Preset alarms only for V2 (no WiFi): 06:00, 06:30, 07:00, 07:30, 08:00
- Re-fires alarm immediately when user returns to bed after silencing
- Auto-disarms after alarm fires for the day

---

## OPEN ISSUES

- TFT display: readID() returns 0x8686 garbage, all forced IDs tried show white screen
- V1 sketch missing show_string.h - cannot compile as hardware reference test
- Touch calibration not done (needs working screen first)
- CTRL not yet pushed to GitHub

---

## IMPORTANT CONTEXT

Arduino hardware: Mega + MCUFRIEND 480x320 parallel TFT + DS3231 RTC
FSR on A8, vibration pin 22, buzzer pin 24
Touch pins: XP=6, YP=A1, XM=A2, YM=7, OHMS=300
diagnose_TFT_support example is the correct next step for screen issue.

BedBouncer website live at bedbouncer.co.uk - Cloudflare Worker + D1 email signup working.

---

## HOW TO START CTRL

D:\AI Work\START-ALL.bat

Or manually:
  Backend:  npm run dev:backend  (in D:\AI Work\Control-Centre)
  Frontend: npm run dev:frontend (in D:\AI Work\Control-Centre)
  Terminal: npm run dev          (in D:\AI Work\Control-Centre\src\terminal-server)

---

## OTHER PROJECTS

- BedBouncer: website live, Arduino V2 firmware built but screen issue on hardware
- CTRLPro: planning phase, first client conversation pending
- Mobile Games: planning phase, game concepts to be decided
