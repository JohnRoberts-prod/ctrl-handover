# CTRL Project Handover
*Last updated: 2026-05-13 — late session, second /afk of the day*
*Session ended: just fixed the orphan-Claude-CLI-subprocess leak in terminal-server; backend not restarted yet.*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.
When John returns, he will paste in any files or context from the web session.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002 (or backend itself per terminal.service.ts)
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

> There was an earlier /afk today. This is a follow-up session covering
> only the work done after that — the previous handover covered the
> initial Email Assistant rebuild. See git log below for full context.

---

## WHAT WE WERE BUILDING THIS SESSION (continuation)

After the morning rebuild of the Email Assistant, two things landed in
this second half of the day:

1. **Updated all 4 chat-context files** in `skills/`. The AI chat that
   launches when you open the Google tab in CTRL was reading files that
   described the OLD MCP-based architecture — it would have given wrong
   advice (recommend MCP tools that no longer exist, miss the new
   rules-only pipeline, miss owned_domains, miss self-learn into
   keyword_groups, etc.).
2. **Fixed a real RAM-leak bug** in `terminal.service.ts`. Every time the
   AI chat was opened/closed, a `claude.exe` subprocess leaked. ~30 leaks
   per day → ~2 GB consumed → Windows pages → backend appears
   unresponsive on `/api/health`. Diagnosed live, then fixed with
   process-tree-kill + WebSocket heartbeat.

---

## CURRENT BUILD STATE

### Recently completed (this session — chronological)

**Updated chat context files** (they're loaded by `terminal.service.ts:PAGE_CONFIG`
when the AI chat opens in each tab):

```
skills/email-assistant.md       — main directive; v2 4-tier pipeline,
                                  7 canonical tags, self-learn, MCP tool list
skills/gmail-inbox-system.md    — owned_domains + Cloudflare import,
                                  brand-grouped tabs, Workspace forwarding,
                                  canonical label tree
skills/email-knowledge.md       — 7 canonical email_tags + user-defined
                                  keyword_groups (Finance/Security/Travel),
                                  ALL user-specific sender rules preserved
                                  (PokerStars/Bridgfords/James/Margaret/
                                  Anthropic-via-Stripe)
skills/marketing-email-filter.md — what auto-flags as marketing, the relay
                                  domain list (Beehiiv/Mailjet/etc), the
                                  stoplist words to veto
```

**Terminal-server orphan-process fix** (`src/backend/src/services/terminal.service.ts`):
- Added `import { exec } from 'child_process'`
- New `killProcessTree(pid)` helper — uses `taskkill /pid X /T /F` on
  Windows, `process.kill(-pid, 'SIGKILL')` on POSIX
- WebSocket `ws.on('close')` now calls `killProcessTree` instead of just
  `ptyProcess.kill()`
- Added 30-second WebSocket heartbeat (ping/pong) — terminates session
  if no pong response (catches browser tabs closed without proper FIN)

**Skill update**: `skills/CTRL-terminal-sessions-pattern.md` got a new
"⚠️ ORPHAN-SUBPROCESS LEAK" section at the top describing the bug + fix
+ verification steps.

### In progress right now

- **Backend NOT restarted** with the terminal-server tree-kill fix. New
  Claude CLI subprocesses will continue to leak until restart.
- **30+ orphan node processes still running** on John's machine at /afk
  time, eating ~2GB RAM. He's been given the PowerShell snippet to kill
  them but hasn't run it yet (or it's been since /afk).

### Pending / next steps

1. Run the cleanup snippet to free RAM:
   ```powershell
   $keep = (Get-NetTCPConnection -LocalPort 3001,3002,5173 -ErrorAction SilentlyContinue).OwningProcess | Sort-Object -Unique
   Get-Process node | Where-Object { $keep -notcontains $_.Id -and $_.WorkingSet64 -gt 30MB } | Stop-Process -Force
   ```
2. Restart the backend (`cd D:\AI Work\Control-Centre && npm run dev:backend`).
3. Hard-refresh browser (Ctrl+Shift+R) — clears the "Loading..." stuck state.
4. Verify Email tab loads (~1,544 inbox emails — DB is ready, backlog
   completed earlier processing 11,474 emails).
5. Open AI chat in Google page → it'll load with v2 context now.
6. **Verify the fix works**: open + close the AI chat tab 5 times, then
   `Get-Process node | Measure-Object -Sum WorkingSet64`. Total memory
   should NOT climb by ~100MB per session anymore.

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | working | not touched |
| Claude Tab | src/frontend/src/modules/claude-tab/ | working | not touched |
| Gmail/Google | src/frontend/src/modules/gmail/ | working (waiting restart) | v2 backlog completed; sidebar reorg + tag-shows-archived live in code, needs backend restart |
| Tasks | src/frontend/src/modules/tasks/ | working | 6 follow-up tasks added across the day |
| Projects | src/frontend/src/modules/projects/ | working | not touched |
| Finance | src/frontend/src/modules/finance/ | working | not touched |
| Trading | src/frontend/src/modules/trading/ | working | not touched |
| GitHub | src/frontend/src/modules/github/ | working | not touched |
| Cloudflare | src/frontend/src/modules/cloudflare/ | working | listZones() reused for owned-domains import |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | working | not touched |
| Settings | src/frontend/src/modules/settings/ | working | not touched |
| Admin | src/frontend/src/modules/admin/ | working | EmailRulesSection still works; v2 self-learn writes to keyword_groups it manages |
| **Terminal sessions** | src/backend/src/services/terminal.service.ts | **FIXED** | Tree-kill + WS heartbeat — fixes the day-long memory leak |

---

## FILES CREATED OR MODIFIED THIS SESSION (since last /afk)

```
src/backend/src/services/terminal.service.ts   — added killProcessTree + WS heartbeat (~50 lines)

skills/email-assistant.md                       — full rewrite (276 → ~250 lines), v2 pipeline + 7 canonical tags + MCP tool list
skills/gmail-inbox-system.md                    — full rewrite (221 → ~220 lines), owned_domains + Workspace forwarding + canonical label tree
skills/email-knowledge.md                       — full rewrite (349 → ~280 lines), preserves all user sender knowledge, updates tag library to v2
skills/marketing-email-filter.md                — full rewrite (97 → ~155 lines), v2-aware
skills/CTRL-terminal-sessions-pattern.md        — added orphan-leak warning section at top

SESSION_STATE.md                                — overwritten
LEARNINGS.md                                    — appended 5 new entries
```

---

## RECENT GIT COMMITS

CTRL itself has no .git in `D:\AI Work\Control-Centre\`.

Linked-repo pushes ran during /afk (just now):
- bedbouncer — pushed
- batondrop — pushed
- ctrl, ctrlpro, mobile-games, worddrop, chessmusic — skipped (no .git or no changes)

---

## OPEN ISSUES / KNOWN BUGS

1. **Frontend "stuck loading" can recur** if backend ever truly hangs again.
   The component's `loading=true` state never flips when fetch hangs without
   a timeout. Follow-up worth doing: add `AbortController` + 15s timeout
   to every email-intel.service fetch in the frontend so a hung backend
   doesn't leave the UI permanently spinning.

2. **53+ stale `classification_source = 'claude'` rows** from earlier
   today's LLM testing. Code paths that produce them are gone. Old rows
   are fine — just legacy. If we ever drop the `'claude'` enum value
   entirely, those need a migration.

3. **One stale `Action Required/Interviews` Gmail label** spotted (the
   LLM was inventing sub-labels off-spec for a few emails before being
   torn out). Doesn't break anything but if you spot more weird
   sub-labels, that's why.

4. **`unread/total` always 0 on every Gmail label** — `EmailCacheService.listLabels`
   hardcodes both. Cosmetic. Per-label counts would need a separate
   query. Listed in the previous /afk too — still open.

5. **Backlog completed status** — 11,474 emails processed. Self-learn
   would have run on completion. Check `keyword_groups.keywords` to see
   what was added (compare entries' length to before-backlog).

6. **Auto-flagged senders are still label-only** — task in CTRL list to
   flip when ready. Same for Marketing default → trash flip.

---

## KEY DECISIONS MADE THIS SESSION

- **Kill process trees on Windows, not just the parent.** `pty.kill()`
  alone leaks Claude CLI subprocesses on Windows. Standard pattern
  going forward: `taskkill /T /F` (Windows) or `process.kill(-pid)`
  (POSIX). Documented in CTRL-terminal-sessions-pattern.md.
- **WebSocket heartbeat is mandatory** for any pty-spawning session —
  catches browser-tab-closed-without-FIN silently-disconnected sockets.
  30s ping interval, terminate if no pong.
- **Chat context files MUST stay in sync** with the actual architecture.
  When the AI chat in a tab launches Claude Code with `--add-dir
  skills/`, it reads PAGE_CONFIG-mapped files. Stale context = wrong
  advice (e.g. "use email_list_tags MCP" when the tool exists but the
  whole pipeline around it has changed).
- **All user-specific sender knowledge preserved** during context-file
  rewrite. PokerStars/Bridgfords/James/Margaret/Anthropic-via-Stripe/
  Cloudflare-vs-Receipts/Deliveroo-subdomain — all kept. The architecture
  sections changed; the knowledge sections stayed.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None this session (the email-intel routes were all built earlier today).

---

## DATABASE CHANGES THIS SESSION

None this session.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **The leak is fixed in code but not yet running.** Must restart backend
  to pick up `terminal.service.ts` changes. Until then, every chat-tab
  open/close still leaks one ~100MB process.
- **The chat context files describe v2.** If anyone changes the email
  pipeline architecture, those 4 skill files (email-assistant.md,
  gmail-inbox-system.md, email-knowledge.md, marketing-email-filter.md)
  MUST be updated too. Otherwise the chat goes stale.
- **The earlier /afk URL is still valid** for full-day context. This
  doc only covers the work between the two /afks. The morning rebuild
  is in `https://raw.githubusercontent.com/JohnRoberts-prod/ctrl-handover/<earlier-sha>/CTRL-HANDOVER.md`
  (find via gh api).
- **Verifying the fix worked**: after backend restart, repeated
  open/close of the AI chat in the Google tab should NOT increase
  total node.exe memory by ~100MB per cycle. If it does, the fix
  isn't working — investigate the kill chain.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`
  (Note: terminal sessions actually run inside the backend — terminal.service.ts
  uses node-pty directly. The terminal-server folder may be vestigial.)

After this restart specifically:
1. Run the orphan-cleanup PowerShell snippet (in OPEN ISSUES section above)
2. Restart backend
3. Hard-refresh browser (Ctrl+Shift+R)
4. Verify Email tab loads emails immediately

---

## PROJECTS OUTSIDE CTRL (for full context)

- **BatonDrop** — mobile game; AAB v20 still ready for Play Store upload
  from yesterday. Auth rebuild deployed. No changes today.
- **CTRLPro / UnifyBI** — hospitality SaaS, planning phase, first client
  conversation pending.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep. No changes today.
- **Mobile Games (other)** — WordDrop, Cavernborn — planning/early dev.
