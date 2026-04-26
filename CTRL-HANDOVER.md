# CTRL Project Handover
*Last updated: 2026-04-26 16:35 UTC*
*Session ended: Audited and fixed all hardcoded paths in the self-building knowledge system; project creation fully dynamic from DB.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

---

## WHAT WE WERE BUILDING THIS SESSION

**Part 1 - Bug fix:** Fixed persistent "Unexpected end of JSON input" error on projects API.
Root causes: (1) GET handlers had no try/catch so exceptions returned HTML from Express default error handler.
(2) Static routes /flat /list /tasks were defined after /:id so Express matched them as id parameters.

**Part 2 - Self-building knowledge system:** Wired full project memory system.
Every project now auto-gets LEARNINGS.md, SESSION_STATE.md, CLAUDE.md, and a /project-<id> slash command
when created from Admin. Everything reads from the projects DB table - no hardcoded project names anywhere.
Sub-projects (BatonDrop under Mobile-Games) share memory with their parent folder.
Session ended confirming no hardcoded paths remain: fixed session-end hook whitelist,
Admin form missing folderPath/parentId fields, and /afk routing table hardcoded paths.

---

## CURRENT BUILD STATE

### Recently completed (this session)

- projects.routes.ts - try/catch on all GET handlers; /flat /list /tasks moved before /:id; POST /:id/ensure-files added
- projects.service.ts - ensureProjectFiles(), generateProjectSkill(), generators added; sub-project memory routing via parentId
- Admin.tsx - folderPath text input + parentId dropdown in create form; Init Files button per project row
- session-end-learning.js - removed hardcoded project whitelist; uses CWD/skills/ always
- session-start-context.js - NEW: session start hook, reads LEARNINGS.md count in CWD
- ~/.claude/settings.json - autoMemoryEnabled + Stop/SessionStart hooks added
- ~/.claude/skills/afk/SKILL.md - Step 0 added; routing table now dynamic via API
- D:\AI Work\CLAUDE.md - routing table replaced with dynamic GET /api/projects/flat instruction
- skill-project-spine.md - new CLAUDE CODE INTEGRATION section added
- /project-* skill files - generated for personal, bedbouncer, ctrlpro, mobile-games, ctrl, batondrop
- BatonDrop added to projects DB as sub-project of mobile-games
- LEARNINGS.md - 5 new entries appended
- SESSION_STATE.md - updated

### In progress right now
Nothing half-finished. Session ended clean.

### Pending / next steps
1. Continue wiring modules to Project Spine - Tasks, Finance, Email tabs (Phase 5 in skill-project-spine.md)
2. Test Admin create form end-to-end: create project with folderPath, verify all files appear on disk
3. Add Claude skill creation step to project creation feedback UI
4. Build knowledge index file watcher (skill-project-spine.md step 11)

---

## ALL MODULES - STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | Built | Basic layout |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Built | Slide-in panel |
| Gmail/Google | src/frontend/src/modules/gmail/ | Built | SQLite-first sync |
| Tasks | src/frontend/src/modules/tasks/ | Built - needs Spine wiring | ProjectSelector not yet on DB projects table |
| Projects | src/frontend/src/modules/projects/ | Built | ProjectSelector wired |
| Finance | src/frontend/src/modules/finance/ | Built - needs Spine wiring | Transactions not yet FK to projects |
| Trading | src/frontend/src/modules/trading/ | Built | Standalone |
| GitHub | src/frontend/src/modules/github/ | Built | Standalone |
| Cloudflare | src/frontend/src/modules/cloudflare/ | Built | Standalone |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | Built - needs Spine wiring | Tabs not from DB yet |
| Settings | src/frontend/src/modules/settings/ | Built | |
| Admin | src/frontend/src/modules/admin/ | Updated this session | folderPath + parentId in create form |

---

## FILES MODIFIED THIS SESSION

```
src/backend/src/routes/projects.routes.ts - try/catch on GETs; static routes before /:id; POST /:id/ensure-files
src/backend/src/services/projects.service.ts - ensureProjectFiles() + generators; sub-project memoryPath routing
src/frontend/src/modules/admin/Admin.tsx - folderPath + parentId in create form; Init Files button
C:\Users\admin\.claude\hooks\session-end-learning.js - removed hardcoded whitelist; CWD/skills/ always
C:\Users\admin\.claude\hooks\session-start-context.js - NEW file created
C:\Users\admin\.claude\settings.json - autoMemoryEnabled + hooks
C:\Users\admin\.claude\skills\afk\SKILL.md - Step 0 + dynamic routing
D:\AI Work\CLAUDE.md - dynamic routing table
D:\AI Work\Control-Centre\skills\skill-project-spine.md - CLAUDE CODE INTEGRATION section
D:\AI Work\Control-Centre\LEARNINGS.md - 5 new entries
D:\AI Work\Control-Centre\SESSION_STATE.md - updated
~/.claude/skills/project-{personal,bedbouncer,ctrlpro,mobile-games,ctrl,batondrop}/SKILL.md - all generated
```

---

## RECENT GIT COMMITS

No git repository initialised - no commit history.

---

## OPEN ISSUES / KNOWN BUGS

- No git repo. All changes are filesystem only - no version history.
- Admin folderPath input has no server-side path validation (acceptable for single-user local tool).
- Project creation progress UI does not yet show the Claude skill generation step.

---

## KEY DECISIONS MADE THIS SESSION

1. Sub-project memory routing: LEARNINGS.md and SESSION_STATE.md always live in parent folder.
   BatonDrop shares memory with Mobile-Games. Game dev knowledge accumulates at parent level.

2. Skill files always overwrite (DB-derived), memory files use writeIfMissing (protect user data).

3. No hardcoded project names in hooks or routing tables anywhere.
   session-end hook uses CWD/skills/ always. Routing tables use GET /api/projects/flat + CWD match.
   Adding a new project requires zero changes to any hook or instruction file.

4. BatonDrop added to DB as sub-project of mobile-games.
   /project-batondrop skill routes memory to Mobile-Games folder.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

POST /api/projects/:id/ensure-files - regenerates LEARNINGS.md, SESSION_STATE.md, CLAUDE.md,
and /project-<id> skill from DB record. Safe to call repeatedly. Called by Admin Init Files button.

---

## DATABASE CHANGES THIS SESSION

- BatonDrop row inserted: id=batondrop, parentId=mobile-games, folderPath=D:\AI Work\Mobile-Games\games\batondrop\
- No schema migrations this session

---

## IMPORTANT CONTEXT FOR NEXT SESSION

The Project Spine system is now fully wired for Claude Code.
Any new project created through Admin automatically gets /project-<id> skill, LEARNINGS.md,
SESSION_STATE.md, CLAUDE.md - all from DB, nothing hardcoded.

To add a new project and start working on it:
1. Admin -> Projects -> New Project -> fill in name, shortCode, folderPath
2. /project-<id> appears in Claude Code autocomplete immediately
3. Type /project-<id> to switch - session state saves, new project context loads

skill-project-spine.md Phase 5 is the next major build: wiring Tasks, Finance, Email, Brand Toolkit
tabs to use projects from the DB table rather than string-based project tags.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: cd "D:\AI Work\Control-Centre" && npm run dev:backend
- Frontend: cd "D:\AI Work\Control-Centre" && npm run dev:frontend
- Terminal server: cd "D:\AI Work\Control-Centre\src\terminal-server" && npm run dev

---

## PROJECTS OUTSIDE CTRL

- BedBouncer - ESP32 smart alarm, Kickstarter prep, needs product video. /project-bedbouncer skill exists.
- Mobile Games - BatonDrop drop reflex game in development. /project-batondrop and /project-mobile-games skills exist.
- CTRLPro - Hospitality SaaS, planning phase. /project-ctrlpro skill exists.