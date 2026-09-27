# ADR-0021: Technology stack lock-in (Node 24, NestJS 12, PostgreSQL 18, BullMQ + Redis/Valkey, Next.js 16)

- **Status:** Accepted, 2026-09-28 (owner decisions in the planning Phase 6 session).
- **Settles:**
  - [ADR-0001](ADR-0001-nestjs-on-express5.md): framework version;
  - [ADR-0006](ADR-0006-outbox-bullmq.md): queue backend;
  - [ADR-0012](ADR-0012-frontend-stack.md): frontend confirmed;
  - the TDD §2 items marked "(Phase 6)";
  - database design D-18 (audit partitioning).

## Decision

| Item | Decision | Why |
|---|---|---|
| Runtime | **Node.js 24 LTS** | Node 22 reaches end of life on 2027-04-30, around go-live; Node 24 is supported to 2028-04-30 |
| Backend | **NestJS 12 on the Express adapter** | A new codebase starts on the current major: ESM packages, built-in Standard Schema validation (Zod 4 contracts plug in directly), Vitest |
| Tests | **Vitest** (API and web), supertest, fast-check, Playwright | Follows NestJS 12; one test runner across the monorepo |
| Database | **PostgreSQL 18** | Native `uuidv7()` for keys; the application-side generator stays as a fallback |
| Audit-log partitioning | **Not partitioned in v1** (database design D-18 declined) | Simpler start. If `core.audit_event` nears 50 M rows, monthly partitioning is added as a planned migration: the primary key becomes (`id`, `occurred_at`) |
| Queue and scheduler | **BullMQ 6 + Redis 7.2 / Valkey** behind the `JobQueue` port | House standard and throughput. Schedules use BullMQ Job Schedulers (the legacy repeatable API was removed in BullMQ 6). The outbox stays in PostgreSQL, so Redis holds no business data |
| Frontend | **Next.js 16 / React 19 house stack**: TanStack Table and Query, react-hook-form + Zod, Tailwind, Radix | Shared conventions, lint rules and the build-fingerprint guard with DMS, GMS and SSO |
| ORM | Drizzle ORM + SQL migrations (ADR-0002) | Not re-opened in the session; stays as accepted |

## Consequences

- **Redis/Valkey becomes a required production service** alongside PostgreSQL and Node.
  - The hosting questions (register part D) and the packaging (ADR-0022) include it.
  - Its data is transient: queue state only, rebuilt from the outbox after loss.
- **Backups** cover PostgreSQL, the attachment files and the two keys (encryption and audit HMAC). Redis is not backed up.
- **`core.audit_event` gets a size watch** in the monitoring baseline (ADR-0022). Partitioning becomes a migration once the table nears 50 M rows.
- **Version pins** are recorded in the repository at WP-00. Upgrades happen in maintenance windows after go-live.
