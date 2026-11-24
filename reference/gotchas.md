# Gotchas (problems seen in earlier builds)

## Deploys and hosting
- **Vercel blocks deploys whose commit email doesn't match a GitHub account.** In each session, set the commit email to the owner's GitHub noreply address (`git config user.email`). Merges done on GitHub are always theirs.
- **Multi-line values get mangled when pasted into dashboards.** Build Supabase's public root certificate into the code.
- **Supabase: use the shared transaction pooler (port 6543).** It works over IPv4 on every plan. The direct connection and the dedicated pooler need IPv6 or a paid IPv4 add-on, and Vercel can't reach them without it.
- **Google says `redirect_uri_mismatch`:** the sign-in base URL doesn't match the live address. Take it from Vercel's production URL variable in production, and add every address to the OAuth client.
- **Every push to `main` redeploys the agent server too, killing runs in progress.** Mark orphaned "running" rows as failed at start-up; consider limiting Render's auto-deploy to `agent/` and `lib/` paths.
- **There's one database, shared by previews and production.** Migrations run on every Vercel build, previews included, so they must be additive. Never drop or rename in a migration.
- **Google sign-in doesn't work on Vercel preview links** (each has a new address Google doesn't know). Check Google sign-in on the production address; email and password works on previews.
- **Vercel previews ask for a Vercel login** by default (Deployment Protection). That's fine: the owner is signed in. Claude can't open them from the session, so ask the owner to look or send a screenshot.
- **The first Vercel deploy fails** at import, because `main` has no website yet. Warn the owner before they click Deploy.
- **Better Auth needs its own address** (`baseURL`). On Vercel, take it from `VERCEL_PROJECT_PRODUCTION_URL` in production and `VERCEL_URL` on previews, and list both in `trustedOrigins`, or email sign-in fails on previews with an "invalid origin" error.
- **`gh pr create` and other GraphQL commands are blocked in cloud sessions.** Use the Create PR button or the built-in GitHub tools.
- **When something fails in production, read the logs first** (Vercel → Logs; Render → service → Logs).

## Secrets
- **Never in chat, never in the session.** Into the Vercel and Render dashboards, pasted by the owner. The agent server secret goes in as a network secret on the cloud environment, which Claude can use but not read.
- **Never generate a secret in the session.** The owner creates long random values (for example `BETTER_AUTH_SECRET`) in their password manager. Render can generate `AGENT_SECRET` itself.
- **Wrongly pasted secrets are common** (three in the wrong boxes on day one, last time). At start-up, the agent server and the website check every setting is present and looks right (for example the database URL starts `postgresql://` and uses port 6543, the OpenRouter key starts `sk-or-`), and log which are missing or malformed, never the values. `/health` reports the same as a yes/no list. Trim whitespace around secrets in code.
- **Use read-only and restricted keys** wherever offered. AI keys on the agent server only.
- **Delete downloaded secret files** (Google client JSON) once the values are stored.

## Agents and AI
- **Start with one agent and let it earn trust** before switching off whatever it replaces.
- **A source that fails quietly is the worst bug.** Any failed source must show in the output and in Settings.
- **Thinking can eat the output limit** and cut JSON off. Set effort and max tokens explicitly; treat "length limit reached" as an error.
- **Don't let the model number its own citations.** Give sources stable ids, check them, renumber afterwards.
- **"No history" isn't "new".** Use statuses like known / colleague / no notes yet / unverified.
- **Colleagues' calendars show up in yours.** Filter to the person's own calendars; skip declined and cancelled events; merge duplicates.
- **Google "Testing" mode expires refresh tokens after 7 days.** Publish to production (External) or use Internal (Workspace).

## Performance
- **Create date formatters once, at module level**, never inside loops. Creating `Intl.DateTimeFormat` per call used hundreds of MB and crashed a 512 MB server.

## Product
- **Ask for a mockup before any redesign.**
- **Ship new agents switched off.** Run now, check the output, then switch on.
