# ADR-0002: Drizzle ORM with SQL migrations; hand-written SQL for database-enforced rules

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md)** (2026-09-27): v1 has no RLS policies or SECURITY DEFINER functions; the hand-written SQL covers triggers, grants, domains and EXCLUDE constraints. The RLS points below apply only to the expansion playbook (TDD Appendix A).

## Context
The house systems use Sequelize (GMS, DMS) and raw `pg` (SSO). The handbook asks for a deliberate ORM choice. MOS needs:
- explicit row locking (`FOR UPDATE`, `SKIP LOCKED`);
- transaction-local settings for RLS (`set_config(...,true)`);
- exact numeric handling;
- database objects an ORM does not model: RLS policies, triggers, EXCLUDE constraints, grants and SECURITY DEFINER functions.

## Decision
- **Drizzle ORM**, with drizzle-kit generating reviewable SQL migrations for tables.
- **Hand-written SQL migrations**, produced by helpers, for RLS, triggers, domains, EXCLUDE constraints, grants and definer functions.
- Numeric columns map to strings, which are wrapped in decimal.js branded types.

## Consequences
- Type-safe queries that still read like SQL, with explicit control over locks and transactions.
- A CI catalog test proves every posting table has the `guard_posted` trigger and that `mos_app` lacks UPDATE/DELETE on insert-only tables (v0.3, ADR-0017; the forced-RLS check returns only with pooled SaaS), so helper-generated SQL cannot silently miss a table.
- Migrations run under a dedicated migrate role; the application role cannot run DDL.
- The house migration-safety check is ported: DROP, RENAME or SET NOT NULL fail CI unless the migration is annotated `-- SAFE:`.

## Alternatives considered
- **Sequelize:** rejected for weak TypeScript support and implicit behaviour.
- **Prisma:** rejected because RLS and `SET LOCAL` handling is awkward and locks would need raw SQL.
- **Raw `pg`:** rejected; no type safety at this schema size.
