---
name: setup-ai-os
description: Sets up and builds the person's own AI operating system (private website + always-on agents) by running a short questionnaire, writing the plan, then building in phases with a stop at every human step. Use when the person says "set up my AI OS", "start setup", "install", or when CLAUDE.md says "Setup status: NOT SET UP".
---

# Set up my AI OS

You are building an AI operating system for a busy business owner who is not a developer. Be warm, brief and plain-spoken. They have finished a 4-week AI agents course, so they know CLAUDE.md, MEMORY.md, skills, agents and scheduled tasks. They have never deployed a website or used a database.

You run in a Claude Code cloud session on their private copy of this repository. Keep progress in `plan/MEMORY.md` from the first answer onwards, and commit it, so a later session on any device can resume.

## Step 0: Welcome and checks
Explain in four lines: what they'll get (a private website with their agents on one screen, running 24/7 in the cloud, usable on their phone), the four phases (1 the plan, 2 the app, 3 the agent server and first agent, 4 the Agents tab and approvals), and that they can stop any time and type "where are we?" in a new session.

If they're on a Team or Enterprise plan, ask whether they're an Owner of their Claude organization (it changes one form in Phase 3).

Then check, one at a time:
1. **Their own copy.** Run `git remote -v`. If it points to the original template repository rather than their own, stop: ask them to click **Use this template** on the template page, create a **private** repository, and start a new session on that one. Ask them to confirm their copy is **Private**.
2. **Network.** Try reaching a docs site they'll need (for example `curl -sI https://openrouter.ai`). If it's blocked, give them the steps in `README.md` → Step 3 (Network access **Limited**, the list in **Allowed domains**), and wait. Changes apply to new sessions, so they start a new session afterwards.
3. **Work data.** Ask: "Will this use a work Google account or company data? If so, have you checked with your IT team?" Continue only on a yes or "it's my own".

## Step 1: The questionnaire (Phase 1)
Ask **one question at a time**, using `questionnaire.md` in this skill's folder. Offer the options and the recommended default. Write each answer to `plan/answers.md` as soon as it is given. Skip questions the earlier answers make irrelevant (the file says which).

When all answers are in, show a one-screen summary and ask: "Anything to change?"

## Step 2: Write the plan (Phase 1, no code)
1. Fill `templates/PRD.md` → `plan/PRD.md`, using their answers. Keep it to two pages.
2. Fill `templates/accounts-checklist.md` → `plan/accounts-checklist.md`, keeping only the accounts their answers need (see `reference/accounts.md`).
3. Fill `templates/project-MEMORY.md` → `plan/MEMORY.md`. The handoff section says: "Plan written. Next: Phase 2."
4. Rewrite `CLAUDE.md` from `templates/project-CLAUDE.md`: fill in their OS name, mode, sign-in method, time zone and rules. Keep the working rules exactly. Change the status line to "Setup status: PLAN WRITTEN".
5. Show each file before saving it, and save after a yes.
6. Commit the plan straight to `main` with their OK (it changes no code, and nothing is deployed yet). Tell them it's now saved in their GitHub repository.

Tell them what happens next: "Phase 2 builds the website with sign-in and your Today page. You'll create 3 or 4 accounts along the way."

## Step 3: Build, phase by phase (Phases 2 to 4)
Follow `reference/phases.md`. For each phase:
1. Say what this phase delivers, in two lines, and which accounts or keys it will need from them.
2. Work on a branch named for the phase (for example `phase-2-app`). Build it using the stack in `reference/architecture.md`. Check current vendor docs before writing code against any third-party API.
3. At each human step, stop and give numbered, click-by-click instructions (use `reference/accounts.md`). Wait for "done".
4. Run typecheck, lint and tests in the session. Push the branch and open a pull request (the **Create PR** button at the top of the diff view, or Claude's built-in GitHub tools; `gh pr create` does not work in cloud sessions). Once Vercel is connected, it posts a preview link on the pull request: ask them to open it and tell you what they see, or send a screenshot.
5. When they're happy, ask them to click **Merge pull request** on GitHub. That publishes it. Then run the phase's live checks.
6. Update `plan/MEMORY.md` and the status line in `CLAUDE.md` ("PHASE 2 DONE", and so on), and commit.
7. Suggest they start a new session for the next phase ("type: where are we?"), to keep sessions short and cheap.

Version 1 is done after Phase 4. Then show `reference/phases.md` → "After version 1" so they can pick the next agent or feature.

## Rules for this skill
- Secrets never enter this session (see CLAUDE.md, rule 1).
- Never invent a price, plan name or menu path. If you aren't sure it's current, say "check this on their pricing page".
- If they get tired, stop cleanly: update and commit `plan/MEMORY.md` with exactly where you are, and tell them to type "where are we?" next time.
- If something fails in production, ask them to open the logs (Vercel → project → Logs; Render → service → Logs) and paste the error lines (never a line containing a key) before changing code.
