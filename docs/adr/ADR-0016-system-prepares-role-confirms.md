# ADR-0016: Automation defaults — "the system prepares, a named role confirms"

- **Status:** Accepted (planning), 2026-09-27. **Amends ADR-0009.**
- **Trigger:** the client's procure-to-pay draft. In it, the storekeeper generates the GRN ("Save & Generate GRN") and the finance officer raises the payment request. The original scope of work said QC approval "automatically generates" the receipt and "triggers" payment.

## Context
ADR-0009 posted the GRN automatically at the second weighing and raised the payment request automatically after matching. The client's process keeps a person accountable at both points. Their reasons are sound: the storekeeper confirms where the goods physically went (silo or store) and how rejected quantity is handled, and the finance officer confirms invoices and how much to pay now. Both the scope and the draft can hold at once if automation *prepares* the document and a person *confirms* it.

## Decision
- **GRN:** at the second weighing (or on inspection acceptance when the gate module is off), the system builds the GRN as a **draft** and runs every posting validation.
  - The storekeeper reviews storage unit, batch and rejected disposition, then generates it (posts it).
  - The visit state GRN_DRAFTED allows the empty truck to exit once the tare is verified.
  - An SLA escalates un-generated drafts to the warehouse supervisor.
- **Payment request:** when the GRN posts, the system creates a **draft** from PO + GRN (+ matched invoice, or self-billed voucher) and assigns it to the finance officer. They capture or confirm invoices and amounts and submit it for approval.
- **Per-installation (optionally per-site) mode setting** for each step: *confirm* (default) or *automatic* (the v0.1 behaviour). Mode changes are audited and apply only to documents not yet in flight.
- Idempotency, the one-live-request-per-invoice rule, and the process-exception handling of ADR-0009 are unchanged, and now also cover drafts.

## Consequences
- Matches the client's accountability model and still satisfies "automatically generates / triggers": no document is keyed by hand.
- **Adds a human wait:** a draft left unconfirmed delays stock availability (GRN) or payment. The SLA escalation and dashboard counts make that visible.
- Installations that trust their data flows can switch to *automatic* with no code change.
