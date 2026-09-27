# ADR-0007: Document lifecycle, immutability and reversal-only corrections

- **Status:** Accepted (planning), 2026-09-26

## Context
ERP auditability requires that posted records never change. Controls such as approvals, SoD and periods depend on a uniform lifecycle.

## Decision
- **Two classes of document:**
  - commitment documents (PR, PO, transfer order, payment request) move no stock or money;
  - posting documents (GRN, issue, transfer, adjustment, count, invoice, payment) write to ledgers.
- **Lifecycle:** DRAFT → SUBMITTED → APPROVED → POSTED → REVERSED, plus REJECTED and CANCELLED; commitment documents end in CLOSED. Fulfilment status (received, billed) is tracked in derived columns.
- **Transitions:** every transition is a status- and version-guarded update. The transition tables are covered by exhaustive tests.
- **Immutability:** a trigger forbids edits to posted headers and lines, and `mos_app` has no UPDATE or DELETE on ledgers. `content_hash` is stamped at posting.
- **Corrections** are reversal documents only. A reversal mirrors the original's ledger entries and is blocked while dependent documents exist.
- **Dates:** posting date and document date are separate. Posting uses fiscal-period locks (FOR SHARE when posting, FOR UPDATE when closing) and backdating windows per document type.

## Consequences
- The audit trail is complete by construction.
- Users correct mistakes with purpose-built documents (return, adjustment, PO reassignment) rather than edits.
