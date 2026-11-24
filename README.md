# AI OS Installer

Build your own AI operating system: a private website where your AI agents live in the cloud, run 24/7, and suggest work for you (or your team) to approve. You open it on your laptop or your phone.

Everything happens in the cloud. Nothing to download, nothing to install on your computer. Claude does the coding; you answer a few questions, create a handful of accounts, and paste keys into the dashboards Claude points you to.

## What you need
- A **Claude Pro, Max or Team plan** (cloud sessions of Claude Code are included).
- A free **GitHub** account.
- A **credit card** for two pay-as-you-go accounts (Render and OpenRouter). Expect about $10 a month to run it, plus your AI usage. Check current prices: they change.
- If you'll use a work Google account or company data, **check with your IT team first**.

## Step 1: Make your own copy

> ⚠️ **Make your copy Private.** GitHub sets new copies to **Public** by default. A public copy lets anyone on the internet read your plan, your settings and your code. Switch it to **Private** before you click Create.

1. Sign in to GitHub (or sign up free at github.com).
2. On this page, click **Use this template** → **Create a new repository**.
3. Name it, for example `my-ai-os`.
4. Under visibility, change **Public** to **Private**. Check it says Private.
5. Click **Create repository**.

You now have your own private copy. Everything Claude builds is saved there. (Made it public by mistake? On your copy: **Settings** → scroll to **Danger Zone** → **Change visibility** → **Make private**.)

## Step 2: Connect Claude to GitHub
1. Go to **claude.ai/code** (or, in the Claude desktop app, open the **Code** tab).
2. If asked, click **Sign in with GitHub** and approve.
3. Install the **Claude GitHub App** on your account and give it access to your new repository: github.com/apps/claude/installations/new. (Claude needs it to work on a private repository.)

## Step 3: Start
1. At **claude.ai/code** (or the **Code** tab in the Claude desktop app), click **Repo** under the message box and choose your `my-ai-os` repository.
2. Set the permission mode to **Auto**: under the message box, click the control that shows the mode (it reads **Accept edits**, **Plan** or **Auto**) and choose **Auto**. If it already says Auto, leave it. Claude then asks you only before risky steps.
3. Type: **set up my AI OS**

You don't need to set anything else up: Claude works on its standard cloud computer.

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
Go to claude.ai/code (or the Code tab), choose your repository, start a new session and type: **where are we?** It works from any computer, or your phone.

AI OS Installer v1.3 · AI Agent Academy 4
