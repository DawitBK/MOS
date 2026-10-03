# ADR-0017: Single tenant, single company in v1, with expansion seams

<!-- nav -->[MOS](../../../README.md) › [6. Decisions](../README.md) › [ADRs](README.md)

- **Status:** Accepted (planning), 2026-09-27. **Supersedes [ADR-0004](ADR-0004-multitenancy-rls.md).** Amends [ADR-0005](ADR-0005-idp-agnostic-oidc.md), [ADR-0006](ADR-0006-outbox-bullmq.md) and [ADR-0013](ADR-0013-attachments-and-dms.md).
- **Trigger:** owner direction (2026-09-27): keep the focus on functionality. Start with one company and no tenancy, keep the ability to expand, and bring in SaaS later.

## Context
v0.1/v0.2 made MOS multi-tenant from day one: `tenant_id` on every table, composite `(tenant_id, id)` keys, FORCE row-level security, three database roles, a boot probe and a catalog test (ADR-0004). That design is sound, but it costs on every table, every query, every test and every migration. The benefit only arrives once shared SaaS is operated, and that is not planned inside the 6-month v1 ([ADR-0018](ADR-0018-lean-v1-six-months.md)).

The owner wants the first release to be one company's ERP, built fast and well, with SaaS as a later business decision.

## Decision

1. **One installation serves one company.**
   - The installation is one deployment with its own database.
   - There is no tenant concept in v1:
     - no `tenant_id`;
     - no RLS policies or tenant GUCs;
     - no composite tenant keys;
     - no tenant boot probe, RLS catalog test or two-tenant suite.
2. **The company dimension is kept, with exactly one company in v1.**
   - `core.company` holds the legal entity profile: name, TIN/VAT, address, logo, base currency, fiscal-year start and time zone.
   - Documents, number series, fiscal periods, valuation areas and bank accounts carry `company_id`, as in v0.2.
   - v1 seeds exactly one company. The UI hides every company picker while only one exists.
   - Rationale:
     - it is one column and one repository filter, so almost free now;
     - it makes a multi-company group a data change later, not a schema rewrite;
     - it absorbs the open client question of whether the sign-in "operation unit" means a legal company ([client-draft review §6 Q9](../../7-reviews/MOS-CLIENT-P2P-DRAFT-REVIEW.md); the Phase 5 decision register will carry it).
3. **Multi-site stays; it is functional, not tenancy.** The hierarchy is company → site (plant) → warehouse → storage unit. Role scopes per site and the operation-unit choice after sign-in (FR-PLT-019) are unchanged.
4. **Keys:** a single-column `id uuid` primary key, UUIDv7 generated in the application, on every table. Codes are unique per the natural business scope, e.g. `item.code` unique in the installation.
5. **Database roles** (least privilege is kept; it protects immutability, not tenancy):
   - `mos_owner` owns the objects and runs migrations through a dedicated migrate login.
   - `mos_app` is the API and worker login: `NOSUPERUSER`, not an owner, DML only. `UPDATE`/`DELETE` are revoked on the ledger, audit, QC-result and posted-document tables.
   - `mos_readonly` serves reporting and support.
   - There is no `mos_rls_definer`. The outbox claim is a plain `SELECT … FOR UPDATE SKIP LOCKED` run by `mos_app`.
6. **Guards that replace the tenancy guards:**
   - **Privilege boot probe:** refuse to start if the app login is a superuser or owns tables, because the immutability grants would then be void.
   - **CI catalog test:** every posting table has the `guard_posted` trigger, and `mos_app` lacks `UPDATE`/`DELETE` on the insert-only tables.
   - **Site-scope test suite:** replaces the two-tenant suite (FRD AS-11).

## Expansion seams (kept deliberately so growth is cheap)

| Seam | Why it matters later |
|---|---|
| All data access goes through module repositories inside one transaction owned by `UnitOfWorkInterceptor` (TDD §7.2) (nestjs-cls) | A tenant filter or GUC can be added in one place |
| The request context (`ctx`) is an object carried by nestjs-cls: user, company, site, and later tenant | Adding a field reaches every query without changing signatures |
| UUIDv7 everywhere; no sequence-based ids exposed | Data from several installations can be merged without key collisions |
| Business settings live in DB tables, not env vars; env holds only infrastructure | Per-customer settings are already data |
| File storage keys use an installation prefix (`<install_id>/<yyyy>/<uuid>`) | A shared bucket later needs no key migration |
| Background jobs take an explicit context argument (`withContext(ctx, fn)`) | Jobs become tenant-aware by filling one field |
| The v0.2 RLS design is preserved as the **expansion playbook** (TDD Appendix A) | The target design is already written and reviewed |

## Expansion path

1. **SaaS v0: instance per customer ("silo").**
   - Each new customer gets its own installation, dedicated on-prem or cloud.
   - It needs **zero code changes**; only deployment automation is needed.
   - This is how a second and third customer are served.
2. **Multi-company in one installation.** Enable more than one company row; show the company pickers. Mostly UI, because the schema already carries `company_id`.
3. **Pooled SaaS (shared database).** Only if the business case appears.
   - Add `tenant_id` to every table with an expand/contract migration.
   - Widen uniqueness constraints and FKs to be composite, turn on FORCE RLS, and add the tenant boot probe and catalog test.
   - The steps are in TDD Appendix A. Estimated at 4–6 dev-weeks with the seams above; it would be far more without them.

## Consequences
- v1 is simpler to build, test and operate. Every query, test and migration loses the tenant dimension.
- Isolation between customers is physical (separate databases), which is the strongest form and the easiest to explain to a customer's IT department.
- A pooled multi-tenant SaaS is a future project, not a switch. That is accepted: it is a business decision the owner has deferred.
- FRD requirements that were purely about tenancy move to FRD Appendix F (Future: multi-tenant/SaaS). Their IDs are kept, not reused.

## Alternatives considered
- **Keep multi-tenancy from day one (ADR-0004):** rejected for v1 because of its cost on every table and test, and because the benefit only comes with shared SaaS.
- **Drop the company dimension too:** rejected. It saves almost nothing, and it makes a multi-company group (common among the target customers) a costly rewrite.
