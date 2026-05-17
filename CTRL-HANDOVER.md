# CTRL Project Handover
*Last updated: 2026-05-17 (end of session) UTC*
*Session ended: After hot-patching the vault-lock loop and slow-load issues on the new Home page*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.
When John returns, he will paste in any files or context from the web session.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173 (NOT in git)
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

---

## WHAT WE WERE BUILDING THIS SESSION

A LONG session covering six distinct phases:

1. **WordDrop audit** against the rebuilt mobile-design skill — confirmed 21/24 score, flagged circular-validation caveat.
2. **Full CTRL design audit** — Nielsen total dropped 29 → 23/40 because the token layer pivoted to Neon Dark but DESIGN.md and modules hadn't followed.
3. **Neon canonical pivot** — DESIGN.md rewritten for Neon Dark + Linear preset, runtime theme system built (CSS custom properties + `[data-theme]` blocks + ThemeService + Admin → Theme UI with preset/accent/radius/density override).
4. **Phase D1 + D2 cleanup pass** (agent-driven) — module CSS hex literals 664 → 16 (data-only); `!important` in finance.css 46 → 0; .tsx inline hex 246 → 100; chart palettes now use `--chart-1..8` tokens.
5. **Toast undo pattern** — built ToastProvider + useToast hook at `shared/components/Toast.tsx` using a "delayed-commit" pattern (API fires 5s after action; undo cancels both). Wired into Tasks delete, Gmail archive/trash, Projects task delete. IntelSidebar bulk-op alerts converted to toast feedback.
6. **Home page** — built the daily focus surface: Focus Today (overdue + due today combined from Google Tasks + project tasks), Deadlines (next 7 days), Projects (sorted by sort_order priority). FocusRow component handles complete/edit/delete/deep-link inline. Project sort_order plumbed through backend + drag-reorder UI in Projects module. Wired into AppShell as the default tab.

Also: **BatonDrop production application submitted to Google Play** — v2.0.0 (versionCode 21). Logged at `D:\AI Work\Mobile-Games\games\batondrop\knowledge\production-application-2026-05-17.md`.

---

## CURRENT BUILD STATE

### Recently completed (this session)

**Documentation**
- `DESIGN.md` — full rewrite (Neon Dark canonical + Linear preset)
- `skills/skill-ctrl-design.md` — palette quick-ref + anti-patterns under neon
- `knowledge/design/ctrl-ui-critique-2026-05-17.md` — new audit baseline (NEW)
- `knowledge/design/ctrl-ui-critique-2026-04-28.md` — deprecation header added
- `CLAUDE.md` — design system description updated
- `PROJECT-SUMMARY.md` — design reference updated
- `skills/skill-toast-undo.md` — NEW skill doc for the toast pattern
- `SESSION_STATE.md` — current state captured
- `LEARNINGS.md` — 12 new entries appended

**Theme system**
- `src/frontend/src/core/tokens.css` — refactored for `[data-theme]` switching + `--chart-1..8` series
- `src/frontend/src/core/base.css` — Inter font loaded, `.btn--hero` variant, `.task-focused` highlight class
- `src/frontend/src/core/shell.css` — sidebar transition perf (contain + will-change)
- `src/frontend/src/core/ThemeService.ts` — NEW (applies theme on boot + on event)
- `src/frontend/src/main.tsx` — initTheme() boot + vault-locked event DEBOUNCE
- `src/frontend/src/services/config.service.ts` — ThemePreferences types
- `src/frontend/src/modules/admin/Admin.tsx` — Theme section wired in as first tab
- `src/frontend/src/modules/admin/components/ThemeSection.tsx` — NEW

**Toast undo pattern**
- `src/frontend/src/shared/components/Toast.tsx` — NEW
- `src/frontend/src/shared/components/Toast.css` — NEW
- `src/frontend/src/App.tsx` — wrapped in ToastProvider

**Module CSS cleanup (Phase D1, agent-driven)**
- 24 module CSS files edited — hex literals 664 → 16 (legitimate data only)
- `finance.css` — palette rationalised, all 46 `!important` removed
- Various bar/gauge transitions converted from `width` to `transform: scaleX`

**`.tsx` inline hex strip (Phase D2, agent-driven)**
- 40 `.tsx` files edited — inline hex 246 → 100 (all legitimate user-choice palettes or external data)
- Chart palettes across analytics/finance/dashboard now use `--chart-1..8`
- JS bar widths migrated to `transform: scaleX` to pair with the CSS work

**Home page (NEW module)**
- `src/frontend/src/modules/home/Home.tsx` — NEW
- `src/frontend/src/modules/home/components/FocusRow.tsx` — NEW
- `src/frontend/src/modules/home/components/FocusRow.css` — placeholder (styles in home.css)
- `src/frontend/src/modules/home/home.types.ts` — NEW (FocusItem, FocusTarget)
- `src/frontend/src/modules/home/useFocusTaskNav.ts` — NEW (deep-link event + listener hook)
- `src/frontend/src/modules/home/home.css` — NEW
- `src/frontend/src/modules/home/index.ts` — NEW
- `src/frontend/src/core/AppShell.tsx` — Home tab wired in
- `src/frontend/src/modules/tasks/Tasks.tsx` — useToast, useFocusTaskListener, handleDelete with toast undo
- `src/frontend/src/modules/tasks/components/TaskBoard.tsx` — `data-task-id` on rows
- `src/frontend/src/modules/gmail/Gmail.tsx` — useToast, handleArchive/Trash with delayed-commit (NOTE: has setState updater bug)
- `src/frontend/src/modules/gmail/components/IntelSidebar.tsx` — useToast, alerts → toasts
- `src/frontend/src/modules/projects/Projects.tsx` — drag-reorder UI, useFocusTaskListener
- `src/frontend/src/modules/projects/projects.css` — drag handle CSS, side-stripe fix
- `src/frontend/src/modules/projects/components/ProjectGroup.tsx` — useToast, handleDelete with delayed-commit + pendingDeletes
- `src/frontend/src/modules/projects/components/TaskRow.tsx` — `data-task-id` on row
- `src/frontend/src/services/projects.service.ts` — Project.sortOrder + reorderProjects API
- `src/backend/src/services/projects.service.ts` — Project type + listProjects sort_order
- `src/backend/src/routes/projects.routes.ts` — `/list/reorder` endpoint, `/list/:id` PATCH accepts sortOrder
- `D:\AI Work\.ctrl-config.json` — `preferences.appearance.theme` block added

### In progress right now

Nothing half-finished mid-edit. Last actions were two hot patches to issues the user reported after Home went live (vault-loop and slow-load). Both shipped, type-check clean (frontend + backend exit=0).

### Pending / next steps (priority order)

1. **Investigate the underlying 401 on Home load** — likely finance. Debounce hides the symptom but root cause not investigated.
2. **Pin to today feature** (Session 2 of Home) — DB pins table + pin button across task surfaces + nightly midnight clear (~2h).
3. **Mobile responsive pass for Home** — grid collapses but FocusRow row controls could be tighter on mobile.
4. **CRM stage-badge + ContractsTab status-pill sanity check** — D2 agent flagged hex+alpha tints lost coloured backgrounds.
5. `.pm-project-card-progress-fill` CSS class — invisible progress bar in ProjectsHome.tsx (pre-existed, just flagged).
6. **xterm.js theme bridge** — 60 hex literals in ClaudeTab/BotTab/PageTerminal because xterm.js requires hex strings; need JS bridge reading computed CSS vars and rebuilding xterm theme on switch.
7. **Tooltips on icon-only buttons** — audit-wide pass.
8. **Fix the setState updater side-effect bug in `Gmail.tsx:201-256`** — handleArchive/handleTrash do side effects inside setEmails updater; fires twice in Strict Mode.

---

## ALL MODULES — STATUS

| Module | Location | Status |
|--------|----------|--------|
| Home | `src/frontend/src/modules/home/` | NEW THIS SESSION — live, default tab |
| Claude Tab | `src/frontend/src/modules/claude-tab/` | working, xterm hex literals deferred |
| Gmail/Google | `src/frontend/src/modules/gmail/` | working + toast undo wired |
| Tasks | `src/frontend/src/modules/tasks/` | working + toast undo wired + deep-link listener |
| Projects | `src/frontend/src/modules/projects/` | working + drag-reorder + toast undo + deep-link listener |
| Finance | `src/frontend/src/modules/finance/` | working — palette rationalised, !important war won |
| Trading | `src/frontend/src/modules/trading/` | working — bot-bounce keyframe kept (status indicator, not anti-pattern) |
| GitHub | `src/frontend/src/modules/github/` | working — language hex colours kept (external data) |
| Cloudflare | `src/frontend/src/modules/cloudflare/` | working |
| Brand Toolkit | `src/frontend/src/modules/brand-toolkit/` | working |
| Settings | `src/frontend/src/modules/settings/` | working — Appearance section has legacy theme controls (dead code, ignored not deleted) |
| Admin | `src/frontend/src/modules/admin/` | working — NEW Theme section as default tab |
| Discord | (not built yet) | scoped in skill-discord.md v1 (4 channels) but not implemented |

---

## RECENT GIT COMMITS

Control-Centre is **NOT a git repo** (no `.git` folder). The CTRL codebase is local-only.
The auto-backup script in /afk attempted to push linked project repos via the `project_repos` DB table but the node script failed on bash escaping — skipped per skill (best-effort).

---

## OPEN ISSUES / KNOWN BUGS

1. **Finance 401 on Home load** — symptom hidden by 2s debounce; root cause not investigated. Could indicate a race between vault unlock and downstream auth, or a specific endpoint needing a session.
2. **Deep-link to collapsed project task** — if user has a project group collapsed in the Projects module, the focus listener may not find the row. There is a 10-try retry but no auto-expand of the collapsed group.
3. **setState updater side-effect bug in Gmail.tsx** — handleArchive (line 201) and handleTrash (line 229) do toast.show, setTimeout, setActiveEmail inside the `setEmails(prev => ...)` updater. In React Strict Mode this fires the toast and timer TWICE per click. Functional but worth cleaning up.
4. **Bulk operations in IntelSidebar have no undo** — by design (bulk reverse needs backend support not yet added). They use confirm dialog + success/error toast.
5. **Legacy theme controls in Settings → Appearance** — dead code (no longer applies). Ignored, not deleted. Should be removed in a future cleanup.
6. **STATUS.md is from 26 April** — quite stale, not updated this session. SESSION_STATE.md is the current snapshot.

---

## KEY DECISIONS MADE THIS SESSION

- **Neon Dark canonical**, Linear preserved as switchable preset.
- **Theme controls in Admin → Theme** (not Settings).
- **Theme switcher uses CSS custom properties at runtime** — no rebuild needed.
- **Toast undo = delayed-commit pattern** (not reversal) — avoids needing backend reverse endpoints.
- **Project sort_order = priority** — exposed via Project.sortOrder, PATCHable, bulk reorder endpoint.
- **Deep-link via global window event** + `data-task-id` attribute on rows.
- **Home is the default tab** (was a placeholder before).
- **Chart palettes use `--chart-1..8` tokens** in canonical order (pink/cyan/lime/orange/purple/amber/yellow/red).
- **`ctrl:vault-locked` event debounced** to once per 2s.
- **For neon, glow flourish kept** but moved to `.btn--hero` variant (reserved for at most one CTA per screen).

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

```
PATCH /api/projects/list/reorder
  body: { ids: string[] }   // new order, sort_order set to i*1000
  Used by Projects module drag-reorder UI.

PATCH /api/projects/list/:id
  body now also accepts: { sortOrder: number }
  Per-project sort_order updates (less common; reorder endpoint is preferred for bulk).
```

---

## DATABASE CHANGES THIS SESSION

None — `sort_order` column already existed on projects table from prior migrations. The Project type and toProject converter were updated to surface the existing column.

`.ctrl-config.json` schema extension (config blob, not DB):

```json
preferences.appearance.theme = {
  "preset": "neon-dark",
  "accent": null,
  "radius": null,
  "density": null
}
```

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **The whole CSS palette switched to neon-pink** as primary. The legacy `--blue` token now ALIASES to neon-pink. Any code that uses `var(--blue)` is automatically getting pink. This is intentional, not a bug.
- **The 100 hex literals remaining in `.tsx` are ALL legitimate data** — annotated with comments by the D2 agent. Future audits won't flag them. Don't strip them.
- **Home does N+1 requests for Google Tasks** (one per list). Acceptable for v1 but worth a backend "all tasks" endpoint someday.
- **The vault-lock debounce is a defensive fix** — it stops the loop but doesn't solve why finance 401s in the first place. If you investigate the underlying auth issue, it's safe to remove the debounce eventually.
- **STATUS.md is very stale** (from 26 April). SESSION_STATE.md is the current snapshot.
- **BatonDrop is awaiting Google Play review** — submitted 2026-05-17. If it bounces, the playbook is in `batondrop/knowledge/production-application-2026-05-17.md`.
- **The toast pattern has a side-effect bug in Gmail.tsx** that should be cleaned up — it's functional but fires toasts twice in Strict Mode.

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

- **BatonDrop** — drop-reflex casual game, v2.0.0 submitted to Google Play production 2026-05-17, awaiting review. RN 0.85.2.
- **WordDrop** — word puzzle, planning stage / brand files updated, ready to build.
- **Cavernborn** — dark fantasy idle RPG, planning, CTRL project created, RN app not yet initialised.
- **BedBouncer** — ESP32 smart alarm, website live, social media is next.
- **CTRLPro** — hospitality SaaS dashboard, planning, first client conversation pending.
- **Lane7 hard line** — John works at Lane7 (competitive socialising). Never use Lane7 contacts/data/IP for any of his other projects. Restated in CLAUDE.md every session.
