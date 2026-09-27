# MOS (Manufacturing Operations Suite): Master Plan

| | |
|---|---|
| Document | Master plan: executive summary of the planning package |
| Version | **0.4**, draft for review (2026-09-28: planning Phases 4–6 complete; 0.3 on 2026-09-27) |
| Audience | Product owner and stakeholders; entry point for developers |
| Status | **Planning only.** No implementation has started and no code has been written |
| Package | This file · [FRD](MOS-FRD.md) (what) · [TDD](MOS-TDD.md) (how) · [Implementation plan](MOS-IMPLEMENTATION-PLAN.md) (when) · [Commercial proposal](MOS-COMMERCIAL-PROPOSAL.md) (price) · [Decision register](MOS-DECISION-REGISTER.md) · [Audit v0.3](MOS-PACKAGE-AUDIT-v0.3.md) (what was checked and fixed) · [Client draft review](MOS-CLIENT-P2P-DRAFT-REVIEW.md) · [ADRs](adr/) (why) |

> "MOS" is a working codename. The client's draft brands the sign-in page **"Factory ERP"**, a candidate name to confirm. One find/replace renames it before any external release.

**v0.3 in one paragraph.** The owner re-framed the package, and a line-by-line audit of v0.2 recorded 135 findings, each resolved or assigned to a later phase ([audit](MOS-PACKAGE-AUDIT-v0.3.md)). What changed:
- **Installation model:** one installation per company, with no tenancy in v1 ([ADR-0017](adr/ADR-0017-single-tenant-single-company-v1.md)).
- **Scope and time:** **all six modules in ≤ 6 months of development, then 2 months to go live** ([ADR-0018](adr/ADR-0018-lean-v1-six-months.md)).
- **Infrastructure:** none is assumed; hosting is decided after the stakeholders answer ([ADR-0019](adr/ADR-0019-infrastructure-not-assumed.md)).
- **Domain:** the client is a **flour mill**. Its draft inspects durum wheat for "Addis Flour Factory", which suggests "Floor Manufacturing" means flour.
- **Attribution:** the procure-to-pay swimlane was the owner's ChatGPT drawing, not the client's.

The FRD now has 231 requirements (205 in v1), each with an acceptance criterion; six of them were added in planning Phase 2 from the process model. The TDD designs production, sales and receivables for v1, and fixes three defects that would have failed in production.

**Planning Phases 4–6 (2026-09-27/28):**
- **Estimate:** v1 was estimated bottom-up at 3,102 AI-assisted build hours. Core depth of all six modules is 2,380 hours.
- **Commitment:** the owner fixed **one release with all six modules at core depth in 26 weeks, live at W35, on every team path** ([ADR-0020](adr/ADR-0020-six-modules-one-release-26-weeks.md)). Each path is planned at the velocity it needs (A ×1.32, B ×1.52, C ×2.50 the likely estimate), measured at W4 and W9.
- **Prices:** US$5 per hour, ETB 162.00 per US$. Build to go-live costs **$23,200 (A)**, **$18,000 (B)** or **$11,760 (C)**, plus maintenance from $150 a month ([proposal](MOS-COMMERCIAL-PROPOSAL.md)).
- **Stack locked:** Node 24, NestJS 12, PostgreSQL 18, BullMQ + Redis/Valkey, Next.js 16 ([ADR-0021](adr/ADR-0021-stack-lock-in.md)).
- **Hosting and operations:** on-premises first, with native or Docker packaging ([ADR-0022](adr/ADR-0022-hosting-and-operations-baseline.md)).
- **Open questions:** every one is in the [decision register](MOS-DECISION-REGISTER.md) and the client [questionnaire](MOS-STAKEHOLDER-QUESTIONNAIRE.md).

---

## 1. What MOS is

MOS is a **company-neutral** system for running a manufacturing plant's core operations. **v1 runs as one installation per company,** with deliberate seams to grow into multi-company and SaaS later. It covers six modules:

1. **Procurement,** including supplier payment: suppliers, requisitions, purchase orders, goods receipt, payment requests, approvals, payments.
2. **Inventory:** multi-site, multi-store and silo stock; lots; stock statuses; transfers; counts; silo measurement; valuation.
3. **Raw-material testing and laboratory:** specifications, inspection, grades and scores, decisions, quality price and weight deductions.
4. **Production:** recipes with co- and by-products, milling runs, packing, work orders, finished-goods release, extraction rate, downtime.
5. **Sales:** customers, credit control, orders, deliveries with outbound weighing, invoicing, receipts, returns.
6. **Gate management:** vehicles and goods in and out, weighbridge, delivery verification, exit control.

It includes a **payables/receivables sub-ledger** that exports journals to whatever accounting package the customer already uses. It is not a general ledger.

**The headline capability is the inbound raw-material loop.** A truck is weighed, and the lab inspects and grades the material. **On acceptance, the goods receipt is prepared automatically and the supplier payment process is triggered.** Nothing is re-keyed. By default, the storekeeper and the finance officer each confirm the prepared document.

---

## 2. Decisions taken

| Topic | Decision |
|---|---|
| Installation model | **One installation per company, no tenancy in v1** (ADR-0017). The company dimension is kept in the schema; multi-company and pooled SaaS are expansion stages with a written playbook |
| Scope and time | **One release: all six modules at core depth, built in 26 weeks, live at W35** (ADR-0018, ADR-0020). Standard and full depth are pulled in if the team runs ahead, otherwise v1.1 |
| Team | **Three paths, all delivering the same release; the client chooses:** A (2 devs + AI + QA), B (2 devs + AI), C (1 dev with heavy AI). Lead developer 56 h a week, second developer 48 h, QA 40 h |
| Infrastructure | **Not assumed** (ADR-0019); the proposal leads with **on-premises** hosting, native services or Docker Compose at the client's choice (ADR-0022). The hosting decision is needed before W18 |
| Industry | **Generic engine; industry specifics are data.** v1 ships the **grain/flour-milling pack** (the client's domain); other packs are Later |
| Finance | Operational sub-ledger + journal export. **No general ledger.** The client's "Finance & Accounts" tile needs that expectation confirmed |
| Identity | **Local username/password accounts by default** (TOTP for sensitive roles), plus optional SSO through any OpenID Connect provider (ADR-0015). The **operation unit** (a site) is chosen right after sign-in |
| Automation | **The system prepares, a named role confirms** (ADR-0016): the storekeeper confirms the GRN, and the finance officer confirms the payment request. Full automation is a setting |
| Platform reuse | **Standalone.** Uses the existing SSO only as an optional IdP; copies proven patterns from DMS; **does not use GMS** |
| Hardware | Weighbridge and barcode/QR **by manual entry with strong controls** in v1. Edge-agent weighbridge capture and label printers are Later |
| Stack (locked in Phase 6, ADR-0021) | Node.js 24 LTS, TypeScript, NestJS 12 + Vitest, Zod, Drizzle ORM, PostgreSQL 18, BullMQ 6 + Redis/Valkey behind a port, Next.js 16 / React 19 |
| Environments | Development, staging and production (ADR-0022); only development exists before the hosting decision |
| Locale | English / Gregorian in v1; the Amharic + Ethiopian-calendar pack is Later |
| Documents | Kept in this repository (private GitHub remote); never published as claude.ai pages |

---

## 3. Scope

| Module | v1 (month) | v1.1 | Later |
|---|---|---|---|
| Platform | Local + optional SSO sign-in, TOTP, operation unit, roles and site scopes, departments, **approval matrix** with bulk actions and delegation, SoD, numbering, periods, sealed audit log, attachments, notifications, imports (M1–M2) | Breached-password check, general approval conditions, parallel/quorum steps, IdP group mapping | Multi-tenant SaaS, public API, API keys, Amharic/EC pack |
| Procurement + payables | **5-step supplier registration** with bank-change control, individual suppliers, approved supplier list, **PR with live available stock**, quotations on the PO, POs with amendments and **issue to supplier**, **GRN** (bagged receipts, mixed lines, dispositions), returns; **PV** drafted from the GRN (gross/WHT/net), approval, **payment with cheque register and proof**, WHT certificates, 3-way match, debit notes, self-billing, tax registers, **CSV journal export** (M1–M3) | Supplier self-registration link, GRN PO-reassignment, advances, payment runs, supplier scorecard | RFQ comparison, contracts, supplier portal, accounting adapters, cheque printing |
| Inventory | Stock ledger, lots, five statuses incl. **damaged**, **warehouse → store/silo** with levels, silo dip measurement, fumigation holds, receipts, issues, transfers, adjustments, counts (freeze or cut-off), **stock balance with transaction history**, reservations, company-level moving average, period close (M1–M3) | Blend-lot genealogy, ageing reports | FIFO option, landed cost, serials |
| Lab / QC | Parameters, versioned specs, **inspection before unloading (gate on or off)** or in quarantine, **grades and quality score**, optional parameters, retests, concessions, decisions, **deduction and grade price rules** (M4); finished-goods QC (M5) | Replicates, statistical sampling, calibration register, generated CoA | Instrument import, skip-lot |
| Gate | Vehicles, drivers, blacklist, shifts, expected arrivals, **manual weigh tickets** (gap-free, blind verification), exit control, gate passes, yard board, incidents, **paper slips with back-entry** (M4); outbound sales dispatch (M6) | TV board, printed degraded pack, contractor visits, multi-document trucks | Edge-agent weighbridge capture, ANPR, barriers |
| Production | Work centres, **recipes with co- and by-products**, **milling runs** (grist, tempering, extraction rate), **packing**, issue and backflush, output by lot, finished-goods release, traceability and recall, **material-cost settlement**, downtime (M4–M5) | Routings, OEE, rework orders | MRP, labour/overhead costing, finite scheduling |
| Sales | Customers, price lists, **orders with credit checks**, reservations, **delivery orders with outbound weighing and gate exit**, invoicing with the fiscal reference, cash sales and cash sessions, advances, receipts, returns and credit notes, statements (M5–M6) | Quotations, dispatch planning, proof-of-delivery link, DSO/OTIF/margin | Fiscal device / e-invoicing adapter (**v1 if legally required**) |

---

## 4. The headline flow: inbound raw material

```
 Truck arrives ─► Check-in (supplier & PO valid) ─► Weigh (gross; photo; blind 2nd-person check)
   ─► Lab: sample → tests → "Calculate quality" → grade + score → decision
        ├─ Reject ─► no receipt; exit weight ≈ entry; rejection note to supplier ─► exit
        └─ Accept (optionally with grade differential / quality deduction / partial)
              ─► Unload to silo/store ─► Weigh (tare; bag tare deducted) ─► GRN PREPARED automatically
              ─► Storekeeper confirms storage unit, batch, rejected disposition ─► GRN generated
              ─► PV DRAFTED automatically ─► Finance officer confirms invoices & amounts (gross / WHT / net)
              ─► Approval (comments mandatory) ─► Payment recorded (cheque/transfer + proof) ─► Journals exported
```

- **With the gate module off** (the client's own drawing), the inspector creates the inspection with the truck's weights, and the GRN is prepared from it (flow C).
- Bulk materials are tested **before unloading**; packaged materials are received **into quarantine** first. This is configurable per item.
- Weight-based deductions reduce the **payable** quantity, never physical stock.
- 34 real-world exception cases are specified, each with a test.

---

## 5. Architecture at a glance

- **Modular monolith.** One API and one background worker share one PostgreSQL database, with one schema per module. Dependencies go in strict tiers, and upward calls use ports.
- **One installation per company.** There is no tenancy machinery. Database roles protect immutability: the application login can never update or delete posted records or ledgers, and a boot probe and a CI catalog test prove it.
- **Expansion seams:**
  - all SQL in repositories;
  - one unit-of-work entry point;
  - UUIDv7 keys;
  - a table registry;
  - the company dimension in the schema.

  These make multi-company and SaaS later additions (TDD Appendix A).
- **Posted records are permanent.** Receipts, issues, invoices, payments, QC results and approval history cannot be edited; corrections are reversal documents. A sealed, tamper-evident audit log records every action. **Status changes only through actions.**
- **The inventory ledger** records every stock movement with quantity and value, per storage unit, batch and status. Balances are maintained transactionally under ranked locking. Property-based tests prove the stock balance always reconciles.
- **Side effects go through a transactional outbox:** emails, PDFs, notifications, and automatic steps such as drafting payment requests. Nothing is half-done after a crash, and duplicates cannot occur. **The queue backend and file storage sit behind ports,** so the infrastructure decision doesn't change the code.
- **Controls are data:** approval matrices, segregation-of-duties rules, tolerances, grade tables and price rules.

The full design is in [MOS-TDD.md](MOS-TDD.md).

---

## 6. Controls built in (highlights)

- **Segregation of duties** (FRD §15.1):
  - The PO creator never posts its GRN.
  - Neither of them may approve a payment request covering their PO or GRN.
  - The payment request's requester cannot approve it, and its approver cannot record the payment.
  - Bank-detail changes need an independent approver and a call-back.
  - A lab analyst cannot approve concessions on their own results.
  - A weighbridge operator cannot verify their own weight.
  - Stock adjustments are approved by warehouse authority, never by QC.
- **Fraud-resistant supplier bank details.** Details are never edited in place. Changes need independent approval, a call-back and a cooling-off period. The payment is blocked if the details changed after approval, and the payment method is fixed at approval.
- **Payments.**
  - Only against an approved request, and never above the approved amount.
  - Cheque numbers are unique, cheque books are gap-checked, and voids are kept.
  - Proof of payment is attached.
- **Weighbridge integrity (manual weighing).** Photo evidence where a camera exists, blind re-entry by a second person, random forced re-tare, tare-history alerts, calibration enforcement, and gap-free weigh tickets.
- **Exit control.** **No outbound goods leave without a posted document and a weight within tolerance. This cannot be overridden.**
- **Sales.** Credit limits, floor prices and discount limits route orders to approval; cash-sale trucks can't leave unpaid.
- **Quality holds, periods and reversals.** Quarantined and fumigated stock can't be issued, shipped or paid for. Period close and backdating windows apply. Corrections are reversal-only, and exception reports cover the rest.

---

## 7. Deployment (defaults decided; the client's answers choose the option)

No hosting is assumed (ADR-0019). The options the TDD (§20.3) prepares, each with what it needs from the client:

| Option | Shape |
|---|---|
| On-prem, Windows Server | Node services (the house model), PostgreSQL service, IIS or Apache in front, local-disk file storage, backups to a NAS |
| On-prem, Linux | systemd services or Docker Compose, nginx in front, WAL archiving to an off-box target |
| Virtual server at a local or cloud provider | As on-prem Linux, with provider snapshots |
| Managed cloud | Containers plus managed PostgreSQL with point-in-time recovery |

The stakeholder questionnaire collects the answers.

**Defaults (ADR-0022):**
- On-premises first.
- Native services and Docker Compose both documented and tested.
- Nightly full backups plus WAL archiving, with a weekly restore test on staging.

**The hosting decision is needed before W18,** so the production environment is ready for the go-live window (W27–W35).

---

## 8. Timeline

Weeks are counted from the go-ahead (W1). This is the committed path A schedule; paths B and C use the same weeks at a higher planned velocity ([implementation plan](MOS-IMPLEMENTATION-PLAN.md) §6).

| Demo | Week | Delivered |
|---|---|---|
| M1 | W4 | Platform core, sign-in, shell; **first velocity checkpoint** |
| M2 | W9 | Roles and scopes, master data, silos, numbering, approvals; the stock ledger; suppliers and PRs; **second checkpoint** |
| M3 | W13 | POs, GRN with weights and bags, returns; invoices and matching; stock documents |
| M4 | W17 | PV → approval → payment; counts and close; inspection with grading and deductions: **the client's procure-to-pay (AS-13)** |
| M5 | W22 | Gate and weighbridge loop; milling runs, packing, costing |
| M6 | W26 | Sales, delivery through the gate, invoices, receipts; hardening; **feature freeze** |
| Go-live | W27–W35 | System test, UAT, data migration, training, production set-up, cutover, hypercare |

**The honest part:**
- At the likely estimate the six modules at core need 34 weeks on path A.
- The 26-week plan needs velocity ×1.32 (A), ×1.52 (B) or ×2.50 (C).
- The W4 and W9 checkpoints show early whether that holds. If not, the lever is added capacity, not the date or the core scope.

---

## 9. Top risks

| Risk | Mitigation |
|---|---|
| Gate, production and sales have no client input yet | Gate and production workshops before W14, sales before W18; month-end demos; the questionnaire's needed-by weeks |
| The 26-week plan needs velocity above the likely estimate (A ×1.32, B ×1.52, C ×2.50) | Checkpoints at W4 and W9; capacity added before W10 if behind; standard depth pulled in only if ahead (ADR-0020) |
| Burnout from 7-day weeks over 8 months | 80% focus factor; one rest day a week recommended; path A spreads the load; a third developer as relief |
| Client answers arrive late | Needed-by weeks (decision register); documented defaults |
| Hosting undecided | Portable design; on-premises default; decision needed before W18 |
| Sales invoices may legally need a fiscal device | Asked before W18; adapter interface ready; enters v1 through the change rule if required |
| Stock or valuation errors | The ledger is built and proven with property tests before any inventory screen |
| Small team (bus factor) | Every session is resumable from written docs, decision records, a progress tracker and acceptance tests |

The full register is in the implementation plan §13.

---

## 10. Honest assessment

- **The six-month commitment rests on AI-assisted velocity above the likely estimate.**
  - At the likely rate, path A would need 34 weeks for the six modules at core. The owner committed to 26, and the plan says so openly.
  - Standard and full depth (722 estimated hours) are v1.1 unless the team runs ahead. The client should see those lists before signing.
- **The client's draft is a strong start, but it has about 40 defects** (review §5):
  - payment requests reuse the requisition prefix;
  - status fields are editable dropdowns;
  - net weight appears before unloading;
  - the stock example counts rejection and damage twice and adds kg to pieces;
  - the screens use unrelated data;
  - currencies differ between PO and payment.

  Built as drawn, these would become bugs. The plan builds the corrected versions.
- **Production and sales are designed from flour-milling practice, not from the client's own description yet.** The workshops before W14 and W18 are essential.
- **Keeping people in the loop costs time.** The storekeeper and finance-officer confirmations match the client's accountability model, but an unconfirmed draft delays stock or payment. SLAs and dashboards make that visible.
- **The weighbridge starts manual.** This is acceptable only with the controls above.
- **AI assistance speeds up coding, not everything.** Client decisions, UAT, data migration and hosting still take calendar time, which is why the go-live window is two months.

---

## 11. What happens next

The v0.3 planning programme is complete ([PROGRESS.md](../PROGRESS.md)):
1. Audit and re-baseline: done.
2. End-to-end process model, with the review of the ChatGPT brainstorm outputs: done.
3. Database design: done.
4. Estimate, week-level roadmap, Module 1 plan and pricing: done ([implementation plan](MOS-IMPLEMENTATION-PLAN.md), [procurement module plan](MOS-PROCUREMENT-MODULE-PLAN.md), [commercial proposal](MOS-COMMERCIAL-PROPOSAL.md)).
5. Decision register and stakeholder questionnaire: done ([register](MOS-DECISION-REGISTER.md), [questionnaire](MOS-STAKEHOLDER-QUESTIONNAIRE.md)).
6. Architecture session with the owner: done (ADR-0020, 0021, 0022).

**Next:**
1. The owner settles the open items in register part B.
2. The proposal and questionnaire go to the client.
3. The client chooses a path and a start week.
4. W1 begins after the go-ahead. No code is written before that.

---

## 12. Document map

| File | Purpose |
|---|---|
| [MOS-MASTER-PLAN.md](MOS-MASTER-PLAN.md) | This summary |
| [MOS-FRD.md](MOS-FRD.md) | Functional requirements: 231 IDs (205 in v1) with tags and acceptance criteria, business rules, controls, NFRs, 19 acceptance scenarios (0.3.3) |
| [MOS-PROCESS-MODEL.md](MOS-PROCESS-MODEL.md) | End-to-end process model: value chain, whole-ERP flow with 33 hand-offs, 21 swimlanes with 199 numbered steps, 27 lifecycles, events, 64 exceptions, RACI, SoD, controls, KPIs, requirement traceability |
| [MOS-PROCESS-MODEL.html](MOS-PROCESS-MODEL.html) | Browsable, printable view of the process model. **Local file; open it in a browser** (diagrams need internet) |
| [MOS-DATABASE-DESIGN.md](MOS-DATABASE-DESIGN.md) | Database design: 230 tables in 9 schemas, every column and constraint, 35 entity diagrams, worked examples, transaction write-sets, invariants and reconciliation, operations, ownership matrix |
| [MOS-DATABASE-DESIGN.html](MOS-DATABASE-DESIGN.html) | Browsable, printable view of the database design, with a table finder. **Local file; open it in a browser** |
| [MOS-BRAINSTORM-OUTPUT-REVIEW.md](MOS-BRAINSTORM-OUTPUT-REVIEW.md) | Review of the owner's ChatGPT outputs: 29 findings, what was kept, where each question went |
| [MOS-TDD.md](MOS-TDD.md) | Architecture and technical design, design only (0.3.3) |
| [MOS-IMPLEMENTATION-PLAN.md](MOS-IMPLEMENTATION-PLAN.md) | Bottom-up estimate (187 features), capacity per team path, week-level roadmap, work packages, go-live hours, risk register (0.4) |
| [MOS-PROCUREMENT-MODULE-PLAN.md](MOS-PROCUREMENT-MODULE-PLAN.md) (+ `.html`) | Module 1 (procurement incl. supplier payment): 48 stories, hours, week plans, acceptance, UAT script, migration, training, go-live |
| [MOS-COMMERCIAL-PROPOSAL.md](MOS-COMMERCIAL-PROPOSAL.md) (+ `.html`) | Client-facing proposal draft: teams, prices by package and module, maintenance, hosting at cost, billing, terms |
| [MOS-DECISION-REGISTER.md](MOS-DECISION-REGISTER.md) (+ `.html`) | What is decided, what the owner and the client decide, infrastructure questions; every earlier question traced |
| [MOS-STAKEHOLDER-QUESTIONNAIRE.md](MOS-STAKEHOLDER-QUESTIONNAIRE.md) (+ `.html`) | The client questionnaire with answer fields and needed-by weeks |
| [MOS-PACKAGE-AUDIT-v0.3.md](MOS-PACKAGE-AUDIT-v0.3.md) | What the v0.3 audit found in v0.2 and how each finding was resolved |
| [MOS-CLIENT-P2P-DRAFT-REVIEW.md](MOS-CLIENT-P2P-DRAFT-REVIEW.md) | Screen-by-screen review of the client's draft: mapping, decisions, 40 issues, questions |
| [client-input/](client-input/) | The client's draft (PDF, DOCX), as received |
| [reference-input/owner-brainstorm/](reference-input/owner-brainstorm/) | The owner's ChatGPT outputs (swimlane, ERP picture, supporting document). **Not client documents;** reviewed in Phase 2 and superseded by the process model |
| [adr/](adr/) | Architecture decision records ADR-0001…0022 |
| [MOS-MASTER-PLAN.html](MOS-MASTER-PLAN.html) | Browsable HTML view of the package. **Local file; open it in a browser** |
| [../PROGRESS.md](../PROGRESS.md) | Planning-phase and work-package tracker |
| [../CLAUDE.md](../CLAUDE.md) | Conventions for AI-assisted sessions |
