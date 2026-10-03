# Architecture decision records

<!-- nav -->[MOS](../../../README.md) › [6. Decisions](../README.md)

| ADR | Decision | Status |
|---|---|---|
| [0001](ADR-0001-nestjs-on-express5.md) | NestJS on the Express adapter | Accepted (planning); amended by 0017; **NestJS 12 on Node 24, decided by 0021** |
| [0002](ADR-0002-drizzle-orm.md) | Drizzle ORM + SQL migrations; hand-written SQL for DB-enforced rules | Accepted (planning); not re-opened in Phase 6 |
| [0003](ADR-0003-modular-monolith.md) | Modular monolith, one Postgres schema per bounded context, dependency tiers | Accepted (planning); amended by 0018 (tiers, mfg and sales in v1) |
| [0004](ADR-0004-multitenancy-rls.md) | Shared-DB multi-tenancy with forced RLS and composite keys | **Superseded by 0017** (kept as the expansion playbook's target design) |
| [0005](ADR-0005-idp-agnostic-oidc.md) | IdP-agnostic OIDC; authorization and sessions inside MOS | Accepted (planning); amended by 0015, 0017, 0018 |
| [0006](ADR-0006-outbox-bullmq.md) | Transactional outbox + BullMQ/Redis | Accepted (planning); amended by 0017; **BullMQ + Redis/Valkey kept, decided by 0021** |
| [0007](ADR-0007-document-posting-model.md) | Document lifecycle, immutability, reversal-only corrections | Accepted (planning) |
| [0008](ADR-0008-inventory-ledger.md) | Signed single-sided inventory ledger, transactional balances, MWA | Accepted (planning); amended by 0017/0018 (company-level valuation, lock ranks, residual clamp) |
| [0009](ADR-0009-inbound-process-managers.md) | Inbound flow as two persistent process managers | Accepted (planning); amended by 0016; edge-agent parts moved to Later by 0018 |
| [0010](ADR-0010-approvals-and-sod.md) | Data-driven approvals engine + SoD rule engine | Accepted (planning); amended by 0018 (lean v1 approvals matrix) |
| [0011](ADR-0011-numbering.md) | Document numbering | Accepted (planning); amended in v0.3 (one same-transaction mechanism; numbers kept on cancel) |
| [0012](ADR-0012-frontend-stack.md) | House Next.js 16 stack + TanStack Table + react-hook-form | Accepted (planning); **confirmed by 0021** |
| [0013](ADR-0013-attachments-and-dms.md) | Own attachment store; optional DMS archive connector | Accepted (planning); amended by 0017, 0019 (local disk by default) |
| [0014](ADR-0014-standalone-no-gms.md) | Standalone; gate built inside MOS; GMS not used | Accepted (planning) |
| [0015](ADR-0015-local-accounts.md) | Local username/password accounts alongside SSO (from the client draft) | Accepted (planning), 2026-09-27 |
| [0016](ADR-0016-system-prepares-role-confirms.md) | Automation defaults: the system prepares, a named role confirms (GRN, payment request) | Accepted (planning), 2026-09-27 |
| [0017](ADR-0017-single-tenant-single-company-v1.md) | **Single tenant, single company in v1**, with expansion seams; instance-per-customer first | Accepted (planning), 2026-09-27 |
| [0018](ADR-0018-lean-v1-six-months.md) | **Lean but complete v1**: all six modules in ≤ 6 months of development, then 2 months to go live; tags v1 / v1.1 / Later; team paths A/B/C | Accepted (planning), 2026-09-27; **amended by 0020 and 0025** |
| [0019](ADR-0019-infrastructure-not-assumed.md) | **Infrastructure not assumed**: hosting and deployment decided after stakeholder answers; the application stays portable | Accepted (planning), 2026-09-27; **amended by 0022 and 0025** (hosting decision before W1) |
| [0020](ADR-0020-six-modules-one-release-26-weeks.md) | **One release, all six modules at core depth in 26 weeks, on every team path**; each path planned at the velocity it needs; checkpoints at W4 and W9 | Accepted, 2026-09-28 (Phase 6); **calendar amended by 0025** |
| [0021](ADR-0021-stack-lock-in.md) | **Stack lock-in:** Node 24, NestJS 12 + Vitest, PostgreSQL 18 (audit log unpartitioned in v1), BullMQ 6 + Redis/Valkey, Next.js 16 | Accepted, 2026-09-28 (Phase 6) |
| [0022](ADR-0022-hosting-and-operations-baseline.md) | **Hosting and operations:** on-premises first; native services and Docker Compose both offered; dev/staging/prod; backup, CI and observability baseline | Accepted, 2026-09-28 (Phase 6) |
| 0023, 0024 | *Reserved* for the stack questions O-27 (frontend: Vite or Next.js) and O-28 (job queue: pg-boss or BullMQ + Redis) | Reserved |
| [0025](ADR-0025-everything-in-six-months.md) | **Everything inside six months:** build W1–W20 with rolling UAT, test server by W4, production server by W16, system test and final UAT W21–W22, training W23, **go-live W24**, hypercare W25–W26; checkpoints W4 and W8 | Accepted (planning), 2026-10-03 |

**"Accepted (planning)"** means the decision is taken for planning purposes. The owner's review may change any of them before implementation starts. The Phase 6 architecture session (2026-09-28) settled the delivery, stack and hosting questions in ADR-0020, 0021 and 0022.
