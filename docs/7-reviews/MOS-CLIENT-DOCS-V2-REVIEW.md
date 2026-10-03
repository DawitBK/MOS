# MOS: Review of the client's second document set, and how we integrate it

<!-- nav -->[MOS](../../README.md) › [7. Reviews](README.md)

| | |
|---|---|
| Sources reviewed | Three documents received from the stakeholders on 2026-10-01/02, copied to [docs/9-inputs/from-client/](../9-inputs/from-client/):<br>• **P2P process v2**: `P2P-process-v2.pdf` (+ `.docx`), 25 pages: the narrative and 28 screen images on pages 2–25 (27 distinct; re-check §3.6)<br>• **P2P data dictionary**: `P2P-data-dictionary.pdf` (+ `.docx`), 17 pages (2 blank), 32 tables<br>• **Architecture proposal**: `ERP-architecture-proposal.pdf` (+ `.docx`), 22 pages: architecture, stack, folder structure, module communication |
| Compared against | Planning package v0.3: [FRD](../2-requirements/MOS-FRD.md) 0.3.3, [database design](../3-design/MOS-DATABASE-DESIGN.md) 0.1.1, [TDD](../3-design/MOS-TDD.md) 0.3.3, [ADRs](../6-decisions/adr/) 0001–0022, [implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) v0.4, [decision register](../6-decisions/MOS-DECISION-REGISTER.md) 0.1, and the [first-draft review](MOS-CLIENT-P2P-DRAFT-REVIEW.md) |
| Date | 2026-10-02; **re-checked 2026-10-03**: a second pass, page by page and column by column. Corrections are marked "re-check"; the additions are §3.6, §4.5 and §5.4 |
| Audience | Product owner. §8 lists the questions for the client; they go into the questionnaire once the owner has reviewed them |
| Status | **Partly applied (2026-10-03, six-month re-plan):** the §7.3 effort is counted in the [implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) 0.5 (Appendix A), the [sprint plan](../4-plan/MOS-SPRINT-PLAN.md) and the [Module 1 plan](../4-plan/MOS-PROCUREMENT-MODULE-PLAN.md); the §8 questions and the §7.5 decisions are in the [decision register](../6-decisions/MOS-DECISION-REGISTER.md) 0.2 and the questionnaire 0.2. **Still waiting for the owner's decisions in §7.5:** the FRD text (new FR IDs), the database design and the TDD changes of §7.2 |

---

## 1. Summary

**What arrived.**
- **P2P v2** is the procure-to-pay draft again.
  - Most first-draft screens are reused unchanged.
  - Nine screens are new: supplier delivery entry; bin card and stock card; goods transfer; physical count, count review and posting the adjustment; bin master; supplier invoice registration.
  - Five are redrawn: inspection, receiving, the payment request (now raised by the supplier), its approval (a two-page review), and the payment.
- **The data dictionary** is new: the client's own table design for procure-to-pay and inventory.
- **The architecture proposal** is new: a generic React + Vite / Express + Sequelize / PostgreSQL design for "eight modules".

**Verdict.**
1. **The spine is unchanged and still matches MOS:** PR → approval → PO → delivery → inspection → GRN → stock → invoice → payment request → approval → payment.
   - **Every v2 screen maps to an existing MOS requirement, except four** (§3.4).
   - **Of the client's 32 tables, 30 map to existing MOS tables.** The exception is the supplier-delivery pair, header and line (§4.4).
2. **The screens are illustrations, not specifications.**
   - Their sample data doesn't reconcile on almost any screen (§3.3).
   - **At least 22 of the 40 first-draft defects are still present, two of them worse** (§3.2, corrected by the re-check in §3.6). Two are fixed and two partly fixed.
   - We take the *fields and the flow* from them, never the numbers or the status lists.
3. **The data dictionary is a useful field list but not a buildable schema** (§4).
   - It has 25 defects, 7 of them critical. For example:
     - delivery lines have no link to their delivery;
     - no document line links to the line it came from, so three-way matching can't be enforced;
     - there are no QC tables at all;
     - the stock ledger is editable and keeps an unlocked running balance;
     - timestamps default to `CURRENT_TIME`.
   - **We keep our database design and adopt the client's fields:** 14 small field additions (§4.4); **27 after the 2026-10-03 re-check** (§4.5).
4. **The architecture proposal mostly agrees with ours** (§5).
   - Of 39 compared decisions:
     - **18 already agree;**
     - **16 we keep ours, with a reason;**
     - **2 we adopt from the client** (their module names and React Router; **3** with their sub-folder names, re-check §5.4);
     - **3 I recommend updating:**
       - the frontend becomes a **Vite single-page app** instead of Next.js;
       - the queue becomes **pg-boss on PostgreSQL** instead of BullMQ + Redis;
       - **OpenAPI documentation** is generated now instead of deferred.
   - The first two reverse owner decisions A-37 and A-35, so they wait for you (§7.5).
5. **Four things in the new documents could change scope.** Each is a question for the client (§8):
   - **(a) Suppliers raising their own payment requests.** Page 22 is a supplier-portal screen; page 1 says the finance officer raises it.
   - **(b) Inspection as plain pass/fail.** v2 has no grades, score or deductions; v1 had a grade and a 92.4/100 score (deductions were never shown, only asked about; re-check).
   - **(c) No gate module and no weighbridge weights anywhere in v2.**
   - **(d) Approval of every supplier invoice.**

**Effect on the plan (indicative; re-estimated when the plan is integrated, §7.3).**
- **Changes we adopt regardless:** +41 core hours and +30 standard hours; after the 2026-10-03 re-check, **+69 core and +16 standard** (§7.3, INT-15…INT-18).
- **The two stack updates:** −10 core hours.
- **Net:** **+31 core hours (+1.3% of 2,380)**; after the re-check, **+59 core hours (+2.5%)**.
  - The price doesn't change: each path's price is its team's capacity in the window (ADR-0020).
  - In the 26 + 9-week frame, the required velocity on path A rises from ×1.32 to about **×1.35** (B ×1.56, C ×2.56). **Recomputed in the six-month re-plan (2026-10-03, [ADR-0025](../6-decisions/adr/ADR-0025-everything-in-six-months.md)):** the additions are now counted (core 2,439 h), and the speeds needed are A+ ×1.33, A ×1.89, B ×2.22 ([implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) §5).
- **Answers that would move scope a lot:**
  - pass/fail-only inspection: up to −42 core hours;
  - no gate module: up to about −200 core hours;
  - a supplier portal in v1: +140 to +200 hours. I recommend treating it as a separate change request.

**Done in this pass:**
- The six files were copied to `9-inputs/from-client/` with a README.
- An incidental finding in our own ADR-0022 was corrected (§6).

Nothing else was changed.

---

## 2. What the three documents are

| Document | Nature | Evidence |
|---|---|---|
| P2P process v2 | The client's draft, second revision. The narrative (page 1) is word for word the first draft's; the nine new screens are not described in it | Same narrative; the PR, PO, dashboards, sign-in, supplier wizard and the item, warehouse, store and silo masters carry the first draft's sample values and defects (§3.2) |
| P2P data dictionary | The client's table design for procurement, inventory and payables. QC, production, sales and receivables are absent, apart from a production material-issue table | Tables follow the screens; section numbering restarts several times; pages 10 and 15 are blank |
| Architecture proposal | **Most likely written with an AI assistant for the client** (as the owner's brainstorm was). It is generic: it addresses "your flour factory" and "your eight main modules", has no weighbridge, gate, lots or silos in its design, and its "Development order" section names no order | **A find-and-replace artefact:** "All Operating units **es**", "operating units **es**", "operating units_id". "Branch**es**" was replaced with "operating units", which leaves a stray "es". **The client's "operation unit" was therefore a branch.** This is new evidence for C-03 (§8) |

How we treat them (the MOS rule "client documents are data"):
- They are **requirements evidence**: what the client wants to see and capture.
- They are **not** our design. Where they disagree with an MOS decision, the decision stands unless §5 recommends changing it, and the client is told why (§8).

---

## 3. P2P process v2: audit

### 3.1 What changed since the first draft

| Area | First draft | v2 |
|---|---|---|
| Narrative | Nine steps, seven roles | **Unchanged.** It still omits the delivery entry, invoice registration, PO approval and the count process that v2 now draws |
| Supplier wizard | 5 steps | Steps 1–4 shown; step 5 (products and services) is no longer included |
| Inbound | Inspection with gross/tare/net, grades and a 92.4/100 score; GRN with weighbridge net | **New "Supplier delivery entry"** (procurement officer). The inspection is **pass/fail per parameter**: a failed result rejects the delivered wheat ("Reject & Return"; 2 of 9 parameters failed in the example; re-check). **No weighbridge weights**: no gross, tare, net, weigh ticket or bag tare (quantities in quintals and an "accepted quantity (weight)" column remain; re-check) |
| Stock views | One stock-balance screen | **Bin card** (one bin) and **stock card** (one item in one warehouse), each with a running balance |
| Stock documents | Stock adjustment | **Goods transfer**, **physical count → review → post adjustment**, bin master |
| Payables | Payment request (finance officer) → approval → payment | **Supplier invoice registration** with three-way match and its own approval (DRAFT → SUBMITTED → APPROVED → POSTED); **payment request raised by the supplier's account manager**; approver review with document verification; payment execution with partial payment of several invoices |

### 3.2 First-draft defects: status in v2

Issue numbers refer to the [first-draft review](MOS-CLIENT-P2P-DRAFT-REVIEW.md) §5.

| # | Defect (short) | v2 status |
|---|---|---|
| 1 | Payment request prefix `PR-` clashes with requisitions | **Partly fixed.** The request and the review use `PRQ-`, but the payment screen still links "PR-2026-000129", and page 23 is headed "Approver takes action for the above **PR**" |
| 2 | Status is an editable dropdown | **Still present** on supplier, PR and PO, and **new** on supplier delivery |
| 3 | "Urgent" as a status; "Confirmatted" | Still present |
| 4 | "Date registered" typed by the user | Still present |
| 5 | Duplicate list entries | Still present ("Bank Account 2" twice) |
| 6 | Net weight before tare | Not applicable: **weights were removed altogether** (see N-05) |
| 7 | Hectolitre weight in kg/l | Still present (unit column "kg/l"; the standard says "≥ 78 kg/hl") |
| 13 | PO line 200 × 4.50 = 990.00 | Still present |
| 14 | "Avg approval time 1.8ms" | Still present |
| 15 | Valuation method per store and silo | Still present, in the screens and the data dictionary |
| 16 | No supplier-invoice step | **Fixed** (page 21) |
| 20 | "Factory ERP" branding; tiles without Sales, QC or Gate | Still present |
| 21 | Required date before request date; malformed created date | Still present |
| 22 | PO date before its PR; PO line differs from the PR line | Still present |
| 23 | PR number in the PO column; DRAFT and PENDING together; delete icons on approvals | Still present |
| 24 | One PR shown as pending, approved and rejected | Still present |
| 25 | PR/PO in USD, invoices and payments in ETB | Still present |
| 27 | Screens don't chain as one scenario | **Worse**, see §3.3: years 2024, 2025 and 2026; the same truck carries 25 t, 50 t and 500 t of wheat on three screens |
| 29 | Silo safety stock above the minimum level | Still present |
| 30 | Duplicate capacity panel; "Metric Tons" twice | Still present |
| 31 | Several number formats per document type | **Worse**: `PO-2026-0087`, `PO-2026-000045`, `PRQ-000001`, `PC-2025-09-001`, lots `LG2026001` |
| 36 | "Supplier Portal" registration | Still present, and **extended** to supplier-raised payment requests (N-24) |

Items 8–12, 26 and 28 were about first-draft screens that v2 replaced. Their replacements show the same kind of defect: mixed-unit totals, stock that doesn't reconcile, amounts typed rather than derived, and transfer data with cheque numbers (§3.3).

### 3.3 New findings on the v2 screens

| # | Sev. | Page / screen | Finding | MOS position |
|---|---|---|---|---|
| N-01 | High | 7 Delivery | "Total preliminary quantity 48,500.000" adds 25 + 10 + 5 + 8 **MT** and 500 **KG** into one number | Totals per unit of measure (FR-RPT-005) |
| N-02 | Medium | 7 Delivery | The supplier is chosen independently of the PO; an "Other (Specify)" free-text item appears on a PO delivery | The supplier and the lines come from the PO; an item not on the PO is refused or needs a PO change |
| N-03 | Medium | 7 Delivery | "Wheat Grain (Grade 1)" is an item name | Grade is a QC result on the lot (FR-QC-022), not part of the item |
| N-04 | Medium | 7 Delivery | Save / Edit / **Reject / Approve** by the procurement officer, with no statement of what is approved | Read as "the delivery matches an open PO; send it to inspection". **Question C-41** |
| N-05 | **High** | 7–9 | **No weighbridge data in the whole flow.** The payable quantity basis is unclear: delivery note, bag count, or a weight taken somewhere else? (v1 had gross, tare and net) | MOS designs weights at the gate or typed on the inspection (FR-QC-025). **Question C-38** |
| N-06 | High | 8 Inspection | **A failed result rejects the delivered wheat** ("Please return the item"; 2 of 9 parameters failed; whether one failure always rejects is not stated: re-check); no grade, score, deduction, concession or partial acceptance | The MOS engine covers pass/fail as its simplest setting. **Questions C-25…C-27 (updated)**; if confirmed, about 42 core hours move to the pull-in list |
| N-07 | Medium | 8 Inspection | "Accept & Receive" is enabled although the result is **Rejected** | Acceptance after a failure only through a concession (FR-QC-011) |
| N-08 | Medium | 7 vs 8 vs 9 | One truck (DN-2026-0345 / DN-2026-0045) is 25 t of wheat at delivery, 500 quintals (50 t) at inspection, and 500 MT at receiving | The acceptance scenario uses one coherent dataset (C-28) |
| N-09 | **High** | 9 GRN | **The wheat rejected at inspection is fully accepted** (500 MT accepted, 0 rejected, "Ready for GRN"; the GRN cites DN-2026-0045, pages 7–8 DN-2026-0345) | No GRN line for a rejected lot; rejected quantity is RETURNED_ON_TRUCK or HELD_AS_REJECTED (FR-PRC-013) |
| N-10 | Medium | 9 GRN | One "Rejected/Short Quantity" column merges **rejected** (delivered, refused) with **short** (never delivered) | They differ: a short isn't stock and isn't paid; a rejection is returned or held. **Question C-43** |
| N-11 | Medium | 9 GRN | One bin (B01) on the header for five materials | Storage unit per line (as the data dictionary already has) |
| N-12 | Low | 9 GRN | "50 bags = 500 MT" (10 t per bag); the ordered total of 930.00 MT includes 200 KG | Bag count and kg per bag from the item conversion (FR-MD-002); totals per unit |
| N-13 | High | 10 Bin card | Summary cards (opening 5,000, issued 3,200, current 11,800) disagree with the rows: the rows start from 0, issue 2,000, include a 3,000 transfer-in the cards omit, and end at 10,800 | Both views are built from the one ledger, so they always agree (adopted as FR-INV-023) |
| N-14 | Medium | 10 Bin card | "Quality rejection" posted as an adjustment of −200 | A rejection is a status change, not a quantity loss (v1 issue 9 again) |
| N-15 | High | 10 vs 11 | The same documents carry different quantities and dates on the bin card and the stock card (GRN-000123: 5,000 vs 10,000; ISS-000045: 2,000 vs 5,000) | As N-13 |
| N-16 | Medium | 11 Stock card | A bin-to-bin move inside one warehouse ("Transfer Out" 3,000) lowers the warehouse balance; the closing row (20,000) ≠ current stock (45,000) | An internal move is net zero at warehouse level (paired entries, TDD §9) |
| N-17 | Medium | 12 Transfer | Wheat, premix and empty bags all leave the **wheat** bin and enter a **finished-goods** bin, "for production" | Material for a milling run is **issued to the work order** (or moved to a production staging unit), not transferred into finished goods. **Question C-44** |
| N-18 | Low | 12 Transfer | No approval step, although the warehouse master says "Allow transfer: conditional, approval required"; "3,500 (KG/PCS)" total | `transfer_requires_approval` per storage unit (FR-MD-010); totals per unit |
| N-19 | High | 13 Count | Totals are wrong and mix units: the lines give 26,700 system and 26,680 physical, but 45,200 and 45,180 are shown "KG". "Matched 3 / Variance 2" against lines showing 2 matched and 3 variances | Computed totals per unit |
| N-20 | Medium | 13 Count | Counters see the system quantity; bins from outside the selected storage area are counted; WHT-001 and WHT-002 are both "Wheat" | Blind count by default (FR-INV-009); a count covers the selected units. **Question C-45** |
| N-21 | High | 14–15 | Review shows physical 48,700 and +3,500 for the same lines. The posted adjustment is "Stock Increase +3,500", but its lines give +200 and **set the −200 shortage to 0**, and leave out the −20 bags | **The adjustment equals the approved count;** a different figure needs a recount (FR-INV-009). Damaged bags are a status change. **Question C-46** |
| N-22 | Medium | 21 Invoice | "Amount due 441,000" is the net before VAT, while the invoice total is 507,150. WHT is 0.00 on a 441,000 ETB goods purchase | The amount due = gross − withholding (FR-AP-013). **C-17 (updated)** |
| N-23 | Medium | 21 Invoice | Its own approval workflow (DRAFT → SUBMITTED → APPROVED → POSTED), on top of the payment-request approval | MOS approves invoices only on a match exception (FR-AP-004). Adopted as an **option** (FR-AP-020). **Question C-42** |
| N-24 | **High** | 22 Payment request | **Raised by the supplier** ("Supplier Account Manager, ABC Trading PLC"; "submit and manage your payment requests"). The supplier picks the bank account and uploads the WHT certificate. Page 1 says the finance officer raises it | Three problems:<br>• it contradicts the narrative;<br>• supplier-chosen bank details are the main payment-fraud risk (FR-PRC-004: bank details come only from the verified master);<br>• the WHT certificate is issued *by the buyer*.<br>A supplier portal is Later (FR-PRC-019). **Question C-39** |
| N-25 | High | 23 Review | The match grid shows invoice quantity 9,600 against GRN quantity 9,800 with "Qty Diff 0.00 ✓"; the totals row shows unit prices 441.00 / 65.00; references are swapped (PO ref = INV-45872; GRN ref "GRI-…") | Match results are computed, never typed (FR-AP-002) |
| N-26 | Low | 23–24 | **Page 24 is a second version of page 23, not a copy** (corrected in the re-check). It has no queue panel, adds "Current Review State: Verification Complete" and Check / Item ID columns, and shows invoice quantity 9,800. Approve/Reject appear three times | Mock-up; N-25's 9,600 applies to page 23 only |
| N-27 | Medium | 25 Payment | A bank transfer carries "Transaction ref / Cheque No CHQ-000123"; it pays "PR-2026-000129 – Office Supplies" against a wheat PO; the payment (13-Aug) precedes its request (01-Oct) | Cheque number only for cheques (FR-AP-012); the paid PV is the approved one; date checks (FR-PLT-055) |
| N-28 | Info | 25 Payment | Methods include "Other" | **C-16 (updated):** what is "Other"? |
| N-29 | Good | 8, 14, 21–23, 25 | Worth adopting:<br>• "Analyzed by" + "Checked by" on inspection;<br>• count review with request-changes;<br>• attachment slots per document type;<br>• the approver's queue panel;<br>• document "Verify" on review;<br>• partial payment across several invoices;<br>• deposit slip required | Adopted where new (§3.4); the rest already exist in MOS |

### 3.4 v2 screens mapped to MOS

| v2 page | Screen | MOS requirement | Result |
|---|---|---|---|
| 2 | Sign-in with operation unit and SSO | FR-PLT-010, 018, 019 | Covered (operation unit picked after sign-in, decision D2) |
| 3–4 | Supplier wizard, steps 1–4 | FR-PRC-001, FR-MD-012/013/014 | Covered |
| 5 | PR and approvals dashboard | FR-PRC-007, FR-PLT-030/032 | Covered |
| 6 | PO and approvals dashboard | FR-PRC-010, FR-RPT-001 | Covered |
| 7 | **Supplier delivery entry** | Gate visit (FR-GATE-005…008) with the gate on; nothing with the gate off | **Gap → FR-PRC-023** (§7.2) |
| 8 | QC inspection | FR-QC-024, 025, 009, 012 | Covered; "checked by" added (FR-QC-012 amended) |
| 9 | Goods receiving | FR-PRC-013, 021 | Covered |
| 10 | **Bin card** | FR-INV-014 (balance + history) | **Gap (the layout) → FR-INV-023** |
| 11 | **Stock card** | FR-INV-014 | **Gap (the layout) → FR-INV-024** |
| 12 | Goods transfer | FR-INV-007 | Covered |
| 13–15 | Count, review, post adjustment | FR-INV-009, 008 | Covered; Excel import of counts and a printable count sheet added (FR-INV-009 amended) |
| 16–20 | Item, warehouse, store, silo and bin masters | FR-MD-003, 010 | Covered; 6 fields added (FR-MD-010 amended, §4.4) |
| 21 | Supplier invoice | FR-AP-001, 002 | Covered; **optional invoice approval → FR-AP-020** |
| 22 | Supplier-side payment request | FR-AP-007 (finance-raised) | **Not adopted for v1** (N-24; C-39) |
| 23–24 | Payment request review | FR-AP-007, FR-PLT-025 | Covered |
| 25 | Payment execution | FR-AP-008, 012, 015 | Covered |

### 3.5 The narrative (page 1)

- The narrative was not updated for v2. It still lacks four steps the screens draw:
  - the delivery entry;
  - PO approval (a PO approval dashboard exists);
  - invoice registration and its approval;
  - the count cycle.
- It contradicts page 22: the narrative has the finance officer raising the payment request; page 22 has the supplier raising it.
- It still has no rejection path: what happens to a failed delivery.
- **Recommendation to the client:** a third revision of the narrative that lists every screen in order, with its role.

---

### 3.6 Re-check (2026-10-03): page-by-page corrections and additions

All 25 pages were looked at again, with pages 23 and 24 zoomed in and their native DOCX images checked.
- **The DOCX and the PDF are identical:** 28 images in the same order, with no crops, hidden text, comments or tracked changes.
- **Against the first draft:**
  - 13 of the 28 images are byte-for-byte the first draft's.
  - The step-5 screen of the supplier wizard was dropped, but step 5 is still listed in the sidebar of steps 1–4.

**Corrections to this review:**

| Where | Was | Correct |
|---|---|---|
| Header, §1 | "24 screen mock-ups" | **28 screen images on pages 2–25.** 27 are distinct: pages 23 and 24 are two versions of one screen |
| §1, §3.2 | "19 of the 40 first-draft defects still present" | **At least 22 are still present.** Items 17–19 and 32–40 weren't tracked:<br>• **still present:** #32 (plastics PR, same image), #34 (Document No., now DOC-2026-0012, still unexplained), #38 (Spare Parts store type, same image);<br>• **fixed:** #19 (the deposit slip is required on page 25);<br>• **half-fixed:** #18 (the note is gone, but the GRN repeats the unit in every quantity cell and has no unit column);<br>• **evidence removed:** #35 ("Addis Flour Factory");<br>• **changed:** #17 (the supplier raises the request, the Finance Manager approves, the Finance Officer pays) and #33 (bran is now bought on the PO; C-24) |
| N-01 | An arbitrary sum | 48,500 is the correct total *in kilograms* (48 MT + 500 kg), shown without a unit |
| N-26 | Page 24 repeats page 23 | Fixed in the table above |
| N-28 | Methods include "Other" | Also "Cash Payment"; the narrative names only cheque and bank transfer |

**New findings:**

| # | Sev. | Page | Finding | MOS position |
|---|---|---|---|---|
| N-30 | **High** | 7–9 | **One person, Yenemengist Demissie, is the Procurement Officer (page 7), the QC Officer (page 8) and the Receiving Officer (page 9).** This may be a mock-up artefact | Separation of duties: the SoD rules stop one user from ordering, inspecting and receiving the same delivery (FR-PLT-028). Raise it with the client |
| N-31 | High | 8 | **Only wheat is inspected** ("Type of Wheat", "Quantity of Wheat"). Soybean, bran, maize and "Other" on the same delivery are received with no inspection | The item master's "Inspection Require" flag decides it per item. **New client question:** which raw materials are inspected, and against which specifications? The FRD has none for maize or soybean |
| N-32 | Medium | 21, 22, 25 | **The invoice is numbered three ways:** INV-000078, INV-2026-000078 and INV-450078. Page 25 shows it as 150,000, not 507,150, and pays 150,000 against an approved request of 507,150. A second invoice, INV-2026-000068, sits on the same GRN | One number per invoice. A partial payment is shown as partial against the approved amount |
| N-33 | Medium | 21–25 | The invoice (09-Aug) and the payment and its approval (13-Aug) are dated **before** the delivery and GRN (25-Sep). The invoice and payment pages cite PO-2026-000045; delivery and GRN cite PO-2026-0087 | Date-order checks (FR-PLT-055); one PO per chain in the acceptance data (C-28) |
| N-34 | Medium | 22 | The same invoice is listed twice. Amount due is 507,150 here but 441,000 on page 21 (adds to N-22). The requested amount is typed (first-draft #11 again). The terms panel appears twice | The requested amount is derived from the open items |
| N-35 | Medium | 23–24 | The review shows PO quantity 9,800, but the page 21 match says 10,000 kg. The "Invoice Quantity" box holds GRN-2026-000077. The Account Number field shows "ABC Trading PLC". The wheat line appears twice, but the totals aren't doubled. Queue entries are malformed: one has no PRQ number, one reads "01-Oct-20 ETB" | Match results are computed (FR-AP-002) |
| N-36 | Medium | 11 | **Row 8 is wrong:** 19,500 − 5,000 is shown as 20,000; it should be 14,500 (adds to N-16) | One ledger (FR-INV-023/024) |
| N-37 | Medium | 10–11 | Adds to N-15: TRF-000067 is "Transfer In from WHT-02, 09-15" on the bin card but "Transfer Out to WHT-03, 09-10" on the stock card. ADJ-000012 is 200, lot WHT-2025-003, "Quality rejection" on one and 500, lot WHT-2025-002, "Quality adjustment" on the other | As N-13 |
| N-38 | Medium | 7 vs 9 | Adds to N-08: every line disagrees between delivery and GRN, not just wheat. Soybean 10 vs 100, bran 5 vs 48, maize 8 vs 80, "Other" 500 kg vs 200 kg | One coherent dataset (C-28) |
| N-39 | Low | 15 | Adds to N-21: the adjustment doesn't reference count PC-2025-09-001, and three different roles review, approve and post it | The adjustment is generated from the approved count and links to it |
| N-40 | Info | 1st draft → v2 | The first draft's heading "Finance Officer Request Payment **on behalf of Supplier**" became "Supplier Payment Request". The move to supplier-raised requests (N-24) looks deliberate | Strengthens C-39 |

**Gaps against the FRD (not in §7.2 either):**
- **Typed attachment slots per document**, such as supplier invoice, tax document, delivery note and WHT certificate, each with a "Verify" status. N-29 says they are adopted, but no requirement carries them. **→ Amend FR-PLT-044** (attachments) when the FRD is integrated: about +6 core hours, under INT-04.
- **Attestation text with an "I agree" checkbox** for the submitter and the approver (pages 22–23). **→ Amend FR-PLT-025** (approver actions), about +2 hours.
- **QC specifications for maize and soybean.** Neither word appears in the FRD; N-31 asks the client.

---

## 4. Data dictionary: audit and mapping

### 4.1 Assessment

**Good, and already the same in MOS:**
- the header/line pattern on every document;
- `NUMERIC(18,3/4)` quantities;
- explicit `posted_by` / `posted_at`;
- a status on most documents;
- approval levels on POs;
- lot and bin on stock movements;
- a supplier delivery separate from the GRN;
- a transaction header separate from ledger lines;
- an operating-unit column.

**Not buildable as written.** The defects below would become data-integrity bugs. None of them affects MOS, whose design already avoids them; they are listed so the client can see why we don't build their tables one to one.

### 4.2 Defects

| # | Sev. | Table(s) | Defect | Consequence if built as written |
|---|---|---|---|---|
| D-01 | **Critical** | Supplier delivery line | No `supplier_delivery_id` (no link to its header) and no PO-line link | Lines can't be tied to a delivery or a PO line |
| D-02 | **Critical** | — | **No QC tables at all** (parameters, specs, inspections, results, decisions), though inspection is a P2P step with its own screen | Inspection results can't be stored |
| D-03 | **Critical** | PO line, GRN line, invoice header and lines | No line-level lineage: no PR-line link on the PO line, no PO-line link on the GRN line, no PO- or GRN-line link on the invoice line. The invoice's PO, GRN and delivery note are **free text** | Three-way match, open quantities, over-receipt and duplicate invoicing can't be enforced |
| D-04 | **Critical** | Many | Referenced tables are never defined:<br>• unit of measure, item category, supplier type, currency, payment and delivery terms;<br>• department, employee/user, operating unit, plant, storage location;<br>• batch/lot, bin type, approval, production order | Foreign keys with no target |
| D-05 | **Critical** | Inventory transactions, stock ledger | The ledger has `Updated_at`/`Updated_by` (it is editable), a DRAFT status on its header, and a stored running `balance_qty` with no locking rule | Concurrent postings corrupt balances; history can be rewritten |
| D-06 | **Critical** | Transfer header | The transfer header has one `warehouse_id`. (Stock-ledger lines carry their own warehouse, location and bin, so on the transaction header the column is only redundant: corrected in the 2026-10-03 re-check) | A transfer between warehouses (page 12 does exactly that) can't be recorded |
| D-07 | High | Silo master, ledger | Silos are a separate master with no silo key in the ledger or bins; `current_level` and `current_capacity` are stored | Silo stock can't be posted; stored levels drift from the ledger |
| D-08 | **Critical** | Payment execution | One payment request number as text, while the screen pays several invoices. Also:<br>• no allocation table;<br>• `payment_method_id`, `currency_id` and `bank_account_id` are text;<br>• no status or void;<br>• no cheque number;<br>• `remaining_amount` stored <br>• currency is held three ways: `currency VARCHAR(3)` (invoice), `currency_id BIGINT` (payment request, bank account), `currency_id VARCHAR(3)` (execution); execution links to the request by its text number; the request's `approval_status` includes PAID | Payments can't be allocated, voided or reconciled |
| D-09 | High | Payment request | The "Null" column says "Yes" for required fields (inverted meaning); the invoice link is text; `attachment_path` is 100 characters | Ambiguous constraints |
| D-10 | High | Inventory adjustment | One material per adjustment (on the header), although the screen has lines; no warehouse, no unit of measure, no value; reason as free text | A count can't post as one adjustment; no valuation |
| D-11 | High | Material issue header and line | No warehouse or storage location on the header; no warehouse, location or bin on the line. The line's `batch_id` is NOT NULL, but a draw from a commingled silo has no single lot (MOS: `mfg.wo_issue_line.lot_id` is nullable) | The issue can't decrement a location; silo draws can't be recorded |
| D-12 | High | Supplier invoice | No unique (supplier, invoice number). `invoice_no` is "the supplier's number" while the screen has a system number plus a supplier reference. No due date, terms, tax totals or WHT. Three fixed attachment columns | Duplicate payment of one invoice |
| D-13 | High | PO header | No currency or exchange rate, though the PR and PO screens are in USD | Foreign-currency POs unrecordable (C-04) |
| D-14 | High | Store, ledger | Valuation method per **store** (`stock_valuation_method`, "FIFO or Moving Average"); the silo master has none (corrected in the re-check); ledger `unit_cost NUMERIC(18,2)` | Inconsistent item cost; rounding drift in the per-kg moving average |
| D-15 | **Critical** (raised in the re-check) | PR approval, PO approval, invoice, payment request | **"PR Approval Information" has no link to the PR** (no `material_request_id`): the same kind of defect as D-01. One approval table per document type (PR approval has no data types); the payment request's `approval_id` and the "approval workflow record" point to an undefined Approval table | Approvals can't be tied to their document; three approval engines to build and keep consistent |
| D-16 | Medium | All | `operating_unit_id` is INT in nine tables (supplier, PR header, PO header, PO approval, store, silo, bin, item, the warehouse's second column) and BIGINT elsewhere, and appears twice in the warehouse master. It is spelled four ways: `Operatining_unit_id` (invoice header), `Oprating_unit_id` (adjustment), `operating_units_id` (payment request and execution), "Operating unit id" (PR approval). Only line and child tables (such as GRN lines) lack it. Masters such as supplier and item carry it, but the architecture proposal says masters may be shared | Scoping unclear (C-03) |
| D-17 | High | All | Mixed conventions:<br>• BIGINT identity and BIGSERIAL;<br>• TIMESTAMP and TIMESTAMPTZ;<br>• `created_at` and `created_date`;<br>• `created_by` BIGINT but `Updated_by` TEXT;<br>• `uom` text and `uom_id`;<br>• `material_id` and `item_id` for the same thing;<br>• columns listed twice.<br>**Defaults of `CURRENT_TIME` on TIMESTAMP columns:** `CURRENT_TIME` is a time of day, not a timestamp | Wrong or failing defaults; joins on mismatched types |
| D-18 | Medium | PR, PO, invoice, delivery | Status lists differ from the screens (PR "Fulfilled" vs "Confirmatted"; PO "Sent" vs "Issued"; invoice PENDING/APPROVED vs DRAFT/SUBMITTED/POSTED); the delivery default 'RECEIVED' isn't in its own list | Lifecycle undefined |
| D-19 | Medium | Many | Derived values stored without a rule for who maintains them: estimated total, line total, available stock, available quantity, variance, balance, remaining amount, current capacity and level | Values drift |
| D-20 | Medium | — | No audit log, attachments, number series, fiscal periods or reversal model (cancel only) | Controls the client's own architecture proposal asks for ("auditable record") have nowhere to live |
| D-21 | High | GRN line | No unit price or value; no reason or disposition for the rejected quantity; `ordered_quantity` is a stored copy. (Rejected and short **are** separable here: ordered, delivered, accepted and rejected are separate columns. The page 9 *screen* merges them, N-10: corrected in the re-check) | The receipt can't be valued; rejected stock has no destination |
| D-22 | Medium | Physical count | No cut-off or freeze time; no recount | The system quantity's moment is undefined |
| D-23 | Low | Document | Section numbering restarts (two section 7s and two section 8s; Inventory has no section 5; "2. Item Master" after "4. Bin Master"); blank pages 10 and 15; typos ("Finical", "**Row** Inventory Transactions", "Crated_by"). Tables have captions only, no SQL names; FK descriptions use inconsistent target names (`grns`, `purchase_orders`, `item_master` …); no constraints, unique keys or indexes are declared | Readability; physical names undefined |
| D-24 | Medium | Supplier, bank account | No supplier approval lifecycle; TIN nullable with no individual flag; no bank-change control; the address table has both `physical_address` and `mailing_address` *and* an `address_type` | The fraud and data-quality controls of FR-PRC-004 and FR-MD-014 are missing |
| D-25 | Low | PR line, transfer line, count line | Item code and name copied onto lines | Acceptable as a print snapshot; the item key stays the reference |

### 4.3 Conventions: theirs vs ours

| Topic | Client dictionary | MOS (database design §2) | Keep ours because |
|---|---|---|---|
| Keys | BIGINT identity / BIGSERIAL | UUIDv7 | Installations can be merged with no key collisions (the SaaS path, ADR-0017); IDs are generated in the application, so retries are idempotent; no guessable sequential IDs in URLs. PostgreSQL 18's native `uuidv7()` keeps index locality |
| Time | Mixed TIMESTAMP / TIMESTAMPTZ | `timestamptz` throughout; business dates in the site's time zone | Correct across sites and daylight changes |
| Money and quantity | NUMERIC(18,2/3/4), mixed per table | Domains `core.qty`, `core.price`, `core.amount`, `core.factor` | One precision rule per kind |
| Status | Free VARCHAR, lists differ from the screens | Text + CHECK, changed only by named actions | Action-driven lifecycle (FR-PLT-031) |
| Posted rows | Updatable | Immutable (trigger + grants); corrections by reversal | Audit and ledger integrity (ADR-0007) |
| Approvals | One table per document type | One engine (`core.approval_*`) for every document | One set of rules, SoD and history (ADR-0010) |
| Attachments | Path columns | `core.attachment`, fingerprinted | Any number of files per document, content-checked |
| Stock | Header + ledger with an editable running balance | Insert-only ledger by bucket (item × storage unit × lot × status), balances under ordered locks, company-level moving average | Correct under concurrency (ADR-0008) |

### 4.4 Table-by-table mapping, and the fields we add

| Client table | MOS table(s) | Coverage | Fields to add to MOS |
|---|---|---|---|
| Supplier master | `md.partner` + `md.supplier_company` | Covered. `supplier_type` → `supplier_group`; status → the supplier lifecycle; registration date set by the system | — |
| Supplier contact | `md.partner_contact` | Covered | — |
| Supplier address | `md.partner_address` (one row per type) | Covered | — |
| Supplier bank account | `md.supplier_bank_account` + `md.bank` (branch, SWIFT) | Covered, with change control | **`is_default`** |
| PR header | `proc.purchase_requisition` | Covered ("Fulfilled" = displayed ORDERED) | **`purpose` becomes a configurable list** (it exists as text today: a type change, not a new field); **`budget_code`** if C-47 says yes |
| PR line | `proc.purchase_requisition_line` | Covered | — |
| PR approval | `core.approval_chain/step/decision` | Covered | — |
| PO header | `proc.purchase_order` | Covered, plus currency and rate | **`budget_code`** (C-47) |
| PO line | `proc.purchase_order_line` | Covered, plus PR-line link and receipt/invoice counters | — |
| PO approval | `core.approval_*` (`approval_level` = stage) | Covered | — |
| Warehouse master | `md.warehouse` | Covered except three fields | **`warehouse_type`, `description`, receipt/issue/transfer flags at warehouse level** (inherited by its units). Effective dates are not added: masters are deactivated (FR-PLT-053) |
| Store master | `md.storage_unit` (type STORE) | Covered except the fields in §4.5 (R-02…R-04); `material_category` → `md.storage_unit_category` (several allowed) | See §4.5 (per-store valuation not adopted, D-14) |
| Silo master | `md.storage_unit` (type SILO) | Covered except two fields; current level is a view | **`min_operating_level`** (physical low-level alarm, distinct from safety stock); the MAINTENANCE status (§4.5 R-05) |
| Bin master | `md.storage_unit` (type BIN, parent = store) | Covered except the fields here and in §4.5 | **`allow_mixed_items`, `allow_mixed_lots`** (enforced at posting), **`temperature_min/max`**; also **`dedicated_item_id`**, which is *our* addition, not a client field |
| Item master | `md.item` + `md.item_uom` + `md.item_category` | Covered; standard cost → `reference_cost`; "is raw material" → `item_type` RAW | — |
| Supplier delivery header and line | **None with the gate module off** (`gate.visit` with it on). `inv.goods_receipt` already has `vehicle_no` and `delivery_note_no` | **Gap:** driver name, arrival time, preliminary quantity, delivery status | **The arrival record** (FR-PRC-023; modelling choice O-29) |
| GRN header and line | `inv.goods_receipt`, `inv.goods_receipt_line` | Covered (plus value, payable quantity, rejected disposition) | — |
| Inventory transaction and ledger | `inv.stock_txn`, `inv.stock_ledger_entry`, `inv.stock_balance` | Covered (immutable, by bucket) | — |
| Goods transfer header and line | `inv.transfer_order(_line)`, `inv.transfer_movement(_line)` | Covered (per-line from/to, between warehouses) | — |
| Production material issue | `mfg.wo_issue(_line)`; consumption: `inv.stock_issue` | Covered | — |
| Physical count header and counted item | `inv.count`, `inv.count_line`, `inv.count_entry` | Covered (blind, recount, freeze/cut-off) | **`import_batch_id`** (Excel import of counts) |
| Inventory adjustment | `inv.stock_adjustment(_line)` | Covered (multi-line, valued) | — |
| Supplier invoice header and line | `fin.supplier_invoice(_line)`, `fin.invoice_match` | Covered | **SUBMITTED/APPROVED statuses when the option is on** (FR-AP-020). The approval chain already exists (`approval_chain_id` through the commitment header, database design §2.5). Invoice type and supplier item code: §4.5 |
| Supplier payment request | `fin.payment_request(_line)` | Covered; `payment_reason` → `purpose`; `rejection_reason` → the approval decision's comment | — |
| Supplier payment execution | `fin.payment`, `fin.cheque`, `fin.partner_allocation` | Covered | — |

**Count:**
- Of the client's 32 tables, 30 are covered.
- The supplier-delivery pair is the one gap.
- **14 fields are added** (the three warehouse-level flags count as one).
- 2 client designs are not adopted: valuation per store (D-14), and effective-dated masters.
- **How the 14 are counted:**
  - the temperature min/max pair counts as two, and the three warehouse-level flags as one;
  - `dedicated_item_id` is ours, and is counted;
  - the arrival record and the invoice statuses are not counted.

  **The 2026-10-03 re-check adds 13 more (§4.5): 27 in all.** 26 of them are client fields. R-12 and R-13 are already covered by existing fields, so **24 are new to the design**: the figure the meeting pack uses.

### 4.5 Re-check (2026-10-03): client fields the first pass missed

A second pass went column by column over all 32 tables: 505 column rows, 502 after three duplicates. It found these client fields with no MOS equivalent. They join INT-03 and INT-04 when the plan is integrated, for about +6 core hours.

| # | Client field | Client table(s) | MOS today | Add |
|---|---|---|---|---|
| R-01 | `remarks` / notes | Transfer header and line (500 characters on the page 12 screen), issue header and line, count header and line, adjustment, payment request, delivery line | Only the PR (`remarks`), the PR line (`notes`) and approval decisions (`comment`) | **`remarks` on the commitment/posting header and on document lines** (database design §2.5) |
| R-02 | `description` | Store, silo, bin | Warehouse only (§4.4) | On `md.storage_unit` |
| R-03 | Store type or purpose (production, maintenance, packaging, laboratory, general) | Store | `unit_type` STORE only | `storage_purpose` (configurable list) |
| R-04 | `temperature_controlled`, `humidity_controlled` | Store | On `md.warehouse` only | On `md.storage_unit`, inherited from the warehouse |
| R-05 | Statuses MAINTENANCE (silo) and BLOCKED (bin) | Silo, bin | `md.storage_unit.status` is ACTIVE/INACTIVE | Add both; neither accepts postings |
| R-06 | `bin_type_id` | Bin | None | `bin_type` (configurable list) |
| R-07 | Contact `status` | Supplier contact | None | `is_active` on `md.partner_contact` |
| R-08 | `invoice_type` (GOODS, SERVICE, CREDIT_NOTE, DEBIT_NOTE) | Supplier invoice header | No record of a credit note *received* (`fin.debit_note` is ours) | `invoice_type`; a received credit note reduces the open item |
| R-09 | `invoice_item_code` (the supplier's item code) | Supplier invoice line | No supplier item code anywhere, including `proc.approved_supplier_item` | `supplier_item_code` on the invoice line and on `proc.approved_supplier_item` |
| R-10 | Count scope: warehouse and storage location | Count header | `inv.count` has only `site_id`; the scope is implied by the lines | `scope_warehouse_id`, `scope_storage_unit_id` |
| R-11 | `received_by` | Material issue header | `core.doc_actor` records only the actors of actions | `received_by` (the production supervisor who receives the material) |
| R-12 | `specification` (free text) | PR line | Spec file or URL, plus notes | Low priority: covered by `notes` |
| R-13 | `reference_type` | Transfer header | None | Low priority: covered by the document-link graph |

**Also from the re-check:**
- **The client's number formats** to seed into `core.number_series`: WH-RM-001, ST-MNT-001, SILO-W-001, BIN-WHT-01, and GRN-/TRF-/ISS-/CNT-/ADJ-/SPR-/PAY-2026-00001. Its transaction-number example, "GRN-2026-00001", collides with the GRN format.
- **Enumerations to map:**
  - adjustment reasons DAMAGE, LOSS, CORRECTION and COUNT_VARIANCE;
  - address type BILLING, which is our BILL_TO;
  - capacity unit PALLET;
  - payment method "Online Transfer".
- **Other observations:**
  - `updated_at` and `updated_date` default to CURRENT_TIMESTAMP on insert.
  - `material_category` is VARCHAR on the item master but a foreign key on the PR line.
  - The item code is VARCHAR(50) on PR and PO lines but VARCHAR(30) on the master.
  - The PO has no total, and PO lines have no tax, discount or need-by date.
  - The transaction header carries both `grn_id`, `issue_id` and `adjustment_id` *and* a `reference_type`/`reference_id` pair, with no transfer or return link.
- **Source check.**
  - The DOCX and the PDF have the same content.
  - pdftotext scrambles the Material Request Header rows; read that table from the rendered page.

**Status lists that D-18 does not cover.** They map to MOS stored and displayed states in the FRD integration.

| Document | Client statuses |
|---|---|
| GRN, transfer | DRAFT, APPROVED, POSTED, CANCELLED. The MOS GRN has no APPROVED: the storekeeper's confirmation is the post (ADR-0016) |
| Count header | DRAFT, COUNTED, REVIEWED, APPROVED, POSTED, CANCELLED |
| Count line | PENDING, MATCHED, SHORTAGE, SURPLUS, APPROVED, ADJUSTED |
| Adjustment | Defaults to PENDING; there is no DRAFT |
| Payment request | Includes PAID |


---

## 5. Architecture, design and stack: theirs vs ours

**Verdicts:**
- **Same:** we already agree.
- **Keep:** we keep ours, for the reason given.
- **Update:** we change ours.
- **Adopt:** we take theirs.
- **Ask:** the client decides.

### 5.1 Decision-by-decision comparison

| # | Topic | Stakeholder proposal | MOS today | Verdict | Reason |
|---|---|---|---|---|---|
| 1 | Architecture style | Modular monolith, client-server | Modular monolith, one API + one worker, one DB (ADR-0003) | **Same** | — |
| 2 | Database topology | One central PostgreSQL for all operating units | One installation and one DB per company; sites inside (ADR-0017) | **Same** | Their operating units = our sites |
| 3 | Module set | 8: user management, procurement, inventory, QC, payables, production, sales, receivables | 6 business modules + platform, with payables and receivables in `fin`, and a gate module | **Adopt their names; keep our boundaries; Ask about the gate** | Their eight = our six + platform, *minus the gate*. Presenting the offer in their eight names (§7.2) avoids "two modules are missing". Whether they want the gate at all is C-38 |
| 4 | Frontend framework | React + Vite single-page app | Next.js 16 App Router (ADR-0012, A-37) | **Update → Vite SPA** (owner decision O-27) | §5.2 |
| 5 | Routing | React Router | Next.js file routes | **Adopt** React Router 7 (follows #4) | — |
| 6 | Styling | Tailwind CSS | Tailwind + Radix | **Same** | — |
| 7 | Client state | Zustand | Zustand | **Same** | — |
| 8 | Server state | (none named) | TanStack Query 5 | **Keep** | Caching, retries and invalidation for data-heavy screens; their proposal has no answer for it |
| 9 | API client | Axios | Typed fetch client generated from `packages/contracts` | **Keep** | The client's types are shared with the API; Axios adds a dependency and no capability |
| 10 | Language | "JavaScript or TypeScript": TS is "worth considering", but every example file is `.jsx`/`.js` and the final recommendation is "Express.js using ES modules" | TypeScript, strict | **Keep** | Their default is JavaScript with ES modules; they call TS worth considering (corrected in the re-check). An ERP needs the types; confirm their developers' skills in C-36 |
| 11 | Backend framework | Node.js + Express.js | NestJS 12 on the Express adapter (ADR-0001, ADR-0021) | **Keep** | Express runs underneath, so Express middleware and knowledge carry over. Their layers (routes, controllers, services, repositories, validators, policies) are exactly NestJS's controllers, services, repositories, pipes and guards, *enforced* rather than by convention, which matters for an AI-assisted team of 1–3. The transaction per request and the audit sit in one interceptor |
| 12 | ORM | Sequelize | Drizzle + SQL migrations (ADR-0002) | **Keep** | MOS needs explicit row locks (`FOR UPDATE`, `SKIP LOCKED`), exact numerics, triggers and grants that an ORM doesn't model, and full TypeScript types. Sequelize's TypeScript support is weak and its hooks act implicitly. The migrations are plain SQL any developer can read |
| 13 | Database | PostgreSQL | PostgreSQL 18 | **Same** | — |
| 14 | Keys | BIGINT identity | UUIDv7 | **Keep** | §4.3 |
| 15 | Authentication | JWT + secure password hashing | Server-side sessions in httpOnly cookies; argon2id, lockout, TOTP; optional OIDC (ADR-0015) | **Keep** | A session can be revoked at once and a permission change applies on the next request (FR-PLT-014). A JWT in the browser can't be revoked before it expires and is exposed to XSS. JWT stays the format for integration keys later (FR-PLT-016) |
| 16 | SSO | "SSO Login" button | Optional OIDC | **Same** | — |
| 17 | Operation unit at sign-in | On the public sign-in page | Chosen right after sign-in (D2) | **Keep** | A public page must not list the company's plants |
| 18 | Validation | Zod or Joi | Zod 4 | **Same** | — |
| 19 | Tests | Vitest, React Testing Library, Jest or Supertest; "automated unit, integration and end-to-end tests" | Vitest, RTL, supertest, Playwright, fast-check | **Same** | End-to-end tests in both; ours adds property tests of the ledger |
| 20 | API documentation | Swagger / OpenAPI, **and "database relationship diagrams"** | API: deferred until a public API exists (TDD §2). ER diagrams: the database design already has 35 | **Update → generate now** (API); **Same** (ER diagrams) | The client owns the code (A-30) and its maintainers need API docs. Generated from the Zod contracts, so about 8 hours and it can't drift |
| 21 | API style | `/api/v1/<module>/<resource>`; approvals as explicit operations | `/api/v1`, named actions (`…/approve`, `…/post`) | **Same** | Page 22 of their architecture proposal states our rule exactly: approval is an operation, not an editable `approval_status` |
| 22 | Posting control | "Should not change balances because a user clicks Save" | Draft → action → post; posted rows immutable | **Same** | — |
| 23 | Background jobs | `jobs/scheduledJobs.js`, unspecified | BullMQ 6 + Redis/Valkey behind a `JobQueue` port (ADR-0021, A-35) | **Update → pg-boss on PostgreSQL** (owner decision O-28) | §5.2 |
| 24 | Integrations | email, SMS, file storage, accounting | Email optional; journals exported as CSV; local disk or S3 | **Same**, except SMS | SMS isn't in v1; asked as part of C-36 |
| 25 | File storage | `storage/uploads`; object storage advised for production | Local disk by default, S3 optional (ADR-0013) | **Same** | — |
| 26 | Logging | `logs/` folder, `config/logger.js`, `requestLogger`; logs belong in "an appropriate logging system" | Pino structured logs, correlation IDs, redaction | **Same in intent** (ours is specified) | — |
| 27 | Deployment | Linux + Nginx | On-premises first, Windows Server or Linux; native services or Docker Compose (ADR-0022); nginx on Linux | **Same on Linux; Keep Windows** | Their target is one of our two options; the client's IT answers I-02/I-03 decide |
| 28 | Repository layout | Two projects (`flour-erp-frontend`, `flour-erp-backend`) | One monorepo with `apps/api`, `apps/worker`, `apps/web`, `packages/contracts` | **Keep** | The shared contracts give the web and the API one definition of every request; two repositories would duplicate them |
| 29 | Frontend structure | Feature folders per module (`pages`, `components`, `services`, `hooks`, `schemas`, `utils`) | `features/<domain>/` | **Same**; adopt their sub-folder names inside each feature | Familiar to their developers at no cost |
| 30 | Backend module structure | `routes`, `controllers`, `services`, `models`, `repositories`, `validators`, `policies`, `constants`, `tests` | `api`, `application`, `domain`, `infra`, `ports` | **Keep**, and add a mapping table to the TDD | Ours separates pure domain logic (property-tested) from I/O; the mapping lets their developers find things |
| 31 | Data model conventions | §4.3 | §4.3 | **Keep** | §4.3 |
| 32 | Stock ledger | Header + editable lines with a running balance | Insert-only bucket ledger with ordered locks | **Keep** | D-05 |
| 33 | Valuation | Per store, per silo | Company-level moving average (FR-INV-010) | **Keep** | D-14 |
| 34 | Approvals | One table per document type | One engine with an approval matrix and SoD | **Keep** | D-15 |
| 35 | Multi-unit access | Users across operating units, data tagged per unit | Role scopes per site | **Same** | — |
| 36 | Shared masters | "Not every table needs an operating unit"; items may be shared (yet their dictionary puts the unit on suppliers and items) | Masters company-wide; documents carry the site | **Keep**, confirm with C-03 | Their two documents disagree; ours matches their architecture text |
| 37 | Security | Authentication, roles, validation, audit logging | Plus SoD, a tamper-evident audit chain, encrypted bank numbers, ASVS L2 | **Same** (ours goes further) | — |
| 38 | Development order | "Organised around end-to-end processes": P2P, production, sales-to-cash | P2P first, then QC/gate, production, sales (plan §6) | **Same** | — |
| 39 | Process flows (§6 of their document) | P2P; production with finished-goods QC and a **hold/non-conformance or release** branch; sales with an **availability check**, one invoice referenced by receivables | Process model L1/L2 | **Same in design; three steps are pull-ins** (re-check) | Their flows are our P03–P09, P12–P13 and P14–P17. At committed core depth, though, finished-goods QC release (f14i, S, 8 h), the stock availability check (f15e, S, 8 h) and sales returns (f15l, S, 28 h) are pull-ins, and non-conformance has no MOS requirement (§5.4) |

**Tally (39 rows):**

| Verdict | Rows | Count |
|---|---|---|
| Same | 1, 2, 6, 7, 13, 16, 18, 19, 21, 22, 24, 25, 27, 29, 35, 37, 38, 39 | 18 |
| Keep | 8, 9, 10, 11, 12, 14, 15, 17, 26, 28, 30, 31, 32, 33, 34, 36 | 16 |
| Adopt | 3 (module names), 5 (React Router, which follows row 4) | 2 |
| Update | 4, 20, 23 | 3 |

### 5.2 The three recommended updates

**U-1. Frontend: Vite + React 19 single-page app instead of Next.js 16** (reverses A-37 / ADR-0012; owner decision O-27)

| | Vite SPA | Next.js 16 (current decision) |
|---|---|---|
| Fit | An authenticated ERP with no SEO and no server-rendered pages; every screen is a data grid or a form calling the API | Server rendering, server components and file routing bring nothing here |
| Production footprint | Static files served by the reverse proxy or by the API process: **no extra service** on the client's server | A third Node process (`next start` / standalone) per installation |
| Per-installation config | One build for every customer; settings read at start-up from `/api/v1/config` | `NEXT_PUBLIC_*` values and rewrites are **baked in at build time**. This is the root cause of the house build-fingerprint guard; a product sold to several companies would need a build per customer or the same workaround |
| Known footguns | Few | The house has documented them: Turbopack root, standalone tracing, build-time env, same-origin rewrite cookie scope |
| Client | The client's stated choice. The client owns the code (A-30), so its developers can maintain what they asked for | House consistency with DMS, GMS and SSO, but MOS is standalone (ADR-0014) and shares no code with them |
| Kept either way | React 19, TanStack Query/Table, react-hook-form + Zod, Zustand, Tailwind, Radix, the feature folders, the house React Compiler lint rules and `useResetOnChange` | |
| Effort | About −4 hours (no build fingerprint or standalone set-up; a small runtime-config call instead) | — |

**U-2. Queue: pg-boss on PostgreSQL instead of BullMQ 6 + Redis/Valkey** (reverses A-35; amends ADR-0006, 0021, 0022; owner decision O-28)

- **Scale.** MOS's background work is small: outbox relay, notifications, report jobs, a nightly audit seal and document-expiry checks. That is hundreds to a few thousand jobs a day. BullMQ's throughput advantage doesn't matter at this volume.
- **One less service.**
  - Redis/Valkey becomes a required production service (ADR-0021) at every installation. It has to be installed, monitored, secured and patched, and the client's IT may not run it.
  - **There is no supported native Redis or Valkey for Windows Server.** The alternatives are Memurai (a commercial Redis-compatible service), WSL2 or Docker. That conflicts with ADR-0022's native Windows packaging (§6).
  - pg-boss runs inside the PostgreSQL we already back up.
- **Simpler and safer.**
  - A job is enqueued **in the same transaction** as the business change.
  - Retries, schedules (cron), dead letters and job history are SQL tables, visible in the backups and in support queries.
  - The outbox stays for events; its relay becomes simpler.
- **Matches the client's architecture,** which has no Redis.
- **Reversible.** Code depends only on the `JobQueue` port (TDD §17.2), so BullMQ can come back as an adapter if a customer ever needs the throughput.
- **Effort:** about −6 core hours (no Redis in development, CI, health checks or packaging), and less go-live set-up.
- **If you keep BullMQ,** ADR-0022 must say how Redis runs on Windows (Memurai, WSL2 or Docker). The correction in §6 records that either way.

**U-3. API documentation now:**
- OpenAPI generated from the shared Zod contracts;
- served to administrators in development and staging;
- published as a file with each release.

About +8 standard hours.

### 5.3 What we deliberately keep, in one line each, for the client

> We keep NestJS (it *is* Express, with your proposed layers enforced), Drizzle instead of Sequelize (row locks, exact numbers and database-enforced rules), UUID keys (safe to merge, not guessable), server sessions instead of browser JWTs (instantly revocable), one approval engine, one immutable stock ledger and one company-level valuation, and one repository with shared contracts. Every field in your data dictionary is captured; the table design is ours because yours cannot enforce three-way matching or stock integrity (§4.2).

### 5.4 Re-check (2026-10-03): what the first pass missed

**Rows to add:**

| Topic | Client | Verdict |
|---|---|---|
| CORS: the frontend and the API on separate origins | `config/cors.js` | **Keep:** MOS is same-origin behind a reverse proxy (TDD §1.2, §6.5). A Vite SPA (U-1) must still be served same-origin for the cookie session |
| ES modules | Yes | Same: NestJS 12 ships ESM |
| Seeders | Yes | Same |
| ESLint | Yes | Same |
| Responsive UI | Yes | Same: the ADR-0012 tablet views |
| "Use Sequelize transactions" | Yes | Same in intent: `UnitOfWorkInterceptor` |

**The client's per-module page lists (pages 5–12, about 70 screens) were not mapped in the first pass.** These are the ones that matter for scope:

| Client page or flow | MOS | Note |
|---|---|---|
| `NonConformanceListPage` and `NonConformanceForm`; the "Quality Hold / Non-Conformance" branch in flow B | No requirement. The closest are OOS investigation (FR-QC-010), the concession (FR-QC-011, a pull-in) and the BLOCKED status | **New client question.** A gap to estimate if it is needed at go-live |
| `ProductionPlanListPage`, `ProductionPlanCreatePage` | FR-MFG-004 is Later; FR-MFG-018 is a pull-in (F) | Client question |
| Finished-flour QC release (flow B) | FR-MFG-008 / FR-QC-019 / FR-QC-026, feature f14i (S, 8 h) | **Our own documents disagree.** The proposal lists "finished-goods release" under Production, and FR-MFG-007 puts output into QUARANTINE, yet the release step is a pull-in. **Promote f14i to core in the re-plan**, or output can never be sold |
| Stock availability check (flow C) | FR-SAL-005, feature f15e (S, 8 h) | Recommend core together with f14i: about +16 h for both |
| `SalesReturnPage` | FR-SAL-009, feature f15l (S, 28 h) | Client question |

**Corrections to the §5.1 tally:**
- Row 29 also adopts their sub-folder names, so there are **3 adoptions**, not 2.
- Row 5 (React Router) happens only if O-27 says yes.
- Row 3's "Ask" isn't counted anywhere.
- Seven rows (14, 16, 17 and 31–34) come from the P2P screens or the data dictionary, not from the architecture proposal.

**Exact wording to quote:**
- row 22: "should not change posted balances simply because a user clicks Save";
- row 38: "organized around complete, end-to-end business processes";
- row 3: the client has 8 business modules **plus** an `auth` module.

**New infrastructure question: connectivity.**
- The proposal says "one centrally managed application… across all operating units". §2 found that an operation unit is a branch.
- Together, these mean remote branches reach the plant's on-premises server over a WAN or VPN.
- ADR-0022 reasons only about keeping work going when the internet is down, so this becomes a new I-question.

**If O-28 adopts pg-boss, three more items change** and join the §7.2 list:
- the TDD §1.2 topology diagram;
- ADR-0022 decision 6, whose health check includes Redis;
- ADR-0021's consequence "Redis is not backed up".

**The proposal has no timeline.** Its §8 gives a principle: follow the dependencies and build complete end-to-end processes. It has no durations, phases, team, UAT, deployment, training or migration. Its order agrees with ours.


---

## 6. Incidental finding in our own documents (fixed in this pass)

**ADR-0022, the native-services packaging.**
- **The problem:** ADR-0022 says "PostgreSQL and Redis/Valkey as system services". On Windows Server neither Redis nor Valkey ships a supported native build.
- **Why it matters:** the "house model" packaging on the client's Windows server isn't possible as written.
- **Fixed in this pass:** a dated correction note in ADR-0022. It says that on Windows the queue store needs Memurai, WSL2 or Docker, and that it is removed entirely if O-28 adopts pg-boss.
- **The TDD §20.3 Windows row** will be aligned when the plan is integrated, after O-28.

---

## 7. Integration plan

### 7.1 Principles

1. **The client's documents feed requirements, not the physical design.** Every adopted item gets an FR ID, a database change and a feature in the estimate. Nothing is copied from a mock-up without a requirement.
2. **Every finding the client should fix goes back to them** as a short feedback note with the questions in §8. That note waits for the owner's review.
3. **One question, one row.** New questions get register rows C-36 onward. Existing rows get the new evidence instead of a duplicate.
4. **The release stays as committed (ADR-0020).** Additions go into core only when the client's own flow needs them. Everything else goes to the pull-in list.

### 7.2 Changes per document (applied in the integration step, after §7.5)

| Document | Changes |
|---|---|
| [FRD](../2-requirements/MOS-FRD.md) → 0.3.4 | **New requirements:**<br>• **FR-PRC-023** supplier delivery (arrival) record with the gate module off: supplier and lines from the PO, delivery note, delivery date, plate, driver, arrival time, declared quantities per unit; statuses; hand-off to inspection and the GRN;<br>• **FR-INV-023** bin card;<br>• **FR-INV-024** stock card (both from the one ledger, with opening balance for the period);<br>• **FR-AP-020** optional approval of supplier invoices (a setting; default: only match exceptions);<br>• **FR-PLT-056** API documentation.<br>**Amended requirements:**<br>• **FR-MD-010:** warehouse type and description, warehouse-level flags, mixed items/lots, dedicated item, temperature range, minimum operating level;<br>• **FR-INV-009:** Excel import of counts, printable count sheet;<br>• **FR-QC-012:** optional "checked by" second confirmation;<br>• **FR-PRC-007/010:** purpose list, budget code if C-47.<br>**§1:** the mapping from the client's eight module names to MOS |
| [Process model](../2-requirements/MOS-PROCESS-MODEL.md) | Inbound flow C gains the delivery-entry step; the count process gains review with request-changes; the L1 map shows the client's module names |
| [Database design](../3-design/MOS-DATABASE-DESIGN.md) → 0.1.2 | The arrival record (per O-29). The 14 fields of §4.4. Supplier-invoice statuses SUBMITTED/APPROVED when FR-AP-020 is on. `qc.usage_decision.checked_by`. **New appendix: the client data-dictionary mapping (§4.4)** |
| [TDD](../3-design/MOS-TDD.md) → 0.3.4 | Per O-27 and O-28: §2 stack, §3 layout (`apps/web` as Vite), §17.2 queue, §20 packaging without Redis. API documentation. The client-layer ↔ MOS-layer mapping table (§5.1 row 30) |
| [ADRs](../6-decisions/adr/) | If O-27: **ADR-0023** (frontend: Vite SPA; supersedes ADR-0012). If O-28: **ADR-0024** (queue: pg-boss; amends ADR-0006, 0021, 0022). The ADR README index |
| [Implementation plan](../4-plan/MOS-IMPLEMENTATION-PLAN.md) → 0.5 | Appendix A gains the features in §7.3. The required velocities are recomputed by the same model (done 2026-10-03; checkpoints now W4/W8, ADR-0025). WP-10 gets a scope note pending C-38 |
| [Procurement module plan](../4-plan/MOS-PROCUREMENT-MODULE-PLAN.md) | Stories for delivery entry, invoice approval, bin card and stock card (in the procure-to-pay bundle); UAT cases for each |
| [Commercial proposal](../5-for-the-client/MOS-COMMERCIAL-PROPOSAL.md) → 0.3 | The module table in the client's eight names plus gate (per O-31). The supplier portal named as a separately priced change. Prices unchanged |
| [Decision register](../6-decisions/MOS-DECISION-REGISTER.md) → 0.2 | New A rows for what is decided; O-27…O-33; C-36…C-47; evidence added to C-03, 04, 16, 17, 24–27, 29 |
| [Questionnaire](../5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.md) | The new and updated questions in §8 |
| [First-draft review](MOS-CLIENT-P2P-DRAFT-REVIEW.md) | A pointer to this review for the v2 status of its issues |
| `MOS/CLAUDE.md`, `README.md`, `PROGRESS.md`, root `CLAUDE.md` | Stack lines if O-27/O-28 change them |

### 7.3 Effort (indicative; recomputed by the estimate model in the integration step)

| ID | Change | Tier | Hours | Condition |
|---|---|---|---|---|
| INT-01 | Supplier delivery (arrival) record, gate off | Core | +20 | — |
| INT-02 | Bin card and stock card | Core | +10 | — |
| INT-03 | Storage-master fields and the mixed-items/lots rule at posting | Core | +8 | — |
| INT-04 | Small fields: supplier default bank account, PR purpose list | Core | +3 | — |
| INT-05 | Count import from Excel, printable count sheet | Standard | +6 | — |
| INT-06 | Optional invoice approval | Standard | +8 | — |
| INT-07 | "Checked by" second confirmation on inspection | Standard | +4 | — |
| INT-08 | Budget code on PR and PO (recorded and reported; no budget control) | Standard | +4 | C-47 |
| INT-09 | OpenAPI documentation (U-3) | Standard | +8 | — |
| INT-10 | Vite SPA instead of Next.js (U-1) | Core | −4 | O-27 |
| INT-11 | pg-boss instead of BullMQ + Redis (U-2) | Core | −6 | O-28 |
| | **Net, unconditional + U-1/U-2 (first pass)** | | **core +31, standard +30** | |
| INT-15 | *(re-check §4.5)* Missed client fields R-01…R-11 (remarks on documents, storage description, type and statuses, bin type, contact status, invoice type, supplier item code, count scope, issue received-by) | Core | +6 | — |
| INT-16 | *(re-check §3.6)* Typed attachment slots per document, with a per-attachment "Verify" (FR-PLT-044 amended) | Core | +6 | — |
| INT-17 | *(re-check §3.6)* Attestation text with an "I agree" checkbox for submitter and approver (FR-PLT-025 amended) | Standard | +2 | — |
| INT-18 | *(re-check §5.4)* Promote finished-flour QC release (f14i) and the sales availability check (f15e) from standard to core | Core +16, standard −16 | 0 net | Recommended; C-48 |
| | **Net after the re-check** | | **core +59, standard +16** | |
| INT-12 | Inspection pass/fail only: grading and score, price and weight rules → pull-in | Core | up to −42 | C-25/26 |
| INT-13 | No gate module (WP-10 core is 222 h; the arrival record and, where a weighbridge exists, the weigh ticket stay) | Core | about −180 to −200 | C-38 |
| INT-14 | Supplier portal: supplier sign-in, self-registration, invoice upload, payment request, statements | — | +140 to +200 | C-39; recommended as a change request, not v1 |

**What the net changes:**
- **Core:** +59 h after the re-check (2,380 → about 2,439; the first pass said +31).
- **Required velocity, in the 26 + 9-week frame of ADR-0020:** A ×1.32 → about ×1.35; B ×1.52 → ×1.56; C ×2.50 → ×2.56. **The six-month re-plan (2026-10-03, ADR-0025) replaced that frame and counts these items: core 2,439 h; speeds needed A+ ×1.33, A ×1.89, B ×2.22.**
- **Price:** unchanged on every path (ADR-0020 prices the team's capacity).
- **Schedule:** absorbed by the six-month plan; checkpoints now at W4 and W8.
- **If INT-12 or INT-13 come true,** the required velocity falls; INT-13 alone would bring path A to about ×1.24 (in the old frame).

### 7.4 Sequence

| Step | What | Who | Gate |
|---|---|---|---|
| I-0 | This review | Claude | **Done; waiting for the owner** |
| I-1 | Decisions O-27…O-33 (§7.5) | Owner | — |
| I-2 | Apply §7.2 to every document; recompute the estimate; rebuild the HTML views | Claude | Checks below |
| I-3 | Feedback note to the client on the three documents, with the §8 questions (questionnaire v0.2) | Claude drafts, owner sends | Owner's review |
| I-4 | Fold the client's answers in (C-25/26, C-38 and C-39 can move scope) | Claude | — |

**Checks for I-2:**
- every v2 screen maps to an FR;
- every client table maps to an MOS table;
- every new FR has a feature and a story;
- the feature hours equal the stories;
- every register question appears once;
- every link resolves;
- every Mermaid diagram parses;
- no code or DDL files in `MOS/`.

### 7.5 Owner decisions needed

| ID | Decision | Recommendation | Needed before |
|---|---|---|---|
| O-27 | Frontend: Vite SPA or keep Next.js 16 (A-37)? | **Vite SPA** (U-1) | I-2 |
| O-28 | Queue: pg-boss on PostgreSQL or keep BullMQ + Redis/Valkey (A-35)? | **pg-boss** (U-2) | I-2 |
| O-29 | Model the supplier delivery as the gate visit in both modes (gate-only fields optional when the gate module is off) or as a separate procurement table? | **One arrival record (the visit) in both modes.** One truck, one record; the inspection and GRN links already exist; nothing to merge if the client turns the gate on later | I-2 |
| O-30 | Supplier portal elements (page 22): in v1? | **No.** The finance officer raises the PV as the narrative says; the portal is a separately priced change (INT-14) | I-3 |
| O-31 | Present the offer in the client's eight module names, with the gate as a ninth (or optional) module? | **Yes**, with the mapping in the proposal | I-2 |
| O-32 | Send the client the feedback note and the new questions? | **Yes,** after your review of §3–§4 and §8 | I-3 |
| O-33 | Position on the data dictionary: "every field captured, our table design" | **Yes** (§4, §5.3) | I-3 |

---

## 8. Questions for the client (new and updated)

**New** (register rows to be added):

| ID | Question | Why | Default if unanswered | Needed by |
|---|---|---|---|---|
| C-36 | Is the architecture document a **requirement** or a **recommendation**? Will your own developers maintain or extend the system after handover, and with which skills? Do you need SMS notifications? | Decides how far we follow it (§5) | A recommendation; we deliver our stack with full source and documentation | Before W1 |
| C-37 | Is the data dictionary the **required table design**, or the list of data you need captured? | §4 | The data to capture; our design captures every field | Before W1 |
| C-38 | **Weighing and the gate.** v2 shows no weighbridge weights (gross, tare, net), while v1 had them. Do you weigh trucks, and is the payable quantity the weighbridge net, the bag count or the delivery note? Do you want the gate and weighbridge module in this release? | Could remove about 200 core hours, or confirm them (INT-13) | Gate module in scope; weights typed when the gate is off | Before W1 |
| C-39 | **Who raises supplier payment requests:** your finance officer (page 1) or the supplier (page 22)? Should suppliers use the system at all (registration, invoice upload, statements)? | A supplier portal is a separate scope (INT-14) | The finance officer; no supplier access in this release | Before W1 |
| C-40 | Which documents or screens in v2 are final, and which are examples? Will a third revision with one coherent example follow? | Acceptance data (C-28) | The fields are final; the numbers are examples | Before W4 |
| C-41 | **Supplier delivery entry:** who records it (procurement, stores, security), and what does "Approve" on it mean? | FR-PRC-023 | Stores record it; "approve" = matches an open PO, send to inspection | Before W8 |
| C-42 | Should **every** supplier invoice be approved (page 21), in addition to the payment request, or only invoices with a match difference? | FR-AP-020 | Only match differences | Before W10 |
| C-43 | Are **rejected** (delivered, refused) and **short** (not delivered) quantities recorded separately? | GRN page 9 merges them | Separately | Before W8 |
| C-44 | When raw material goes "for production" (page 12), is it moved to a production staging store, or issued directly to the milling run? | Transfer vs issue | Issued to the work order | Before W14 (six-month plan: W10) |
| C-45 | Should counters see the system quantity (page 13) or count blind? Do you count in Excel and import? | FR-INV-009 | Blind; Excel import available | Before W12 |
| C-46 | May an approved count be adjusted by a different figure (page 15 sets −200 to 0)? | Count integrity | No: a different figure needs a recount | Before W12 |
| C-47 | Do you budget purchases (the dictionary's `budget_code`)? Record only, or should the system check budgets? | INT-08 | Record and report only | Before W8 |
| C-48 | *(re-check §5.4)* Your architecture document lists **non-conformance, production plans, finished-flour release, a stock check before sales-order approval and sales returns**. Which do you need at go-live? | f14i, f15e, f15l are pull-ins; non-conformance has no requirement; production plans are Later/pull-in | Finished-flour release and the availability check at go-live (recommended promotion to core, about +16 h); the rest after go-live | Before W1 |
| C-49 | *(re-check N-30)* Can one person order, inspect **and** receive the same delivery (pages 7–9 show one person in all three roles)? Which roles must be different people? | SoD rules (FR-PLT-028) | Three different people; logged administrator exceptions | Before W6 |
| I-18 | *(re-check §5.4)* Are the branches (operation units) at other locations, and how do they reach the central server: internet, VPN or a private line? | One central on-premises server must be reachable from every branch (ADR-0022) | One site, or branches over VPN | Before W18 (six-month plan: W12) |

**Updated with new evidence** (existing rows):

| ID | What the new documents add |
|---|---|
| C-03 Operation unit | The architecture document's "operating units es" shows the term replaced **"branch"**; its text says masters may be shared, but its data dictionary puts the unit on suppliers and items. Confirm: a branch/plant, and are suppliers and items shared across them? |
| C-04 Currency | v2 still has USD on PR and PO; the dictionary's PO has no currency column |
| C-16 Payment methods | The payment screen lists "Other": what is it (mobile money, letter of credit)? |
| C-17 Withholding | The invoice shows WHT 0.00 on a 441,000 ETB goods purchase; the supplier uploads a WHT certificate. Who issues WHT certificates, and when is tax withheld? |
| C-24 Items | The delivery lists wheat, soya bean, wheat bran and maize: confirm the raw-material list, and whether bran is also bought. **Re-check (N-31):** page 8 inspects only the wheat; soybean, bran, maize and "Other" on the same delivery are received uninspected. Which raw materials are inspected, and against which specifications (none yet for maize or soybean)? |
| C-25…C-27 Grading, deductions, partial acceptance | v2 inspects pass/fail and rejects the delivery on any failure (v1 had grades and a 92.4 score). Which is right? Are deliveries ever accepted with a deduction, a concession, or in part? |
| C-29 Gate workshop | Merged into C-38 for the go/no-go; the workshop details stay in C-29 |
