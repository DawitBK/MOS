# MOS: Decision register

<!-- nav -->[MOS](../../README.md) › [6. Decisions](README.md)

| | |
|---|---|
| Document | Decision register: what is decided, what the owner decides, what the client decides, and the infrastructure questions |
| Version | **0.2**, draft for review (0.1: planning Phase 5, 2026-09-28; **0.2: 2026-10-03, the six-month calendar and the client-documents review**) |
| Date | 2026-10-03 (first version 2026-09-28) |
| Audience | Product owner; the client's sponsor and module owners (parts C and D) |
| Status | **Planning only; no implementation has started** |
| Companion docs | [Stakeholder questionnaire](../5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.md) (parts C and D as a form for the client) · [Implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) · [Commercial proposal](../5-for-the-client/MOS-COMMERCIAL-PROPOSAL.md) · [docs/6-decisions/adr/](adr/) |

---

## How to read this register

- **One row per decision or question.** Every open question raised in planning Phases 1–4 appears here exactly once. Part E shows where each earlier question went; duplicates were merged, and a merged row lists all its sources.
- **Needed by** is a week of the six-month plan ([implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) 0.5 §9), the earlier of paths A+ and A. W1 is the go-ahead week; go-live is W24 ([ADR-0025](adr/ADR-0025-everything-in-six-months.md)).
- **Default if unanswered:** what the team builds if the answer is late. The work continues on the default, and changing it later goes through the change rule.
- **Status** is Decided or Open. When an open item is answered, it moves to part A with the date and the answer. The questionnaire's answers come back into parts C and D.

| Part | Rows | Who decides |
|---|---|---|
| A. Decided | 43 | Done (ADRs, owner inputs, planning phases) |
| B. Open, owner | 18 (16 were decided in the Phase 6 session and 1 on 2026-10-03, all moved to part A) | Product owner |
| C. Open, client business | 49 | The client's sponsor and module owners |
| D. Open, client infrastructure | 18 | The client's IT |

---

## A. Decided

| ID | Decision | Why | Source | Reversible | Confidence |
|---|---|---|---|---|---|
| A-01 | Modular monolith: one PostgreSQL schema per module, in dependency tiers; lower tiers call higher ones only through ports | One deployable for a 1–3 person team; module boundaries still enforced by lint and grants | ADR-0003; database design §2.1 | Costly | High |
| A-02 | One installation per company, no tenancy in v1; UUIDv7 keys and a company column keep the SaaS path open | Removes RLS and tenant machinery from a 6-month build; instance-per-customer needs no schema change | ADR-0017 | Costly | High |
| A-03 | v1 / v1.1 / Later tags replace MVP / P2 / P3; every module is contractual, depth is what moves | The client expects all six modules; depth is the honest lever | ADR-0018 | Yes | High |
| A-04 | Infrastructure is not assumed; the application stays portable (Linux or Windows Server, containers optional, 12-factor config) | The client infrastructure is unknown; guessing adds services the client may not run | ADR-0019 | Yes | High |
| A-05 | Local username/password accounts by default, TOTP for sensitive roles; OpenID Connect sign-in optional | The client draft shows local sign-in; SSO is not guaranteed at the plant | ADR-0015, ADR-0005 | Yes | High |
| A-06 | Posted documents are immutable; corrections only by reversal documents | Audit trail and ledger integrity | ADR-0007; FR-PLT-021, 022 | No | High |
| A-07 | Signed single-sided stock ledger with transactional balances; moving-average cost at company level | Correct stock and value under concurrency; FIFO derivable later | ADR-0008; FR-INV-010 | Costly | High |
| A-08 | Transactional outbox for every side effect; the queue sits behind a port | No half-done work after a crash; infrastructure-neutral | ADR-0006 | Yes (backend) | High |
| A-09 | "The system prepares, a named role confirms": GRN and PV are drafted automatically and confirmed by STK and APC; automatic mode is a setting | Matches the client accountability model and still removes re-keying | ADR-0016; FR-INB-002, 003, 010 | Yes | High |
| A-10 | Data-driven approval matrix (document type × amount band × site × category + flags) with sequential stages; SoD rules enforced at assignment and posting | Controls as configuration; lean engine for v1 | ADR-0010 as amended by ADR-0018 | Yes | High |
| A-11 | Gap-free numbering at the irreversible point, in the same transaction; PVs numbered at submission with the prefix PV- | Legal and audit numbering; PV- avoids the clash with PR- in the client draft | ADR-0011; FR-PLT-040, 041 | Costly | High |
| A-12 | Own attachment store on local disk by default; S3-compatible storage and virus scanning optional | No assumed object store or scanner | ADR-0013, ADR-0019 | Yes | High |
| A-13 | Standalone product; the gate is built inside MOS; GMS is not used | Company-neutral product; GMS serves another purpose | ADR-0014 | Yes | High |
| A-14 | Weighbridge weights entered by hand with photo, blind verification and forced re-tare; direct capture is Later | No hardware integration inside 6 months; controls make manual entry safe | ADR-0018; FR-GATE-009, 010 | Yes | Medium |
| A-15 | The weighbridge station stays available when the Gate module is off (flow C) | The client own flow has no gate module but has weighbridges | FR-PLT-006 (FRD 0.3.2) | Yes | High |
| A-16 | Journals exported to the accounting package as CSV; no general ledger | Scope; the client keeps its accounting package | FRD §1.2; FR-AP-010 | Yes | High |
| A-17 | Grain/flour-milling industry pack only in v1 | The client is a flour mill (pending Q0) | ADR-0018; FR-MD-011 | Yes | High |
| A-18 | Supplier bank details never edited in place: independent approver, recorded call-back, cooling-off period; payment method fixed at approval | The main payment-fraud risk in procure-to-pay | FR-PRC-004, FR-AP-007 | No | High |
| A-19 | Keys UUIDv7; money, quantity, price and factor as numeric domains; statuses as text with CHECK, changed only by named actions | Portable, sortable keys; exact arithmetic; action-driven status | Database design §2; FR-PLT-031 | Costly | High |
| A-20 | One `fin.open_item` for every partner document; company paying accounts and tax codes in master data | One settlement model for AP and AR; removes two tier violations | Database design D-01…D-19 | Costly | High |
| A-21 | Usage decisions and approval decisions are insert-only | Decision history can never be rewritten | Database design §18; FR-PLT-054 | No | High |
| A-22 | English and the Gregorian calendar in v1; locale hooks from day one | Scope; Amharic and the Ethiopian calendar are a Later pack | FR-PLT-052 | Yes | High |
| A-23 | Estimate bottom-up by feature, tagged core / standard / full, AI-assisted hours with a ±25% range; scope per path chosen by schedule | Replaces the top-down month weights, which under-estimated by about a third | Implementation plan v0.4 §3–§5 | Yes | Medium |
| A-24 | Price basis: US$5 per team hour, quoted in ETB; lead developer 56 h a week; development includes testing and development/CI infrastructure | Owner input (2026-09-27) | PROGRESS.md; commercial proposal §1 | Yes | High |
| A-25 | Planning documents stay local in the MOS repository (never published to claude.ai); work runs phase by phase with an owner review | Owner instruction | MOS/CLAUDE.md | Yes | High |
| A-26 | All three team paths are laid out for the client, each delivering the same release; the client chooses (was O-01). **Under review for the six-month calendar: O-34** | Which team path is recommended to the client? | Phase 6 round 1; ADR-0020 | Yes | High |
| A-27 | **Calendar superseded by A-42 (2026-10-03); scope unchanged.** One release: all six modules at core depth, built W1–W26, live W27–W35, on every path; each path planned at the velocity it needs (A ×1.32, B ×1.52, C ×2.50); checkpoints at W4 and W9 (was O-02) | Which delivery option is recommended? | Phase 6 round 1; ADR-0020 | Yes | High |
| A-28 | Second developer works 48 h a week (6 days) (was O-05) | Second developer's week: 40, 48 or 56 hours | Phase 6 round 1 | Yes | High |
| A-29 | No free warranty period: defects after hypercare are handled under the maintenance retainer (was O-07) | Warranty after go-live | Phase 6 round 1 | Yes | High |
| A-30 | The client owns its build (full source, perpetual irrevocable right to use and change it); the vendor keeps the right to sell MOS to other companies; client data and configuration are the client's alone (was O-11) | Ownership and licence model; source-code escrow | Phase 6 round 1 | Yes | High |
| A-31 | The stale v0.2 page on claude.ai was deleted (was O-14) | Delete the stale v0.2 page still published on claude.ai? | Phase 6 round 3; deleted 2026-09-28 | Yes | High |
| A-32 | Node.js 24 LTS (was O-15) | Node.js major version | Phase 6 round 2; ADR-0021 | Yes | High |
| A-33 | NestJS 12 on Express, with Vitest (was O-16) | NestJS 11 (CommonJS, Jest) or 12 (ESM, Vitest, built-in schema validation) | Phase 6 round 2; ADR-0021 | Yes | High |
| A-34 | PostgreSQL 18 (was O-17) | PostgreSQL major version | Phase 6 round 2; ADR-0021 | Yes | High |
| A-35 | BullMQ 6 + Redis 7.2 / Valkey behind the JobQueue port (was O-18) | Queue backend behind the JobQueue port: Redis/Valkey + BullMQ, BullMQ on PostgreSQL, or pg-boss | Phase 6 round 2; ADR-0021 | Yes | High |
| A-36 | core.audit_event is not partitioned in v1; partitioning becomes a planned migration if it nears 50 M rows (was O-19) | Partition `core.audit_event` by month from day one | Phase 6 round 2; ADR-0021 | Yes | High |
| A-37 | Next.js 16 house stack (was O-20) | Frontend: house Next.js 16 stack or a Vite single-page app | Phase 6 round 2; ADR-0021 | Yes | High |
| A-38 | Both packagings documented and tested; the client chooses native services (PM2 optional) or Docker Compose; on-premises hosting proposed first (was O-21) | Default production packaging to propose: native Node services (house style, PM2 optional) or Docker Compose | Phase 6 round 3; ADR-0022 | Yes | High |
| A-39 | dev + staging + production; private GitHub repository with GitHub Actions (was O-22) | Environments, CI/CD and repository hosting | Phase 6 round 3; ADR-0022 | Yes | High |
| A-40 | Nightly full backup + WAL archiving, weekly restore test on staging; Pino logs, health, metrics, alerts (was O-23) | Backup, restore and observability baseline to propose | Phase 6 round 3; ADR-0022 | Yes | High |
| A-41 | FRD month tags re-aligned to the path A schedule (FRD 0.3.3) (was O-25); **re-aligned to the six-month schedule in FRD 0.3.5** | Re-align the FRD month tags to the chosen path and option | Phase 6; FRD 0.3.3 | Yes | High |
| A-42 | **Everything inside 26 weeks:** build W1–W20 with rolling UAT, test server by W4, production server by W16, system test and final UAT W21–W22, training W23, **go-live W24**, hypercare W25–W26; scope unchanged (six modules at core); checkpoints at W4 and W8; speeds needed A+ ×1.33, A ×1.89, B ×2.22; path C does not fit | Owner instruction: "everything including deployment, testing, hosting, UAT fits in under 6 months" | Owner, 2026-10-03; ADR-0025 | Yes | High |
| A-43 | **Price: one fixed price of ETB 986,615** (about US$6,090), within ETB 1,000,000, for every given requirement (core, standard and full) and every go-live service; allocated by estimated work at ETB 245 per hour; paid as work is accepted. Outside it: maintenance, new features, hosting and third-party services (was O-35) | Owner instruction (2026-10-03). **Owner note, internal:** at the US$5 rate card the six-month team costs ETB 3.59 M (path A+), 2.82 M (A) or 2.17 M (B); the fixed price covers 27%, 35% or 46% of that, about ETB 222 per team hour on A+ instead of ETB 810 | Owner, 2026-10-03; [pricing and timeline](../5-for-the-client/MOS-PRICING-AND-TIMELINE.md) | Yes | High |

---

## B. Open decisions for the owner

The Phase 6 session (2026-09-28) decided 16 owner items; they are now rows A-26 onward in part A, each noting its old number. O-27…O-33 come from the [client-documents review](../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md) §7.5; O-34 from the six-month re-plan (O-35, the pricing basis, was decided as A-43). These remain open:

| ID | Decision | Recommendation | Needed by | Cost of a late decision |
|---|---|---|---|---|
| O-03 | Go-ahead date (W1) | After contract signature and the "before W1" client answers | Contract | Every needed-by week shifts |
| O-04 | Product name for the client ("Factory ERP", MOS or other); also a client question (C-02) | Use the client name for its installation; keep MOS as the product codename | Before W1 | Branding in templates is a find/replace; low |
| O-06 | Is QA billed at the same US$5 rate? | Yes, one rate for every team hour keeps the offer simple | Before the proposal | Prices depend on it |
| O-08 | Maintenance: minimum term and unused hours | 12-month minimum; unused hours carry over one month | Before the proposal | Legal terms |
| O-09 | Exchange-rate clause | Invoice at ETB 162.00 per US$; switch to the NBE rate of the day if it differs by more than 5% | Before the proposal | FX risk (R24) |
| O-10 | Travel and expenses | Visits in the team city included; beyond it at cost with approval, travel time at the hourly rate | Before the proposal | Legal terms |
| O-12 | Billing model, fixed-price premium and payment terms | Hybrid (monthly hours capped at plan; go-live as a fixed fee); 10% premium if fixed price; 15 days | Before the proposal | Legal terms |
| O-13 | Import supplier bank accounts as PENDING and verify them in W23 (instead of bypassing the call-back rule) | Yes; pay by cheque until an account is verified | Before W17 (migration) | Go-live payments by transfer delayed |
| O-24 | Offline gate and label printing | Paper mode with back-entry (no offline app); PDF labels, no direct printer driver in v1 | Before WP-10 | Gate scope |
| O-26 | Who pays for capacity added after a W4/W8 checkpoint shows the team behind (a third developer or extra hours)? | Decide before the proposal; either the vendor absorbs it (fixed commitment) or it is billed with the client's approval | Before the proposal is sent | The proposal carries an [owner to confirm] marker |
| O-27 | Frontend: a Vite single-page app, or keep Next.js 16 (A-37)? | **Vite SPA** (review U-1); the estimate assumes it (−4 h) | Before S1 | S1 tasks `f04a`, `f04m` |
| O-28 | Job queue: pg-boss on PostgreSQL, or keep BullMQ + Redis/Valkey (A-35)? | **pg-boss** (review U-2); the estimate assumes it (−6 h); no Redis to install on Windows | Before S1 | S1 tasks `f00d`, `f01j` |
| O-29 | The supplier delivery as the gate visit in both modes, or a separate procurement table? | **One arrival record (the visit) in both modes** | Before S3 | Feature `f08s` |
| O-30 | Supplier-portal elements (raised by suppliers) in v1? | **No**; a separately priced change (INT-14) | Before the proposal | Scope |
| O-31 | Present the offer in the client's eight module names, with the gate shown separately? | **Yes**, with a mapping table | Before the proposal | Presentation |
| O-32 | Send the client the feedback note and the new questions? | **Yes**, after the owner's review | After the 3 October meeting | Client answers |
| O-33 | Position on the data dictionary: "every field captured, our table design" | **Yes** | Before the proposal | Design |
| O-34 | **Which team to plan with** for the six-month calendar (ADR-0025)? The client price is fixed (A-43), so this decides cost and risk, not price | **A+** (×1.33); A only with the gate and grading answers (×1.59) and the second developer at 56 h (×1.49); not B (×2.22) or C (does not fit) | Before W1 | Hiring the third developer |

---

## C. Client business decisions

### C.1 General

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-01 | Does "Floor Manufacturing" mean **flour** (the draft inspects wheat for "Addis Flour Factory")? | Decides the industry pack and every example | Flour milling | Before W1 | Sponsor | Q0, BQ-28, A-02 |
| C-02 | What is the product called at your company: "Factory ERP" (as on the sign-in mock-up) or another name? | Branding on screens and documents | "Factory ERP" | Before W1 | Sponsor | Q1 |
| C-03 | The **operation unit** chosen at sign-in: a plant (site), a legal company, or a department? | Sets the organisation model and access scopes | A site (plant) | Before W1 | Sponsor | Q9 |
| C-04 | **Currency:** PRs and POs show USD, invoices and payments ETB. Are some purchases in foreign currency? How are they converted and paid? | Document currency, exchange-rate storage and FX variance postings | One currency per document; USD POs keep their rate; payment in ETB with FX variance | Before W1 | Finance | Q11, P-14, DB-USD |
| C-05 | Who are the users, with roles, departments and sites? Who is the project sponsor, and who is the key user per module? | Accounts, scopes, approvers, UAT | — (needed) | Before W1 (sponsor, key users) / W3 (users and roles) | Sponsor / HR | DATA-USERS |
| C-06 | How long must documents and audit records legally be kept? | Retention and archiving | 10 years online | Before W17 | Finance / legal | PM-7, BQ-42, DB-RET |

### C.2 Approvals

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-07 | Approval bands for PRs, POs, PVs, adjustments and bank-detail changes: who is "Management", and which amounts go to whom? | Seeds the approval matrix | Two stages above a threshold, one below; PVs always approved | Before W4 | Management | Q5, BQ-33 |

### C.3 Procurement

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-08 | Is a PO without a PR ever allowed (never, or for named categories)? | PO validation | Never | Before W6 | Procurement | Q6 |
| C-09 | Should suppliers self-register through a link ("Supplier Portal" on the draft)? Are there individual suppliers (farmers, traders) without a TIN? | Self-registration is v1.1; individuals need self-billing and the no-TIN rate | Procurement registers suppliers; individuals allowed | Before W6 | Procurement | Q12, F-32 |
| C-10 | Which documents must a supplier provide per supplier group (licence, TIN, VAT, bank letter), and which expire? | Onboarding checklist | Trade licence, TIN, VAT certificate, bank letter | Before W6 | Procurement | DATA-KYC |
| C-11 | How many quotations are needed per amount band, and when is sole source allowed? | Sourcing policy on the PO | Three above a band, justification otherwise | Before W6 | Procurement | DATA-SOURCING |
| C-12 | Quantity and price tolerances between PR, PO, GRN and invoice | Over-receipt, over-ordering and matching rules | PR→PO 0%; receipt +2%; invoice price ±1% | Before W6 | Procurement / finance | PM-4, BQ-32 |
| C-13 | Confirm: partial deliveries and several GRNs against one PO are allowed | Designed that way | Yes | Before W6 | Procurement | BQ-31 |

### C.4 Payables

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-14 | Are invoices received before or after the goods, and at gross or accepted weight? | The matching basis | After the GRN, at payable weight | Before W8 | Finance | Q8 |
| C-15 | Should the person who pays be different from the person who raised the payment request? (The draft shows the same finance officer) | An SoD rule | The same person may raise and record, never approve | Before W8 | Finance | Q7 |
| C-16 | Payment methods and paying accounts (banks, cash boxes, mobile money); can the method change after approval? | Paying accounts and re-approval rule | Transfer and cheque; a change needs re-approval | Before W8 | Finance | Q13 |
| C-17 | Is the company a withholding agent? Current VAT and withholding rates and thresholds, including the no-TIN rate | Tax codes are seed data | Rates entered by finance before go-live | Before W8 | Finance | Q14, BQ-41, F-05 |
| C-18 | The WHT certificate layout and numbering rules | Certificate template | A generic certificate with a gap-free number | Before W8 | Finance | PM-2, DB-WHT |
| C-19 | Confirm: three-way match is a hard control before payment (services by confirmation; farmers by self-billing) | Designed that way | Yes | Before W8 | Finance | BQ-34 |
| C-20 | Which accounting package receives the journals, and in which import format? | CSV export layout and account map | A generic journal CSV | Before W8 | Finance | PM-6, BQ-40 |

### C.5 Inventory

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-21 | Rejected quantity on a GRN: unloaded and held, or left on the truck? Then return, replacement or disposal? | Disposition rules and stock status | Both allowed, chosen per line | Before W7 | Stores / QC | Q4, BQ-35 |
| C-22 | Empty bags: returned, kept as packaging stock, or credited? The tare per bag type | Payable net weight | Returned; 0.15 kg per bag | Before W7 | Stores | Q15 |
| C-23 | Stores, silos and their capacities (and silo geometry for dips); who approves transfers between stores? | Storage set-up and transfer approval | Warehouse supervisor approves | Before W3 (list) / W7 (approvers) | Stores | Q10, BQ-36, DATA-STORAGE |

### C.6 Lab and QC

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-24 | The item list with lot tracking, inspection requirement and inspection point; the test ranges per raw material | QC policies and specs | The grain pack defaults | Before W10 | QC | PM-5, BQ-29, BQ-30 |
| C-25 | Grading: worst parameter or score bands? The actual grade table and score weights behind "92.4 / 100" | Grade and score model | Worst-parameter grading; equal weights | Before W1 (pass/fail or grades: Q-02) / W10 (the grade table) | QC | Q2, F-37 |
| C-26 | Price effect of quality: a differential per grade, deductions per parameter, or both? | Payable quantity and price | Moisture and impurity deductions | Before W1 (Q-02) / W10 (the rules) | QC / procurement | Q3 |
| C-27 | Can one truck be partly accepted? Who decides: the inspector or the QC manager? | Usage-decision rights | Yes; the QC manager | Before W10 | QC | Q16 |
| C-28 | Confirm the end-to-end procure-to-pay example (one supplier, PO, truck and payment) we will use for acceptance | Acceptance scenario AS-13 | The corrected example in the FRD | Before W4 | Procurement / QC | F-28 |

### C.7 Gate

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-29 | Gate workshop: documents checked in and out; weighbridge make, capacity and division; tolerances; who may override | Gate module design | The grain pack defaults | Before W1 (gate module or not: Q-01) / W10 (workshop) | Security / stores | Q17-GATE, BQ-39 |

### C.8 Production

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-30 | Production workshop: products, milling stages, extraction rates, by-products, packing sizes, fortification; reservation, issue, return and scrap | Production module design | The flour pack defaults | Before W10 | Production | Q17-MFG, BQ-37 |

### C.9 Sales

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-31 | Sales workshop: customers, price lists, credit rules, delivery documents, sales approvals | Sales module design | The designed defaults | Before W12 | Sales | Q18, BQ-38 |
| C-32 | Is a fiscal device or e-invoice legally required for sales invoices? | Could add a v1 integration | Fiscal reference recorded by hand | Before W12 | Finance / legal | Q18-FISCAL, F-07 |
| C-33 | Who posts customer invoices at the mill: sales staff, a billing clerk or finance? | Roles and SoD | A billing clerk in finance | Before W12 | Finance | PM-3 |
| C-34 | Are customers ever refunded in cash or by transfer, or are credit balances always offset? | A refund document would be added | Always offset | Before W12 | Finance | PM-1, DB-REFUND |

### C.10 Go-live

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-35 | Migration data extracts, UAT dates, training schedule and the go-live date | Go-live plan | — | Before W17 (migration extracts) / W21 (UAT key users, training dates, go-live date) | Sponsor | DATA-GOLIVE |


### C.11 From the client's v2 documents (added 2026-10-03)

The questions of the [client-documents review](../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md) §8. The 3 October discussion pack asks them as Q-01…Q-23; the pack-to-register map is in the [owner briefing](../8-meetings/2026-10-03-client-documents/OWNER-BRIEFING.md) §8. New evidence on C-03, C-04, C-16, C-17 and C-24…C-27 is in the review §8.

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by | Sources |
|---|---|---|---|---|---|---|
| C-36 | Is your architecture document a **requirement** or a **recommendation**? Will your own developers maintain or extend the system after handover, and with which skills? Do you need SMS notifications? | Decides how far we follow it | A recommendation; we deliver our stack with full source and documentation | Before W1 | Sponsor / IT | Review §8 (2026-10-03) |
| C-37 | Is the data dictionary the **required table design**, or the list of data you need captured? | Table design | The data to capture; our design captures every field | Before W1 | Sponsor | Review §8; pack Q-18 |
| C-38 | **Weighing and the gate.** Do you weigh trucks, and is the payable quantity the weighbridge net, the bag count or the delivery note? Do you want the gate and weighbridge module in this release? | Could remove about 190 core hours, or confirm them | Gate module in scope; weights typed when the gate is off | Before W1 | Sponsor / stores | Review §8; pack Q-01 |
| C-39 | **Who raises supplier payment requests:** your finance officer or the supplier? Should suppliers use the system at all? | A supplier portal is a separate scope | The finance officer; no supplier access in this release | Before W1 | Finance | Review §8; pack Q-03 |
| C-40 | Which documents or screens in v2 are final, and which are examples? Will a third revision with one coherent example follow? | Acceptance data (C-28) | The fields are final; the numbers are examples | Before W4 | Sponsor | Review §8; pack Q-05 |
| C-41 | **Supplier delivery entry:** who records it (procurement, stores, security), and what does "Approve" on it mean? | The delivery record | Stores record it; "approve" = matches an open PO, send to inspection | Before W6 | Procurement / stores | Review §8; pack Q-10 |
| C-42 | Should **every** supplier invoice be approved, in addition to the payment request, or only invoices with a match difference? | Optional invoice approval | Only match differences | Before W1 (sizing: Q-04) / W8 (the rule) | Finance | Review §8; pack Q-04 |
| C-43 | Are **rejected** (delivered, refused) and **short** (not delivered) quantities recorded separately? | Your GRN screen merges them | Separately | Before W6 | Stores | Review §8; pack Q-11 |
| C-44 | When raw material goes "for production", is it moved to a production staging store, or issued directly to the milling run? | Transfer vs issue | Issued to the work order | Before W10 | Production | Review §8; pack Q-12 |
| C-45 | Should counters see the system quantity or count blind? Do you count in Excel and import? | Count design | Blind; Excel import available | Before W7 | Stores | Review §8; pack Q-14 |
| C-46 | May an approved count be adjusted by a different figure? | Count integrity | No: a different figure needs a recount | Before W7 | Stores / finance | Review §8; pack Q-15 |
| C-47 | Do you budget purchases (the dictionary's budget code)? Record only, or should the system check budgets? | Budget code | Record and report only | Before W6 | Finance | Review §8; pack Q-16 |
| C-48 | Your architecture document lists non-conformance, production plans, finished-flour release, a stock check before sales-order approval and sales returns. Which do you need at go-live? | Scope at go-live | Finished-flour release and the stock check at go-live (now core); the rest after go-live | Before W1 | Sponsor | Review §8; pack Q-20 |
| C-49 | Can one person order, inspect **and** receive the same delivery? Which roles must be different people? | Segregation-of-duties rules | Three different people; logged administrator exceptions | Before W4 | Management | Review §8; pack Q-23 |
---

## D. Infrastructure questions for the client (ADR-0019)

The questions for the **test server** (I-01–I-04, I-06, I-15, I-16) are needed **before W1**, because the test server runs on the client's infrastructure from W4. The rest are needed before W12, so the production server is ready by W16 ([ADR-0025](adr/ADR-0025-everything-in-six-months.md)). Until then the designs in [TDD](../3-design/MOS-TDD.md) §20.3 stay proposals.

| ID | Question | Why it matters | Default if unanswered | Needed by | Answered by |
|---|---|---|---|---|---|
| I-01 | Where should the system run: your server room, a local data centre, or a cloud? Any rule that data must stay in Ethiopia? | The hosting option | On-premises | Before W1 | IT |
| I-02 | Which servers are available (CPU, memory, disk), physical or virtual, and which operating system (Windows Server or Linux, version)? | Sizing and installation | A new virtual server, 8 vCPU / 32 GB / 500 GB | Before W1 | IT |
| I-03 | May we run containers (Docker), or must services run natively? | Packaging | Native services | Before W1 | IT |
| I-04 | Is there a spare server or VM for a staging environment? | Testing updates before production | A smaller staging VM | Before W1 | IT |
| I-05 | Network: are the gate, weighbridge, lab, stores and offices on the same network? Wired or Wi-Fi? How reliable is it? | Station connectivity | Wired LAN to each station | Before W12 | IT |
| I-06 | Internet: speed and reliability at the plant; is remote access (VPN) allowed for support? | Support and updates | VPN for support | Before W1 | IT |
| I-07 | Power: is there a UPS for the server and the gate PCs? How often does power fail? | Degraded modes | UPS recommended | Before W12 | IT / facilities |
| I-08 | Backups: where can backups go (a second disk, NAS, off-site copy)? Who checks them? | Backup target | Nightly to a NAS plus an off-site copy | Before W12 | IT |
| I-09 | After a failure, how much data may be lost at most (RPO) and how long may the system be down (RTO)? | Backup frequency and recovery design | RPO 15 minutes, RTO 4 hours | Before W12 | Management / IT |
| I-10 | Domain name and certificate: will users reach the system by a name (e.g. erp.company.et)? Who manages DNS? | TLS and access | An internal name with a certificate | Before W12 | IT |
| I-11 | Email: is there an email server or relay MOS may send through (notifications, PO emails)? | Email is optional | No email; in-app notifications only | Before W12 | IT |
| I-12 | Sign-in: do you use Active Directory, Microsoft Entra ID or another identity provider, and should MOS use it? | SSO is optional | Local accounts with TOTP | Before W12 | IT |
| I-13 | Devices: PCs or tablets at the gate, weighbridge, lab, stores and production; printers (A4, label); barcode scanners; cameras for weighbridge photos | Screens and fallbacks | One PC per station; A4 printing; typing instead of scanning | Before W12 | IT |
| I-14 | Weighbridges: make, model, indicator, capacity, division and calibration date | Manual entry and tolerances (capture is Later) | Manual entry | Before W10 | Stores / facilities |
| I-15 | Security policy: password rules, MFA, who may administer the server, antivirus on servers | Configuration and hardening | Our defaults (ASVS L2) | Before W1 | IT |
| I-16 | Who is the IT contact for installation and support, and in which hours? | Go-live and maintenance | — | Before W1 | IT |
| I-17 | Other systems: banking platforms, the tax authority portal, the current accounting package; anything MOS must connect to later? | Integration roadmap (CSV export in v1) | CSV exports only | Before W12 | Finance / IT |
| I-18 | Are the branches (operation units) at other locations, and how do they reach the central server: internet, VPN or a private line? | One central server must be reachable from every branch | One site, or branches over VPN | Before W12 | IT |

---

## E. Where every earlier question went

| Earlier question | Register row |
|---|---|
| Q0 (Client-draft review §6 Q0) | C-01 |
| Q1 (Client-draft review §6 Q1) | C-02 |
| Q2 (Client-draft review §6 Q2) | C-25 |
| Q3 (Client-draft review §6 Q3) | C-26 |
| Q4 (Client-draft review §6 Q4) | C-21 |
| Q5 (Client-draft review §6 Q5) | C-07 |
| Q6 (Client-draft review §6 Q6) | C-08 |
| Q7 (Client-draft review §6 Q7) | C-15 |
| Q8 (Client-draft review §6 Q8) | C-14 |
| Q9 (Client-draft review §6 Q9) | C-03 |
| Q10 (Client-draft review §6 Q10) | C-23 |
| Q11 (Client-draft review §6 Q11) | C-04 |
| Q12 (Client-draft review §6 Q12) | C-09 |
| Q13 (Client-draft review §6 Q13) | C-16 |
| Q14 (Client-draft review §6 Q14) | C-17 |
| Q15 (Client-draft review §6 Q15) | C-22 |
| Q16 (Client-draft review §6 Q16) | C-27 |
| Q17-GATE (Review Q17 (gate part)) | C-29 |
| Q17-MFG (Review Q17 (production part)) | C-30 |
| Q18 (Client-draft review §6 Q18) | C-31 |
| Q18-FISCAL (Review Q18 (fiscal device)) | C-32 |
| BQ-28 (Brainstorm review BQ-28) | C-01 |
| BQ-29 (Brainstorm review BQ-29) | C-24 |
| BQ-30 (Brainstorm review BQ-30) | C-24 |
| BQ-31 (Brainstorm review BQ-31) | C-13 |
| BQ-32 (Brainstorm review BQ-32) | C-12 |
| BQ-33 (Brainstorm review BQ-33) | C-07 |
| BQ-34 (Brainstorm review BQ-34) | C-19 |
| BQ-35 (Brainstorm review BQ-35) | C-21 |
| BQ-36 (Brainstorm review BQ-36) | C-23 |
| BQ-37 (Brainstorm review BQ-37) | C-30 |
| BQ-38 (Brainstorm review BQ-38) | C-31 |
| BQ-39 (Brainstorm review BQ-39) | C-29 |
| BQ-40 (Brainstorm review BQ-40) | C-20 |
| BQ-41 (Brainstorm review BQ-41) | C-17 |
| BQ-42 (Brainstorm review BQ-42) | C-06 |
| PM-1 (Process model §14.4 item 1) | C-34 |
| PM-2 (Process model §14.4 item 2) | C-18 |
| PM-3 (Process model §14.4 item 3) | C-33 |
| PM-4 (Process model §14.4 item 4) | C-12 |
| PM-5 (Process model §14.4 item 5) | C-24 |
| PM-6 (Process model §14.4 item 6) | C-20 |
| PM-7 (Process model §14.4 item 7) | C-06 |
| DB-RET (Database design §18.3 (retention)) | C-06 |
| DB-WHT (Database design §18.3 (WHT layout)) | C-18 |
| DB-REFUND (Database design §18.3 (refunds)) | C-34 |
| DB-USD (Database design §18.3 (USD POs)) | C-04 |
| F-05 (Audit F-05) | C-17 |
| F-07 (Audit F-07) | C-32 |
| F-28 (Audit F-28) | C-28 |
| F-32 (Audit F-32) | C-09 |
| F-37 (Audit F-37) | C-25 |
| P-14 (Audit P-14) | C-04 |
| A-02 (Audit A-02) | C-01 |

Also carried here:
- the database design's owner items (PostgreSQL version, audit partitioning) → decided in Phase 6 (was O-17, O-19);
- the TDD's Phase 6 items (Node, NestJS, queue) → decided in Phase 6 (was O-15, O-16, O-18);
- the audit's Phase 6 items T-03 and T-05 → decided in Phase 6 (was O-15, O-16, O-18);
- the Phase 4 commercial choices marked "[owner to confirm]" in the proposal → O-05…O-12 and O-26.

---

## F. Change log

| Date | Change |
|---|---|
| 2026-09-28 | First version (planning Phase 5) |
| 2026-09-28 | Planning Phase 6: 16 owner decisions recorded (A-26 onward; ADR-0020, ADR-0021, ADR-0022); O-26 added |
| 2026-10-03 | **0.2:** the six-month calendar (A-42, ADR-0025); every needed-by week moved to the new plan; O-27…O-35 added; C-36…C-49 and I-18 added from the client-documents review |
| 2026-10-03 | The fixed price within ETB 1,000,000 recorded (A-43; O-35 closed) |
