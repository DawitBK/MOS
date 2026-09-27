# MOS: progress tracker

**Overall status:** Planning package **v0.3, planning programme complete** (Phases 1–6, 2026-09-27/28). **Implementation not started**; it waits for the owner's go-ahead and the client's choice of team path. Open items are in the [decision register](docs/MOS-DECISION-REGISTER.md).

## v0.3 planning programme

| # | Phase | Owner's points covered | Outputs | Status |
|---|---|---|---|---|
| 1 | **Audit and re-baseline** | 1 (re-check everything), 6 (single tenant/company, infrastructure as questions), the 6-month frame | [Audit](docs/MOS-PACKAGE-AUDIT-v0.3.md); FRD, TDD, implementation plan, master plan (`.md` + `.html`), client-draft review at v0.3; ADR-0017/18/19 and amendments; `reference-input/owner-brainstorm/` | **Done** (owner go-ahead for Phase 2, 2026-09-27) |
| 2 | End-to-end process model | 3 (ChatGPT outputs → better flows and supporting doc) | [Brainstorm review](docs/MOS-BRAINSTORM-OUTPUT-REVIEW.md); [process model](docs/MOS-PROCESS-MODEL.md) + local `.html` (value chain, whole-ERP flow with 33 hand-offs, 21 swimlanes + 2 sub-processes with 199 steps, 27 lifecycles, gate step sequences, document chain, 36 events + 11 jobs, 64 exceptions, RACI, SoD, controls, KPIs, traceability); FRD/TDD/plan 0.3.1 | **Done** (owner go-ahead for Phase 3, 2026-09-27) |
| 3 | Database design | 4 | [Database design](docs/MOS-DATABASE-DESIGN.md) + local `.html` (230 tables in 9 schemas, 3,304 columns, 1,144 foreign keys, 35 entity diagrams; conventions, deep dives with worked rows, transaction write-sets, invariants and reconciliation, operations, ownership matrix); TDD and FRD 0.3.2, process model 0.2 | **Done** (owner go-ahead for Phases 4–6, 2026-09-28) |
| 4 | Estimate, roadmap, Module 1 plan, pricing | 2 | [Implementation plan v0.4](docs/MOS-IMPLEMENTATION-PLAN.md) (187-feature estimate, capacity per path, week-level roadmap, go-live hours); [procurement module plan](docs/MOS-PROCUREMENT-MODULE-PLAN.md) + `.html` (48 stories, UAT script, migration, training, go-live); [commercial proposal](docs/MOS-COMMERCIAL-PROPOSAL.md) + `.html` | **Done** (revised after the Phase 6 decisions) |
| 5 | Decision register and stakeholder questionnaire | 5a, 6 | [Decision register](docs/MOS-DECISION-REGISTER.md) + `.html` (41 decided, 10 owner items open, 35 client business and 17 infrastructure questions; 54 earlier questions traced once each); [stakeholder questionnaire](docs/MOS-STAKEHOLDER-QUESTIONNAIRE.md) + `.html` | **Done** |
| 6 | Architecture grilling and lock-in | 5b | Three question rounds with the owner; [ADR-0020](docs/adr/ADR-0020-six-modules-one-release-26-weeks.md) (delivery), [ADR-0021](docs/adr/ADR-0021-stack-lock-in.md) (stack), [ADR-0022](docs/adr/ADR-0022-hosting-and-operations-baseline.md) (hosting and operations); ADR-0001/0006/0012/0018/0019 amended; TDD 0.3.3, FRD 0.3.3, database design 0.1.1; register updated | **Done** |

**Rules for every phase:**
- Documents only; no code, DDL or scaffolding.
- Everything stays local in `MOS/`; nothing is published to claude.ai.
- Nothing is committed unless the owner asks.

### Inputs recorded for Phase 4 (pricing)
- ETB client quote at **US$5 per dev-hour**. The lead developer works **7 days × 8 h**; other roles are parameterised.
- Present **team paths A (2 devs + AI + QA), B (2 devs + AI), C (1 dev with heavy AI)**.
- Price separately **development, QA/testing/UAT, production deployment and hosting, maintenance/support**.
- The window is ≤ 6 months of development + 2 months for the other work.
- **Reference view from the owner** (2026-09-27, "not to be suggestive"):
  - estimate = number of developers × hours × hourly rate;
  - a contract can add expenses such as travel to site and hours spent on non-development work;
  - development work should include testing and infrastructure setup for the production release.
- The ETB/USD rate is looked up at Phase 4 time, with its date and source recorded.

## Implementation work packages (committed path A schedule; details in the [implementation plan](docs/MOS-IMPLEMENTATION-PLAN.md) §6–§7)

Paths B and C use the weeks in the implementation plan §7. Every package's core is in the release; standard and full features are pull-ins.

| WP | Title | Weeks (path A) | Status |
|---|---|---|---|
| WP-00 | Repository and tooling bootstrap | W1–W2 | Not started |
| WP-01 | Platform core | W1–W4 | Not started |
| WP-04 | Web shell and shared services | W2–W4 | Not started |
| WP-02 | Identity and access | W4–W5 | Not started |
| WP-03 | Organisation and master data | W4–W8 | Not started |
| WP-07 | Approvals and SoD | W5–W7 | Not started |
| WP-05 | Inventory ledger engine | W7–W9 | Not started |
| WP-08 | Procurement | W8–W12 | Not started |
| WP-11 | Payables and supplier payment | W10–W15 | Not started |
| WP-06 / WP-12 | Inventory documents, counts, close | W12–W15 | Not started |
| WP-09 | Lab and quality | W15–W18 | Not started |
| WP-10 | Gate and the automated inbound loop | W17–W21 | Not started |
| WP-14 | Production | W18–W21 | Not started |
| WP-15 | Sales and receivables | W21–W24 | Not started |
| WP-13 | Hardening and feature freeze | W24–W25 | Not started |
| WP-16 | Go live | W27–W35 | Not started |

Velocity checkpoints at **W4** and **W9** (implementation plan §11).

## Open questions
All open items are in the [decision register](docs/MOS-DECISION-REGISTER.md):
- **Part B:** 10 owner items. These include who pays for capacity added after a checkpoint (O-26), the go-ahead date, the product name, the FX clause, the maintenance minimum, travel and billing terms, and the bank-account import approach.
- **Parts C and D:** 35 client business and 17 infrastructure questions. They are packaged for the client in the [stakeholder questionnaire](docs/MOS-STAKEHOLDER-QUESTIONNAIRE.md).

## Session log
- **2026-09-26:** Planning package v0.1 written: master plan, FRD, TDD, implementation plan, ADR-0001…0014, HTML view. No code.
- **2026-09-27 (a):** The client's procure-to-pay draft was reviewed, and the package revised to v0.2 (ADR-0015 local accounts, ADR-0016 system prepares / role confirms; MVP ≈ 10.4 months). No code.
- **2026-09-27 (b), planning Phase 1:**
  - **Reviews:** three parallel reviews of v0.2 and author verification produced 135 findings ([audit](docs/MOS-PACKAGE-AUDIT-v0.3.md)).
  - **Re-framed:** single company, no tenancy (ADR-0017); all six modules in ≤ 6 + 2 months with team paths A/B/C (ADR-0018); infrastructure not assumed (ADR-0019).
  - **Found:** the client is a flour mill ("Addis Flour Factory"). The swimlane is the owner's ChatGPT image, not the client's; it was moved to `docs/reference-input/owner-brainstorm/` with the ChatGPT PDF, DOCX and ERP picture.
  - **FRD v0.3:** 225 requirements, each with an acceptance criterion; production and sales in v1.
  - **TDD v0.3:** three production-breaking defects fixed; production and sales/AR designed.
  - **Versions checked on the web:** Node 22 end of life 2027-04-30; NestJS 12 released 2026-08-28; BullMQ 6 removed repeatable jobs.
  - Nothing published, nothing committed, no code.
  - **Next:** owner review of Phase 1, then Phase 2 on the owner's go-ahead.
- **2026-09-27 (c), planning Phase 2** (owner: "go do phase 2"):
  - **Brainstorm review:** ChatGPT's PDF, ERP picture and swimlane checked against the client's draft page by page: 29 findings (8 High), e.g. no weighbridge, pass/fail instead of grades and deductions, no invoice matching, discrete instead of milling production, no Sales panel in the picture, outbound exit on paperwork. Its page references to the client draft were correct.
  - **Process model:** L0 value chain; L1 whole-ERP flow (33 hand-offs); 21 processes + 2 shared sub-processes as sequence-form swimlanes with 199 numbered steps; 27 lifecycle diagrams matching FRD §1.5; gate step sequences per visit class (closes audit F-52); document chain and silo-commingling trace; 15 synchronous hand-offs, 36 event types, 11 scheduled jobs; 64 exception cases; RACI; SoD pairs; controls, KPIs, reports; traceability generated from the step tables.
  - **Fed back (FRD, TDD, plan → 0.3.1):** 6 new v1 requirements (FR-PRC-022, FR-AP-019, FR-MFG-018, FR-SAL-016…018), BR-PRC-05, BR-MFG-01, SOD-17; 12 lifecycles added to FRD §1.5 and 3 rows corrected; a TDD tier violation fixed (`ReceiptCommitmentPort`), `OutboundLoadListener`, weigh-ticket verification status, invoice journal, weighbridge-scoped numbering, event catalogue. FRD now 231 requirements, 205 in v1.
  - **Incidental fixes:** v0.2 vocabulary notes in ADR-0005/0010/0013; stale TDD section references in ADR-0015, WP-00 and R16.
  - **Checks:** every v1 requirement maps to a step; every cited ID exists; lifecycle states equal FRD §1.5; every event has a producer and consumer; all 51 Mermaid diagrams parse (mermaid 12 parser, scratchpad only); the HTML renders in headless Chrome.
  - Nothing published, nothing committed, no code.
  - **Next:** owner review of Phase 2, then Phase 3 (database design) on the owner's go-ahead.
- **2026-09-27 (d), planning Phase 3** (owner: "go to phase 3"):
  - **Database design:** 230 tables in 9 schemas (core 45, md 30, inv 30, qc 22, proc 9, gate 33, mfg 18, sales 20, fin 23); 3,304 columns; 1,144 foreign keys; 35 entity diagrams. Conventions (keys, types, standard column sets, table classes and grants, stored vs displayed statuses, deletion, polymorphic references, time, currency, lock ranks); a data dictionary with class, writing steps and volume per table; 13 deep dives with worked rows (ledger, commingled silos, document links, approvals, numbering, audit, attachments, outbox, weigh tickets, QC, payables, production, sales); 10 transaction write-sets in lock order; invariants P1–P22 and 12 reconciliation jobs; operations (indexing, volumes, partitioning, retention, backup requirements, migrations and seeds, grants, encryption and personal data, expansion); ownership and CRUD matrix.
  - **Decisions (D-01…D-19), fed back:** one `fin.open_item` for every partner document; company accounts and tax codes moved to `md` (two tier fixes); new ports `PartnerLedgerPort` and `WeighTicketReader`; insert-only usage and approval decisions; per-module period status; goods issue as its own posting; supplier-return journal split with a new `supplier_return.posted` event. TDD and FRD → 0.3.2 (FR-PLT-006: the weighbridge station stays with the gate module off); process model → 0.2.
  - **Incidental fixes:** MOS `CLAUDE.md` pointed to TDD §6.3 for locks (now §7.3) and named `TxInterceptor` (now `UnitOfWorkInterceptor`); ADR-0017 had the same old name.
  - **Checks:** every FK target exists; every table has a key; all 28 FRD §1.5 lifecycles map to stored or derived states; all 32 process-model documents map to tables; every "written by" step exists; TDD insert-only tables are insert-only; every cited ID exists; the worked-example arithmetic is recomputed; all 88 Mermaid diagrams in the package parse; the HTML renders in headless Chrome.
  - Nothing published, nothing committed, no DDL or code.
  - **Next:** owner review of Phase 3, then Phase 4 (roadmap, Module 1 plan, pricing) on the owner's go-ahead.
- **2026-09-27/28 (e), planning Phase 4** (owner: "do phase 4 then 5 then 6"):
  - **Estimate:** bottom-up over 187 features that map every one of the 205 v1 requirements (checked by script). Lean-complete v1 = 3,102 AI-assisted build hours (likely; range ×0.75–×1.25); core = 2,380 h; standard 606 h; full 116 h.
  - **Capacity** at the owner's hours (lead 56 h, second developer 48 h, QA 40 h; 80% focus; one reserve week; +15% testing without QA): path A 2,080, B 1,809, C 974 build hours in 26 weeks.
  - **Finding:** at the likely estimate, no path builds all six modules in 26 weeks (A: procure-to-pay only; C: platform only). The v0.3 "≈ 58 dev-weeks, path A just fits" was about a third too low.
  - **Outputs:** a scheduler and pricing model in the scratchpad generated every table and Gantt chart; implementation plan v0.4, procurement module plan (48 stories = 640 dev hours, UAT script with 21 cases), commercial proposal. FX looked up on 2026-09-27: Wise 161.20, XE 162.17 mid-market; CBE 160.23/163.44 (2026-09-17); planning rate ETB 162.00.
  - **Incidental finding, fixed:** the FRD month tags and the v0.3 month plan disagreed (e.g. QC tagged M3 in the FRD, planned M4). The FRD tags were re-aligned to the committed schedule in Phase 6 (FRD 0.3.3).
- **2026-09-28 (f), planning Phase 5:** decision register (A decided, B owner, C client business by module, D infrastructure, E traceability of 54 earlier questions, each exactly once) and the client questionnaire with answer fields and needed-by weeks.
- **2026-09-28 (g), planning Phase 6** (three rounds with the owner):
  - **Delivery (ADR-0020):** one release with all six modules at core depth in 26 weeks, live at W35, on every path. The owner rejected the longer and phased options. Each path is planned at the velocity it needs (A ×1.32, B ×1.52, C ×2.50 the likely estimate), with checkpoints at W4 and W9; capacity is the lever if behind. Prices build-to-go-live: A $23,200, B $18,000, C $11,760.
  - **Commercial:** second developer 48 h; no free warranty (defects after hypercare through the retainer); the client owns its build, and the vendor may sell MOS to others.
  - **Stack (ADR-0021):** Node 24, NestJS 12 + Vitest, PostgreSQL 18 (audit log unpartitioned in v1, D-18 declined), BullMQ 6 + Redis/Valkey, Next.js 16.
  - **Hosting and operations (ADR-0022):** on-premises first; native services and Docker Compose both documented and tested; dev, staging and production; nightly full + WAL backups with a weekly restore test; GitHub private repository with Actions.
  - **Other:** the stale v0.2 claude.ai page was deleted at the owner's request. MOS was committed and pushed to a new private GitHub repository at the owner's request.
  - **Checks:** every placeholder filled; every cited requirement, table and link exists; every money cell satisfies hours × 5 = US$ and US$ × 162 = ETB; every procurement requirement has a story; story hours equal feature hours; all Mermaid diagrams parse; a Gantt chart rendered in headless Edge.
  - Nothing published; no DDL or code.
