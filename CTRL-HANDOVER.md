# CTRL Project Handover
*Last updated: 2026-05-19 (end of long session)*
*Session ended: Vertex AI setup partway through — service account JSON in vault, two more vault keys pending. Also: BatonDrop got approved for Google Play production today.*

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

## 🎉 HEADLINE NEWS

**BatonDrop is LIVE on Google Play (approved 2026-05-19).** First CTRL Play game to clear the bar. Submitted 2026-05-17, 2-day turnaround. All post-launch monitoring tasks (review responses, Crashlytics, AdMob fill rate, Day-1 retention check) are seeded in BatonDrop's Tasks list — start there.

---

## WHAT WE WERE BUILDING THIS SESSION

A multi-hour session that touched almost every CTRL module. Final activity before /afk: configuring Vertex AI so Gemini calls consume the £225 GCP credit on project `ctrl-493720`. The service account is created and its JSON key is in the vault under `vertex_service_account`. The Settings → AI Providers status page reports it needs **two more vault keys** (`vertex_project_id` + `vertex_region`) before flipping to "configured" — those are the first clicks tomorrow.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- **Tasks tab — Monday-clone rebuild** (Google Tasks-backed, project hierarchy with Mobile Games as parent, 10 sub-games nested, collapsed by default, QuickAdd per group, expandable rows with notes/subtasks/priority/due chips)
- **Projects tab removed entirely** — Home rewritten to read tasks from Google Tasks via `project_lists` mapping
- **Home — widget grid v2** — 6 widgets (Finance, Today, Priority, This Week, Pulse, Recent emails) in a 12-col CSS grid, responsive collapse to 6-col on mobile
- **Admin Projects — big-card rebuild** — website/code/tasks slots, inline colour picker, status badges, sub-projects indented (no side-stripe)
- **Project colours — chart-N palette** — auto-assigned on creation, theme-aware via `var(--chart-N)`
- **New `/ctrl-design-review <file>` skill** — runs Nielsen + anti-pattern scanner against the CTRL design rubric
- **New `/project <id>` generic switcher** — replaces 11 per-project skills (deleted)
- **`/project-create` updated** with Step 6.5 so new projects appear in Admin + Tasks immediately
- **Skill audit** — `skill-discord.md` (1,204 lines) and `skill-clickup.md` no longer auto-load on every CTRL session
- **Brand portal fixes** — dual-location resolver, `Promise.allSettled`, empty-state UX, CTRL now appears
- **Migration 42** (project_lists) + **Migration 43** (drop project_tasks after migrating Cavernborn's 19 rows to Google)
- **`/api/tasks/cached-all`** single round-trip read replaces N+1 fetches
- **Gmail.tsx setState bug fix** — archive/trash undo no longer double-fires under Strict Mode
- **Rate limit bumped 500→5000/15min, JSON 429 body**
- **114 tasks seeded** across 14 projects
- **BatonDrop status updated to LIVE** + 9 post-launch monitoring tasks added

### In progress right now
**Vertex AI setup.** Service account `ctrl-vertex@ctrl-493720.iam.gserviceaccount.com` created with `Vertex AI User` role. JSON key pasted into vault as `vertex_service_account`. Status: *"Vertex AI is not configured. Set vault keys vertex_service_account, vertex_project_id, and vertex_region to use it."* — two of three keys still missing.

### Pending / next steps (START HERE)
1. **Add vault key** `vertex_project_id` = `ctrl-493720`
2. **Add vault key** `vertex_region` = `us-central1`
3. **Refresh Settings → AI Providers** — status should flip green
4. **Test:** Design module → generate a small image
5. **30 min later:** Cloud Console → Billing → confirm credit usage on `ctrl-493720` is non-zero

All 5 are now Tasks in CTRL's list (high priority). BatonDrop has 9 post-launch tasks queued.

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | ✅ Widget grid v2 | 6 widgets, reads Google Tasks via project_lists |
| Claude Tab | src/frontend/src/modules/claude-tab/ | ✅ Working | No changes |
| Gmail/Google | src/frontend/src/modules/gmail/ | ✅ setState bug fixed | Archive/trash undo no longer double-fires |
| Tasks | src/frontend/src/modules/tasks/ | ✅ Monday-clone | Mobile Games has 10 sub-games nested |
| ~~Projects~~ | (removed) | ❌ Deleted | Module + nav gone; Home reads Google Tasks |
| Finance | src/frontend/src/modules/finance/ | ✅ Working | No changes |
| Trading | src/frontend/src/modules/trading/ | ✅ Working | No changes |
| GitHub | src/frontend/src/modules/github/ | ✅ Working | No changes |
| Cloudflare | src/frontend/src/modules/cloudflare/ | ✅ Working | No changes |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | ✅ Resilient | Dual-location resolver + allSettled + empty-state UX; CTRL appears |
| Settings | src/frontend/src/modules/settings/ | ✅ Vertex toggle present | AIProvidersSection live |
| Admin | src/frontend/src/modules/admin/ | ✅ Big-card rebuild | Inline colour picker, sub-projects nested |

---

## CONTENT POPULATED THIS SESSION

| Project | Tasks | Brand | Knowledge |
|---|---|---|---|
| Personal | 3 | n/a | n/a (left as-is) |
| BedBouncer | 11 | ✅ 5/5 | ✅ 14 files |
| **CtrlPro** | 6 | ✅ 5/5 | (knowledge skipped per user) |
| Mobile Games (parent) | — | — | 4 shared docs |
| CTRL | 3+5 | ✅ 5/5 (new) | ✅ 9 files |
| BatonDrop | 7+9 | ✅ 5/5 | ✅ 12 files |
| WordDrop | 9 | ✅ 5/5 | ✅ 4 files |
| Chess Music | 5 | ✅ 5/5 | ✅ 2 files |
| **Homeland** | 5 | 0/5 | (left as-is per user) |
| Cavernborn | 31 | ✅ 5/5 | ✅ 7 files |
| CrunchBall | 6 | ✅ 5/5 | ✅ 2 files |
| Stack Attack | 11 | ✅ 5/5 | 1 + shared |
| Word Chain | 12 | ✅ 5/5 | 1 + shared |
| Colour Flood | 12 | ✅ 5/5 | 1 + shared |
| Reflex Ring | 12 | ✅ 5/5 | 1 + shared |

---

## OPEN ISSUES / KNOWN BUGS

- **Vault re-locks on every backend restart.** Any ts-node-dev reload clears vault state. Re-unlock before testing protected endpoints.
- **Rate limit was bumped** to 5000/15min after one bust. JSON 429 body now (so frontend parser doesn't crash on "Too many r..." text).
- **WordDrop folder rename pending** — Windows file lock won't release. Cosmetic only — no code depends on path.
- **`/project-create` JSON-escape gotcha** — `node -e "..."` with Windows backslashes via MSYS bash mangles them. Always write to a temp `.cjs` file. Documented in skill.
- **`.home-card / .home-focus` CSS classes (~250 lines) dead** after widget v2 — backlog cleanup.
- **Admin.tsx heavy inline styles** — backlog migration to admin.css.

---

## KEY DECISIONS MADE THIS SESSION

- **Tasks are Google Tasks-backed, not local.** `project_tasks` table dropped. Each project owns one Google Tasks list.
- **Brand files have two valid locations** — `brand/guidelines/<file>` (canonical) OR `brand/<file>` (legacy). Dual resolver reads both.
- **Promise.allSettled, not Promise.all,** for any multi-fetch screen. Same root cause blanked Tasks + Brand in this session.
- **Project colours use `chart-N` slots,** not hex. Theme-aware. Auto-assigned.
- **Mobile Games is a container project.** No own tasks/brand. Sub-games inherit shared docs.
- **Per-project switcher skills gone.** One generic `/project <id>` reads the DB.
- **Vertex AI for Gemini** to use the £225 GCP credit. Default stays AI Studio until toggle flips.

---

## BACKEND API ENDPOINTS ADDED

```
GET   /api/tasks/cached-all              — all local-cached tasks, one round-trip
GET   /api/tasks/project-lists           — list project→Google list mappings
POST  /api/tasks/project-lists/ensure    — auto-create Google list for project
POST  /api/tasks/project-lists/ensure-all — backfill for every project
POST  /api/tasks/migrate-legacy          — one-shot project_tasks → Google Tasks
```

---

## DATABASE CHANGES

- **Migration 42** — `project_lists` table: (project_id PK, list_id UNIQUE, list_title, created_at, updated_at) + index on list_id
- **Migration 43** — `DROP TABLE IF EXISTS project_tasks` (after migrating Cavernborn)
- `tasks.priority` column (migration 41) now actively used — round-trip through Google notes

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **Vault re-locks on backend restart.** First action: unlock.
- **Rate limit 5000/15min, JSON 429.** Don't burn through on bulk scripts.
- **Bash `node -e "..."` mangles Windows backslashes.** Use temp `.cjs` files.
- **ts-node-dev wedges sometimes.** Kill worker; supervisor usually respawns.
- **`/project-create` now materialises in Spine DB automatically.** No manual Admin click needed.
- **Shared CTRL Play subsystems** (gold coin, leaderboard Worker, daily login, push notifications, tutorial) live in `Mobile-Games/knowledge/decisions/shared-systems.md` + `architecture/leaderboard-worker.md`. Build once in Word Chain (Week 1), other 3 games inherit.
- **BatonDrop is LIVE.** Keep an eye on Crashlytics + Day-1 retention + reviews.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre\src\backend && npm run dev`
- Frontend: `cd D:\AI Work\Control-Centre\src\frontend && npm run dev`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

(Root `npm run dev:backend` doesn't exist — go into `src/backend/`.)

---

## PROJECTS — COMPLETE LIST

**Standalone:**
- **CTRL** — this system. Brand portal now populated.
- **BedBouncer** — ESP32 smart alarm. Stripe + £5 reservation + Meta ads + Kickstarter prep all in Tasks.
- **CtrlPro** — hospitality SaaS. Planning phase, first client conversation needed.
- **Personal** — catch-all.

**Mobile Games (parent container):**
- **BatonDrop** — 🎉 LIVE on Google Play 2026-05-19
- **WordDrop** — ready to build (GDD complete)
- **Cavernborn** — RN scaffolded, Phase 2 next
- **CrunchBall** — browser prototype, Phase 1 Session 1 done
- **Chess Music** — concept phase
- **Homeland** — concept phase (intentionally minimal)
- **Word Chain** — scaffolded (first of 4 new casual games)
- **Stack Attack** — scaffolded (build second)
- **Colour Flood** — scaffolded (build third — British "Colour" rule)
- **Reflex Ring** — scaffolded (build fourth — cross-promote BatonDrop)

---

*End of handover. See you when you're back.*
