# CTRL Project Handover
*Last updated: 2026-04-23 UTC*
*Session ended: John typed /afk — no active build task in progress this session*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.
When John returns, he will paste in any files or context from the web session.

The CTRL codebase is at: Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at 
---

## WHAT WE WERE BUILDING THIS SESSION

No active build task was in progress this session. John typed /afk immediately, so this handover is based entirely on STATUS.md and project memory. The current build priority per STATUS.md is Email Intelligence UI (Tasks 2h-2l). All email intelligence backend is complete. The next Claude Code session should start by reading skill-email-intelligence.md and picking up from Task 2h.

---

## CURRENT BUILD STATE

### Recently completed (prior sessions)
- Full email intelligence backend: SQLite migration 5, Gmail Pub/Sub webhook, rules engine (7 priorities), keyword groups, contact tagging, importance scoring, thread state, List-Unsubscribe parsing, 60-minute background sync
- Trading tab: Alpaca connected, 6-tab layout (Overview, Trade Log, Research, Weekly, Strategies, Routines), paper/live toggle, vault-backed credentials in Settings, SQLite migrations 1-16
- App shell, sidebar, topbar, Claude panel
- VaultGate UI (create and unlock vault)
- Google OAuth — real Gmail loading
- Gmail tab — real data displaying
- Claude tab — xterm.js WebSocket terminal
- Auth protection on all routes

### In progress right now
Nothing was in progress at /afk time. Clean stopping point.

### Pending / next steps
1. Email Intelligence UI — Tasks 2h-2l (DO FIRST — read skill-email-intelligence.md)
2. Notes tab — skill-notes.md designed 23 Apr, ClickUp Docs v3 API
3. GitHub integration service — skill-github.md needs designing before building
4. Settings tab completion — trading/perplexity/clickup sections
5. ClickUp integration — workspace auto-setup
6. Perplexity service — backend proxy
7. Push CTRL to GitHub (critical pending computer task)

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| App Shell / Sidebar | src/frontend/src/ | Working | Dark canvas, collapsible 200px/62px |
| VaultGate | src/frontend/src/modules/ | Working | Create and unlock vault |
| Google OAuth | src/backend/ | Working | Real Gmail loading |
| Gmail Tab | src/frontend/src/modules/gmail/ | Working | Real data, intelligence backend complete |
| Email Intelligence Backend | src/backend/ | Complete | Tasks 2a-2g done, UI 2h-2l pending |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Working | xterm.js WebSocket terminal |
| Home Tab | src/frontend/src/modules/home/ | Basic | Needs redesign — skill-home.md not yet written |
| Finance Tab | src/frontend/src/modules/finance/ | Basic | Needs redesign — skill-finance-dashboard.md |
| Trading Tab | src/frontend/src/modules/trading/ | Working | 6 tabs, Alpaca paper+live, strategies |
| GitHub Tab | src/frontend/src/modules/github/ | Stub | skill-github.md needs designing first |
| Notes Tab | — | Not built | skill-notes.md designed 23 Apr, ready to build |
| CRM Tab | — | Not built | skill-crm.md not written |
| Knowledge Base | — | Not built | skill-knowledge-base.md not written |
| Brand Toolkit | — | Not built | skill-brand-toolkit.md not written |
| Settings | src/frontend/src/modules/settings/ | Partial | Trading section done, others missing |
| Admin Tab | — | Not built | skill-admin.md not written |

---

## FILES CREATED OR MODIFIED THIS SESSION

None — /afk was called immediately with no build work this session.

---

## RECENT GIT COMMITS

No git history available — CTRL has not yet been pushed to GitHub.
Pushing CTRL to GitHub is listed as a critical pending computer task in STATUS.md.

---

## OPEN ISSUES / KNOWN BUGS

- PowerShell truncates long pasted commands — save as .ps1 and run instead
- Port 3001 EADDRINUSE on restart — kill with: netstat -ano | findstr :3001, then taskkill /PID [pid] /F
- Gmail watch expires every 7 days — auto-renewal not yet built
- CTRL not yet on GitHub — this is a critical pending task

---

## KEY DECISIONS MADE THIS SESSION

No decisions made this session. Refer to CLAUDE.md for all standing decisions:
- CTRL is a local web app (not desktop)
- Google OAuth for login — no username/password
- AES-256-GCM vault for all secrets
- Config blob at D:AI Work.ctrl-config.json
- File system sandboxed to D:AI Work only
- SQLite first — never query Gmail API directly from display routes
- Claude API only called when user explicitly asks (zero background token use)
- Staging: one click. Live: explicit confirmation required

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None this session.

---

## DATABASE CHANGES THIS SESSION

None this session. Current schema is through migration 16 (trading tables).

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. READ skill-email-intelligence.md FIRST — this is the next build task (2h-2l)
2. Notes tab skill file is designed and ready — skill-notes.md exists and is ready to build after email UI
3. GitHub tab CANNOT be built yet — skill-github.md must be designed in Claude.ai first
4. CTRL is not on GitHub yet — before any destructive local changes, manually push first
5. Gmail Pub/Sub webhook needs a Cloudflare Tunnel URL — public webhook not yet configured
6. Alpaca paper account may not be signed up yet — check before testing trading features
7. The email tagging system is a core behavior: tag every email touched, learn tags continuously

---


## CRITICAL RULE — NO ANTHROPIC API KEY

John runs Claude Code on a Max plan subscription continuously.
**There is NO separate Anthropic API key. Never suggest it.**
Never build features that make direct Anthropic SDK calls.
All AI assistance happens through Claude Code itself.
If a feature needs AI reasoning, it must be user-initiated through Claude Code — not autonomous background API calls.
Existing references to anthropic_api_key in the codebase (trading-briefing.service.ts, claude.service.ts) are flagged for redesign.

---
## HOW TO START THE SYSTEM

\
Or manually:
- Backend: - Frontend: - Terminal server: 
---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLPro** — formerly UnifyBI, AI hospitality SaaS dashboard, planning phase. Domains purchased. First client conversation needed before building. Based on public hospitality knowledge only — no Lane7 data ever.
- **BedBouncer** — ESP32 smart alarm (weight sensor, silences when you leave bed). Working prototype exists. Needs product video for Kickstarter relaunch. Retail £60, early bird £39-45.
- **Mobile Games** — planning phase, game concepts to be decided, free-to-play hybrid monetisation
