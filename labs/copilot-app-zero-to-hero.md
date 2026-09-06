---
title: "Lab: GitHub Copilot App: Zero to Hero"
description: Build and ship a REST API using the GitHub Copilot app — parallel agent sessions, issue-to-PR lifecycle, canvases, orchestration, and automations.
sidebar_position: 2
---

# Lab: GitHub Copilot App: Zero to Hero

> **Duration:** ~2 hours self-paced · facilitator guide below for demo-led delivery | **Level:** Beginner → Intermediate | **Prerequisites:** Active [GitHub Copilot subscription](https://github.com/features/copilot/plans), [Node.js 22+](https://nodejs.org/), [Git](https://github.com/git-guides/install-git), and a **private or internal** GitHub repository

## Objective

The **GitHub Copilot app** is the agent-native desktop application for directing several AI agents at once. In this lab you'll **build, review, and ship a Task Manager REST API** with it.

You won't just chat with one agent. You'll run **parallel sessions on isolated worktrees**, move work through **Plan → Autopilot**, take an **issue to a merged pull request**, build a **canvas** you and the agent share, orchestrate child sessions, and set up an **automation** that runs when your laptop is closed.

:::note The Copilot app is evolving fast
Commands, panels, and availability change frequently. Type `/` in the prompt box to see what's available right now, or check the [official docs](https://docs.github.com/copilot/concepts/agents/github-copilot-app) if something looks different.

**Last verified:** 2026-09-06.
:::

## What You'll Build

A **Task Manager REST API** with:

- Express.js server with CRUD endpoints, Zod validation, and centralized error handling
- Jest test suite plus a GitHub Actions workflow, so pull requests get real CI
- Two features built **simultaneously** in parallel sessions on separate branches
- A feature taken from **GitHub issue to merged PR** inside the app
- A custom **canvas** — a shared UI surface you and the agent both drive
- An **automation** that triages the repo on a schedule

### Why the app, specifically?

Copilot is in your IDE, your terminal, on GitHub.com, and in this app. They overlap. The honest framing isn't "the app does things nothing else can" — it's that the app is the **dedicated control center** for running many local and cloud sessions and managing GitHub work, without assembling that yourself from an editor, a terminal, and six browser tabs.

| Capability | IDE | Copilot CLI | Copilot App |
|-----------|-----|-------------|-------------|
| Inline code completion | ✅ | ❌ | ❌ |
| Single agent session | ✅ | ✅ | ✅ |
| Built-in worktree creation + visual session switching | ⚠️ manual | ⚠️ manual | ✅ |
| Launch cloud agent work that outlives your client | ✅ | ✅ | ✅ |
| Unified control center for local **and** cloud sessions | ⚠️ partial | ❌ | ✅ |
| Issue → session → PR → merge in one surface | ⚠️ extensions | ⚠️ partial | ✅ |
| Custom bidirectional canvases | ❌ | ❌ | ✅ |
| Visual automation management | ❌ | ❌ | ✅ |

The app is built on Copilot CLI, so everything you know from the CLI still works.

## Facilitator Guide

Skip this section if you're working through the lab on your own.

This lab is written to the learner, so leading it is a matter of choosing a path through it rather than following a script. It works best as **show it → they try it → discuss**, which means the exercise order below is a spine, not a running order.

### Prep

1. Connect an **empty private repo** (README only) as a project.
2. **File 4–6 issues that read like a Task Manager backlog.** Exercise 7.4 asks the agent to populate a board from open issues, and an empty repo produces an empty board. Filing them before any code exists is fine — a backlog normally precedes the code — but it does mean the agent has nothing to read, so the prompt has to carry the spec. Start a throwaway session on the repo and paste this:

   ```text
   This repository will hold a Node.js REST API for a task manager, not yet written. Planned shape:

   - Express.js, entry point src/index.js, port 3000, Zod for validation
   - In-memory array as the data store, no database
   - CRUD at /tasks: GET /tasks, GET /tasks/:id, POST /tasks, PUT /tasks/:id, DELETE /tasks/:id
   - A task has: id (uuid), title (1-100 chars, required), description (optional, max 500),
     status (todo | in-progress | done, defaults to todo), createdAt
   - Centralized error handling, Jest + supertest, GitHub Actions running tests on every PR

   Create these six issues as a starting backlog. Do not write any code.

   1. Add pagination to GET /tasks (?page, ?limit)
   2. Add API key authentication to all write endpoints
   3. Add rate limiting
   4. Publish an OpenAPI 3.1 spec and serve interactive docs
   5. Add a Dockerfile and docker-compose for local development
   6. Replace the in-memory store with SQLite behind a repository interface

   For each issue write a real body, not a title restatement: the problem it solves, acceptance
   criteria as a checklist, the specific endpoints or files affected, and edge cases worth calling
   out (for pagination: invalid page numbers and out-of-range pages; for auth: which endpoints stay
   public; for rate limiting: what the limit applies per and what the 429 response looks like).
   When you reference files, use the planned layout above — routes live in src/routes/tasks.js and
   src/index.js is only the entry point. Do not invent a flatter structure.
   Keep each body under 200 words. Label them appropriately if labels exist.
   ```

   Two of these — pagination and rate limiting — are what Exercise 8's `/orchestrate` prompt builds, so the board stops being decorative. None of them duplicate what Exercises 3 and 4 build, so nobody ends up implementing an issue the lab already covers. If you'd rather they arrive in narrative order, file them right after Exercise 2 merges instead; anytime before Exercise 7 works.
3. Optional insurance, worth 15 minutes if you're demoing to a customer: run **Exercise 2** to green in a session you `/rename` to `scaffold-prepared` and **leave it unmerged**, and run **Exercise 7**'s canvas in `kanban-prepared`. If a live build runs long you switch to these and say so plainly. Nobody minds the cooking-show move; they mind watching a spinner.
4. Create and **run the Exercise 9 automation once**, so you have real output rather than an empty history.

### Opening move

Start **Exercise 2** and don't wait for it. Set it going in Plan mode, push back on the plan in front of the room — that's the highest-value thing a human still does — then approve and switch to Autopilot.

Now open a second session and start **Exercise 7**'s canvas alongside it. Two agents, two branches, two worktrees, neither blocked on you. **This is the whole argument, and you're making it by doing it rather than describing it.** In a small room the discussion this provokes is the session; you're not filling time, you're using it.

Come back when the scaffold is green, verify, and merge. Then follow the room.

### Where the conversation usually goes

| They ask about… | Go to |
|---|---|
| "Doesn't my IDE already do this?" | The comparison table above |
| Cost, credits, token burn | Exercise 10.4, and per-session models in Exercise 3.2 |
| "What keeps running when I close my laptop?" | Exercise 3.3 runtimes, then Exercise 9 |
| Policy, admin control, what users can install | Exercise 10.5 |
| Security review and code review | Exercise 5 |
| "How do I get my team onto this?" | Exercise 7's project scoping, Exercise 10.2 instruction layers |

### If you have a fixed 90 minutes

Demo **Exercises 2, 3, and 7** (about 30 minutes with discussion), then have attendees work **Exercises 1–6** themselves (about an hour). Leave **7–10** as self-paced follow-up. Exercises 8, 9, and 10 are the ones most worth *showing* rather than assigning, because they cost credits and mostly reward watching once.

## Setup {#setup}

### S.1 Install and Sign In

Download the app for macOS, Windows, or Linux from the [GitHub Copilot app page](https://github.com/features/ai/github-app), then sign in through the browser OAuth flow. For GitHub Enterprise Server, choose **Use GitHub Enterprise** and enter your server address.

:::note Business and Enterprise users
The **GitHub Copilot app policy** must be enabled for your organization. It's on by default, and it is *separate* from the Copilot CLI policy. If sign-in is refused, check that first.
:::

### S.2 Create the Lab Repository

Create a new GitHub repository named `task-manager-api`. Three settings matter:

| Setting | Value | Why |
|---------|-------|-----|
| **Visibility** | **Private** or **Internal** | Automations (Exercise 9) are **not available in public repos** |
| **Initialize with a README** | ✅ Yes | A repo with no commits has no default branch, which breaks worktrees and PR targeting |
| **Actions enabled** | ✅ Yes | Exercise 6 needs real check runs. Some orgs disable Actions on private repos by policy — confirm now, not in Exercise 6. |

:::warning Don't improvise these settings
A public repo blocks Exercise 9 entirely. An empty repo makes Exercises 3 and 6 fragile. Disabled Actions blocks half of Exercise 6. Fixing any of them later means recreating the repo.
:::

### S.3 Connect the Project

Click **+** in the sidebar next to **Sessions**. Under **Add project from**, you can choose a **local folder**, a **GitHub repository**, or any **repository URL** (Azure DevOps, GitLab, self-hosted). Choose **GitHub repository** and pick `task-manager-api`.

### S.4 Learn the Sidebar

Click through each area — every exercise below lives in one of them:

| Area | What's there |
|------|--------------|
| **My work** | Your issues and PRs, with CI status and reviews inline |
| **Sessions** | Active agent sessions grouped by project — your parallel-work control center |
| **Chats** | Conversations that *don't* create a branch or worktree |
| **Automations** | Agent tasks that run on a schedule, on an event, or on demand |
| **Customize** | Plugins, skills, MCP servers, and canvases |
| **Search** | Search across connected repositories |

### ✅ Checkpoint

The app is installed and authenticated, and a **private** repo with **Actions enabled** and a **README** is connected as a project.

## Exercise 1 — Chats vs. Sessions (~6 min)

A common mistake is starting a full session — creating a branch and a worktree — just to ask a question. **Chats** exist for that.

### 1.1 Think in a Chat

Click **Chats** and start a conversation. No branch, no worktree, no diff pane.

```text
I'm about to build a Task Manager REST API in Node.js with Express and Zod, storing tasks in memory.

Before I write any code, help me pin down:
1. The resource shape for a task (fields, types, constraints)
2. The full endpoint list including filtering and stats
3. Which validation and error-handling patterns to standardize up front
4. What the test strategy should be

Ask me clarifying questions. Don't write code yet.
```

Iterate until the design feels right. **This is cheap thinking** — you're clarifying requirements before an agent starts burning credits writing files.

### 1.2 Carry the Decision Into a Session

```text
Summarize our agreed design as a concise implementation brief I can paste into a coding session. Include the task schema, endpoint list, validation rules, and test expectations.
```

Copy that brief. You'll use it in Exercise 2.

### 1.3 Know Which to Reach For

| Use | Reach for |
|-----|-----------|
| "How should I model this?" | **Chat** |
| "Explain how this repo's auth works" | **Chat** |
| "Implement the thing we agreed on" | **Session** |
| "Fix this failing test" | **Session** |
| "Pick up issue #42" | **Session** started from the issue |

**Archive, don't delete.** Right-click a chat or session and choose **Archive**. **Settings → Sessions → Manage sessions** lets you search, bulk-archive, and see each session's disk usage.

### ✅ Checkpoint

You can explain the Chats/Sessions split and have an implementation brief ready to hand to an agent.

## Exercise 2 — Scaffold with Plan Mode (~15 min)

Now the mode ladder that defines app workflow: **Plan** to agree on the approach, then **Autopilot** to execute it.

### 2.1 The Three Session Modes

| Mode | Who drives | Use when |
|------|-----------|----------|
| **Interactive** | You and the agent together | Tight steering, unfamiliar or risky code |
| **Plan** | Agent proposes, **you approve before execution** | Scope is fuzzy or the change is large |
| **Autopilot** | Agent works autonomously | The task is well defined and verifiable |

Switch anytime with the dropdown or `/interactive`, `/plan`, `/autopilot`.

### 2.2 Enter Plan Mode

Create a session on a **new working tree**, `/rename` it to `scaffold`, type `/plan`, and paste your brief from Exercise 1. If you skipped it, use this:

```text
Create a Node.js REST API project for a task manager in this repository.

- Express.js server, entry point src/index.js, port 3000
- Zod for validation
- src/routes/tasks.js with full CRUD: GET /tasks, GET /tasks/:id, POST /tasks, PUT /tasks/:id, DELETE /tasks/:id
- src/middleware/errorHandler.js for centralized error handling with an AppError pattern
- In-memory array as the data store, no database
- A task has: id (uuid), title (string, 1-100 chars, required), description (optional, max 500), status (enum: todo | in-progress | done, defaults to todo), createdAt
- Jest + supertest, with tests for happy paths and validation failures
- A GitHub Actions workflow at .github/workflows/test.yml running npm ci and npm test on every pull request
- package.json scripts: start, dev, test
- A Node.js .gitignore

Plan the work. Do not execute yet.
```

:::note The CI workflow is required
`.github/workflows/test.yml` is what gives your pull requests real check runs. **Exercise 6 depends on it.** Don't let it get dropped from the plan.
:::

### 2.3 Actually Read the Plan

**This is the highest-leverage minute in the lab.** Is the file structure right? Are dependencies sensible? Is the workflow there? Push back before approving:

```text
Two changes before we execute:
1. Add request logging middleware that logs method, URL, status, and response time.
2. Add a GET /health endpoint returning status, uptime, and version from package.json.
Update the plan.
```

### 2.4 Approve and Run on Autopilot

Approve, and let the session continue in **Autopilot**. It creates the structure, installs dependencies, writes tests, then runs and fixes them until green. This takes several minutes — **don't sit and watch it.** Read ahead, or start Exercise 7 in a second session and come back.

Autopilot may still ask permission for some tools. `/allow-all-tools` (aliased `/yolo`) enables auto-approval — use it while you're watching. `/reset-allowed-tools` clears session approvals and turns auto-approval back off.

### 2.5 Verify the Scaffold

Click **Changes** above the prompt box to see the full diff, then:

```text
/terminal npm test
```

```text
/terminal npm start
```

```text
/terminal curl -s -X POST http://localhost:3000/tasks -H "Content-Type: application/json" -d '{"title":"Learn the Copilot app"}'
```

`/terminal` isn't a shell-out — it's a canvas in the side panel, your first taste of Exercise 7.

### 2.6 Merge the Scaffold to `main` {#merge-scaffold}

**Required before Exercise 3.** Your scaffold lives on this session's branch. Exercise 3 creates sessions that branch from `main`, which currently holds only a README.

```text
/pr-open
```

```text
/pr-merge
```

:::warning Confirm before continuing
This is the most common place to get stuck. Verify on GitHub that `main` now contains `src/routes/tasks.js` and `.github/workflows/test.yml`.
:::

### ✅ Checkpoint

A working, tested Express API is **merged into `main`** with a CI workflow, and you can switch modes and reset tool permissions.

## Exercise 3 — Three Agents at Once (~18 min)

Everything so far you could have done in the CLI or an IDE. This is where the app earns its place.

A single session is a queue: you ask, it works, you wait. Real work isn't a queue — you have a feature to build, a flaky test to chase, and a docs update a week overdue. The app gives every session its **own git worktree and branch**, so agents work in the same repo simultaneously without touching each other's files.

### 3.1 Launch Three Sessions

Create three sessions from **+**, each in a **new working tree**, each named with `/rename`.

**Session A — "filtering"** *(Interactive)*

```text
Add query-parameter filtering to GET /tasks:
- ?status=todo|in-progress|done
- ?q=<text> for case-insensitive search across title and description
- ?sort=createdAt|title and ?order=asc|desc
Validate query params with Zod and return 400 on invalid values. Add tests for each filter.
```

**Session B — "stats"** *(Autopilot)*

```text
Add GET /tasks/stats returning:
{ total, byStatus: { todo, inProgress, done }, oldest, newest, completionRate }
completionRate is done/total as a percentage rounded to one decimal, 0 when total is 0.
Register this route BEFORE the existing GET /tasks/:id route, otherwise /tasks/:id will match "stats" as an id.
Add tests including the empty-store edge case. Follow existing code conventions.
```

**Session C — "docs"** *(Interactive)*

```text
Generate a comprehensive README.md: description, setup, every endpoint with request/response examples and curl commands, error format, and how to run tests. Read the actual route files — don't invent endpoints.
```

If a session reports an empty repository, its base branch is wrong — you skipped [merging the scaffold](#merge-scaffold).

### 3.2 Work Them Concurrently

Click between the three. Each has its own branch, worktree, transcript, context window, diff, model, and reasoning effort. Session B keeps working while you steer Session A. **That's the point** — your attention becomes the scarce resource, not agent throughput.

**Match model to task.** Set Session C (docs) to a lighter, faster model and Session A (validation logic) to a higher-capability one. Per-session model selection is one of the quietest cost-saving features in the app.

### 3.3 Where a Session Runs

The dropdown under the prompt box offers three runtimes:

| Runtime | Executes on | Survives machine sleep? |
|---------|------------|------------------------|
| **New working tree** | Your machine, isolated worktree + branch | ❌ |
| **Local repository** | Your machine, existing checkout and branch | ❌ |
| **Cloud sandbox** (preview) | GitHub-hosted environment | ✅ |

Try it: start one more throwaway session on **Cloud sandbox**, prompt it to summarize the repo, then close your laptop lid for a minute. It's still going when you come back.

:::note Three things that sound alike
Customers conflate these constantly:

- **Cloud sandbox** — a *session runtime*; the session itself runs on GitHub.
- **Cloud agent** — asynchronous work in a GitHub Actions environment, launched from GitHub.com, an IDE, an issue, or an automation. **Not app-exclusive.**
- **Remote control** (`/remote`) — the session stays **on your machine**; GitHub.com only *steers* it. If your laptop sleeps, work stops.

Only the first two keep running when your machine is off.
:::

### 3.4 Land Them Independently

Each session opens its own pull request with `/pr-open`.

**The app isolates execution; Git still arbitrates integration.** Separate worktrees stop agents overwriting each other's files. They do *not* prevent merge conflicts — if filtering and stats both touch `src/routes/tasks.js`, the second PR may still need a rebase.

### 3.5 Clean Up

**Settings → Sessions → Manage sessions** shows disk usage per session. Every session is real disk space, and stale worktrees accumulate fast.

### ✅ Checkpoint

You ran three agents concurrently on isolated branches with different models and modes, and can explain cloud sandbox vs. cloud agent vs. remote control.

## Exercise 4 — From Issue to Session (~15 min)

Most real work starts with an issue, not a blank prompt. The app makes the issue the entry point.

### 4.1 Explore "My work"

Click **My work**. Issues and PRs appear grouped into **All**, **Active**, **Review requests**, and **Done**. Search inside a section with a qualifier like `label:bug` or `is:open author:@me`, then add your own section filtered to `review-requested:@me is:open` — that turns My work into a real triage dashboard, with CI status inline.

### 4.2 Have the Agent File an Issue

In any session:

```text
Create an issue in this repository proposing bulk operations for the task API:
- POST /tasks/bulk to create multiple tasks
- DELETE /tasks/bulk to delete by an array of ids
- PATCH /tasks/bulk/status to update status on multiple tasks
Include acceptance criteria, validation rules, and edge cases (partial failures, empty arrays, unknown ids).
```

The agent picks a repository issue template appropriate to the type. Name a specific template in your prompt to force one.

### 4.3 Start a Session From the Issue

Open the issue in **My work** and click **New session** — the session opens **with the issue context already loaded**. You paste nothing. Set mode to **Plan**:

```text
Implement this issue. Plan first, and call out anything in the acceptance criteria that's ambiguous or that you'd push back on.
```

### 4.4 Preserve the Reasoning

Review the plan against the acceptance criteria, refine, then approve and let it build. Once it's working:

```text
Attach your implementation plan to this issue as an artifact so reviewers can see the approach before they read the diff.
```

Small habit, outsized payoff: the *why* lives where stakeholders already look instead of evaporating with your session transcript.

### 4.5 Open the PR — and Leave It Open {#leave-pr-open}

```text
/pr-open
```

**Don't merge this one.** Exercise 6 uses it.

### ✅ Checkpoint

You filed an issue from an agent, launched a session from it with context preloaded, attached the plan back, and have a PR waiting.

## Exercise 5 — Break It, Then Catch It (~9 min)

The app ships several review agents. They aren't redundant — each looks for something different.

### 5.1 Seed a Real Bug

In the session with your bulk-operations changes:

```text
In src/routes/tasks.js, change the DELETE route to use findIndex but remove the check for -1 before calling splice. Make only that change and do not fix it.
```

Start the server, create a task, then delete one that doesn't exist:

```text
/terminal npm start
```

```text
/terminal curl -s -X POST http://localhost:3000/tasks -H "Content-Type: application/json" -d '{"title":"Task 1"}'
```

```text
/terminal curl -s -X DELETE http://localhost:3000/tasks/does-not-exist && curl -s http://localhost:3000/tasks
```

No error, no crash — but **a real task is gone**. When `findIndex` returns `-1`, `splice(-1, 1)` removes the *last* element. Exactly the class of bug that survives a casual eyeball review.

### 5.2 Catch It

```text
/review
```

`/review` reviews the **current session's changes** for bugs and logic errors, ignoring style noise.

:::note If `/review` misses it
Model output isn't deterministic. Narrow the ask: `/review Look specifically at the DELETE handler's index handling.` That's a useful lesson — targeted review prompts beat broad ones.
:::

Apply the fix.

### 5.3 The Other Reviewers

```text
/security-review
```

Public preview. Returns **prioritized findings with severity and confidence scores** plus suggested fixes. Requires an active session **with changes**. It's a *pre-PR* check that complements, not replaces, code scanning and Dependabot.

```text
/rubber-duck Critique my bulk operations implementation. Focus on partial-failure semantics.
```

`/rubber-duck` runs on a **different model than your session**, so you get independent judgment rather than a model agreeing with itself. It requires the main agent to be on a Claude or GPT model — switch with `/model` if unavailable.

```text
/spar We're storing tasks in memory and shipping bulk endpoints with no rate limiting. Argue why that's a mistake.
```

| Command | Looks for |
|---------|----------|
| `/review` | Bugs and logic errors in current changes |
| `/security-review` | Exploitable vulnerabilities, ranked by severity |
| `/rubber-duck` | Design critique from a *different* model |
| `/spar` | Adversarial challenge to your reasoning |

### ✅ Checkpoint

You caught a subtle bug before it reached a pull request, and know what each review agent is for.

## Exercise 6 — The Pull Request Lifecycle (~15 min)

Here the app collapses three tools into one: diff to merged without opening a browser.

:::warning Check this before you start
This exercise needs **GitHub Actions to actually run** on your repo. Confirm a workflow has completed at least once (the PR from Exercise 2 should have triggered one). If Actions is disabled for private repos in your org, `/pr-fix-checks` has nothing to work with — do 6.1's comment half and substitute `/review` for the checks half.

Also budget for queue time. A first Actions run on a fresh repo can take several minutes to *start*, which is outside anyone's control.
:::

### 6.1 Create Real Review and CI Context

`/pr-resolve-comments` and `/pr-fix-checks` are **context-gated** — they only appear when unresolved comments or failing checks actually exist. Create them deliberately.

**Make a check fail.** In the session holding your [open PR](#leave-pr-open):

```text
Add a test to the bulk operations test file asserting that POST /tasks/bulk rejects an empty array with a 400. Do not change the route implementation — I want this test to fail. Commit and push it.
```

Your workflow from Exercise 2 now runs and goes red.

**Leave unresolved comments.** Open the PR in **My work** → **Files changed** and leave two inline review comments, submitted as **Comment** (not Approve):

> This doesn't validate that the array is non-empty. What happens with `[]`?

> Partial failures aren't handled — if one id is unknown, does the whole request fail?

:::note Working solo is fine
You can leave review comments on your own PR. GitHub only stops you from **approving** it. That's all `/pr-resolve-comments` needs.
:::

### 6.2 Review the PR in the App

Click the PR in **My work**. You get the overview with **CI check results**, a **Files changed** diff, and a **New session** button scoped to that PR. Start one and ask:

```text
Review this pull request as a senior engineer. Focus on correctness and API contract consistency with the existing endpoints. Flag anything that would break existing clients.
```

Comments you draft in a session are **staged into your pending review** — nothing reaches GitHub until you submit with **Review**.

### 6.3 Resolve the Feedback

```text
/pr-resolve-comments
```

The agent's reply goes **into the review thread**, under the reviewer's comment — not as a disconnected top-level PR comment.

### 6.4 Fix the Failing CI

```text
/pr-fix-checks
```

The agent reads the failure output, diagnoses it, and pushes a fix — here, the empty-array validation your seeded test demanded.

### 6.5 Merge, or Let an Agent Do It

```text
/pr-merge
```

Better, try **agent merge** — enable it at the top of the app on this PR. It prompts a session to read the PR, fix what's blocking it (comments, checks, conflicts), and merge as soon as GitHub allows. It **runs in the background, survives app restarts, and switches itself off once merged.**

Turn it on, close the app, make coffee, and come back. The PR merged itself. There is no equivalent to this in the IDE or the CLI — it's the clearest example of the app behaving like infrastructure rather than a tool you operate.

### 6.6 The Full Loop

```mermaid
flowchart LR
    A[Issue in My work] --> B[Session with issue context]
    B --> C[Plan: agree approach]
    C --> D[Autopilot: build + test]
    D --> E["/review"]
    E --> F["/pr-open"]
    F --> G[Review in app + CI]
    G --> H["/pr-resolve-comments<br/>/pr-fix-checks"]
    H --> I[Agent merge]
```

### ✅ Checkpoint

You created real review and CI context, then opened, reviewed, revised, unblocked, and merged a PR without leaving the app — the last step without being present for it.

## Exercise 7 — Build a Canvas (~12 min)

If parallel sessions are the app's most *useful* feature, canvases are its most *distinctive*. Nothing else in the Copilot family has this.

### 7.1 What a Canvas Is

Chat is good for defining intent, but most real work happens in a **work surface**: a terminal, a document, a board. A canvas is that surface in the app's side panel, and it's **bidirectional** — the agent updates it while working, you edit it directly, and the agent continues *from your edits*.

You've already used two: `/terminal` opens a terminal canvas, and viewing a markdown artifact opens an editor canvas.

### 7.2 Browse What Exists

Go to **Customize → Canvas**, browse the featured list, then click **Installed**. Opening a prebuilt canvas takes seconds and shows the concept immediately; building one takes minutes. Do them in that order.

### 7.3 Create Your Own

In an active session:

```text
/create-canvas Create an agentic kanban board for this repository's tasks.

People should be able to:
- Add a card with a title, description, and status
- Move cards between columns (Todo, In Progress, Done)
- Filter cards by status and by text search

The agent should be able to call:
- get_board to read current state
- add_card to create a card
- move_card to change a card's status
- summarize_board to report progress

Persist the board state so it survives a restart. Scope it to the project.
```

Choose the scope when prompted:

| Scope | Location | Use for |
|-------|----------|---------|
| **Project** | `.github/extensions` | Team-shared, committed to the repo |
| **User** | `~/.copilot/extensions` | Personal, on your machine |

This takes several minutes — the agent generates extension files, installs dependencies, reloads, and renders the UI. **Start something else while it works.** This exercise pairs naturally with Exercise 2 or 3 running in another session.

### 7.4 Drive It Both Ways

**You drive:** add a few cards through the UI and move one to In Progress.

**The agent drives:**

```text
Read the board and add a card for every open issue in this repository, in the Todo column.
```

```text
Summarize the board: what's in flight, what's blocked, and what I should pick up next.
```

You're both manipulating the *same state*. You never described the board to the agent, and it never described the board back to you.

:::note No issues, no cards
If your repo has no open issues, that first prompt correctly does nothing. File a few first — Exercise 4 creates one, or seed several by hand.
:::

### 7.5 Iterate on the Canvas Itself

```text
Add a "Blocked" column, an agent-callable block_card action that takes a reason, and show the blocking reason on the card.
```

A project-scoped canvas is committed, so **your whole team gets it on their next pull**. Other things worth building: an issue triage board, a release checklist the agent ticks off, or an incident timeline.

### ✅ Checkpoint

You built a canvas from a prompt, drove it from both the UI and the agent, and know where it lives and how to share it.

## Exercise 8 — Orchestration (~8 min)

Exercise 3 ran sessions in parallel *by hand*. Orchestration lets an agent create and steer them for you.

Each command below creates sessions and consumes AI credits. Run **8.1**, then use the table in 8.3 as reference.

### 8.1 `/orchestrate` — Coordinate Child Sessions

```text
/orchestrate Split the remaining Task Manager work into independent workstreams and run them in parallel child sessions:
1. Add pagination (?page, ?limit) to GET /tasks with tests
2. Add rate-limiting middleware with tests
Each workstream should end with a pull request. Report back with the PR links.
```

Watch the **Sessions** sidebar — child sessions appear **nested under their creator**. Kick it off and move on; like everything else here, it doesn't need you watching. Come back at the end of the lab to find the PRs.

### 8.2 `/fork` — Branch Your Conversation

```text
/fork
```

Forks the session at the latest turn into a new worktree, **carrying your conversation history with it**. Your backlog has an issue asking for SQLite. Big commitment, unclear payoff — so spike it instead of debating it:

```text
Take the first step of the SQLite issue in our backlog: rewrite the in-memory store as a repository class with an interface that would let us swap in SQLite later, without changing the route handlers. Don't add SQLite yet.
```

Like it? `/merge-to-parent`. Don't? Archive the fork — your original was never touched.

Git branches files; `/fork` branches files **and** the agent's accumulated context. There's no real equivalent in IDE chat.

### 8.3 Choosing the Right Tool

| You want to… | Use |
|--------------|-----|
| Run several tasks as coordinated child sessions | `/orchestrate` |
| Hand off one side task | `/spawn` |
| Throw more agents at *one* big task | `/fleet` |
| Try an alternative without losing your current path | `/fork` |
| Ship a big change as reviewable layers | `/pr-stack` |

`/spawn` creates one focused child session; `/fleet` puts multiple agents on a **single** task and consolidates the result.

### ✅ Checkpoint

You've orchestrated child sessions, forked a conversation, and can articulate which primitive fits which shape of work.

## Exercise 9 — Automations (~10 min)

Sessions require you. **Automations don't.** This turns the app from a tool you use into infrastructure that runs.

### 9.1 Check the Prerequisites

| Requirement | Detail |
|-------------|--------|
| **Private or internal repo** | Automations are **not available in public repositories** |
| **Write access** | Any user with write access can create them |
| **Cloud agent enabled** | For Copilot Business/Enterprise, an **admin must enable the cloud agent policy** |
| **Plan** | Copilot Pro, Pro+, Max, Business, or Enterprise |

:::warning Two different policies, two different defaults
The **Copilot app policy** is enabled by default. The **cloud agent policy** for Business/Enterprise is **not** — an admin must turn it on. Don't assume the second because you observed the first.
:::

If you can't meet these, **read this exercise rather than building it** — the concepts still matter for customer conversations.

### 9.2 Local vs. Cloud

| | Local automation | Cloud automation |
|---|---|---|
| Runs on | Your machine | GitHub-hosted environment |
| Machine must be on | ✅ Yes | ❌ No |
| Custom CRON expressions | ✅ | ❌ fixed trigger types |
| Tool scoping | Session permissions | Explicit **Tools** allow-list |

### 9.3 Create a Daily Triage Automation

Go to **Automations → New automation**. Name it `Daily repo triage`, set the trigger to `Daily` at `08:30`, and enable **Run in the cloud**.

Under **Tools**, select only what the task needs — reading issues and updating labels, **not** pushing code. A **Suggest tools** button proposes tools based on your prompt, but review what it picks.

```text
Triage this repository and report:

1. New issues opened in the last 24 hours — summarize each in one line and suggest labels
2. Open pull requests that are blocked: failing checks, conflicts, or no review after 48 hours
3. Any issue with no activity for 14+ days that looks stale
4. One recommended priority for today, with a one-sentence justification

Keep it under 300 words. Lead with anything needing a decision from me.
```

Select your project, then use the dropdown next to **Create** → **Create and run** to test it immediately.

### 9.4 Trigger Types

| Trigger | Behavior |
|---------|----------|
| **Manual** | Runs only when you press play |
| **Hourly / Daily / Weekly** | Fixed schedules |
| **CRON** | Custom expression, **local only** |
| **Issue** | Issue created, with an optional search filter |
| **Pull request** | PR opened or new commits pushed |

**Add another trigger** fires the automation when *any* trigger occurs. Try an event-driven one:

```text
A new issue was just opened.
1. Classify it as bug, feature, question, or docs, and apply the matching label
2. Check whether it duplicates an existing open issue; if so, link it
3. If it's a bug report missing reproduction steps, environment, or expected vs actual behavior, post a polite comment asking for what's missing
4. Do not close anything and do not modify code
```

### 9.5 What a CSA Needs to Know

These matter more to an enterprise buyer than a second sample automation:

| Topic | Reality |
|-------|---------|
| **Visibility** | An automation is **private to its creator** — even repo admins can't see it. The sessions it starts *are* visible to anyone with repo access. |
| **Billing** | Each run consumes **Actions minutes and AI credits**, billed to the creator. |
| **Least privilege** | The **Tools** allow-list is the main control. Issue labeling shouldn't imply push access. |
| **Prompt injection** | Automations **ignore events from users without write access by default**, so an outside contributor can't drive your agent. |
| **Attribution** | PRs from an automation are attributed to its creator, who therefore **cannot approve them**. |
| **Not versioned** | Definitions live outside your repo — **not in Git**, not code-reviewed, not revertible. |
| **Secrets** | Never put secrets in a prompt; session logs are visible to collaborators. |

Automations inherit the repo's cloud agent configuration: custom instructions, skills, firewall rules, and secrets. If cloud runs fail on dependencies or credentials, ask the agent to set up `copilot-setup-steps.yml` for you.

### ✅ Checkpoint

You know the real prerequisites, the local/cloud split, and the visibility, billing, and security facts that come up in every enterprise conversation.

## Exercise 10 — Customize and Govern (~12 min)

The app inherits the entire Copilot CLI customization ecosystem. **The app-specific value is the visual management UI**, not the underlying mechanisms.

### 10.1 Everything Carries Over

MCP servers and skills already configured for your repos or for Copilot CLI are **automatically available**. Confirm under **Customize → Installed**.

Skills, custom agents, hooks, and MCP authoring are covered in the [Copilot Customization Workshop](/workshops/copilot-customization) and the [Copilot CLI lab](/labs/copilot-cli-zero-to-hero).

### 10.2 Instruction Layers

Beyond the standard repo files, the app adds two settings-based layers. Set a global one under **Settings → Sessions → App instructions**:

```text
Always explain the "why" behind a change, not just the "what".
Prefer small, reviewable diffs. If a change exceeds ~300 lines, propose splitting it.
Never commit secrets, and flag any credential-shaped string you encounter.
```

| Layer | Scope | Shared with team? |
|-------|-------|-------------------|
| App instructions (settings) | Every session, every project | ❌ |
| Project instructions (settings) | One repository | ❌ |
| `.github/copilot-instructions.md` | Repository-wide | ✅ committed |
| `.github/instructions/**/*.instructions.md` | Path-scoped via `applyTo` | ✅ committed |
| `AGENTS.md` | Agent-facing repo instructions | ✅ committed |

Generate the committed one with `/init`, then verify it took hold:

```text
Add a PATCH /tasks/:id/status endpoint that only updates status. Follow the project conventions.
```

### 10.3 Discover Tools

**Customize → Plugins** and **Customize → MCP** let you browse trending servers by category. Enterprises can add a **custom marketplace** — any GitHub repo or Git URL hosting marketplace metadata — which is the realistic path to distributing internal, approved tooling.

```text
/af I need something to query a Postgres database from an agent session
```

Plugin and MCP availability varies by enterprise policy, and many servers need their own authentication.

You can also bring your own model (public preview) under **Settings → Model providers** — OpenAI, Azure OpenAI, Anthropic, Ollama, LM Studio, and any OpenAI-compatible endpoint. Credentials are stored in the system credential store.

### 10.4 Context and Cost

Use `/context` to see usage, `/compact` to relieve token pressure, and `/usage` for plan limits. **The single most effective cost habit:** start a new session when you switch tasks, so you stop paying to carry irrelevant history.

Mine your own history for the rest:

```text
/chronicle cost-tips
```

```text
/chronicle standup
```

```text
/chronicle improve
```

`/chronicle improve` suggests changes to your instructions file based on what you keep correcting manually. If you repeat the same feedback to agents, run it.

### 10.5 Enterprise Governance

Worth knowing if you field admin questions. Enterprises can control which actions users may take — including **which plugins users can install** and **whether auto-approval (`/yolo`) is permitted** — through **enterprise managed settings**, deployed three ways:

| Method | Where |
|--------|-------|
| Server-managed | `.github-private/copilot/managed-settings.json` |
| File-based | macOS `/Library/Application Support/GitHubCopilot/` · Windows `%ProgramFiles%\GitHubCopilot\` · Linux `/etc/github-copilot/` |
| MDM-managed | Native policy values, not a deployed JSON file |

### 10.6 Remote Control

```text
/remote
```

Lets you monitor and steer the current session from **GitHub.com or GitHub Mobile**.

:::warning Remote control does not mean "runs in the cloud"
The most commonly misunderstood feature in the app:

- **The session still runs on your machine.** Every command executes locally.
- **Your machine must stay online.** If it sleeps or loses connectivity, remote control is unavailable until it's back.
- **Slash commands don't work remotely.** You can prompt, approve, switch modes, and cancel — that's it.
- An org owner must set the "Store local sessions in the Cloud" policy to **View and control**. It's unconfigured by default.
- A separate `remoteControl` managed setting applies on top of that policy, per device.

If you need work that survives a closed laptop, you want a cloud sandbox or a cloud automation.
:::

### 10.7 Deep Links

You can launch the app into a specific repo, issue, PR, or a new session from a link, which is how you embed Copilot into runbooks and ticketing systems.

App links use the `ghapp://` scheme — for example `ghapp://github.com/OWNER/REPO/issues/NUMBER`. For anything you **share**, wrap the encoded app link in the hosted launcher so browsers hand off reliably and fall back gracefully:

```text
https://github.com/copilot/app/launch?open=ENCODED_APP_LINK
```

```text
/research Generate a GitHub Copilot app deep link that opens a new plan-mode session in this repository with a kickoff prompt of "Investigate failing tests". Give me both the raw ghapp:// app link and the fully encoded hosted launcher URL.
```

Deep links open a **confirmation UI** — they don't silently create sessions. Never put secrets in one; URLs land in browser history and server logs.

### ✅ Checkpoint

You know what the Customize UI adds, how instruction layers stack, how to control cost, and precisely what remote control, managed settings, and deep links do.

## Quick Reference

### Session Modes

| Mode | Command | Autonomy |
|------|---------|----------|
| Interactive | `/interactive` | Agent proposes, waits for you |
| Plan | `/plan` | Agent plans, you approve, then it executes |
| Autopilot | `/autopilot` | Fully autonomous |

### Essential Slash Commands

| Command | Purpose |
|---------|---------|
| `/model`, `/models` | Select model (including BYOK and `Auto`) |
| `/agent` | Select a custom agent |
| `/review` | Review the current session's changes |
| `/security-review` | Security-focused review of current diffs |
| `/rubber-duck` | Critique from a different model |
| `/spar` | Adversarial challenge to your approach |
| `/pr-open` | Open a PR from session changes |
| `/pr-resolve-comments` | Work through unresolved review comments |
| `/pr-fix-checks` | Address failing PR checks |
| `/pr-merge` | Merge the current PR |
| `/pr-stack` | Create a stack of dependent PRs (built-in skill) |
| `/orchestrate` | Coordinate work across child sessions |
| `/spawn` | Create a focused child session |
| `/fleet` | Multiple agents in parallel on one task |
| `/fork`, `/merge-to-parent` | Fork a session and merge it back |
| `/create-canvas` | Build a canvas extension |
| `/terminal [cmd]` | Open a terminal canvas |
| `/inbox` | Render the interactive inbox widget |
| `/init` | Generate or improve repository instructions |
| `/skills` | Manage skills (`/skills reload` mid-session) |
| `/af` | Find installable MCP servers, tools, skills, agents |
| `/chronicle` | History, standup, cost tips, instruction improvements |
| `/remote` | Steer this session from GitHub.com or GitHub Mobile |
| `/research` | Run a research workflow and produce a cited report |
| `/allow-all-tools`, `/reset-allowed-tools` | Toggle and clear tool auto-approval |
| `/context`, `/compact`, `/usage` | Context and cost management |
| `/rename`, `/clear`, `/restart-session` | Session housekeeping |
| `/export-gist` | Export the transcript to a secret gist |
| `/attach-files`, `/attach-folder` | Attach context to a message |

:::note Many commands are context-gated
Commands appear only when their context exists — an active session, session changes, an open PR. Type `/` to see what's valid right now.
:::

### Prompt Box Shortcuts

| Symbol | Does |
|--------|------|
| `#` | Reference an issue |
| `@` | Add a file to context |
| `/` | Open the command picker |

### Customization Locations

| Item | Project scope | User scope |
|------|--------------|-----------|
| Canvas extensions | `.github/extensions` | `~/.copilot/extensions` |
| Skills | `.github/skills/<name>/SKILL.md` | `~/.copilot/skills/<name>/SKILL.md` |
| Custom agents | `.github/agents/<name>.agent.md` | `~/.copilot/agents/<name>.agent.md` |

## Related Resources

- [About the GitHub Copilot app](https://docs.github.com/en/copilot/concepts/agents/github-copilot-app)
- [Getting started with the GitHub Copilot app](https://docs.github.com/en/copilot/get-started/quickstart-copilot-app)
- [Working with agent sessions](https://docs.github.com/en/copilot/how-tos/github-copilot-app/agent-sessions)
- [Working with canvas extensions](https://docs.github.com/en/copilot/how-tos/github-copilot-app/working-with-canvas-extensions)
- [Managing issues and pull requests](https://docs.github.com/en/copilot/how-tos/github-copilot-app/managing-issues-and-pull-requests)
- [Using automations in the app](https://docs.github.com/en/copilot/how-tos/github-copilot-app/using-automations)
- [About Copilot automations](https://docs.github.com/en/copilot/concepts/agents/cloud-agent/about-automations)
- [About remote control of Copilot CLI sessions](https://docs.github.com/en/copilot/concepts/agents/copilot-cli/about-remote-control)
- [Slash commands reference](https://docs.github.com/en/copilot/reference/github-copilot-app-reference/slash-commands)
- [Built-in skills reference](https://docs.github.com/en/copilot/reference/github-copilot-app-reference/built-in-skills)
- [Deep links reference](https://docs.github.com/en/copilot/how-tos/github-copilot-app/open-with-deep-links)
- [Using your own LLM models (BYOK)](https://docs.github.com/en/copilot/how-tos/github-copilot-app/use-byok-models)
- [Companion lab: Copilot CLI: Zero to Hero](/labs/copilot-cli-zero-to-hero)
