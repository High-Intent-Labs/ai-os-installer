# <OS name>

Setup status: PLAN WRITTEN

At the start of every session, read this file, then `plan/MEMORY.md` (handoff section first). If the person says "where are we?", summarise what's done and what's next in three lines, then continue.

## What this is
<OS name> is a private website for <owner / team at company> with AI agents that run 24/7 in the cloud and suggest work for people to approve. Spec: `plan/PRD.md`. Accounts: `plan/accounts-checklist.md`.

## Key facts
- Mode: <just me | team>. Access: <rule>. Sign-in: <method>. Admins: <emails>.
- Time zone: <tz>. AI budget: <$> a month (OpenRouter limit).
- Owner's own email addresses and company domains (so agents know who "you" and colleagues are): <list>.

## Stack and where things live
- Code: the owner's private GitHub repo, worked on in Claude Code cloud sessions. Website code at the root (Next.js, TypeScript, Tailwind). Agent server in `agent/`. Shared logic in `lib/` (no website-only code there).
- Website: Vercel. Agent server: Render (web service). Database: Supabase Postgres via transaction pooler (IPv4). AI: OpenRouter. Sign-in: Better Auth.
- Migrations: numbered SQL in `db/migrations/`, additive only, applied with `npm run db:setup`.
- Details: `reference/architecture.md`. Problems seen before: `reference/gotchas.md`.

## Rules for agents (built into code, not just prompts)
1. AI suggests, people decide. Agents never: <list>.
2. Every claim cites a numbered source; the code checks citations.
3. People identified by email address, never first name.
4. Live data re-read at every run. A failed source shows as a warning, never silence.
5. AI keys live on the agent server only.

## Working rules
1. Secrets never enter this session. Never ask for one in chat; if one is pasted, ask them to rotate it. Secrets live in the Vercel and Render dashboards; the agent server secret is a network secret on the cloud environment.
2. Stop at every human step with numbered, click-by-click instructions. Wait for "done".
3. Work on a branch and open a pull request. The owner checks the Vercel preview and merges on GitHub. A merge into `main` deploys.
4. Run typecheck, lint and tests before every commit.
5. Check current vendor docs before using any third-party API; if `reference/` disagrees, the docs win.
6. When production breaks, read the logs first.
7. Plain English, one question at a time.
8. Update `plan/MEMORY.md` at the end of every piece of work.
