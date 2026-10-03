# Factory ERP: questions for your team

<!-- nav -->[MOS](../../README.md) › [5. For the client](README.md)

| | |
|---|---|
| Document | Stakeholder questionnaire: business and infrastructure questions for the new system |
| Version | **0.2, draft for the owner's review — not yet sent** (0.2, 2026-10-03: the six-month plan's dates; questions from your October documents in §12) |
| Date | 2026-10-03 |
| For | [Client name]: the project sponsor, and the owners of procurement, finance, stores, quality, security/gate, production, sales and IT |
| From | [Vendor name] |

---

## How to answer

- Each question has a number (for example C-04 or I-02). Please keep the number with your answer.
- **"Needed by"** is the week of the project in which we need the answer. Week 1 is the week the project starts; the system goes live in week 24. Answers marked "Before W1" are needed before we start, including **the infrastructure questions for the test server** (section 10).
- **"If we don't hear from you"** is what we will build meanwhile. You can change it later, but a late change may cost extra work.
- Write "yes, as proposed" where the proposal is right. Attach files (lists, tables, sample documents) where asked, and note the file name in the answer.
- One person per section is enough. Their name goes at the top of the section.

## 1. General and approvals

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-01 | Does "Floor Manufacturing" mean **flour** (the draft inspects wheat for "Addis Flour Factory")? | Decides the industry pack and every example | Flour milling | Before W1 | |
| C-02 | What is the product called at your company: "Factory ERP" (as on the sign-in mock-up) or another name? | Branding on screens and documents | "Factory ERP" | Before W1 | |
| C-03 | The **operation unit** chosen at sign-in: a plant (site), a legal company, or a department? | Sets the organisation model and access scopes | A site (plant) | Before W1 | |
| C-04 | **Currency:** PRs and POs show USD, invoices and payments ETB. Are some purchases in foreign currency? How are they converted and paid? | Document currency, exchange-rate storage and FX variance postings | One currency per document; USD POs keep their rate; payment in ETB with FX variance | Before W1 | |
| C-05 | Who are the users, with roles, departments and sites? Who is the project sponsor, and who is the key user per module? | Accounts, scopes, approvers, UAT | — (needed) | Before W1 (sponsor, key users) / W3 (users and roles) | |
| C-06 | How long must documents and audit records legally be kept? | Retention and archiving | 10 years online | Before W17 | |
| C-07 | Approval bands for PRs, POs, PVs, adjustments and bank-detail changes: who is "Management", and which amounts go to whom? | Seeds the approval matrix | Two stages above a threshold, one below; PVs always approved | Before W4 | |

## 2. Procurement

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-08 | Is a PO without a PR ever allowed (never, or for named categories)? | PO validation | Never | Before W6 | |
| C-09 | Should suppliers self-register through a link ("Supplier Portal" on the draft)? Are there individual suppliers (farmers, traders) without a TIN? | Self-registration is v1.1; individuals need self-billing and the no-TIN rate | Procurement registers suppliers; individuals allowed | Before W6 | |
| C-10 | Which documents must a supplier provide per supplier group (licence, TIN, VAT, bank letter), and which expire? | Onboarding checklist | Trade licence, TIN, VAT certificate, bank letter | Before W6 | |
| C-11 | How many quotations are needed per amount band, and when is sole source allowed? | Sourcing policy on the PO | Three above a band, justification otherwise | Before W6 | |
| C-12 | Quantity and price tolerances between PR, PO, GRN and invoice | Over-receipt, over-ordering and matching rules | PR→PO 0%; receipt +2%; invoice price ±1% | Before W6 | |
| C-13 | Confirm: partial deliveries and several GRNs against one PO are allowed | Designed that way | Yes | Before W6 | |

## 3. Payables and payments

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-14 | Are invoices received before or after the goods, and at gross or accepted weight? | The matching basis | After the GRN, at payable weight | Before W8 | |
| C-15 | Should the person who pays be different from the person who raised the payment request? (The draft shows the same finance officer) | An SoD rule | The same person may raise and record, never approve | Before W8 | |
| C-16 | Payment methods and paying accounts (banks, cash boxes, mobile money); can the method change after approval? | Paying accounts and re-approval rule | Transfer and cheque; a change needs re-approval | Before W8 | |
| C-17 | Is the company a withholding agent? Current VAT and withholding rates and thresholds, including the no-TIN rate | Tax codes are seed data | Rates entered by finance before go-live | Before W8 | |
| C-18 | The WHT certificate layout and numbering rules | Certificate template | A generic certificate with a gap-free number | Before W8 | |
| C-19 | Confirm: three-way match is a hard control before payment (services by confirmation; farmers by self-billing) | Designed that way | Yes | Before W8 | |
| C-20 | Which accounting package receives the journals, and in which import format? | CSV export layout and account map | A generic journal CSV | Before W8 | |

## 4. Stores and inventory

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-21 | Rejected quantity on a GRN: unloaded and held, or left on the truck? Then return, replacement or disposal? | Disposition rules and stock status | Both allowed, chosen per line | Before W7 | |
| C-22 | Empty bags: returned, kept as packaging stock, or credited? The tare per bag type | Payable net weight | Returned; 0.15 kg per bag | Before W7 | |
| C-23 | Stores, silos and their capacities (and silo geometry for dips); who approves transfers between stores? | Storage set-up and transfer approval | Warehouse supervisor approves | Before W3 (list) / W7 (approvers) | |

## 5. Laboratory and quality

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-24 | The item list with lot tracking, inspection requirement and inspection point; the test ranges per raw material | QC policies and specs | The grain pack defaults | Before W10 | |
| C-25 | Grading: worst parameter or score bands? The actual grade table and score weights behind "92.4 / 100" | Grade and score model | Worst-parameter grading; equal weights | Before W1 (pass/fail or grades: Q-02) / W10 (the grade table) | |
| C-26 | Price effect of quality: a differential per grade, deductions per parameter, or both? | Payable quantity and price | Moisture and impurity deductions | Before W1 (Q-02) / W10 (the rules) | |
| C-27 | Can one truck be partly accepted? Who decides: the inspector or the QC manager? | Usage-decision rights | Yes; the QC manager | Before W10 | |
| C-28 | Confirm the end-to-end procure-to-pay example (one supplier, PO, truck and payment) we will use for acceptance | Acceptance scenario AS-13 | The corrected example in the FRD | Before W4 | |

## 6. Gate and weighbridge

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-29 | Gate workshop: documents checked in and out; weighbridge make, capacity and division; tolerances; who may override | Gate module design | The grain pack defaults | Before W1 (gate module or not: Q-01) / W10 (workshop) | |

## 7. Production

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-30 | Production workshop: products, milling stages, extraction rates, by-products, packing sizes, fortification; reservation, issue, return and scrap | Production module design | The flour pack defaults | Before W10 | |

## 8. Sales

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-31 | Sales workshop: customers, price lists, credit rules, delivery documents, sales approvals | Sales module design | The designed defaults | Before W12 | |
| C-32 | Is a fiscal device or e-invoice legally required for sales invoices? | Could add a v1 integration | Fiscal reference recorded by hand | Before W12 | |
| C-33 | Who posts customer invoices at the mill: sales staff, a billing clerk or finance? | Roles and SoD | A billing clerk in finance | Before W12 | |
| C-34 | Are customers ever refunded in cash or by transfer, or are credit balances always offset? | A refund document would be added | Always offset | Before W12 | |

## 9. Going live

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-35 | Migration data extracts, UAT dates, training schedule and the go-live date | Go-live plan | — | Before W17 (migration extracts) / W21 (UAT key users, training dates, go-live date) | |

## 10. Infrastructure and IT

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| I-01 | Where should the system run: your server room, a local data centre, or a cloud? Any rule that data must stay in Ethiopia? | The hosting option | On-premises | Before W1 | |
| I-02 | Which servers are available (CPU, memory, disk), physical or virtual, and which operating system (Windows Server or Linux, version)? | Sizing and installation | A new virtual server, 8 vCPU / 32 GB / 500 GB | Before W1 | |
| I-03 | May we run containers (Docker), or must services run natively? | Packaging | Native services | Before W1 | |
| I-04 | Is there a spare server or VM for a staging environment? | Testing updates before production | A smaller staging VM | Before W1 | |
| I-05 | Network: are the gate, weighbridge, lab, stores and offices on the same network? Wired or Wi-Fi? How reliable is it? | Station connectivity | Wired LAN to each station | Before W12 | |
| I-06 | Internet: speed and reliability at the plant; is remote access (VPN) allowed for support? | Support and updates | VPN for support | Before W1 | |
| I-07 | Power: is there a UPS for the server and the gate PCs? How often does power fail? | Degraded modes | UPS recommended | Before W12 | |
| I-08 | Backups: where can backups go (a second disk, NAS, off-site copy)? Who checks them? | Backup target | Nightly to a NAS plus an off-site copy | Before W12 | |
| I-09 | After a failure, how much data may be lost at most (RPO) and how long may the system be down (RTO)? | Backup frequency and recovery design | RPO 15 minutes, RTO 4 hours | Before W12 | |
| I-10 | Domain name and certificate: will users reach the system by a name (e.g. erp.company.et)? Who manages DNS? | TLS and access | An internal name with a certificate | Before W12 | |
| I-11 | Email: is there an email server or relay MOS may send through (notifications, PO emails)? | Email is optional | No email; in-app notifications only | Before W12 | |
| I-12 | Sign-in: do you use Active Directory, Microsoft Entra ID or another identity provider, and should MOS use it? | SSO is optional | Local accounts with TOTP | Before W12 | |
| I-13 | Devices: PCs or tablets at the gate, weighbridge, lab, stores and production; printers (A4, label); barcode scanners; cameras for weighbridge photos | Screens and fallbacks | One PC per station; A4 printing; typing instead of scanning | Before W12 | |
| I-14 | Weighbridges: make, model, indicator, capacity, division and calibration date | Manual entry and tolerances (capture is Later) | Manual entry | Before W10 | |
| I-15 | Security policy: password rules, MFA, who may administer the server, antivirus on servers | Configuration and hardening | Our defaults (ASVS L2) | Before W1 | |
| I-16 | Who is the IT contact for installation and support, and in which hours? | Go-live and maintenance | — | Before W1 | |
| I-17 | Other systems: banking platforms, the tax authority portal, the current accounting package; anything MOS must connect to later? | Integration roadmap (CSV export in v1) | CSV exports only | Before W12 | |

## 11. Data we need from you

| What | Format | Needed by |
|---|---|---|
| Users: name, role, department, site, email or phone | Our Excel template | Before W3 |
| Items and units of measure, with lot and inspection flags | Our Excel template | Before W3 (list), W10 (flags) |
| Stores, silos and capacities (and silo drawings for dip readings) | Our Excel template | Before W3 |
| Suppliers with contacts, TIN, bank details and documents | Our Excel template + scanned documents | Before W3 |
| Banks, company accounts, cheque book ranges | Our Excel template | Before W8 |
| Tax codes and rates (VAT, withholding, the no-TIN rate) | A table from finance | Before W8 |
| Test specifications, grade table, score weights, deduction rules | Existing lab documents | Before W10 |
| Opening stock with lots and cost; open POs; unpaid supplier invoices | Our Excel templates, at the cutover date | A first trial run in W17–W20, a rehearsal in W23, the final load at go-live (W24) |
| Sample documents in use today (PR, PO, GRN, payment voucher, WHT certificate, weigh ticket, invoice) | Photos or PDFs | Before W3 |

## 12. Questions from your October documents

Answered by: ____________________ Date: __________

Some of these may already have been answered in our discussion of 3 October; please confirm them here in writing.

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-36 | Is your architecture document a **requirement** or a **recommendation**? Will your own developers maintain or extend the system after handover, and with which skills? Do you need SMS notifications? | Decides how far we follow it | A recommendation; we deliver our stack with full source and documentation | Before W1 | |
| C-37 | Is the data dictionary the **required table design**, or the list of data you need captured? | Table design | The data to capture; our design captures every field | Before W1 | |
| C-38 | **Weighing and the gate.** Do you weigh trucks, and is the payable quantity the weighbridge net, the bag count or the delivery note? Do you want the gate and weighbridge module in this release? | Could remove about 190 core hours, or confirm them | Gate module in scope; weights typed when the gate is off | Before W1 | |
| C-39 | **Who raises supplier payment requests:** your finance officer or the supplier? Should suppliers use the system at all? | A supplier portal is a separate scope | The finance officer; no supplier access in this release | Before W1 | |
| C-40 | Which documents or screens in v2 are final, and which are examples? Will a third revision with one coherent example follow? | Acceptance data (C-28) | The fields are final; the numbers are examples | Before W4 | |
| C-41 | **Supplier delivery entry:** who records it (procurement, stores, security), and what does "Approve" on it mean? | The delivery record | Stores record it; "approve" = matches an open PO, send to inspection | Before W6 | |
| C-42 | Should **every** supplier invoice be approved, in addition to the payment request, or only invoices with a match difference? | Optional invoice approval | Only match differences | Before W1 (sizing: Q-04) / W8 (the rule) | |
| C-43 | Are **rejected** (delivered, refused) and **short** (not delivered) quantities recorded separately? | Your GRN screen merges them | Separately | Before W6 | |
| C-44 | When raw material goes "for production", is it moved to a production staging store, or issued directly to the milling run? | Transfer vs issue | Issued to the work order | Before W10 | |
| C-45 | Should counters see the system quantity or count blind? Do you count in Excel and import? | Count design | Blind; Excel import available | Before W7 | |
| C-46 | May an approved count be adjusted by a different figure? | Count integrity | No: a different figure needs a recount | Before W7 | |
| C-47 | Do you budget purchases (the dictionary's budget code)? Record only, or should the system check budgets? | Budget code | Record and report only | Before W6 | |
| C-48 | Your architecture document lists non-conformance, production plans, finished-flour release, a stock check before sales-order approval and sales returns. Which do you need at go-live? | Scope at go-live | Finished-flour release and the stock check at go-live (now core); the rest after go-live | Before W1 | |
| C-49 | Can one person order, inspect **and** receive the same delivery? Which roles must be different people? | Segregation-of-duties rules | Three different people; logged administrator exceptions | Before W4 | |
| I-18 | Are the branches (operation units) at other locations, and how do they reach the central server: internet, VPN or a private line? | One central server must be reachable from every branch | One site, or branches over VPN | Before W12 | |

Thank you. We will confirm every answer in writing and record it in the project's decision register.
