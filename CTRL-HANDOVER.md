# CTRL Project Handover
*Last updated: 2026-05-13 — end of session*
*Session ended: just finished sidebar reorg + Tag-click-shows-archived fix; backend not restarted yet.*

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

Full **Email Assistant rebuild** in CTRL's Google tab against
`Control-Centre/skills/skill-email-intelligence.md`. This was an all-day
iterative session. Started by implementing the spec literally, then made
~15 course-corrections as John reacted to live behaviour. Final state is
**rules-only classifier (no LLM), self-learning into `keyword_groups`,
Cloudflare-imported owned-domain whitelist, brand-grouped inbox tabs,
clean sidebar layout matching John's mental model**.

The destructive operations from the spec (auto-delete senders, thread
pruning, bulk-delete) all became label-only flows by John's explicit
"no deletes in v1" instruction. Tasks list has follow-ups to flip them
when he's reviewed enough to trust the labels.

---

## CURRENT BUILD STATE

### Recently completed (this session — chronological)

Backend:
- Migration **v38**: ALTER emails (16 new columns) + 9 new tables
  (inbox_addresses, email_tags, keyword_bank, sender_reputation,
  auto_label_senders, unsubscribe_log, domain_mapping, training_log,
  backlog_state)
- Migration **v39**: classification_reasoning column + domain_mapping seed
- Migration **v40**: owned_domains table
- New service folder `src/backend/src/services/email/`:
  - `domain-extractor.service.ts` — TLD strip + KNOWN_COMPANIES (now
    includes BedBouncer/CTRL Pro/CTRL Play canonical labels) + dynamic
    inbox detect with brand grouping (`@bedbouncer.` matches both TLDs)
  - `unsubscribe.service.ts` — HTTP unsubscribe on NEW emails only;
    sender-flagging without delete
  - `thread-manager.service.ts` — `markLatestInThread` only (no Gmail
    deletion)
  - `sender-reputation.service.ts` — auto-classify after 95%+ on 10+
  - `rule-classifier.service.ts` — wraps existing `EmailIntelService.enrich`
    + adds receipt extraction (£/$/€), action detection, marketing
    detection, confidence normalisation 0-1, gmail_label_path with
    `Receipts/[Company]` sub-labels
  - `email-pipeline.service.ts` — 4-tier orchestrator (auto-label → thread
    → sender_rep → rules) + per-email watchdog (30s timeout)
  - `backlog-processor.service.ts` — yields every 5 emails, SSE every 25,
    self-learn every 500, default unthrottled rate
  - `keyword-self-learn.service.ts` — scans high-confidence
    classifications, finds tag-discriminating words (5+ occurrences,
    3x dominance ratio, 5+ chars, 120-word stoplist), writes to
    `keyword_groups`
  - `label-warmup.service.ts` — pre-creates canonical Gmail labels on boot
  - **DELETED**: `llm-classifier.service.ts` (was Tier 4; torn out)

- New route file `src/backend/src/routes/email-intelligence.routes.ts`
  mounted at `/api/email/*`. ~25 endpoints. Includes:
  - GET /list (FTS5 search, gmail_label filter incl. UNREAD), /stats,
    /inboxes, /tags, /companies, /unsubscribe-log
  - POST /inboxes, /inboxes/bootstrap, /messages/:id/train,
    /messages/:id/reclassify
  - POST /senders/auto-label, /senders/reputation/teach, DELETE handlers
  - POST /backlog/start (rules-only), /backlog/stop, GET /backlog/progress
  - POST /bulk/archive-by-tag, /archive-by-company,
    /archive-flagged-senders, /trash-by-tag, /trash-by-company
  - POST /keywords/self-learn (manual trigger)
  - POST /owned-domains, /owned-domains/import-cloudflare, /cleanup
  - **REMOVED**: /natural-query (was LLM-backed)

- Wired delta sync (`gmail-sync.service.ts:applyHistoryEvent`) to call
  `runPipeline(id, { isNew: true })` for new mail (no LLM).

- Gmail API now has 20s `AbortController` timeout on every fetch
  (`gmail.service.ts:147-165`). Critical infra — without this the
  backlog Stop button doesn't work mid-await.

- Server.ts boot adds: `startInboxCacheRefresh()` + best-effort
  `warmLabelCache()`. Email intel router mounted at `/api/email`.

Frontend:
- New service `src/frontend/src/services/email-intel.service.ts` —
  typed wrapper for /api/email/* (~20 methods)
- New components in `src/frontend/src/modules/gmail/components/`:
  - `IntelEmailList.tsx` — replaces row-rendering with source badges
    (rules/thread/sender_rep/auto_label/training), receipt/action chips,
    needs-review highlight
  - `IntelSidebar.tsx` — Tags + Auto-labelled-senders + Owned-domains
    panel (Companies removed). Mounts FolderTree's three sub-components
    in the user's requested order.
  - `IntelStatsRow.tsx` — Total / Action / Review / Receipts / Marketing
    pills + secondary row of source breakdown
  - `IntelBacklogPill.tsx` — single Start button + rate dropdown
    (Smart button removed with LLM tear-out)
  - `IntelOwnedDomains.tsx` — collapsible panel: Cloudflare import,
    Clean up data, Rebuild inbox tabs, manual add/remove
  - `FolderTree.tsx` — refactored into 3 named exports: SystemFolders
    (Inbox/Sent/Spam/Trash/Unread whitelist), UserLabels (nested by `/`),
    SmartFolders (virtual filters at bottom, italic+muted)
- `Gmail.tsx` — full rewrite. Dynamic inbox tabs from /api/email/inboxes,
  Tag-click switches gmail_label to ALL_MAIL, reader uses existing
  `EmailReader` (sandboxed iframe preserved) wrapped with trainer bar
  (correction UI, reasoning line)

### In progress right now

- **Backend NOT restarted** with the latest sidebar reorg + ALL_MAIL
  semantics fix + Tag-click-shows-archived behaviour. All changes on
  disk + typecheck clean.
- **Backlog status idle**, ~530 emails classified across the day, ~11,474
  unclassified to go.

### Pending / next steps

1. Restart backend → hard-refresh browser
2. Sidebar should now match: System (Inbox/Sent/Spam/Trash/Unread) →
   Tags → Labels → Smart (rough) → Auto-labelled senders → Owned domains
3. Click Marketing tag → should show all ~670 (was showing 3 pre-fix)
4. Click Backlog → Start → ~30-90 min unthrottled, no LLM, self-learns
   automatically into keyword_groups every 500 emails
5. Review Marketing/Receipts labels; Correct tags on wrong ones; new
   keywords flow back into keyword_groups via training + self-learn

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | working | not touched |
| Claude Tab | src/frontend/src/modules/claude-tab/ | working | not touched |
| Gmail/Google | src/frontend/src/modules/gmail/ | **rebuilt** | full v2 — see Email Assistant section |
| Tasks | src/frontend/src/modules/tasks/ | working | added 6 tasks to CTRL list this session (Email Assistant followups) |
| Projects | src/frontend/src/modules/projects/ | working | not touched |
| Finance | src/frontend/src/modules/finance/ | working | not touched |
| Trading | src/frontend/src/modules/trading/ | working | not touched |
| GitHub | src/frontend/src/modules/github/ | working | not touched |
| Cloudflare | src/frontend/src/modules/cloudflare/ | working | reused `listZones()` for owned-domains import |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | working | not touched |
| Settings | src/frontend/src/modules/settings/ | working | not touched |
| Admin | src/frontend/src/modules/admin/ | working | EmailRulesSection still works; will surface new self-learned keywords |

---

## FILES CREATED OR MODIFIED THIS SESSION

```
src/backend/src/db/migrate.ts                                       — migrations v38, v39, v40
src/backend/src/server.ts                                           — mount /api/email + warmLabelCache + startInboxCacheRefresh
src/backend/src/routes/email-intelligence.routes.ts                — NEW (~25 endpoints)
src/backend/src/services/gmail.service.ts                          — added 20s fetch timeout helper
src/backend/src/services/claude.service.ts                         — added { model } option to callClaudeCLI
src/backend/src/services/sync/gmail-sync.service.ts                — applyHistoryEvent calls new runPipeline
src/backend/src/services/sync/scheduler.ts                         — added publishSyncEvent export
src/backend/src/services/email/domain-extractor.service.ts         — NEW
src/backend/src/services/email/unsubscribe.service.ts              — NEW
src/backend/src/services/email/thread-manager.service.ts           — NEW
src/backend/src/services/email/sender-reputation.service.ts        — NEW
src/backend/src/services/email/rule-classifier.service.ts          — NEW
src/backend/src/services/email/email-pipeline.service.ts           — NEW (LLM tier removed end of session)
src/backend/src/services/email/backlog-processor.service.ts        — NEW (rules-only, unthrottled default)
src/backend/src/services/email/keyword-self-learn.service.ts       — NEW
src/backend/src/services/email/label-warmup.service.ts             — NEW
src/backend/src/services/email/llm-classifier.service.ts           — DELETED (was created and removed same day)

src/frontend/src/services/email-intel.service.ts                   — NEW
src/frontend/src/modules/gmail/Gmail.tsx                           — full rewrite
src/frontend/src/modules/gmail/Gmail.types.ts                      — minor (claude source enum)
src/frontend/src/modules/gmail/gmail.css                           — appended ~250 lines for v2 UI
src/frontend/src/modules/gmail/components/IntelEmailList.tsx       — NEW
src/frontend/src/modules/gmail/components/IntelSidebar.tsx         — NEW
src/frontend/src/modules/gmail/components/IntelStatsRow.tsx        — NEW
src/frontend/src/modules/gmail/components/IntelBacklogPill.tsx     — NEW
src/frontend/src/modules/gmail/components/IntelOwnedDomains.tsx    — NEW
src/frontend/src/modules/gmail/components/FolderTree.tsx           — refactored into SystemFolders/UserLabels/SmartFolders

skills/skill-email-intelligence.md                                  — added §"DEVIATIONS FROM ORIGINAL SPEC"
SESSION_STATE.md                                                    — overwritten
LEARNINGS.md                                                        — appended ~12 new entries
```

---

## RECENT GIT COMMITS

CTRL has no .git in `D:\AI Work\Control-Centre\` itself.

Linked-repo pushes ran during /afk:
- bedbouncer — pushed (no changes today, just push)
- batondrop — pushed
- ctrl, ctrlpro, mobile-games, worddrop, chessmusic — skipped (no .git or no changes)

---

## OPEN ISSUES / KNOWN BUGS

1. **Frontend cache may show stale tabs** until hard refresh. The tabs
   come from inbox_addresses; cleanup wipes them but the React state
   doesn't always reload. Mitigated this session by adding loadInboxes()
   to onChanged + SSE handler, but Ctrl+Shift+R is the safe move after
   cleanup.

2. **`unread` and `total` are always 0 on every Gmail label** —
   `EmailCacheService.listLabels` hardcodes them. Cosmetic. Per-label
   counts would require a separate SQL query each.

3. **Custom UNREAD virtual folder has no unread badge** — same root cause.

4. **`auto_label_senders` is label-only** — no auto-trash. Task in CTRL
   list to flip the mode flag when you've reviewed enough.

5. **Marketing default is archive, not trash** — task in CTRL list to flip
   when ready.

6. **Tasks API still 403 Quota Exceeded** for old `task_delete` ops in
   outbox. Wait for daily quota reset; new tasks added today went to
   local DB only (visible in CTRL Tasks tab, not on phone Google Tasks
   yet).

7. **Smart virtual folders aren't reliable** (Action needed / Awaiting
   reply / Newsletters / Stalled). Underlying intel (thread_state,
   importance_score) isn't yet rich enough. Marked italic+muted in
   sidebar; works mechanically but not promoted.

8. **`keyword_bank` table is now empty** — orphan. Was the location for
   per-email auto-learn writes (1,860 garbage entries wiped). Self-learn
   writes to `keyword_groups` (the table EmailRulesSection manages).
   Consider dropping the table in a future migration.

9. **The `bedbouncer.com` Cloudflare zone is in owned_domains** but the
   user only owns the `.co.uk`. Doesn't break anything — both produce
   the same brand match_string `@bedbouncer.` — but tasks should mention
   that Cloudflare zones aren't a perfect "I own this" signal.

---

## KEY DECISIONS MADE THIS SESSION

(All deviations from spec are catalogued in
`skills/skill-email-intelligence.md` §"DEVIATIONS FROM ORIGINAL SPEC".
Read that section before changing anything in this module.)

Highlights:
- **No LLM in pipeline.** Each `claude -p` call costs ~0.2% of session
  quota regardless of model. For 12k+ emails, mathematically impossible.
  Reserved enum values for re-introduction via batched approach.
- **No deletes in v1.** Label + archive only. Trash routes exist but
  only fire from explicit hover-button clicks.
- **Owned domains gate inbox tabs**, populated from Cloudflare zones.
- **Personal_addresses ≠ inbox triggers** (purely "people you know" so
  classifier doesn't tag their mail as marketing).
- **Companies as sub-labels** (`Receipts/Amazon`), not a sidebar section.
- **Tag click shows archived** by switching gmail_label to ALL_MAIL.
- **Self-learn writes to `keyword_groups`** (the source of truth managed
  via EmailRulesSection admin).
- **Sidebar layout** explicit per user: System → Tags → Labels →
  Smart (rough) → Auto-labelled senders → Owned domains.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

Mounted at `/api/email/*`:

```
GET    /inboxes
POST   /inboxes
DELETE /inboxes/:id
POST   /inboxes/bootstrap
GET    /list                         # FTS5 + gmail_label + tag/company filters, paginated
GET    /stats
GET    /companies
GET    /tags
GET    /senders/auto-label
POST   /senders/auto-label
DELETE /senders/auto-label/:domain
GET    /senders/reputation
POST   /senders/reputation/teach
GET    /unsubscribe-log
GET    /receipts/spend
POST   /messages/:id/train
POST   /messages/:id/reclassify
POST   /backlog/start                # body: { ratePerHour? }
POST   /backlog/stop
GET    /backlog/progress
POST   /bulk/archive-by-tag
POST   /bulk/archive-by-company
POST   /bulk/archive-flagged-senders
POST   /bulk/trash-by-tag
POST   /bulk/trash-by-company
POST   /keywords/self-learn          # manual trigger
GET    /owned-domains
POST   /owned-domains
DELETE /owned-domains/:domain
GET    /owned-domains/cloudflare
POST   /owned-domains/import-cloudflare
POST   /cleanup
```

Removed mid-session: `POST /natural-query` (LLM-backed).

---

## DATABASE CHANGES THIS SESSION

Migrations v38, v39, v40 (additive only). New tables:
- inbox_addresses, email_tags (seeded with 7 canonical), keyword_bank
  (now empty/unused), sender_reputation, auto_label_senders,
  unsubscribe_log, domain_mapping (seeded from KNOWN_COMPANIES),
  training_log, backlog_state (singleton), owned_domains

ALTERed `emails` table — 17 new columns: inbox_address,
classification_source, action_taken, gmail_label_path, confidence (REAL
0-1), needs_review, is_marketing, is_receipt, has_action,
is_latest_in_thread, has_unsubscribe, unsubscribed_at, receipt_amount,
receipt_currency, receipt_order_ref, classification_reasoning.

FTS5 emails_fts index untouched and used for /list search.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **DO NOT ADD LLM CALLS TO THE EMAIL PIPELINE** unless implementing the
  batched approach (≥50 emails per CLI call). Per-email LLM is what
  caused today's session-quota burn (53 calls = ~10% session).
- **`personal_addresses` is for marketing-suppression, NOT inbox tabs.**
  Earlier mistake. Anywhere that touches that table for inbox routing
  needs to be revisited.
- **The Workspace forwarding case** (info@bedbouncer.co.uk → gmail) means
  `delivered_to` is the gmail address but `to_json` preserves the
  bedbouncer one. `pickBestRecipient` in email-pipeline scans all
  recipients in to_json for an owned-domain match BEFORE falling back
  to delivered_to. Don't break this.
- **Self-learning is local-only** (SQL + JS, zero LLM). Triggered every
  500 emails in backlog + once on completion + manual endpoint. Adds
  to `keyword_groups`, not `keyword_bank`.
- **Outbox `local_xxx` label IDs are bugs.** The legacy
  `EmailCacheService.getOrCreateLabel` creates them; Gmail rejects them
  with 400 Invalid Label. Use `GmailService.getOrCreateLabel` (calls
  Gmail API, returns real label ID). New pipeline does this correctly.
  If you see `local_` prefix in outbox failures, the legacy code path
  is being hit somewhere.
- **All reads hit local SQLite via `EmailCacheService`** (the routes
  alias it as `GmailService` — confusingly!). Never query Gmail live
  for display. Only sync uses the live Gmail API.
- **6 new tasks were added to the CTRL Tasks list** for the deferred
  spec items (real auto-delete, thread pruning, bulk-delete UI, Pub/Sub
  push, Discord notify, Marketing-default-trash). Local-only because
  Tasks API quota was 403 — they'll sync when quota resets.

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend: `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal server: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

After restart for this session's work specifically:
1. Hard-refresh browser (Ctrl+Shift+R) — frontend has heavy changes
2. Open Email tab → owned_domains panel at sidebar bottom
3. Click Backlog → Start (no Smart button anymore)
4. Watch the pill tick every 5 emails
5. Self-learn fires automatically every 500; final pass on completion

---

## PROJECTS OUTSIDE CTRL (for full context)

- **BatonDrop** — mobile game; AAB v20 still ready for Play Store upload from
  yesterday's session. Auth rebuild deployed. No changes today.
- **CTRLPro / UnifyBI** — hospitality SaaS, planning phase, first client
  conversation pending.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep. No changes today.
- **Mobile Games (other)** — WordDrop, Cavernborn — planning/early dev.
