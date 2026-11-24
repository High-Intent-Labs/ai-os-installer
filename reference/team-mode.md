# Sign-in, access and team mode

Use Better Auth for everything. Check its current docs before writing code: option names change between versions.

## Sign-in methods
- **Google:** Better Auth's Google provider. Ask for offline access so a refresh token is stored (the agent server renews access tokens itself to read calendars at night). Store tokens only in the database (`account` table).
- **Email and password:** Better Auth's email and password option, with **email verification required** and **password reset**, both sending through Resend. Minimum password length 12. Rate-limit sign-in attempts with `rateLimit.storage: "database"` (memory storage doesn't work on Vercel, where each request can run on a new server). Better Auth hides whether an email exists, so a refused sign-up looks like a success: after sign-up always show "If this email is allowed, you'll get a verification link."
- **Access checks:** put them in Better Auth's `user.validateUserInfo` hook (runs on sign-up, account linking and Google sign-in), and block removed people in `databaseHooks.session.create.before`. Check the current docs for the names.
- **Both:** enable both. A person who signed up with email and password can still connect Google Calendar later from Settings (Better Auth account linking); require the Google email to be verified.

## Just me
- `ALLOWED_EMAILS` = the owner's email(s) from question 3. Refuse every other sign-in and sign-up, server side.
- If Workspace and Google sign-in: also check the hosted-domain claim.

## Team: access rules (from question 4)
Enforce in a server-side hook that runs on every sign-up and every sign-in (not just in the page):
- **Company domain:** allow only emails ending `@<domain>` (`ALLOWED_DOMAINS`, comma-separated). For Google, also check the hosted-domain claim.
- **Invited only:** allow only emails in `invites` that are not expired or revoked. Admins add invites in Settings; the app emails an invite link (Resend) or the admin shares the link.
- **Sign-up with approval:** anyone can create an account, but `user_profile.status = 'pending'` until an admin approves. Pending people see one "waiting for approval" screen and nothing else. Admins see pending people in Settings and get a suggestion card on Today.

Check the session again in **every page and every server action**. The edge request filter is only a first gate.

## Roles
| | Admin | Member |
|---|---|---|
| Own Today page, tasks, suggestions | ✓ | ✓ |
| Run an agent now | ✓ | ✓ |
| Switch agents on/off, change models, edit instructions and memory | ✓ | |
| See costs and the budget | ✓ | |
| Approve members, invite, remove, change roles | ✓ | |

The person who runs setup is the first admin. Keep at least one admin at all times (refuse to demote or remove the last one).

## Shared agents, personal Today pages
- Agents (`workflows`) are shared: one definition, one schedule, one set of instructions.
- Agents marked `per_user` run once per active person who connected the source they need (for example the morning brief per person with a calendar). Each output has a `user_id` and appears only on that person's Today page.
- Tasks, suggestions, briefs and source connections belong to a person (`user_id`). Categories are shared.
- A person's calendar and notes are never shown to another person, including admins. Admins see run status and costs, not content.
- Version 1: call transcripts connect for the admin who ran setup only. Per-person recorders come after version 1.

## Removing someone
Set status `removed`, revoke their sessions, stop their per-person runs, keep their data until an admin chooses to delete it (ask for an explicit yes).
