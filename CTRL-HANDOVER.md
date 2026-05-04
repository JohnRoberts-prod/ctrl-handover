# CTRL Project Handover
Last updated: 2026-05-04 UTC
Session ended: BedBouncer hardware -- FSR + buzzer confirmed working, alarm test pending

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

CTRL codebase: D:\AI Work\Control-Centre\
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Database: SQLite at D:\AI Work\.ctrl-data.db

---

## WHAT WE WERE BUILDING THIS SESSION

Three areas covered:

1. BedBouncer hardware prototype -- wired FSR pressure sensor (GPIO32, 10k resistor) and active buzzer (GPIO25). Both confirmed working. Full firmware with alarm logic, re-trigger window, web API uploaded and running on ESP32.

2. BedBouncer website CSP -- restored security header tightening. Extracted all JS to site.js, converted 18 inline onclick handlers to addEventListener, removed unsafe-inline from script-src, redeployed both Cloudflare workers.

3. BedBouncer marketing -- rewrote all 25 image prompts with problem/solution strategy: 10 no-clock posts (problem/intrigue), 9 with-clock posts (solution/aspirational), 4 stat cards, 2 bouncer character posts.

---

## BEDBOUNCER HARDWARE -- CONFIRMED CONFIG

FSR pin: GPIO32 (GPIO34 was DEAD on this board -- do not suggest it)
Buzzer pin: GPIO25 -- active buzzer, use digitalWrite HIGH/LOW NOT ledcWrite
10k resistor: pulldown from GPIO32 to GND. FSR between 3.3V and GPIO32.
FSR readings: idle=0, hand press=1300-1700. Threshold=400.
Buzzer volume quiet at 3.3V -- production needs 5V via transistor.

---

## BEDBOUNCER FIRMWARE -- API

ESP32 runs web server. Get IP from OLED on boot.
GET  http://[IP]/status   -- returns time, alarm config, alarmActive, personInBed, fsr raw value
POST http://[IP]/alarm    -- body: {"hour":7,"minute":30,"enabled":true}

To test alarm in PowerShell (replace IP and set minute 1 ahead of current time):
Invoke-RestMethod -Uri "http://192.168.1.70/alarm" -Method POST -ContentType "application/json" -Body '{"hour":22,"minute":15,"enabled":true}'

---

## NEXT STEPS IN ORDER

1. Alarm test: POST /alarm 1 min ahead, press FSR, verify buzzer+OLED fires, release to dismiss, re-press within 10 min to test re-trigger
2. Real-weight test: slide FSR under mattress, confirm inBed:YES through bedding, tune threshold if needed (currently 400)
3. Generate 25 social media images using Design tab (prompts at D:\AI Work\BedBouncer\knowledge\design-image-prompts.md)
4. Write June + July campaign posts (PART2 and PART3)
5. Write email sequence (8 pre-launch emails)
6. Write paid ad copy (Meta, TikTok, Reddit)

---

## FILES MODIFIED THIS SESSION

D:\AI Work\BedBouncer\firmware\BedBouncer\src\main.cpp -- full firmware, FSR GPIO32, active buzzer GPIO25
D:\AI Work\BedBouncer\site.js -- new file: all JS extracted from index.html
D:\AI Work\BedBouncer\index.html -- removed 18 onclick=, replaced inline script with site.js reference
D:\AI Work\BedBouncer\security-worker\index.js -- removed unsafe-inline from script-src
D:\AI Work\BedBouncer\knowledge\design-image-prompts.md -- complete rewrite, 25 prompts, problem/solution split
D:\AI Work\Control-Centre\src\frontend\src\core\Sidebar.tsx -- added bed-bouncer to Build group
D:\AI Work\Control-Centre\src\frontend\src\core\nav.config.ts -- added BedDouble icon and nav item
D:\AI Work\Control-Centre\src\frontend\src\core\AppShell.tsx -- added BedBouncer module render
D:\AI Work\.ctrl-config.json -- added bedbouncer.deviceIp

---

## OPEN ISSUES

- FSR threshold 400 confirmed for hand press only -- not yet tested under mattress + bedding
- Active buzzer quiet at 3.3V GPIO -- production needs 5V via NPN transistor
- GPIO34 dead on this ESP32 board -- do not use it, GPIO32 is the FSR pin
- Alarm end-to-end test not completed

---

## KEY DECISIONS

- GPIO32 for FSR (GPIO34 unresponsive -- always test ADC pins with 3.3V before trusting pinout diagrams)
- Active buzzer: digitalWrite HIGH/LOW not ledcWrite
- Image strategy: no-clock posts for reach (problem), with-clock posts for conversion (solution), ratio 7:3 in May
- CSP: unsafe-inline blocks both script blocks AND onclick= handlers -- must extract ALL JS before removing it

---

## HOW TO START CTRL

D:\AI Work\START-ALL.bat

Or manually:
Backend:  cd "D:\AI Work\Control-Centre" && npm run dev:backend
Frontend: cd "D:\AI Work\Control-Centre" && npm run dev:frontend

---

## OTHER PROJECTS

BedBouncer -- ESP32 smart alarm, hardware prototype working, Kickstarter prep in progress
CTRLPro -- hospitality SaaS, planning phase, first client conversation pending
Mobile Games -- planning phase, game concepts to be decided
