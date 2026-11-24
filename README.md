# AI OS Installer

Build your own AI operating system: a private website where your AI agents live in the cloud, run 24/7, and suggest work for you (or your team) to approve. You open it on your laptop or your phone.

Everything happens in the cloud. Nothing to download, nothing to install on your computer. Claude does the coding; you answer a few questions, create a handful of accounts, and paste keys into the dashboards Claude points you to.

## What you need
- A **Claude Pro, Max or Team plan** (cloud sessions of Claude Code are included).
- A free **GitHub** account.
- A **credit card** for two pay-as-you-go accounts (Render and OpenRouter). Expect about $10 a month to run it, plus your AI usage. Check current prices: they change.
- If you'll use a work Google account or company data, **check with your IT team first**.

## Step 1: Make your own copy
1. Sign in to GitHub (or sign up free at github.com).
2. On this page, click **Use this template** → **Create a new repository**.
3. Name it, for example `my-ai-os`. Choose **Private**. Click **Create repository**.

You now have your own private copy. Everything Claude builds is saved there.

## Step 2: Connect Claude to GitHub
1. Go to **claude.ai/code** (or, in the Claude desktop app, open the **Code** tab).
2. If asked, click **Sign in with GitHub** and approve.
3. Install the **Claude GitHub App** on your account and give it access to your new repository: github.com/apps/claude/installations/new. (Claude needs it to work on a private repository.)

## Step 3: Set up your cloud environment (once)
The environment is the cloud computer Claude works on. You decide which websites it can reach.
1. At claude.ai/code, open the **environment selector** under the message box. At the bottom, click **New environment**. (In the desktop app: the first control under the prompt box, then **Cloud**.)
2. Name it `AI OS`.
3. **Network access:** choose **Limited**. Keep **Allow package managers** ticked. Paste this list into **Allowed domains**:
   ```
   supabase.com
   *.supabase.com
   *.supabase.co
   *.vercel.app
   *.onrender.com
   openrouter.ai
   better-auth.com
   *.better-auth.com
   vercel.com
   render.com
   resend.com
   developers.google.com
   support.google.com
   ```
   (These let Claude read each tool's documentation and check your live site. It can always reach GitHub. If you use a call recorder such as Granola or Fireflies, add its website too, for example `granola.ai` and `*.granola.ai`.)
4. Leave **environment variables** empty. Your keys never go here: they go straight into the Vercel and Render dashboards.
5. Click **Add environment**.

## Step 4: Start
1. At claude.ai/code (or the desktop app's Code tab with your **AI OS** cloud environment selected), choose your `my-ai-os` repository in the **repository selector** under the message box.
2. Set the mode to **Auto** (the mode dropdown under the message box). Claude then asks you only before risky steps.
3. Type: **set up my AI OS**

Claude will:
1. **Phase 1, the plan:** ask you 14 short questions and write your plan, your choices and the list of accounts you need.
2. **Phase 2, the app:** your website with sign-in and a Today page for each person.
3. **Phase 3, the agent server:** your first agent, running 24/7.
4. **Phase 4, the Agents tab:** run, pause and approve.

It stops every time you need to do something yourself.

## Three rules
1. **Never paste a secret key, password or API key into the chat.** Claude tells you which dashboard it goes in, and you paste it there yourself.
2. **Nothing goes live without your OK.** Claude saves each piece of work as a pull request. You check the preview link, then click **Merge** on GitHub to publish it.
3. **Read every permission request before you approve it.** In Auto mode there are fewer of them, so each one matters.

## Coming back later
Go to claude.ai/code (or the Code tab), choose the **AI OS** environment and your repository, start a new session and type: **where are we?** It works from any computer, or your phone.

AI OS Installer v1.2 · AI Agent Academy 4
