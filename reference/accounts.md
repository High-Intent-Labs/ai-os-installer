# Accounts: what to create and how

Walk the owner through each one only when the phase needs it. Menus and plan names change: if a step doesn't match what they see, check the vendor's docs and adapt. Never ask them to paste a key into chat. Keys go straight into the Vercel and Render dashboards; suggest they keep a copy in a password manager.

## GitHub (free): stores the code
Done before setup starts (see `README.md`): their private copy of the template, and the Claude GitHub App installed with access to it.
- Settings → Emails: note the "noreply" address (`<id>+<username>@users.noreply.github.com`). Claude sets it as the commit email in each session; Vercel can refuse deploys whose commit email it can't match to a GitHub account.

## Supabase (free): the database
1. Sign up at supabase.com. Check which organisation the new project lands in: a paid organisation adds a monthly charge.
2. New project, region near them, strong database password (save it in a password manager).
3. Click **Connect** at the top of the project → **Transaction pooler** (shared pooler, port 6543; it works over IPv4 on the free plan). Copy the connection string, replace `[YOUR-PASSWORD]` with the database password, and keep it in their password manager. The username looks like `postgres.<project-ref>`. It goes into Vercel and Render as `DATABASE_URL`. (Forgot the password? **Reset database password** is in the same dialog.)
4. Don't use the Supabase client library or Data API. The code connects directly with `pg` over verified TLS, using Supabase's public root certificate built into the code. Claude gets the certificate from https://supabase-downloads.s3-ap-southeast-1.amazonaws.com/prod/ssl/prod-ca-2021.crt (the same file as Database settings → **Download certificate**). Remove any `sslmode=` from the connection string in code, so the verified settings are the ones used.

## Vercel (free Hobby plan to start): hosts the website
1. Sign up at vercel.com **with GitHub**.
2. Add New → Project → import the private repo. Before clicking **Deploy**, open **Environment Variables** and paste each value `.env.example` lists for the website (they apply to Production and Preview). Single-line values only: Claude keeps multi-line values (certificates) in code.
3. `BETTER_AUTH_SECRET` (the sign-in secret): create a random password of 40 or more characters in their password manager and paste it here. Never in chat. Keep a copy.
4. Click **Deploy**. **The first deploy fails, and that's expected:** `main` holds only the plan until the website's pull request is merged. Ask them to tell Claude "Vercel is connected". Claude then pushes to the pull request's branch, and Vercel posts a preview link on the pull request.
5. Settings → Functions: choose a region close to the Supabase region.
6. Every push to `main` deploys again.

## Google Cloud (free): Google sign-in and calendar
Only if they chose Google sign-in or Google Calendar.
1. Go to console.cloud.google.com **signed in as the account they'll log in with**. Create a project.
2. APIs & Services → Library: enable **Google Calendar API** (if calendar).
3. Google Auth Platform → **Branding**: app name, support email. (Older screens call this the "OAuth consent screen".)
4. **Audience**:
   - Workspace, and everyone who signs in is on the same domain: **Internal**.
   - Personal Gmail, or people from several domains: **External**, then **Publish app** to "In production". In "Testing", Google refresh tokens expire after 7 days and agents lose calendar access every week. An "unverified app" warning on sign-in is expected for a private app; for more than 100 users or sensitive scopes, Google may require verification (check current rules).
5. **Data Access** (scopes): `openid`, `email`, `profile`; plus `calendar.readonly` if calendar. Nothing more in version 1.
6. **Clients** → Create client → **Web application**. Authorised redirect URIs:
   - `https://<their-app>.vercel.app/api/auth/callback/google` (add once Vercel gives the address)
7. Copy the client ID and secret into their password manager (they go into Vercel and Render). Delete any downloaded JSON file afterwards.
8. Google sign-in only works on addresses listed here, so it won't work on Vercel preview links. Check it on the production address.
9. When a scope is added later, people sign out and in again.

## Resend (free plan at small volume): sign-in emails
Only for email and password sign-in.
1. Sign up at resend.com.
2. Domains → add a domain they own (or a subdomain like `mail.acme.com`), and add the DNS records it shows at their domain registrar (or ask whoever manages their DNS). Continue once it shows "Verified". Without a domain, Resend only sends to the account owner's own address: fine for Just-me mode, not for a team.
3. API Keys → create a key with "Sending access" only, into Vercel as `RESEND_API_KEY`. Sender address as `EMAIL_FROM`.

## Render (smallest paid instance): the always-on agent server
1. Sign up at render.com **with GitHub**, add a payment method.
2. Claude writes `render.yaml`. In Render: **New** → **Blueprint** → **Connect** the repo → check the name and branch (`main`) → **Deploy Blueprint**. Render reads the file and asks for each secret once, only at this first creation; later changes go in the service's **Environment** tab.
3. Use the smallest paid instance (0.5 CPU, 512 MB) or higher, never `free`: free instances sleep and miss schedules. Claude checks the current `plan` value in render.com/docs/blueprint-spec (Render renamed its instance types; use `runtime: node`, not the older `env: node`).
4. Copy the service's address (`https://<name>.onrender.com`) into Vercel as `AGENT_URL`, then redeploy Vercel.

## OpenRouter (pay as you go): the AI
1. Sign up at openrouter.ai, add credits (OpenRouter charges a small fee on credit purchases; check current pricing).
2. Settings → **Privacy**: switch on zero data retention (ZDR) for every model group listed, and switch off any option that lets providers train on their data. The code enforces this on every call too.
3. **Keys** → Create key: set **Credit limit** to their monthly budget and the reset to **Monthly**. Paste the key into Render only (`OPENROUTER_API_KEY`). Never into Vercel or the browser.

## Claude cloud environment: network secret for the agent server
So Claude can reach the agent server and test agents with "Run now" without ever seeing the secret. They do this on the new-session screen (the environment menu disappears once a session has started):
1. At claude.ai/code, click the first control under the message box (the cloud icon): it opens the environment menu. Under **Cloud**, hover the environment they use (usually **Default**) and click the settings icon on its right.
2. Click the **Network secrets** row, then **Add secret**.
3. Keep **Secret type: Bearer**. **Allowed websites:** `<their-service>.onrender.com`. Under **Custom headers**, leave Header name `Authorization` and Prefix `Bearer`, and paste the same `AGENT_SECRET` as on Render into **Value**.
4. Click **Add**. It saves straight away. The value can't be viewed again; to change it, delete and re-add.
5. Team or Enterprise plan without the Owner role: the form is shorter. **Host:** `<their-service>.onrender.com` (no `*.`), **Secret type: Token**, paste the value into **Token**, click **Add secret**.
6. Start a new session so it picks the secret up. Claude can now reach `<their-service>.onrender.com`, even though the environment's network setting is unchanged.

## Call recorder
See `call-recorders.md`.
