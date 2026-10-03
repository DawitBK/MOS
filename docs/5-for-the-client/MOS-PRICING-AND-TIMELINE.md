# MOS: Pricing and timeline (fixed price within ETB 1,000,000)

<!-- nav -->[MOS](../../README.md) › [5. For the client](README.md)

| | |
|---|---|
| Document | The price and the timeline of the Factory ERP (MOS), broken down by phase, sprint, module and feature |
| Version | **0.1, draft for the owner's review — not yet sent to the client** |
| Date | 2026-10-03 |
| Total price | **ETB 986,615** (about $6,090 at ETB 162.00 per US$), **fixed**, within the ETB 1,000,000 cap. Prices exclude VAT and withholding tax |
| Covers | Every requirement already given (all six modules: core, standard and full depth), and every go-live service: test and production servers set-up, testing and UAT, data migration, training, go-live and two weeks of close support |
| Does not cover | Maintenance after go-live (§9); new features or changes beyond the given requirements; hosting, servers, domains, certificates, email, third-party services and devices (bought at cost) |
| Plan | [Implementation plan 0.5](../4-plan/MOS-IMPLEMENTATION-PLAN.md) and [sprint plan 0.2](../4-plan/MOS-SPRINT-PLAN.md): everything in 26 weeks, go-live in week 24 |

---

## 1. Summary

- **One fixed price: ETB 986,615.** It is 98.7% of the ETB 1,000,000 cap, leaving ETB 13,385 unused.
- **What it buys:** all six modules (procurement with supplier payment, inventory, lab and quality, gate and weighbridge, production, sales with receivables), every given requirement, and everything needed to go live.
- **When:**

  | Weeks | What happens |
  |---|---|
  | 1–20 | The system is built in ten two-week sprints. Each module goes to your key users as soon as it is built |
  | by 4 · by 16 | Test server on your infrastructure · production server ready |
  | 21–22 | System test and final acceptance test |
  | 23 | Training and the migration rehearsal |
  | **24** | **Go-live** |
  | 25–26 | Close support (hypercare) |
  | 27–32 | The remaining standard and full features (the v1.1 completion), unless built earlier |

- **How the price is split:** by the share of the estimated work in each feature, sprint, phase and module. Every figure below comes from the same 4,027 hours of estimated work (3,177 hours of building and 850 hours of go-live work), at **ETB 245 per hour of estimated work**.
- **You pay as work is delivered** (§8): 10% at signature, then the value of what is accepted at each review.

### Words used here

| Word | Meaning |
|---|---|
| **Estimated work** | The hours a feature should take, at the likely estimate. The price follows it, so a feature's price shows its size |
| **Core / standard / full** | Depth of a feature. Core is the flow with its key controls, delivered by go-live; standard and full are year-one conveniences and extras, delivered in the release if time allows, otherwise in the v1.1 completion (weeks 27–32). All three are in the price |
| **Sprint** | A two-week step of work, ending with a review where you see what was built |
| **UAT** | User acceptance testing: your key users test the system against agreed scripts |
| **Hypercare** | Close daily support right after go-live |

---

## 2. Price by phase

| Phase | When | What it delivers | Est. hours | ETB | Share |
|---|---|---|---|---|---|
| **0 Mobilisation** | before W1 | Contract, team set-up, the "before W1" answers | — | — | — |
| **1 Foundation** | S1–S5 · W1–W10 | The platform: users and roles, master data, approvals, numbering, documents, the stock ledger | 854 | **209,230** | 21.2% |
| **2 Procure-to-pay** | S4–S8 · W7–W16 | Procurement, payables, inventory and lab/quality: the client's procure-to-pay end to end | 879 | **215,355** | 21.8% |
| **3 Plant operations** | S7–S10 · W13–W20 | Gate and weighbridge, production, sales and receivables | 598 | **146,510** | 14.8% |
| **4 Hardening and freeze** | S10 · W20 | Security review, performance, restore drill, acceptance scripts, guides | 108 | **26,460** | 2.7% |
| **5 Go-live** | W3–W26 (mostly S11–S13) | Test and production servers, rolling and final UAT, system test, migration, training, cutover (W24), hypercare | 850 | **208,250** | 21.1% |
| **6 v1.1 completion** | W27–W32, or earlier if pulled in | The standard and full features of the given requirements not pulled into the release | 738 | **180,810** | 18.3% |
| | | **Total** | **4,027** | **986,615** | 100% |

The phases overlap in time because three developers work in parallel; the price of each phase is the work in it, wherever it falls.

---

## 3. Price by module

| Module | What it covers | Core (by go-live) | Standard + full (v1.1 completion) | Total est. hours | ETB | Share |
|---|---|---|---|---|---|---|
| **Foundation** | Users, roles, sign-in; company, stores and silos, items, partners, taxes; approvals and segregation of duties; numbering, periods, audit, documents, PDFs; the stock ledger | 854 h · ETB 209,230 | 166 h · ETB 40,670 | 1,020 | **249,900** | 25.3% |
| **Procurement** | Suppliers with protected bank details, requisitions, purchase orders, the supplier delivery record, goods receipt, returns | 279 h · ETB 68,355 | 80 h · ETB 19,600 | 359 | **87,955** | 8.9% |
| **Payables** | Invoices and matching, debit notes, payment requests drafted from the receipt, approval, payment with cheques and withholding, supplier ledger, journal export | 256 h · ETB 62,720 | 60 h · ETB 14,700 | 316 | **77,420** | 7.8% |
| **Inventory** | Stock documents, transfers, adjustments, bin and stock cards, counts, period close, silo levels | 168 h · ETB 41,160 | 146 h · ETB 35,770 | 314 | **76,930** | 7.8% |
| **Lab/QC** | Specifications, sampling, results, grading and score, decisions, quality deductions | 176 h · ETB 43,120 | 48 h · ETB 11,760 | 224 | **54,880** | 5.6% |
| **Gate** | Arrivals, weigh tickets with blind verification, exit clearance, gate passes | 222 h · ETB 54,390 | 94 h · ETB 23,030 | 316 | **77,420** | 7.8% |
| **Production** | Recipes, work orders, milling runs, issues, output and packing, finished-flour release, cost settlement, traceability | 178 h · ETB 43,610 | 62 h · ETB 15,190 | 240 | **58,800** | 6.0% |
| **Sales** | Customers and credit, price lists, orders with stock check, delivery through the gate, invoices, cash sales, receipts | 198 h · ETB 48,510 | 46 h · ETB 11,270 | 244 | **59,780** | 6.1% |
| **Hardening** | Security review, performance test, restore drill, acceptance scripts, user and administration guides | 108 h · ETB 26,460 | 36 h · ETB 8,820 | 144 | **35,280** | 3.6% |
| **Go-live services** | Servers, testing and UAT, migration, training, cutover, hypercare (§6) | 850 h · ETB 208,250 | — | 850 | **208,250** | 21.1% |
| | **Total** | 3,289 h · ETB 805,805 | 738 h · ETB 180,810 | **4,027** | **986,615** | 100% |

**Why the foundation is the largest line:** it is the platform every module stands on (sign-in, approvals, numbering, audit, documents, master data, the stock ledger). It is built once and serves all six modules.

---

## 4. Price by sprint

What is built in each sprint, and its value. Build sprints follow the recommended three-developer plan; a feature that spans two sprints is split between them by its weeks.

| Sprint | Weeks | What is built or done | Review | ETB | Cumulative |
|---|---|---|---|---|---|
| **S1** | W1–W2 | repository and CI, platform core, web shell, sign-in, users, roles | sprint review | 50,873 | 50,873 |
| **S2** | W3–W4 | platform core, web shell, sign-in, users, roles, approvals, company set-up and master data; test server set-up | M1; test server live | 77,063 | 127,936 |
| **S3** | W5–W6 | approvals, company set-up and master data, stock ledger | sprint review | 48,032 | 175,968 |
| **S4** | W7–W8 | company set-up and master data, stock ledger, inventory, procurement; rolling UAT | M2 | 70,826 | 246,794 |
| **S5** | W9–W10 | stock ledger, inventory, procurement, payables; rolling UAT | sprint review | 58,312 | 305,106 |
| **S6** | W11–W12 | inventory, procurement, payables, lab and quality; rolling UAT | M3 | 77,780 | 382,886 |
| **S7** | W13–W14 | procurement, payables, lab and quality, gate and weighbridge, production; rolling UAT | sprint review | 75,789 | 458,676 |
| **S8** | W15–W16 | payables, gate and weighbridge, production, sales; production server set-up; rolling UAT | M4: procure-to-pay end to end; production server ready | 93,460 | 552,136 |
| **S9** | W17–W18 | gate and weighbridge, production, sales; migration mapping; rolling UAT | sprint review | 56,509 | 608,645 |
| **S10** | W19–W20 | sales; hardening; migration dry run; rolling UAT | M5; feature freeze | 50,650 | 659,295 |
| **S11** | W21–W22 | System test of every scenario; final UAT; role guides | UAT sign-off | 73,132 | 732,428 |
| **S12** | W23–W24 | Training and migration rehearsal (W23); cutover and go-live (W24) | Go-live (W24) | 51,082 | 783,510 |
| **S13** | W25–W26 | Hypercare: daily support and fixes; handover to maintenance | End of hypercare | 22,295 | 805,805 |
| **v1.1** | W27–W32 | The standard and full features not pulled into the release (§7) | v1.1 acceptance | 180,810 | 986,615 |
| | | | **Total** | **986,615** | |

### 4.1 Sprint by module (ETB)

| Sprint | Foundation | Procurement | Payables | Inventory | Lab/QC | Gate | Production | Sales | Hardening | Go-live services | Total |
|---|---|---|---|---|---|---|---|---|---|---|---|
| S1 | 50,873 | · | · | · | · | · | · | · | · | · | **50,873** |
| S2 | 71,183 | · | · | · | · | · | · | · | · | 5,880 | **77,063** |
| S3 | 48,032 | · | · | · | · | · | · | · | · | · | **48,032** |
| S4 | 39,040 | 11,606 | · | 16,540 | · | · | · | · | · | 3,640 | **70,826** |
| S5 | 102 | 27,126 | 3,950 | 23,494 | · | · | · | · | · | 3,640 | **58,312** |
| S6 | · | 27,126 | 23,520 | 1,126 | 22,367 | · | · | · | · | 3,640 | **77,780** |
| S7 | · | 2,496 | 23,520 | · | 20,753 | 22,640 | 2,741 | · | · | 3,640 | **75,789** |
| S8 | · | · | 11,730 | · | · | 27,126 | 23,494 | 11,790 | · | 19,320 | **93,460** |
| S9 | · | · | · | · | · | 4,624 | 17,375 | 23,520 | · | 10,990 | **56,509** |
| S10 | · | · | · | · | · | · | · | 13,200 | 26,460 | 10,990 | **50,650** |
| S11 | · | · | · | · | · | · | · | · | · | 73,132 | **73,132** |
| S12 | · | · | · | · | · | · | · | · | · | 51,082 | **51,082** |
| S13 | · | · | · | · | · | · | · | · | · | 22,295 | **22,295** |
| v1.1 | 40,670 | 19,600 | 14,700 | 35,770 | 11,760 | 23,030 | 15,190 | 11,270 | 8,820 | · | **180,810** |
| **Total** | **249,900** | **87,955** | **77,420** | **76,930** | **54,880** | **77,420** | **58,800** | **59,780** | **35,280** | **208,250** | **986,615** |

---

## 5. Price by feature, module by module

Every feature of the given requirements, with its depth, when it is built (sprint and weeks in the recommended plan) and its price. Standard and full features show **v1.1**: they are delivered in the release if the team runs ahead, otherwise in weeks 27–32. Features marked *(new)* come from your documents of 1–2 October.

### 5.1 Foundation: ETB 249,900

Users, roles, sign-in; company, stores and silos, items, partners, taxes; approvals and segregation of duties; numbering, periods, audit, documents, PDFs; the stock ledger.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f00a` | Monorepo layout, workspaces, shared packages | Core | S1 (W1) | 12 | 2,940 |
| `f00b` | Lint, formatting, dependency-cruiser tier rules | Core | S1 (W1) | 8 | 1,960 |
| `f00c` | CI: types, unit and integration tests on PostgreSQL as mos_app, migration-safety check, build | Core | S1 (W1) | 20 | 4,900 |
| `f01a` | Unit of work: transactions, serialisation retry, request context | Core | S1 (W1) | 16 | 3,920 |
| `f04a` | App shell, navigation, sign-in pages, message catalogues, one date formatter | Core | S1 (W1) | 20 | 4,900 |
| `f00d` | Development environment, env contract templates, seed scripts | Core | S1 (W1) | 10 | 2,450 |
| `f00e` | Test harness: factories, fixtures, database reset | Core | S1 (W1–W2) | 14 | 3,430 |
| `f01b` | Idempotency keys | Core | S1 (W1–W2) | 10 | 2,450 |
| `f04b` | Shared components: data grid, line-items form, document page, scan input, lookups | Core | S1 (W1–W2) | 48 | 11,760 |
| `f01c` | Outbox, relay, processed events, process-exception records | Core | S1 (W2) | 28 | 6,860 |
| `f01d` | Audit log of every create, submit, approve, post, reverse, override and sign-in | Core | S1 (W2) | 10 | 2,450 |
| `f02a` | Local accounts: password policy, hashing, lockout, admin reset, forced change | Core | S1–S2 (W2–W3) | 24 | 5,880 |
| `f01f` | Error model, env validation, health, structured logs | Core | S1–S2 (W2–W3) | 12 | 2,940 |
| `f04c` | Approvals inbox, usable on a phone | Core | S1–S2 (W2–W3) | 16 | 3,920 |
| `f01g` | Table registry, generated grants, posted-row guard trigger | Core | S2 (W3) | 10 | 2,450 |
| `f02d` | Sessions, CSRF, idle and absolute expiry, sign-out | Core | S2 (W3) | 12 | 2,940 |
| `f04e` | Attachment service: content check, size limit, fingerprint, local-disk or S3 storage | Core | S2 (W3) | 12 | 2,940 |
| `f01i` | Document engine: lifecycles, named actions, status map, lock ranks, posting and reversal base | Core | S2 (W3–W4) | 32 | 7,840 |
| `f02e` | Users, invitations, role and permission editor, site-scoped access in every query | Core | S2 (W3–W4) | 32 | 7,840 |
| `f04g` | PDF template engine with a branded base template and QR code | Core | S2 (W3) | 20 | 4,900 |
| `f04h` | In-app notifications | Core | S2 (W3–W4) | 10 | 2,450 |
| `f01j` | Job queue port and scheduler | Core | S2 (W4) | 6 | 1,470 |
| `f02f` | Operation-unit selection after sign-in | Core | S2 (W4) | 6 | 1,470 |
| `f07a` | Approval matrix: amount bands, site, category, flags; admin screen | Core | S2 (W4) | 28 | 6,860 |
| `f04j` | List export to XLSX/CSV within scopes and filters | Core | S2 (W4) | 8 | 1,960 |
| `f03a` | Setup wizard, company profile, sites, departments, site time zones | Core | S2 (W4) | 16 | 3,920 |
| `f04l` | Process-exception work queue | Core | S2 (W4) | 8 | 1,960 |
| `f03b` | Warehouses and storage units: types, permissions, allowed categories and statuses, keepers, stock levels | Core | S2 (W4) | 22 | 5,390 |
| `f04m` | Build fingerprint | Core | S2 (W4) | 4 | 980 |
| `f04o` | Typed attachment slots per document, each with a "Verify" action *(new)* | Core | S2 (W4) | 6 | 1,470 |
| `f07b` | Approval chains: submit, approve, reject, request changes, recall; sequential stages, one-of-N | Core | S2–S3 (W4–W5) | 28 | 6,860 |
| `f03d` | Units of measure and versioned item conversions | Core | S2–S3 (W4–W5) | 14 | 3,430 |
| `f03e` | Items, categories, item stock levels | Core | S3 (W5) | 20 | 4,900 |
| `f07e` | SoD rules, actor tracking, exclusion at assignment and posting, eligible-approver check | Core | S3 (W5) | 24 | 5,880 |
| `f03g` | Business partners (organisations and individuals), addresses, contacts, categories, TIN rules | Core | S3 (W5) | 18 | 4,410 |
| `f07f` | Approval history (immutable) | Core | S3 (W5) | 6 | 1,470 |
| `f03i` | Bank master; company paying accounts (bank, cash, mobile money) | Core | S3 (W5–W6) | 10 | 2,450 |
| `f03j` | Tax codes and groups, withholding profiles, payment terms, currencies and rates, reason codes | Core | S3 (W6) | 24 | 5,880 |
| `f05a` | Stock ledger, buckets and balances with sorted locking | Core | S3 (W6) | 28 | 6,860 |
| `f03k` | Numbering: series, patterns, gap-free counters with a concurrency test | Core | S3 (W6) | 20 | 4,900 |
| `f05b` | Moving-average valuation at company level | Core | S3–S4 (W6–W7) | 20 | 4,900 |
| `f03l` | Fiscal years, periods, per-module period status, close and reopen | Core | S3–S4 (W6–W7) | 16 | 3,920 |
| `f03m` | Module switches, deactivate-only masters, backdating windows, date checks | Core | S4 (W7) | 12 | 2,940 |
| `f05c` | Capacity check and commingled-silo FIFO | Core | S4 (W7) | 20 | 4,900 |
| `f03n` | Import framework: CSV/XLSX templates, dry-run report, all-or-nothing commit | Core | S4 (W7) | 20 | 4,900 |
| `f05d` | Inventory posting and reversal framework, negative-stock refusal, pending-issue hook | Core | S4 (W7–W8) | 24 | 5,880 |
| `f03p` | Grain/flour industry pack: items, categories, reason codes, templates | Core | S4 (W7) | 12 | 2,940 |
| `f03q` | Storage-master fields from the client dictionary (type, description, flags, dedicated item, temperature, minimum level); mixed items/lots rule at posting *(new)* | Core | S4 (W7–W8) | 8 | 1,960 |
| `f03r` | Client fields the first review missed: remarks on documents, storage statuses, bin type, contact status, invoice type, supplier item code, count scope, issue received-by *(new)* | Core | S4 (W8) | 6 | 1,470 |
| `f05e` | Property tests: invariants over at least 10,000 generated sequences | Core | S4 (W8) | 32 | 7,840 |
| `f05f` | Lots and genealogy links | Core | S4–S5 (W8–W9) | 12 | 2,940 |
| `f01e` | Audit hash chain with daily sealing and verification | Standard | v1.1 | 10 | 2,450 |
| `f01h` | Privilege boot probe; grant and trigger catalogue test | Standard | v1.1 | 10 | 2,450 |
| `f02b` | TOTP second factor, enforceable per role | Standard | v1.1 | 14 | 3,430 |
| `f02c` | Optional OpenID Connect sign-in with invitation matching | Full | v1.1 | 20 | 4,900 |
| `f02g` | Access-review export; sign-in event log | Standard | v1.1 | 8 | 1,960 |
| `f02h` | Password reset by email link | Full | v1.1 | 6 | 1,470 |
| `f03c` | Silo geometry tables and bulk density | Standard | v1.1 | 6 | 1,470 |
| `f03f` | Category attribute schemas for lots | Standard | v1.1 | 8 | 1,960 |
| `f03h` | Fuzzy duplicate-name warning | Full | v1.1 | 6 | 1,470 |
| `f03o` | Import templates for every master and opening balance | Standard | v1.1 | 12 | 2,940 |
| `f04d` | Inbox KPI tiles and bulk approve/reject | Standard | v1.1 | 8 | 1,960 |
| `f04f` | Virus-scan adapter for an optional scanner | Full | v1.1 | 6 | 1,470 |
| `f04i` | Email notifications through SMTP, with per-user opt-out | Standard | v1.1 | 8 | 1,960 |
| `f04k` | Global search | Full | v1.1 | 14 | 3,430 |
| `f04n` | API documentation (OpenAPI) generated from the contracts *(new)* | Standard | v1.1 | 8 | 1,960 |
| `f07c` | Delegation | Standard | v1.1 | 10 | 2,450 |
| `f07d` | Reminders and fallback escalation | Standard | v1.1 | 10 | 2,450 |
| `f07g` | Attestation text with an "I agree" box for the submitter and the approver *(new)* | Standard | v1.1 | 2 | 490 |
| | **Foundation total** (core 854 h · standard and full 166 h) | | | **1,020** | **249,900** |

### 5.2 Procurement: ETB 87,955

Suppliers with protected bank details, requisitions, purchase orders, the supplier delivery record, goods receipt, returns.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f08a` | Supplier 5-step wizard with drafts; onboarding document checklist | Core | S4 (W8) | 24 | 5,880 |
| `f08c` | Supplier lifecycle actions and order/receipt/payment eligibility | Core | S4 (W8) | 12 | 2,940 |
| `f08e` | Supplier bank accounts (encrypted, masked, audited reveal) and bank-change control | Core | S4–S5 (W8–W9) | 28 | 6,860 |
| `f08g` | Purchase requisition with live available stock, quantity to purchase, approval, conversion tracking | Core | S5 (W9) | 32 | 7,840 |
| `f08i` | Purchase order: terms, lines, discounts, taxes, schedules, tolerances, approval, issue by PDF/email | Core | S5 (W9–W10) | 44 | 10,780 |
| `f08k` | PO closure (auto, short-close, cancel) and stale-PO report | Core | S5 (W10) | 10 | 2,450 |
| `f08l` | GRN with the gate off: pre-fill, accepted/rejected with disposition, batches, weights, bags, tolerances, valuation | Core | S5–S6 (W10–W11) | 56 | 13,720 |
| `f08n` | Supplier returns and replacement | Core | S6 (W11–W12) | 20 | 4,900 |
| `f08o` | Procurement reports: open PRs and POs, ageing, status, outstanding deliveries | Core | S6 (W12) | 10 | 2,450 |
| `f08q` | Procurement dashboard | Core | S6 (W12) | 8 | 1,960 |
| `f08r` | PDF templates: PO, GRN, supplier receipt with deductions, rejection note | Core | S6 (W12) | 12 | 2,940 |
| `f08s` | Supplier delivery (arrival) record with the gate module off: PO lines, delivery note, plate, driver, declared quantities; hand-off to inspection and the GRN *(new)* | Core | S6–S7 (W12–W13) | 20 | 4,900 |
| `f08t` | Small fields: supplier default bank account; PR purpose list *(new)* | Core | S7 (W13) | 3 | 735 |
| `f08b` | Supplier document expiry tracking | Standard | v1.1 | 8 | 1,960 |
| `f08d` | Approved supplier list with enforcement | Standard | v1.1 | 8 | 1,960 |
| `f08f` | Document-expiry notification job | Standard | v1.1 | 6 | 1,470 |
| `f08h` | Sourcing policy, quotations on the PO, sole-source justification | Standard | v1.1 | 14 | 3,430 |
| `f08j` | PO amendments as re-approved revisions | Standard | v1.1 | 20 | 4,900 |
| `f08m` | Service and non-stock receipt confirmation | Standard | v1.1 | 10 | 2,450 |
| `f08p` | Price history and spend reports | Standard | v1.1 | 10 | 2,450 |
| `f08u` | Budget code on PR and PO (recorded and reported; no budget control) *(new)* | Standard | v1.1 | 4 | 980 |
| | **Procurement total** (core 279 h · standard and full 80 h) | | | **359** | **87,955** |

### 5.3 Payables: ETB 77,420

Invoices and matching, debit notes, payment requests drafted from the receipt, approval, payment with cheques and withholding, supplier ledger, journal export.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f11a` | Supplier invoice capture (list and inline on the PV), duplicate detection, due dates | Core | S5–S6 (W10–W11) | 20 | 4,900 |
| `f11b` | Matching: three-way/two-way on payable quantity and adjusted price, tolerances, PPV, holds | Core | S6 (W11) | 40 | 9,800 |
| `f11c` | Match-exception resolution through the approval matrix | Core | S6 (W11–W12) | 12 | 2,940 |
| `f11d` | Debit notes (quality and return) and their allocation | Core | S6 (W12) | 10 | 2,450 |
| `f11g` | PV drafted from the GRN; lines, gross/VAT/WHT/net, reservation, approval, lifecycle | Core | S6–S7 (W12–W13) | 40 | 9,800 |
| `f11h` | Payment recording: paying account, cheque, bank reference, proof, allocation, bank-detail re-check | Core | S7 (W13) | 28 | 6,860 |
| `f11i` | Cheque register | Core | S7 (W13–W14) | 12 | 2,940 |
| `f11k` | Withholding computation and certificates | Core | S7 (W14) | 14 | 3,430 |
| `f11l` | Partner ledger: open items, entries, allocations, balances, statements, ageing | Core | S7 (W14) | 28 | 6,860 |
| `f11m` | Journals: posting rules, account map, CSV export batches | Core | S7–S8 (W14–W15) | 32 | 7,840 |
| `f11p` | Payables dashboard | Core | S8 (W15) | 8 | 1,960 |
| `f11q` | PDF templates: self-billed voucher, PV, payment voucher, debit note, WHT certificate | Core | S8 (W15) | 12 | 2,940 |
| `f11e` | Automatic quality debit note | Standard | v1.1 | 6 | 1,470 |
| `f11f` | Self-billed vouchers and their rules | Standard | v1.1 | 16 | 3,920 |
| `f11j` | Cheque books with gap detection; cleared and stale cheques | Standard | v1.1 | 8 | 1,960 |
| `f11n` | Tax registers (input VAT, withholding) | Standard | v1.1 | 10 | 2,450 |
| `f11o` | GRNI reconciliation report | Standard | v1.1 | 12 | 2,940 |
| `f11r` | Optional approval of every supplier invoice (a setting; default: only match exceptions) *(new)* | Standard | v1.1 | 8 | 1,960 |
| | **Payables total** (core 256 h · standard and full 60 h) | | | **316** | **77,420** |

### 5.4 Inventory: ETB 76,930

Stock documents, transfers, adjustments, bin and stock cards, counts, period close, silo levels.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f06a` | Miscellaneous receipts and issues (consumption, scrap) at moving average | Core | S4 (W7–W8) | 20 | 4,900 |
| `f06b` | Intra-site transfers | Core | S4 (W8) | 12 | 2,940 |
| `f06d` | Stock adjustments routed by value through the approval matrix | Core | S4 (W8) | 16 | 3,920 |
| `f06e` | Status changes: block, damage, recovery | Core | S4 (W8) | 16 | 3,920 |
| `f06f` | Lot attributes and backward/forward trace | Core | S4–S5 (W8–W9) | 12 | 2,940 |
| `f06h` | Stock balance with status breakdown and transaction history | Core | S5 (W9) | 24 | 5,880 |
| `f06j` | Silo levels and level alerts | Core | S5 (W9) | 10 | 2,450 |
| `f06m` | Inventory dashboard | Core | S5 (W9–W10) | 8 | 1,960 |
| `f06n` | Bin card and stock card from the one ledger, with the opening balance for the period *(new)* | Core | S5 (W10) | 10 | 2,450 |
| `f12a` | Counts: count, approval, generated adjustment | Core | S5 (W10) | 20 | 4,900 |
| `f12e` | Inventory period close with checklist and value snapshot | Core | S5–S6 (W10–W11) | 20 | 4,900 |
| `f06c` | Inter-site transfers through in-transit, short receipts, transit loss | Standard | v1.1 | 20 | 4,900 |
| `f06g` | Lot split (relabel) with genealogy | Full | v1.1 | 8 | 1,960 |
| `f06i` | Movement ledger, valuation, expiry, adjustments register, in-transit ageing | Standard | v1.1 | 12 | 2,940 |
| `f06k` | Reorder report that creates PRs | Standard | v1.1 | 10 | 2,450 |
| `f06l` | PDF labels with barcode/QR; scanner input | Standard | v1.1 | 12 | 2,940 |
| `f12b` | Count modes (freeze or cut-off), blind counting, forced recount | Standard | v1.1 | 16 | 3,920 |
| `f12c` | Silo dip measurement | Standard | v1.1 | 14 | 3,430 |
| `f12d` | Fumigation and treatment holds | Standard | v1.1 | 12 | 2,940 |
| `f12f` | Exception reports (the detective controls) | Standard | v1.1 | 24 | 5,880 |
| `f12g` | Background report jobs | Full | v1.1 | 12 | 2,940 |
| `f12h` | Count import from Excel; printable count sheet *(new)* | Standard | v1.1 | 6 | 1,470 |
| | **Inventory total** (core 168 h · standard and full 146 h) | | | **314** | **76,930** |

### 5.5 Lab/QC: ETB 54,880

Specifications, sampling, results, grading and score, decisions, quality deductions.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f09a` | Test methods, parameters, versioned and approved specs, QC policies, fixed-count sampling | Core | S6 (W11) | 24 | 5,880 |
| `f09c` | Inspection lots (from the gate, from quarantine, gate off with weights), inspection record, samples and labels | Core | S6 (W11–W12) | 28 | 6,860 |
| `f09e` | Result entry grid, insert-only results, invalidation | Core | S6 (W12) | 28 | 6,860 |
| `f09f` | Evaluation, grading schemes, quality score | Core | S6–S7 (W12–W13) | 28 | 6,860 |
| `f09h` | Usage decisions, their stock effects, decision changes | Core | S7 (W13) | 24 | 5,880 |
| `f09i` | Quality price and weight rules; deduction results | Core | S7 (W13–W14) | 24 | 5,880 |
| `f09k` | Lab dashboard | Core | S7 (W14) | 8 | 1,960 |
| `f09m` | Flour-pack QC seed: wheat parameters, specs, grades, deduction rules | Core | S7 (W14) | 12 | 2,940 |
| `f09b` | Percentage and composite sampling plans | Standard | v1.1 | 8 | 1,960 |
| `f09g` | Out-of-spec retest and concessions | Standard | v1.1 | 20 | 4,900 |
| `f09j` | Certificate-of-analysis gate; retention samples | Full | v1.1 | 10 | 2,450 |
| `f09l` | QC reports | Standard | v1.1 | 6 | 1,470 |
| `f09n` | "Checked by" second confirmation on the usage decision *(new)* | Standard | v1.1 | 4 | 980 |
| | **Lab/QC total** (core 176 h · standard and full 48 h) | | | **224** | **54,880** |

### 5.6 Gate: ETB 77,420

Arrivals, weigh tickets with blind verification, exit clearance, gate passes.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f10a` | Gate masters: posts, weighbridges, vehicles, tares, drivers, tolerance rules, checklists | Core | S7 (W13) | 18 | 4,410 |
| `f10c` | Guard shifts | Core | S7 (W13) | 6 | 1,470 |
| `f10e` | Expected arrivals | Core | S7 (W13) | 10 | 2,450 |
| `f10f` | Inbound visits: arrival by plate, entry, one open visit per vehicle, documents, seals, checklist, events | Core | S7 (W13–W14) | 36 | 8,820 |
| `f10g` | Weigh tickets: gap-free per weighbridge, direction, blind verification, tolerances, stored tare, re-weighing | Core | S7–S8 (W14–W15) | 40 | 9,800 |
| `f10h` | Inspection hand-off and unloading pre-flight checks | Core | S8 (W15) | 16 | 3,920 |
| `f10i` | GRN prepared at tare; receipt exceptions | Core | S8 (W15–W16) | 28 | 6,860 |
| `f10j` | Exit clearance, overrides, exit passes | Core | S8 (W16) | 16 | 3,920 |
| `f10o` | Outbound visit class (dispatch, returns, transfers) | Core | S8 (W16) | 16 | 3,920 |
| `f10p` | Inbound failure-case suite and idempotency replay tests | Core | S8–S9 (W16–W17) | 20 | 4,900 |
| `f10s` | Gate reports and dashboard | Core | S9 (W17) | 10 | 2,450 |
| `f10t` | PDF templates: weigh ticket, gate pass, exit pass | Core | S9 (W17) | 6 | 1,470 |
| `f10b` | Blacklist with approval | Standard | v1.1 | 6 | 1,470 |
| `f10k` | Material gate passes | Standard | v1.1 | 20 | 4,900 |
| `f10l` | Incidents and weight disputes | Full | v1.1 | 10 | 2,450 |
| `f10m` | Yard board | Standard | v1.1 | 8 | 1,960 |
| `f10n` | Paper mode and back-entry workbench | Standard | v1.1 | 28 | 6,860 |
| `f10q` | Remaining failure cases | Standard | v1.1 | 12 | 2,940 |
| `f10r` | Supplier notifications | Full | v1.1 | 10 | 2,450 |
| | **Gate total** (core 222 h · standard and full 94 h) | | | **316** | **77,420** |

### 5.7 Production: ETB 58,800

Recipes, work orders, milling runs, issues, output and packing, finished-flour release, cost settlement, traceability.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f14a` | Work centres and shift calendar | Core | S7 (W14) | 10 | 2,450 |
| `f14b` | Recipes with co- and by-products and a mass-balance band, versioned and approved | Core | S7–S8 (W14–W15) | 24 | 5,880 |
| `f14c` | Work orders (standard and packing) and release | Core | S8 (W15) | 20 | 4,900 |
| `f14e` | Milling run: grist from silos, extraction rate | Core | S8 (W15–W16) | 20 | 4,900 |
| `f14g` | Material issue: silo draw-down, scan issue, backflush, pending issues; supervisor screens | Core | S8 (W16) | 28 | 6,860 |
| `f14h` | Output by lot with co- and by-products; packing into bag SKUs | Core | S8–S9 (W16–W17) | 20 | 4,900 |
| `f14i` | Finished-goods QC and release | Core | S9 (W17) | 8 | 1,960 |
| `f14j` | Material-cost settlement | Core | S9 (W17–W18) | 32 | 7,840 |
| `f14l` | Traceability backward and forward | Core | S9 (W18) | 8 | 1,960 |
| `f14o` | Production reports (extraction rate, yield) and dashboard | Core | S9 (W18) | 8 | 1,960 |
| `f14d` | Campaign work orders | Standard | v1.1 | 8 | 1,960 |
| `f14f` | Tempering and fortification dosing | Standard | v1.1 | 12 | 2,940 |
| `f14k` | Shift logs and downtime | Standard | v1.1 | 14 | 3,430 |
| `f14m` | Recall report | Standard | v1.1 | 6 | 1,470 |
| `f14n` | Planner's demand and material view | Full | v1.1 | 14 | 3,430 |
| `f14p` | Further production reports | Standard | v1.1 | 8 | 1,960 |
| | **Production total** (core 178 h · standard and full 62 h) | | | **240** | **58,800** |

### 5.8 Sales: ETB 59,780

Customers and credit, price lists, orders with stock check, delivery through the gate, invoices, cash sales, receipts.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f15a` | Customers with approval, credit limits and changes, ship-to addresses | Core | S8 (W15–W16) | 24 | 5,880 |
| `f15b` | Price lists with approval, floor prices, discount limits | Core | S8 (W16) | 12 | 2,940 |
| `f15d` | Sales orders with credit, price and discount holds | Core | S8–S9 (W16–W17) | 28 | 6,860 |
| `f15e` | Availability check and soft reservation | Core | S9 (W17) | 8 | 1,960 |
| `f15f` | Delivery orders through the outbound gate: pick, load, weigh, goods issue, exit | Core | S9 (W17–W18) | 36 | 8,820 |
| `f15g` | Goods issue posting at average cost | Core | S9 (W18) | 12 | 2,940 |
| `f15h` | Customer invoicing (gap-free, fiscal reference) | Core | S9 (W18) | 24 | 5,880 |
| `f15i` | Cash sales and cash sessions | Core | S9–S10 (W18–W19) | 20 | 4,900 |
| `f15j` | Customer receipts with allocation | Core | S10 (W19) | 16 | 3,920 |
| `f15m` | Statements, ageing, sales reports and dashboard | Core | S10 (W19) | 10 | 2,450 |
| `f15p` | PDF templates: delivery note, customer invoice, credit note, receipt | Core | S10 (W19–W20) | 8 | 1,960 |
| `f15k` | Customer advances and cash-before-delivery | Standard | v1.1 | 8 | 1,960 |
| `f15l` | Customer returns and credit notes | Standard | v1.1 | 28 | 6,860 |
| `f15n` | Output VAT register; further sales reports | Standard | v1.1 | 10 | 2,450 |
| | **Sales total** (core 198 h · standard and full 46 h) | | | **244** | **59,780** |

### 5.9 Hardening: ETB 35,280

Security review, performance test, restore drill, acceptance scripts, user and administration guides.

| Key | Feature | Depth | When | Est. h | ETB |
|---|---|---|---|---|---|
| `f13a` | Security review (ASVS L2, local-account attacks) and fixes | Core | S10 (W20) | 40 | 9,800 |
| `f13b` | Performance test at the NFR targets | Core | S10 (W20) | 16 | 3,920 |
| `f13d` | Backup and restore drill on development | Core | S10 (W20) | 8 | 1,960 |
| `f13e` | Acceptance scripts and user-guide drafts (developer share) | Core | S10 (W20) | 20 | 4,900 |
| `f13g` | Administration guide and runbook drafts | Core | S10 (W20) | 24 | 5,880 |
| `f13c` | Extended performance test and tuning | Standard | v1.1 | 16 | 3,920 |
| `f13f` | Further acceptance scripts and guides | Standard | v1.1 | 20 | 4,900 |
| | **Hardening total** (core 108 h · standard and full 36 h) | | | **144** | **35,280** |

---

## 6. Go-live services

| Weeks | Sprint | Service | Est. hours | ETB |
|---|---|---|---|---|
| W3–W4 | S2 | Test server on your infrastructure: install, deploy pipeline, first release | 24 | 5,880 |
| W8–W20 | S4–S10 | Rolling UAT: test script per module, key-user sessions, triage | 49 | 12,005 |
| W8–W20 | S4–S10 | Rolling UAT: fix what the key users find | 55 | 13,475 |
| W15–W16 | S8 | Production server: set-up, hardening, backups with a tested restore, monitoring, runbook | 64 | 15,680 |
| W17–W20 | S9–S10 | Data migration: mapping, templates, first dry run | 60 | 14,700 |
| W21–W22 | S11 | System test: every acceptance scenario end to end, regression | 98 | 24,010 |
| W21–W22 | S11 | System test: fix defects | 79 | 19,355 |
| W21–W22 | S11 | Final acceptance test: the whole flow with key users; sign-off | 33 | 8,085 |
| W21–W22 | S11 | Final acceptance test: fix defects | 55 | 13,475 |
| W21–W23 | S11–S12 | Training: role guides and role sessions | 67 | 16,415 |
| W23 | S12 | Training: administrator and key-user sessions | 50 | 12,250 |
| W23–W24 | S12 | Data migration: rehearsal and final load | 60 | 14,700 |
| W23–W24 | S12 | Data migration: reconciliation checks | 17 | 4,165 |
| W24 | S12 | Cutover and go-live | 40 | 9,800 |
| W24 | S12 | Cutover checks | 8 | 1,960 |
| W25–W26 | S13 | Hypercare: fixes and support | 77 | 18,865 |
| W25–W26 | S13 | Hypercare: verification | 14 | 3,430 |
| | | **Total** | **850** | **208,250** |

---

## 7. The v1.1 completion (weeks 27–32)

The standard and full features (738 estimated hours, ETB 180,810) are part of the given requirements, so they are in the price. They are built in the release whenever the team runs ahead of plan (each one is pulled in only if it can be finished and tested before the week-20 freeze). Whatever remains is delivered as one follow-up release in **weeks 27–32**, tested by your key users and accepted at the end. The features are listed with their prices in §5 (marked v1.1).

---

## 8. Payment schedule

You pay as work is accepted: an advance at signature, then the value of what was delivered since the previous review, less the share of the advance it recovers.

| When | What is accepted | Value delivered (ETB) | Payment (ETB) |
|---|---|---|---|
| **Signature** | — (advance, recovered across the later payments) | — | **98,662** |
| **M1 review (W4)** | Sign-in, the shell, the platform core; the test server | 127,936 | **115,142** |
| **M2 review (W8)** | Master data, approvals, the stock ledger | 118,858 | **106,972** |
| **M3 review (W12)** | Inventory; suppliers, requisitions, orders, deliveries | 136,092 | **122,483** |
| **M4 review (W16)** | Procure-to-pay end to end; the production server | 169,250 | **152,325** |
| **M5 review and freeze (W20)** | Gate, production, sales; hardening | 107,159 | **96,443** |
| **UAT sign-off (W22)** | System test and final UAT | 73,132 | **65,819** |
| **Go-live (W24)** | Training, migration, cutover | 51,082 | **45,974** |
| **End of hypercare (W26)** | Two weeks of close support | 22,295 | **20,066** |
| **v1.1 acceptance (W32)** | The remaining standard and full features | 180,810 | **162,729** |
| | **Total** | 986,615 | **986,615** |

- Each payment is due 15 days after its review is accepted **[owner to confirm]**.
- If a feature planned for a review is not accepted, its value moves to the next review; the total does not change.
- Invoices are in ETB. The price is fixed in birr, so exchange-rate changes do not move it.

---

## 9. Not in the price

| Item | How it is charged |
|---|---|
| **Maintenance and support after hypercare** | A monthly retainer: Basic 30 h (ETB 24,300 / month), Standard 60 h (ETB 48,600), Premium 100 h (ETB 81,000) **[owner to confirm the maintenance rate under the fixed-price model]** |
| **New features or changes** beyond the given requirements | Estimated in hours, approved by you in writing first, billed at the hourly rate |
| **Hosting and infrastructure** | At cost: servers or virtual servers, backup storage, domain name, TLS certificate, email relay, internet, UPS, PCs, tablets, printers and scanners |
| **Third-party services and licences** | At cost, with your approval first |
| **Travel beyond the team's city** | At cost, with your approval first |
| VAT and withholding tax | Added as the law requires |

---

## 10. How the price was built

1. **The work:** every given requirement was broken into 199 features and each was estimated in hours ([implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) Appendix A): 2,439 hours of core, 738 of standard and full. The go-live services add 850 hours (servers, testing, migration, training, cutover, hypercare).
2. **The price:** fixed at ETB 986,615, within the ETB 1,000,000 cap, and spread over the 4,027 hours of estimated work at ETB 245 each. The same rate is used everywhere, so every line of this document adds up to the total.
3. **The timeline:** the recommended plan with three developers, AI assistance and a QA engineer ([sprint plan](../4-plan/MOS-SPRINT-PLAN.md)). The price does not change with the team we use; the dates and the scope are what we commit to.
4. **Checks:** the phases, modules, sprints, features and payments each add up to ETB 986,615 exactly (checked by script).
