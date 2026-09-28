---
name: create-intent-md
description: Interview to shape a rough problem into a concise intent.md.
disable-model-invocation: true
argument-hint: "[rough problem description] [--out <path>]"
---

# Intent Doc Interview

Turn a rough, spoken-aloud problem into a one-screen written brief that a
planner, a reviewer, or a future session can act on without the original
conversation.

An intent doc is **pre-solution**. It records what hurts and what "fixed" would
look like. It does not record file paths to change, function signatures, or a
step-by-step plan. That is the job of whatever runs after it.

## Arguments

Rough problem description (argument): $ARGUMENTS

- Free text: the user's rough framing of the problem. Treat it as the answer to
  the first interview slot, not as a spec.
- `--out <path>`: write the doc here instead of `intent.md` in the repo root.

If no problem description was given, ask for one before anything else: "What's
the problem, in whatever rough words you have?"

## Phases

- **ORIENT**: read project context, do repo legwork, so no question wastes a turn
- **INTERVIEW**: one question at a time until the five-slot ledger is full
- **WRITE**: emit `intent.md` and offer revision

---

## ORIENT

1. Read `CLAUDE.md` if it exists, then `AGENTS.md` if it exists. These carry the
   architecture and the vocabulary the doc must use. An intent doc that renames
   the project's own concepts is worse than no doc.
2. Get the author name: `git config user.name`. Use it verbatim in the
   `Author:` line. Do not ask the user their name.
3. Do the **legwork**: from the rough description, identify every named system,
   package, table, endpoint, or role, and go look each one up (`rg`, `Glob`,
   read the file). Two or three targeted searches, not a survey.

The legwork sets a hard bar for the whole interview: **never spend a question on
something the repo answers.** "Which package owns contract sync?" is a search,
not a question. "Should contract sync stay per-office or move per-project?" is a
question, because the code cannot tell you what the user wants.

State what you found in one or two lines before the first question, so the user
sees their answers won't cover ground you already have.

---

## INTERVIEW

### The ledger

Track five slots. The interview ends when the ledger is full, not when you feel
informed:

| Slot | Full when it holds |
| --- | --- |
| **Problem** | The concrete failure or friction, and who feels it, not the absence of a feature |
| **Proposed outcome** | The observable end state, stated so someone could tell whether it happened |
| **Affected users and systems** | Named roles and named systems (services, packages, tables, integrations, external parties) |
| **Constraints** | What is fixed and cannot be traded away: deadlines, compatibility, data volume, org/process, prior decisions |
| **Open questions** | Everything genuinely undecided, each phrased as a question with a named decider where one exists |

A slot is full only when it holds a statement **you could not have written before
the interview**. Your own inference, a restatement of the rough description, or
a plausible-sounding guess leaves the slot empty.

### Asking

Use `AskUserQuestion`, **one question per turn**. Never bundle two questions into
one, and never send a numbered list to answer in bulk. The whole point is that
each answer steers the next question.

For each question:

- Keep the question itself to one or two sentences.
- Offer 2–4 concrete options drawn from what you found in ORIENT, phrased in the
  project's own vocabulary. Put your recommendation first and mark it
  `(Recommended)`.
- The auto-injected "Other" option carries anything you didn't anticipate, so you
  do not need to add an escape option yourself.

Ask in ledger order, but follow the user: a surprising answer that opens a bigger
hole earns the next question, whatever slot it belongs to.

After each answer, write the resulting statement into its slot and say in one
line what you now believe, so a wrong reading gets corrected on the spot rather
than surviving into the doc.

### Ending

Two rules bound the interview, and either one can end it:

- **A slot the user cannot settle moves to Open questions.** After two attempts
  on the same slot, stop asking and record it as an open question naming who
  decides. An honest open question is a better artifact than a confident
  invention.
- **Roughly ten questions is the ceiling.** Past that, whatever is still empty
  becomes an open question. An intent doc is a starting point, not a
  requirements sign-off.

Before writing, tell the user the ledger is full and name anything that landed
in Open questions rather than getting answered.

---

## WRITE

Write the file to `intent.md` in the repo root (or `--out <path>`). If that file
already exists, ask before overwriting, and offer `intent-<slug>.md` as the
alternative, where `<slug>` is a kebab-case short name for the problem.

Use exactly this structure:

```markdown
# Intent: <short problem name>
Author: <git config user.name>. Status: draft.

## Problem

## Proposed outcome

## Affected users and systems

## Constraints

## Open questions
```

Formatting bounds (the doc is **concise** or it is not read):

- The whole file fits on one screen: under 60 lines.
- Each section is at most four sentences or five bullets.
- Bullets carry specifics: a named system, a number, a role, a date. A bullet
  that would read the same on any project's intent doc is filler; cut it.
- `Affected users and systems` is a list of names, not prose. Split it into
  `Users` and `Systems` sub-bullets when both are non-trivial.
- `Open questions` is a list of questions, each ending in `?`, with the decider
  in parentheses where one is known.
- Use the project's vocabulary from `CLAUDE.md` / `AGENTS.md`. No file paths, no
  function names, no implementation steps.

For a filled-in reference, see [`EXAMPLE.md`](EXAMPLE.md) in this skill's
directory.

After writing, show the user the path and ask whether any section needs
revision. Do not commit the file unless the user asks.

---

## Notes

- The doc is `Status: draft.` on every first pass. Promoting it past draft is the
  user's call, made in a later conversation.
- If the user's rough description is already a solution ("add a `retry_count`
  column"), the first question walks it back to the problem: what goes wrong
  today that the column would fix?
- Scope creep in the interview is the common failure. When an answer opens an
  adjacent problem, ask whether it belongs in this intent doc or its own, and
  respect the answer.
