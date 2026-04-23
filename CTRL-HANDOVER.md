# CTRL Project Handover
Last updated: 2026-04-23 UTC

## HOW TO USE THIS DOCUMENT

You are Claude web browser picking up a CTRL development session.
John Roberts is the developer. Read this entire document before responding.

The CTRL codebase is at: D:AI WorkControl-CentreBackend: Node.js + Express + TypeScript on port 3001
Frontend: React 18 + Vite + TypeScript on port 5173
Database: SQLite at D:AI Work.ctrl-data.db

## WHAT WE WERE BUILDING

GitHub management dashboard inside CTRL, Karpathy agent principles installed, /afk handover system built. Discovered Gist URL blocked by robots.txt in Claude web - moving handover to raw.githubusercontent.com repo file.

## PENDING NEXT STEPS

1. Add github_personal_token to vault (Settings > Integrations) - GitHub dashboard needs this
2. Verify trading terminal context loads on next fresh session (not reconnect)
3. Push CTRL codebase to GitHub (critical path)
4. Set up 4 daily trading routines in Routines tab
5. Update /afk skill to push to this repo instead of Gist

## ALL MODULES STATUS

- Home: Basic, needs redesign
- Claude Tab: Working (xterm.js + node-pty)
- Gmail/Google: Working (syncs to SQLite)
- Tasks: Working (Google Tasks sync)
- Projects: Working (board view)
- Finance: Working (double-entry, pence integers)
- Trading: Working (Alpaca paper trading)
- GitHub: Built this session - needs github_personal_token in vault
- Cloudflare: Working (DNS/zones/tunnels)
- Settings: Working (vault, OAuth, integrations)
- Admin: Working

## FILES CHANGED THIS SESSION

src/backend/src/services/github.service.ts - NEW
src/backend/src/routes/github.routes.ts - NEW
src/frontend/src/services/github.service.ts - NEW
src/frontend/src/modules/github/GitHub.tsx - NEW
src/frontend/src/modules/github/github.css - NEW
src/frontend/src/core/AppShell.tsx - UPDATED wired GitHub
src/backend/src/server.ts - UPDATED mounted /api/github
src/terminal-server/src/server.ts - UPDATED page-specific session dirs
D:/AI Work/ctrl-sessions/trading/CLAUDE.md - NEW loads trading skills
D:/AI Work/ctrl-sessions/google/CLAUDE.md - NEW loads email skills
D:/AI Work/ctrl-sessions/tasks/CLAUDE.md - NEW loads tasks skill
D:/AI Work/skills/karpathy-agent-principles.md - NEW
D:/AI Work/CLAUDE.md - UPDATED added karpathy principles
D:/AI Work/Control-Centre/CLAUDE.md - UPDATED added karpathy principles
C:/Users/admin/.claude/skills/afk/SKILL.md - NEW /afk skill
C:/Users/admin/.claude/CLAUDE.md - UPDATED /afk registered

## KEY DECISIONS

- Terminal context: CLAUDE.md auto-loading only. PTY stdin @-references never again.
- Session dirs at D:AI Workctrl-sessions (outside Control-Centre)
- Handover: raw.githubusercontent.com not Gist
- GitHub API: fetch only, no Octokit, affiliation= param not type=
- Karpathy principles in both master CLAUDE.md files

## HOW TO START

D:AI WorkSTART-ALL.bat

## IMPORTANT GOTCHAS

- GitHub dashboard 404s until github_personal_token added to vault
- Trading terminal CLAUDE.md only loads on NEW sessions, not reconnects
- CTRL codebase not pushed to GitHub yet
