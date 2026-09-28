---
name: after-meeting
description: After a meeting or huddle, read its Granola note and update state, priorities, acceptance criteria, and todos in the out-of-repo work folders. Use when the user types /after-meeting, or pastes a Zapier "Meeting ended" DM.
argument-hint: "[meeting title] (default: most recent Granola meeting)"
---

Turn one finished meeting into updated files. The user has ADHD: follow `adhd-orchestrator` for output shape. Read `~/.claude/adhd-profile.md` if it exists.

## Where things live

- `~/.claude/work/EX-####-name/`: one folder per Jira scope: `intent.md` (incl. AC, out of scope, size budget), `plan.md`, `todos.md`, `notes.md`. See `~/.claude/work/README.md`.
- `~/.claude/now.md`: Now / Next / Parked / Open loops / Last stopped at.
- `~/.claude/workflow/ledger.md`: agent status lines.
- Jira: one ticket is the scope. New tickets only for **deferred** work.

## Steps

1. **Find the meeting.** Use the Granola MCP tools (`list_meetings`, `get_meetings`, `query_granola_meetings`, `get_meeting_transcript`). Match `$ARGUMENTS` by title; with no argument, take the most recent finished meeting. If two meetings match, pick the latest and say so.
2. **Extract.** Decisions, action items (who owns each), changed requirements, new risks, deadlines. Keep only items that are the user's or affect the user's scopes.
3. **Map to scopes.** Match each item to a work folder by EX key, topic, or attendees. Items with no folder go to `Parked` in `now.md`. Never create a work folder without asking.
4. **Update files (local, no approval needed).**
   - `todos.md`: add new todos with `(from: <meeting>, <date>)`; tick ones the meeting closed.
   - `intent.md`: change AC or out-of-scope only when the meeting clearly decided it. Mark each edit `(changed <date>, <meeting>)`.
   - `notes.md`: append decisions with date and meeting title.
   - Scope guard: if the new asks push a scope past its size budget, do not silently grow it. Flag it in the reply as a pick: cut, or defer to a new Jira.
5. **Re-rank.** Rewrite `now.md` Next (max 3), weighing deadlines and consequences first. Keep `Last stopped at` unless the meeting changed it.
6. **Draft outbound, don't send.** Deferred work -> Jira ticket drafts. Follow-ups to others -> Slack drafts. Show them; send only after the user approves each.

## Reply shape

- Line 1: what changed in priority (or "No priority change").
- Up to 5 bullets: file edits, one line each.
- Drafts waiting for approval, if any.
- One next step, under 2 minutes.
