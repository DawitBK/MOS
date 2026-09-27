# ADR-0011: Gap-free and loose document numbering

- **Status:** Accepted (planning), 2026-09-26. **Amended** (2026-09-27, v0.3 audit T-07/T-29):
  - **one allocation mechanism for every series**, in the same transaction as the document; the separate short transaction for loose series risked connection-pool starvation;
  - a numbered document that is later cancelled or voided **keeps its number**;
  - per-site patterns must contain `{SITE}`.

## Context
Receipts, vouchers, debit notes, payments and withholding certificates (and later customer invoices) typically need gap-free numbering for audit and legal reasons. Other documents do not, and gap-free allocation serialises writers.

## Decision
- **Series:** `core.number_series` per company and document type (optionally per site), with a pattern and a reset rule (never, fiscal year, calendar year, month). `core.number_counter` rows are keyed by a period derived from the **posting date**.
- **Gap-free series:** the number is allocated inside the approval or posting transaction, at the irreversible point, **as the last lock** in the hierarchy. The pattern is the DMS counter: `INSERT … ON CONFLICT DO NOTHING` → `SELECT … FOR UPDATE` → increment.
  - Posted documents are never cancelled, only reversed (and the reversal gets its own number), so gaps cannot appear.
  - Voided WHT certificates keep their number with status VOID.
- **Other series** (PR, PO, gate visit, sample, lot): the same same-transaction mechanism is used (v0.3). Gaps are acceptable for them, but in practice none occur.
- **Backstop:** a unique constraint on `(company, doc_type, number)` (v0.3: no tenant column; ADR-0017).

## Consequences
- Lock contention is limited to milliseconds per gap-free posting. If a site's volume ever requires it, series can be split per site.
