# ADR-0008: Signed single-sided inventory ledger with transactional balances and moving-average valuation

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md)/[ADR-0018](ADR-0018-lean-v1-six-months.md)** (2026-09-27):
  - valuation is company-level in v1;
  - the separate receipt-layer table is dropped (receipt SLEs already carry the cost);
  - locks follow the six ranks (TDD §7.3) with `FOR NO KEY UPDATE`;
  - residuals are clamped in the same apply (TDD §9.3);
  - "deadlocks structurally impossible" is corrected to "minimised, with retry as the backstop".

## Context
Stock must be correct under concurrency, fully traceable, and reconcilable to value and journals. Status changes (quarantine → available) and lot splits need the same mechanism as location moves.

## Decision
- **Ledger rows:** `inv.stock_ledger_entry` rows each touch one **bucket** (item × storage unit × lot × stock status) with a signed quantity and value. They are grouped by `inv.stock_txn`. Internal moves are zero-sum pairs, enforced by a deferred trigger.
- **Balances:** `inv.stock_balance` and `inv.item_valuation` are maintained **in the same transaction** under **sorted row locks**, following the lock ranks (TDD §7.3). This removes lock-order deadlocks between postings; 40P01 retry remains the backstop for waits outside the ranks.
- **Negative stock** is refused by CHECK constraints.
- **Valuation:**
  - moving weighted average per item at company level in v1 (site-level areas Later);
  - an empty-out rule removes rounding dust;
  - receipt SLEs keep each receipt's cost, so a FIFO option can be derived later;
  - differences between invoice and receipt price go to PPV (no stock revaluation in v1).
- **Numerics:** all arithmetic uses decimal.js with fixed numeric domains; there are no floats anywhere.
- **Proof:** a property-based model test with concurrent workers checks invariants P1–P9 before any inventory UI is built.

## Consequences
- Every invariant is a single SQL aggregate.
- Valuation is serialised per item. That is acceptable at plant scale, because transactions do no I/O and hold locks for milliseconds.
- Incremental readers must not use `seq` as a commit-order cursor (see TDD §9.4).

## Alternatives considered
- **From/to double-sided rows:** rejected; they need null sides for receipts and issues and are clumsier for status and lot moves.
- **Balances computed on read only:** rejected; too slow, and they cannot enforce non-negativity under concurrency.
