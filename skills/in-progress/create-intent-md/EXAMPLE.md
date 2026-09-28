# Reference: a filled intent doc

A worked output for the `create-intent-md` skill, grounded in this repo's domain.
It shows the shape and the level of specificity, not a real ticket.

Note what it does *not* contain: no package paths, no function names, no
migration plan. Every bullet names something concrete (a role, a system, a
number, a date) and the unresolved parts sit honestly in Open questions.

---

# Intent: Plexxis push failure visibility
Author: Greg Stark. Status: draft.

## Problem

When a Plexxis push fails, the integration records the failure but surfaces
nothing a user can act on. The UI shows the PCO as un-pushed with no reason.
Project admins retry blindly, and support escalates roughly 15 tickets a month
that turn out to be three recurring causes (missing cost code, closed period,
stale auth token).

## Proposed outcome

A failed push carries a user-readable reason and a suggested next action, shown
on the PCO wherever push status is displayed. A project admin can tell an
un-pushed PCO caused by their own data (fixable) from one caused by an
integration or credential problem (needs support) without opening a ticket.

## Affected users and systems

- **Users**: project admins (primary), Clearstory support (ticket volume),
  subcontractor accounting staff who own the Plexxis-side data
- **Systems**: the Plexxis adapter and the integration push path; the PCO push
  status surfaced to `host-ui`; error records the API returns to the client
- **External**: Plexxis API error responses, the only source for cause detail,
  and it is not documented

## Constraints

- Reason strings must be safe to show a customer: no Plexxis internal IDs, no
  stack traces, no credential fragments.
- Existing failed-push rows must keep working. Cause detail is best-effort for
  history, required only for new failures.
- Must not change push retry semantics; this is a visibility change only.

## Open questions

- Which Plexxis error responses map to which of the three known causes, and what
  is the fallback for an unrecognized response? (needs adapter spike)
- Does the reason belong on the PCO row, a detail panel, or both? (Thomas:
  product)
- Should a credential-class failure notify the office admin rather than waiting
  for someone to notice the PCO? (open)
