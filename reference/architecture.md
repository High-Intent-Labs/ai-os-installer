# Architecture

## The parts
| Part | Tool | Job |
|---|---|---|
| Website | Next.js (TypeScript, App Router, Tailwind) on Vercel | Pages and buttons. Reads and writes the database. Asks the agent server to "run now". **Never calls an AI model.** |
| Agent server | Plain Node (`tsx`, `croner`) on Render, a "web" service | Runs every agent and schedule, receives webhooks, makes every AI call. Always on. |
| Database | Postgres on Supabase | Single source of truth: users, tasks, agents and settings, every run with its cost, briefs, suggestions, sync status. |
| Sign-in | Better Auth | Google and/or email + password. Sessions in the database. Google tokens stored so the agent server can read calendars while people sleep. |
| AI | OpenRouter | One key for Claude and other models. Each agent names a model and a backup. Exact cost per call comes back with each response. |
| Email sender | Resend (only with email + password) | Verification and password-reset emails. |
| Code | One private GitHub repo | Website at the root, agent server in `agent/`, shared logic in `lib/`. Every push to `main` deploys both. |

## Why it's split this way
- **Runs without a laptop.** The agent server is always on.
- **The website does UI only.** Vercel runs code for seconds at a time; agents need minutes and schedules. So agents live on the agent server.
- **Decisions go through the database.** Approving a suggestion writes "approved"; the agent server picks it up. A website redeploy mid-way loses nothing.
- **Shared code.** Database access, dates and time zones, Google token renewal, OpenRouter calls live in `lib/` so both sides use them.

## Agent server routes
- `GET /health`: up or not, which schedules are loaded.
- `POST /run/<agent-id>`: run one agent now (optional `?user=<id>` for per-person agents). Requires `Authorization: Bearer <AGENT_SECRET>`. Answers at once; `?wait=1` waits for the result (for testing).
- `POST /webhooks/<source>`: incoming webhooks (for example "call notes ready"). Verify the sender's signature; store each event id so retries run once.

## Database tables (version 1)
| Table | Holds |
|---|---|
| `user`, `session`, `account`, `verification` | Better Auth's tables (Google tokens in `account`) |
| `user_profile` | role (`admin` / `member`), status (`active` / `pending` / `removed`), time zone, display name |
| `invites` | Team, invite-only mode: email, invited by, accepted at |
| `categories`, `tasks` | Shared categories; tasks with `user_id`, title, category, priority, due date, Top 3 date, source link |
| `workflows` | One row per agent: id, name, job title, description, schedule (cron), `per_user` flag, model, backup model, enabled, settings (JSON) |
| `workflow_runs` | Every run: workflow, user (if per-person), trigger, status (`ok`, `used_backup`, `failed`, `skipped`), model used, tokens, cost, output link, error, instruction version |
| `model_changes` | Who changed which model or switch, and when |
| `agent_docs` | Versioned CLAUDE.md (instructions) and MEMORY.md (memory) per agent |
| `briefs` | Agent outputs (structured content + sources + prompt version), with `user_id` |
| `suggestions` | Suggested tasks or actions waiting for approve / reject, with `user_id`, source and quote |
| `source_status` | Each integration per user: status, last good sync, last error |
| `webhook_events` | Processed webhook ids |

Switch on row level security for every table and don't use the Supabase Data API or client library. Only the website's server code and the agent server connect, directly, over verified TLS. Every query that returns personal data filters by the signed-in user's id, checked on the server.

## How an agent runs
- **Scheduler.** At start-up and every five minutes, read `workflows`; keep one `croner` timer per enabled agent, in the owner's time zone. Schedule changes take effect within five minutes, no restart.
- **Runner.** Every run (schedule, Run now, webhook) goes through one function: read the agent's row (skip if off, unless run by hand) → insert a `workflow_runs` row as `running` → call the handler → record status, model used, tokens, cost, output link, error.
- **Per-person agents** (`per_user = true`, for example the morning brief in team mode): the runner loops over active users who connected the needed source, one run row each.
- **Quiet skips.** A scheduled check with nothing to do deletes its own run row.
- **Start-up cleanup.** Mark any run left as `running` from before a restart as `failed`.

## Prompts: instructions, memory, locked part
At every run, build the system prompt fresh: (1) the agent's CLAUDE.md (edited version or the built-in one), (2) its MEMORY.md, (3) a **locked part** people can't edit: output format (JSON schema) and safety rules. Record which versions each run used.

## The agent design rules (enforce in code)
1. **AI suggests, people decide.** No send, no calendar change, no publish without a click. Add a test that fails if any code calls an email "send" endpoint.
2. **Every claim cites its source.** Number the material given to the model (`S1`, `S2`…). The model cites ids; the code checks them against what it was shown and drops anything uncited.
3. **People by email address, never first name.** If a person can't be tied to an email, mark them "unverified". Keep the owner's addresses and company domains in config.
4. **Re-read live data at every run.** Never cache a calendar.
5. **Show each source and its last sync.** A source failing during a run shows as a warning in the output and in Settings.

## Models
- Build the model picker from OpenRouter's public model list, filtered to providers with **zero data retention** and, for JSON agents, **structured output**. On every call send OpenRouter's privacy options (no data collection, ZDR). Check OpenRouter's current docs for the exact parameter names.
- Mid-range model for most writing (for example the morning brief), a top model as backup, small cheap models for narrow jobs (sorting a task into a category). Some agents need no model at all (counts, calendar flags): use plain code.
- Set the thinking/effort level and max output tokens explicitly. Treat "cut off at the length limit" as a clear error.
