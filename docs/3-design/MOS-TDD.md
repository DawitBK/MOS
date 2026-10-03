# MOS: Technical Design Document (TDD)

<!-- nav -->[MOS](../../README.md) › [3. Design](README.md)

| | |
|---|---|
| Product | MOS (Manufacturing Operations Suite). The name is a working codename |
| Document | Technical Design Document: design only; no code is written at this stage |
| Version | **0.3.3**, draft for review (package v0.3) |
| Date | 2026-09-27 |
| Audience | Developers (human and AI-assisted), reviewers, future operators |
| Companion docs | [MOS-FRD.md](../2-requirements/MOS-FRD.md) (what) · [MOS-PROCESS-MODEL.md](../2-requirements/MOS-PROCESS-MODEL.md) (who, in which order) · [MOS-DATABASE-DESIGN.md](MOS-DATABASE-DESIGN.md) (every table and column) · [MOS-IMPLEMENTATION-PLAN.md](../4-plan/MOS-IMPLEMENTATION-PLAN.md) (when) · [docs/6-decisions/adr/](../6-decisions/adr/) (why) · [MOS-PACKAGE-AUDIT-v0.3.md](../7-reviews/MOS-PACKAGE-AUDIT-v0.3.md) (what the v0.3 audit changed) |

**Revision history**

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-09-26 | First design |
| 0.2 | 2026-09-27 | Client procure-to-pay draft: local accounts, operation unit, storage units and silos, grading and scoring, GRN confirm mode, PV screen, cheque register |
| **0.3** | 2026-09-27 | **Re-framed and audited:**<br>• **single installation per company, no tenancy** ([ADR-0017](../6-decisions/adr/ADR-0017-single-tenant-single-company-v1.md)); the v0.2 multi-tenant design moves to **Appendix A (expansion playbook)**<br>• **production, sales and receivables designed for v1** (§14, §15)<br>• **infrastructure not assumed** ([ADR-0019](../6-decisions/adr/ADR-0019-infrastructure-not-assumed.md)): queue and storage behind ports; deployment options become proposals<br>• **defects fixed:** settlement counters on posted rows (§8.4), QC result invalidation under insert-only grants (§11), transactions around streaming routes (§7.2), pool starvation from separate numbering transactions (§17.1), idempotency-key scope (§7.4), dependency cycles (§1.3), rounding consistency (§9.6), moving-average residuals (§9.3)<br>• **leaner engineering** ([ADR-0018](../6-decisions/adr/ADR-0018-lean-v1-six-months.md)): approval matrix, six lock ranks, sealed audit log, edge agent deferred (Appendix B)<br>• versions re-checked (Node 22 reaches end of life 2027-04-30, NestJS 12 and BullMQ 6 released); the stack choices go to the Phase 6 architecture session |
| **0.3.1** | 2026-09-27 | **Feedback from the process model** (planning Phase 2, [process model §14](../2-requirements/MOS-PROCESS-MODEL.md#14-gaps-found-and-fed-back)):<br>• **tier fix:** new `ReceiptCommitmentPort` (§1.3), because GRN posting in `inv` wrote PO lines in `proc`<br>• new `OutboundLoadListener` port; the three `UsageDecisionListener` implementations listed<br>• weigh-ticket verification is a separate status (§12.5)<br>• outbound visits start from an expected arrival (§12.4)<br>• `grn.reversed` cleans draft PVs (§12.3)<br>• the supplier-invoice journal made explicit (§13.2, §13.4)<br>• settlement before inventory close (§14.3)<br>• customer, credit-limit and price-list approvals (§15)<br>• weighbridge-scoped number series and new default patterns (§17.1)<br>• event catalogue reference (§17.2) |
| **0.3.2** | 2026-09-27 | **Feedback from the database design** (planning Phase 3, [database design §18](MOS-DATABASE-DESIGN.md#18-decisions-made-in-this-phase-changes-fed-back-open-items)):<br>• `fin.open_item` replaces `fin.invoice_open_item` for every partner document (§8.4, §13)<br>• company paying accounts and tax codes move to `md` (tier fixes, §10, §13.1)<br>• new ports `PartnerLedgerPort` and `WeighTicketReader` (§1.3)<br>• usage decisions insert-only, with the lot pointing at the current one (§11)<br>• approval decisions in an insert-only table (§16.1)<br>• per-module period status as the lock target (§7.3, §8.5); document-type policy per company (§8.1)<br>• goods issue as its own posting document; ship-to as partner addresses (§15)<br>• supplier-return journal split (§13.4); the "never deleted" registry attribute (§4.2) |
| **0.3.3** | 2026-09-28 | **Stack and operations locked in the planning Phase 6 session** ([ADR-0021](../6-decisions/adr/ADR-0021-stack-lock-in.md), [ADR-0022](../6-decisions/adr/ADR-0022-hosting-and-operations-baseline.md)):<br>• Node.js 24 LTS, NestJS 12 with Vitest, PostgreSQL 18, BullMQ 6 + Redis/Valkey, Next.js 16 (§2, §17.2)<br>• `core.audit_event` not partitioned in v1 (database design D-18 declined)<br>• deployment: on-premises first; native services and Docker Compose both documented and tested; dev, staging and production; backup and observability baseline (§20) |

Tags follow the FRD: **v1 (Mn)**, **v1.1**, **Later**. Requirement IDs (`FR-…`, `BR-…`, `AS-…`) point to the FRD.

---

## 1. Architecture overview

### 1.1 Style
MOS is a **modular monolith**: one deployable API process plus one worker process, sharing one PostgreSQL database.

- **Modules.** Each bounded context owns its own Postgres **schema**. Modules talk to each other through exported application services when atomicity matters (same transaction), otherwise through domain events on a **transactional outbox**.
- **Why a modular monolith** (ADR-0003):
  - with 1–2 developers, microservices would multiply operations work with no benefit at this scale;
  - the inbound flow needs single-transaction consistency across gate, QC, inventory and procurement;
  - module boundaries are enforced by lint, so the code can be split along schema lines later if scale ever demands it.

### 1.2 Runtime topology
```
                 Browser / tablet (Next.js web app)
                            │ HTTPS (same origin; any reverse proxy: Apache / nginx / IIS)
                 ┌──────────┴───────────┐
                 │    Reverse proxy     │  TLS, compression, path routing (/, /api)
                 └───────┬───────┬──────┘
                         │       │
           ┌─────────────┘       └────────────┐
   ┌───────▼────────┐                 ┌───────▼────────┐
   │  web (Next 16) │                 │  api (NestJS)  │── OIDC (optional) ──► Identity provider
   └────────────────┘                 └───┬─────┬──────┘                      (any OIDC IdP)
                                          │     │ enqueue via JobQueue port
                          SQL             │     ▼
                   ┌──────────────────────▼┐  ┌──────────────────────┐
                   │  PostgreSQL 18        │  │ Redis / Valkey       │  BullMQ 6 queue state only
                   │  schemas: core md inv │  │ (ADR-0021)           │  (the outbox stays in
                   │  proc qc gate fin     │  └──────────┬───────────┘  PostgreSQL)
                   │  mfg sales            │             │
                   └──────────▲────────────┘  ┌──────────▼───────────┐
                              └───────────────│  worker (NestJS)     │ outbox consumers, PDFs, email,
                                              │                      │ exports, schedules, audit sealing
                                              └──────────┬───────────┘
                                                         │ BlobStore port
                                              ┌──────────▼───────────┐    ┌──────────────────┐
                                              │ File storage:        │    │ Virus scanner    │
                                              │ local disk / NAS     │    │ (optional)       │
                                              │ (default) or S3      │    └──────────────────┘
                                              └──────────────────────┘
```
Hardware integrations (weighbridge edge agent, label printers) are Later (Appendix B).

### 1.3 Bounded contexts, schemas and dependency tiers
| Schema | Module | Owns |
|---|---|---|
| `core` | Platform | Installation, company profile, sites, departments, users, roles/permissions, sessions, auth policy, document types, number series, fiscal periods, approvals and SoD, doc actors and links, outbox, processed events, idempotency, process exceptions, attachments, audit, notifications |
| `md` | Master data | UoM, items, categories, partners (supplier/customer/transporter), bank master, supplier bank accounts and **company paying accounts**, KYC, **tax codes, tax groups and withholding profiles**, payment terms, currencies, FX, warehouses, storage units, reason codes |
| `inv` | Inventory | Stock transactions, ledger entries, balances, valuation, lots and genealogy, receipts, transfers, adjustments, status changes, counts, silo measurements, treatment holds, reservations |
| `qc` | Lab/quality | Methods, parameters, specs, QC policies, sampling, grade schemes, score models, price rules, inspection lots, samples, results, invalidations, decisions, concessions |
| `proc` | Procurement | Sourcing policy, approved supplier list, PR, quotations on the PO, PO, revisions |
| `gate` | Gate | Gate posts, weighbridges, vehicles, drivers, blacklist, shifts, expected arrivals, visits, weigh tickets, weighings, exit clearance, gate passes, incidents, paper series, back-entry |
| `mfg` | Production | Work centres, recipes, work orders, issues, outputs, pending issues, settlement, shift logs, downtime |
| `sales` | Sales | Customers (extension), price lists, sales orders, delivery orders, customer invoices, credit notes, receipts, cash sessions, advances |
| `fin` | Payables/receivables | Match tolerances, supplier invoices, **open items of every partner document**, matches, debit notes, AP/AR entries and allocations, PVs, payments, cheques and cheque books, WHT certificates, account map, journals, export batches |

**Dependency tiers** (enforced by dependency-cruiser; a module may import only from lower tiers):

| Tier | Modules |
|---|---|
| 0 | `core` |
| 1 | `md` |
| 2 | `inv` |
| 3 | `qc`, `proc` |
| 4 | `gate`, `mfg`, `sales` |
| 5 | `fin` |

- **Calls that must go "upwards" use ports** (dependency inversion): the lower module declares an interface, and the higher module implements it. Examples:
  - `qc` declares `UsageDecisionListener`; `gate` implements it to advance the visit.
  - `core` declares `JournalPort`; `fin` implements it, so the posting engine can write journals without importing `fin`.
  - `gate` declares `OutboundDocumentChecker`; `sales` and `inv` implement it for exit clearance.
  - `inv` declares `ReceiptCommitmentPort`; `proc` implements it. A GRN is an `inv` document, but posting it must lock the PO header and lines and update the received quantity, which `inv` cannot import. *(Added in 0.3.1; v0.3 had the GRN write PO lines directly, a tier violation.)*
  - `gate` declares `OutboundLoadListener`; `sales` (goods issue) and `inv` (bulk transfer dispatch) implement it. The verified outbound net becomes the draft of the outbound document.
  - `UsageDecisionListener` has three implementations, chosen by the inspection lot's origin: `gate` (inbound visit), `proc` (gate module off: prepare the GRN draft) and `sales` (customer return: draft the credit note).
  - `core` declares `PartnerLedgerPort`; `fin` implements it (beside `JournalPort`). Sales postings (customer invoices, receipts, credit notes) write open items and partner entries through it. *(Added in 0.3.2.)*
  - `qc` declares `WeighTicketReader`; `gate` implements it. With the Gate module off, the weighbridge station still records weigh tickets, and the inspection lot reads the weights through this port (flow C). *(Added in 0.3.2.)*
  - The full list of synchronous hand-offs is in [process model §9.1](../2-requirements/MOS-PROCESS-MODEL.md#91-synchronous-hand-offs). Every table, column and constraint is in the [database design](MOS-DATABASE-DESIGN.md).
- **Siblings in the same tier** talk only through events or ports, never by direct import.

---

## 2. Technology stack

Versions were re-checked on 2026-09-27 and **locked with the owner in the planning Phase 6 session on 2026-09-28** ([ADR-0021](../6-decisions/adr/ADR-0021-stack-lock-in.md)).

| Layer | Choice | Version policy | Notes / ADR |
|---|---|---|---|
| Runtime | Node.js | **24 LTS** (end of life 2028-04-30); decided in Phase 6 | House standard was 22, which reaches end of life on 2027-04-30, about when v1 goes live |
| Language | TypeScript | Current major at build start, `strict` | — |
| Backend framework | **NestJS on the Express adapter** | **12.x** (ESM packages, built-in Standard Schema validation, Vitest), released 2026-08-28; decided in Phase 6 | ADR-0001. No request-scoped providers; context via nestjs-cls |
| Validation / contracts | Zod (house standard); shared schemas in `packages/contracts` | Zod 4 | With NestJS 12, the built-in schema pipe can replace nestjs-zod's validation. OpenAPI generation is deferred until a public API exists |
| ORM / SQL | **Drizzle ORM** + SQL migrations + hand-written SQL for triggers, grants and EXCLUDE constraints | latest | ADR-0002 |
| Database | PostgreSQL | **18** (native `uuidv7()`); decided in Phase 6. `core.audit_event` is not partitioned in v1 | Extensions: `pgcrypto`, `btree_gist`, `pg_trgm`, `citext` |
| Queue / scheduler | **BullMQ 6 + Redis 7.2 / Valkey**, behind the `JobQueue` port; decided in Phase 6 | — | ADR-0006, ADR-0021; §17.2. Schedules use BullMQ Job Schedulers (the legacy repeatable-job API was removed in BullMQ 6). Redis holds queue state only; the outbox stays in PostgreSQL |
| Real-time | Server-Sent Events (yard board, approvals badge), fed by an in-process emitter (single API instance) or Postgres `LISTEN/NOTIFY` after commit | — | Simpler than WebSockets for one-way updates |
| Logging | Pino via `nestjs-pino`, JSON, correlation ID, secret redaction | house | — |
| Metrics / health | prom-client `/metrics` (protected), `/api/v1/health` (DB, queue, worker heartbeat) | — | — |
| Auth | Local accounts (argon2id, TOTP); optional OIDC via `openid-client` v6 (ESM; discovery, PKCE S256, JWKS) | 6.x | ADR-0005, ADR-0015 |
| PDF / labels | `@pdfme/generator` + barcode/QR plugins; templates per company | — | No headless browser needed |
| Spreadsheets | `exceljs` (import/export) | — | — |
| Decimal math | `decimal.js` behind branded types `Qty`, `Money`, `Price`, `Factor` | — | Numbers never become JS floats |
| File storage | `BlobStore` port: **local disk / NAS (default)**, S3-compatible optional; downloads streamed through the API after an authorisation check | — | ADR-0013, ADR-0019 |
| AV scanning | Optional ClamAV adapter; fail-open or fail-closed by setting | — | Depends on the client's infrastructure |
| Frontend | **Next.js 16 / React 19**, TanStack Query 5, Zustand, Tailwind, Radix primitives, lucide-react, **TanStack Table**, **react-hook-form + Zod** | house versions (16.3.x) | ADR-0012 |
| Testing | **Vitest** + supertest + a real PostgreSQL 18 (as `mos_app`) + fast-check; Vitest + RTL (web); Playwright smoke/e2e | — | §18 |
| Lint / format | ESLint flat config, Prettier, dependency-cruiser | — | House Next 16 rules apply |
| Repo | npm workspaces monorepo | — | §3 |
| CI | GitHub Actions (or equivalent) | — | §18.4 |

**Deliberate deviations from the house handbook**, each recorded as an ADR:
- NestJS instead of bare Express; it still runs on Express.
- Drizzle instead of Sequelize or raw `pg`.
- react-hook-form and TanStack Table added; the house systems have no form or grid library.
- Node 24 LTS instead of 22 (proposed), because of the end-of-life date.

Everything else follows the handbook: `/api/v1`, the response envelope, AppError codes, Pino, Zod, the env contract, the outbox, migration-safety checks and Playwright smoke tests.

---

## 3. Repository layout (planned; nothing created yet)

```
MOS/
├─ apps/
│  ├─ api/            NestJS HTTP API (modules/core, md, inv, qc, proc, gate, mfg, sales, fin)
│  ├─ worker/         NestJS standalone app: outbox relay (if the queue needs one), job processors, schedulers
│  └─ web/            Next.js 16 app (app/ thin routes, features/<domain>/, shared/, providers/, state/)
├─ packages/
│  ├─ contracts/      Zod schemas + typed fetch client shared with the web app
│  ├─ domain-kernel/  Decimal brands, rounding helper, calendar formatting, error codes
│  └─ db/             Drizzle schema per context, SQL migrations, table registry (grant classes), seeds, industry pack
├─ infra/
│  ├─ dev/            dev dependencies (Postgres, optional queue backend, Mailpit)
│  └─ deploy/         deployment proposals, written after the hosting decision (ADR-0019)
├─ docs/              this planning package, ADRs
├─ CLAUDE.md          conventions and footguns for AI-assisted sessions
└─ PROGRESS.md        progress tracker
```

Backend module internal layout, which follows the handbook's route → service → repository layering:

```
modules/<ctx>/
  <ctx>.module.ts
  api/          controllers (thin; Zod DTOs from packages/contracts)
  application/  services/use-cases (transaction boundaries, orchestration, events)
  domain/       pure logic: state machines, evaluators, posters (no I/O; property-tested)
  infra/        repositories (Drizzle), external adapters; the ONLY place SQL is allowed
  ports/        interfaces this module offers to higher tiers (§1.3)
  index.ts      the ONLY export surface other modules may import
```

---

## 4. Installation model and data protection (ADR-0017)

### 4.1 Model
- **One installation serves one company.** It is one deployment with its own database, holding one company and any number of sites.
- **No tenant layer:** no `tenant_id`, no RLS, no tenant GUC.
- **Keys:** every table has a single-column `id uuid` primary key (UUIDv7, generated in the application, or `uuidv7()` on PostgreSQL 18). Foreign keys are plain.
- **Company dimension kept:** document headers, number series, fiscal periods and bank accounts carry `company_id`. v1 seeds exactly one company, and the UI hides company pickers.
- **Site scoping** is enforced in the application: a mandatory repository scope helper filters by the user's site scopes, covered by scope tests (AS-11).
- **`core.installation`** is a singleton (id, name, settings jsonb) holding installation-wide settings: auth policy, module switches, automation modes.

### 4.2 Database roles
| Role | Attributes | Used for |
|---|---|---|
| `mos_owner` | NOLOGIN; owns every schema, table, function and trigger | Nothing at runtime |
| `mos_migrator` | LOGIN; member of `mos_owner` | The migration job only (`SET ROLE mos_owner`) |
| `mos_app` / `mos_worker` | LOGIN, NOSUPERUSER, NOCREATEDB, NOCREATEROLE; members of group `mos_rw` | The API and the worker. Separate logins make `pg_stat_activity` attribution easy |
| `mos_readonly` | LOGIN | Reporting and support: SELECT, with column-level REVOKE on secrets (password hashes, TOTP secrets, encrypted account numbers) |

- `REVOKE CREATE ON SCHEMA public FROM PUBLIC`. No role except the owner has CREATE on MOS schemas.
- **Grants are generated from a table registry** in `packages/db`. A MUTABLE table may carry the attribute **never deleted**, which drops the DELETE grant (masters, users, approval chains, visits, cheques; database design §2.6). The registry classifies each table as:
  - **MUTABLE:** SELECT, INSERT, UPDATE, DELETE for `mos_rw`.
  - **INSERT_ONLY:** SELECT and INSERT only. This covers:
    - the stock ledger and stock transactions;
    - journal entries and lines;
    - AP/AR entries and allocations;
    - `core.audit_event`, `core.audit_seal`, `core.doc_actor`, `core.processed_event`;
    - `qc.result`, `qc.result_invalidation`;
    - `gate.weighing`, `gate.visit_event`, `gate.exit_clearance`;
    - `fin.export_batch_item`.
  - **POSTING:** document headers and lines guarded by `core.guard_posted()` (§8.4).
- Grants never include TRUNCATE, REFERENCES or TRIGGER. **`ALTER DEFAULT PRIVILEGES … GRANT ALL` is forbidden:** it would silently grant UPDATE on insert-only tables.

### 4.3 Guards (replacing the v0.2 tenancy guards)
1. **Privilege boot probe**, run in every environment. It refuses to start if the connected role:
   - is a superuser;
   - owns any MOS object;
   - has CREATE on a MOS schema; or
   - holds UPDATE, DELETE or TRUNCATE on any INSERT_ONLY table.
2. **CI catalog test.** It checks that:
   - every POSTING table has the `guard_posted` trigger;
   - INSERT_ONLY tables grant exactly SELECT and INSERT to `mos_rw`;
   - every table has a primary key;
   - every FK column is indexed.
3. **Tests run as `mos_app`**, never as the owner or a superuser. Otherwise the immutability grants are never exercised.

### 4.4 Expansion seams
Kept on purpose, so that growth is cheap. The full path is in Appendix A.

- All SQL lives in `infra/` repositories; lint bans raw SQL elsewhere.
- There is one unit-of-work entry point for HTTP and one for jobs.
- UUIDv7 ids are used everywhere, so databases from several installations can later be merged.
- The table registry's classification becomes the list of tables that get `tenant_id`.
- Object keys are prefixed with the installation id.
- `core.installation` becomes the future `core.tenant` row.
- Every job payload carries an explicit context; nothing is ambient.
- Hosts and URLs come only from env.
- Company-level export and import of master and opening data exist.
- Caches are keyed with room for a scope prefix.

---

## 5. Organisation and storage model

- **Organisation:**
  - `core.company` holds the profile: legal name, TIN/VAT number, address, logo, base currency, fiscal-year start, time zone and rounding mode.
  - The hierarchy is `core.company` → `core.site` → `core.department` (hierarchical per company, with `head_user_id`).
  - The user profile holds `employee_id`, `home_department_id`, and a default operation unit (`default_site_id`).
- **Storage:**
  - `md.warehouse(site_id, code, name, capacity, capacity_uom_id, temp_controlled, humidity_controlled, storekeeper_user_id, status)`.
  - `md.storage_unit(warehouse_id, code, name, type STORE|SILO|TANK|BIN|YARD|QUARANTINE|REJECT|IN_TRANSIT, parent_unit_id NULL, capacity, capacity_uom_id, geometry_table jsonb NULL, reorder_level, max_level, safety_level, capacity_mode WARN|BLOCK, commingled bool (default true for SILO/TANK), allowed_category_ids, allowed_statuses, restricted bool, allow_receipt, allow_issue, allow_transfer, transfer_requires_approval, count_session_id, status)`.
    - A CHECK enforces safety ≤ reorder ≤ max ≤ capacity.
  - `md.storage_unit_keeper(unit_id, user_id)` lists storekeepers. A restricted unit accepts postings only from its keepers or supervisors.
- **Ledger key:** the inventory ledger's `storage_unit_id` refers to `md.storage_unit`. Bins are storage units whose parent is a store.
- **Current level** of a unit = Σ `stock_balance.qty_on_hand` in its capacity UoM (converted through item conversions, or via bulk density for volume capacity). It is exposed as a view, never stored.
- **Capacity check at posting:**
  - Units in BLOCK mode are locked `FOR NO KEY UPDATE` by inbound postings, so concurrent receipts into one silo serialise.
  - Units in WARN mode are read `FOR SHARE` and warn only.
- **Silo measurement** (FR-INV-020): `inv.silo_measurement(unit_id, measured_at, method DIP|SENSOR, level_value, level_unit, volume_m3, bulk_density, computed_qty, book_qty, variance, count_id NULL)`. Volume comes from the unit's geometry table.
- **Treatment holds** (FR-INV-021): `inv.treatment_hold(scope_type UNIT|LOT, scope_id, reason, chemical, dose, operator, applied_at, release_not_before, released_at, released_by)`. While a hold is open, a STATUS_CHANGE moves the affected stock to BLOCKED with reason FUMIGATION; release is refused before `release_not_before`.

---

## 6. Identity, sessions and authorization (ADR-0005, ADR-0015)

### 6.1 Local sign-in (default)
- **Tables:**
  - `core.local_credential(user_id PK, username citext UNIQUE, password_hash, password_changed_at, must_change bool, failed_count, locked_until, totp_secret_enc NULL, totp_enabled, totp_last_step, recovery_codes_hash[])`
  - `core.auth_policy` (singleton): `local_enabled, oidc_enabled, min_length default 12, lockout_threshold 5, lockout_minutes 15, mfa_required_roles[], reset_mode ADMIN|EMAIL_LINK`.
- **Hashing:** argon2id (m = 64 MiB, t = 3, p = 1, tunable) with a per-hash salt. Parameters live in the hash string, and hashes are rehashed on sign-in when the parameters change. **Concurrent hashing is capped** (a small semaphore), because 64 MiB per attempt is otherwise a memory-exhaustion vector.
- **Lockout:** counters per account in Postgres (authoritative), plus in-memory rate limiting per IP on a single API instance. Every attempt is written to `core.auth_event`.
- **TOTP** (v1): RFC 6238; the secret is encrypted with AES-256-GCM; ±1 step; a replay guard (`totp_last_step`); recovery codes stored hashed. Enforced for the roles in `mfa_required_roles` (e.g. PAP, TRS, ADM).
- **Breached-password check** (v1.1): a bundled list of the most common passwords (top-N, or a Bloom filter). There is no online lookup.
- **Reset:**
  - *Admin reset* sets a temporary password and `must_change`; the user can do nothing until it is changed.
  - *Email-link reset* (only when an email relay is configured) sends a single-use 30-minute token and stores its hash; it goes through the outbox.
- **Never:** passwords in logs, reversible password storage, or security questions.
- **Timing:** constant-time comparison, and the same response time and message for an unknown user and a wrong password.

### 6.2 OIDC sign-in (optional)
1. `GET /api/v1/auth/login` reads the installation's IdP config, then generates `state`, `nonce` and a PKCE S256 verifier. These are stored server-side (`core.oidc_flow`, 10-minute TTL, single use) with a browser-binding cookie, and the browser is redirected to the IdP.
2. `GET /api/v1/auth/callback` checks state and binding, then exchanges the code (`client_secret_post` + `code_verifier`, with a 5 s timeout through a custom fetch with `AbortSignal.timeout`). It verifies the id_token against JWKS (issuer, audience, expiry, nonce), with a JWKS cache and one refetch on an unknown key id.
   - **Email verification is per-IdP config.** Options are a claim name (`email_verified`, or Entra's optional `xms_edov`), or "trust issuer". The real control is binding the invitation to `(issuer, sub)`.
3. **User resolution:**
   - Look up `core.user_identity(issuer, sub)`.
   - A first sign-in succeeds only for an existing or invited user (FR-PLT-011).
   - An email conflict with a different `sub` is refused.
4. **Group-to-role sync** from a configured claim path is v1.1 (FR-PLT-013).
5. **Logout** deletes the session, then performs RP-initiated logout at the IdP's `end_session_endpoint`.

### 6.3 Session and operation unit
- **Session:** both sign-in paths create a `core.session` (an opaque 256-bit id, stored hashed; user, `authz_version`, idle and absolute expiry, user agent, IP). The cookie is `mos_sid` (`HttpOnly; Secure; SameSite=Lax; Path=/`).
- **Operation unit:**
  - After sign-in, the user picks a site from the sites their role grants cover. The step is skipped when only one applies, and it defaults to the remembered one.
  - The choice is stored on the session (`op_site_id`). It sets document defaults and list filters, and **never widens** authorization.
  - It is shown *after* authentication, so an anonymous page never lists the organisation's sites.

### 6.4 Reference IdP (existing SSO) specifics
Optional, and only if the client uses it.
- Register the client `mos` in the SSO's `clientSeed`, with scope `openid profile email ad_groups` and env vars `MOS_CLIENT_SECRET`, `MOS_REDIRECT_URIS`, `MOS_LOGOUT_URIS`.
- **Gate the client behind `MOS_ENABLED=true`.** The SSO's production boot check refuses dev secrets and localhost URIs for *any* seeded client. The portal tile goes behind the same flag.
- **SSO behaviour:**
  - PKCE is mandatory for all clients.
  - Clients authenticate with `client_secret_post`.
  - `ad_groups` needs its own scope.
  - Access tokens are opaque.
  - There is no back-channel logout for new clients and no `client_credentials` grant.

### 6.5 CSRF and browser security
- `SameSite=Lax` cookie plus a double-submit CSRF token (`X-CSRF-Token` header) on every mutating request. Strict CORS to the web origin.
- **CSP lives in the Next.js app:**
  - a per-request nonce set in `proxy.ts` (Next 16), so there are no `unsafe-inline` scripts;
  - HSTS in production;
  - `Referrer-Policy: no-referrer`.

  The API itself answers with `Content-Security-Policy: default-src 'none'` and helmet defaults.

### 6.6 Authorization
- Permissions are strings of the form `module.object.action` (e.g. `proc.po.approve`, `inv.adjustment.post`, `gate.weighing.verify`).
- **Tables:** roles in `core.role` (seeded defaults, editable); grants in `core.user_role(user_id, role_id, site_id NULL)`.
- **Per request:**
  1. A guard loads the session.
  2. The effective permission set comes from a cache keyed by `(user, authz_version)`. Any grant change bumps `authz_version`, so permissions are never stale (FR-PLT-014).
  3. `@RequirePermission()` checks the permission.
  4. Repositories apply `scope.sites(user)`.
- Machine principals (API keys, device credentials) are Later.

---

## 7. Request lifecycle, transactions and concurrency

### 7.1 NestJS request order
Middleware runs first:
1. correlation ID
2. helmet
3. CORS
4. body parsing (with size limits)
5. rate limit

Then:
- **Guards:** session, permission.
- **Interceptors:** `UnitOfWorkInterceptor`.
- **Pipes:** Zod validation. Pipes run **after** interceptors, so validation happens inside the open transaction. That is harmless, because validation does no I/O.
- **Controller.**
- **Exception filter:** AppError → envelope.

### 7.2 `UnitOfWorkInterceptor`
- Built on `nestjs-cls` and `@nestjs-cls/transactional` with the Drizzle adapter. The plugin offers `TransactionHost.withTransaction`; the interceptor is MOS code around it.
- **Mutating routes** get one transaction per request:
  1. open it;
  2. set `statement_timeout`, `lock_timeout = '5s'`, `idle_in_transaction_session_timeout` and `app.user_id` (used by database-side audit helpers) via `set_config(…, true)`;
  3. run the handler;
  4. **commit before the response is serialised.**
- **Plain GETs** use a short read-only transaction.
- **`@NoUnitOfWork()` routes** get none: SSE streams, uploads, downloads, health. Without this, a yard-board TV would hold a pooled connection in an open transaction for hours.
- **Retries:**
  - The whole handler is retried up to 3× with jitter on **40P01** (deadlock). 40001 is retried too, in case a future path uses REPEATABLE READ.
  - **55P03** (lock timeout) is *not* retried; it returns 409 "busy, try again", so a request never takes 20 s.
- **The retry is safe because of a hard rule: no external I/O inside a handler.** Email, PDFs, webhooks and exports go through the outbox, and a lint rule bans HTTP and storage clients in `application/` and `domain/`.
- **Savepoints** (nested transactions) isolate a business failure that must not poison the outer transaction, e.g. GRN preparation inside the tare step. Savepoint handlers catch **only AppError**, never 40P01 or 55P03.
- **Uploads** stream to the BlobStore through a `@NoUnitOfWork` endpoint **before** any business transaction and return a `file_id`. Business commands only reference `file_id`s.
- **Workers** use `runInUnitOfWork(jobCtx, fn)`, with the same guarantees. The job payload carries the context explicitly.

### 7.3 Isolation and lock ranks
READ COMMITTED plus explicit row locks. There is no global SERIALIZABLE: with long posting transactions it causes retry storms.

**Locks are taken in rank order.** Within a rank, rows are taken by fixed table order, then ascending id. `LockService.acquire(rank, table, ids, mode)` is the only place `.for(…)` is allowed (lint rule). A test-mode tracer asserts that ranks never decrease within a transaction.

| Rank | Objects | Mode |
|---|---|---|
| R1 | `core.idempotency_key` | insert, first statement |
| R2 | Process and document headers, then their approval chain. Table order: `gate.visit` → `qc.inspection_lot` → `mfg.work_order` → document headers by doc-type rank (PR, PO, GRN, transfer, adjustment, count, supplier invoice, PV, payment, SO, DO, customer invoice, receipt) → `core.approval_chain` | `FOR NO KEY UPDATE` |
| R3 | Lines and settlement rows: PR/PO/SO/DO/transfer lines, `inv.grn_line_settlement`, `fin.open_item`, `sales.delivery_line_billing` | `FOR NO KEY UPDATE` |
| R4 | Reference rows: `core.period_status` (period × module), `md.supplier_company`, `md.supplier_bank_account`, `sales.customer` (credit), `md.storage_unit` | `FOR SHARE`. `FOR NO KEY UPDATE` for a storage unit at count start or when its capacity mode is BLOCK, and for a period at close |
| R5 | Balances: `inv.item_valuation`, `inv.stock_balance`, `fin.partner_balance` | `FOR NO KEY UPDATE`, sorted by key |
| R6 | `core.number_counter` | `FOR NO KEY UPDATE`, last |

- **`FOR NO KEY UPDATE`** (not `FOR UPDATE`) is used wherever no key changes, so the KEY SHARE locks taken by FK checks on child inserts don't conflict.
- **The PO header (R2) is locked before its lines (R3)** in both GRN posting and amendment approval, so the two cannot deadlock.
- **Master-data checks** at payment (supplier block, bank account) read R4 rows `FOR SHARE`, so a block cannot race a payment.
- **The claim is "deadlocks minimised", not "impossible".** Unique-index waits and deferred checks sit outside the ranks, so 40P01 retry is the backstop.

### 7.4 Idempotency
- Every create, post, decide and process-step request requires an `Idempotency-Key` header.
- `core.idempotency_key(principal_id, key, method_route, request_hash, status, response_code, response_body, expires_at)` has **PK `(principal_id, key)`**. It is inserted with `ON CONFLICT DO NOTHING` as the first statement of the transaction:
  - a replay by the same principal returns the stored response;
  - a concurrent duplicate blocks on the key, then replays;
  - the same key with a different `method_route` or request hash returns 422.
- **Only successful (2xx) responses are stored.** A failed request rolls its key row back, so the client can retry with the same key.
- A daily sweep removes expired keys (TTL 48 h).

### 7.5 Optimistic concurrency
Drafts and master data carry `version`. Updates send `If-Match` / `version`; a mismatch returns 409 with the current state.

---

## 8. Document and posting model (ADR-0007)

### 8.1 Classes and registry
`core.doc_type(code, module, class COMMITMENT|POSTING, requires_approval, number_series_id, gap_free, number_at SUBMIT|APPROVE|POST, reversible, allow_backdate_days_in, allow_backdate_days_out, auto_post_on_approval)`, configurable per company.

*(0.3.2)* In the database design, `core.doc_type` is a global catalogue keyed by `code`, and the per-company settings (approval, auto-post threshold, backdating window, bulk approval) live in `core.doc_type_policy`.

### 8.2 Common header (Drizzle mixin)
`id, company_id, site_id, doc_type, number (NULL until allocated), status, document_date, posting_date, currency_code, fx_rate, external_ref, is_reversal, reversal_of_id, reversed_by_id, approval_chain_id, content_hash, version, submitted_at/by, approved_at/by, posted_at/by, cancelled_at/by, cancel_reason, created_at/by, updated_at/by`.

- Constraint `UNIQUE(company_id, doc_type, number)`. Per-site series must include `{SITE}` in their pattern so they cannot collide.
- Constraint `UNIQUE(reversal_of_id) WHERE is_reversal`.

### 8.3 State machine
```
DRAFT ─submit─► SUBMITTED ─(chain APPROVED)─► APPROVED ─post─► POSTED ─(reversal posted)─► REVERSED
  ▲               │    │                          │
  └─recall/return─┘    └─rejected─► REJECTED      └─cancel─► CANCELLED
Commitment documents: APPROVED ─(PO only) issue─► ISSUED ─► CLOSED
```
- **Stored `status`** holds the approval/posting lifecycle only. **Fulfilment** lives in derived columns (`receipt_status`, `order_status`, `delivery_status`, `billing_status`: NONE / PARTIAL / FULL), maintained under the R3 line locks.
- The FRD's displayed statuses (e.g. PARTIALLY_RECEIVED, PARTIALLY_ORDERED; FRD §1.5) are computed from `status` and the fulfilment columns. This avoids a combinatorial state explosion.
- Every transition is a **status-guarded update** (`WHERE status = $expected AND version = $v`); zero rows affected means 409.
- Allowed transitions live in one typed table per document type. An exhaustive (state × command) unit test covers them.

### 8.4 Immutability and settlement counters
- **Trigger `core.guard_posted()`** runs before update or delete on every POSTING header and line table. It raises if the old status is POSTED or REVERSED, except for the whitelisted POSTED → REVERSED stamp.
- **Grants:** INSERT_ONLY tables (§4.2) have no UPDATE or DELETE for `mos_rw`.
- **`content_hash`:** SHA-256 of the canonical JSON of header and lines at posting, included in the audit event.
- **Counters that must change after posting live in side tables** that are not guarded. This fixes a v0.2 defect, where matching and payment would have been blocked by the immutability trigger.

  | Side table | Holds | Key |
  |---|---|---|
  | `inv.grn_line_settlement` | `qty_invoiced`, `qty_returned`, `qty_debited`, `version` | `grn_line_id` |
  | `fin.open_item` | `amount`, `reserved`, `allocated`, `match_status`, `hold_reasons[]`, `version`, for **every** partner document (supplier and customer invoices, debit and credit notes, vouchers, advances, payments, receipts) | `(source_doc_type, source_doc_id)` |
  | `sales.delivery_line_billing` | `qty_invoiced`, `qty_credited` | `goods_issue_line_id` |
  | `fin.export_batch_item` | journal-entry-to-batch link (insert-only) | `journal_entry_id` |

  CHECKs on the side tables enforce the business rules, e.g. `qty_invoiced − qty_debited ≤ grn_line.qty_payable × (1 + tol)`.

### 8.5 Posting engine
`PostingService.post(docType, docId)` runs inside the request transaction:

1. Lock the header (R2) and check that its status permits posting.
2. Lock and update commitment and settlement rows (R3), e.g. PO lines for a GRN.
3. Run SoD and master-data checks (R4 reference rows `FOR SHARE`).
4. Resolve `posting_date` (default: today in the site's time zone) and check the backdating window. Lock the module's `core.period_status` row `FOR SHARE` (R4) and require it to be OPEN, or SOFT_CLOSED with permission.
5. The doc type's **Poster**, a pure function `document → IntendedEntries`, produces stock entries, AP/AR entries and journal lines.
6. Apply them: `InventoryLedger.apply` (R5), the partner ledger (R5; through `PartnerLedgerPort` for modules below `fin`), and the journal through `JournalPort`.
7. Allocate the number (R6, last lock).
8. Stamp POSTED and `content_hash`. Write `core.doc_actor`, `core.doc_link`, outbox events (`<doctype>.posted`) and the audit event (an unlocked insert; §17.3).

### 8.6 Reversal
- **What it is:** a new document of the same type with `is_reversal = true`. Its lines mirror the original's **ledger entries** (negated quantity, original unit cost, value mode `AT_REFERENCE`); it never re-runs the Poster.
- **Posting date:** the original's date if that period is still open, otherwise the first open date.
- **Dependency guard:** the `core.doc_link` graph must show no posted downstream documents.
- **Fungible stock limitation:** the guard cannot see fungible consumption. If a GRN's stock was partly issued since, the reversal would need more quantity than the bucket holds and is refused (no negative stock); a supplier return or adjustment is used instead.
- **Residuals** at a changed average are handled by the clamp in §9.3.

---

## 9. Inventory ledger (ADR-0008)

### 9.1 Movement model
- **`inv.stock_txn`** (header) groups **`inv.stock_ledger_entry`** rows (SLE).
- Each SLE touches **one bucket** (item, storage unit, lot, stock status) with a **signed** quantity and value.
- Internal moves write two rows sharing a `pair_id`: −q at the source and +q at the destination.
- **Why signed single-sided entries:**
  - status changes, lot splits and moves share one mechanism;
  - receipts and issues need no null "other side";
  - each invariant is one query;
  - value sits on the same row as quantity.
- **Zero-sum rule** for TRANSFER, STATUS_CHANGE and LOT_SPLIT: it is verified **once per transaction in the Poster**. A cheap deferred constraint trigger acts as a backstop; it skips its check when a transaction-local flag says the `txn_id` was already verified, so large counts do not cost quadratic time.

```
inv.stock_ledger_entry(
  id, seq bigint (taken while the key locks are held),
  txn_id, pair_id, company_id, site_id, warehouse_id, storage_unit_id,
  item_id, lot_id NULL, stock_status, move_type,   -- RECEIPT|ISSUE|TRANSFER|STATUS_CHANGE|ADJUSTMENT|COUNT_ADJ|
                                                   -- RETURN_TO_SUPPLIER|REVALUATION|LOT_SPLIT|PRODUCTION_ISSUE|
                                                   -- PRODUCTION_OUTPUT|SALES_ISSUE|CUSTOMER_RETURN
  qty_base core.qty (signed; 0 only for REVALUATION),
  qty_entered, uom_entered_id, uom_factor,         -- traceability snapshot
  unit_cost core.price, value core.amount (signed),
  bucket_qty_after, item_qty_after, item_value_after,   -- running snapshots
  posting_date, posted_at, source_doc_type, source_doc_id, source_line_id,
  reversal_of_entry_id, reason_code_id)
-- indexes: (item, storage_unit, lot, status, seq) (source_doc_type, source_doc_id) (lot, seq)
--          (item, seq) (company, posting_date)
```
*(Column list as design notation; the DDL is written in implementation.)*

### 9.2 Balances and valuation
- **`inv.stock_balance`:**
  - key `UNIQUE NULLS NOT DISTINCT (item_id, storage_unit_id, lot_id, stock_status)`;
  - `qty_on_hand CHECK ≥ 0`;
  - warehouse and site denormalised.
- **`inv.item_valuation`:**
  - **company-level** moving average (FR-PLT-003): PK `(company_id, item_id)`;
  - `qty_total`, `value_total`, `avg_cost`;
  - `CHECK qty_total ≥ 0`, `CHECK value_total ≥ 0`, `CHECK (qty_total <> 0 OR value_total = 0)`.
- **Site-level valuation areas are Later.** The ledger keeps site columns, so a later split is a data migration, not a redesign.
- **`inv.reservation(item_id, site_id, source_doc_type, source_doc_id, qty, status)`:** soft reservations from approved sales orders and released work orders (FR-INV-015).
  - Available-to-use at a site = Σ AVAILABLE on-hand − Σ open reservations.
  - Reserving locks the item's `item_valuation` row (R5), which serialises reservations per item. That is trivial at the NFR-01 volumes.
- **There is no separate valuation-layer table.** Each receipt's cost is already on its RECEIPT SLE, and a later FIFO option derives its layers from there.

### 9.3 `InventoryLedger.apply(entries)`
Each intended entry carries a `value_mode`:

| Value mode | Used by |
|---|---|
| `EXPLICIT` | Receipts (GRN value per FR-INV-022), positive adjustments with a cost, production output at provisional cost |
| `AT_AVG` | Issues, sales issues, production issues, transfer-outs, negative adjustments |
| `AT_REFERENCE` | Reversals, supplier returns, and in-transit receipt or loss at the dispatch entry's cost |
| `PAIRED` | Transfer-in and status-change-in, taking the value of the paired out-entry |

The algorithm:

1. Compute the sorted unique item-valuation keys and bucket keys.
2. `INSERT … ON CONFLICT DO NOTHING` the missing valuation and balance rows, in sorted order.
3. `SELECT … ORDER BY key FOR NO KEY UPDATE` the valuation rows, then the balance rows (R5). The deterministic order prevents lock-order deadlocks between postings.
4. In decimal.js, walk the entries against the in-memory state:
   - **Outbound `AT_AVG`:** if the item quantity reaches exactly zero, value = −(item value), the empty-out rule that removes rounding dust. Otherwise value = round(q × item value ÷ item qty).
   - **Inbound:** value = round(q × unit cost).
   - **Paired:** value = −(the paired entry's value).
   - **Residual clamp:**
     - after every entry, if the item quantity is 0 while its value is not, or if its value would go negative, the difference is taken out **in the same apply** as a REVALUATION entry;
     - the counter-entry posts to PPV for purchase-side documents and to revaluation for others.
     - This generalises v0.2's "excess to PPV" rule to every `AT_REFERENCE` and REVALUATION entry.
   - **Stock check:** if any bucket or item would go below zero → `422 INV_INSUFFICIENT_STOCK`, with bucket detail.
   - **Capacity check:** for units in BLOCK mode, locked in R4.
5. Bulk-insert the SLEs (with `seq` taken while the locks are held), then update the balance and valuation rows with a version check.

### 9.4 Ordering note
`seq` comes from a global sequence, so it is **not** in commit order globally. It **is** in commit order per item, because it is taken while that item's valuation lock is held. Valuation replay depends only on per-item order.

Incremental readers must therefore never use `seq > :cursor`. Journal export claims rows through `fin.export_batch_item` (§13.5).

### 9.5 Lots, statuses, counts, transfers
- **Lots:** `inv.lot(item_id, lot_no UNIQUE per item, supplier_id, supplier_lot_no, source doc, received/mfg/expiry dates, qc_status, grade, attributes jsonb validated against the category's attribute schema)`.
  - Genealogy lives in `inv.lot_link(parent, child, link_type SPLIT|PRODUCE|REGRADE|BLEND, qty, txn_id)`. v1 uses SPLIT, PRODUCE and REGRADE; BLEND is v1.1.
  - Trace is a recursive CTE with a depth limit.
- **Status transitions** are STATUS_CHANGE transactions and are value-neutral.
  - Statuses: QUARANTINE, AVAILABLE, BLOCKED, DAMAGED, REJECTED; allowed transitions per FR-INV-003.
  - Storage units may restrict which statuses they hold.
  - **A status change never changes on-hand quantity** (FR-INV-014).
- **Commingled units** (silos, tanks):
  - Several lot buckets may coexist in one unit.
  - An issue from a commingled unit names no lot. The ledger allocates across that unit's AVAILABLE lot buckets in FIFO order (oldest `lot.received_date` first) inside the same locking step.
  - Each allocation is recorded, which gives production its input lots for traceability (FR-MFG-009).
- **Stock balance report** (FR-INV-014):
  - It reads `stock_ledger_entry`. Opening = Σ qty before the period; received / issued / adjusted = Σ per move-type class within the period; closing on-hand = opening + period Σ.
  - Status columns come from `stock_balance` (current) or the period snapshot plus delta (as-of).
  - Totals are grouped by UoM (FR-RPT-005).
- **UoM:** `md.uom` (dimension factors) and `md.item_uom` (item-specific conversions, immutable once referenced).
  - Lines store `qty_entered`, `uom_entered_id`, a `uom_factor` snapshot and `qty_base`, computed once and rounded to the item's base precision.
  - Secondary quantities (bags, quintals) are informational, except the bag count used for bag tare (FR-PRC-021).
- **Transfers:**
  - Intra-site: one document with paired entries.
  - Inter-site: transfer order → dispatch (source → destination's IN_TRANSIT unit, **valued at dispatch cost**) → receipt `AT_REFERENCE` to the dispatch entry.
  - A short receipt stays in transit until a transit-loss adjustment clears it, at the same reference cost.
- **Counts:**
  - Tables: `inv.count(mode FREEZE|CUT_OFF, cut_off_at)` / `count_line` (snapshot quantity per bucket across all statuses; seq at cut-off) / `count_entry` (count 1, 2, 3 by user).
  - **FREEZE:** starting the count locks the storage-unit rows `FOR NO KEY UPDATE` and sets `count_session_id`. Ledger applies read storage units `FOR SHARE` and refuse frozen units.
  - **CUT_OFF:** no freeze. The variance is counted − (snapshot at cut-off), and postings after the cut-off roll forward into the balance unaffected.
  - **Silos:** counted through `inv.silo_measurement` (§5).
  - **Blind counts:** snapshot quantities are omitted from API responses for users without `inv.count.view_expected`.
  - Variance approval → a generated adjustment document posting COUNT_ADJ at the current average.
- **Period snapshot:** `inv.period_snapshot` holds quantity and value per bucket and item at inventory close. As-of reports read the snapshot plus the ledger delta since.

### 9.6 Valuation specifics and rounding
- **Receipt valuation** (FR-INV-022):
  - GRN value = payable quantity × quality-adjusted price (after grade and deduction effects, FR-QC-013); unit cost = value ÷ physical quantity.
  - **Before-unloading flows:** deductions are known before the GRN, so they create no PPV.
  - **Quarantine flow:** deductions are known after the GRN and are posted as a REVALUATION entry (qty 0, value −deduction) on the on-hand share. The share already consumed goes to PPV.
- **Invoice price ≠ GRN price:** the difference goes entirely to PPV in v1, with no stock revaluation.
- **Numerics:** domains `core.qty numeric(20,6)`, `core.amount numeric(20,4)`, `core.price numeric(20,8)`, `core.factor numeric(24,12)`.
  - Values are strings at the API edge (regex-validated in Zod) and decimal.js brands in TypeScript.
  - An ESLint rule bans `Number()`, `parseFloat` and unary `+` on these brands.
- **Rounding** (one rule, one helper in `domain-kernel`):
  - SLE values and journal lines carry `core.amount` (4 dp).
  - Document amounts shown to users (invoice lines, PV lines, payments) are rounded to the currency's minor unit at line level; totals are sums of rounded lines.
  - The **CSV export** rounds each journal line to the minor unit and adds a per-entry ROUNDING line, so every exported entry balances.
  - The rounding mode is a company setting (default HALF_UP, commercial rounding; HALF_EVEN available).
  - Invariant P9 compares at 4 dp.

---

## 10. Procurement design

- **Supplier status per company:** `md.supplier_company(status, payment_block, terms, currency, withholding_profile, self_billing bool, individual bool, ap_account_key)`. KYC comes from `md.kyc_requirement` and `md.supplier_document`, and the onboarding approval uses the approval engine.
- **Supplier wizard** (client draft):
  - `md.partner` holds `business_reg_no`, `description`, `tin NULL` and `national_id NULL` (for individual suppliers).
  - `md.partner_address(type PHYSICAL|MAILING, country, region, city, postal_code, line)`; `md.partner_contact(name, position, phone, mobile, email, is_primary)`.
  - `md.partner_category(partner_id, category_id)`; `md.bank(name, branch, swift)`.
  - Wizard steps save a DRAFT partner. Submit validates all steps and the required KYC documents. `status` and `registered_at` are system-set.
- **Bank accounts:** `md.supplier_bank_account` stores:
  - the account number, encrypted (AES-256-GCM, app-level, key from env or KMS), plus its last 4 digits;
  - status (PENDING / ACTIVE / INACTIVE / REJECTED);
  - `details_hash`, `usable_from` and the call-back verification evidence.

  Changes go through `md.bank_change_request`: one independent approval (or two, by setting) under the SoD rule, plus the cooling-off period. A PV snapshots `bank_account_id` and `details_hash`, and payment recording re-verifies them (transfers only).
- **Company paying accounts:** `md.company_account(type BANK|CASH|MOBILE, bank_id NULL, account_no_enc NULL, currency, export_account_code, cash_limit NULL, status)`.
- **Approved supplier list:** `proc.approved_supplier_item(supplier, item|category, site, status, validity, qc_spec_id, price_rule_set_id)`, with enforcement per category.
- **PR:** `proc.purchase_requisition` / `_line`.
  - The header holds priority, required date, `department_id` and the requester's employee ID (snapshot).
  - Lines hold `available_qty_snapshot`, `qty_to_purchase`, `qty_ordered_base` (maintained under the R3 line lock) and a specification file or URL.
  - Quantity to purchase comes from an available-to-use query at line entry and is snapshotted on submit.
- **Sourcing:**
  - `proc.sourcing_policy(company, category, amount band, min_quotes, sole_source_allowed)`.
  - `proc.po_quotation(po_id, supplier_id, amount, currency, quoted_at, file_id, selected bool)` records quotations on the PO.
  - PO submit validates the quote count or requires a sole-source justification, which becomes an approval-matrix flag.
- **PO:**
  - The header holds `pr_id` (required unless the `po_without_pr` setting is on), `delivery_terms`, `purchasing_department_id`, `plant_site_id` and `header_discount`, allocated to lines pro rata by net line value with the rounding remainder on the largest line.
  - The `issue` action records `issued_at` and `issued_via EMAIL|PRINT` and queues the PDF email when email is configured.
  - Lines carry tolerances, `excess_policy BLOCK|REQUIRE_APPROVAL`, `qc_spec_id`, `price_rule_set_id` and `approved_excess_qty`, and a CHECK enforces `received − returned ≤ ordered × (1 + over_tol) + approved_excess_qty`.
  - Schedules live in `proc.po_schedule` and generate expected arrivals.
  - PO submission checks the quantity ordered per PR line against the PR tolerance (BR-PRC-05).
- **Amendments:** `proc.po_revision` (a draft copy) with a revision snapshot. Approval locks the header (R2) and then the lines (R3) and applies the revision, which is the same order as GRN posting.
- **GRN:**
  - The header is `inv.goods_receipt(supplier, po NOT NULL, gate_visit_id NULL, inspection_lot_id NULL, vehicle_no, delivery_note_no, warehouse)`.
  - Lines hold: physical quantity; accepted and rejected quantity; `rejected_disposition RETURNED_ON_TRUCK|HELD_AS_REJECTED`; payable quantity; price snapshot; quality-adjusted price; net unit cost; gross/tare/net kg; bag count and bag tare; `batch_no`; `storage_unit_id`; the usage decision; the deduction snapshot.
  - Mutable counters live in `inv.grn_line_settlement` (§8.4).
  - A partial unique index allows one live GRN per (visit or inspection lot, PO line).
  - **Posting writes RECEIPT entries:** the accepted quantity into the item's receipt status, and the held-rejected quantity into REJECTED. A returned-on-truck quantity is not received.
  - **Mixed trucks:** at most one line with `qty_source = WEIGHBRIDGE`. Its quantity = truck net − bags × bag tare − Σ(counted lines × `item.gross_weight_per_base_uom`), and a difference beyond tolerance blocks generation.
  - **Bagged receipts:** the empty-bag disposition per supplier is RETURN, KEEP (a receipt into packaging stock) or CREDIT.
  - **Gate module off:** `gate_visit_id` is NULL, and weights come from the inspection lot (FR-QC-025) or an inline `gate.weigh_ticket`, with the same photo and verification rules; the inspection lot reads the ticket through `WeighTicketReader` (§1.3).
  - Posting emits `grn.posted`.
  - PO received quantities are updated through `ReceiptCommitmentPort` (§1.3), under the PO header (R2) and line (R3) locks.
  - **Service and non-stock lines** (FR-PRC-022) are confirmed as GRN lines with `line_kind = SERVICE`. They write no ledger entries and are matched three ways like goods.

---

## 11. Lab/QC design

- **Specs:** `qc.spec` (a scope key generated from item or category + supplier + site + purpose), with a GiST `EXCLUDE` constraint that forbids overlapping effective ranges among APPROVED specs (`btree_gist`). Spec lines carry bounds, inclusivity, target, mandatory flag, action on failure and the OOS rule. Replicates, aggregation, per-line rounding and verification are v1.1.
- **Spec resolution:** the most specific match wins, effective on the inspection date. The resolved spec, grade scheme and score model are **snapshotted as JSON onto the inspection lot**.
- **Policy:** `qc.item_qc_policy(inspection_point BEFORE_UNLOADING|QUARANTINE|NONE, release_mode IMMEDIATE|HOLD_PENDING_LONG_TESTS, sampling_plan, auto_release_on_pass, retention, coa_required, reweigh_on_reject)`.
- **Inspection lot:**
  - Status: CREATED → IN_TESTING → DECISION_PENDING → DECIDED → CLOSED | CANCELLED, with an `oos_open` flag.
  - Creation paths (FR-QC-006):
    - automatic at first weighing (gate on);
    - **manual by LAB against a PO line and truck (gate off)**, with header weights, bag count and a provisional-net flag;
    - automatic at GRN posting (quarantine);
    - manual for stock re-inspection.
  - Partial unique indexes enforce one live lot per (delivery, item) and per GRN line.
  - Header fields: `inspection_type`, `supplier_doc_no`, `truck_plate`, `gross_kg`, `tare_kg`, `net_kg`, `net_is_provisional`, `bag_count`, `qty_secondary`, and lot attributes (`variety`, `origin`).
- **Results:** `qc.result` is **insert-only**.
  - A correction inserts `qc.result_invalidation(result_id PK, reason, by, at)` and a new result with `supersedes_id`. A result is valid when it has no invalidation.
  - This fixes a v0.2 defect: v0.2 updated the old row, which the insert-only grant forbids.
- **Evaluation:** a pure function `evaluate(snapshot, validResults) → {lineOutcomes, lotOutcome, suggestedDecision, grade, score, scoreBreakdown, deductionPreview}`. It is property-tested for boundary inclusivity, monotonic deductions and monotonic score (a worse value never raises the score).
- **Grading:**
  - `qc.grade_scheme(item|category, version, method WORST_PARAMETER|SCORE_BANDS, status, effective dates)`; `qc.grade(scheme_id, seq, code, label, is_reject)`.
  - `qc.grade_band(grade_id, parameter_id, min, max, accepted_option_codes)` (worst parameter) or `qc.grade_score_band(grade_id, min_score)`.
- **Scoring:** `qc.score_model(item|category, version)` with `qc.score_weight(parameter_id, weight, target NULL, best_value NULL, option_scores jsonb)`.
  - Two-sided sub-score = 100 × (1 − abs(v − target) ÷ abs(limit − target)); one-sided = 100 × (limit − v) ÷ (limit − best). Both are clamped to [0, 100].
  - Score = Σ weight × sub-score ÷ Σ weight, rounded to one decimal (FR-QC-023).
- **Price and weight rules:**
  - `qc.price_rule_set` (versioned) with `qc.price_rule(parameter NULL, basis THRESHOLD_STEP|TIERED|GRADE, threshold, step, rate, effect PRICE_PCT|AMOUNT_PER_BASE_UNIT|PAYABLE_QTY_PCT, cap, reject_above, allow_premium)` and `qc.price_rule_grade(rule_id, grade_id, price_pct)`.
  - **Combination order** (FR-QC-013):
    1. payable-quantity % effects, added per parameter and capped, then capped overall;
    2. price % effects, including the grade differential, added and capped;
    3. amount-per-unit effects.
  - The "no stacking" setting skips per-parameter price effects for grade-defining parameters.
  - Output `{payable_qty_base, adjusted_price, breakdown[], rule_set_version}` is persisted on the decision.
- **Usage decision:** `qc.usage_decision` is **insert-only**. A superseding decision is a new row with `supersedes_id` (unique), and the live decision is `qc.inspection_lot.current_decision_id` *(0.3.2; v0.3 updated a `superseded_by_id` on the old row)*. A concession is its own approval-controlled record, `qc.concession`.
  - The suggested outcome comes from `evaluate`. LAB may confirm it; QAM is required to override it, and for concessions and partial acceptance (FR-QC-012).
  - Effects run **in the same transaction**, through the `UsageDecisionListener` port:
    - gate on: advance the visit;
    - gate off: mark the lot decided and allow GRN preparation;
    - quarantine: status change(s), lot QC status, and revaluation for deductions.
- **Concessions:** routed through the approval matrix. The SoD rule `RESULT_ENTRY_NOT_DECIDER` excludes whoever entered the results.
- **Optional parameters:** spec lines with `mandatory = false` stay hidden until the inspector adds them. Once added, they are evaluated like any other line and included in the score when weighted.
- **Finished-goods QC** (FR-QC-019): the same engine with purpose FINISHED, and lots created by production output. Release, regrade (REGRADE lot link plus a STATUS_CHANGE or item transfer at the same cost) or reject.

---

## 12. Gate and the inbound process (ADR-0009, ADR-0016)

### 12.1 Two process managers
- **(A) The physical visit:** the `gate.visit` row *is* the process instance. It has a state machine, status and version guards, and an append-only `gate.visit_event` log. With the gate module off, the **inspection lot** plays this role for flow C.
- **(B) Settlement:** GRN → supplier invoice or self-billed voucher → match → PV → approval → payment. It is keyed by GRN, invoice and PV, and is triggered from (A) through outbox events.

Every user action is **one local transaction** that runs:
1. a guarded state update;
2. same-transaction calls into the QC, inventory and procurement services, through their application services or ports;
3. a visit event;
4. outbox rows for side effects.

### 12.2 Inbound state machine (behaviour class `INBOUND_PO`, default GRN mode *confirm*)
| # | Transition | Synchronous (same transaction) | Asynchronous (outbox) |
|---|---|---|---|
| 1 | → CHECKED_IN (or HOLD_NO_PO) | Supplier APPROVED and not blocked; PO APPROVED/ISSUED and open for the item; snapshot the inspection point and release mode from the QC policy; one open visit per plate | Notify stores and QC |
| 2 | → FIRST_WEIGHED | Gross weighing (division, range, photo where configured; verification per coverage setting); plausibility ≤ vehicle maximum. **If BEFORE_UNLOADING:** create the inspection lot and sampling plan, and pre-create the `inv.lot` (qc_status PENDING) | Lab queue update (SSE) |
| 3 | → SAMPLED | Record samples; labels (PDF via outbox) | — |
| 4 | → QC_DECIDED | Usage decision in the same transaction (via `UsageDecisionListener`) → UNLOAD_AUTHORIZED (accept / deduct / concession / partial) or REJECTED_AT_GATE (+ rejection exit pass) | Rejection or deduction notice to the supplier |
| 5 | → UNLOAD_AUTHORIZED | **Pre-flight:** PO line open with tolerance headroom (estimated net from tare history), period open, item and storage unit active with capacity, supplier not blocked; assign the unload unit | Stores task |
| 6 | → UNLOADED | Storekeeper confirms unit, bags and damage notes | — |
| 7 | → GRN_DRAFTED (or RECEIPT_EXCEPTION) | Tare weighing; net = gross − tare − bags × bag tare; tare-anomaly check. **Build the GRN as a DRAFT inside a savepoint** and run every posting validation. On a business failure: roll back the savepoint, keep the weighing, set RECEIPT_EXCEPTION and create a `core.process_exception` | STK task; weigh-ticket PDF |
| 8 | → RECEIPTED | STK confirms storage unit, batch and rejected disposition → the GRN posts | `grn.posted` → settlement; GRN PDF |
| 9 | → EXITED | Exit-pass scan; exit-clearance checks. Allowed from GRN_DRAFTED (tare verified), RECEIPTED, REJECTED_AT_GATE, ABANDONED, CANCELLED, or RECEIPT_EXCEPTION with an override pass | — |

**Other transitions:**
- **Mode *automatic*:** step 7 posts the GRN directly (→ RECEIPTED).
- **§9.5 #6, exit without tare:** SSUP records EXITED from UNLOADED with the reason `NO_TARE`. The GRN is drafted with an estimated tare or bag count, flagged, and posting requires PMGR approval. A payment block is set.
- **§9.5 #7, abandoned:** UNLOAD_AUTHORIZED → ABANDONED (SSUP). The inspection lot closes without a GRN; the pre-created lot is voided.
- **SLA:** a GRN draft not generated within the SLA (default 4 h) escalates to WSUP.

**Flows B and C:**
- **Flow B (IN_QUARANTINE)** goes from CHECKED_IN or FIRST_WEIGHED straight to UNLOAD_AUTHORIZED. The GRN (confirm or automatic) posts into QUARANTINE and creates the inspection lot in the same transaction.
- **Flow C (gate off):**
  1. The inspection lot is created manually by LAB. Its weights come from the weigh ticket recorded at the weighbridge station, read through `WeighTicketReader`; where there is no weighbridge they are typed on the lot. The Gate module switch turns off visits, shifts, the yard board and exit clearance, but not the weighbridge station.
  2. After the decision, `UsageDecisionListener` prepares the GRN draft from the lot, pre-filling weights, bags, grade and accepted/rejected quantities.
  3. STK confirms the draft as in step 8.

### 12.3 Settlement (per mode)
- **PV mode *draft* (default):**
  - On `grn.posted`, the consumer re-reads and locks the GRN. **If it is no longer POSTED, it does nothing**; this covers a reversal before the event was processed.
  - Otherwise it creates a **PV DRAFT** assigned to APC, with matched invoices attached, or marked "awaiting invoice". For a self-billing supplier, it prepares the self-billed voucher for APC confirmation (FR-AP-018).
  - Later invoice matches update the draft.
- **PV mode *auto-submit*:** when the invoice for a GRN matches within tolerance, the PV is created and submitted into approval.
- **Guards:**
  - `UNIQUE(source_grn_id) WHERE origin = 'AUTO' AND status NOT IN ('CANCELLED','REJECTED')` means one automatic PV per GRN.
  - Over-reservation is prevented by the `reserved + allocated ≤ amount` CHECK on `fin.open_item`, not by a one-invoice-per-PV rule, because a PV carries many invoice lines and partial payments need follow-up PVs.
  - `core.processed_event(consumer, event_id)` gives consumers exactly-once **database effects**.
  - Business errors (e.g. no approved bank account for a transfer-paid supplier) create a process exception instead of retrying.
- **`grn.reversed`:** a GRN may be reversed while its PV is still a DRAFT, because a draft is not a posted dependant. The consumer removes that GRN's lines from draft PVs and cancels an automatic draft left empty. *(Added in 0.3.1.)*
- **All §9.5 failure cases** are implemented as named scenario tests.

### 12.4 Outbound state machine (behaviour class `OUTBOUND_SALES`, built with sales)
An expected arrival (from the released delivery order; FR-SAL-015) → CHECKED_IN → FIRST_WEIGHED (tare) → LOADING → SECOND_WEIGHED (gross; verified) → GOODS_ISSUED (the delivery order's goods issue posts from the verified net) → EXITED.

- At the verified second weighing, `OutboundLoadListener` hands `sales` the verified net; `sales` prepares the goods-issue draft and DSP confirms it (ADR-0016). `goods_issue.posted` advances the visit to GOODS_ISSUED.
- Exit clearance calls the `OutboundDocumentChecker` port: the goods issue must be posted and the weight within tolerance (BR-GATE-09, not overridable).
- Purchase returns (`OUTBOUND_RETURN`) and material gate passes follow the same pattern with their own documents.

### 12.5 Gate data model
- **Masters:**
  - `gate.gate_post`
  - `gate.weighbridge(capacity, division, min_weight, calibration_due, status, capture_mode MANUAL)`
  - `gate.vehicle_type`; `gate.vehicle(plate_norm UNIQUE)`
  - `gate.vehicle_tare` (keyed by the tractor + trailer combination, validity, superseded_at)
  - `gate.driver`; `gate.blacklist_entry` (with history)
  - `gate.tolerance_rule` (most specific wins); `gate.checklist_template` (versioned)
- **Operations:**
  - `gate.guard_shift` / `_member`.
  - `gate.expected_arrival` (PLANNED → ARRIVED → CLOSED | CANCELLED), generated by an outbox consumer from PO, transfer and delivery-order events.
  - `gate.visit`: behaviour class, state, vehicle, driver, partner, PO or delivery order, delivery note, declared quantity, inspection point snapshot, unload unit, exception flags, version. A partial unique index enforces one open visit per vehicle.
  - Children: `gate.visit_line`, `gate.visit_document`, `gate.visit_seal`, `gate.visit_event` (append-only).
- **Weighing:**
  - `gate.weigh_ticket`: number per weighbridge, **gap-free** (FR-GATE-030); status OPEN / COMPLETE / LOCKED / VOIDED, plus a separate `verification_status` NOT_REQUIRED / PENDING / VERIFIED (a ticket can be LOCKED by its GRN while a deferred verification is still PENDING, FR-GATE-010; the single v0.3 status could not represent that); gross, tare, net; bag count; tare source; payable basis; `locked_by_doc`.
  - `gate.weighing` (append-only): seq, role FIRST|LAST, weight, mode MANUAL|STORED_TARE|PAPER_BACKENTRY, operator, verifier, blind-verify result, photo, `supersedes_id`, reason.
  - `gate.tolerance_evaluation`.
- **Clearance and exceptions:**
  - `gate.exit_clearance` (append-only, one row per attempt with a JSON array of checks), `gate.exit_pass`.
  - `gate.gate_pass` / `_line` / `_movement`, `gate.incident`, `gate.weight_dispute`.
  - `gate.paper_series`, `gate.back_entry_batch`.
- **Timestamps:** every operational record carries both `occurred_at` and `recorded_at`, because back-entry depends on the difference.
- **Flow design:** gate flows are **coded state machines per behaviour class**, with configuration switches. Data-driven flow templates are Later.

### 12.6 Weighing controls (manual)
- **Value checks:** a multiple of the division, within [min, capacity]; the photo is mandatory where a camera is configured (NFR-14).
- **Blind second-person verification:** coverage is configurable (every manual weighing by default, a random %, or above a value). The second person enters the value without seeing the first; a mismatch rejects the verification and logs both attempts. A deferred inbound verification lets the GRN post, but blocks matching and payment.
- **Direction check:** an inbound first weight lower than the last triggers a direction-anomaly hold, except for REJECTED_AT_GATE visits (BR-GATE-06 applies instead).
- **Stored tare:** only when valid and permitted. The **random forced re-tare** decision is made on the server and revealed only at the weighing step; a deviation from tare history raises an alert.
- **Re-weighing** supersedes the earlier weighing. Once the ticket is LOCKED (consumed by a posting), correcting it means reversing the downstream document, voiding the ticket (its number is kept) and issuing a new linked ticket, with approval.
- **Weight disputes** never change the ticket. The resolution creates an AP (or AR) adjustment request.

### 12.7 Paper mode (lean v1)
- `gate.paper_series` holds registered, pre-numbered slip books per post. Back-entry records use `source_channel = BACK_ENTRY` and the slip's `occurred_at`, and require a slip scan or photo plus verification.
- **Gap detection:** every issued number must end used, VOID or LOST; LOST creates an incident.
- **v1.1:** the printed degraded-mode pack, completion SLA timers and retroactive clearance re-evaluation.

---

## 13. Payables sub-ledger and accounting export

### 13.1 Tax
- `md.tax_code(kind VAT|WITHHOLDING|EXCISE|OTHER, rate, calc, base, direction, threshold, threshold_basis INVOICE|CONTRACT, recoverable, moment AT_INVOICE|AT_PAYMENT, effective dates, account_map_key)`, plus `md.tax_group` and `md.withholding_profile` (exemptions, no-TIN rate). *(0.3.2: in `md`, not `fin`, because PO and sales lines in lower tiers use them.)*
- Country rates are **seed data confirmed by the client**, never code.

### 13.2 Supplier invoice and match
- `fin.supplier_invoice` (a POSTING header): `invoice_no_norm`, with `UNIQUE(supplier, invoice_no_norm, fiscal_year)`.
  - Invoices are created as DRAFT, from the invoice list or inline on the PV screen, and **"Submit" posts them**. Matching runs in the posting transaction. A PV cannot include a line whose invoice is on hold.
- Mutable state lives in `fin.open_item`: `amount, reserved, allocated, match_status, hold_reasons[]`, with `CHECK reserved + allocated ≤ amount`.
- `fin.invoice_match(invoice_line, grn_line, matched_qty, matched_amount, price_var, qty_var)` locks the `grn_line_settlement` rows (R3).
- **Expected price** = the GRN's quality-adjusted price snapshot, so grade differentials don't create false price exceptions.
- **Tolerances:** `fin.match_tolerance(scope, price_pct, price_abs, qty_pct, weight_pct, amount_abs)`. Within-tolerance differences post to PPV or ROUNDING.
- **Quality debit notes** (FR-AP-005) and self-billed vouchers use gap-free series. A debit note is created only when the excess equals the recorded deduction within rounding.

### 13.3 Ledger, PVs and payments
- **Ledger:** `fin.partner_entry` (insert-only; signed; company and document currency; `partner_role SUPPLIER|CUSTOMER`) and `fin.partner_allocation`. `fin.partner_balance` is updated under R5.
- **PVs:** `fin.payment_request` / `_line`, with `origin MANUAL|AUTO` and `source_grn_id`.
  - Numbered at **submission** (FRD FR-PLT-041); a cancelled or rejected PV keeps its number.
  - Submission locks the open items (R3) and increments `reserved`.
  - `amount` is a generated column (Σ line amounts to pay); the API rejects a client-supplied total.
  - Lines carry gross, VAT, WHT and net (FR-AP-013). WHT thresholds are assessed on the invoice (or contract) value.
  - Changing `payment_method` after approval resets the PV to SUBMITTED for re-approval.
- **Payments:** `fin.payment(company_account_id, method, cheque_id NULL, cheque_date, bank_reference, amount, amount_in_words, proof_file_id)`, with `UNIQUE(company_account_id, bank_reference)`.
  - `method` must equal the approved PV's.
  - `amount_in_words` comes from a locale-aware formatter (English in v1).
  - Posting takes the supplier and bank rows `FOR SHARE` (R4), re-verifies the bank-detail hash (transfers), and runs:
    - the allocations;
    - the WHT entry and `fin.wht_certificate` (gap-free, VOID kept);
    - the balance update;
    - the journal.
  - `proof_file_id` is required when the `require_payment_proof` setting is on.
- **Cheques:**
  - `fin.cheque_book(company_account_id, from_no, to_no, received_at)`.
  - `fin.cheque(company_account_id, cheque_no, book_id, status ISSUED|VOIDED|CLEARED|STALE|MISSING, payment_id NULL, cleared_at, voided_reason, voided_by/at)`, with `UNIQUE(company_account_id, cheque_no)`.
  - A gap report lists leaves of a registered book that are neither issued nor voided.
  - Voiding a cheque on a posted payment requires the payment reversal.

### 13.4 Journals
- **Generation:** journals are written **in the posting transaction** through `JournalPort` as `fin.journal_entry` + `fin.journal_line` (map key, resolved account snapshot, debit, credit, dimensions).
- **Balance check:** the Poster verifies Σ debit = Σ credit per entry, and a deferred trigger is the backstop (skipped when the transaction-local flag says it is already verified).
- **Account resolution:** `fin.account_map(company, map_key, conditions jsonb, priority, external_account, cost_centre)`.
- **Map keys:**
  - `INVENTORY`, `GRNI`, `IN_TRANSIT`, `WIP`;
  - `AP_CONTROL`, `AR_CONTROL`, `CUSTOMER_ADVANCE`;
  - `PPV`, `VAT_INPUT`, `VAT_OUTPUT`, `WHT_PAYABLE`, `WHT_RECEIVABLE`;
  - `BANK:<id>`, `CASH:<id>`;
  - `ADJ:<reason>`, `COUNT_VARIANCE`, `REVALUATION`, `QC_DEDUCTION`;
  - `COGS`, `REVENUE:<category>`, `PRODUCTION_VARIANCE`, `BY_PRODUCT_CREDIT`;
  - `ROUNDING`, `FX_VARIANCE`.

| Document | Journal |
|---|---|
| GRN | Dr Inventory / Cr GRNI |
| Supplier invoice | Dr GRNI (the invoice's goods value) + Dr VAT input / Cr AP (+ Cr WHT if at invoice). Within-tolerance differences post Dr/Cr PPV against GRNI in the same transaction. Held differences stay in GRNI until resolved (accepted → PPV; supplier credit note → Cr GRNI) and are listed by the GRNI reconciliation (FR-AP-019) |
| Quality debit note | Dr AP / Cr GRNI |
| Payment | Dr AP / Cr Bank or Cash (+ Cr WHT if at payment) |
| Adjustment / count | Inventory ↔ ADJ:reason / COUNT_VARIANCE |
| Transfer via in-transit | Inventory ↔ IN_TRANSIT (at dispatch cost) |
| Supplier return | Cr Inventory / Dr GRNI. When the returned goods were already invoiced, the `supplier_return.posted` consumer drafts a RETURN debit note: Dr AP / Cr GRNI |
| Production issue | Dr WIP / Cr Inventory |
| Production output | Dr Inventory / Cr WIP (at provisional cost) |
| WO settlement | WIP ↔ Inventory (revaluation of the on-hand share) and WIP ↔ PRODUCTION_VARIANCE (the consumed share); by-product credit |
| Sales goods issue | Dr COGS / Cr Inventory |
| Customer invoice | Dr AR (or CUSTOMER_ADVANCE) / Cr REVENUE + Cr VAT output |
| Credit note | The reverse of the invoice lines it credits |
| Customer receipt | Dr Bank/Cash / Cr AR (or CUSTOMER_ADVANCE for a deposit) |

### 13.5 Export
- **Claiming rows:** `INSERT INTO fin.export_batch_item (journal_entry_id, batch_id) SELECT id, $batch FROM fin.journal_entry WHERE posting_date <= $cutoff AND id NOT IN (SELECT journal_entry_id FROM fin.export_batch_item) ON CONFLICT DO NOTHING RETURNING journal_entry_id`.
  - This is insert-only, so the guarded journal rows are never updated.
  - Rows are never selected by id cursor, because late commits would be skipped.
- **Batches:** `fin.export_batch(adapter, status, file_id, row_count, sha256)`. A re-download is byte-identical.
- **Adapters:** v1 has a generic CSV adapter with a configurable column mapping and per-entry ROUNDING lines (§9.6). Package-specific adapters are Later.

---

## 14. Production design (v1, M4–M5)

### 14.1 Masters
- **Work centres:** `mfg.work_centre(site_id, code, name, type CLEANING|MILLING|PACKING|OTHER, input_unit_id, output_unit_id, calendar_id, status)`; `mfg.shift_calendar`.
- **Recipes** (FR-MFG-003):
  - `mfg.recipe(item_id, version, base_qty, base_uom, status, effective dates, allocation_method FIXED_RATIO|RELATIVE_SALES_VALUE, mass_gain_pct_max, mass_loss_pct_max)`.
  - `mfg.recipe_input(recipe_id, item_id, qty_per_base, scrap_pct, issue_method MANUAL|BACKFLUSH, is_packaging)`.
  - `mfg.recipe_output(recipe_id, item_id, role PRIMARY|CO_PRODUCT|BY_PRODUCT|SCRAP|WASTE, expected_yield_pct, yield_min_pct, yield_max_pct, allocation_ratio NULL, by_product_value_per_uom NULL)`.
  - Submission checks that expected outputs fall within the mass-balance band and that allocation ratios sum to 100%.

### 14.2 Work orders
- **Header:** `mfg.work_order(type STANDARD|CAMPAIGN|PACKING, work_centre_id, recipe_id, recipe_snapshot jsonb, planned_qty, planned_start, status, settled_at)`. Lifecycle per FRD §1.5, with row lock R2.
- **Release** snapshots the recipe, creates soft reservations (`inv.reservation`), checks QC holds and prints the work-order sheet (outbox PDF).
- **Issue:** `mfg.wo_issue` (a posting document) writes PRODUCTION_ISSUE SLEs at `AT_AVG`.
  - Silo draw-down names no lot; the commingled-unit FIFO allocation (§9.5) records the lots consumed.
  - Journal: Dr WIP / Cr Inventory.
- **Output:** `mfg.wo_output` (a posting document) writes PRODUCTION_OUTPUT SLEs at a provisional unit cost (the item's reference cost).
  - It creates lots with PRODUCE links from the issued input lots, with initial status QUARANTINE when release is required.
  - It backflushes packaging inputs `AT_AVG`.
- **Backflush shortages** never go negative. They create `mfg.pending_issue(wo_id, item_id, qty, status)`, and the work order cannot be settled or closed while any pending issue is open (FR-INV-011).
- **Milling run data** (FR-MFG-015):
  - `mfg.wo_grist(wo_id, source_unit_id, target_ratio_pct, actual_qty)`.
  - `mfg.wo_conditioning(wo_id, water_litres, conditioning_hours)`.
  - The mass balance = Σ outputs − (Σ inputs + water), checked against the recipe band at completion.
- **Packing orders** (FR-MFG-016) are STANDARD orders whose recipe converts bulk flour into bag SKUs, with packaging backflushed. The net-content checks are QC results on the output lot.

### 14.3 Settlement (FR-MFG-014)
`mfg.wo_settlement` is a posting document, run at completion (or at the campaign end):
1. **Cost pool** = Σ issue values + Σ backflushed packaging values.
2. **By-product credit** = Σ by-product qty × `by_product_value_per_uom`.
3. **Allocate** the remainder to primary and co-products: by `allocation_ratio`, or by relative sales value (qty × current list price).
4. **True-up** per output lot: actual cost − provisional cost.
   - The share still on hand is posted as REVALUATION SLEs.
   - The share already consumed goes to PRODUCTION_VARIANCE.

The output lots' total value then equals the order's net cost (FRD FR-MFG-014 acceptance criterion).

- **Period rule (BR-MFG-01):** inventory close refuses a period that has unsettled work orders with postings in it. A SETTLED order becomes CLOSED at that close.
- **Demand and material view** (FR-MFG-018): a read model over open sales-order lines, `inv.reservation`, released work orders and available-to-use stock. Nothing is stored; full MRP stays Later.

### 14.4 Performance data
- **Shift log:** `mfg.shift_log(work_centre_id, shift, supervisor, notes, handover)`.
- **Downtime:** `mfg.downtime(work_centre_id, period tstzrange, reason_code_id)`, with `EXCLUDE USING gist (work_centre_id WITH =, period WITH &&)`, so entries cannot overlap.
- **Reports:** extraction rate, yield vs band, by-product %, loss, downtime Pareto and production vs plan are computed views over issues, outputs and downtime. No stored KPIs.

---

## 15. Sales and receivables design (v1, M4–M5)

### 15.1 Masters and pricing
- **Customers:** `sales.customer(partner_id PK, group, price_list_id, terms_id, credit_limit, credit_mode NONE|WARN|BLOCK, overdue_block_days, credit_hold bool, sales_rep_id, territory)`. Ship-to addresses are `md.partner_address` rows of type SHIP_TO.
- **Price lists:** `sales.price_list(currency, tax_inclusive, valid_from, valid_to)`; `sales.price_list_line(item_id, uom, price, floor_price)`.
  - Price resolution is deterministic (customer's list → default list) and **snapshotted** on the order line.
  - Line-discount limits per role.
- **Approvals** (FR-SAL-016, FR-SAL-017): customer onboarding, credit-limit changes and price-list versions are document types in the approval matrix (`CUSTOMER`, `CREDIT_LIMIT_CHANGE`, `PRICE_LIST`). The SoD rule type `NOT_DOC_ACTOR` enforces SOD-17 (requester ≠ approver).

### 15.2 Orders, reservations, deliveries
- **Sales order:** `sales.sales_order` / `_line`. Stored status per §8.3, with derived `delivery_status` and `billing_status`.
- **Approval triggers** are approval-matrix flags evaluated at submit: credit exposure over the limit, overdue beyond N days, price below floor, discount over limit, manual hold.
  - **Credit exposure** = `partner_balance` (AR) + delivered-not-invoiced + open approved orders + this order.
  - It is computed under an R4 `FOR SHARE` lock on `sales.customer` and the R5 balance row, then re-checked at delivery release.
- **Reservation:** soft, at approval (`inv.reservation`), released on delivery or cancellation.
- **Delivery order:** `sales.delivery_order` / `_line`.
  - **Release** creates the expected outbound visit (FR-SAL-015).
  - Pick (scan; validates lot status) → load.
  - **Goods issue** is its own posting document, `sales.goods_issue` / `_line`, under the delivery order (a commitment). It writes SALES_ISSUE SLEs at `AT_AVG` (Dr COGS / Cr Inventory). It takes the quantity from the verified outbound net for bulk items.
  - Gate exit follows §12.4.
- **Proof of delivery:** recorded manually with an attachment in v1; a tokenised link is v1.1.

### 15.3 Invoicing and receivables
- **Customer invoice:** `sales.customer_invoice` / `_line`, a posting document with a **gap-free** number at posting.
  - Its lines come only from posted goods-issue lines, tracked in `sales.delivery_line_billing`. Its open item and partner entry are written through `PartnerLedgerPort`.
  - Fields `fiscal_receipt_no` and `fiscal_machine_id` hold the external fiscal reference (FR-SAL-012).
  - Journal: Dr AR / Cr REVENUE + VAT output.
- **Credit note:** `sales.credit_note` / `_line`, linked to the invoice lines it credits, with `CHECK credited ≤ invoiced` in `delivery_line_billing`. It is approved through the matrix and posts on approval. A credit note without a return (FR-SAL-018) carries a reason and a mandatory attachment.
- **Cash sale:** a sales order flagged CASH. The gate's exit clearance requires a posted receipt covering the invoice, and (by setting) the fiscal number.
- **Customer receipt:** `sales.receipt` (method CASH|TRANSFER|CHEQUE|MOBILE, `company_account_id`, cheque details and clearance) with `fin.partner_allocation` to invoices. A bounced cheque posts a receipt reversal that reopens the invoices.
- **Customer advances:** a receipt with purpose ADVANCE credits CUSTOMER_ADVANCE and is allocated at invoicing. Release of a cash-before-delivery order requires advance cover.
- **Cash sessions:** `sales.cash_session(cashier_id, company_account_id (cash box), opened_at, float, closed_at, counted, variance, reason)`. Receipts by CSH require an open session.
- **Returns:** an inbound customer-return visit → receipt into QUARANTINE (CUSTOMER_RETURN SLE at the original COGS cost, `AT_REFERENCE` to the sales issue) → QC decision → credit note.

---

## 16. Approvals and SoD (ADR-0010, amended by ADR-0018)

### 16.1 Approval matrix (v1)
**`core.approval_matrix`**, one row per approval stage:

| Column | Meaning |
|---|---|
| `doc_type` | Document type the row applies to |
| `company_id` NULL | Company (NULL = any) |
| `site_id` NULL | Site (NULL = any) |
| `category_id` NULL | Item category (NULL = any) |
| `department_id` NULL | Department (NULL = any) |
| `min_amount`, `max_amount` | Amount band |
| `flag` NULL | `SOLE_SOURCE`, `BANK_CHANGED_RECENTLY`, `VARIANCE_OVER_TOLERANCE`, `PRIORITY_URGENT`, `BELOW_FLOOR_PRICE`, `CREDIT_EXCEEDED`, `DISCOUNT_OVER_LIMIT` |
| `stage_seq` | Order of the stage in the chain |
| `approver_role_id` | Role whose holders can approve |
| `sla_hours` | Time allowed before escalation |
| `fallback_role_id` | Role that receives the step on escalation |
| `version`, `active` | Versioning |

**How it is evaluated:**
- Each doc type registers a typed facts provider (amount in company currency, site, category, department, flags).
- Every row whose band and scope match contributes a stage.
- Within a stage, **any one** eligible holder of the role, in scope and after SoD exclusion, may decide.
- **v1.1:** general JSON-AST conditions, parallel steps and ALL/QUORUM completion.

**Chains** keep the v0.2 structure:
- `core.approval_chain(doc_type, doc_id, doc_content_hash, matrix_version, facts snapshot, status)`, with one live chain per document (partial unique index);
- `core.approval_step(stage, status, due_at, escalation_level)`;
- `core.approval_assignment(step, user, source MATRIX|DELEGATION|ESCALATION|REASSIGN, on_behalf_of, status, comment)`.

Decisions are rows of the insert-only table `core.approval_decision`; chains, steps and assignments are never deleted. History is immutable (FR-PLT-054).

### 16.2 Algorithm
- **Submit:**
  1. Lock the document (R2).
  2. Evaluate the matrix.
  3. Materialise stage-1 assignments after SoD exclusion.
  4. Run the "can anyone decide" check per stage. If it fails, use the fallback role, or return `APPROVAL_UNSTAFFED`.
  5. Lock the document for editing.
  6. If no row matches and the doc type allows it, auto-approve with a system actor.
- **Decide:**
  1. Lock the document, then the chain (R2 table order).
  2. Run a status-guarded update of the assignment.
  3. Any rejection rejects the chain; **request changes** returns the document to DRAFT (history records RETURNED).
  4. On approval, activate the next stage.
  5. When the chain is APPROVED, run the doc type's `onApproved` hook in the same transaction (status; number if `number_at = APPROVE`; optional auto-post).
- **Re-check at decision time:** the user is active, holds the permission, passes SoD and (where the role requires it) has TOTP enrolled.
- **Recall** by the submitter cancels the chain and returns the document to DRAFT.
- **Bulk approve/reject** (FR-PLT-032):
  - documents are sorted by id;
  - each is decided in its own **savepoint**, with a per-item result (approved / refused with reason);
  - one idempotency key covers the bulk call;
  - it is disabled per doc type by setting (off for PVs and bank changes by default).

### 16.3 Delegation and escalation
- **Delegation:** `core.approval_delegation(delegator, delegate, doc_types, max_amount, validity)`.
  - Delegations do not chain.
  - SoD applies to both delegator and delegate.
  - Creating a delegation back-fills pending steps.
- **Escalation:** a scheduled job (Job Scheduler or pg-boss cron, §17.2) runs every 5 minutes.
  - Level 1 sends a reminder; level 2 adds the fallback role's holders as assignees.
  - Each level is a guarded increment, so re-runs are idempotent.
  - SLAs use wall-clock time in v1; business-hours calendars are v1.1.

### 16.4 SoD engine
`core.sod_rule(code, rule_type, params, doc_types, severity BLOCK|WARN_JUSTIFY, enabled)`. The rule types form a closed set:

| Rule type | What it enforces |
|---|---|
| `NOT_DOC_ACTOR` | The approver did not create or submit the document |
| `CROSS_DOC_DISTINCT` | One user may not hold both actions of a pair across linked documents: PO.CREATE↔GRN.POST, GRN.POST↔PV.APPROVE, PO.CREATE↔PV.APPROVE, PV.APPROVE↔PAYMENT.POST, GRN.POST↔SELFBILL.CONFIRM, WO_OUTPUT.POST↔FG.RELEASE |
| `MASTER_EDITOR_NOT_APPROVER` | Whoever edited a master record (e.g. bank details) cannot approve that change |
| `ONE_STAGE_PER_USER` | One user cannot approve two stages of the same chain |
| `RESULT_ENTRY_NOT_DECIDER` | Whoever entered QC results cannot approve a concession on them |
| `ROLE_CONFLICT` | Conflicting role combinations: reported in v1, blocked in v1.1 |

- **Data sources:** `core.doc_actor` (append-only) and `core.doc_link`.
- **Where checks run:** when assignments are materialised, at decision time (authoritative), and in operational services (`SodService.assert(action, doc, user)`).

---

## 17. Other platform services

### 17.1 Numbering (ADR-0011)
- **Tables:** `core.number_series(company, doc_type, site NULL, scope_ref NULL, pattern, reset, gap_free)` and `core.number_counter(series, period_key, last_value)`.
  - `scope_ref` scopes a series to one object. Weigh tickets use it to be gap-free **per weighbridge** (FR-GATE-030), which a site-only scope could not express. *(Added in 0.3.1.)*
  - The period key derives from the **posting date** (or submission date for PVs) through the fiscal calendar.
  - Pattern tokens: `{FY}`, `{YYYY}`, `{YYYYMMDD}`, `{SITE}`, `{WB}` (weighbridge code, for scoped series), `{SEQ:n}`.
- **One mechanism for every series:** `INSERT … ON CONFLICT DO NOTHING` → `SELECT … FOR NO KEY UPDATE` (R6, the last lock) → increment, **in the same transaction** as the document.
  - A rollback rolls back the increment, so numbers stay contiguous.
  - v0.2 allocated "loose" series in a separate transaction. That needed a second pooled connection per request and could starve the pool.
  - At the NFR-01 volumes, contention on counter rows is negligible.
- **A numbered document keeps its number** forever: a cancelled PV or a voided weigh ticket or cheque stays visible as CANCELLED or VOID, so the sequence has no gaps.
- **Drafts** show `DRAFT-<short uuid>`.
- **Default patterns** follow the client draft's style:
  - `SUP-{SEQ:5}`, `PR-{FY}-{SEQ:5}`, `PO-{FY}-{SEQ:5}`, `INS-{FY}-{SEQ:6}`, `GRN-{FY}-{SEQ:6}`;
  - `BATCH-{YYYYMMDD}-{SEQ:2}`, `ISS-{FY}-{SEQ:5}`, `ADJ-{FY}-{SEQ:6}`, `CNT-{FY}-{SEQ:5}`;
  - **`PV-{FY}-{SEQ:6}`** (the draft's `PR-` clashed with purchase requisitions), `PAY-{FY}-{SEQ:6}`;
  - `WO-{FY}-{SEQ:5}`, `SO-{FY}-{SEQ:5}`, `DO-{FY}-{SEQ:5}`, `INV-{FY}-{SEQ:6}`, `RCT-{FY}-{SEQ:6}`, `CN-{FY}-{SEQ:5}`.

  - Added in 0.3.1 (process model §8.1): `GV-{FY}-{SEQ:6}` gate visit, `WT-{WB}-{SEQ:7}` weigh ticket, `SINV-{FY}-{SEQ:6}` supplier invoice (internal), `DN-{FY}-{SEQ:5}` debit note, `SBV-{FY}-{SEQ:6}` self-billed voucher, `WHT-{FY}-{SEQ:6}` WHT certificate (the tax authority's format is a client question), `SRN-{FY}-{SEQ:5}` supplier return, `TRF-{FY}-{SEQ:5}` transfer, `CR-{FY}-{SEQ:5}` customer return, `GP-{FY}-{SEQ:5}` gate pass, `S-{FY}-{SEQ:7}` sample.

  The client-draft review §8 compares these with the formats seen in the client's draft; process model §8.1 lists every document's default.

### 17.2 Outbox, queue and exceptions (ADR-0006)
- **`core.outbox_event`** is written in the business transaction: `id, aggregate, event_type, payload, status PENDING|CLAIMED|DONE|DEAD, attempts, process_after, claimed_until`.
- **Relay** (queue options A and B):
  - **Claim:** `UPDATE … SET status='CLAIMED', claimed_until=now()+'5 min', attempts=attempts+1 WHERE id IN (SELECT id … WHERE (status='PENDING' AND process_after<=now()) OR (status='CLAIMED' AND claimed_until<now()) ORDER BY id LIMIT $n FOR UPDATE SKIP LOCKED) RETURNING *`. The lease makes a crashed relay's claims recoverable.
  - **Enqueue** with `jobId = event.id`.
  - **Mark DONE** only when the **consumer** succeeds, in the same transaction as `core.processed_event`.
  - **Redis durability:** with Redis, `appendonly yes` is required, so a Redis restart doesn't lose dispatched jobs.
- **Option C (pg-boss):** jobs are enqueued **inside the business transaction** through its Drizzle adapter, and the relay disappears.
- **Ordering** is not guaranteed across events. Consumers therefore **re-read and lock the source document** and do nothing if it is no longer in the expected state (e.g. a GRN reversed before its `grn.posted` event ran).
- **Error handling:**
  - Technical errors are retried with backoff; dead letters raise an alert.
  - Business errors write `core.process_exception` and mark the event processed.
- **Effects:** "exactly once" holds for **database effects** only. Email and PDF delivery are at-least-once; a notification dedupe key in `processed_event` prevents duplicate sends in practice.
- **The queue backend is BullMQ 6 on Redis/Valkey** (decided in Phase 6, ADR-0021). Code still depends only on the `JobQueue` port (`enqueue(tx, job)`, `schedule(name, cron)`), so a later swap stays an adapter change. The option table below is kept as the record of the trade-off.
- **Event catalogue:** every event type, with its producer and consumers, is listed in [process model §9.2](../2-requirements/MOS-PROCESS-MODEL.md#92-event-catalogue-outbox), and the scheduled jobs in §9.3. Events are named `<aggregate>.<past-tense verb>`; a new event type is added to that catalogue first.

| Option | For | Against |
|---|---|---|
| **(A)** Redis 7.2 or Valkey + BullMQ 6 + outbox relay | Most battle-tested; house precedent (BullMQ) | One more stateful service to run, back up and secure at the client's site; the relay, lease and AOF are needed; Redis 7.4+ licensing (RSAL/SSPL, AGPL on 8.x) vs Valkey (BSD) |
| **(B)** BullMQ 6 PostgreSQL backend | Same API; no Redis; Postgres 13+ | New (released 2026-07-30); enqueue inside the app's own transaction is not documented, so the relay is still needed |
| **(C)** pg-boss | Postgres only; enqueue **in the business transaction** (no relay); cron schedules, retries with backoff, dead-letter queues | Polling latency; load on Postgres; not the house standard |

At the NFR-01 volumes (about 5,000 movements a day), all three are comfortably sufficient.

### 17.3 Audit (sealed log)
- **Writes:** `core.audit_event` rows are inserted **without any lock** as part of each transaction. Each row carries its details (personal data in free text is pseudonymised) and `pg_current_xact_id()`.
- **Sealing:**
  1. A worker job runs every few minutes.
  2. It takes all unsealed rows whose transaction id is below the current snapshot's `xmin`, i.e. committed and stable.
  3. It orders them by (txid, id).
  4. It writes a `core.audit_seal(seq, from_id, to_id, count, hash, prev_hash, hmac)` row. HMAC-SHA256 uses a key from env or KMS.
- **Verification:** a daily job verifies the seal chain and raises an alert on a break.
- **Why this design:** it keeps tamper evidence (FR-PLT-046) while removing v0.2's advisory lock and shard design from the hot path, and it avoids the commit-order trap.
- **Retention:** 10 years.

### 17.4 Attachments (ADR-0013)
- **Upload pipeline** (`@NoUnitOfWork`):
  1. stream upload;
  2. content-type sniffing;
  3. size limit;
  4. optional virus scan (fail-open or fail-closed by setting);
  5. BlobStore key `<install_id>/<yyyy>/<uuid>`;
  6. `core.attachment(storage_key, sha256, mime, size, av_status, entity_type, entity_id)`.
- **Downloads** are streamed through the API after an authorisation check. Presigned URLs are used only with the S3 adapter.
- **Orphan sweep:** files uploaded but never referenced for more than 24 h are removed.

### 17.5 Notifications
- **In-app:** `core.notification`, with an SSE badge.
- **Email** (only when an SMTP relay is configured): nodemailer via a template registry, sent by an outbox consumer with a dedupe key. Per-user opt-out for non-critical email.
- **SMS:** a pluggable provider interface, Later.

### 17.6 PDFs and labels
- pdfme templates per company (seeded by the industry pack), rendered in the worker from outbox events.
- Documents embed a QR code carrying the document id plus a short hash. Labels use Code128 or QR.
- Direct ZPL printing is Later.

### 17.7 Real-time updates
SSE endpoints (`@NoUnitOfWork`) stream events from an in-process emitter, published after commit (single API instance). If the API is ever scaled out, Postgres `LISTEN/NOTIFY` fans events out between instances.

### 17.8 Import and export
- XLSX/CSV import templates per entity. A **dry run** validates every row and returns an error report; commit is one transaction per batch (all or nothing).
- **Opening balances** are imported as dedicated `OPENING_BALANCE` receipts with explicit costs, plus opening AP/AR open items.

---

## 18. Quality engineering

### 18.1 Test pyramid
| Layer | Tools | What |
|---|---|---|
| Unit | Jest or Vitest | Domain logic: state machines, posters, QC evaluator, price and weight rules, scoring, approval matrix, settlement allocation, decimal and rounding helpers |
| Property-based | fast-check + a real Postgres **as `mos_app`** | Ledger model test (random command sequences, concurrent workers) with invariants P1–P9 and P13–P15 |
| Integration | supertest | Module APIs, grants and immutability, idempotency, SoD, numbering under concurrency, site scopes |
| Scenario / acceptance | API-level tests + Playwright (UI smoke) | FRD acceptance scenarios AS-01…AS-19 and the §9.5 inbound failure cases |
| Security | Automated tests plus review | Privilege probe, catalog test, immutability (UPDATE/DELETE as `mos_app` fails), authz matrix, CSRF, upload validation, local-account attacks |
| Performance | k6 (ad hoc in WP-13) | Posting p95 under concurrent gate, warehouse, production and sales load |

### 18.2 Ledger and finance invariants
Invariants P1–P9 and P13–P15 are **property or concurrency tests**. P10–P12 and P16–P22 are example-based tests.

| # | Invariant |
|---|---|
| P1 | For every bucket, balance qty = Σ SLE qty |
| P2 | For every item, qty = Σ bucket qty, and value = Σ SLE value |
| P3 | No bucket or item is ever negative; rejected commands leave no partial rows |
| P4 | A reversal nets to zero in qty and value (± an exact revaluation entry) |
| P5 | Zero quantity ⇒ zero value; value is never negative |
| P6 | Internal moves are zero-sum per transaction |
| P7 | Running snapshots equal the per-item prefix sums |
| P8 | Every journal entry balances |
| P9 | Journal inventory + in-transit + WIP = Σ SLE value per company (at 4 dp) |
| P10 | GRNI = uninvoiced GRN value |
| P11 | Partner balance = Σ partner entries (suppliers and customers) |
| P12 | Open invoice amount ≥ 0, and reserved + paid ≤ payable |
| P13 | PO received ≤ ordered × (1 + tol) + approved excess, under parallel GRNs |
| P14 | Gap-free series are exactly 1..n under faults |
| P15 | An idempotent replay gives identical state and response |
| P16 | Unit conversion invariance |
| P17 | QC evaluation boundaries; monotonic deductions and score |
| P18 | Approval determinism; SoD never violated; no unstaffed chain |
| P19 | Invoiced qty ≤ delivered qty per delivery line; credited ≤ invoiced |
| P20 | Receipt allocations ≤ receipt amount and ≤ invoice open amount |
| P21 | Settled output value = work-order net cost (issues + packaging − by-product credit) |
| P22 | Reserved ≤ available on-hand per item and site at reservation time |

### 18.3 Targeted concurrency tests
- Parallel issue of the last unit.
- Parallel GRNs at the tolerance edge.
- Opposite transfers A→B and B→A in parallel.
- Two receipts into a BLOCK-mode silo at capacity.
- Period close racing a posting; count start racing a posting.
- Two approvers on the same stage.
- Duplicate delivery of `grn.posted`; a GRN reversal racing its `grn.posted` consumer.
- Parallel reservations of the last available quantity.

### 18.4 CI gates (per commit)
- Typecheck; lint (including the dependency-cruiser tiers, the decimal lint rule and the `LockService`-only rule); unit tests and property tests.
- Integration tests on PostgreSQL 18, as `mos_app`.
- Privilege and catalog tests.
- Migration-safety check (ported from DMS): DROP, RENAME or SET NOT NULL fails unless the migration is annotated `-- SAFE:`.
- Frontend production build.
- npm audit gate (no new high or critical findings).
- Playwright smoke tests.
- *(OpenAPI contract diff: deferred until a public API exists.)*

---

## 19. Frontend architecture (ADR-0012)

- **Stack:** Next.js 16 App Router, mostly client components behind authentication. TanStack Query for server state; Zustand for small UI state.
- **Structure:**
  - thin routes in `app/`;
  - domain logic in `features/<domain>/`;
  - a shared UI kit in `shared/ui` (Radix primitives + Tailwind, hand-built in the shadcn style; not shared with other systems, per house rule).
- **Building blocks:**
  - `DataGrid`: TanStack Table with server-side pagination, sort and filter, column chooser, XLSX export.
  - `DocumentPage`: header, lines grid, status badge, an action bar driven by the transitions the API allows, approval timeline, attachments, audit tab.
  - `LineItemsForm`: react-hook-form `useFieldArray` + Zod schemas from `packages/contracts`.
  - `ScanInput`: a focused field that accepts keyboard-wedge input and resolves lot, storage unit, item, sample or visit codes. Typing works too.
  - `ApprovalsInbox` (KPI tiles, bulk).
  - `YardBoard`: SSE-driven; TV mode is v1.1.
  - `LabResultGrid`: keyboard-first, live evaluation, "Calculate quality".
  - `LineSupervisor`: large-target production screens.
- **API client:** typed fetch helpers built from the shared Zod contracts. Every mutation carries an `Idempotency-Key`, generated per form submission and kept across retries.
- **House Next 16 rules:**
  - build-time env fingerprint (`scripts/build-fingerprint.cjs`);
  - `turbopack.root` pinned;
  - native eslint flat config (no FlatCompat);
  - React Compiler hook rules;
  - `useResetOnChange` with stable dependencies.

  `NEXT_PUBLIC_*` values and `basePath` are baked at build time, so config changes need a rebuild, which the fingerprint enforces.
- **CSP:** a per-request nonce set in `proxy.ts` (§6.5).
- **Accessibility:** semantic HTML, labels, focus states and contrast (WCAG 2.1 AA). Tablet stations use ≥ 48 px targets.
- **i18n hooks:** every string goes through a message catalog (`next-intl`), and dates through one formatter with a calendar adapter interface (Gregorian in v1).

---

## 20. Environments, configuration and deployment

### 20.1 Configuration contract (house rule, from day one)
- **Which file is read:** `NODE_ENV` is read before any env file loads. Production reads `.env.production` **only**; development reads `.env`. Files are never layered.
- **Validation:** Zod validation at boot. Production **refuses to start** on a missing file, placeholder values, localhost browser URLs or dev secrets.
- **Verify without starting:** `npm run env:verify:prod` checks the production config without booting anything.
- **No hardcoded config:** every host, port and URL comes from env, with guarded dev fallbacks only. dotenv loads with `override: true`, because a machine-wide `DATABASE_URL` has bitten this platform before.
- **Only the development environment is operated** until the hosting decision (ADR-0019).

### 20.2 Development environment (planned)
- API and web ports are chosen in WP-00, after checking Windows excluded port ranges, and kept in `.env`. 7500/7501 are proposed; 7400 (the retired MRS) is avoided.
- Dev dependencies: PostgreSQL 18, Redis or Valkey, and Mailpit. These run on non-default host ports; Docker Compose for dev dependencies is a developer convenience, not a production decision.
- Identity: local accounts. Optionally, the reference SSO on its dev port, with the `mos` client registered behind `MOS_ENABLED`.

### 20.3 Deployment options (ADR-0019, defaults from ADR-0022)

**Defaults decided in Phase 6** ([ADR-0022](../6-decisions/adr/ADR-0022-hosting-and-operations-baseline.md)):
- **On-premises first,** on the client's Windows Server or Linux server.
- **Both packagings are documented and tested,** and the client chooses: native services (PM2 optional) or Docker Compose.
- **Environments:** development, staging (UAT and restore drills) and production.
- **Backups:** nightly full plus WAL archiving; the attachments and the two keys backed up separately; a weekly restore test on staging.
- **CI:** GitHub Actions on a private repository.

Redis/Valkey is part of every shape below. The table stays as the menu for the client's answers.
| Option | What it needs from the client | Shape | Backup / DR (proposed) |
|---|---|---|---|
| **On-prem, Windows Server** | A server or VM, Windows Server, local admin access, backup target | Node processes as services (the house model: startup script, optional PM2), PostgreSQL as a Windows service, IIS or Apache as the reverse proxy, local-disk storage | pg_basebackup + WAL archiving, or nightly pg_dump, to an off-box target (NAS); restore runbook; drill before go-live |
| **On-prem, Linux** | A server or VM, a Linux distribution, admin access | systemd services or Docker Compose (api, worker, web, postgres, optional queue backend), nginx as the reverse proxy | As above (WAL-G possible) |
| **Virtual server** at a local provider or cloud | A provider account, a domain, internet quality at the plant | As on-prem Linux; managed PostgreSQL if available | Provider snapshots + WAL archiving to object storage |
| **Managed cloud** | A cloud account, budget, data-residency approval | Containers on a managed platform, managed PostgreSQL with PITR | Provider PITR and a cross-region backup copy |

**Upgrades:**
- Versioned release artefacts, then a one-shot migration job run as `mos_migrator`.
- Migrations follow expand/contract: additive first, destructive only after the code no longer depends on the old shape.
- Rollback means redeploying the previous artefact, which expand/contract makes safe.

### 20.4 Observability
- Pino JSON logs with the correlation ID.
- `/metrics`: request rate and latency, outbox depth and age, dead letters, queue lag, posting durations, lock waits.
- `/api/v1/health`: DB, Redis, worker heartbeat.
- **Alert rules:** dead letter created; outbox age > 5 min; audit-seal break; backup job failure; disk above 80%; worker heartbeat lost; `core.audit_event` above 40 M rows (partitioning trigger, ADR-0021).
- The alert channel (email, SMS, monitoring tool) depends on the client's infrastructure.

---

## 21. Security design summary

| Threat | Control |
|---|---|
| Cross-site data access by a scoped user | Repository scope helper, site-scope tests (AS-11), operation unit never widens scope |
| Tampering through the database role | App login is neither superuser nor owner; insert-only grants; `guard_posted` trigger; privilege boot probe; catalog test |
| Privilege escalation | Server-side permission checks per request; `authz_version` bump; admins cannot grant permissions they don't hold |
| Session theft / CSRF | HttpOnly + Secure + SameSite=Lax cookies; CSRF double-submit token; sessions stored hashed; 8 h idle and 12 h absolute limits |
| Credential attacks on local accounts | argon2id with a concurrency cap, lockout, non-enumerating responses, TOTP for sensitive roles, audit of every attempt |
| Procurement fraud (fake suppliers, bank redirection) | KYC approval, SoD, bank changes with an independent approver and call-back, cooling-off, hash re-check at payment, payment method fixed at approval, exception reports |
| Weighbridge fraud | Blind verification, photo evidence, forced re-tare, tare analytics, calibration enforcement, gap-free tickets |
| Cash and cheque fraud | Cash-box limits, cash sessions with counted close, cheque books with gap detection, voids kept |
| Tampering with records | Immutable postings, insert-only ledgers and results, sealed audit log, content hashes |
| Malicious uploads | Content sniffing, size limits, optional AV scan, downloads through the API, no inline rendering of untrusted HTML |
| Injection | Parameterised SQL (Drizzle), SQL only in repositories, Zod validation on every input, typed approval-matrix facts (no eval) |
| Secrets leakage | Env or KMS only, redaction in logs, placeholder refusal in production, column-level REVOKE for `mos_readonly` |
| Dependency risk | npm audit gate, pinned versions with comments on overrides, no `audit fix --force` |

---

## 22. Key technical risks and mitigations

| Risk | Mitigation |
|---|---|
| Ledger or valuation bugs found late | The ledger engine and its property harness are built **before** any inventory UI (WP-05); invariants run in CI |
| Immutability blocking legitimate updates | Settlement counters live in side tables (§8.4); the catalog test classifies every table |
| Deadlocks under load | Lock ranks, the `LockService`-only rule, `FOR NO KEY UPDATE`, 40P01 retry |
| NestJS transaction propagation mistakes | One interceptor owns the transaction; lint bans ad-hoc `db.transaction()` in controllers; `@NoUnitOfWork` for streams |
| Version churn (NestJS 12, BullMQ 6, Node 24) | Decided in Phase 6 (ADR-0021); pinned versions; upgrade windows after go-live |
| Queue backend unknown | The `JobQueue` port; the outbox semantics hold under all three options |
| Next.js build-time env baking | The build fingerprint refuses stale builds |
| Scope creep through "generic" configurability | Closed rule types (approval matrix, SoD, price rules); anything outside them is a code change reviewed against the FRD |
| Production and sales designed without client input | Month-end demos; client workshops before M4 and M5; recipe and price data as configuration |

---

## Appendix A. Expansion playbook: from one installation per company to multi-tenant SaaS

This appendix preserves the v0.2 multi-tenant design (ADR-0004, superseded by ADR-0017) as the reviewed target, and gives the steps to reach it.

**Stage 1: one instance per customer.** Each customer gets its own database and application stack. The work is:
- scripted provisioning;
- upgrade fan-out across instances;
- backups per instance;
- per-customer configuration export and import.

It needs no code changes.

**Stage 2: multi-company in one installation.** Allow more than one `core.company` row and show the company pickers. The schema already carries `company_id`. Site-level valuation areas can be added at the same time.

**Stage 3: pooled multi-tenant SaaS (shared database).** Only if the business case appears.
1. Create `core.tenant` from `core.installation`.
2. Add `tenant_id uuid NOT NULL DEFAULT '<install_id>'` to every table the registry classifies as organisation-owned, using expand/contract.
3. Add `UNIQUE(tenant_id, id)` and composite foreign keys `(tenant_id, x_id) → (tenant_id, id)`. Use `NOT VALID` then `VALIDATE CONSTRAINT` to avoid long locks.
   - This step matters because **referential-integrity checks bypass RLS**: a single-column FK would let a guessed UUID point at another tenant's row.
4. Prefix business unique keys with `tenant_id`.
5. Apply the RLS template to every tenant table:
   ```sql
   CREATE FUNCTION core.current_tenant() RETURNS uuid LANGUAGE sql STABLE AS
   $$ SELECT nullif(current_setting('app.tenant_id', true), '')::uuid $$;
   ALTER TABLE <t> ENABLE ROW LEVEL SECURITY;
   ALTER TABLE <t> FORCE  ROW LEVEL SECURITY;
   CREATE POLICY tenant_isolation ON <t>
     USING (tenant_id = core.current_tenant())
     WITH CHECK (tenant_id = core.current_tenant());
   ```
6. **Roles:** `mos_app` becomes NOBYPASSRLS. A `mos_rls_definer` role owns the few SECURITY DEFINER functions that must work across tenants (outbox claim, active-tenant list, login-time membership lookup).
7. Set `app.tenant_id` with `set_config(…, true)` inside the unit of work. **Never add an "unset tenant sees everything" policy arm**, a lesson carried over from DMS.
8. **Guards:**
   - a boot probe that refuses superuser or BYPASSRLS logins, and any rows returned under a random tenant id;
   - a CI catalog test: every tenant table has `relrowsecurity` and `relforcerowsecurity`, a policy, and `tenant_id NOT NULL`; no policy text contains `IS NULL`; every FK to a tenant table is composite;
   - a two-tenant acceptance suite;
   - tests run as `mos_app`.
9. **Tenant resolution:** subdomain → `core.tenant` → the tenant's IdP config and sign-in methods. A user with several memberships picks one at sign-in; switching creates a new session.
10. Import each instance database into the pooled database. UUIDv7 ids guarantee no collisions.

**Estimated effort:** 4–6 dev-weeks for stage 3, given the seams in §4.4.

---

## Appendix B. Deferred designs (Later)

### B.1 Edge agent for weighbridges and label printers
Kept from v0.2 for when hardware integration is scheduled.

- **Packaging:** a Node LTS app bundled with esbuild, shipped with prebuilt native addons (`serialport`, `better-sqlite3`). It runs as a Windows service or a systemd unit on a fanless PC with a UPS.
- **Identity:** a single-use enrollment code. The agent generates Ed25519 **auth** and **reading-signing** keys and authenticates with `private_key_jwt` (RFC 7523, `jti` replay cache).
- **Transport:** outbound-only WSS, with an HTTPS batch fallback. Envelopes are delivered at-least-once, deduplicated by id.
- **Capture interlocks:** stable, ≥ minimum, ≤ capacity, and returned to zero since the last capture. One reading can feed only one weighing.
- **Signed readings:** JCS canonical form, Ed25519 with a domain prefix, hash-chained. Keys are kept after revocation so old readings stay verifiable.
- **Indicator profiles are data**, with a sniff tool. Offline capture prints QR slips for later attachment.
- **Limits:** signing proves a reading came unaltered from the device. It cannot detect a tampered load cell, which is why calibration records and tare analytics remain necessary.

### B.2 Other Later items with design notes
- **ANPR and barriers:** they subscribe to visit state changes and drive barrier relays through the edge agent.
- **Supplier portal:** a separate route group with supplier principals; documents filtered by partner.
- **Public API and webhooks:** scoped API keys; HMAC-signed outbound webhooks from the outbox; OpenAPI generated from the Zod contracts.
