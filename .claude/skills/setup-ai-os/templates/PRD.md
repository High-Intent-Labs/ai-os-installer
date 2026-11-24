# <OS name>: product spec (version 1)

_Written <date>. Update the decision log at the bottom when anything changes._

## What it is
A private website where <name / the team at company> see their day and their AI agents in one place. Agents run in the cloud 24/7 and suggest work; people approve or reject.

## Who signs in
- Mode: <just me | team>
- Access: <only <email> | anyone @<domain> | invited emails only | sign-up with admin approval>
- Sign-in: <Google | email and password | both>
- Admins: <emails> (team mode)

## Version 1 screens
| Screen | What it does |
|---|---|
| Sign in | <Google button and/or email + password, verification, reset> |
| Today | My calendar for today and tomorrow <if calendar>, my Top 3, my tasks due, and agent suggestions to approve or reject |
| Tasks | My tasks by category: <categories>. Quick add. |
| Agents | Every agent as a card: job, schedule, model and backup, on/off, Run now, last run, this month's cost against the <$budget> cap. Profile page with run history, instructions (CLAUDE.md) and memory (MEMORY.md). |
| Settings | Every connected source with status and last sync. <Team: members, invites, pending approvals, roles.> |

Installable on phone home screens.

## First agent
<name>, <schedule> (<time zone>). Reads: <sources>. Writes: <output>. Model via OpenRouter with a backup model.

## Sources
- Calendar: <Google Calendar, per person, read-only | none>
- Call transcripts: <tool> via <API / webhook / daily import> (<admin only in v1>)

## Rules for every agent
- AI suggests, people decide. Agents never: <list from question 14>.
- Every claim cites a numbered source; uncited lines are dropped.
- People are identified by email address, never first name.
- A failed source shows a warning in the output and in Settings. Nothing fails silently.

## Stack
GitHub (private repo) · Vercel (website) · Supabase Postgres (database) · Render (agent server) · OpenRouter (AI) · Better Auth (sign-in) · <Google Cloud> · <Resend> · <call recorder>

## Budget
Hosting about $10 a month (check current prices). AI capped at <$budget> a month in OpenRouter.

## Not in version 1
Outlook calendar, per-person call recorders, Gmail drafts, business dashboard, long-running agents, custom domain. See reference/phases.md → "After version 1".

## Decision log
- <date>: plan written from the setup questionnaire.
