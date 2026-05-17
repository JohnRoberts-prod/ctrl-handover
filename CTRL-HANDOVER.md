# CTRL Project Handover
*Last updated: 2026-05-17 UTC*
*Session ended: WordDrop v3 drag-drop rebuild + plugin/skill bootstrap (NOT a CTRL build session — handover reflects the Mobile-Games + skills work that actually happened)*

---

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up where Claude Code left off.
John Roberts is the developer. Read this entire document before responding.

The active project this session was **Mobile-Games / WordDrop**, not CTRL.
CTRL was only touched to add the new `skill-ctrl-design.md` file.

CTRL codebase: `D:\AI Work\Control-Centre\` (untouched this session beyond skill files)
Mobile-Games: `D:\AI Work\Mobile-Games\`
WordDrop: `D:\AI Work\Mobile-Games\games\worddrop\WordDrop\`

---

## WHAT WE WERE BUILDING THIS SESSION

Two parallel threads:

**1. WordDrop v3 redesign — drag-and-drop completely rebuilt.** Spec was reversed mid-session: instead of validate-on-place crossword, the new mechanic is drop-anywhere on any empty cell. A CLEAR button (endless) walks rows + columns, finds valid en-GB words, removes them, scores them, and applies Tetris column gravity. Daily auto-clears on board full + all valid. Drag mechanic was rewritten three times this session as John iterated on feel — current state uses worklet-first Pan callbacks, ghost rendered OUTSIDE SafeAreaView (window-relative coords), snap-to-nearest with overhang slop, hit-test on ghost center not finger, and a live hover-cell highlight via useAnimatedReaction.

**2. Plugin / skill bootstrap for game dev.** Installed 4 Claude Code plugins (Software Mansion, Callstack RN best practices, Anthropic frontend-design, Jezweb Cloudflare). Merged two duplicate design-skill files into one canonical `skill-mobile-design.md` (859 lines) with detailed sections per plugin + a 0–3 review rubric. Created `skill-ctrl-design.md` (419 lines) at Control-Centre/skills for cross-stack consistency. Fixed two install blockers: github.com SSH host key + git `url.insteadOf` rewrite for plugin clones over HTTPS.

---

## CURRENT BUILD STATE

### Recently completed (this session)

- **WordDrop v3 drag-drop architecture** (worklet-first, ghost-at-root, snap-to-nearest, hover indicator)
- **`src/game/clearer.ts`** — clear+gravity engine (find valid words, remove, Tetris column settling)
- **`src/util/dragHitTest.ts`** — shared helper (GHOST_SIZE=56, GHOST_LIFT=110, hitTestNearestCell())
- **Daily Challenge** — tower builds up via constant-cadence drops (was wrong one-at-a-time), 3 stages with curated magic-word-square pools
- **MainMenu** — two-button picker (DAILY CHALLENGE + ENDLESS) replacing single PLAY
- **GameOver** — adds longest word + best word reveal
- **Plugins installed**: skills@swmansion, frontend-design@anthropics, react-native-best-practices@callstack-agent-skills, cloudflare@jezweb-skills
- **Canonical skill files**: `D:\AI Work\Mobile-Games\skills\skill-mobile-design.md` (merged), `D:\AI Work\Control-Centre\skills\skill-ctrl-design.md` (new)
- **Auto-load registered**: `@skills/skill-mobile-design.md` added to `D:\AI Work\Mobile-Games\CLAUDE.md`

### In progress right now (was being tested when /afk fired)

- Smoke-testing the new drag mechanic on emulator-5554 (com.worddrop)
- John's last reported issue: "the tile is placed slightly higher than it should be" — the fix (ghost outside SafeAreaView for window-relative coords) is shipped but not yet confirmed by John
- Daily Challenge constant-cadence drops just landed — untested
- Live hover indicator wired — untested

### Pending / next steps

1. **Smoke test drag on emulator** — corners + edges + every cell in both Endless and Daily
2. **Smoke test Daily** — constant drops, undo, auto-clear on full+valid, stage progression 3→4→5
3. **Test Endless CLEAR button** — valid word detection, scoring, column gravity
4. **Sanity-check plugins** in a fresh session by asking: "Review WordDrop's current UI using skill-mobile-design.md's review rubric"
5. **Update Claude Code to v2.1.139+** (`npm install -g @anthropic-ai/claude-code@latest`) — current 2.1.116 throws "unhandled case [object Object]" in VS Code

---

## ALL MODULES — STATUS (CTRL — untouched this session)

| Module | Location | Status | Notes |
|--------|----------|--------|-------|
| Home | src/frontend/src/modules/home/ | Working | basic, needs redesign |
| Claude Tab | src/frontend/src/modules/claude-tab/ | Working | xterm.js WebSocket |
| Gmail/Google | src/frontend/src/modules/gmail/ | Working | real data via OAuth |
| Tasks | src/frontend/src/modules/tasks/ | Working | |
| Projects | src/frontend/src/modules/projects/ | Working | Project Spine done |
| Finance | src/frontend/src/modules/finance/ | Working | ProjectSelector wired |
| Trading | src/frontend/src/modules/trading/ | Working | |
| GitHub | src/frontend/src/modules/github/ | Working | ProjectSelector wired |
| Cloudflare | src/frontend/src/modules/cloudflare/ | Working | |
| Brand Toolkit | src/frontend/src/modules/brand-toolkit/ | Working | |
| Settings | src/frontend/src/modules/settings/ | Working | |
| Admin | src/frontend/src/modules/admin/ | Working | Projects section live |

---

## FILES CREATED OR MODIFIED THIS SESSION

### Mobile-Games / WordDrop

```
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\game\clearer.ts                  — NEW: find valid words, remove, column gravity
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\util\dragHitTest.ts             — NEW: shared GHOST_SIZE/LIFT + hitTestNearestCell()
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\hooks\useEndlessGame.ts         — REWRITTEN: 5x5 empty, no place check, clear() action
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\hooks\useDailyChallenge.ts      — REWRITTEN: tower build-up, undo, auto-clear
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\components\Tower.tsx            — Worklet-first Pan, shared ghost values, runOnJS only for lift/end
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\components\Board.tsx            — Drop on any empty cell, hoverCell prop
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\screens\EndlessGame.tsx         — Ghost outside SafeAreaView, hit-test helper, CLEAR button
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\screens\DailyChallenge.tsx      — Same drag pattern, Undo button, tiles-left counter
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\screens\MainMenu.tsx            — Two-button picker (DAILY CHALLENGE + ENDLESS)
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\src\screens\GameOver.tsx            — Adds longest word + best word fields
D:\AI Work\Mobile-Games\games\worddrop\WordDrop\App.tsx                              — Routing for daily + endless + dailyresult
```

### Skills / docs

```
D:\AI Work\Mobile-Games\skills\skill-mobile-design.md         — MERGED + EXTENDED (859 lines, canonical)
D:\AI Work\Mobile-Games\skills\skill-mobile-game-design.md    — DELETED (merged into above)
D:\AI Work\Control-Centre\skills\skill-ctrl-design.md         — NEW (419 lines)
D:\AI Work\Mobile-Games\CLAUDE.md                              — added @skills/skill-mobile-design.md line
D:\AI Work\Mobile-Games\SESSION_STATE.md                       — overwritten (this session)
D:\AI Work\Mobile-Games\LEARNINGS.md                            — appended (10 new entries)
```

---

## RECENT GIT COMMITS

Control-Centre is not currently a git repo (no .git directory at root). WordDrop also not git-versioned. No commit history to surface.

---

## OPEN ISSUES / KNOWN BUGS

- **Claude Code VS Code extension on 2.1.116** throws "unhandled case [object Object]" intermittently when payloads contain types it can't render. Fix: `npm install -g @anthropic-ai/claude-code@latest` (target 2.1.139+).
- **Daily mode letter pools** depend on en-GB SOWPODS having TRETS, REEF, etc. — assumed valid, not verified.
- **Cloudflare leaderboard Worker** for endless scores is documented in WORDDROP-REDESIGN-v2.md but not yet built (deferred per spec — would be a dedicated session).
- **BatonDrop v2.0 AAB (versionCode 21)** still unsuploaded at `D:\AI Work\Mobile-Games\games\batondrop\app\android\app\build\outputs\bundle\release\app-release.aab` (125 MB) — versionCode 21 is "consumed" once it's uploaded so next attempt is 22.
- **Drag fix not yet confirmed** by John in hands-on testing.

---

## KEY DECISIONS MADE THIS SESSION

- **Drag mechanic is drop-anywhere with deferred validation.** v2 spec (validate-on-place crossword) was scrapped. CLEAR button validates in endless; daily auto-clears on full+valid.
- **Tower keeps gravity (auto-drop), board does not.** Player can place a tile in any empty cell, including top row with nothing below.
- **Daily uses constant cadence** (4500ms), no escalation. Tower builds up; game over at 8 same as endless.
- **Newest tower letter at BOTTOM** of stack (`flexDirection: column-reverse`) — matches spec figure.
- **Drag ghost outside SafeAreaView** is the canonical fix for visual-vs-hit-test coord mismatch. Insets-subtraction was a hack masking the real issue.
- **GHOST_LIFT = 110pt** (was 78). Hit-test uses ghost CENTER, not finger.
- **Snap-to-nearest** via `Math.round` + 1.25-cell overhang slop. Corners now reachable.
- **Live hover-cell highlight** via `useAnimatedReaction` watching ghost shared values; `runOnJS` only when cell ID changes.
- **One canonical skill file** `skill-mobile-design.md` (merged from two duplicates).
- **CTRL gets its own design skill** `skill-ctrl-design.md` — same plugins, project-specific framing. `frontend-design@anthropics` is NOT mobile-specific.
- **Plugin installs via VS Code UI**, not slash commands (the extension blocks `/plugin`). Fix git SSH issues first.

---

## BACKEND API ENDPOINTS ADDED THIS SESSION

None — no CTRL backend work this session.

---

## DATABASE CHANGES THIS SESSION

None — no CTRL DB work this session.

---

## IMPORTANT CONTEXT FOR NEXT SESSION

- **WordDrop drag rewrite ships in three layers and ALL are required**: (1) worklet-first Pan, (2) ghost rendered outside SafeAreaView, (3) shared hitTestNearestCell helper. Removing any one re-breaks the corners/snap behaviour.
- **Daily mode now tests the same overflow rule as endless** — tower fills, drops keep coming on a 4500ms timer until the sequence is exhausted. If John reports "daily feels too easy", the cadence may need shortening.
- **Plugin slash commands (`/plugin marketplace add` etc.) DO NOT WORK in the VS Code Claude Code extension** — they only work in the standalone Claude Code CLI. Use the Plugin UI in VS Code: Marketplaces tab → add `owner/repo` → Discover → Install for you.
- **Git is configured to clone github.com over HTTPS** even when SSH URLs are given (`url.insteadOf` rewrite). Public repo clones don't need an SSH key. Authenticated pushes do — `gh auth status` should show JohnRoberts-prod.
- **`skill-mobile-design.md` auto-loads on every Mobile-Games session** via the `@skills/skill-mobile-design.md` line in Mobile-Games/CLAUDE.md. Do the same for skill-ctrl-design.md when ready — would need adding to Control-Centre/CLAUDE.md.

---

## HOW TO START THE SYSTEM

CTRL:

```
D:\AI Work\START-ALL.bat
```

WordDrop:

```
cd "D:\AI Work\Mobile-Games\games\worddrop\WordDrop"
npx react-native start --port 8082
```

Then on the emulator (adb device emulator-5554):

```
adb reverse tcp:8082 tcp:8082
adb shell am start -n com.worddrop/.MainActivity
```

---

## PROJECTS OUTSIDE CTRL (for full context)

- **UnifyBI / CTRLPro** — hospitality SaaS dashboard, planning phase, first client conversation pending
- **BedBouncer** — ESP32 smart alarm, Kickstarter prep, needs product video
- **Mobile Games** — active. BatonDrop v2.0 ready to upload (AAB sitting). WordDrop v3 drag-drop just rebuilt this session. WordChain planned next (new NativeWind + GlueStack stack). Cavernborn in planning.

---

*This handover was generated by /afk on 2026-05-17 after a WordDrop + design-skills session. CTRL itself was untouched beyond the new skill-ctrl-design.md file.*
