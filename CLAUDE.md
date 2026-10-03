# MOS: conventions for AI-assisted sessions

**Current phase: PLANNING ONLY. The v0.3 planning programme (Phases 1–6) is complete; implementation waits for the owner's go-ahead and the client's choice of team path.** The owner has said explicitly: no implementation and no application code until they give the go-ahead. That includes DDL scripts and scaffolding. Until then, work in this repo is limited to the planning documents in `docs/`.

## How the owner wants work done (standing instructions)
- **Phase-gated.** The v0.3 programme runs in six phases (see `PROGRESS.md`). Finish one phase completely, verify it, update `PROGRESS.md`, then **stop for the owner's review**. Never start the next phase in the same turn.
- **Local only.** Every output (docs, diagrams, HTML views) stays in this folder. **Never publish MOS documents as claude.ai Artifacts.** This overrides the root repo's "pair .md/.html and republish" convention for MOS.
- **Git:** this folder is its own repository with a **private** GitHub remote (created at the owner's request, 2026-09-28). Commit and push only when the owner asks.
- **Docs first, then HTML.** `.md` is canonical. `.html` is a local, printable view of every main document. After editing a `.md`, rebuild its page. The pages, the plan figures (implementation plan, sprint plan, Module 1 plan) and the prices were produced by generator scripts in the 2026-10-03 session's scratchpad, which is **not kept** (MOS holds no code files). So a figure change must be recomputed and carried into every document that shows it (implementation plan, sprint plan, Module 1 plan, master plan, pricing and timeline, proposal, register, ADR-0025), never edited in one place only.
- **Diagrams:** Mermaid inside `.md`. The HTML renders Mermaid from `cdn.jsdelivr.net/npm/mermaid`. Hero diagrams are inline SVG, so they work offline.

## Where things live (reorganised 2026-10-03)
- **Navigation:**
  - `index.html` is the browser start page;
  - `README.md` has the "I want to…" table and the folder map;
  - `READING-GUIDE.md` (+ `.html`) is the reading order, file by file; **keep it in step** when a document is added, renamed or re-sectioned (it cites tabs and section numbers);
  - every numbered folder has a README.
- **`docs/` is numbered in reading order:**
  - `1-overview`, `2-requirements` and `3-design`;
  - `4-plan` and `5-for-the-client` (drafts not yet sent);
  - `6-decisions` (the register plus `adr/`) and `7-reviews` (newest is current, the rest history);
  - `8-meetings` (one `YYYY-MM-DD-topic/` folder per meeting: `DISCUSSION-PACK` for the client, `OWNER-BRIEFING` for the owner only);
  - `9-inputs` (`from-client/`, `owner-brainstorm/`, kept as received).
- **A new document** goes in the matching folder. It gets:
  - a row in that folder's README and a card on `index.html`;
  - the breadcrumb line under its title (`<!-- nav -->[MOS](README.md) › [N. Folder](README.md)`, with the paths made relative to the file);
  - in its `.html` view, the slim "← MOS start page" bar (hidden when printed).
- **Prices:** the client price is fixed at ETB 986,615 within the owner's ETB 1,000,000 cap (register A-43); `docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.md` allocates it over 4,027 hours of estimated work at ETB 245 each, and every breakdown adds up to the total; a change to the estimate means re-allocating the whole document and the proposal's price sections together.
- **File names keep their `MOS-` prefix,** so references in other places and in the client's copies stay valid.
- **Links are relative.** After moving anything, check that every link resolves.

## Inputs and their weight
- **Client input** (`docs/9-inputs/from-client/`): four documents, listed with dates in that folder's README.
  - The procure-to-pay process, first draft (27 Sep) and v2 (1 Oct, 28 screen images).
  - The data dictionary (1 Oct).
  - The architecture proposal (2 Oct).

  They are the real requirements source. Reviews: [MOS-CLIENT-DOCS-V2-REVIEW.md](docs/7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md) (current, re-checked 2026-10-03) and [MOS-CLIENT-P2P-DRAFT-REVIEW.md](docs/7-reviews/MOS-CLIENT-P2P-DRAFT-REVIEW.md) (first draft; its issue numbers are still cited).
  - Don't build the mock-ups literally where a review lists a defect, e.g. editable status dropdowns or the `PR-` prefix on payment requests.
  - Treat each listed defect as a spec correction. The client's data dictionary is a field list; the table design is ours.
- **Owner brainstorm** (`docs/9-inputs/owner-brainstorm/`): ChatGPT outputs, i.e. the swimlane, the whole-ERP picture and the supporting document. **Not client documents**, and not requirements. Reviewed in Phase 2 ([docs/7-reviews/MOS-BRAINSTORM-OUTPUT-REVIEW.md](docs/7-reviews/MOS-BRAINSTORM-OUTPUT-REVIEW.md)); the process reference is now [docs/2-requirements/MOS-PROCESS-MODEL.md](docs/2-requirements/MOS-PROCESS-MODEL.md), whose step IDs (e.g. `P05.14`) the database design, tests and training refer to.
- **Domain:** the scope of work says "Floor Manufacturing". That text was supplied by the owner in chat on 2026-09-26/27 and is not a file in the repo. But the client's draft names "Addis Flour Factory" and covers durum wheat, hectolitre weight, silos and bran, which indicates a **flour** mill. This is an open client question; grain/flour milling is the reference pack.

## Session protocol (resumability)
1. **Read first:** this file, then `PROGRESS.md`, then the TDD section for the work in hand.
2. **Scope:** work inside one phase (planning) or one work package (implementation). Log anything outside it in `PROGRESS.md` rather than doing it.
3. **Contracts first** (implementation): Zod schemas in `packages/contracts` and acceptance tests from the FRD come before implementation.
4. **Done means** the acceptance tests are green in CI (implementation), or the phase's verification checks pass (planning).
5. **End of session:** update `PROGRESS.md` with status, last change, next concrete step and open questions.
6. **Decisions** go in `docs/6-decisions/adr/` as a new numbered ADR. Scope changes follow the change rule in the implementation plan.

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
- **Tables:** [docs/3-design/MOS-DATABASE-DESIGN.md](docs/3-design/MOS-DATABASE-DESIGN.md) is the reference for every table, column and constraint. A new table gets a class in the table registry (mutable, never deleted, insert-only, posting, side) and its grants follow from it. Foreign keys may cross schemas; code follows the tiers.
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
Node.js 24 LTS · NestJS 12 on Express + Vitest · Drizzle ORM · PostgreSQL 18 (`core.audit_event` unpartitioned in v1) · BullMQ 6 + Redis/Valkey behind the `JobQueue` port (the outbox stays in PostgreSQL) · Next.js 16 / React 19 house stack. **Open:** O-27 (Vite SPA) and O-28 (pg-boss), both recommended; the estimate already assumes them. Delivery commitment: ADR-0020 with the calendar of **ADR-0025 (everything in 26 weeks, go-live W24)**; hosting and operations: ADR-0022.

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
