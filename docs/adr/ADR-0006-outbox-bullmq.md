# ADR-0006: Transactional outbox in Postgres, dispatched through BullMQ/Redis

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md)** (2026-09-27):
  - With no RLS, the relay claims rows with a plain `SELECT … FOR UPDATE SKIP LOCKED` as `mos_app`; the SECURITY DEFINER claim function is dropped.
  - `withTenant()` becomes `withContext()`.
- **Decided in the Phase 6 session (2026-09-28, [ADR-0021](ADR-0021-stack-lock-in.md)): keep BullMQ 6 on Redis 7.2 / Valkey** (house standard). The Postgres-queue alternative weighed below was not chosen. The outbox table stays in PostgreSQL, so Redis holds queue state only.
- *Record of the question (ADR-0019):* keep Redis + BullMQ (house standard), or use a Postgres-backed queue such as pg-boss or graphile-worker. The outbox table stays either way.
  - A Postgres queue removes one infrastructure dependency for a single-company install.
  - BullMQ is the house standard and has richer tooling.

## Context
Business actions trigger side effects: email, PDFs, notifications, and automatic steps such as raising a payment request after a GRN. These must happen exactly when the business change commits, and never twice. The house standard is BullMQ on Redis plus an outbox table.

## Decision
- The business transaction writes `core.outbox_event`.
- A relay in the worker claims rows through the SECURITY DEFINER function `core.claim_outbox()` (`FOR UPDATE SKIP LOCKED`) and dispatches them to BullMQ queues, with retries, dead letters and an abandoned-claim sweep. This is ported from DMS **without** DMS's "unset tenant" RLS arm.
- Consumers that write to the database run under `withTenant()` and insert `core.processed_event` in the same transaction, which gives exactly-once effects.
- **Error handling:** technical errors are retried. Business errors create a `core.process_exception` task for a human instead of retrying forever.
- **No external I/O inside request handlers.** This rule is what makes whole-request retry on serialization or deadlock errors safe.

## Consequences
- Events survive a Redis outage because the outbox is durable.
- Missing finance configuration never stops a truck being received; it becomes a visible task instead.
