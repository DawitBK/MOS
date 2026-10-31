# MOS on Odoo: lifecycles of the process model against the models

<!-- nav -->[MOS](../../README.md) › [3. Design](README.md)

- **Date:** 2026-10-31. **Source:** the 27 state diagrams of [the process model](../2-requirements/MOS-PROCESS-MODEL.md) §6, read from the diagrams themselves (`%% lifecycle:` blocks), against the state fields in the live database of `mos-odoo`.
- **How to read it.** The build uses its own words for states (a PO is *issued*, not ISSUED, and a supplier invoice is *payable*, not MATCHED). **Same** means the states and moves of the diagram exist. **Equivalent** means the diagram's step is carried by another field or by one state standing for two, with the reason. **Fixed** means a difference was found on 2026-10-31 and closed that day (with tests). **Open** is what is still different.
- **Result:** 27 lifecycles: 12 same, 10 equivalent, 5 fixed today, 0 open that block a requirement. The five fixes are listed first because they were real gaps, found by this comparison.

## Fixed on 2026-10-31

| Lifecycle | What was different | What was done |
|---|---|---|
| 6.11 Stock status | Only AVAILABLE, QUARANTINE and BLOCKED existed; the FRD asks for DAMAGED and REJECTED too (FR-INV-002, 003) | The two statuses, with their exits: damaged stock can be recovered (with approval) or scrapped, rejected stock leaves only by scrap, supplier return or concession; only quarantine is rejected. Scrap can leave a damaged or rejected place; consumption cannot. Balance and position reports carry the new columns |
| 6.2 Supplier and customer | A customer could only be approved or blocked, never rejected, suspended, reinstated or unblocked; a supplier could not be rejected | Customer: reject, suspend, reinstate, unblock, each with its role rule (the person who suspended or blocked does not lift it). Supplier: reject candidate (reinstating makes it a candidate again) |
| 6.15 Payment and customer receipt | A customer receipt could not be reversed, so a bounced cheque (E55) had no way to reopen the invoice | Receipt reversal by a finance manager other than the cashier, with a reason; the invoice owes again, the customer balance rises, a reversed cash receipt leaves the till, and a closed session refuses it |
| 6.25 Material gate pass | No rejection, no validity or expiry, no partial out or return, no asset tag or serial, no estimated value, no link from a stock item to its inventory document (FR-GATE-019) | Reject with reason, valid-until with a daily expiry job, quantity out and returned per line (partial), asset tag, serial, estimated value, and a final pass for a stock item cannot leave without a posted delivery or goods issue |
| FR-INV-010 / FR-INV-011 (found while comparing 6.11 and 6.17) | Categories were on Odoo's default *standard* cost, not the moving weighted average the FRD asks for; and nothing stopped a store from going below zero | Every category is on the average method (new ones start there; a migration moves the existing ones); a stock quant in a store can never go negative |

## The comparison

| # | Lifecycle (diagram) | Build (model: states) | Result |
|---|---|---|---|
| 6.1 | Generic approval document: DRAFT, SUBMITTED, APPROVED, REJECTED, with return to draft | Every approvable model: draft, pending, approved, rejected; returned is a history entry (`mos.approvable`) | Same |
| 6.2 | Supplier and customer | Supplier: candidate, approved, rejected, suspended, blocked. Customer: not approved, approved, rejected, suspended, blocked. INACTIVE is *archive* (refused while documents or balances are open, FR-PLT-053) | Fixed; inactive is equivalent |
| 6.3 | Supplier bank account | `mos.supplier.bank.change`: requested, verified (call-back), applied, rejected; the account in use is the applied one, the earlier one is replaced | Equivalent (PENDING = requested and verified, ACTIVE = applied) |
| 6.4 | Purchase requisition, with PARTIALLY_ORDERED, ORDERED, CLOSED | `mos.purchase.request`: draft, pending, approved, rejected, converted, cancelled | Equivalent: *converted* is ORDERED; the partial and short-close steps are shown on the order lines (quantity still to order), not as request states |
| 6.5 | Purchase order | Odoo order states plus `mos_po_status` (draft, pending, issued, cancelled), receiving status (waiting, ready, short, done) and the close state with short-close (`mos.close.short.wizard`) | Equivalent (one lifecycle carried by three fields) |
| 6.6 | Expected arrival | `mos.gate.visit` in state *expected* (made by the daily job from orders, transfers and released deliveries) becomes *arrived* | Equivalent |
| 6.7 | Gate visit, inbound (14 states) | `mos.gate.visit`: expected, arrived, weighing, weighed, cleared, exited, cancelled, abandoned; plus entry state (entered, approved, rejected), inspection state, receipt state (not prepared, prepared, confirmed, rejected load) and the receipt-failure exception | Equivalent: HOLD_NO_PO and REJECTED_AT_GATE are the entry state; GRN_DRAFTED and RECEIPTED are the receipt state; RECEIPT_EXCEPTION is an exception of that kind |
| 6.8 | Gate visit, outbound | Same model, outbound purposes; the delivery order carries loading and issue | Equivalent |
| 6.9 | Weigh ticket: OPEN, COMPLETE, VOIDED, LOCKED | `mos.weigh.ticket`: draft, weighed, posted, superseded | Equivalent (superseded = voided with the replacing ticket kept, posted = locked) |
| 6.10 | Inspection lot: CREATED, IN_TESTING, DECISION_PENDING, DECIDED, CLOSED, CANCELLED | `mos.lab.inspection`: open, tested, decided, closed | Equivalent: a lot of an abandoned visit is *closed* with the reason on the visit; no separate cancelled |
| 6.11 | Stock status per bucket | Location status: available, quarantine, blocked, damaged, rejected | Fixed |
| 6.12 | GRN: DRAFT, POSTED, CANCELLED, REVERSED | Odoo receipt (draft to done) with the visit's receipt state, and `action_reverse_grn` (refused while an invoice is matched) | Same |
| 6.13 | Supplier invoice | `mos.supplier.invoice`: draft, payable, vouchered, paid, cancelled, with match state (unmatched, matched, exception, awaiting receipt, QC hold, payment hold) and invoice reversal | Equivalent (MATCHED, ON_HOLD, AWAITING_RECEIPT are the match state) |
| 6.14 | PV (payment request) | `mos.payment.voucher`: draft, submitted, approved, partially paid, paid, rejected, cancelled | Same |
| 6.15 | Payment and customer receipt: DRAFT, POSTED, REVERSED | Supplier payment with payment reversal; customer receipt: posted, reversed | Fixed (receipt) |
| 6.16 | Cheque: ISSUED, VOIDED, MISSING, CLEARED, STALE | `mos.cheque.leaf`: unused, issued, cleared, stale, void, missing | Same (fixed earlier today) |
| 6.17 | Stock adjustment, transfer, issue | Adjustment, transfer order, goods transfer, goods issue, miscellaneous receipt: draft, pending, approved, applied/posted or issued/received, rejected, cancelled | Same |
| 6.18 | Count: PLANNED, IN_PROGRESS, COUNTED, RECOUNT, APPROVED, POSTED, CANCELLED | `mos.cycle.count`: draft, counting, submitted, review, closed (with recount by another person and the adjustment it makes) | Equivalent |
| 6.19 | Work order: PLANNED, RELEASED, IN_PROGRESS, COMPLETED, SETTLED, CLOSED, CANCELLED | `mos.work.order`: draft, planned, running, done, cancelled; the material cost is settled when the run completes | Equivalent: one step does what the diagram splits into complete, settle and close, because settlement is not a separate job in v1 |
| 6.20 | Sales order | Odoo order states with the approval state (none, pending, approved, rejected, returned, cancelled); on hold is an approval pending for credit | Equivalent |
| 6.21 | Delivery order: RELEASED, PICKED, LOADED, GOODS_ISSUED, EXITED, DELIVERED | `mos.delivery.order`: draft, released, dispatched, cancelled; loading and exit are the gate visit's steps, delivery is the proof-of-delivery record | Equivalent |
| 6.22 | Customer invoice | `mos.customer.invoice`: posted, with paid and balance computed (partly paid, paid) | Equivalent (derived statuses, as the diagram says) |
| 6.23 | Credit note: DRAFT, SUBMITTED, POSTED, REJECTED | `mos.credit.note`: draft, pending, approved, posted, cancelled | Equivalent (a rejected approval returns the note to draft) |
| 6.24 | Cash session: OPEN, CLOSED | `mos.cash.session`: open, closed | Same |
| 6.25 | Material gate pass | `mos.gate.pass`: draft, approved, rejected, expired, out, returned, closed, cancelled | Fixed |
| 6.26 | Fiscal period per module: OPEN, SOFT_CLOSED, CLOSED | `mos.period.lock` per area (payables, receivables, inventory, production): soft and hard lock dates, reopening by approval | Equivalent |
| 6.27 | Process exception: OPEN, RESOLVED, CANCELLED | `mos.exception`: open, assigned, resolved, dismissed | Equivalent |

## What this comparison did not do

It compares states and the existence of the moves, not each guard (who may do it, in which condition). The guards are covered by the tests of each addon (505 before today's additions, 535 and more after) and by the requirement table in [IMPLEMENTATION-STATUS.md](IMPLEMENTATION-STATUS.md).
