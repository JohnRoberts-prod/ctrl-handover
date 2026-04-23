# CTRL Project Handover
*Last updated: 23 April 2026*
*Session ended: discussing swapping @imgly/background-removal for Remove.bg API*

---

## HOW TO USE THIS DOCUMENT

You are Claude (web or Code) picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript — port 3001
Frontend: React 18 + Vite + TypeScript — port 5173
Terminal server: node-pty WebSocket — port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

---

## HOW TO START THE SYSTEM

```
D:\AI Work\START-ALL.bat
```

Or manually:
- Backend:  `cd D:\AI Work\Control-Centre && npm run dev:backend`
- Frontend: `cd D:\AI Work\Control-Centre && npm run dev:frontend`
- Terminal: `cd D:\AI Work\Control-Centre\src\terminal-server && npm run dev`

---

## COMPLETE FEATURE LIST — ALL BUILT FUNCTIONALITY

### INFRASTRUCTURE
- AES-256-GCM encrypted vault — create, unlock, lock, CRUD keys via UI
- VaultGate — blocks app until vault unlocked
- Google OAuth — real Gmail auth, token stored in vault
- App shell — sidebar (collapsible, grouped), topbar, Claude slide-in panel
- Auth protection on all routes
- SQLite database with 16+ migrations
- Config blob at `D:\AI Work\.ctrl-config.json` — projects, preferences
- Sandboxed file API (read/write/list/delete, locked to D:\AI Work\)
- Git routes — status, commit, push staging, push live

### GMAIL MODULE (`/gmail`)
- Full inbox with real Gmail data (synced to SQLite, never direct API from display)
- Folder tree — labels, virtual folders (Unread, Starred, Sent, etc.)
- Project filtering bar — filter inbox by configured projects
- Email list with importance scoring, tags, read/unread state
- Thread reader — full conversation view
- Inbox Health Bar — stats (unread, starred, tag coverage)
- Review Mode — triage panel for inbox cleanup
- Bulk archive / bulk trash
- Tag system — per-message tags with bulk operations
- Unsubscribe — list-unsubscribe parsing, one-click domain unsubscribe
- Email intelligence backend — rules engine (7 priorities), keyword groups, contact tagging, importance scoring, Pub/Sub webhook, 60-minute background sync

### TASKS MODULE (`/tasks`)
- Google Tasks integration
- Task lists sidebar
- Task board per list — create, complete, delete tasks

### TRADING MODULE (`/trading`)
- **Overview tab** — portfolio header (equity, P&L, cash), positions table, open orders, manual override panel, market status indicator
- **Trade Log tab** — filled order history from Alpaca
- **Research tab** — saved research notes with tags
- **Weekly Review tab** — stub (GitHub-backed, in design)
- **Strategies tab** — strategy cards with paper/live toggle, gate enforcement (paper-only guard)
- **Routines tab** — built-in briefing triggers (Morning Briefing, Midday Check run-now buttons), user-created routines with Perplexity questions editor (add/remove/edit queries per routine)
- **Bot tab** — stub
- Paper/live mode toggle with confirmation gate
- Alpaca API wrapper — paper + live endpoints
- Trading scheduler — cron jobs (morning briefing 13:15 UTC, midday 17:30 UTC, order sync every 30 min)
- Trading briefing service — market data, bar charts, Perplexity integration

### FINANCE MODULE (`/finance`)
- Double-entry bookkeeping (immutable postings, all amounts in pence)
- Multiple accounts — create, edit, delete, balance anchors
- Transaction import — CSV upload with configurable column mapping per account
- Transaction list — filter by account, date range, category; paginated
- Auto-categorisation rules — pattern matching, bulk apply
- Reimbursements tracker — claim, resolve, link to transactions
- Contracts/Agreements — recurring commitments with start/end dates
- Overview tab — balance summary, recent transactions, spending breakdown
- CSV format library — save and reuse import configurations

### CLOUDFLARE MODULE (`/cloudflare`)
- Domains — list all zones, zone detail
- DNS Records — view, create, edit, delete records per zone
- Tunnels — list, create, delete Cloudflare tunnels
- Analytics — traffic, requests, bandwidth per zone
- Cache — purge cache per zone

### BRAND TOOLKIT MODULE (`/brand`)
- Multi-project support — create projects (BedBouncer, Mobile-Games, CtrlPro, etc.)
- Per-project: Brand Guidelines, Tone of Voice, AI Reference doc, CSS tokens, Style Guide
- Asset management — upload/view/delete assets in sections (logos, hero-images, social-media, videos, print, other)
- Games support — isStudio flag, per-game asset library within a project
- Save-from-Design — Design module can save generated images directly into Brand Toolkit

### DESIGN MODULE (`/design`) — Creative Studio
- **Sprites tab** — text-to-image via Gemini (Nano Banana 2); optional reference image upload; model selector (Nano Banana 2 / Pro); size selector (256/512/1024px)
- **Images tab — Generate mode** — Ideogram v3; aspect ratio, style type, rendering speed, seed, negative prompt; style reference picker (up to 3 Brand Toolkit images with strength slider)
- **Images tab — Edit mode** — Ideogram v3 Edit (inpainting); source image upload; in-browser mask painting tool (black=keep, white=edit, variable brush size, fill all); auto white mask if no mask drawn
- **Images tab — Recompose mode** — Gemini img2img (Nano Banana 2); source image upload; model selector; describe new scene in prompt
- **Video tab** — Kling AI text-to-video; duration (5s/10s); aspect ratio; async polling with status updates
- Gallery — generated results stored locally; download button; Save to Brand Toolkit button; scissors button (background removal — currently @imgly/background-removal, pending swap to Remove.bg)
- API status indicator — shows which services are configured

### NOTES MODULE (`/notes`)
- ClickUp Docs integration — syncs docs and pages from ClickUp workspace
- Doc list with search
- Page viewer with markdown rendering
- Manual sync trigger, last-synced timestamp
- Create new docs and pages

### CRM MODULE (`/crm`)
- Contact management — create, edit, delete contacts
- Contact fields — name, email, phone, company, LinkedIn, website, location, notes
- Activity log per contact — note, call, email, meeting, task
- Deals/pipeline — create deals, stage progression (lead → prospect → customer → partner)
- Gmail sync — pull emails associated with a contact into their profile
- Contact list with search and stage filtering

### KNOWLEDGE BASE MODULE (`/knowledge`)
- Per-project knowledge entries
- Markdown content with simple renderer
- Tag system — create, filter by tag
- Create, edit, delete entries

### GITHUB MODULE (`/github`)
- Profile overview — avatar, bio, stats (repos, followers, following)
- Activity feed — recent GitHub events
- Repository list — stars, forks, language, visibility, last updated
- Issues — open issues across repos, filter by repo
- Pull Requests — open PRs, status, assignees
- Per-repo drill-down — commits, issues, PRs

### PROJECTS MODULE (`/projects`)
- Project list — create, rename projects
- Per-project task list — create, complete, delete, reorder tasks

### ADMIN MODULE (`/admin`)
- Keyword groups — create/edit/delete groups for email intelligence
- Keyword management — add/remove keywords per group
- Sender profiles — domain-level email sender configuration
- Audit log — view and create audit entries
- Email intelligence health check

### SETTINGS MODULE (`/settings`)
- Vault key management — view all keys, add, delete
- Trading section — Alpaca paper/live credential fields
- Partial — full settings not yet complete

---

## OUTSTANDING TASKS — NEXT BUILD PRIORITIES

### IMMEDIATE (agreed, not yet done)
- [ ] **Replace @imgly/background-removal with Remove.bg API** — scissors button in Design gallery cards calls handleRemoveBg() in GalleryCard component. Replace with POST /api/design/remove-bg backend route calling https://api.remove.bg/v1.0/removebg (header X-Api-Key, vault key: remove_bg_api_key, 50 free/month then ~$0.02/image). Uninstall @imgly/background-removal from frontend.
- [ ] **Add gemini_api_key to vault** — user needs key from aistudio.google.com to unlock Sprites + Recompose

### DESIGN MODULE
- [ ] Veo 3.1 for video — replace Kling AI with Google Veo 3.1 (same gemini_api_key, discussed not built)

### GMAIL / EMAIL
- [ ] Email intelligence UI (Tasks 2h-2l) — frontend for rules, tags, keyword groups display in Gmail module
- [ ] Gmail watch auto-renewal — currently expires every 7 days, needs cron job

### TRADING
- [ ] Weekly Review tab — design and build (currently stub)
- [ ] Bot tab — design and build (currently stub)
- [ ] Trading briefing — verify Perplexity queries wired to scheduler

### INFRASTRUCTURE
- [ ] Push CTRL codebase to GitHub (critical path)
- [ ] Create ctrl-trading-agent GitHub repo (separate, private)
- [ ] Setup wizard — first-run configuration for new users
- [ ] Notification system — system-wide alerts design
- [ ] Perplexity backend proxy — /api/search endpoint

### SETTINGS
- [ ] Full Settings tab — add ClickUp, Perplexity, Gemini, Ideogram, Kling, Remove.bg credential sections
- [ ] Add remove_bg_api_key field to Settings vault section

### MODULES NOT YET BUILT
- [ ] Home tab redesign (skill-home.md needs writing first)
- [ ] ClickUp frontend (backend routes exist, no UI)
- [ ] Notifications UI

---

## API KEYS REQUIRED — VAULT KEYS

| Vault Key | Service | Used by |
|-----------|---------|---------|
| google_oauth_tokens | Google | Gmail, Tasks |
| anthropic_api_key | Anthropic | Claude panel |
| gemini_api_key | Google Gemini | Design: Sprites, Recompose (Nano Banana 2) |
| ideogram_api_key | Ideogram | Design: Generate, Edit images |
| kling_api_key | Kling AI | Design: Video generation |
| kling_api_secret | Kling AI | Design: Video generation |
| remove_bg_api_key | Remove.bg | Design: Background removal (PENDING) |
| replicate_api_token | Replicate | REMOVED — replaced by Gemini |
| alpaca_paper_key | Alpaca | Trading (paper) |
| alpaca_paper_secret | Alpaca | Trading (paper) |
| alpaca_live_key | Alpaca | Trading (live) |
| alpaca_live_secret | Alpaca | Trading (live) |
| perplexity_api_key | Perplexity | Trading briefings |
| cloudflare_api_token | Cloudflare | Cloudflare module |
| github_personal_token | GitHub | GitHub module |
| clickup_api_key | ClickUp | Notes, ClickUp sync |
| clickup_workspace_id | ClickUp | Notes, ClickUp sync |

---

## KEY ARCHITECTURAL DECISIONS

- **Replicate replaced by Gemini (Nano Banana 2)** April 2026 — sprites and recompose both on Gemini; gemini_api_key covers both
- **Ideogram stays for Generate + Edit** — best text accuracy (~90%), best inpainting
- **Gemini returns data URIs not CDN URLs** — gallery stores data URIs in localStorage; large but acceptable for local app
- **Ideogram Edit mask must use alpha:false canvas** — RGB PNG required; RGBA rejected by Ideogram API. Hard-won fix. Never revert.
- **All amounts stored in pence** (integers) — Finance module, never floats
- **SQLite first** — never query external APIs from display routes; sync to SQLite then read from SQLite
- **Vault for all secrets** — no hardcoded credentials anywhere
- **Config blob** at D:\AI Work\.ctrl-config.json — projects, preferences, portable

---

## KNOWN ISSUES / GOTCHAS

- @imgly/background-removal (ISNet) clips product photo edges — pending Remove.bg swap
- Gmail watch expires every 7 days — manual renewal needed until auto-renewal built
- Port 3001 EADDRINUSE on restart — kill with netstat + taskkill
- Gemini data URIs in localStorage can approach quota with many large generations
- PowerShell truncates long pasted commands — save as .ps1 and run
- No git history yet — repo not pushed to GitHub

---

## PROJECTS OUTSIDE CTRL

- **UnifyBI** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video. John uses Design module (Gemini Recompose + Ideogram) to create lifestyle imagery of the alarm clock.
- **Mobile Games** — planning phase, game concepts to be decided
