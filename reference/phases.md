# Build phases

Version 1 = four phases. Phase 1 is the plan (the setup questionnaire, done by the `setup-ai-os` skill). Phases 2 to 4 are the build. Each build phase is worked on a branch, ends with checks in the session, a pull request, the owner's look at the Vercel preview, their merge on GitHub, live checks and a MEMORY.md update. Suggest a new session per phase.

There is no local computer and no `.env.local`. The session never holds secrets: they live in the Vercel and Render dashboards.

## Phase 1: The plan
**Delivers:** `plan/answers.md`, `plan/PRD.md`, `plan/accounts-checklist.md`, `plan/MEMORY.md` and a filled-in `CLAUDE.md`. No code. Run by the `setup-ai-os` skill.

## Phase 2: The app
**Delivers:** live website with sign-in, a Today page per person, Tasks, Settings. Installable on phones.

Build in this order:
1. Next.js project at the repository root (TypeScript, Tailwind, App Router). `create-next-app` refuses a folder that already has files, so create it in a temporary folder and copy it in. Keep the installer's `README.md`, `CLAUDE.md`, `reference/`, `plan/` and `.claude/`; merge `.gitignore` rather than replacing it. Read the docs in `node_modules/next/dist/docs` before using Next APIs.
   - **Next.js 16 turns Cache Components on.** Pages that read the database or the session fail the production build unless the data is wrapped in `<Suspense>` or the page sets `export const instant = false`. `export const dynamic = "force-dynamic"` is now an error. Run `next typegen` before the first typecheck (it creates types such as `LayoutProps`).
2. `.env.example` with every variable blank and a comment saying where each value comes from and which dashboard (Vercel, Render, or both) it goes in. This is the owner's checklist for pasting keys.
3. In this session, set the commit email to their GitHub noreply address (`git config user.email "<id>+<username>@users.noreply.github.com"`; they find it in GitHub → Settings → Emails). Vercel can refuse to deploy commits it can't match to a GitHub account.
4. Migrations in `db/migrations/` + `npm run db:setup` (records which have run), plus Better Auth's tables. Row level security on for every table. **Migrations run at the start of every Vercel build** (`db:setup && next build`), so a preview has the tables it needs. That's why every migration must be additive (new tables, new nullable columns), never a drop or rename.
5. **Sign-in** with Better Auth, following `team-mode.md` for their answers.
6. Today page (calendar today and tomorrow if connected, Top 3, tasks due, suggestions block), Tasks page, Settings page (sources with status; team: members, invites, pending).
7. Web app manifest and icons for "Add to Home Screen".
8. Typecheck, lint and unit tests pass in the session. Push the branch and open the pull request.
9. **Human step: Supabase.** New project in a region near them. Connection string from **Connect** → **Transaction pooler** (port 6543), password filled in. They keep it for the Vercel step.
10. **Human steps for sign-in**, as their answers need: Google Cloud project and OAuth client, Resend key and sender domain (see `accounts.md`).
11. **Human step: Vercel.** Import their GitHub repo and paste each variable from `.env.example`, including a `BETTER_AUTH_SECRET` they create in their password manager (see `accounts.md`). The first deploy of `main` fails because the website isn't merged yet: warn them first that this is expected. Once they say "Vercel is connected", push to the branch so Vercel posts a preview link on the pull request. Then set the function region near the database, and add the production address's callback URL to the Google OAuth client.
12. They open the preview, check the pages, and merge.

**Checks** (on the live site after the merge; Google sign-in only works on the production address, not on previews): sign in with each method chosen; a non-allowed email is refused; in team mode, an invited / domain / pending user each behaves as designed; Today shows the right calendar; phone home-screen install works.

## Phase 3: The agent server and your first agent
**Delivers:** always-on agent server and the first agent (default: morning brief) running on schedule.

1. `agent/server.ts`: `/health`, `/run/:id` (shared secret), `/webhooks/:source`; scheduler and runner as in `architecture.md`; start-up cleanup; a **settings check** at start-up (see `gotchas.md`).
2. `workflows`, `workflow_runs`, `briefs`, `suggestions`, `agent_docs` tables.
3. OpenRouter client in `lib/` with model + backup, privacy options, exact cost recording.
4. First agent handler. Morning brief: gather sources (calendar, tasks, waiting items, recent call notes with today's people), number them, compute calendar flags in plain code (back-to-back, no lunch, unanswered invites, double bookings) with unit tests, one OpenRouter call with a strict JSON schema, validate, check citations, save to `briefs`, show on Today. Per person in team mode. Unit-test the handler in the session with recorded sample data and a fake model response (no keys needed).
5. **Ship the agent switched off** (`enabled = false`), so nothing runs on a schedule until they've seen a real output.
6. `render.yaml` Blueprint: web service, `runtime: node`, region near the database, smallest paid instance (check the current `plan` value in render.com/docs/blueprint-spec; never `free`), `healthCheckPath: /health`, `autoDeployTrigger: commit` from `main`, a `buildFilter` limited to `agent/`, `lib/`, `db/` and `package*.json`. Start command `node --import tsx agent/server.ts`; keep `tsx` in `dependencies`. Secrets as `sync: false`; `AGENT_SECRET` with `generateValue: true`.
7. Push, open the pull request, check the preview, merge.
8. **Human step: OpenRouter.** Account, credits, a key with a credit limit equal to their budget, privacy settings restricting to providers that don't collect data.
9. **Human step: Render.** New → Blueprint → their repo. Paste secrets when Render asks (database URL, OpenRouter key, Google client ID and secret, `AGENT_SECRET` which Render can generate). Then, on Vercel, set `AGENT_URL` (the Render address) and the same `AGENT_SECRET`, and redeploy.
10. **Human step: network secret.** So you can call `/run` without seeing the secret, they add it to their cloud environment (usually **Default**), following `accounts.md` → "Claude cloud environment" (the form differs for Team-plan members who aren't Owners). Then they start a new session.
11. Call recorder, if chosen and ready (see `call-recorders.md`); otherwise leave it until version 1 is live.

**Checks** (after the network secret is added; before that, this session can't reach the agent server, so ask the owner to open `/health` in their browser): `/health` returns 200 with schedules listed; `/run/...` without the secret returns 401; an unsigned webhook returns 401; `POST /run/morning_brief?wait=1` (from this session, the proxy adds the secret) produces a brief with valid citations; the brief shows on Today; then they switch the agent on; a deliberately broken source shows a warning.

Tell them: keep any old version of this agent running in parallel until the new one has earned their trust.

## Phase 4: The Agents tab and approvals
**Delivers:** the team view, and the "agents suggest, you decide" loop.

**Part A: Agents tab.**

Agents page: one card per agent (job title, schedule, model, on/off, Run now, last run and result, this month's spend). Stat cards: active agents, runs today, spend against the monthly cap. Profile page per agent: run history (time, trigger, result, model used, backup used, tokens, cost, duration, error), model and backup picker (from OpenRouter's list, ZDR + structured output filters, rough cost per run from the last ten runs), editable versioned CLAUDE.md and MEMORY.md with restore, model change log. Team mode: only admins change models, switches and instructions; members can Run now.

**Part B: Approvals.** Suggestions from any agent appear on Today (and a Review page in team mode for admins): approve (optionally edit first), reject with a reason, or dismiss. Approving a task suggestion creates the task with a link to its source. Rejection reasons are stored and passed to the agent's next run, so it learns. Nothing an agent produces changes anything else without this click.

**Checks:** change the model, Run now, see the new model in run history; switch the agent off and confirm the scheduler skips it within five minutes; costs match OpenRouter's activity page; approve and reject end to end; the reason appears in the next run's input; a member only sees their own suggestions.

**Version 1 is done.** Update CLAUDE.md status to "VERSION 1 LIVE".

## After version 1 (pick one at a time)
- **Meeting action items** from call transcripts (webhook → suggested tasks and "to chase", each with a word-for-word quote checked against the notes).
- **Meeting prep briefs** for every meeting with other people, rewritten when the meeting changes.
- **Evening wrap-up** and **weekly review.**
- **Shared context files** every agent reads (company, services, clients, writing voice), versioned, with a size limit per agent.
- **Per-person call recorders** in team mode.
- **Gmail drafts** (drafts only, plus a test that fails if anything calls send).
- **Business dashboard** (analytics, payments, newsletter), each source failing on its own; no AI needed.
- **Custom domain** (add the new Google callback first; on Cloudflare set records to "DNS only").
- **Outlook calendar.**
