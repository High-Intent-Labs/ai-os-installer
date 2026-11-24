# AI OS Installer

Setup status: NOT SET UP

This repository installs a personal or team AI operating system: a private website (Next.js on Vercel), a database (Postgres on Supabase), an always-on agent server (Node on Render) and AI agents that call models through OpenRouter. The person using it is a business owner who has finished a 4-week AI agents course. They are comfortable with Claude Code but are not a developer.

You are running in a **Claude Code cloud session** on their own private copy of this repository. Nothing runs on their computer. There is no local preview: checks happen on Vercel preview links, the live site and the agent server's `/health`.

## What to do
- If the person says "set up my AI OS", "start", "install", or anything similar, and the status above is NOT SET UP: follow the `setup-ai-os` skill.
- If they say "where are we?" or start a new session after setup: read `plan/MEMORY.md` (the handoff section at the top) and `plan/PRD.md`, tell them in three lines what's done and what's next, and continue from there.

## Working rules (apply at all times)
1. **Secrets never enter this session.** Never ask for a key, password or connection string in chat, and if they paste one, tell them to rotate it. Secrets go only into the Vercel and Render dashboards, which the person fills in. The only exception is the agent server secret, added by the person as a **network secret** on their cloud environment (see `reference/accounts.md`), which the proxy attaches without you seeing it.
2. **Stop at every human step** (create an account, paste a key, click a button in a dashboard, approve a Google screen). Give numbered click-by-click instructions, then wait for "done".
3. **Nothing goes live without their OK.** Work on a branch, push it, and open a pull request. The person checks the Vercel preview link, then merges on GitHub. Only a merge into `main` deploys the live website and agent server.
4. **AI suggests, people decide.** No agent sends a message, changes a calendar, adds a task or publishes anything without a click. Email agents make drafts only.
5. **Check vendor docs before using any third-party API.** Prices, plans, endpoints and dashboard menus change. If something in `reference/` disagrees with current docs, the docs win: tell the person. If a docs site is blocked by the network settings, tell them which domain to add to their environment's allowed list.
6. **Never delete their data**, a database or a cloud project without an explicit yes for that exact action.
7. Plain English. Say "website", "database", "agent server", not jargon. One question at a time.
8. Update `plan/MEMORY.md` at the end of every piece of work: the handoff section at the top, then a dated line in the log. Commit it with the work, so the next session (on any computer) sees it.

## Reference (read when needed, not all at once)
- `reference/architecture.md`: the parts, how they talk, the database tables, the agent design rules.
- `reference/phases.md`: the four phases, with checks.
- `reference/accounts.md`: every account, which plan, and click-by-click setup.
- `reference/team-mode.md`: sign-in options, access rules and roles.
- `reference/call-recorders.md`: connecting Granola, Fireflies, Otter, Fathom, Zoom or Google Meet.
- `reference/gotchas.md`: problems seen in earlier builds, and their fixes.
