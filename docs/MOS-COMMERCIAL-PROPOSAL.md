# Commercial proposal: Factory ERP (MOS) for a flour mill

| | |
|---|---|
| Document | Commercial proposal: team options, delivery plan, prices, payment terms |
| Version | **0.2, draft for the owner's review — not yet sent to the client** (0.2: the owner's Phase 6 decisions: six modules in one release in 26 weeks; ownership; no free warranty) |
| Date | 2026-09-28 |
| Prepared for | [Client name] (the flour mill whose procure-to-pay draft this proposal answers) |
| Prepared by | [Vendor name] |
| Validity | 30 days from the date of issue |
| Currency | Prices in US dollars with the Ethiopian birr equivalent at **ETB 162.00 per US$** (§8). **All prices exclude VAT and withholding tax** |
| Basis | The planning package in this repository: FRD (231 requirements, 205 in the first release), process model, database design, implementation plan v0.4 |

Items marked **[owner to confirm]** are proposals the owner decides before this document goes to the client.

---

## 1. Summary

- **What you get:** one integrated system for the plant, in six modules:
  - procurement with supplier payment;
  - inventory, with silos and stores;
  - lab and quality;
  - gate and weighbridge;
  - production (milling and packing);
  - sales with receivables.

  It runs as your own installation on your own server. It exports journals to your accounting package.
- **When:** all six modules are **built in 26 weeks and live nine weeks later** (week 35, about eight months after the start). This holds whichever team you choose.
- **How we charge:** team hours × **US$5 per hour** (ETB 810). Development includes testing and setting up the development and CI environments. Quality assurance, go-live services and maintenance are priced separately (§5, §7).
- **Your build is yours.** You receive the source code of your installation (§13).

| Team (§3) | Build | Go-live | Total to go-live (packages 1–3) |
|---|---|---|---|
| **A: 2 developers + AI + QA engineer** | W1–W26 | W27–W35 | **$23,200** · ETB 3,758,400 |
| **B: 2 developers + AI** | W1–W26 | W27–W35 | **$18,000** · ETB 2,916,000 |
| **C: 1 developer + heavy AI** | W1–W26 | W27–W35 | **$11,760** · ETB 1,905,120 |

Maintenance follows from US$150 a month (§7). Infrastructure is at cost (§9).

## 2. What we will deliver

**The six modules**, all on one platform with one set of users, approvals, numbering, audit and documents:

| Module | Main functions |
|---|---|
| Procurement and supplier payment | Supplier registration with protected bank details; requisitions with live stock; purchase orders with approval and amendments; goods receipt with weights, bags and rejected quantities; supplier returns; invoices, three-way match, debit notes, self-billing; payment requests drafted from the receipt; approval; payment with cheque register and withholding certificates |
| Inventory | Stock by store, silo, lot and status; issues, transfers, adjustments; counts and silo dips; fumigation holds; period close; stock balance and valuation |
| Lab and quality | Specifications, sampling, results entry, grading, quality score, decisions, weight and price deductions |
| Gate and weighbridge | Expected arrivals, gate entry, weigh tickets with blind verification, exit clearance, gate passes, yard board, paper fallback |
| Production | Recipes with flour and bran, milling runs, packing into bags, issue and output, finished-goods release, cost of production, extraction rate |
| Sales and receivables | Customers and credit limits, price lists, orders, delivery through the gate, invoices, cash sales, receipts, returns and credit notes |

**Depth.** Every function above has three levels of depth:

| Level | Meaning |
|---|---|
| **Core** | The flow works end to end with its key controls: approvals, segregation of duties, numbering without gaps, audit trail, posted documents that cannot be edited |
| **Standard** | The year-one controls and conveniences, e.g. two-factor sign-in for payment roles, delegation, PO amendments, self-billing, blind counts, paper fallback at the gate |
| **Full** | The remaining features of the first release, e.g. single sign-on with an external identity provider, global search, supplier notifications |

The release includes all six modules at core depth (§4). Standard and full features come in the same release if the team runs ahead of plan, or in the next release at the same rate.

**Also included:**
- installation on your infrastructure;
- data migration from your spreadsheets;
- role-based training;
- three weeks of hypercare after go-live;
- documentation (user guides per role, administration guide, runbook).

---

## 3. Team options

| Team | People (hours per week) | Suits |
|---|---|---|
| **A: 2 developers + AI + QA** | Lead developer (56 h), second developer (48 h), QA engineer (40 h) | The most schedule margin, and a dedicated tester for a payments-grade system |
| **B: 2 developers + AI** | Lead developer (56 h), second developer (48 h) | A lower price. The developers also carry the testing |
| **C: 1 developer + heavy AI** | Lead developer (56 h) | The lowest price. One person builds everything, so illness or leave stops the project, and the go-live needs your key users to carry more of the testing and training |
| *Reserve capacity* | *A third developer (48 h), added if the week-4 or week-9 checkpoint shows the team behind* | *Keeps the date; priced as extra hours only if needed* |

All developers use AI coding assistants throughout; the estimate already assumes it (Appendix A).

## 4. Delivery plan and prices

One release: **all six modules at core depth, built in W1–W26 and taken live in W27–W35.** Going live covers testing with your users, installation, data migration, training, cutover and three weeks of hypercare.

| Path | Team | Scope | Build | Go-live | Hours | US$ | ETB |
|---|---|---|---|---|---|---|---|
| **A** | 2 developers + AI + QA | All six modules at core depth | W1–W26 | W27–W35 | 4,640 | **$23,200** | 3,758,400 |
| **B** | 2 developers + AI | All six modules at core depth | W1–W26 | W27–W35 | 3,600 | **$18,000** | 2,916,000 |
| **C** | 1 developer with heavy AI | All six modules at core depth | W1–W26 | W27–W35 | 2,352 | **$11,760** | 1,905,120 |

- **Core depth** is every module's flow with its key controls (§2). The standard and full features are delivered in the same release if the team runs ahead of plan. Otherwise they are the first items of the next release, priced when you order them.
- **Progress checkpoints.** At weeks 4 and 9 we measure actual speed against the plan and report it to you. If the team is behind, we add capacity so the date and the six modules hold. Any extra hours are agreed with you before they are billed **[owner to confirm who bears them]**.

## 5. Price by package

| Package | What it covers | Path A | Path B | Path C |
|---|---|---|---|---|
| **1. Development** | Build, developer testing, CI and the development environments |2,704 h<br>**$13,520**<br>ETB 2,190,240 | 2,704 h<br>**$13,520**<br>ETB 2,190,240 | 1,456 h<br>**$7,280**<br>ETB 1,179,360 |
| **2. QA, testing and UAT** | QA engineer during the build (path A); system integration test and UAT with their fixes | 1,409 h<br>**$7,045**<br>ETB 1,141,290 | 369 h<br>**$1,845**<br>ETB 298,890 | 369 h<br>**$1,845**<br>ETB 298,890 |
| **3. Production deployment, hosting set-up and go-live** | Environment set-up, data migration, training, cutover, 3 weeks of hypercare | 527 h<br>**$2,635**<br>ETB 426,870 | 527 h<br>**$2,635**<br>ETB 426,870 | 527 h<br>**$2,635**<br>ETB 426,870 |
| **Total, build to go-live** | Packages 1–3 | 4,640 h<br>**$23,200**<br>**ETB 3,758,400** | 3,600 h<br>**$18,000**<br>**ETB 2,916,000** | 2,352 h<br>**$11,760**<br>**ETB 1,905,120** |
| 4. Maintenance and support | Monthly retainer after hypercare (§7) | from $150 / month | from $150 / month | from $150 / month |
| Hosting and infrastructure | Servers, backup storage, domain, TLS, email relay, devices | at cost, after the infrastructure answers | ← | ← |

## 6. Price by module

Development and QA hours during the build, shared out by each module's share of the estimated work. The platform foundation (sign-in, roles, approvals, numbering, audit, documents, master data, the stock ledger) is built once and serves every module. It is priced as its own line.

| Module | Path A: estimated h → billed h → US$ | Path B: estimated h → billed h → US$ | Path C: estimated h → billed h → US$ |
|---|---|---|---|
| Foundation | 844 → 1,328 → **$6,639** | 844 → 959 → **$4,794** | 844 → 516 → **$2,582** |
| Procurement | 256 → 403 → **$2,014** | 256 → 291 → **$1,454** | 256 → 157 → **$783** |
| Payables | 256 → 403 → **$2,014** | 256 → 291 → **$1,454** | 256 → 157 → **$783** |
| Inventory | 158 → 249 → **$1,243** | 158 → 180 → **$898** | 158 → 97 → **$483** |
| Lab/QC | 176 → 277 → **$1,384** | 176 → 200 → **$1,000** | 176 → 108 → **$538** |
| Gate | 222 → 349 → **$1,746** | 222 → 252 → **$1,261** | 222 → 136 → **$679** |
| Production | 170 → 267 → **$1,337** | 170 → 193 → **$966** | 170 → 104 → **$520** |
| Sales | 190 → 299 → **$1,494** | 190 → 216 → **$1,079** | 190 → 116 → **$581** |
| Hardening | 108 → 170 → **$849** | 108 → 123 → **$614** | 108 → 66 → **$330** |
| **Development window** | 2,380 → 3,744 → **$18,720** | 2,380 → 2,704 → **$13,520** | 2,380 → 1,456 → **$7,280** |

**Procurement on its own** (Module 1: platform + procurement + payables; or with stock and inspection added, which matches your procure-to-pay draft), if you prefer to start with one module:

| Package | Build h (incl. hardening) | Path A: weeks · US$ · ETB | Path B: weeks · US$ · ETB | Path C: weeks · US$ · ETB |
|---|---|---|---|---|
| Module 1, core: platform + procurement + payables | 1,420 | build W1–W17, live ≈ W23<br>**$15,760**<br>ETB 2,553,120 | build W1–W17, live ≈ W23<br>**$12,360**<br>ETB 2,002,320 | build W1–W16, live ≈ W22<br>**$8,000**<br>ETB 1,296,000 |
| Module 1, core + standard | 1,663 | build W1–W18, live ≈ W24<br>**$16,765**<br>ETB 2,715,930 | build W1–W18, live ≈ W24<br>**$13,165**<br>ETB 2,132,730 | build W1–W19, live ≈ W25<br>**$9,125**<br>ETB 1,478,250 |
| Client procure-to-pay bundle: Module 1 core + stock (inventory core) + inspection (QC core) | 1,770 | build W1–W20, live ≈ W26<br>**$18,315**<br>ETB 2,967,030 | build W1–W20, live ≈ W26<br>**$14,315**<br>ETB 2,319,030 | build W1–W20, live ≈ W26<br>**$9,515**<br>ETB 1,541,430 |

## 7. Maintenance and support

The retainer starts after hypercare. It is billed monthly, for a minimum of 12 months **[owner to confirm]**. Unused hours carry over for one month. **Defect fixes after hypercare are covered by the retainer**; there is no separate warranty period.

| Tier | Hours / month | Covers | Response | US$ / month | ETB / month | US$ / year |
|---|---|---|---|---|---|---|
| **Basic** | 30 | Defect fixes, security and dependency patches, backup checks | Next business day for all severities; business hours | $150 | 24,300 | $1,800 |
| **Standard** | 60 | Basic + minor enhancements, monthly health check, version upgrades, one report change a month | 4 business hours for Critical and High; next business day otherwise | $300 | 48,600 | $3,600 |
| **Premium** | 100 | Standard + 7-day on-call for Critical (weighbridge, gate or payments down), quarterly restore drill, small change requests | 2 hours for Critical, 7 days a week; 4 business hours for High | $500 | 81,000 | $6,000 |

- **Beyond the retainer:** work above the retainer hours is billed at the hourly rate, with your approval first.

## 8. Rate card, exchange rate and extras

| Role | Typical week | US$ / hour | ETB / hour | US$ / week | ETB / week |
|---|---|---|---|---|---|
| Lead developer | 7 days × 8 h = 56 h | $5.00 | 810 | $280 | 45,360 |
| Second developer | 6 days × 8 h = 48 h | $5.00 | 810 | $240 | 38,880 |
| Third developer | 6 days × 8 h = 48 h | $5.00 | 810 | $240 | 38,880 |
| QA engineer | 5 days × 8 h = 40 h | $5.00 | 810 | $200 | 32,400 |

- **Exchange rate.** The planning rate is **ETB 162.00 per US$**. Mid-market was 161.20 (Wise) and 162.17 (XE) on 2026-09-27. Commercial Bank of Ethiopia's transactional rates were 160.23 buying and 163.44 selling on 2026-09-17 (Appendix B).
  - Invoices are issued in ETB at the planning rate.
  - If the National Bank of Ethiopia rate on the invoice date differs from it by more than 5%, that invoice uses the day's rate **[owner to confirm]**.

  | Total, build to go-live | US$ | at ETB 145.80 (−10%) | at ETB 162.00 (planning) | at ETB 178.20 (+10%) |
|---|---|---|---|---|
| Path A | $23,200 | 3,382,560 | 3,758,400 | 4,134,240 |
| Path B | $18,000 | 2,624,400 | 2,916,000 | 3,207,600 |
| Path C | $11,760 | 1,714,608 | 1,905,120 | 2,095,632 |
- **Change requests:** new or changed requirements are estimated in hours, approved by you in writing, and billed at the hourly rate. Scope is only added to the release if work of equal size leaves it, or by agreement.
- **Extra non-development hours:** workshops, training sessions or meetings beyond those planned are billed at the hourly rate. Planned: 3 requirement workshops, the monthly demos, the training in §2.
- **Travel and expenses:** visits within the city where the team works are included. Travel beyond it is billed at cost (transport, accommodation, per diem) with your approval first; travel time is billed at the hourly rate **[owner to confirm]**.

## 9. Hosting and infrastructure

The system runs on infrastructure you choose. Nothing is assumed until you answer the stakeholder questionnaire:
- **on-premises** on your own server (Windows Server or Linux);
- **a virtual server** at a local provider or in a cloud;
- **a managed cloud setup.**

**Infrastructure is priced at cost** once you have answered. You buy it or rent it directly, or we buy it with your approval and re-invoice at cost. Our set-up hours are already in package 3.

**Indicative sizing for one plant**, to confirm with your answers:

| Item | Indicative size |
|---|---|
| Application and database server (or two servers) | 8 virtual CPUs, 32 GB memory, 500 GB SSD |
| Staging server, for testing updates | 4 virtual CPUs, 16 GB memory, 200 GB SSD |
| Backup storage, off the server | 1–2 TB, with one copy off site |
| Power | A UPS for the server and the gate PCs |
| Gate and weighbridge | One PC or tablet per gate post; a 4G router as a network fallback; label printer and barcode scanner (optional) |
| Domain name and TLS certificate; email relay | Optional; needed for email notifications and remote access |

## 10. Billing and payment schedule

Three billing models:

1. **Hybrid (recommended).** Development and QA are invoiced monthly for the hours worked, capped at the plan. Go-live services are a fixed fee (60% at UAT sign-off, 40% at hypercare exit). Maintenance is monthly. You pay only for hours worked, and the cap protects you from overruns.
2. **Fixed price by milestone.** The plan's price plus 10%, which covers the risk we then carry, paid at the milestones below.
3. **Time and materials.** Hours worked, invoiced monthly, with no cap.

**Team A:**

| When | Hybrid (recommended) | Fixed price per milestone |
|---|---|---|
| Signature (mobilisation) | Monthly invoice for the planned team hours, ≈ $3,120 | 10% = $2,552 |
| End-of-month-1 demo | Monthly, as above | 10% = $2,552 |
| End-of-month-2 demo | Monthly, as above | 10% = $2,552 |
| End-of-month-3 demo | Monthly, as above | 10% = $2,552 |
| End-of-month-4 demo | Monthly, as above | 10% = $2,552 |
| End-of-month-5 demo | Monthly, as above | 10% = $2,552 |
| Feature freeze (W26) | Monthly, as above | 10% = $2,552 |
| UAT sign-off | Go-live services: 60% of $4,480 = $2,688 | 15% = $3,828 |
| Hypercare exit | Go-live services: 40% = $1,792 | 15% = $3,828 |
| **Total** | **$23,200** (hours worked, capped at the plan) | **$25,520** (plan + 10% for the fixed-price risk) |

**Team B:**

| When | Hybrid (recommended) | Fixed price per milestone |
|---|---|---|
| Signature (mobilisation) | Monthly invoice for the planned team hours, ≈ $2,253 | 10% = $1,980 |
| End-of-month-1 demo | Monthly, as above | 10% = $1,980 |
| End-of-month-2 demo | Monthly, as above | 10% = $1,980 |
| End-of-month-3 demo | Monthly, as above | 10% = $1,980 |
| End-of-month-4 demo | Monthly, as above | 10% = $1,980 |
| End-of-month-5 demo | Monthly, as above | 10% = $1,980 |
| Feature freeze (W26) | Monthly, as above | 10% = $1,980 |
| UAT sign-off | Go-live services: 60% of $4,480 = $2,688 | 15% = $2,970 |
| Hypercare exit | Go-live services: 40% = $1,792 | 15% = $2,970 |
| **Total** | **$18,000** (hours worked, capped at the plan) | **$19,800** (plan + 10% for the fixed-price risk) |

**Team C:**

| When | Hybrid (recommended) | Fixed price per milestone |
|---|---|---|
| Signature (mobilisation) | Monthly invoice for the planned team hours, ≈ $1,213 | 10% = $1,294 |
| End-of-month-1 demo | Monthly, as above | 10% = $1,294 |
| End-of-month-2 demo | Monthly, as above | 10% = $1,294 |
| End-of-month-3 demo | Monthly, as above | 10% = $1,294 |
| End-of-month-4 demo | Monthly, as above | 10% = $1,294 |
| End-of-month-5 demo | Monthly, as above | 10% = $1,294 |
| Feature freeze (W26) | Monthly, as above | 10% = $1,294 |
| UAT sign-off | Go-live services: 60% of $4,480 = $2,688 | 15% = $1,940 |
| Hypercare exit | Go-live services: 40% = $1,792 | 15% = $1,940 |
| **Total** | **$11,760** (hours worked, capped at the plan) | **$12,936** (plan + 10% for the fixed-price risk) |

**For every model:**
- Payment is due 15 days after invoice **[owner to confirm]**.
- Month-end demos are the acceptance points for development.
- UAT sign-off and hypercare exit are the acceptance points for go-live services.

## 11. Assumptions

1. **Client decisions and inputs.**
   - You name a project sponsor and one key user per module, available about 4 hours a week during the build and full-time during UAT.
   - Answers to the questionnaire arrive by the weeks listed in it. Late answers move the affected modules, not the price per hour.
   - Master data (items, suppliers, customers, storage units, opening balances) is supplied in our templates. Cleaning your data is your responsibility; we help with checks.
2. **Scope.** Requirements are as in the FRD version 0.3.2 and the process model. Changes go through §8.
3. **Standards.** Screens follow your procure-to-pay draft's layout, with the corrections we agreed. English with the Gregorian calendar in the first release.
4. **Rates.** Tax rates, withholding rules and approval bands are configuration you confirm. No country rule is hard-coded.
5. **Infrastructure.** Infrastructure and third-party services are bought at cost and are not in these prices (§9).
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
| Acceptance | Month-end demos for development; the UAT script for go-live; hypercare exit criteria for handover |
| Termination | Either side with 30 days' notice; hours worked are paid; delivered work and your data are handed over |

## 14. Next steps

1. Choose the team (A, B or C).
2. Name the sponsor and the key users; choose the start week (W1).
3. Answer the stakeholder questionnaire. The items needed before W1 come first: product scope ("flour"), currency of purchase orders, the meaning of "operation unit", and users and roles.
4. Sign the agreement. W1 starts with the platform, and the first progress checkpoint is at week 4.

---

## Appendix A. Basis of estimate

- **Bottom-up.** Every one of the 205 first-release requirements was mapped to one of 187 features, and each feature estimated in developer hours with AI assistance (implementation plan v0.4, Appendix A).
  - Core depth: 2,380 hours.
  - Core + standard: 2,986 hours.
  - Full first release: 3,102 hours.
- **Capacity.** About 80% of each person's hours go to building; the rest goes to meetings, reviews, demos and your questions. One week of the 26 is kept as reserve.
- **Plan.** The six modules at core depth are scheduled into each developer's weeks, respecting which module depends on which, so they finish by week 25. The week-4 and week-9 checkpoints compare actual speed with that plan.
- **Price.** Team members × weekly hours × 26 weeks × US$5 for development and QA during the build, plus the go-live hours. It is not a fixed price unless you choose the fixed-price model (§10).

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
