# ADR-0004: Shared-database multi-tenancy with forced row-level security

- **Status:** **Superseded by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md)** (2026-09-27). v1 is single tenant, single company. This design is kept unchanged as the reviewed target for a future pooled SaaS; its steps are in TDD Appendix A (expansion playbook). Originally accepted (planning) 2026-09-26.

## Context
MOS must run as a shared SaaS *and* as dedicated installs from one codebase. Retrofitting tenancy is very expensive. DMS already proved the per-request RLS approach, and it also left lessons:
- a bare `SET` leaks the tenant across pooled connections;
- the GUC reverts to `''`, not NULL;
- a policy arm meaning "unset tenant sees all" is a hole;
- tests that run as a superuser never exercise RLS;
- global role grants caused cross-tenant bugs.

## Decision
- **Columns and keys:** `tenant_id uuid NOT NULL` on every tenant table; primary keys `(tenant_id, id)`; **composite foreign keys** everywhere. Postgres checks FKs without row security, so single-column FKs would allow cross-tenant references.
- **RLS:** `ENABLE` + `FORCE ROW LEVEL SECURITY` with a single policy `tenant_id = core.current_tenant()`, and **no NULL arm**.
- **Roles:**
  - `mos_app` (NOBYPASSRLS, DML only; insert-only on ledger, audit and QC result tables);
  - `mos_owner` (owns objects, runs migrations);
  - `mos_rls_definer` (owns a few cross-tenant SECURITY DEFINER functions, e.g. the outbox claim).
- **Tenant context:** `set_config('app.tenant_id', $1, true)` inside the per-request transaction.
- **Guards:** a boot probe, a CI catalog test, a two-tenant acceptance suite, and **all tests run as `mos_app`**.
- **Scoping:** company and site scoping is enforced in the application layer; RLS handles tenant isolation only.

## Consequences
- A dedicated install is the same code running with one tenant.
- Every query needs a tenant context. Workers use `withTenant()`.

## Alternatives considered
- **Database per tenant:** rejected; operations cost and migration fan-out are too high for a small team.
- **Schema per tenant:** rejected; migration fan-out, and harder cross-tenant operations.
