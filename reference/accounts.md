# Accounts: what to create and how

Walk the owner through each one only when the phase needs it. Menus and plan names change: if a step doesn't match what they see, check the vendor's docs and adapt. Never ask them to paste a key into chat. Keys go straight into the Vercel and Render dashboards; suggest they keep a copy in a password manager.

## GitHub (free): stores the code
Done before setup starts (see `README.md`): their private copy of the template, and the Claude GitHub App installed with access to it.
- Settings → Emails: note the "noreply" address (`<id>+<username>@users.noreply.github.com`). Claude sets it as the commit email in each session; Vercel can refuse deploys whose commit email it can't match to a GitHub account.

## Supabase (free): the database
1. Sign up at supabase.com. Check which organisation the new project lands in: a paid organisation adds a monthly charge.
2. New project, region near them, strong database password (save it in a password manager).
3. Connect → **Transaction pooler** → switch on **Use IPv4 connection**. Copy the connection string (port 6543), with the password filled in, into their password manager. It goes into Vercel and Render as `DATABASE_URL`.
4. Don't use the Supabase client library or Data API. The code connects directly with `pg` over verified TLS, using Supabase's public root certificate built into the code.

## Vercel (free Hobby plan to start): hosts the website
1. Sign up at vercel.com **with GitHub**.
2. Add New → Project → import the private repo.
3. Settings → Functions: choose a region close to the Supabase region.
4. Settings → Environment Variables: paste each value `.env.example` lists for the website, ticking **Production** and **Preview**. Single-line values only: Claude keeps multi-line values (certificates) in code.
5. Deploy. Every push to `main` deploys again.

## Google Cloud (free): Google sign-in and calendar
Only if they chose Google sign-in or Google Calendar.
1. Go to console.cloud.google.com **signed in as the account they'll log in with**. Create a project.
2. APIs & Services → Library: enable **Google Calendar API** (if calendar).
3. Google Auth Platform (or "OAuth consent screen"): app name, support email.
   - Workspace, and everyone who signs in is on the same domain: Audience **Internal**.
   - Personal Gmail, or people from several domains: Audience **External**, then **Publish app** to "In production". In "Testing", Google refresh tokens expire after 7 days and agents lose calendar access every week. An "unverified app" warning on sign-in is expected for a private app; for more than 100 users or sensitive scopes, Google may require verification (check current rules).
4. Scopes: `openid`, `email`, `profile`; plus `calendar.readonly` if calendar. Nothing more in version 1.
5. Clients → Create client → **Web application**. Authorised redirect URIs:
   - `https://<their-app>.vercel.app/api/auth/callback/google` (add once Vercel gives the address)
6. Copy the client ID and secret into their password manager (they go into Vercel and Render). Delete any downloaded JSON file afterwards.
7. Google sign-in only works on addresses listed here, so it won't work on Vercel preview links. Check it on the production address.
8. When a scope is added later, people sign out and in again.

## Resend (free plan at small volume): sign-in emails
Only for email and password sign-in.
1. Sign up at resend.com.
2. Domains → add a domain they own (or a subdomain like `mail.acme.com`), and add the DNS records it shows at their domain registrar. Wait for "Verified". Without a domain, Resend only sends to the account owner's own address, which is fine for testing but not for a team.
3. API Keys → create a key with "Sending access" only, into Vercel as `RESEND_API_KEY`. Sender address as `EMAIL_FROM`.

## Render (Starter instance, paid): the always-on agent server
1. Sign up at render.com **with GitHub**, add a payment method.
2. Claude writes `render.yaml`. In Render: New → **Blueprint** → choose the repo. Render reads the file and asks for each secret once.
3. Use the paid Starter instance or higher: free instances sleep and miss schedules.
4. Copy the service's address (`https://<name>.onrender.com`) into Vercel as `AGENT_URL`, then redeploy Vercel.

## OpenRouter (pay as you go): the AI
1. Sign up at openrouter.ai, add credits (OpenRouter charges a small fee on credit purchases; check current pricing).
2. Settings → Privacy: allow only providers that don't train on or retain data (zero data retention where offered). The code enforces this on every call too.
3. Keys → create a key with a **credit limit** equal to their monthly budget. Paste it into Render only (`OPENROUTER_API_KEY`). Never into Vercel or the browser.

## Claude cloud environment: network secret for the agent server
So Claude can test agents with "Run now" without ever seeing the secret:
1. At claude.ai/code, open the environment selector under the message box, hover **AI OS** and click the gear icon.
2. In **Network secrets**, click **Add secret**.
3. Name: `AGENT_SECRET`. Allowed websites: `<their-service>.onrender.com`. Value: the same `AGENT_SECRET` as on Render. Type: **Bearer** (sent as the Authorization header).
4. Click **Add**, then save the environment. The value can't be viewed again; to change it, delete and re-add.

## Call recorder
See `call-recorders.md`.
