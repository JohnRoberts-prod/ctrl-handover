# CTRL Project Handover
*Last updated: 2026-04-23 UTC*
*Session ended: Infrastructure session — fixed handover URL, created ctrl-handover repo, updated /afk skill, ran permissions scan*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: D:\AI Work\Control-Centre\
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at D:\AI Work\.ctrl-data.db

---

## WHAT WE WERE BUILDING THIS SESSION

Infrastructure-only session — no CTRL features built. Completed the handover system:
moved from Gist (blocked by robots.txt in Claude web) to a dedicated public GitHub repo
(ctrl-handover). Created the repo, pushed the handover file, updated the /afk skill and
all instruction files to use the new raw.githubusercontent.com URL. Also ran the
fewer-permission-prompts skill and added 3 allowlist rules to .claude/settings.json.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- Created public GitHub repo: JohnRoberts-prod/ctrl-handover
- Pushed CTRL-HANDOVER.md to ctrl-handover repo
- Updated C:\Users\admin\.claude\skills\afk\SKILL.md — now pushes to ctrl-handover repo
- Updated D:\AI Work\CTRL-WEB-SESSION.md — new URL
- Updated D:\AI Work\CTRL-PROJECT-INSTRUCTIONS-FULL.md — new URL
- Updated D:\AI Work\Control-Centre\.claude\settings.json — added 3 permissions rules

### In progress right now
Nothing in progress — session ended cleanly.

### Pending / next steps
1. Add github_personal_token to vault (Settings > Integrations) — GitHub dashboard 404s without it
2. Push CTRL codebase to GitHub (critical path — in STATUS.md)
3. Email intelligence UI (Tasks 2h-2l) — NEXT BUILD PRIORITY per STATUS.md
4. Set up 4 daily trading routines in Routines tab

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | Basic | Needs redesign (skill-home.md to be written) |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Working | xterm.js + node-pty |
| Gmail/Google | src/frontend/src/modules/gmail/ | Working | Email intelligence backend complete, UI pending |
| Tasks | src/frontend/src/modules/tasks/ | Working | Google Tasks sync |
| Projects | src/frontend/src/modules/projects/ | Working | Board view |
| Finance | src/frontend/src/modules/finance/ | Basic | Needs dashboard redesign |
| Trading | src/frontend/src/modules/trading/ | Working | Alpaca paper trading, 6-tab layout |
| GitHub | src/frontend/src/modules/github/ | Built | Needs github_personal_token in vault |
| Cloudflare | src/frontend/src/modules/cloudflare/ | Working | DNS/zones/tunnels |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | Placeholder | |
| Settings | src/frontend/src/modules/settings/ | Partial | Trading section added, more needed |
| Admin | src/frontend/src/modules/admin/ | Placeholder | |

---

## FILES CREATED OR MODIFIED THIS SESSION

C:\Users\admin\.claude\skills\afk\SKILL.md — updated: pushes to ctrl-handover repo, fixed Step 3
D:\AI Work\CTRL-WEB-SESSION.md — updated: new handover URL
D:\AI Work\CTRL-PROJECT-INSTRUCTIONS-FULL.md — updated: new handover URL
D:\AI Work\Control-Centre\.claude\settings.json — updated: added permissions.allow block

---

## RECENT GIT COMMITS

No git history — CTRL codebase not pushed to GitHub yet (pending).

---

## OPEN ISSUES / KNOWN BUGS

- GitHub dashboard 404s until github_personal_token added to vault
- Trading terminal CLAUDE.md only loads on NEW sessions, not reconnects
- Gmail watch expires every 7 days — auto-renewal not yet built
- Port 3001 EADDRINUSE on restart — kill with netstat + taskkill
- PowerShell truncates long pasted commands — save as .ps1 and run

---

## KEY DECISIONS MADE THIS SESSION

- Handover storage: raw.githubusercontent.com/JohnRoberts-prod/ctrl-handover/main/CTRL-HANDOVER.md
  (Gist URLs are blocked by robots.txt in Claude web — this repo is not blocked)
- Permissions allowlist: only 3 rules added (gmail_search, gmail_get_inbox, findstr)
  Most commands already auto-allowed. Destructive/interpreter wildcards not allowlisted.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None — infrastructure session only.

---

## DATABASE CHANGES THIS SESSION

None.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

STATUS.md says next build priority is Email intelligence UI (Tasks 2h-2l).
All backend for email intelligence is complete (migrations, rules engine, keyword groups,
contact tagging, importance scoring, thread state). The UI layer is what remains.
Read skill-email-intelligence.md before starting.

GitHub dashboard built last session but will 404 until github_personal_token in vault.
Trading session dirs created — CLAUDE.md auto-loads skill files on NEW sessions only.
CTRL codebase not in git yet — no history, no push. Still on filesystem only.

---

## HOW TO START THE SYSTEM

D:\AI Work\START-ALL.bat

Or manually:
- Backend: npm run dev:backend
- Frontend: cd D:\AI Work\Control-Centre && npm run dev:frontend
- Terminal server: cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev

---

## PROJECTS OUTSIDE CTRL (for full context)

- UnifyBI — hospitality SaaS dashboard, planning phase, first client conversation pending
- BedBouncer — ESP32 smart alarm, Kickstarter prep, needs product video
- Mobile Games — planning phase, game concepts to be decided