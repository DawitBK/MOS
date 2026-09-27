# Factory ERP: questions for your team

| | |
|---|---|
| Document | Stakeholder questionnaire: business and infrastructure questions for the new system |
| Version | **0.1, draft for the owner's review — not yet sent** |
| Date | 2026-09-28 |
| For | [Client name]: the project sponsor, and the owners of procurement, finance, stores, quality, security/gate, production, sales and IT |
| From | [Vendor name] |

---

## How to answer

- Each question has a number (for example C-04 or I-02). Please keep the number with your answer.
- **"Needed by"** is the week of the project in which we need the answer. Week 1 is the week the project starts. Answers marked "Before W1" are needed before we start.
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
| C-05 | Who are the users, with roles, departments and sites? Who is the project sponsor, and who is the key user per module? | Accounts, scopes, approvers, UAT | — (needed) | Before W4 | |
| C-06 | How long must documents and audit records legally be kept? | Retention and archiving | 10 years online | Before W27 | |
| C-07 | Approval bands for PRs, POs, PVs, adjustments and bank-detail changes: who is "Management", and which amounts go to whom? | Seeds the approval matrix | Two stages above a threshold, one below; PVs always approved | Before W6 | |

## 2. Procurement

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-08 | Is a PO without a PR ever allowed (never, or for named categories)? | PO validation | Never | Before W8 | |
| C-09 | Should suppliers self-register through a link ("Supplier Portal" on the draft)? Are there individual suppliers (farmers, traders) without a TIN? | Self-registration is v1.1; individuals need self-billing and the no-TIN rate | Procurement registers suppliers; individuals allowed | Before W8 | |
| C-10 | Which documents must a supplier provide per supplier group (licence, TIN, VAT, bank letter), and which expire? | Onboarding checklist | Trade licence, TIN, VAT certificate, bank letter | Before W8 | |
| C-11 | How many quotations are needed per amount band, and when is sole source allowed? | Sourcing policy on the PO | Three above a band, justification otherwise | Before W8 | |
| C-12 | Quantity and price tolerances between PR, PO, GRN and invoice | Over-receipt, over-ordering and matching rules | PR→PO 0%; receipt +2%; invoice price ±1% | Before W8 | |
| C-13 | Confirm: partial deliveries and several GRNs against one PO are allowed | Designed that way | Yes | Before W8 | |

## 3. Payables and payments

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-14 | Are invoices received before or after the goods, and at gross or accepted weight? | The matching basis | After the GRN, at payable weight | Before W10 | |
| C-15 | Should the person who pays be different from the person who raised the payment request? (The draft shows the same finance officer) | An SoD rule | The same person may raise and record, never approve | Before W10 | |
| C-16 | Payment methods and paying accounts (banks, cash boxes, mobile money); can the method change after approval? | Paying accounts and re-approval rule | Transfer and cheque; a change needs re-approval | Before W10 | |
| C-17 | Is the company a withholding agent? Current VAT and withholding rates and thresholds, including the no-TIN rate | Tax codes are seed data | Rates entered by finance before go-live | Before W10 | |
| C-18 | The WHT certificate layout and numbering rules | Certificate template | A generic certificate with a gap-free number | Before W10 | |
| C-19 | Confirm: three-way match is a hard control before payment (services by confirmation; farmers by self-billing) | Designed that way | Yes | Before W10 | |
| C-20 | Which accounting package receives the journals, and in which import format? | CSV export layout and account map | A generic journal CSV | Before W10 | |

## 4. Stores and inventory

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-21 | Rejected quantity on a GRN: unloaded and held, or left on the truck? Then return, replacement or disposal? | Disposition rules and stock status | Both allowed, chosen per line | Before W12 | |
| C-22 | Empty bags: returned, kept as packaging stock, or credited? The tare per bag type | Payable net weight | Returned; 0.15 kg per bag | Before W12 | |
| C-23 | Stores, silos and their capacities (and silo geometry for dips); who approves transfers between stores? | Storage set-up and transfer approval | Warehouse supervisor approves | Before W4 (list) / W12 (approvers) | |

## 5. Laboratory and quality

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-24 | The item list with lot tracking, inspection requirement and inspection point; the test ranges per raw material | QC policies and specs | The grain pack defaults | Before W14 | |
| C-25 | Grading: worst parameter or score bands? The actual grade table and score weights behind "92.4 / 100" | Grade and score model | Worst-parameter grading; equal weights | Before W14 | |
| C-26 | Price effect of quality: a differential per grade, deductions per parameter, or both? | Payable quantity and price | Moisture and impurity deductions | Before W14 | |
| C-27 | Can one truck be partly accepted? Who decides: the inspector or the QC manager? | Usage-decision rights | Yes; the QC manager | Before W14 | |
| C-28 | Confirm the end-to-end procure-to-pay example (one supplier, PO, truck and payment) we will use for acceptance | Acceptance scenario AS-13 | The corrected example in the FRD | Before W14 | |

## 6. Gate and weighbridge

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-29 | Gate workshop: documents checked in and out; weighbridge make, capacity and division; tolerances; who may override | Gate module design | The grain pack defaults | Before W14 | |

## 7. Production

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-30 | Production workshop: products, milling stages, extraction rates, by-products, packing sizes, fortification; reservation, issue, return and scrap | Production module design | The flour pack defaults | Before W14 | |

## 8. Sales

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-31 | Sales workshop: customers, price lists, credit rules, delivery documents, sales approvals | Sales module design | The designed defaults | Before W18 | |
| C-32 | Is a fiscal device or e-invoice legally required for sales invoices? | Could add a v1 integration | Fiscal reference recorded by hand | Before W18 | |
| C-33 | Who posts customer invoices at the mill: sales staff, a billing clerk or finance? | Roles and SoD | A billing clerk in finance | Before W18 | |
| C-34 | Are customers ever refunded in cash or by transfer, or are credit balances always offset? | A refund document would be added | Always offset | Before W18 | |

## 9. Going live

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| C-35 | Migration data extracts, UAT dates, training schedule and the go-live date | Go-live plan | — | Before W27 | |

## 10. Infrastructure and IT

Answered by: ____________________ Date: __________

| No. | Question | Why we ask | If we don't hear from you | Needed by | Your answer |
|---|---|---|---|---|---|
| I-01 | Where should the system run: your server room, a local data centre, or a cloud? Any rule that data must stay in Ethiopia? | The hosting option | On-premises | Before W18 | |
| I-02 | Which servers are available (CPU, memory, disk), physical or virtual, and which operating system (Windows Server or Linux, version)? | Sizing and installation | A new virtual server, 8 vCPU / 32 GB / 500 GB | Before W18 | |
| I-03 | May we run containers (Docker), or must services run natively? | Packaging | Native services | Before W18 | |
| I-04 | Is there a spare server or VM for a staging environment? | Testing updates before production | A smaller staging VM | Before W18 | |
| I-05 | Network: are the gate, weighbridge, lab, stores and offices on the same network? Wired or Wi-Fi? How reliable is it? | Station connectivity | Wired LAN to each station | Before W18 | |
| I-06 | Internet: speed and reliability at the plant; is remote access (VPN) allowed for support? | Support and updates | VPN for support | Before W18 | |
| I-07 | Power: is there a UPS for the server and the gate PCs? How often does power fail? | Degraded modes | UPS recommended | Before W18 | |
| I-08 | Backups: where can backups go (a second disk, NAS, off-site copy)? Who checks them? | Backup target | Nightly to a NAS plus an off-site copy | Before W18 | |
| I-09 | After a failure, how much data may be lost at most (RPO) and how long may the system be down (RTO)? | Backup frequency and recovery design | RPO 15 minutes, RTO 4 hours | Before W18 | |
| I-10 | Domain name and certificate: will users reach the system by a name (e.g. erp.company.et)? Who manages DNS? | TLS and access | An internal name with a certificate | Before W18 | |
| I-11 | Email: is there an email server or relay MOS may send through (notifications, PO emails)? | Email is optional | No email; in-app notifications only | Before W18 | |
| I-12 | Sign-in: do you use Active Directory, Microsoft Entra ID or another identity provider, and should MOS use it? | SSO is optional | Local accounts with TOTP | Before W18 | |
| I-13 | Devices: PCs or tablets at the gate, weighbridge, lab, stores and production; printers (A4, label); barcode scanners; cameras for weighbridge photos | Screens and fallbacks | One PC per station; A4 printing; typing instead of scanning | Before W18 | |
| I-14 | Weighbridges: make, model, indicator, capacity, division and calibration date | Manual entry and tolerances (capture is Later) | Manual entry | Before W18 | |
| I-15 | Security policy: password rules, MFA, who may administer the server, antivirus on servers | Configuration and hardening | Our defaults (ASVS L2) | Before W18 | |
| I-16 | Who is the IT contact for installation and support, and in which hours? | Go-live and maintenance | — | Before W18 | |
| I-17 | Other systems: banking platforms, the tax authority portal, the current accounting package; anything MOS must connect to later? | Integration roadmap (CSV export in v1) | CSV exports only | Before W18 | |

## 11. Data we need from you

| What | Format | Needed by |
|---|---|---|
| Users: name, role, department, site, email or phone | Our Excel template | Before W4 |
| Items and units of measure, with lot and inspection flags | Our Excel template | Before W4 (list), W14 (flags) |
| Stores, silos and capacities (and silo drawings for dip readings) | Our Excel template | Before W4 |
| Suppliers with contacts, TIN, bank details and documents | Our Excel template + scanned documents | Before W4 |
| Banks, company accounts, cheque book ranges | Our Excel template | Before W10 |
| Tax codes and rates (VAT, withholding, the no-TIN rate) | A table from finance | Before W10 |
| Test specifications, grade table, score weights, deduction rules | Existing lab documents | Before W14 |
| Opening stock with lots and cost; open POs; unpaid supplier invoices | Our Excel templates, at the cutover date | Two trial runs from W29, final at cutover |
| Sample documents in use today (PR, PO, GRN, payment voucher, WHT certificate, weigh ticket, invoice) | Photos or PDFs | Before W4 |

Thank you. We will confirm every answer in writing and record it in the project's decision register.
