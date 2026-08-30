# Repair claims

Run `anyweb repair claims --json`. Translate each `queue_status`:

- `queued`: waiting for the hosted worker.
- `repairing`: worker is checking a source-free reproduction.
- `completed_pending_outcome_adjudication`: execution finished; publication is
  not yet available.
- `reproduction_input_required`: the worker could not prove the repair from the
  available source-free evidence.
- `repair_available`: an immutable verified successor is available.

This command reads only the current account's opaque, source-free claims. It
does not enqueue a repair and performs no website mutation. A successor claim
does not authorize an irreversible site action.
