# CTRL Project Handover
*Last updated: 2026-05-08 UTC*
*Session ended: Built BedBouncer marketing analytics infrastructure (CTRL Subscribers tab, Looker Studio Report tab, GA4 event tracking on bedbouncer.com, advertising-dashboards skill file)*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: `D:\AI Work\Control-Centre\`
Backend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Terminal server: node-pty WebSocket server on port 3002
Database: SQLite (better-sqlite3) at `D:\AI Work\.ctrl-data.db`

This session also touched the BedBouncer site repo at `D:\AI Work\BedBouncer\`
(deployed to Cloudflare Workers + Pages — public site at bedbouncer.com).

---

## WHAT WE WERE BUILDING THIS SESSION

A complete marketing analytics stack for BedBouncer (and reusable for CTRLPlay/CTRLPro).
Four threads:

1. **Domain migration** — moved BedBouncer primary domain from .co.uk to .com
2. **GA4 event tracking** — added scroll depth, section views, CTA clicks, and
   `generate_lead` conversion event firing on email signup
3. **CTRL Analytics extensions** — added two new tabs to the existing Analytics module:
   Subscribers (D1 waitlist data) and Report (Looker Studio iframe embed)
4. **Knowledge capture** — wrote comprehensive skill file from Coupler.io's 60+ Looker
   Studio dashboard examples article

Session ended with the user about to build their first Looker Studio report.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- BedBouncer domain migration: canonical, OG, Twitter, JSON-LD all updated to bedbouncer.com
- Created /sitemap.xml on bedbouncer.com
- Worker.js redirect logic added then partially rolled back (see Open Issues)
- analytics.js rewritten with scroll/section/CTA event tracking
- site.js fires `generate_lead` GA4 event on successful email signup
- New file: `src/frontend/src/modules/analytics/tabs/SubscribersTab.tsx`
- New file: `src/frontend/src/modules/analytics/tabs/ReportTab.tsx`
- Extended `analytics.service.ts` with `subscriberSource` + `lookerStudioUrl` on `AnalyticsSite`
- `SiteManager.tsx` now has subscriber source dropdown + Looker Studio embed URL field
- `analytics.css`: added subscribers tab styles + `report-frame` iframe styling
- New skill: `D:\AI Work\skills\skill-advertising-dashboards.md`

### In progress right now
- CTRL frontend likely needs restart to pick up new tabs in the running browser
- User reported "can't see Manage Sites" — the gear icon (⚙) is subtle. Probably
  just a frontend restart issue, but consider adding text label if it persists.
- bedbouncer.com is NOT yet a Cloudflare custom domain on the worker.

### Pending / next steps
1. Restart CTRL frontend (`npm run dev` from `src/frontend/`) so new tabs render
2. Verify Manage Sites gear is visible top-right of Analytics module
3. Add bedbouncer.com as Cloudflare custom domain on the bedbouncer worker
4. Re-add bedbouncer.co.uk → bedbouncer.com 301 redirect once .com is serving
5. User to build first Looker Studio report (recommend All-in-one Marketing template)
6. Paste embed URL into CTRL Site Manager → Looker Studio Embed URL field
7. Add bedbouncer.com property in Search Console + submit sitemap.xml

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Analytics | src/frontend/src/modules/analytics/ | EXTENDED THIS SESSION | 5 tabs: Analytics, Search Console, Live, Subscribers (new), Report (new) |
| Settings | src/frontend/src/modules/settings/ | stable | |
| Admin | src/frontend/src/modules/admin/ | stable | |
| Trading | src/frontend/src/modules/trading/ | stable | |
| Projects | src/frontend/src/modules/projects/ | stable | |
| Game Shop | src/frontend/src/modules/game-shop/ | has pre-existing TS error | AddItemSection.tsx missing asset_version — not from this session |
| (others) | various | not touched this session | |

---

## FILES CREATED OR MODIFIED THIS SESSION

### CTRL frontend (D:\AI Work\Control-Centre\)
```
src/frontend/src/modules/analytics/Analytics.tsx — added 'subscribers' and 'report' tabs to TABS array, wired both render branches
src/frontend/src/modules/analytics/SiteManager.tsx — added subscriberSource <select> and lookerStudioUrl <input> fields
src/frontend/src/modules/analytics/analytics.css — added .sub-* classes for subscriber tab, .report-frame-wrap for iframe, .ana-body:has(.report-frame-wrap) overflow override
src/frontend/src/modules/analytics/tabs/SubscribersTab.tsx — NEW — KPIs, daily growth bar chart (SVG), cumulative line chart (SVG), source breakdown, country breakdown, day-of-week chart, recent signups table with masked emails
src/frontend/src/modules/analytics/tabs/ReportTab.tsx — NEW — simple iframe wrapper for Looker Studio embed URL with empty-state instructions
src/frontend/src/services/analytics.service.ts — added subscriberSource? + lookerStudioUrl? to AnalyticsSite interface, added SubscriberRaw interface, added analyticsApi.subscribers(source) method
```

### CTRL project memory
```
SESSION_STATE.md — overwritten
LEARNINGS.md — appended 7 new learnings from this session
```

### BedBouncer site (D:\AI Work\BedBouncer\)
```
index.html — canonical/OG/Twitter/JSON-LD all updated from bedbouncer.co.uk to bedbouncer.com
worker.js — added www.bedbouncer.com → bedbouncer.com 301 redirect (kept), www.bedbouncer.co.uk → bedbouncer.com 301 redirect (kept), bedbouncer.co.uk → bedbouncer.com 301 redirect was added then ROLLED BACK because .com isn't a Cloudflare custom domain yet. Added CORS for bedbouncer.com origins.
analytics.js — rewritten: scroll depth (25/50/75/100%), section view tracking for #pricing #pro #compare #faq #waitlist #how, CTA click tracking on hero-form and waitlist-form buttons, exposed window.bbTrack for site.js
site.js — calls window.bbTrack('generate_lead', { form_location: id }) on successful email signup
sitemap.xml — NEW — single-URL sitemap for Search Console submission
.gitignore — whitelist updated to track analytics.js, site.js, worker.js, sitemap.xml, analytics-dashboard.html (latter then deleted)
analytics-dashboard.html — created then DELETED — Looker Studio-style dashboard accidentally placed on public site, removed because admin pages shouldn't be public
```

### Shared skills (D:\AI Work\skills\)
```
skill-advertising-dashboards.md — NEW — comprehensive reference: Coupler.io connector matrix, Looker Studio visual design language, dashboard catalogue with use cases per project, standard PPC metrics glossary, UTM parameter templates per platform, build order workflow, common failure modes
```

---

## RECENT GIT COMMITS

CTRL repo: not initialised as git locally — no commit history available.

BedBouncer repo (committed and pushed during this session):
```
9e4b781 Add marketing analytics dashboard and enhanced GA4 event tracking
03ce832 Migrate primary domain to bedbouncer.com, add SEO structured data and sitemap
c3783ac Remove analytics dashboard from public site — belongs in CTRL
```

---

## OPEN ISSUES / KNOWN BUGS

1. **CRITICAL: bedbouncer.co.uk redirect rolled back** — Original plan was bedbouncer.co.uk → bedbouncer.com 301 redirect, but this broke the live site because bedbouncer.com isn't yet configured as a Cloudflare custom domain on the worker. Need to fix Cloudflare DNS first, then re-add the redirect to worker.js and re-deploy.

2. **User reports "can't see Manage Sites"** — Likely just a frontend restart issue. The button collapses to a bare gear icon (⚙) when sites exist; only shows "Add site" label when site list is empty. Consider making this more obvious.

3. **Pre-existing TS error** — `src/frontend/src/modules/game-shop/components/AddItemSection.tsx` line 11: 'asset_version' missing in ShopItem type. Not from this session, was already there. Doesn't block analytics work.

4. **www.bedbouncer.com redirect** — Worker code has the redirect, but only fires if Cloudflare routes www.bedbouncer.com requests to the worker. Requires DNS CNAME (proxied) + Workers custom domain config. Not yet verified.

---

## KEY DECISIONS MADE THIS SESSION

1. **Embed real Looker Studio in CTRL via iframe** rather than building custom React dashboards. User wants the actual Looker Studio aesthetic (light theme, gradient KPI cards, heat-mapped tables) — replicating it inside CTRL's dark theme would be jarring.

2. **Subscribers tab stays as custom CTRL component** — Looker Studio cannot connect to Cloudflare D1 directly. Subscriber data flows: D1 → bedbouncer worker `/api/admin/subscribers` → CTRL backend `/api/subscribers/:source` → CTRL SubscribersTab.

3. **Public-facing analytics dashboard deleted** — Initially built `analytics-dashboard.html` on bedbouncer.com (password protected), but admin pages should not be on the public site. Removed and rebuilt as a CTRL tab.

4. **Coupler.io is the connector** — For non-Google ad platforms (Meta/TikTok/LinkedIn/etc.), Coupler.io's free tier covers one connector, paid plans (~£40/mo) cover more. This is captured in the new skill file.

5. **bedbouncer.com is the primary domain going forward** — .co.uk will become a redirect once .com is properly served by the worker.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None new in CTRL backend. The existing `GET /api/subscribers/:source` route (added in a prior session) is consumed by the new SubscribersTab.

In the BedBouncer worker:
- `GET /api/admin/subscribers` — already existed, no changes
- Added CORS for https://bedbouncer.com and https://www.bedbouncer.com origins
- Redirect handler: www.bedbouncer.com + www.bedbouncer.co.uk → bedbouncer.com (301)
- Redirect for bare bedbouncer.co.uk was added then rolled back

---

## DATABASE CHANGES THIS SESSION

None. The vault key `analytics_sites` now stores additional optional fields per site:
```json
{
  "name": "BedBouncer",
  "ga4PropertyId": "XXX",
  "gscSiteUrl": "https://bedbouncer.com/",
  "subscriberSource": "bedbouncer",
  "lookerStudioUrl": "https://lookerstudio.google.com/embed/reporting/..."
}
```
Existing sites without these fields work unchanged (TypeScript optional).

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. **The CTRL Analytics module already had GA4, Search Console, and Live tabs working** — built in a previous session (2026-05-01). This session ADDED Subscribers and Report tabs, didn't rebuild anything. There is also a complete backend service at `src/backend/src/services/analytics.service.ts` with caching in the `analytics_cache` SQLite table.

2. **GA4 + Search Console need OAuth scopes refreshed** — per LEARNINGS.md from 2026-05-01: "Google OAuth needs reconnecting to pick up new analytics scopes". User may not have done this yet. If Analytics tab shows errors, this is the first thing to check.

3. **GA4 takes 24-48h on new properties** — bedbouncer.com is a new property created 2026-05-01. Empty Looker Studio reports might just be data lag, not broken connectors.

4. **Looker Studio embed URL format**: `https://lookerstudio.google.com/embed/reporting/{REPORT_ID}/page/{PAGE_ID}`. Get this from Share → Embed report → Enable embedding → copy URL.

5. **The user prefers terse, actionable responses** — no preamble, no postamble. Tell him what to do, not what you're going to do.

6. **Lane7 hard line** — user works at Lane7. CTRLPro must NEVER be sold to or pitched to Lane7. No Lane7 data, contacts, or operational knowledge used for commercial purposes outside Lane7. This is non-negotiable. See `D:\AI Work\CLAUDE.md`.

7. **Zero hardcoding rule** — every personal preference, path, credential, or name lives in config blob or vault, not in code. CTRL must be packageable as a product without refactoring.

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

- **CTRLPro** — hospitality SaaS, planning phase, first client conversation pending. NEVER Lane7.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep Spring 2026, website live at bedbouncer.com. Now has full GA4 event tracking ready for paid campaigns.
- **CTRLPlay** — mobile games studio (Cavernborn, BatonDrop, WordDrop). Cavernborn = idle RPG, RN project not yet initialised. BatonDrop active build. WordDrop GDD complete.

---

## VISIT THE NEW SKILL

For any future advertising/marketing dashboard work, read first:
`D:\AI Work\skills\skill-advertising-dashboards.md`

It covers: Coupler.io connector matrix (which ad platforms are free vs paid in Looker Studio),
the Looker Studio visual design language (gradient KPI cards, heat-mapped tables, etc.),
a catalogue of 30+ relevant dashboards with use cases per project, standard PPC metrics
glossary, UTM parameter templates per platform, build order workflow, and common failure modes.
