# MOS: Review of the client's procure-to-pay draft, and the plan revisions it caused

| | |
|---|---|
| Source reviewed | "Raw Material Procurement-to-Payment Process – 1st Draft": **18 pages = 1 narrative page + 17 screen mock-ups (21 images; the 5-step supplier wizard counts as one screen).** Copies in [client-input/](client-input/): `P2P-process-1st-draft.pdf`, `.docx` |
| Not client input | The procure-to-pay **swimlane** image was made with ChatGPT by the owner, from the client's narrative. It is in [reference-input/owner-brainstorm/](reference-input/owner-brainstorm/). v0.2 of this review wrongly called it the client's swimlane (corrected in v0.3; see the [audit](MOS-PACKAGE-AUDIT-v0.3.md)) |
| Date | 2026-09-27 (v0.2 review); revised 2026-09-27 for package v0.3 |
| Planning package version | This review caused v0.1 → **v0.2**. The v0.3 re-frame ([ADR-0017/18/19](adr/)) supersedes its timeline statements (§7) |
| Audience | Product owner, the client's process owners, developers |
| Status | Planning only. No code has been written |

---

## 1. What the draft describes

**The client's narrative** (PDF page 1) describes a raw-material flow across seven roles. The step table below is taken from that narrative.

| # | Step | Role |
|---|---|---|
| 1 | Raise purchase requisition (PR) for raw materials | Storekeeper |
| 2 | Review and approve PR | Management |
| 3 | Convert PR to purchase order (PO) and send to supplier | Purchasing department |
| 4 | Deliver the materials | Supplier |
| 5 | Inspect against specifications | Inspection / QC |
| 6 | If passed: receive goods, generate GRN, add the accepted quantity to stock | Storekeeper |
| 7 | Raise payment request from PO + GRN + supplier invoice | Finance officer |
| 8 | Review and approve payment request | Authorised approver |
| 9 | Process payment (cheque or bank transfer) | Finance officer |

Two details appear only in the owner's ChatGPT swimlane, not in the client's narrative:
- the fail branch ("reject materials and notify supplier; return or replacement");
- the supplier's "receive PO and prepare delivery".

MOS designs a fail path anyway (FRD §9.5), but **what the client actually does with rejected material is a client question** (§6).

**The 17 screens:**
- sign-in;
- supplier registration (5 steps, titled "Supplier Portal – New Registration");
- PR form and PR approvals dashboard;
- PO form and PO approvals dashboard;
- wheat inspection (for "Addis Flour Factory");
- item, warehouse, store and silo masters;
- goods receiving (GRN);
- stock balance;
- stock adjustment;
- payment request, payment approval and supplier payment.

## 2. Verdict

**The draft and the plan agree on the spine:** PR → approval → PO → inspection → GRN → stock → payment request → approval → payment. Roughly 80% of the draft maps directly onto FRD requirements.

It changed the plan in five material ways (v0.2):

1. **"First module" means the whole procure-to-pay chain**, including inspection, stores and payment. The plan delivers this chain end to end as its own milestone. Under v0.3 that is **the end of M3 without inspection and the end of M4 with inspection** (AS-13).
2. **People confirm the automated steps.** The storekeeper generates the GRN, and the finance officer raises the payment request. The rule is "the system prepares, a named role confirms" (ADR-0016); full auto-posting stays available as a setting.
3. **Sign-in has a username/password option as well as SSO,** plus an "operation unit" choice. Local accounts are in v1, and they are the default in v0.3 (ADR-0015).
4. **Inspection produces a grade and a quality score,** not just pass/fail.
5. **Stores are richer than the plan assumed:** warehouse → store / silo, with capacity, levels, storekeepers, restricted access and receipt/issue/transfer flags.

**What it also tells us:**
- **The client is a flour mill.** The inspection screen names "Addis Flour Factory" and durum wheat in 50 kg bags; the stock screen lists bran. v0.3 treats "Floor Manufacturing" as flour until the client confirms.
- **Production already touches this flow.** The stock history shows "Issue to Production Line 1" and a bran receipt.

The draft contains about 40 defects that should be fixed before its second revision (§5). None blocks planning, but several would become bugs if built as drawn.

---

## 3. Screen-by-screen mapping

"As-is" means adopted as drawn. "Changed" means adopted with the changes listed.

| Draft screen | Maps to (FRD v0.3) | Decision | Notes |
|---|---|---|---|
| Sign-in: username, password, operation unit, "SSO Login" | FR-PLT-010, 018, 019 | Changed | Local accounts are the default and SSO is optional (ADR-0015). The operation unit (a site) is picked **right after** sign-in, so the public page never lists the company's sites |
| Supplier registration, 5 steps ("Supplier Portal") | FR-PRC-001, FR-MD-012, 013, 014; FR-PRC-020 (v1.1) | Changed | Fields adopted in full. Status and registration date are system-controlled. Staff use the wizard in v1; supplier self-registration by invite link is v1.1. **Whether the client expects suppliers to self-register is a question** (§6) |
| PR form (header and lines) | FR-PRC-007, FR-PLT-007, FR-PLT-055 | Changed | Adds priority, department, requester employee ID, required date (≥ request date), live available-to-use stock, computed quantity to purchase and a specification link. Status becomes action-driven |
| PR approvals dashboard | FR-PLT-030, 032, 054; FR-RPT-001 | Changed | Bulk approve/reject with guardrails. Approval history is immutable (no delete icon). Only submitted items appear in the queue |
| PO form | FR-PRC-010 | Changed | The PR link is mandatory by default (as drawn). Adds delivery terms, plant, storage location, header discount (allocated to lines for costing) and an explicit **ISSUED** status |
| PO approvals dashboard (KPI tiles incl. average approval time) | FR-RPT-001, FR-PLT-054 | Changed | Approval cycle time becomes a standard KPI. The delete icons on approval rows are not built |
| Wheat inspection | FR-QC-004, 008, 009, 012, 013, 022–025 | Changed | Grades, weighted quality score, optional "additional parameters" and grade-based price differentials. Net weight is *provisional* until the tare is weighed. **With the gate module off, this screen creates the inspection lot** (FR-QC-025). Adds accepted and rejected quantity. The wheat parameters become the grain/flour-milling pack |
| Item master | FR-MD-003 | As-is | "Inspection required" drives the QC policy; "raw material" maps to item type; standard cost is a reference price, not the valuation method. Adds the spare-part (MRO) item type (the store master has a Spare Parts store) |
| Warehouse, store and silo masters | FR-MD-010, FR-INV-019, 020, FR-PLT-053 | Changed | Warehouse → storage unit (STORE / SILO / TANK / BIN / YARD / QUARANTINE / REJECT / IN_TRANSIT). Silos get capacity, validated levels, a calculated current level and dip measurement. Valuation method is company-level, not per store or silo. "Close Warehouse" becomes deactivate-only, refused while stock remains |
| Goods receiving (GRN) | FR-PRC-013, 021, FR-INB-002 | Changed | Pre-filled from the PO and the inspection (or gate). Accepted and rejected quantity per line, with the rejected quantity either returned on the truck or held as REJECTED stock. Bag tare deducted. Batch pattern `BATCH-{YYYYMMDD}-{SEQ:2}`. Weighed and counted lines can be mixed. The storekeeper confirms ("Save & Generate GRN") |
| Stock balance and transaction history | FR-INV-001, 014, 015, FR-RPT-005 | Changed | Columns adopted, plus DAMAGED and **Reserved** (v1, because sales is now v1). The formula and status breakdown are defined so totals reconcile, and totals are per unit of measure (the draft's example fails on both; see §5) |
| Stock adjustment | FR-INV-008, 009 | Changed | Count-driven adjustments are generated from the count. The approver comes from the approval matrix, not typed in. "Save & Post" becomes "Submit", with auto-post only under the threshold |
| Payment request (finance officer) | FR-AP-001, 007, 013, 014; FR-INB-003 | Changed | Pre-drafted when the GRN posts and routed to the finance officer. Invoices are captured inline (stored as separate records). Partial amounts; gross / WHT / net shown. Derived total. Prefix `PV-` instead of `PR-` |
| Payment approval | FR-AP-007, FR-PLT-025 | As-is | Comments are mandatory for both approve and reject, as drawn |
| Supplier payment | FR-AP-008, 012, 015, 016 | Changed | Cheque number and date (a post-dated cheque is allowed), cheque register and cheque books, amount in words, **proof-of-payment attachment**, invoice selection limited to the approved request. **The payment method must equal the approved request's; the paying account is a company account** |

---

## 4. Plan changes made (decisions)

| # | Decision | Where recorded |
|---|---|---|
| D1 | **Local username/password accounts** in v1 (the default in v0.3) alongside optional SSO, configured per installation: argon2id, lockout, password policy, optional TOTP (enforceable per role), admin reset that forces a change. Breached-password check in v1.1 | ADR-0015, FR-PLT-018, TDD §6.1 |
| D2 | **Operation unit** (a site) chosen right after sign-in, remembered, switchable from the header. It sets default scope and document defaults, but never widens permissions | FR-PLT-019, TDD §6.3 |
| D3 | **"The system prepares, a named role confirms."** The GRN is drafted from gate or inspection data and confirmed by the storekeeper. The PV is drafted from GRN, PO and invoice and submitted by the finance officer. Each step is an installation setting: *confirm* (default) or *automatic* | ADR-0016, FR-INB-002/003/010 |
| D4 | **The PV captures invoices inline.** The data model keeps invoices separate, so duplicate detection, partial payments and matching still work | FR-AP-001/007, TDD §13.2 |
| D5 | **Storage hierarchy:** site → warehouse → storage unit → optional bins, with storekeepers, capacity, validated levels, allowed categories, restricted access and receipt/issue/transfer permissions | FR-MD-010, FR-INV-019, TDD §5 |
| D6 | **QC grading and scoring:** grade schemes, weighted score out of 100 (formula in FR-QC-023), grade-based price differentials with a defined combination order, optional parameters, explicit "Calculate quality" | FR-QC-013/022–024, TDD §11 |
| D7 | **GRN lines:** accepted and rejected quantity with disposition; one weighed line plus counted lines per truck; bag tare; manual weights with controls when the gate module is off | FR-PRC-013/021, TDD §10 |
| D8 | **Stock status DAMAGED** added. The stock balance report always reconciles: on-hand = Σ statuses; status changes never deduct twice | FR-INV-002/003/014, TDD §9.5 |
| D9 | **Sequencing (v0.3):** procure-to-pay end to end without inspection at the end of M3; with inspection (AS-13) and the gate loop at the end of M4 | Implementation plan §3 |
| D10 | **Scope moves:** RFQ comparison → Later (quotations are recorded on the PO); GRN PO-reassignment → v1.1; supplier self-registration link → v1.1 | FR-PRC-008/009/016/020 |
| D11 | **Departments and employee IDs** in the organisation model | FR-PLT-007, TDD §5 |
| D12 | **PO lifecycle** gains ISSUED. A PR link is required by default (as drawn) | FR-PRC-010 |
| D13 | **Status fields are action-driven,** never editable dropdowns | FR-PLT-031 |
| D14 | **Default number formats** follow the draft's style (§8), with the payment-request prefix changed to avoid a clash | FR-PLT-040, TDD §17.1 |
| D15 | **Inspection before unloading with the gate module off** creates the inspection lot from the draft's inspection screen, and the GRN is prepared from it | FR-QC-025, FRD §9.3 (flow C) |
| D16 | **Approval history is immutable; used masters are deactivate-only; date sanity checks** | FR-PLT-053/054/055 |

---

## 5. Issues found in the draft (fix before its second revision)

Items 1–20 are from the v0.2 review, re-verified against the PDF (amendments in *italics*). Items 21–40 were added by the v0.3 audit.

| # | Severity | Screen | Issue | Recommendation |
|---|---|---|---|---|
| 1 | High | Payment request | The request number `PR-2026-000129` uses the same prefix as purchase requisitions (`PR-2024-00123`) | Payment requests use `PV-` (payment voucher) |
| 2 | High | Supplier, PR, PO | Status is an editable dropdown (supplier Active/Inactive; PR Draft/Submitted/Approved/…; PO Draft/Issued/Pending/Canceled). *The PO status list also has no Approved or Rejected, although a PO approval dashboard exists* | Status is shown as a badge and changes only through actions |
| 3 | Medium | PR | "Urgent" appears as a status; it is a priority. "Confirmatted" is unclear | Lifecycle per FRD §1.5 |
| 4 | Medium | Supplier step 1 | "Date registered" is an input field | System-set when the record is created |
| 5 | Low | Supplier steps 4–5 | Step 5 repeats the bank account list from step 4. *Step 4 lists "Bank Account 2" twice; step 5 lists "Product Categories" twice* | Remove the duplicates |
| 6 | High | Inspection | Net weight is entered before receiving. Tare (the empty truck) is known only **after** unloading, unless a stored tare is used | Inspection shows gross and a *provisional* net; the GRN uses the final net after tare |
| 7 | Low | Inspection | Hectolitre weight is shown as kg/l | kg/hl |
| 8 | High | GRN | Net weight 20,000 kg, but the lines total 20,500 kg (wheat 19,800 accepted + 200 rejected + additive 500) | Weighed line = truck net − bag tare − nominal weight of counted lines; mismatches are flagged |
| 9 | High | Stock balance | Wheat row: opening 10,000 + received 19,800 − issued 2,500 ≠ closing 18,050. The rejection is subtracted twice (the GRN already splits 19,800 / 200, then "Inspection Rejection −200"). *Also:*<br>• *"Damaged Adjustment −50" double-counts the same way;*<br>• *the additive row gives 530, not 400; packaging gives 1,200, not 1,300;*<br>• *the totals card (24,480) disagrees with the rows (24,750) and with the formula (33,780);*<br>• *the summary's Adjustment column shows 0 while the history shows −250;*<br>• *kg and pcs are summed into "KG";*<br>• *closing excludes the rejected 200;*<br>• *an opening balance sits on a batch created by that day's GRN* | Rejection and damage are **status moves**, not deductions. Closing on-hand = opening + received − issued ± adjustments = Σ status columns; totals per unit of measure. The corrected example is the FRD FR-INV-014 fixture |
| 10 | Medium | Stock adjustment | "Approved by: Quality Inspector" is typed in; the button is "Save & Post Adjustment". *The summary panel doesn't reconcile (45,080 vs 24,080); "System Stock (Available)" shows the closing figure, not available; "Adjustmenty" is a duplicate field* | The approver comes from the approval matrix (warehouse authority, not QC). Posting follows approval, with auto-post only below a threshold. The count compares against on-hand across statuses |
| 11 | Medium | Payment request | "Requested amount" is typed separately from "Total amount to pay" | Derived from the invoice lines |
| 12 | Low | Payment request | The "PO Number" field shows a GRN number; invoice date "99-Aug-2026" | Mock-up typos |
| 13 | Low | PO | Line 200 × 4.50 shown as 990.00 (should be 900.00; the PO total already uses 900) | Mock-up arithmetic |
| 14 | Low | PO approvals | "Avg approval time 1.8ms" | Hours or days |
| 15 | Medium | Store master | Valuation method is set per store. *The silo master also carries a Stock_Valuation_Method* | Valuation is company-level; shown read-only |
| 16 | Info | Whole flow | No supplier-invoice step and no gate step. *Weighbridge data is captured twice with conflicting values: plate/gross/tare/net on the inspection (AB-12345, tare 2,500) and on the GRN (ET-12345, tare 5,000)* | Invoices are captured inline on the PV (D4). Weights are captured once (gate, or the inspection when the gate is off) and flow to the GRN |
| 17 | Info | Roles | The finance officer both raises and pays the request | Acceptable: an independent approver sits between them. A setting can require a different payer. The approver can never record the payment (SOD-15) |
| 18 | Question | GRN | Annotation "Unit ups one colument" | Read as: show the unit of measure once per line, in its own column. **Please confirm** |
| 19 | Question | Supplier payment | Annotation "Upplode depset slip" | Adopted: a proof-of-payment attachment is required before a payment posts. *For a cheque, the proof is usually a cheque copy, not a deposit slip* |
| 20 | Question | Sign-in | Branded "Factory ERP", with a "Finance & Accounts" tile. *The tiles show Inventory, Procurement, Production, Finance & Accounts and Reports, but no Sales, QC or Gate* | Is "Factory ERP" the product name? MOS has **no general ledger**, only a payables/receivables sub-ledger with journal export; please confirm. The tile set should include all six modules |
| 21 | Medium | PR | Required date 07/10/2024 is before the request date 07/18/2024; "Created Date: Jan 18 2024, 12:35:254 PMT" is malformed and doesn't match | Date sanity checks (FR-PLT-055) |
| 22 | Medium | PO vs PR | The PO date (07/10/2024) predates its PR; the PO line (MAT-A-002, 200 Each) doesn't match the linked PR line (MAT-P-001 polypropylene, 380 kg) | A PO line converts from a PR line with its item and quantity (FR-PRC-007/010) |
| 23 | Medium | PO approvals | Row 2's "PO Number" shows a PR number; row 1 shows DRAFT and PENDING together; approval rows have **delete icons** | Drafts never appear in approval queues; approval history cannot be deleted (FR-PLT-054) |
| 24 | Low | PR approvals | The same PR-2024-00123 appears as Pending, Approved, Approved and Rejected; the pending row has an approval date | One row per approval task; dates only after decisions |
| 25 | Medium | PR/PO vs payment | PR and PO amounts are in **USD ($)**; invoices, the payment request and the payment are in **ETB** | **Question:** are some POs in foreign currency and paid in ETB? v1 refuses an invoice in a different currency from its PO unless the client confirms (FRD §9.5 #33) |
| 26 | Medium | Payment | The approved request says **Bank Transfer**; the payment says **Check**. The payment's "Bank Account" shows the account number from the request's supplier bank details | A method change after approval requires re-approval; the paying account must be a company account (FR-AP-007/008) |
| 27 | Medium | Whole flow | The screens don't chain as one scenario: different trucks, tares, inspections, GRNs, and years 2024/2025/2026; invoices total 175,000 against a PO amount of 150,000 | The AS-13 fixture is one coherent dataset, to be confirmed with the client. Over-invoicing goes to hold (FR-AP-002) |
| 28 | Medium | Inspection vs GRN | The inspection says "Accepted" and has no rejected-quantity field; the GRN shows 200 kg rejected with inspection "PASSED" | The inspection records accepted and rejected quantity (partial accept), and the GRN takes both from it (FR-QC-012/024) |
| 29 | Low | Silo master | Safety stock 10,000 is above the minimum level 5,000; the silo has no allow-flags while store and warehouse do | Level validation (FR-MD-010); flags on every storage unit |
| 30 | Low | Warehouse master | The Capacity panel appears twice; "Metric Tons" is listed twice; "Close Warehouse" action | Remove the duplicates; closing = deactivation, refused while stock remains (FR-PLT-053) |
| 31 | Low | Numbering | Four PO formats (`PO-2024-00456`, `PO-2025-0010`, `PO-2026-000125`, `PO-2026-00045`), two GRN, two INS and two item-ID formats (`MAT000123`, `MAT-0001`) | One pattern per document type (§8) |
| 32 | Info | PR | The PR example is plastics (polypropylene resin, "new product line"), not flour | The examples should use the plant's real materials |
| 33 | Info | Stock | Bran is "received" and wheat is "Issued to Production Line 1": production already interacts with this flow | Production is in v1 (M5); bran is a by-product of milling runs (FR-MFG-003/015) |
| 34 | Question | Inspection | The meaning of "Document No." (DOC-2025-0001) is unclear: the supplier's delivery note? | Read as the supplier document number; **please confirm** |
| 35 | Question | Inspection | "Factory Name: Addis Flour Factory"; durum wheat | Confirms a **flour** mill; **please confirm** that "Floor Manufacturing" means flour |
| 36 | Question | Supplier registration | Titled "Supplier Portal – New Registration" | Staff enter suppliers in v1; supplier self-registration by link is v1.1. **Should suppliers register themselves?** |
| 37 | Info | Stock balance | "Reserved" column shown | Reservations are v1 (sales and production) |
| 38 | Info | Store master | A "Spare Parts" store type | MRO spare parts are an item type (FR-MD-003) |
| 39 | Info | Inspection | 450 bags = 225 quintals = 22,500 kg: wheat arrives in 50 kg bags | Bag count and bag tare on receipts; empty-bag handling (FR-PRC-021) |
| 40 | Info | Sign-in / whole flow | No sales, gate or QC screens yet | Client workshops for gate and production (before M4) and sales (before M5) |

---

## 6. Questions for the client

**Answered or partly answered by the draft** (to confirm, not to ask open-ended):

| # | Question | What the draft shows | Needed by |
|---|---|---|---|
| Q6 | Is a PO without a PR ever allowed? | The PO's "Purchase Requisition Number" is mandatory. **Confirm: never, or only for named categories?** | M2 |
| Q7 | Should the payer be a different person from the requester? | The same finance officer raises and pays. **Confirm that is intended.** | M3 |
| Q8 | Are invoices received before or after the goods, and at gross or accepted weight? | The narrative says "After the GRN is completed…"; the invoice dates follow the GRN date. **Confirm, and state the weight basis.** | M3 |

**Open questions** (the Phase 5 decision register carries them with owners):

| # | Question | Needed by |
|---|---|---|
| Q0 | Does "Floor Manufacturing" mean **flour**? (The draft names "Addis Flour Factory") | M1 |
| Q1 | **Product name:** "Factory ERP" (as on the sign-in mock-up), or something else? | M1 |
| Q9 | **Operation unit:** what does it represent: a plant (site), a legal company, or a department? The draft's "Factory Name" and "Plant/Factory Location" suggest a plant | M1 |
| Q11 | **Currency:** PRs and POs show USD, and invoices and payments show ETB. Are some purchases in foreign currency? How are they converted and paid? | M1 |
| Q5 | **PR approval:** who exactly is "Management"? Amount thresholds per approver | M1 |
| Q12 | **Suppliers:** should suppliers self-register (the screens say "Supplier Portal")? Are there individual suppliers (farmers) without a TIN? | M2 |
| Q13 | **Payment method:** can the method change after approval (the draft goes from bank transfer to cheque)? Which company accounts pay (banks, cash boxes, mobile money)? | M2 |
| Q14 | **Withholding and VAT:** is the company a withholding agent? The current rates and thresholds, including the no-TIN rate | M3 |
| Q4 | **Rejected quantity on the GRN** (200 kg in the example): unloaded and held, or left on the truck? What happens next: return, replacement, disposal? | M3 |
| Q15 | **Bags:** are empty bags returned to the supplier, kept, or credited? What is the bag tare? | M3 |
| Q10 | **Transfers:** "Allow transfer: conditional, approval required". Who approves transfers between stores? | M3 |
| Q2 | **Grading:** is the wheat grade decided by the worst parameter, or by score bands? Please share the actual grade table and score weights behind "92.4 / 100" | M4 |
| Q3 | **Price effect of grade/quality:** a price differential per grade, deductions per parameter (e.g. moisture above a base), or both? | M4 |
| Q16 | **Partial acceptance at inspection:** can one truck be partly accepted? Who decides: the inspector, or the QC manager? | M4 |
| Q17 | **Gate and production:** gate documents checked in and out, weighbridge details; products, milling stages, extraction rates, by-products, packing sizes, fortification. A workshop is needed | M4 |
| Q18 | **Sales:** customers, price lists, credit rules, delivery documents; **is a fiscal device / e-invoice legally required for sales invoices?** A workshop is needed | M5 |
| Q19 | **Hosting and infrastructure** (ADR-0019): servers, OS, network, backups, devices. Full list in the Phase 5 questionnaire | M5 |

---

## 7. Timeline effect

| | v0.1 plan | v0.2 plan (this review) | **v0.3 (ADR-0018)** |
|---|---|---|---|
| Scope of the first release | Procurement, inventory, QC, gate, AP | Same, plus the draft's additions | **All six modules** (production and sales added) |
| First release calendar | ≈ 40 weeks | ≈ 45 weeks (≈ 10.4 months) at 1.5 FTE | **≤ 26 weeks of development + ≈ 9 weeks to go live** (team path A/B/C) |
| Procure-to-pay usable end to end | End of MVP | Week 31 | **End of M3 without inspection; end of M4 with inspection (AS-13)** |
| Gate + automated inbound loop | Week 30 | Week 36 | **End of M4** |
| Full scope | ≈ 21–23 months | ≈ 22–24 months | v1 at ≈ 8 months; v1.1 and Later lists with no date |

v0.2's arithmetic was 59.5 dev-weeks ÷ 1.5 FTE × 1.15 buffer = 45.6 weeks, stated as "≈ 45 weeks / 10.4 months". That figure is now historical; the v0.3 hours come in planning Phase 4.

---

## 8. Default number formats (configurable)

`{FY}` is the fiscal year of the posting date; `{YYYY}` is the calendar year; `{YYYYMMDD}` is the calendar date.

| Document | Draft (as seen) | Default adopted | Gap-free? |
|---|---|---|---|
| Supplier | `SUP-00045` | `SUP-{SEQ:5}` | No |
| Item / material | `MAT000123`, `MAT-0001`, `RAW-WHT-001` | `MAT{SEQ:6}` system ID + user-defined code | No |
| Purchase requisition | `REQ-2024-00123` / `PR-2024-00123` (inconsistent) | `PR-{FY}-{SEQ:5}` | No |
| Purchase order | `PO-2024-00456`, `PO-2025-0010`, `PO-2026-000125`, `PO-2026-00045` | `PO-{FY}-{SEQ:5}` | No |
| Inspection | `INS-2025-0001`, `INS-2026-000078` | `INS-{FY}-{SEQ:6}` | No |
| Goods receipt | `GRN-2026-000001`, `GRN-2026-00078` | `GRN-{FY}-{SEQ:6}` | **Yes** |
| Batch / lot | `BATCH-20260813-01` | `BATCH-{YYYYMMDD}-{SEQ:2}` | No |
| Stock issue | `ISS-2026-00015` | `ISS-{FY}-{SEQ:5}` | **Yes** |
| Stock adjustment | `ADJ-2026-000046` | `ADJ-{FY}-{SEQ:6}` | **Yes** |
| Physical count | `PHY-COUNT-2026-00012` | `CNT-{FY}-{SEQ:5}` | No |
| Payment request | `PR-2026-000129` (clash) | **`PV-{FY}-{SEQ:6}`**, numbered at submission; cancelled numbers kept | **Yes** |
| Payment | `PAY-2026-000156` | `PAY-{FY}-{SEQ:6}` | **Yes** |
| Approval record | `AP-2024-001` | Internal only (approval chain id); not a user-facing number | — |

The formats for documents not in the draft (weigh tickets, work orders, sales orders, delivery orders, customer invoices, receipts, credit notes) are listed in TDD §17.1. The gap-free list is FRD FR-PLT-041.
