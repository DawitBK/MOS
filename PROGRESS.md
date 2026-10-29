# MOS: progress tracker

**Build status (2026-10-18): the Odoo build exists.** The owner overruled the no-go and MOS is being built on Odoo 20 in the private repository `DawitBK/mos-odoo` ([ADR-0026](docs/6-decisions/adr/ADR-0026-odoo-as-the-base-proposed.md) supersedes ADR-0021 for MOS; fit-gap: [ODOO-FIT-GAP](docs/3-design/ODOO-FIT-GAP.md)). 33 addons, **301 automated tests passing**, a browser smoke test that opens every menu action, a browser journey that carries a wheat delivery through six people (weigh, blind verify, lab, decision, receipt, exit), and a server-side check that 17 roles can open every screen their menu offers (live system reprovisioned and single sign-on verified 2026-10-18):

| Addon | What it does |
|---|---|
| `mos_core` | sites and scoping, reason codes, hash-chained append-only audit, posting rules, attachments (SHA-256, type check, size, scan hook) |
| `mos_gate` | visits, direction-aware weigh tickets with blind verification, watch list, guard shifts, exit pass with QR, vehicles with stored tare and loading limit, incidents, material gate passes, yard board, throughput report |
| `mos_lab` | versioned specifications, inspections, rule-based deductions, score and grade, sampling plans, retention samples, certificate of analysis gate, lab report |
| `mos_inbound`, `mos_silo`, `mos_production` | flow A receipts, silo levels, milling runs and extraction |
| `mos_approvals` | approval matrix, segregation of duties, delegation, escalation |
| `mos_payables`, `mos_sales` | three-way match, debit notes, vouchers, payments, cheque books; credit control, deliveries through the gate, invoices, receipts |
| `mos_procurement` | purchase requests, supplier approval and documents, bank-change control, sourcing and receipt policies, PO amendments, supplier returns, GRN reassignment |
| `mos_inventory` | stock adjustments with approval, cycle counts, quarantine and blocked stock, reorder report, archive rules, labels |
| `mos_controls` | period close, backdating windows, date sanity, exceptions queue, global search |
| `mos_flows` | flow B (quarantine), flow C (lab-led), automatic receipts, pre-flight checks, receipt exceptions, payments drafted from receipts |
| `mos_reports`, `mos_dashboard`, `mos_auth_oidc` | printed documents, the mill overview, single sign-on |
| `mos_manufacturing` | batches with trace, finished-goods release, packing, dosing, work centres, downtime, cost, planner demand view |
| `mos_commercial` | customer approval, credit-limit changes, price floors and price-list approval, credit notes, returns, proof-of-delivery link, customer statements |
| `mos_quality` | test methods and parameters, QC policy with waiver and skip-lot, instruments and calibration, lab queue |
| `mos_gate_plus` | posts and weighbridges, plate search, expected arrivals, tolerance, clearance log, service visits, paper slips, register |
| `mos_production_plus` | recipes (grist), work orders, silo strapping and dip readings against the ledger, treatment holds |
| `mos_finance_plus` | self-billing, supplier statements, GRNI, VAT and withholding registers, journal export, payment runs |
| `mos_procurement_plus` | request for quotation with award, order closure, bag tare, service receipts, supplier invitation and registration, procurement reports |
| `mos_platform_plus` | role panels on the overview, access review and role-grant log, working-site choice, identity-group role mapping, idle session limit, stock overview, stock balance with history, receipt valuation, period close checklist, soft-closed periods, exception digest, Ethiopian dates |
| `mos_planning_plus` | versioned routings, MRP-lite suggestions with pegging, labour and overhead costing with variances, soft stock reservations, purchase contracts with call-offs |

| `mos_stock_plus` | lots (batch numbering, supplier lot, expiry, category attributes, split and trace, expiry list), transfers between sites through an in-transit location with transit-loss write-off and late alerts, supplier product and service categories |
| `mos_gate_multi` | one truck against several purchase orders: intermediate weighings, allocation of the net weight, a receipt per document |
| `mos_notify` | supplier notices (arrival, rejection note with results, receipt with deductions, payment advice) queued as e-mail; recorded when a supplier has no address |
| `mos_supplier_portal` | a supplier signs in to see only its own orders, promise delivery dates, answer requests for quotation, send invoices (checked by content, reviewed by payables) and read its statement; portal invitation by a procurement manager |
| `mos_operations_ui` | the screens that were missing (weighing, blind verification, re-weigh, resolve or dismiss an exception, void a cheque, split a lot, order from a request, call off a contract, soft-close and more), search views for 87 lists, plate search on visits, read access to confirmed orders for the gate and lab roles |
| `mos_master_plus` | unique tax numbers, duplicate-name warnings, individual suppliers and the no-TIN withholding rate, item conversions frozen once used, quality concessions through the approval matrix, heavy reports as background jobs |
| `mos_theme` | the look of the client's screens: navy top bar, blue actions, rounded status pills, section bars, summary cards (2026-10-28) |
| `mos_stock_cards` | the client's **Bin card** and **Stock card** screens (FR-INV-023/024): filters, summary cards, stock by bin, transactions with a running balance, print and export; read from the one stock ledger; a transfer between two bins is net zero; a warning when stock on hand has no movement behind it (2026-10-28) |
| `mos_dashboards` | the **approvals dashboard** (total, pending, approved, rejected, returned, average approval time, past the time limit, a six-month trend; each figure opens its list) and the **mill overview as cards** (today, this month, needs attention) (FR-PLT-030, FR-RPT-001) |
| `mos_documents` | **typed attachment slots with Verify** on the purchase request and order, the delivery, the supplier invoice and the payment voucher (FR-PLT-044); **optional approval of every supplier invoice** (FR-AP-020); optional rules: verified invoice document before matching, deposit slip before a bank transfer |
| `mos_deliveries` | the **supplier delivery entry** screen (FR-PRC-023): delivery note, items from the order with declared quantities and totals per unit, approve to inspection, reject with a reason, optional approval before weighing |
| `mos_request_forms` | the purchase requisition as the client draws it (FR-PRC-007): request date, priority, requester ID, remarks, live available stock and quantity to purchase per item (raise with a reason, never lower), specification, notes; **PART 1 / PART 2** layout on the request and the purchase order |
| `mos_counts` | **blind counting**, a **printable count sheet**, **import of counts from CSV or Excel** (all rows or none), a **recount by somebody else** when a variance is above a limit, and a **review that can approve or ask for changes** (FR-INV-009 as amended in FRD 0.3.6); the recount limit and the review are settings, off by default |
| `mos_process_map` | the **process map**: the whole mill on one board in four lanes (buy and receive, make, sell and collect, control), each stage with what is waiting in it, what needs attention and a click into its list, built from the same records and shown only if the person may read them; and the **document trail** button on a purchase or sales order, listing every linked document in the order the work happened with a click into each (2026-10-28) |

**Left, by decision or by dependency (2026-10-18):** e-mails (supplier notices, the daily exception digest, portal invitations) are queued but leave the server only when the owner places an outgoing mail server (`runtime/saas/smtp.json`; the company mailbox attempt was refused and is not retried). The Amharic pack is a **draft** (2,186 entries: menus, field labels, selections, help texts; error messages stay in English; `mos_supplier_portal` pages are English) and needs a native speaker's review. The FRD tags these **Later** and they are not built: the weighbridge edge agent, thermal label printing, ANPR and RFID (FR-GATE-026/027/029), API keys for devices (FR-PLT-016; Odoo has its own) and the optional DMS archive connector. Soft-close is one flag per area (payables, receivables, inventory, production) rather than a status per module. Multi-document trucks cover inbound purchase orders; outbound delivery orders still use one visit each. The no-TIN withholding rate is a system parameter that stays empty until the client confirms the rate. `tools/ui-smoke.mjs` (with `tools/seed_smoke_user.py`) opens every menu action, every view type and the first record of each list in a real browser; it found and fixed one broken screen (the approvals inbox).

**Planning status (2026-10-03):** planning only; **implementation not started** (it waits for the owner's go-ahead and the client's choice of team path). **Everything now fits in 26 weeks** ([ADR-0025](docs/6-decisions/adr/ADR-0025-everything-in-six-months.md)): build W1–W20 with rolling UAT, test server by W4, production server by W16, system test and final UAT W21–W22, training W23, **go-live W24**, hypercare W25–W26. Speed needed: **path A+ (3 developers + QA) ×1.33**, path A ×1.89, path B ×2.22; path C does not fit. The client's v2 documents are reviewed and their effort counted (core 2,439 h). Discussed with the client on 3 October ([discussion pack](docs/8-meetings/2026-10-03-client-documents/DISCUSSION-PACK.md)). **Price: fixed at ETB 986,615** within the owner's ETB 1,000,000 cap ([pricing and timeline](docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.md); register A-43). Open items: the [decision register](docs/6-decisions/MOS-DECISION-REGISTER.md) (owner: O-34 team, O-27/O-28 stack). Start at [index.html](index.html), [README.md](README.md) or the [reading guide](READING-GUIDE.md).

## v0.3 planning programme

| # | Phase | Owner's points covered | Outputs | Status |
|---|---|---|---|---|
| 1 | **Audit and re-baseline** | 1 (re-check everything), 6 (single tenant/company, infrastructure as questions), the 6-month frame | [Audit](docs/7-reviews/MOS-PACKAGE-AUDIT-v0.3.md); FRD, TDD, implementation plan, master plan (`.md` + `.html`), client-draft review at v0.3; ADR-0017/18/19 and amendments; `9-inputs/owner-brainstorm/` | **Done** (owner go-ahead for Phase 2, 2026-09-27) |
| 2 | End-to-end process model | 3 (ChatGPT outputs → better flows and supporting doc) | [Brainstorm review](docs/7-reviews/MOS-BRAINSTORM-OUTPUT-REVIEW.md); [process model](docs/2-requirements/MOS-PROCESS-MODEL.md) + local `.html` (value chain, whole-ERP flow with 33 hand-offs, 21 swimlanes + 2 sub-processes with 199 steps, 27 lifecycles, gate step sequences, document chain, 36 events + 11 jobs, 64 exceptions, RACI, SoD, controls, KPIs, traceability); FRD/TDD/plan 0.3.1 | **Done** (owner go-ahead for Phase 3, 2026-09-27) |
| 3 | Database design | 4 | [Database design](docs/3-design/MOS-DATABASE-DESIGN.md) + local `.html` (230 tables in 9 schemas, 3,304 columns, 1,144 foreign keys, 35 entity diagrams; conventions, deep dives with worked rows, transaction write-sets, invariants and reconciliation, operations, ownership matrix); TDD and FRD 0.3.2, process model 0.2 | **Done** (owner go-ahead for Phases 4–6, 2026-09-28) |
| 4 | Estimate, roadmap, Module 1 plan, pricing | 2 | [Implementation plan v0.4](docs/4-plan/MOS-IMPLEMENTATION-PLAN.md) (187-feature estimate, capacity per path, week-level roadmap, go-live hours); [procurement module plan](docs/4-plan/MOS-PROCUREMENT-MODULE-PLAN.md) + `.html` (48 stories, UAT script, migration, training, go-live); [commercial proposal](docs/5-for-the-client/MOS-COMMERCIAL-PROPOSAL.md) + `.html` | **Done** (revised after the Phase 6 decisions) |
| 5 | Decision register and stakeholder questionnaire | 5a, 6 | [Decision register](docs/6-decisions/MOS-DECISION-REGISTER.md) + `.html` (41 decided, 10 owner items open, 35 client business and 17 infrastructure questions; 54 earlier questions traced once each); [stakeholder questionnaire](docs/5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.md) + `.html` | **Done** |
| 6 | Architecture grilling and lock-in | 5b | Three question rounds with the owner; [ADR-0020](docs/6-decisions/adr/ADR-0020-six-modules-one-release-26-weeks.md) (delivery), [ADR-0021](docs/6-decisions/adr/ADR-0021-stack-lock-in.md) (stack), [ADR-0022](docs/6-decisions/adr/ADR-0022-hosting-and-operations-baseline.md) (hosting and operations); ADR-0001/0006/0012/0018/0019 amended; TDD 0.3.3, FRD 0.3.3, database design 0.1.1; register updated | **Done** |

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

## Implementation work packages (six-month plan; details in the [implementation plan](docs/4-plan/MOS-IMPLEMENTATION-PLAN.md) §6–§7 and the [sprint plan](docs/4-plan/MOS-SPRINT-PLAN.md))

Every package's core is in the release; standard and full features are pull-ins. Path B uses path A's weeks.

| WP | Title | Weeks (path A+) | Weeks (path A) | Status |
|---|---|---|---|---|
| WP-00 | Repository and tooling bootstrap | W1–W2 | W1 | Not started |
| WP-01 | Platform core | W1–W4 | W1–W3 | Not started |
| WP-04 | Web shell and shared services | W1–W4 | W1–W3 | Not started |
| WP-02 | Identity and access | W2–W4 | W3–W4 | Not started |
| WP-03 | Organisation and master data | W4–W8 | W3–W6 | Not started |
| WP-07 | Approvals and SoD | W4–W5 | W4–W5 | Not started |
| WP-05 | Inventory ledger engine | W6–W9 | W5–W7 | Not started |
| WP-08 | Procurement | W8–W13 | W6–W10 | Not started |
| WP-11 | Payables and supplier payment | W10–W15 | W8–W12 | Not started |
| WP-06 / WP-12 | Inventory documents, counts, close | W7–W11 | W10–W12 | Not started |
| WP-09 | Lab and quality | W11–W14 | W12–W14 | Not started |
| WP-10 | Gate and the automated inbound loop | W13–W17 | W13–W17 | Not started |
| WP-14 | Production | W14–W18 | W14–W17 | Not started |
| WP-15 | Sales and receivables | W15–W20 | W17–W19 | Not started |
| WP-13 | Hardening and feature freeze | W20 | W20 | Not started |
| WP-16 | Go live (servers, rolling UAT, system test, training, cutover, hypercare) | W3–W26; go-live W24 | W3–W26; go-live W24 | Not started |

Velocity checkpoints at **W4** and **W8** (ADR-0025).

## Open questions
All open items are in the [decision register](docs/6-decisions/MOS-DECISION-REGISTER.md) 0.2:
- **Part B:** 18 owner items, among them O-34 (which team to plan with), O-27/O-28 (stack, before S1), O-26 (who pays for capacity added after a checkpoint). The pricing basis (O-35) is decided: a fixed price (A-43).
- **Parts C and D:** 49 client business and 18 infrastructure questions, packaged for the client in the [stakeholder questionnaire](docs/5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.md) 0.2. The most urgent: the four sizing questions (Q-01–Q-04 of the 3 October pack) and the infrastructure questions for the test server, all **before W1**.

## Session log
- **2026-09-26:** Planning package v0.1 written: master plan, FRD, TDD, implementation plan, ADR-0001…0014, HTML view. No code.
- **2026-09-27 (a):** The client's procure-to-pay draft was reviewed, and the package revised to v0.2 (ADR-0015 local accounts, ADR-0016 system prepares / role confirms; MVP ≈ 10.4 months). No code.
- **2026-09-27 (b), planning Phase 1:**
  - **Reviews:** three parallel reviews of v0.2 and author verification produced 135 findings ([audit](docs/7-reviews/MOS-PACKAGE-AUDIT-v0.3.md)).
  - **Re-framed:** single company, no tenancy (ADR-0017); all six modules in ≤ 6 + 2 months with team paths A/B/C (ADR-0018); infrastructure not assumed (ADR-0019).
  - **Found:** the client is a flour mill ("Addis Flour Factory"). The swimlane is the owner's ChatGPT image, not the client's; it was moved to `docs/9-inputs/owner-brainstorm/` with the ChatGPT PDF, DOCX and ERP picture.
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
- **2026-10-02 (h), client documents v2** (owner: "analyze and audit them; plan how we integrate them; compare the stakeholders' architecture, design and stack decisions against ours and decide what to update or keep"):
  - Copied the three documents (PDF + DOCX) to `docs/9-inputs/from-client/` with a README.
  - Wrote [MOS-CLIENT-DOCS-V2-REVIEW.md](docs/7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md):
    - the v2 audit: 19 first-draft defects still present, 29 new findings;
    - the data-dictionary audit: 25 defects, 7 critical; 30 of 32 tables already covered; 14 fields to add;
    - the architecture comparison: 39 decisions;
    - the integration plan, with the effort, the owner decisions and the client questions.
  - **Recommended stack updates, awaiting the owner:** Vite SPA instead of Next.js (O-27); pg-boss on PostgreSQL instead of BullMQ + Redis (O-28); OpenAPI now.
  - **Incidental finding, fixed:** ADR-0022 assumed native Redis/Valkey on Windows Server, which doesn't exist; a correction note was added.
  - Nothing else changed (FRD, database design, TDD, plan, proposal and register untouched until the owner decides). Nothing published; no DDL or code.
- **2026-10-03 (i), sprint plan** (owner: "sprint by sprint, phase by phase, feature by feature implementation plan with clear timelines, tasks, deliverables, detailed for every module"):
  - Wrote [MOS-SPRINT-PLAN.md](docs/4-plan/MOS-SPRINT-PLAN.md) + local `.html`, generated from the implementation plan's Appendix A and its Gantt days:
    - seven phases (0 mobilisation … 6 maintenance and v1.1);
    - 13 build sprints and 4 go-live sprints, each with goal, features by stream, load against capacity, QA work, client inputs, deliverables and exit criteria;
    - per module, all 187 features with tasks, a "done when" test, requirements and the sprint on paths A/B and C;
    - a 51-item pull-in queue for standard features; the v1.1 list; where each pending INT item lands.
  - **Finding 1, disclosed:** dependency gaps leave the second developer about 260 estimated hours free (S5, S8, S11–S12). They are named as buffer slots with a use order: own overrun, then pending integration items, then relief of the lead's critical path, then pull-ins.
  - **Finding 2, fixed:** M4 (AS-13) was at W17, but QC core finishes in W18. The milestone reviews moved to sprint ends (M2 W10, M3 W14, M4 W18) in the implementation plan (0.4.1), the master plan (`.md` + `.html` timeline) and the procurement module plan (`.md` + `.html`). The velocity checkpoints stay at W4 and W9.
  - **Finding 3, disclosed (double-check pass, owner: "double check, make sure it is fully complete, detailed and clear"):** four of the FRD's 19 acceptance scenarios (AS-06, AS-10, AS-17, AS-19) consist only of standard-depth features, and eight more have a standard-depth step. The plan's §3.5 now maps every scenario to the sprint it can first pass, its core variant and the pull-ins its full variant needs. The release exit criterion is every core variant green.
  - **Added in the double-check:**
    - the acceptance-scenario map;
    - "depends on" and "hands off to" for every module;
    - go-live content per module (migration objects, UAT focus, training, go-live checks);
    - a "watch" line per sprint;
    - path C differences per milestone;
    - slot B0 (W1) and the QA capacity per sprint;
    - story ranges and line tables spelled out;
    - path C code review (AI review plus a checklist, as there is no second developer).
  - **Checks:**
    - every feature placed or queued once; hours 2,380 + 606 + 116 = 3,102;
    - sprint loads within about 8 h of capacity (10 h on path C); go-live hours 896 equal the implementation plan §8;
    - every one of the 230 tables, 48 stories, 19 scenarios and 205 v1 requirements is named, and every cited ID exists in its source;
    - links resolve; the Gantt chart renders in headless Edge; no horizontal scroll at 375 px on any tab.
  - Nothing published; no DDL or code.
- **2026-10-03 (j), client meeting pack and re-check of the client documents** (owner: fit everything, including deployment, testing, hosting and UAT, in under 6 months; plain wording and easy-to-read pages everywhere; check the client-input folder in detail; clean up the MOS folder; prepare the discussion material for the client's meeting on "the two documents"):
  - **Order chosen:** the meeting pack first, because the meeting is the same evening (7 PM local, "1 o'clock at night"), and its answers Q-01…Q-04 change what fits in six months. The other parts are phased below and wait for the owner's go-ahead.
  - **Re-check of the client inputs:** three parallel page-by-page and column-by-column passes over P2P v2 (28 images, every page viewed), the data dictionary (32 tables, 505 column rows) and the architecture proposal; DOCX against PDF for all three (identical content).
  - **Our review was corrected in place:**
    - "24 mock-ups" is in fact 28 images (27 distinct);
    - page 24 is a second version of page 23, not a copy;
    - at least 22 first-draft defects remain, not 19;
    - valuation is set per store, not per silo;
    - the dictionary's GRN line does separate rejected from short quantities;
    - the single-warehouse defect applies to the transfer header only;
    - the client's default language is JavaScript;
    - the client also asks for end-to-end tests and ER diagrams.

    **New sections:** §3.6 (11 new screen findings N-30…N-40, including one person in three roles, only wheat inspected, an invoice numbered three ways), §4.5 (13 missed client fields R-01…R-13, so 27 field additions in all), and §5.4 (CORS and other missing rows, the client's per-module page lists: non-conformance, production plans, finished-flour release, availability check, sales returns, plus branch connectivity). The review HTML was regenerated.
  - **Incidental finding, disclosed:** finished-flour QC release (f14i) is a pull-in, yet production output starts in QUARANTINE, so at core depth output could never be sold. Recommendation: promote f14i (and the availability check f15e) to core in the re-plan, about +16 h.
  - **Meeting folder** `docs/8-meetings/2026-10-03-client-documents/`:
    - `DISCUSSION-PACK.md` + `.html` + `.pdf`: agenda, the client's screens with each one's "keep / add / change / question", the data dictionary in plain words, the four decisions that size the work, 23 questions with defaults and answer columns, next steps, a glossary;
    - `OWNER-BRIEFING.md` + `.html`: for the owner only;
    - `img/`: the 28 screen images extracted from the client's PDF.
  - **Verification pass** (owner: "double check and verify your work"):
    - **Independent fact-check:** an agent checked about 95 claims in the pack against all 25 page images, the dictionary and the architecture proposal. It found 23 wrong or doubtful items, all corrected. The main ones:
      - the first draft had a grade and a score but **no price deductions**;
      - a failed result rejects **the wheat**, not the whole truck;
      - v2 has **no weighbridge** weights, but it does have weight quantities;
      - page 25 lists two invoices but pays one;
      - five spellings of the operation-unit column, not three;
      - the invoice step is already in the plan;
      - the Excel count import is already on page 13;
      - "SSO Login" and `is_primary` are the client's own terms.
    - **Own read-through:** stale counts in the briefing; a duplicated item; a contradiction in "we keep every field"; React Router presented as adopted although it depends on O-27; the next-steps range; glossary gaps (now 33 terms); an out-of-order §5.4 and a table that didn't render in the review.
    - **Pack:** it now says which additions come in the first release and which in the follow-up release, because blind count, the count import, "checked by" and invoice approval are standard depth today.
    - **Review:** its effort figures now include the re-check items INT-15…INT-18 (core +59, standard +16). New register rows C-48 (the module-list items), C-49 (one person in three roles) and I-18 (branch connectivity); C-24 updated (only wheat inspected). The briefing maps every pack question Q-01…Q-23 to its register row.
  - **Checks:**
    - every cited FR ID exists;
    - all 23 Q numbers are defined, and none is used without a definition;
    - all 28 screen images are present;
    - no markdown leaks into the three HTML views;
    - no sideways scroll at 375 px;
    - the pack prints to 28 pages;
    - 0 broken links across every MOS `.md`.
  - Nothing published, nothing committed, no code.
  - **Next (owner-gated):**
    - **Phase B, folder cleanup:** structure, archive the superseded documents, links.
    - **Phase C, the all-in six-month re-plan:** best after the meeting's answers on the gate and inspection.
    - **Phase D, plain-language and visual pass:** every document and HTML view, plus a shared glossary.
- **2026-10-03 (k), Phase B: folder cleanup and navigation** (owner: "organize and clean up, making navigation intuitive and easier on the MOS folder"):
  - **New layout.** `docs/` had 24 documents side by side. They are now in numbered folders, in reading order:
    - `1-overview`, `2-requirements`, `3-design`;
    - `4-plan`, `5-for-the-client`;
    - `6-decisions` (the register and `adr/`), `7-reviews` (the newest is current; the others are history);
    - `8-meetings` (was `meetings/`);
    - `9-inputs` (`from-client/`, was `client-input/`; `owner-brainstorm/`, was `reference-input/owner-brainstorm/`).
  - **File names are unchanged** (MOS- prefix), so references elsewhere and in the client's copies stay valid. 94 files moved; the 50 already tracked were moved with `git mv`, so git shows them as renames and their history follows.
  - **Navigation:**
    - **`index.html`:** a browser start page with a card per document (what it answers, version, "Open page" / "Text").
    - **`README.md`:** rewritten around an "I want to… → open" table and a folder map.
    - **`docs/README.md`:** the document map.
    - **A README in every numbered folder and in the meeting folder.**
    - **A breadcrumb line under every document title,** and a slim "← MOS start page" bar on every HTML view. The bar is hidden when printed, and is left off the client-facing discussion pack.
  - **Links:**
    - 278 links were recomputed by a script that maps each file's old location to its new one.
    - Plain-text path mentions were updated in 20 files, including the HTML footers and `CLAUDE.md`.
    - 21 link texts that showed old relative paths now show the file name.
    - `CLAUDE.md` gained a "Where things live" section and updated input notes.
  - **Checks:**
    - 712 links checked, 0 broken;
    - the database-design and process-model pages are intact (every embedded diagram present; sizes changed only by the path text);
    - no loose files left in `docs/` except its README;
    - the start page has no sideways scroll at 375 px;
    - a full backup taken before the move (scratchpad).
  - Nothing deleted, published or committed.
  - **Next:** Phase C (the six-month re-plan), ideally after the meeting's answers on the gate and inspection, then Phase D.
- **2026-10-03 (l), Phase C: everything in six months** (owner: "make it so that everything including deployment, testing, hosting, UAT etc. fits in under 6 months"):
  - **New calendar** ([ADR-0025](docs/6-decisions/adr/ADR-0025-everything-in-six-months.md)): build W1–W20 with rolling UAT from about W8; test server by W4, production server by W16; system test and final UAT W21–W22; training and migration rehearsal W23; **go-live W24**; hypercare W25–W26. Checkpoints W4 and W8. No reserve week.
  - **Estimate:** the client-documents review's items counted: 12 new features and 3 changed (199 features; core 2,439 h, standard 622, full 116, total 3,177). Hours for INT-10/INT-11 assume O-27/O-28 as recommended.
  - **Model:** a new six-month model (scratchpad `p9/model6.py`, same method as Phase 4/6): speeds needed **A+ ×1.33, A ×1.89, B ×2.22; C does not fit** (go-live work 598 h vs 336 h available). Without the gate module and with pass/fail inspection: A+ ×1.19, A ×1.59. Prices at US$5/h: A+ $22,190, A $17,390, B $13,390.
  - **Documents updated:** implementation plan 0.5 (rewritten, plain-language glossary); sprint plan 0.2 (regenerated: 13 sprints, A+ primary, A in every feature row, buffer slots, rolling UAT, go-live sprints S11–S13); Module 1 plan 0.2 (54 stories, UAT-22 delivery record); commercial proposal 0.3 (teams A+ and A; B and C marked "owner to confirm", O-34); decision register 0.2 (A-42; O-27…O-35; C-36…C-49; I-18; every needed-by week moved); questionnaire 0.2 (new section 12); FRD 0.3.5 (month tags M1–M5); master plan 0.5 (rewritten, Mermaid diagrams); ADR-0018/0019/0020 status notes; TDD month labels; review status; owner briefing §5, §6, §8.
  - **Web pages:** six documents that were text-only now have pages (FRD, TDD, implementation plan, and the three history reviews); eight pages rebuilt with one builder (`p9/house.py`), which fixes tables nested in lists that the old builder dropped, links `.md` → `.html`, and the start-page bar.
  - **Checks:** 60 Mermaid diagrams parse; every HTML link resolves; every md table appears in its page; no markdown leaks; no horizontal scroll at 375 px on any of the 18 pages; a full backup was taken first (scratchpad `mos_backup_pre_phaseC/`).
  - **Incidental fixes:** the master plan's HTML was a hand-made v0.3 page with stale labels (PostgreSQL 16+, "queue decided in Phase 6", 26 + 9 weeks); replaced. Long code and tokens wrapped on phones.
  - Nothing published. Code: none.
- **2026-10-03 (m), price cap, reading guide, platform reorganisation** (owner: "keep the price within 1 million ETB…"; "the step by step… order… navigation guiding me"; "do the same organisation… for DMS, GMS, SSO and the root"):
  - **Pricing and timeline** ([MOS-PRICING-AND-TIMELINE.md](docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.md) + `.html`, generated by `p9/build_pricing.py`): one **fixed price of ETB 986,615** (98.7% of the cap) for every given requirement (core, standard and full) and every go-live service, allocated over 4,027 hours of estimated work at ETB 245; broken down by phase, module, sprint (13 + v1.1), sprint × module, every one of the 199 features, the go-live services, and a pay-as-accepted schedule (10% at signature). Outside it: maintenance, new features, hosting, third-party services. Every breakdown adds up to the total (checked by script).
  - **Owner note (A-43):** at the US$5 rate card the six-month team costs ETB 3.59 M (A+), 2.82 M (A), 2.17 M (B); the fixed price covers about 27% of A+'s cost. Recorded in the register and the owner briefing, not in client documents.
  - **Commercial proposal 0.4:** the fixed price, packages, modules, payment schedule; team section rewritten (price independent of the team); change requests at ETB 810/h. Implementation plan, master plan, ADR-0025 and briefing now call the dollar figures "team cost at the rate card".
  - **Reading guide** ([READING-GUIDE.md](READING-GUIDE.md) + `.html`): 20 stops in seven parts with time, focus and a "done when" line each; a fast route (★, about 1 hour); the numbers to know; the question → page table; a pre-meeting checklist. Linked first on the start page and in the README.
  - **Platform reorganisation (root, SSO, GMS, DMS):** start pages and indexes added; dated records and transcripts moved to `history/`; old Office files and orphan manuals moved into labelled subfolders; stale bundle copies refreshed; banners on HTML pages older than their `.md` (see the root `CLAUDE.md`, "Other root folders").
- **2026-10-18, Odoo fit-gap (platform plan Phase 16, step 1, documents only):** [docs/3-design/ODOO-FIT-GAP.md](docs/3-design/ODOO-FIT-GAP.md) classifies all 231 FRD requirement ids against Odoo 20.0 Community and OCA (61 standard, 10 configuration, 29 OCA, 128 custom, 1 against Odoo's model, 2 outside v1), and records an OCA coverage check on the 20.0, 19.0 and 18.0 branches (1, 11 and 16 of 20 modules present). **Proposed [ADR-0026](docs/6-decisions/adr/ADR-0026-odoo-as-the-base-proposed.md): recommendation no-go for now; ADR-0021 stands.** Nothing published, no code, no `mos-odoo` repository, no SSO client. The HTML view of the fit-gap is not built yet. **Next: owner decision on ADR-0026.**
- **2026-10-18 (b), owner overrules ADR-0026: build MOS on Odoo 20.** Repository `mos-odoo` (private, local; `C:Usersdawitdevproductmos-odoo`): addons `mos_core` (sites, scoping, reason codes, hash-chained audit, posting rules), `mos_gate` (visits, weigh tickets, blind verification, re-weigh), `mos_lab` (versioned specs, inspections, usage decision), `mos_inbound` (flow A with system-prepared receipt), `mos_auth_oidc` (SSO login, code flow with PKCE). 14 automated tests pass on Odoo 20.0-20261015 (the full inbound flow, rejection, segregation of duties, immutability, audit tamper detection). SSO: a gated `mos` client (MOS_ENABLED=true) exists in the SaaS edition only; the company SSO is untouched. Odoo 20 finding: record rules and access rights became one `ir.access` model and the config-parameter API is typed (get_str/get_float); both cost time. **Not built:** approvals/SoD matrix, payables, production, sales hand-off, exception queue, reports, flow B/C. **Open:** a stable public address for Odoo (a quick tunnel is in use), whether ADR-0021 is dropped.

- **2026-10-28, UI towards the client's screens (phase UI-1; owner: "get it close to the MOS requirements and documentation including the UI").** Compared the live system with the client's 28 mock-ups: the UI was stock Odoo with no styling of its own and two screens were missing. Built: `mos_theme` (look), `mos_stock_cards` (Bin card, Stock card), and the QC inspection screen in `mos_lab` (standard range and Pass/Fail pill per parameter, QC summary cards: total, passed, failed, overall result, "Action required" notice, analyzed by / checked by, section bars). Checked in a real browser on the live system: no client errors, all 131 menu actions open (`tools/ui-smoke.mjs`). The browser check found a crash the unit tests could not (a field called `parent` is reserved in Odoo's view context); fixed. The cards also showed that the demo seed sets silo stock without movements (closing balance of the movements -3.9 million kg against 630,000 kg on hand), so the card now shows a warning with the gap instead of hiding it. Tests: 7 for the cards, 1 for the summary. **Left in this phase (next, in this order):** approval dashboards (FR-RPT-001: total, pending, approved, rejected, average approval time), the typed attachment slots with a Verify status (FR-PLT-044) and the "I agree" attestation on submit and approve (FR-PLT-025), the overview as cards, the supplier delivery entry screen (FR-PRC-023), the optional invoice approval (FR-AP-020), PART 1 / PART 2 headings on the request and order forms. Waiting for the owner's review before the next step.

- **2026-10-28 (b), UI phase 2: the rest of the list, without stopping** (owner: "no dont stop for review, do it all perfectly and carefully"). Built: `mos_dashboards`, `mos_documents`, `mos_deliveries`, `mos_request_forms`, `mos_counts` (the count screens of the client's pages 13 to 15 were missing the printable sheet, the import and the review with request-changes); in `mos_approvals` the optional "I agree" statements for the submitter and the approver (stored on the approval history; every module's own Submit button opens the dialog and finishes after the tick; bulk approval is off while on). **The FRD now holds FR-INV-023/024, FR-PRC-023 and FR-AP-020** (version 0.3.6; they had been waiting since the review), with FR-PLT-044 and FR-PLT-025 amended; the id counts, estimate and price are deliberately not recomputed (stated in the FRD revision row), and `MOS-FRD.html` was not rebuilt (its generator is not kept). Checked: every new screen in a real browser on the live system with no client errors; the whole statement flow with a buyer and a department manager (refused without the tick, accepted with it, text stored); the existing menu smoke test. Found and fixed on the way: the demo requests asked for wheat when the item was meant to be bags, lubricant and sieve cloth (the new rule correctly said nothing needs buying); internal callers of the submit method would have skipped the statement. Switched on in the live demo: the statements and the deposit slip; off by default: invoice approval for every invoice, verified invoice document, approval before weighing.
  - **Checks and tool fixes (same session):** 1,929 menu screens open for all 17 roles (`tools/role_check.py`, 0 failures); 133 menu actions open in a browser (`tools/ui-smoke.mjs`); the six-person delivery journey passes 3 times in a row. The journey tool had three faults of its own, all fixed: it took the first order in the list, which earlier runs had already received (it now picks an order for local wheat that still has something to receive); it never recorded the retention samples a larger load needs; and it pressed Enter in the search box before the suggestions were ready. `tools/seed_journey_orders.py` tops the open orders up, because each run receives one.

- **2026-10-29, the screens were not the client's screens** (owner, angry: "what kind of UI is that, I gave you specifics, did you even read my MOS folder"). Fair: the first UI pass put fields on stock Odoo forms in a blue theme. Read the 28 mock-ups and compared them with the running system in a browser; the honest list is [UI-PARITY.md](docs/3-design/UI-PARITY.md). **Built in this pass:** one mechanism in `mos_theme` that gives every MOS form the client's page (header, cards, button bar, history below); the sign-in page of screen 2; the supplier delivery entry of screen 7; the goods transfer of screen 12 (a new document, posted as one stock move, nine tests); the five-step supplier registration of screens 3 and 4 (a rail of steps, checks per step, several contacts and bank accounts, the business registration number now kept on the supplier). **Found in the real flow and fixed:** every new specification version renamed its parameters to "... (copy)" (model fix, test, migration); the demo's silo stock had no stock movements so every stock card closed below zero (demo and a repair part `0_ledger`); the registration, run as the administrator in a browser, was refused (the administrator may not create suppliers, by design: a buyer registers). The rest of the list was built later the same day: see the next entry.
- **2026-10-29, the rest of the client's screens, built and checked one after the other.** **Payment execution (25):** one payment pays several invoices with an amount for each (the allocation table the review called critical), amount in words, method rail, deposit slip kept with the voucher, approval information; a payment run and old payments follow the same rule, a reversal frees the invoices. **Supplier invoice (21):** several items, each matched on its own against the order and the receipts, per-item match grid, terms, due date, amount due; choosing a supplier now fills its VAT and withholding (the form used to save 0% tax). **Goods receiving (9)**, **payment request review (23-24)** with approve / send back / reject on the document itself, **count, review and adjustment (13-15)** with one total for each unit, **QC inspection (8)** fields, **priority pills (5)**. **Masters (16-20):** item, warehouse, store, bin and silo, whose switches and levels act: receipts, issues and transfers are refused where a warehouse, store or bin forbids them, nothing goes in beyond a capacity, a load above a silo's maximum is refused, a run cannot draw below the safety stock, the reorder report marks items below safety stock and caps a suggestion at the maximum. **Operation unit (2):** chosen after sign-in, shown in the user menu, the default of every new record, the unit of the overview. Open: demo money is dollars with cent prices (a re-seed with birr prices is needed), the purchase order is Odoo's form underneath, the ageing counts a reversed payment as paid. Every screen was opened as the role that uses it and the step it exists for was done in it. Details: [UI-PARITY.md](docs/3-design/UI-PARITY.md).
- **2026-10-28 (c), demo data in every state and the whole process on screen** (owner: "seed demo data everywhere it needs it and make sure the whole process from end to end is presented on the UI, everything visible, everything the system can and will do"). Counted every MOS model first: about 45 business models were empty (amendments, adjustments, transfers, returns, quotes, certificates, holds, concessions, revisions, supplier invitations, portal submissions, notices, lots, delegations, service receipts, receipt reassignments, work orders, labour, planning suggestions...) and many more held one or two rows. `tools/seed_lifecycle.sh` (six parts, `tools/seed_lifecycle_*.py`, helpers in `tools/seed_lib.py`) now fills them **by calling the actions the screens call**, as the people who would do the work, so every approval, separation of duties and rule is exercised by the demo data itself; each step is recorded under `mos_seed.<step>`, runs in a savepoint and is skipped when it already ran. Every document type now has records in each of its states (for example stock adjustments draft, waiting, applied, refused, cancelled; transfers draft, in transit, received complete, received short, recalled; concessions waiting, refused, applied, withdrawn; supplier returns draft, approved, dispatched, credited, cancelled; work orders draft, planned, running, done, cancelled). Also: a second site (Adama depot), lots with expiry and attributes (one expired, one expiring within 30 days), supplier standings (approved, candidate, suspended, blocked) with a portal user and invoices submitted through the portal, the notices switched on, a visitor and a contractor with tools at the gate, one truck carrying two orders weighed in steps, finished-goods inspections with packing from the released batch, payment runs, journal exports and guard shifts. **New in the UI (`mos_process_map`):** the **Process map** (first menu after the overview) shows supplier to payment, wheat to flour, order to cash and the controls as four lanes of stage cards with live counts, what needs attention and a click into each list; and a **Document trail** button on every purchase and sales order lists every document that belongs to it in order, from the request to the payment. **Found and fixed on the way:** (1) a certificate of analysis could only be created from code, so its screen could never fill: an **Issue certificate** button on the decided inspection now does it (lab manager, one at a time, a revoked one can be replaced); (2) every weigh ticket form crashed when opened (a chatter on a model that has none): removed, with a test that no MOS form shows a chatter without a thread; (3) an administrator was let through by the code that approves a decision revision but refused by its access rows: the row is added, with a test; (4) the earlier demo deliveries had no supplier on the visit, so supplier notices could never be sent and the skip-lot waiver never applied: filled in from the order (the waiver now shows in the demo); (5) the journey tool took orders the quality policy now waives, or that had a truck or an amendment waiting, and searched lists without waiting for the search: it now picks an order that will be inspected, `tools/seed_journey_orders.py` orders from the supplier with the shortest history, and a Process-map test checks that every state a card names exists. **Checked:** 134 menu actions open in a browser with no client error, and the screens that open empty are now listed at the end of the run (only the administrator's own approval inbox is empty, by design); 1,946 menu screens open for all 17 roles with 0 failures; the six-person delivery journey passes repeatedly; the process map and the document trail rendered and clicked through in a browser as administrator, buyer and sales manager. Left as it is by design: the MOS administrator role cannot read purchase or sales orders, so those two cards and their trails are not offered to it; every other role sees what it may read.
