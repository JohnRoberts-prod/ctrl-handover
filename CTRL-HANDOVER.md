# CTRL Project Handover
*Last updated: 2026-05-30 11:45 UTC*
*Session ended: After scaffolding RoadToCtrl + writing tomorrow's day brief. Big multi-project session — mobile app Phase 2 + auth bypass + PowerShell 7 swap + roadtoctrl creation + /afk modification.*

---

## ⚠ PRIMARY READ — START HERE

**Tomorrow's plan is in: `D:\AI Work\daily-briefs\2026-05-30.md`**

That doc is THE day plan with six 90-min blocks, win conditions, file paths, and all context. Read it first. This handover is the backup/deeper-context document for cross-referencing what was built today.

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser (or a new Claude Code session) picking up after a big multi-project working day. John Roberts is the developer/builder. Read this entire document before responding, but the day brief above is the action document.

- CTRL backend: `D:\AI Work\Control-Centre\` — Node.js + Express + TypeScript on port 3001
- CTRL frontend: React 18 + Vite + TypeScript on port 5173
- Terminal server: node-pty WebSocket on port 3002
- Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`
- Mobile app: `D:\AI Work\CTRL-Mobile\CTRLMobile\` — React Native 0.85, NativeWind, installed on John's phone as `com.ctrlmobile`

---

## WHAT WE WERE BUILDING THIS SESSION

Multi-project session that started as mobile-app polish and grew. Three big threads:

1. **CTRL Mobile Phase 2** — added 12 real screens (Inbox/Detail, Tasks/Add, Settings, MoreScreen hub, Projects/Detail, Finances, Games, Knowledge/DocView, CRM/ContactDetail, GitHub, Cloudflare, Design/ImageView). Each with services, loading skeletons, error states, pull-to-refresh.

2. **RoadToCtrl YouTube channel project created** — full scaffold at `D:\AI Work\roadtoctrl\`. Spine DB row materialised. **/afk modified to auto-append video ideas to `roadtoctrl\knowledge\video-ideas.md` every session.**

3. **Tooling fixes:**
   - Terminal-server bound to `127.0.0.1` — flipped to `0.0.0.0` in `.env`.
   - powershell.exe → pwsh.exe (PowerShell 7) — needed MSI install, NOT the Microsoft Store version (node-pty can't follow App Execution Aliases).
   - Mobile Claude tab removed (was duplicate of Terminal tab).
   - Inbox query-params bug (`inbox=true` was a string filter, needed `gmail_label=UNREAD`).
   - Knowledge wire-format bug (objects not strings, `content` not `body`).

---

## CURRENT BUILD STATE

### Recently completed (this session)

**Mobile (CTRL-Mobile/CTRLMobile):**
- All 12 new screens
- Services for each: inbox, tasks, projects, finance, games, knowledge, crm, github, cloudflare, design
- `src/services/api.service.ts` — added `.patch()` method
- Mobile bug fixes: `tabBarHideOnKeyboard: true`, WS transient errors swallowed, CR-redraw spinner collapse, knowledge crash fix
- 57 files staged, NOT committed (no remote, no git user.email — needs manual setup)

**Backend (Control-Centre):**
- `src/routes/dev-auth.routes.ts` — NEW. LAN-only mobile auth bypass. **Delete when real Google OAuth ships.**
- `src/terminal-server/src/server.ts` — shell now `${process.env.ProgramFiles}\PowerShell\7\pwsh.exe`
- `src/terminal-server/.env` — HOST=0.0.0.0
- **Control-Centre is NOT a git repo** — changes on disk only.

**RoadToCtrl scaffold:**
- Full canonical layout + channel-specific subfolders (scripts/, thumbnails/, uploads/)
- README, CLAUDE.md, SESSION_STATE, LEARNINGS, design/PRODUCT.md, skills/skill-roadtoctrl-workflow.md
- knowledge/video-ideas.md — seeded with 3 initial + 3 session-end ideas
- Registered in .ctrl-config.json (#ef4444)
- Spine DB row created (chart-3, sortOrder 18)
- Memory pointer at ~/.claude/projects/d--AI-Work/memory/project_roadtoctrl.md

**/afk SKILL.md modified:** new Step 0.D for video idea generation, strict skip rules, Lane7 hard line baked in.

**Daily brief:** `D:\AI Work\daily-briefs\2026-05-30.md` — full timetable for tomorrow.

### In progress right now
Nothing functionally half-done. Phase 2 mobile screens installed but only Inbox + Knowledge post-fix-tested. Others may have wire-format bugs lurking.

### Pending / next steps
**See `D:\AI Work\daily-briefs\2026-05-30.md` for the full timetable.** Six blocks: SA bugs / CF bugs / banks / BedBouncer-Shelbie workflow / task tidy / Play Store production-push.

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home (mobile) | src/screens/home/ | Working | Pre-Phase-2 |
| Terminal (mobile) | src/screens/terminal/ | Working | PowerShell 7 backed, CR-redraw fixed |
| Inbox (mobile) | src/screens/inbox/ | Working | Fixed mid-session; Compose is placeholder |
| Tasks (mobile) | src/screens/more/Tasks*.tsx | Working | Sectioned by date |
| Settings (mobile) | src/screens/more/Settings*.tsx | Working | Host, vault, sign out |
| More hub (mobile) | src/screens/more/MoreScreen.tsx | Working | 10 tiles |
| Projects (mobile) | src/screens/more/Projects*.tsx | Built, not verified | List + Detail with summary |
| Finances (mobile) | src/screens/more/FinancesScreen.tsx | Built, not verified | Monthly summary + recent txns |
| Games (mobile) | src/screens/more/GamesScreen.tsx | Built, not verified | Metro start/stop |
| Knowledge (mobile) | src/screens/more/Knowledge*.tsx | Working | Fixed mid-session |
| CRM (mobile) | src/screens/more/CRM*.tsx | Built, not verified | Contacts + Detail |
| GitHub (mobile) | src/screens/more/GitHubScreen.tsx | Built, not verified | Repos list |
| Cloudflare (mobile) | src/screens/more/CloudflareScreen.tsx | Built, not verified | Zones list |
| Design (mobile) | src/screens/more/Design*.tsx | Built, not verified | Gallery + ImageView |

---

## FILES CREATED OR MODIFIED THIS SESSION

### NEW
```
CTRL-Mobile/CTRLMobile/src/screens/inbox/{Inbox,EmailDetail}Screen.tsx
CTRL-Mobile/CTRLMobile/src/screens/more/{More,Tasks,AddTask,Settings,Projects,ProjectDetail,Finances,Games,Knowledge,DocumentView,CRM,ContactDetail,GitHub,Cloudflare,Design,ImageView}Screen.tsx
CTRL-Mobile/CTRLMobile/src/services/{inbox,tasks,projects,finance,games,knowledge,crm,github,cloudflare,design,terminal}.service.ts
Control-Centre/src/backend/src/routes/dev-auth.routes.ts
roadtoctrl/ — full new project scaffold
daily-briefs/2026-05-30.md
~/.claude/projects/d--AI-Work/memory/project_roadtoctrl.md
```

### MODIFIED
```
CTRL-Mobile/CTRLMobile/src/services/api.service.ts — added .patch()
CTRL-Mobile/CTRLMobile/src/navigation/{AppNavigator,InboxStack,MoreStack,TabIcons,types}.{tsx,ts}
CTRL-Mobile/CTRLMobile/src/screens/auth/LoginScreen.tsx — vault unlock flow
CTRL-Mobile/CTRLMobile/src/screens/home/HomeScreen.tsx — CTA → TerminalTab
CTRL-Mobile/CTRLMobile/src/screens/terminal/TerminalScreen.tsx — CR-redraw, preload param
Control-Centre/src/backend/src/server.ts — devAuthRouter mounted
Control-Centre/src/terminal-server/src/server.ts — pwsh via ProgramFiles
Control-Centre/src/terminal-server/.env — HOST=0.0.0.0
Control-Centre/SESSION_STATE.md — current state
Control-Centre/LEARNINGS.md — 9 new entries
.ctrl-config.json — roadtoctrl entry
CLAUDE.md — RoadToCtrl section
~/.claude/skills/afk/SKILL.md — Step 0.D added
~/.claude/projects/d--AI-Work/memory/MEMORY.md — pointer added
```

### DELETED
```
CTRL-Mobile/CTRLMobile/src/navigation/ClaudeStack.tsx
CTRL-Mobile/CTRLMobile/src/screens/claude/ (folder)
CTRL-Mobile/CTRLMobile/src/services/claude.service.ts
```

---

## RECENT GIT COMMITS

- Control-Centre is **NOT a git repo** — fatal: not a git repository.
- CTRL-Mobile/CTRLMobile is a git repo with **no commits yet** — fatal: branch 'main' has no commits.
- batondrop pushed during /afk Step 0.5 (auto-backup of linked repos in the project_repos DB table).

Today's backend + mobile work is on local disk only. Not on GitHub.

---

## OPEN ISSUES / KNOWN BUGS

- Mobile app Phase 2 screens not individually verified — Projects/Finances/Games/CRM/GitHub/Cloudflare/Design built in one batch; treat as suspect.
- Compose mobile screen is placeholder — no backend send-email endpoint exists.
- TaskDetail / GameDetail placeholders — parent screens cover the use case.
- CTRL-Mobile/CTRLMobile no GitHub remote, no commits.
- Control-Centre not under version control at all — biggest risk in the codebase.

---

## KEY DECISIONS MADE THIS SESSION

1. Mobile Claude tab killed — Terminal tab covers the use case (persistent REPL).
2. Dev-auth bypass kept as a removable single-file shim.
3. PowerShell 7 via MSI not Microsoft Store (node-pty doesn't follow App Execution Aliases).
4. Shell path uses `process.env.ProgramFiles` — Zero Hardcoding satisfied.
5. /afk Step 0.D auto-generates RTC video ideas. Strict skip rules.
6. RoadToCtrl voice locked: solopreneur journey, mix shorts + weekly long-form.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
POST /api/auth/dev-login   → LAN-only. Returns { token: string }.
GET  /api/auth/me          → Bearer token → AuthUser.
POST /api/auth/logout      → invalidates token.
```

All three mounted under `/api/auth` from `dev-auth.routes.ts`.

---

## DATABASE CHANGES THIS SESSION

No schema migrations. One Spine project row added via `POST /api/projects` (roadtoctrl). Google Tasks list `CTRL: RoadToCtrl` auto-created via SpineService hook.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Vault was unlocked at session end.** If it's locked, dev-auth mobile flow still works but most other API routes 401.
- **Mobile installs as `com.ctrlmobile`.** APK builds via `cd android && ./gradlew.bat assembleRelease`. Phone disconnects during long builds — expect this.
- **Today's most important code is not in version control.** Worth setting up Control-Centre as a git repo soon.
- **Tomorrow is a MULTI-PROJECT operating day, not a CTRL-coding day.** The daily brief is the plan.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

NOTE: that script tries to `npm run dev` in `D:\AI Work\CTRL-Mobile\` but the RN app moved into the CTRLMobile/ subfolder, so step [3/4] fails. The desktop CTRL comes up fine. Mobile app launches from the phone.

If `pm2 list` shows ctrl-backend missing, the backend may be running via `npm run dev` directly — verify with `Get-NetTCPConnection -LocalPort 3001`.

---

## PROJECTS OUTSIDE CTRL (for full context)

- **CTRLPro** — hospitality SaaS, planning, first client pending. **Lane7 hard line: never CTRLPro → Lane7.**
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, website live at bedbouncer.co.uk. Partner Shelbie handles content. Tomorrow Block 4 = her workflow.
- **Mobile Games** — SA + CF ready for tomorrow's production push. BatonDrop already in production. WordDrop/WordChain/ReflexRing in various stages.
- **RoadToCtrl** — NEW today. YouTube channel, avatar-hosted, multi-product storyline. Lane7 hard line applies forever.
