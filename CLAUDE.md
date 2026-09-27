# MOS: conventions for AI-assisted sessions

**Current phase: PLANNING ONLY. The v0.3 planning programme (Phases 1–6) is complete; implementation waits for the owner's go-ahead and the client's choice of team path.** The owner has said explicitly: no implementation and no application code until they give the go-ahead. That includes DDL scripts and scaffolding. Until then, work in this repo is limited to the planning documents in `docs/`.

## How the owner wants work done (standing instructions)
- **Phase-gated.** The v0.3 programme runs in six phases (see `PROGRESS.md`). Finish one phase completely, verify it, update `PROGRESS.md`, then **stop for the owner's review**. Never start the next phase in the same turn.
- **Local only.** Every output (docs, diagrams, HTML views) stays in this folder. **Never publish MOS documents as claude.ai Artifacts.** This overrides the root repo's "pair .md/.html and republish" convention for MOS.
- **Git:** this folder is its own repository with a **private** GitHub remote (created at the owner's request, 2026-09-28). Commit and push only when the owner asks.
- **Docs first, then HTML.** `.md` is canonical. `.html` is a local, printable view of the main reader-facing documents.
- **Diagrams:** Mermaid inside `.md`. The HTML renders Mermaid from `cdn.jsdelivr.net/npm/mermaid`. Hero diagrams are inline SVG, so they work offline.

## Inputs and their weight
- **Client input** (`docs/client-input/`): the client's procure-to-pay draft, i.e. the narrative and 21 screen images (17 screens). It is the first real requirements source. Where it conflicts with the plan, [docs/MOS-CLIENT-P2P-DRAFT-REVIEW.md](docs/MOS-CLIENT-P2P-DRAFT-REVIEW.md) records the decision.
  - Don't build the mock-ups literally where the review lists a defect, e.g. editable status dropdowns or the `PR-` prefix on payment requests.
  - Treat each defect in the review's §5 as a spec correction.
- **Owner brainstorm** (`docs/reference-input/owner-brainstorm/`): ChatGPT outputs, i.e. the swimlane, the whole-ERP picture and the supporting document. **Not client documents**, and not requirements. Reviewed in Phase 2 ([docs/MOS-BRAINSTORM-OUTPUT-REVIEW.md](docs/MOS-BRAINSTORM-OUTPUT-REVIEW.md)); the process reference is now [docs/MOS-PROCESS-MODEL.md](docs/MOS-PROCESS-MODEL.md), whose step IDs (e.g. `P05.14`) the database design, tests and training refer to.
- **Domain:** the scope of work says "Floor Manufacturing". That text was supplied by the owner in chat on 2026-09-26/27 and is not a file in the repo. But the client's draft names "Addis Flour Factory" and covers durum wheat, hectolitre weight, silos and bran, which indicates a **flour** mill. This is an open client question; grain/flour milling is the reference pack.

## Session protocol (resumability)
1. **Read first:** this file, then `PROGRESS.md`, then the TDD section for the work in hand.
2. **Scope:** work inside one phase (planning) or one work package (implementation). Log anything outside it in `PROGRESS.md` rather than doing it.
3. **Contracts first** (implementation): Zod schemas in `packages/contracts` and acceptance tests from the FRD come before implementation.
4. **Done means** the acceptance tests are green in CI (implementation), or the phase's verification checks pass (planning).
5. **End of session:** update `PROGRESS.md` with status, last change, next concrete step and open questions.
6. **Decisions** go in `docs/adr/` as a new numbered ADR. Scope changes follow the change rule in the implementation plan.

## Non-negotiables (from the TDD; violating these is a bug)
- **Installation model** (ADR-0017):
  - One installation = one company, one database.
  - No tenant columns and no RLS in v1.
  - Keep the expansion seams: every query goes through module repositories inside the transaction owned by `UnitOfWorkInterceptor`; context travels in nestjs-cls; UUIDv7 keys; business settings in DB tables, not env.
  - The app DB login is never a superuser or table owner (the boot probe enforces this).
  - **Tests run as `mos_app`, never as the owner or a superuser.** Otherwise the insert-only grants and the `guard_posted` trigger are never exercised.
- **Transactions:**
  - One transaction per request, owned by `UnitOfWorkInterceptor` (TDD §7.2).
  - No ad-hoc `db.transaction()` in controllers.
  - **No external I/O inside request handlers.** Email, PDFs, webhooks and exports go through the outbox.
- **Locks:** acquire them through `LockService` in rank order R1–R6 (TDD §7.3; table names in the database design §2.13). The lock tracer fails tests that don't.
- **Money and quantities:** use decimal.js branded types only. Numbers are strings at the API edge; never call `Number()` or `parseFloat` on them.
- **Posted records are immutable.** Corrections are reversal documents. Never add an UPDATE path to a posted table; counters that change after posting live in side tables.
- **Tables:** [docs/MOS-DATABASE-DESIGN.md](docs/MOS-DATABASE-DESIGN.md) is the reference for every table, column and constraint. A new table gets a class in the table registry (mutable, never deleted, insert-only, posting, side) and its grants follow from it. Foreign keys may cross schemas; code follows the tiers.
- **Status is action-driven.** No create or update payload may carry a status value; status changes only through named actions.
- **Local accounts** (ADR-0015): argon2id, lockout, non-enumerating responses. Never log a password or return a hash.
- **Idempotency:** every mutating endpoint requires `Idempotency-Key`.
- **Module boundaries:** import other modules only through their `index.ts`. dependency-cruiser enforces this.
- **Configuration:**
  - Production reads `.env.production` only and refuses placeholders.
  - No hardcoded hosts, ports or URLs.
  - Load dotenv with `override: true`, because a machine-wide `DATABASE_URL` has bitten this platform before.
  - Infrastructure is not assumed (ADR-0019): keep storage, AV scan and the queue behind adapters.

## NestJS pitfalls
- No request-scoped providers; use `nestjs-cls` for request context.
- Validate with Zod contracts through NestJS 12's built-in Standard Schema validation (ADR-0021), not class-validator.

## Stack (locked, ADR-0021)
Node.js 24 LTS · NestJS 12 on Express + Vitest · Drizzle ORM · PostgreSQL 18 (`core.audit_event` unpartitioned in v1) · BullMQ 6 + Redis/Valkey behind the `JobQueue` port (the outbox stays in PostgreSQL) · Next.js 16 / React 19 house stack. Delivery commitment: ADR-0020; hosting and operations: ADR-0022.

## Next.js 16 pitfalls (house lessons)
- `NEXT_PUBLIC_*` values and `basePath` are baked in at build time. The build fingerprint refuses a stale build.
- Pin `turbopack.root`. Use native ESLint flat configs (no FlatCompat).
- React Compiler hook rules are errors. Every dependency of `useResetOnChange` must have a stable identity.

## Reference identity provider (existing SSO)
Optional; local accounts are the default.
- **Gating:** register the `mos` client and portal tile **only when `MOS_ENABLED=true`**. SSO refuses to boot in production or mirror if *any* seeded client has a dev secret or a localhost URI.
- **SSO behaviour to design around:**
  - PKCE is mandatory for all clients, although the SSO guide says otherwise.
  - Clients authenticate with `client_secret_post`.
  - `ad_groups` needs its own scope.
  - There is no back-channel logout for new clients and no `client_credentials` grant.

## Standalone
MOS does not use GMS and never reads another system's database.
