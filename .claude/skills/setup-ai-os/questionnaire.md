# Questionnaire

Ask one at a time. Show the options and the recommended one. Record answers in `plan/answers.md` under the question's number.

## 1. What should we call it?
A short name for the website, for example "Acme OS" or "Jane OS". Default: "<first name> OS".

## 2. Just you, or your team?
- **Just me.** Only you can sign in.
- **My team.** Several people sign in. Agents are shared, and each person gets their own Today page. One or more admins manage agents and members.

If "Just me", skip 4 and 5.

## 3. Your email addresses
"Which email address(es) will you sign in with? And which email domains belong to your company (for example acme.com)?"
In Just-me mode, only these addresses can sign in. In every mode, agents use them to tell you apart from colleagues and outside contacts.

## 4. Team only: who can join?
- **Anyone at my company's email domain** (for example everyone @acme.com). Recommended if you use Google Workspace or Microsoft 365 with one domain.
- **Only people I invite.** An admin adds email addresses; nobody else can create an account. Recommended if your team uses different email domains.
- **Anyone can sign up, an admin approves.** New sign-ups wait on a "pending" screen until an admin approves them.

Record the domain if they pick the first.

## 5. Team only: who are the admins?
Their email addresses. Admins can add and remove members, switch agents on or off, change models and see costs. Members see their own Today page, run agents, and approve or reject suggestions meant for them. The first admin is the person running setup.

## 6. How do people sign in?
- **Google.** One click. Needs a Google Cloud project (free). Recommended if everyone has Google accounts.
- **Email and password.** Works for anyone. Needs an email sender for verification and password-reset emails: Resend (free plan at small volume). One more account. **For a team it also needs a web domain you own, and access to its DNS settings** (or someone in IT who can add three records). Without that, Resend only sends to your own address, so teammates never get their verification email. If you don't have that access, choose Google.
- **Both.**

## 7. Calendar
"Do you want your Today page to show your calendar?"
- **Yes, Google Calendar.** Each person connects their own Google Calendar, read-only. (If they sign in with email and password, they can still connect Google Calendar from Settings.)
- **Not now.** Today shows tasks and agent suggestions only. Calendar can be added later.

(Outlook calendars are not in version 1. Say so if asked, and note it in the PRD as a later add-on.)

## 8. Google account type
Ask only if 6 includes Google or 7 is Google Calendar.
"Is your Google account a Google Workspace account (your own company domain), or a personal Gmail?"
- **Workspace.** The Google sign-in screen can be set to "Internal": only your domain can sign in, no Google review, no warning screen. If the team includes people outside your domain, it must be "External" instead.
- **Gmail.** The sign-in screen must be "External" and published "In production". Users will see an "unverified app" warning once, which is expected for a private app.

## 9. Which tool records your calls?
Options: Granola, Fireflies, Otter, Fathom, Zoom (cloud recording), Google Meet (Workspace recording/transcripts), another tool, or none for now.
Then follow `reference/call-recorders.md`: check the tool's current docs for an API or webhooks. If it has neither (or their plan doesn't include it), propose the fallback: transcripts emailed to a dedicated address or saved to a Google Drive folder, imported daily.
In team mode, version 1 connects call transcripts for the admin running setup only. Per-person connections are an add-on.

## 10. Time zone
For example Europe/London. Schedules run in this time zone. Ask them: the cloud session itself runs in UTC.

## 11. Monthly AI budget
A hard cap you'll set in OpenRouter, for example $20. The morning brief alone costs about $1–3 a month on a mid-range model. The Agents tab shows spend against this cap.

## 12. Your first agent
Recommended: **Morning brief.** Every weekday at a time you choose (default 06:30), it reads today's calendar, your tasks, and recent call notes with today's people, and writes a short brief on your Today page, every line citing its source. In team mode it runs once per person who connected a calendar.
Other options: weekly review (Sunday evening), meeting action items (after each recorded call), or describe your own.
Ask the time it should run.

## 13. Task categories
Up to eight, for example Work, Clients, Admin & Money, Family, Health. In team mode, these are shared.

## 14. What agents must never do alone
Defaults (keep all of them): send any message or email, change a calendar, delete anything, spend money, publish anything. Ask what they'd add.
