# Repair claims

Run `anyweb repair claims --json`. Translate each `queue_status`:

- `queued`: waiting to run for this account.
- `repairing`: the reported problem is being checked.
- `completed_pending_outcome_adjudication`: the check finished; a published
  successor is not yet available.
- `reproduction_input_required`: more facts are needed before the check can
  finish.
- `repair_available`: a verified successor is available.

This command reads only the current account's opaque claims. It does not start
a repair and does not change a website. A successor claim does not authorize
an irreversible site action.
