---
name: predeploy-gonogo
description: Compile an end-of-day pre-deploy recap across this session and any other live agent sessions, for a go/no-go check before deploying the day's work.
---

Build a short, end-of-day pre-deploy recap so the user can make a go/no-go call before deploying today's work.

## 1. Recap this session's own ticket

Cover, in short plain language (short sentences, no jargon):

- Ticket/issue and PR number, current status (merged / open+green / blocked / failing).
- Decisions made and implemented today.
- Any deferred work, with its Jira ticket number(s).
- What's left before it can ship: CI status, open review threads, waiting on review, etc.

## 2. Reach out to other live sessions

Call ListAgents to find peer sessions. For each one, use SendMessage, never the Agent tool, which spawns a fresh context-less agent that will just describe THIS session's own work back at you instead of reaching the peer. Ask each peer for the same recap in the same short format.

A peer message can sit waiting on that session's own user for approval before its Claude even sees it. Don't block on replies. If nothing has come back within your usual pace, tell the user the ping is out, name who hasn't answered yet, and compile from whatever did come back.

## 3. Compile a go/no-go verdict

Produce ONE combined summary:

- One line per ticket: status + go/no-go verdict (ready to deploy / blocked / needs review only).
- A flagged list of anything that would block a deploy today: failing CI, unresolved must-fix review comments, uncommitted work, merge conflicts.
- If nothing needs action, say so plainly and don't pad it.

Keep the whole thing tight. This runs at end of day; the user is tired. A short list per ticket beats a headers-heavy report.
