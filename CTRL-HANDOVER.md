# CTRL Project Handover
*Last updated: 2026-05-08 UTC*
*Session ended: Massive dashboard design system rollout — Analytics + Finance restyled with dark/neon aesthetic, dashboard skill expanded into canonical metrics + archetype reference, Looker Studio embed permanently removed*

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

End-to-end dashboard design system for CTRL. Six threads:

1. **Created the Dashboard Design System skill** at `D:\AI Work\skills\skill-dashboard-design-system.md` — this is now the canonical reference for any dashboard work in CTRL, CTRLPro, BedBouncer or future products.
2. **Rebuilt CTRL Analytics module** with the new dark/neon aesthetic (pure black canvas, hot pink filled active tabs, 36-56px hero KPIs, italic uppercase display headings, neon cyan/lime/orange chart palette).
3. **Rebuilt Finance module** with the same aesthetic via append-only CSS overrides (KPI numbers scaled to 27px per user preference — 75% of analytics size).
4. **Permanently removed Looker Studio iframe embed** — Report tab, ReportTab.tsx, lookerStudioUrl field on AnalyticsSite all deleted. Documented in skill that this is a permanent decision.
5. **Resolved Google OAuth scope-insufficient errors** — diagnosed as Google Cloud Console issue (APIs not enabled + scopes not registered on consent screen), fixed by user.
6. **Massively expanded the dashboard skill** with comprehensive metrics catalogue (PPC, GA4, SEO, email, social, ecommerce, CRM, finance, subscribers, mobile games, hospitality SaaS) and 14 dashboard archetype recipes.

Session ended just after John added the metrics catalogue work — frontend not yet restarted to verify final state visually.

---

## CURRENT BUILD STATE

### Recently completed (this session)
- Added neon design tokens to `src/frontend/src/core/tokens.css` (additive — no existing tokens changed)
- Full rewrite of `src/frontend/src/modules/analytics/analytics.css` (~900 lines) with dark/neon aesthetic
- Full append override section in `src/frontend/src/modules/finance/finance.css` (~330 lines added at end)
- Updated chart colours in `AnalyticsTab.tsx` (Users → pink, Sessions → cyan)
- Updated chart colours in `SearchConsoleTab.tsx` (Clicks → lime, Impressions → amber)
- Updated chart colours in `SubscribersTab.tsx` (replaced var(--blue) with var(--neon-pink))
- Updated chart colours in `Finance OverviewTab.tsx` (line colours: lime/red, merchant: pink)
- Live tab hero number now uses gradient text (white → pink) at 96px
- `Manage Sites` button now shows "Manage" text next to gear icon (was bare gear)
- Deleted `src/frontend/src/modules/analytics/tabs/ReportTab.tsx`
- Removed `lookerStudioUrl` field from AnalyticsSite interface and SiteManager UI
- Removed `report-frame*` CSS classes
- Created `D:\AI Work\skills\skill-dashboard-design-system.md` — comprehensive skill
- Skill now has 15 component patterns (was 8), full metrics catalogue (~280 metrics across 13 categories), and 14 dashboard archetype recipes

### In progress right now
- Finance and Analytics restyled but **frontend not yet restarted** — user needs to verify visually
- Stages 1, 5, 6 of the dashboard rollout plan are complete; Stages 8-10 pending
- Advanced chart components NOT yet built as React components (ScatterChart, Funnel, HalfGauge, HeatBarTableCell, ComboChart, WorldHeatMap, CardHeaderInline)

### Pending / next steps
1. **User restart frontend** (`npm run dev` from src/frontend/) to verify visual state
2. **Stage 8 — Advanced chart components**: build ScatterChart, Funnel (h+v variants), HalfGauge, HeatBarTableCell, ComboChart, WorldHeatMap, CardHeaderInline as reusable React components
3. **Stage 9 — Build the archetype dashboards** in priority order:
   - Pre-launch Waitlist (BedBouncer, partly done in SubscribersTab)
   - Single-Platform Ad Performance (when paid campaigns launch)
   - SEO Health Check (partly done in SearchConsoleTab)
   - Cash Flow / AR-AP (partly done in Finance Overview)
   - Mobile Game LiveOps (when CTRLPlay launches)
4. Apply design system to remaining modules: Trading, Projects, Settings, Gmail, GitHub, Cloudflare, etc.
5. Cloudflare DNS for bedbouncer.com → re-enable bedbouncer.co.uk redirect
6. Build first BedBouncer Looker Studio report (user can build natively now using the new components when ready)

---

## ALL MODULES — STATUS

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Analytics | src/frontend/src/modules/analytics/ | **REBUILT 2026-05-08** | Dark/neon aesthetic. 4 tabs: Analytics, Search Console, Live, Subscribers. Report tab deleted. |
| Finance | src/frontend/src/modules/finance/ | **REBUILT 2026-05-08** | Dark/neon overrides via append. KPI numbers at 27px (75% scale per user). |
| Settings | src/frontend/src/modules/settings/ | stable | Has Google OAuth reconnect flow |
| Admin | src/frontend/src/modules/admin/ | stable | |
| Trading | src/frontend/src/modules/trading/ | stable | Candidate for design system rollout |
| Projects | src/frontend/src/modules/projects/ | stable | Candidate for design system rollout |
| Game Shop | src/frontend/src/modules/game-shop/ | has pre-existing TS error | AddItemSection.tsx asset_version missing (not from this session) |
| Cloudflare | src/frontend/src/modules/cloudflare/ | stable | |
| Gmail | src/frontend/src/modules/gmail/ | stable | OAuth issue resolved this session |
| GitHub | src/frontend/src/modules/github/ | stable | |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | stable | |

---

## FILES CREATED OR MODIFIED THIS SESSION

### CTRL frontend
```
src/frontend/src/core/tokens.css — APPENDED neon dashboard tokens (additive, no existing tokens changed)
src/frontend/src/modules/analytics/Analytics.tsx — removed report tab + ReportTab import, added "Manage" text label
src/frontend/src/modules/analytics/SiteManager.tsx — removed lookerStudioUrl field, updated EMPTY constant
src/frontend/src/modules/analytics/analytics.css — FULL REWRITE (~900 lines) with dark/neon aesthetic
src/frontend/src/modules/analytics/tabs/AnalyticsTab.tsx — chart colours updated (pink/cyan)
src/frontend/src/modules/analytics/tabs/SearchConsoleTab.tsx — chart colours updated (lime/amber)
src/frontend/src/modules/analytics/tabs/SubscribersTab.tsx — chart fill/stroke updated (pink)
src/frontend/src/modules/analytics/tabs/ReportTab.tsx — DELETED
src/frontend/src/modules/finance/finance.css — APPENDED ~330 line override section at end
src/frontend/src/modules/finance/components/OverviewTab.tsx — chart line colours updated (lime/red/pink)
src/frontend/src/services/analytics.service.ts — removed lookerStudioUrl from AnalyticsSite interface
```

### CTRL project memory
```
SESSION_STATE.md — overwritten
LEARNINGS.md — appended 8 new learnings from this session
```

### Shared skills (D:\AI Work\skills\)
```
skill-dashboard-design-system.md — CREATED then MASSIVELY EXPANDED. Now contains:
  - Core principles (7 rules)
  - Design tokens (full :root CSS variable block)
  - Typography patterns
  - 15 component patterns (KpiCard, StatusPill, HeatBarList, Banner, TabNav, DataTable, Sparkline, FilterChip, InlineHeaderKpi, ScatterChart, Funnel-h, Funnel-v, HalfGauge, HeatBarTableCell, ComboChart, WorldHeatMap)
  - Chart styling rules + 7-colour CHART_COLORS array
  - METRICS CATALOGUE (~280 metrics across 13 categories with formulas, chart types, benchmarks)
  - 14 DASHBOARD ARCHETYPES (pre-built configurations for common dashboards)
  - Layout patterns
  - React component shapes
  - 10-stage rollout plan
  - Do/Don't quick reference
  - Looker Studio decision (permanent removal)
```

---

## RECENT GIT COMMITS

CTRL repo: not initialised as git locally — no commit history available.

BedBouncer repo (from earlier sessions, no new commits this session):
```
9e4b781 Add marketing analytics dashboard and enhanced GA4 event tracking
03ce832 Migrate primary domain to bedbouncer.com, add SEO structured data and sitemap
c3783ac Remove analytics dashboard from public site — belongs in CTRL
```

---

## OPEN ISSUES / KNOWN BUGS

1. **Frontend not restarted** — User needs to run `npm run dev` from src/frontend/ to see all the design system changes rendered. TypeScript compiles cleanly (verified twice this session, excluding pre-existing game-shop error).

2. **Pre-existing TS error** — `src/frontend/src/modules/game-shop/components/AddItemSection.tsx` line 11: 'asset_version' missing in ShopItem type. Not from this session, was already there.

3. **Cloudflare DNS for bedbouncer.com** — still not configured as Custom Domain on the worker, so bedbouncer.co.uk → bedbouncer.com 301 redirect remains rolled back. Next time user is in Cloudflare dashboard, set this up.

4. **Lingering hardcoded colours in finance subviews** — AccountsTab, RulesTab, ContractsTab, SavingsDash, PensionDash still have some hardcoded blue/teal colours in TSX inline styles. Some of these are user-configurable per-account colours and should NOT be changed. Audit case-by-case if a specific subview looks off.

---

## KEY DECISIONS MADE THIS SESSION

1. **Dark/neon aesthetic chosen for all CTRL dashboards** — pure black canvas, hot pink primary accent (#FF1493), neon cyan/lime/orange/amber/purple secondaries. Italic uppercase display headings. Hero-sized KPI numerals (27-56px). Hairline card borders. This look is significantly better than Looker Studio's light/gradient style for an "operations centre" UI and is now the permanent CTRL design language.

2. **Looker Studio iframe embed permanently abandoned** — Report tab and all related code/CSS removed. CTRL builds dashboards natively using its own GA4 + GSC + D1 API access. Documented in skill so future sessions don't reintroduce it.

3. **Subscribers tab stays as custom CTRL component** — Looker Studio cannot connect to Cloudflare D1 directly, so subscriber data needs CTRL's own dashboard.

4. **Finance KPI numerals scaled to 27px** (75% of Analytics 36px) per explicit user request — Analytics module unchanged at 36px.

5. **CSS strategy for large existing files** — for finance.css (2866 lines), used append-only override at end rather than rewriting. Preserves working layouts, uses !important sparingly to win on specificity. Contrast: analytics.css was rewritten in full because it was smaller.

6. **Tokens are additive** — never replace or rename existing tokens in tokens.css (other modules depend on them). Add new tokens with new names (--neon-pink, --dash-bg, etc.).

7. **Dashboard skill expanded into canonical reference** for both visual design AND metric definitions — covers PPC, GA4, SEO, email, social, ecommerce, CRM, finance, subscribers, mobile games, hospitality SaaS. Includes 14 dashboard archetype recipes (pre-launch waitlist, ad performance, conversion funnel, SEO health, cash flow / AR-AP, etc.) so future sessions can build any dashboard by picking an archetype + components.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None.

---

## DATABASE CHANGES THIS SESSION

None. The vault key 'analytics_sites' now stores ONE LESS optional field per site (lookerStudioUrl removed). Sites that already had lookerStudioUrl values stored will simply ignore the field — no migration needed.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

1. **The dashboard skill is the canonical reference** — `D:\AI Work\skills\skill-dashboard-design-system.md`. Read it before any new dashboard work. It contains the complete component library, metrics catalogue with formulas, and 14 archetype recipes.

2. **Design tokens are in `src/frontend/src/core/tokens.css`** — neon dashboard tokens are at the bottom in their own section. Always reference these via CSS variables, never hardcode.

3. **CSS class naming convention preserved** — both analytics (ana-*) and finance (fin-*) module rebuilds preserved existing class names so React components didn't need restructuring. Future restyles should follow this pattern: rewrite CSS, leave component structure alone.

4. **OAuth troubleshooting playbook** — if any Google API returns ACCESS_TOKEN_SCOPE_INSUFFICIENT, the fix is in Google Cloud Console: (a) enable the API in API Library, (b) register the scope on OAuth Consent Screen, (c) user revokes at myaccount.google.com/permissions and reconnects. CTRL backend code is correct.

5. **Stage 8 of rollout is the highest-value next work** — building ScatterChart, Funnel, HalfGauge, HeatBarTableCell, ComboChart, WorldHeatMap as reusable React components. Once these exist, building any of the 14 archetype dashboards becomes assembly work.

6. **Employer separation rule** — see `D:\AI Work\CLAUDE.md` for the full policy. Read it before any CTRLPro work or before adding any data structure / KPI / business logic that could be confused with current employer's domain. The dashboard skill is purely visual + universal metrics — fine to use anywhere.

7. **Zero hardcoding rule** — every personal preference, path, credential, or name lives in config blob or vault. CTRL must be packageable as a product without refactoring.

8. **The user prefers terse responses** — no preamble, no postamble, no narration of what you're about to do. State results, file paths with line numbers, and decisions directly.

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

- **CTRLPro** — hospitality SaaS, planning phase, first client conversation pending. Dashboard skill includes a Hospitality Multi-Venue archetype ready to build when first client signs.
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep Spring 2026, website live at bedbouncer.com. Now has full GA4 event tracking + dashboard skill includes the Pre-launch Waitlist archetype.
- **CTRLPlay** — mobile games studio (Cavernborn, BatonDrop, WordDrop). Dashboard skill includes a Mobile Game LiveOps archetype (DAU/MAU, retention curves, LTV:CPI ratio targets).
