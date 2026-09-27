# ADR-0009: The inbound raw-material flow as two persistent process managers

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0016](ADR-0016-system-prepares-role-confirms.md)** (2026-09-27): by default the GRN and payment request are prepared as drafts and confirmed by the storekeeper and finance officer; full auto-posting is a setting. **Edge-agent weighbridge capture moves to Later** (ADR-0018).

## Context
The headline requirement is: QC approval auto-generates the raw-material receipt and triggers supplier payment. The truck leaves long before payment, and finance configuration problems must never stop receiving.

## Decision
- **(A) Physical inbound.** The `gate.visit` row is the process instance. It has a coded state machine:

  CHECKED_IN → FIRST_WEIGHED → SAMPLED → QC_DECIDED → UNLOAD_AUTHORIZED → UNLOADED → RECEIPTED → EXITED

  with the side states HOLD_NO_PO, REJECTED_AT_GATE, ABANDONED and RECEIPT_EXCEPTION.
  - Each user action is **one local transaction**: a guarded state update, same-transaction calls into QC, inventory and procurement, a visit event, and outbox rows.
  - The GRN is **posted automatically inside a savepoint** at the second weighing. A business failure keeps the weighing and moves the visit to RECEIPT_EXCEPTION with a task.
- **(B) Settlement.** This process is triggered by outbox events:
  - a self-billed voucher plus a payment request when the GRN posts; or
  - a payment request when the supplier invoice matches (3-way).
  - Guards: a partial unique index allows one live payment request per invoice, and `processed_event` makes consumers exactly-once in effect.
- **Behaviour classes:** gate flows are coded state machines per behaviour class with configuration switches in v1. Data-driven flow templates are Later.
- **Failure modes:** all 34 enumerated in FRD §9.4 are implemented as named scenario tests.

## Consequences
- A single database means no distributed transactions are needed. "Saga" here means persistent state plus compensating documents.
- Receiving never waits on finance.
