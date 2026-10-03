# Commercial proposal: Factory ERP (MOS) for a flour mill

<!-- nav -->[MOS](../../README.md) › [5. For the client](README.md)

| | |
|---|---|
| Document | Commercial proposal: team options, delivery plan, prices, payment terms |
| Version | **0.4, draft for the owner's review — not yet sent to the client** (0.3: everything in six months; **0.4, 2026-10-03: one fixed price within ETB 1,000,000**) |
| Date | 2026-10-03 |
| Prepared for | [Client name] (the flour mill whose procure-to-pay documents this proposal answers) |
| Prepared by | [Vendor name] |
| Validity | 30 days from the date of issue |
| Currency | **Ethiopian birr, fixed.** US dollar figures are for reference at ETB 162.00 per US$. **All prices exclude VAT and withholding tax** |
| Basis | The planning package: requirements (205 in the first release), process model, database design, implementation plan 0.5, and your documents of 27 September – 2 October |

Items marked **[owner to confirm]** are proposals the owner decides before this document goes to the client. The full breakdown of the price, by phase, sprint, module and feature, is in the [pricing and timeline](MOS-PRICING-AND-TIMELINE.md) document.

---

## 1. Summary

- **What you get:** one integrated system for the plant, in six modules:
  - procurement with supplier payment;
  - inventory, with silos and stores;
  - lab and quality;
  - gate and weighbridge;
  - production (milling and packing);
  - sales with receivables.

  It runs as your own installation on your own server and exports journals to your accounting package.
- **When: everything inside six months.**

  | Weeks | What happens |
  |---|---|
  | 1–20 | We build the system in two-week steps. **Each module goes to your key users for testing as soon as it is built.** |
  | by week 4 | A test server runs on your own infrastructure |
  | by week 16 | The production server is ready |
  | 21–22 | Final system test and your acceptance test |
  | 23 | Training, and a full practice run of the data transfer |
  | **24** | **Go-live** |
  | 25–26 | Close support after go-live, then maintenance |

- **Price: one fixed price of ETB 986,615** (about US$6,090) for every requirement already given, at every depth, and every go-live service. It stays within ETB 1,000,000.
- **What is not in it:** maintenance after go-live (§7), new features beyond the given requirements (§8), and hosting, domains and third-party services, which you buy at cost (§9).
- **You pay as work is accepted** (§10): 10% at signature, then the value of what each review accepts.
- **Your build is yours.** You receive the source code of your installation (§13).

## 2. What we will deliver

**The six modules**, on one platform with one set of users, approvals, numbering, audit and documents:

| Module | Main functions |
|---|---|
| Procurement and supplier payment | Supplier registration with protected bank details; requisitions with live stock; purchase orders with approval; **supplier delivery record**; goods receipt with weights, bags and rejected quantities; supplier returns; invoices, three-way match, debit notes; payment requests drafted from the receipt; approval; payment with cheque register and withholding certificates |
| Inventory | Stock by store, silo, lot and status; issues, transfers, adjustments; **bin card and stock card**; counts; period close; stock balance and valuation |
| Lab and quality | Specifications, sampling, results entry, grading, quality score, decisions, weight and price deductions |
| Gate and weighbridge | Expected arrivals, gate entry, weigh tickets with blind verification, exit clearance, gate passes |
| Production | Recipes with flour and bran, milling runs, packing into bags, issue and output, **finished-flour release**, cost of production, extraction rate |
| Sales and receivables | Customers and credit limits, price lists, orders with **a stock check**, delivery through the gate, invoices, cash sales, receipts |

**Depth.** Every function has three levels:

| Level | Meaning |
|---|---|
| **Core** | The flow works end to end with its key controls: approvals, segregation of duties, numbering without gaps, audit trail, posted documents that cannot be edited |
| **Standard** | Year-one conveniences, e.g. two-factor sign-in for payment roles, delegation, PO amendments, self-billing, blind counts, paper fallback at the gate |
| **Full** | The rest of the first release, e.g. sign-in with an external identity provider, global search, supplier notifications |

The release includes all six modules at **core** depth. Standard and full features come in the same release if the team runs ahead of plan, or in the next release.

**Also included:** a test server from week 4; installation of the production server on your infrastructure; data transfer from your spreadsheets; role-based training; two weeks of close support after go-live; documentation (user guides per role, administration guide, runbook).

---

## 3. The team

We plan the work with **three developers using AI coding assistants, and a QA engineer**: a lead developer (56 hours a week), two developers (48 hours each) and a QA engineer (40 hours). Three streams of work run in parallel, and a dedicated tester checks a system that handles payments.

**The price is fixed whichever team we field.** What we commit to is the scope, the dates and the quality checks; the team is how we meet them. Progress is measured at weeks 4 and 8, and if the team is behind we add people at our cost, without moving the date or the price **[owner to confirm: O-26]**.

## 4. Delivery plan and price

One release: **all six modules at core depth, live in week 24**, with testing, servers, training and close support inside the 26 weeks. The standard and full features of the given requirements come in the same release if the team runs ahead of plan, otherwise in a follow-up release in weeks 27–32, **within the same price**.

| Phase | When | What it delivers | ETB |
|---|---|---|---|
| 1 Foundation | weeks 1–10 | The platform: users and roles, master data, approvals, numbering, documents, the stock ledger | 209,230 |
| 2 Procure-to-pay | weeks 7–16 | Procurement, payables, inventory and lab/quality: your procure-to-pay end to end | 215,355 |
| 3 Plant operations | weeks 13–20 | Gate and weighbridge, production, sales and receivables | 146,510 |
| 4 Hardening | week 20 | Security review, performance, restore drill, acceptance scripts, guides | 26,460 |
| 5 Go-live | weeks 3–26 | Servers, rolling and final acceptance tests, system test, data migration, training, go-live (week 24), close support | 208,250 |
| 6 v1.1 completion | weeks 27–32, or earlier | The standard and full features not built in the release | 180,810 |
| | | **Total, fixed** | **986,615** |

- **Your answers decide how comfortable the plan is.** If the gate module and grading are not needed (Q-01, Q-02 of our 3 October discussion), the work shrinks by about 230 hours; the price would fall by that work's value.

## 5. Price by package

| Package | What it covers | ETB |
|---|---|---|
| **1. Development** | Building all six modules at every depth of the given requirements (3,177 estimated hours), with the developers' own tests, CI and the development environment | **778,365** |
| **2. Testing and UAT** | Rolling acceptance tests as each module is built; the system test and the final acceptance test | **90,405** |
| **3. Servers, migration, training, go-live** | Test server (week 4), production server (week 16), data migration, training, cutover (week 24), two weeks of close support | **117,845** |
| **Total, fixed** | Packages 1–3 | **986,615** |
| 4. Maintenance and support | Monthly, after close support (§7) | not in the price |
| Hosting and infrastructure | Servers, backup storage, domain, TLS, email relay, devices, third-party services | at cost, not in the price |

## 6. Price by module

Each module's share of the price follows its share of the estimated work. The platform foundation (sign-in, roles, approvals, numbering, audit, documents, master data, the stock ledger) is built once and serves every module, so it is its own line.

| Module | ETB |
|---|---|
| Foundation | 249,900 |
| Procurement | 87,955 |
| Payables | 77,420 |
| Inventory | 76,930 |
| Lab/QC | 54,880 |
| Gate | 77,420 |
| Production | 58,800 |
| Sales | 59,780 |
| Hardening | 35,280 |
| Go-live services | 208,250 |
| **Total** | **986,615** |

Every sprint's and every feature's price is in the [pricing and timeline](MOS-PRICING-AND-TIMELINE.md) document.

**Procurement on its own.** If you prefer to start with procurement only, Module 1 (platform + procurement + payables) or your procure-to-pay (Module 1 + stock + inspection) can be built first and go live earlier. We quote it on request; the timeline is in the [Module 1 plan](../4-plan/MOS-PROCUREMENT-MODULE-PLAN.md) §5.

## 7. Maintenance and support (not in the fixed price)

The retainer starts after the close-support weeks, billed monthly for a minimum of 12 months **[owner to confirm]**. Unused hours carry over for one month. **Defect fixes after the close-support weeks are covered by the retainer**; there is no separate warranty period.

| Tier | Hours / month | Covers | Response | ETB / month |
|---|---|---|---|---|
| **Basic** | 30 | Defect fixes, security and dependency patches, backup checks | Next business day for all severities; business hours | 24,300 |
| **Standard** | 60 | Basic + minor enhancements, monthly health check, version upgrades, one report change a month | 4 business hours for Critical and High; next business day otherwise | 48,600 |
| **Premium** | 100 | Standard + 7-day on-call for Critical (weighbridge, gate or payments down), quarterly restore drill, small change requests | 2 hours for Critical, 7 days a week; 4 business hours for High | 81,000 |

- **Beyond the retainer:** work above the retainer hours is billed at the hourly rate (§8), with your approval first.

## 8. Changes, extras and the exchange rate

- **The price is fixed in birr.** Exchange-rate movements do not change it.
- **Change requests:** new or changed requirements beyond those already given are estimated in hours, approved by you in writing, and billed at **ETB 810 per hour** (US$5) **[owner to confirm]**. Scope is only added to the release if work of equal size leaves it, or by agreement.
- **Extra non-development hours:** workshops, training sessions or meetings beyond those planned are billed at the same hourly rate. Planned and included: 3 requirement workshops, the five milestone reviews, the rolling acceptance tests and the training in §2.
- **Travel and expenses:** visits within the city where the team works are included. Travel beyond it is billed at cost with your approval first **[owner to confirm]**.

## 9. Hosting and infrastructure

The system runs on infrastructure you choose. **Because the test server is needed by week 4, we need your infrastructure answers before we start.** The options:
- **on-premises** on your own server (Windows Server or Linux);
- **a virtual server** at a local provider or in a cloud;
- **a managed cloud setup.**

**Infrastructure is priced at cost** once you have answered. You buy it or rent it directly, or we buy it with your approval and re-invoice at cost. Our set-up hours are already in package 3.

**Indicative sizing for one plant**, to confirm with your answers:

| Item | Indicative size |
|---|---|
| Application and database server (or two servers) | 8 virtual CPUs, 32 GB memory, 500 GB SSD |
| Test server (from week 4) | 4 virtual CPUs, 16 GB memory, 200 GB SSD |
| Backup storage, off the server | 1–2 TB, with one copy off site |
| Power | A UPS for the server and the gate PCs |
| Gate and weighbridge | One PC or tablet per gate post; a 4G router as a network fallback; label printer and barcode scanner (optional) |
| Domain name and TLS certificate; email relay | Optional; needed for email notifications and remote access |

## 10. Payment schedule

**Fixed price, paid as work is accepted:** 10% at signature, then at each review the value of what it accepts, less the share of the advance it recovers.

| When | What is accepted | Payment (ETB) |
|---|---|---|
| **Signature** | Advance (recovered across the later payments) | **98,662** |
| **M1 review (W4)** | Sign-in, the shell, the platform core; the test server | **115,142** |
| **M2 review (W8)** | Master data, approvals, the stock ledger | **106,972** |
| **M3 review (W12)** | Inventory; suppliers, requisitions, orders, deliveries | **122,483** |
| **M4 review (W16)** | Procure-to-pay end to end; the production server | **152,325** |
| **M5 review and freeze (W20)** | Gate, production, sales; hardening | **96,443** |
| **UAT sign-off (W22)** | System test and final UAT | **65,819** |
| **Go-live (W24)** | Training, migration, cutover | **45,974** |
| **End of hypercare (W26)** | Two weeks of close support | **20,066** |
| **v1.1 acceptance (W32)** | The remaining standard and full features | **162,729** |
| | **Total** | **986,615** |

- Payment is due 15 days after each review is accepted **[owner to confirm]**.
- If something planned for a review is not accepted, its value moves to the next review; the total does not change.
- The milestone reviews are the acceptance points for development; UAT sign-off, go-live and the end of close support for the go-live services.

## 11. Assumptions

1. **Client decisions and inputs.**
   - You name a project sponsor and one key user per module **before we start**. Key users are available about 4 hours a week during the build (more in the sprint after their module is built) and full-time in weeks 21–23.
   - Answers to the questionnaire arrive by the weeks listed in it. **The infrastructure answers are needed before week 1.** Late answers move the affected modules, not the price.
   - Master data (items, suppliers, customers, storage units, opening balances) is supplied in our templates. Cleaning your data is your responsibility; we help with checks.
2. **Scope.** Requirements as in the requirements document and the process model, with the additions from your documents of 1–2 October. Changes go through §8.
3. **Standards.** Screens follow your procure-to-pay documents' layout, with the corrections we agree. English with the Gregorian calendar in the first release.
4. **Rates.** Tax rates, withholding rules and approval bands are configuration you confirm. No country rule is hard-coded.
5. **Infrastructure.** Infrastructure and third-party services are bought at cost and are not in the price (§9).
6. **Weighbridge.** Weights are entered by hand, with photo and blind-verification controls. A direct weighbridge connection is a later option.

## 12. Exclusions

These are not in the price unless ordered separately:
- a general ledger, chart of accounts and financial statements (journals are exported to your accounting package);
- payroll and HR;
- direct connections to the weighbridge, label printers, barriers or number-plate cameras;
- bank payment files, fiscal devices and e-invoicing. If the law requires a fiscal device for sales invoices, it is added by change request;
- a supplier or customer portal; RFQ comparison; contracts;
- MRP, production scheduling, labour and overhead costing;
- Amharic and the Ethiopian calendar;
- hardware, software licences, hosting and internet.

## 13. Terms to agree

| Topic | Proposal |
|---|---|
| Ownership | **You own your build:** you receive its complete source code, with a perpetual, irrevocable right to use, change and maintain it for your business, yourselves or through anyone you hire. [Vendor] keeps the right to use the same software (MOS) and sell it to other companies. Your data and your company-specific configuration are yours alone, always exportable, and never shared |
| Source code | Delivered to you at go-live and with each maintenance release |
| Confidentiality | Mutual; your business data is never used outside your installation |
| Acceptance | Milestone reviews for development; the UAT script for go-live; the close-support exit criteria for handover |
| Termination | Either side with 30 days' notice; hours worked are paid; delivered work and your data are handed over |

## 14. Next steps

1. Review the plan and the fixed price; ask us anything in the [pricing and timeline](MOS-PRICING-AND-TIMELINE.md) document.
2. Name the sponsor and the key users; choose the start week (week 1).
3. Answer the questions needed before week 1: the four questions from our 3 October discussion (gate, grading, supplier payment requests, invoice approval), the infrastructure for the test server, currency, and the meaning of "operation unit".
4. Sign the agreement. Week 1 starts with the platform; the first checkpoint and the test server come in week 4.

---

## Appendix A. Basis of estimate

- **Bottom-up.** Every one of the 205 first-release requirements was mapped to one of 199 features, each estimated in developer hours with AI assistance (implementation plan 0.5, Appendix A).
  - Core depth: 2,439 hours.
  - Everything in the first release: 3,177 hours.
- **Capacity.** About 80% of each person's hours go to building; the rest goes to meetings, reviews, demos and your questions.
- **Plan.** The six modules at core depth are scheduled into each of the three developers' weeks, respecting which module depends on which, so that they finish by week 20. The week-4 and week-8 checkpoints compare actual speed with that plan.
- **Price.** One fixed price of ETB 986,615, spread over the 4,027 hours of estimated work (3,177 of building, 850 of go-live services) at ETB 245 each, so that every phase, sprint, module and feature has a price that adds up to the total.

## Appendix B. Exchange-rate sources (checked 2026-09-27)

| Source | Rate (ETB per US$) | Date |
|---|---|---|
| Wise, mid-market | 161.20 | 2026-09-27 |
| XE, mid-market | 162.17 | 2026-09-27, 19:09 UTC |
| Commercial Bank of Ethiopia, transactional buying / selling | 160.23 / 163.44 | 2026-09-17 |
| **Planning rate used here** | **162.00** | rounded from the two mid-market rates |

Sources:
- https://wise.com/gb/currency-converter/usd-to-etb-rate/history
- https://www.xe.com/en-us/currencyconverter/convert/?Amount=1&From=USD&To=ETB
- https://ethio.forex/bank/CBET
