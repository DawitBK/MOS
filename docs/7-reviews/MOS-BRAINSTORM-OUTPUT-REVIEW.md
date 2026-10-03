# MOS: Review of the owner's ChatGPT brainstorm outputs

<!-- nav -->[MOS](../../README.md) › [7. Reviews](README.md)

| | |
|---|---|
| Reviewed | Three files from the owner's ChatGPT session of 2026-09-27, kept in [docs/9-inputs/owner-brainstorm/](../9-inputs/owner-brainstorm/):<br>• `chatgpt-erp-flow-and-supporting-document.pdf` (7 pages; the `.docx` has the same content)<br>• `chatgpt-erp-flow-diagram.png` ("Floor Manufacturing ERP – End-to-End Process Flow Diagram", 12 numbered panels)<br>• `chatgpt-p2p-swimlane.png` ("Raw Material Procurement-to-Payment Process", 7 lanes, 9 steps) |
| Inputs ChatGPT had | The client's procure-to-pay narrative, the six-module scope of work, and the client's 18-page draft ([docs/9-inputs/from-client/](../9-inputs/from-client/)) |
| Checked against | The client's draft page by page; FRD, TDD and client-draft review v0.3 |
| Date | 2026-09-27 (planning Phase 2) |
| Audience | Product owner; developers (traceability) |
| Replaced by | [MOS-PROCESS-MODEL.md](../2-requirements/MOS-PROCESS-MODEL.md) (and its local `.html` view), which is the process reference from now on. These three files stay as history and are not requirements |

---

## 1. Verdict

**The ChatGPT outputs get the procure-to-pay spine right and little else.** The spine is PR → approval → PO → delivery → inspection → GRN → stock → payment request → approval → payment. The traceability chain and the list of generic controls are also sound; both were already in the FRD and are kept.

Everything that makes this client a **flour mill that buys wheat by weight and grade** is missing, generic or wrong:
- no weighbridge;
- pass/fail instead of grades and deductions;
- no invoice matching;
- a discrete-assembly production model instead of milling;
- no sales panel at all in the picture;
- outbound trucks that leave on paperwork instead of on a posted goods issue and a verified weight.

This review found **29 findings: 8 High, 13 Medium, 7 Low, 1 Info** (§3). The High findings are exactly the areas where money moves in a mill: weight, quality, matching, milling yield, food-safety release and dispatch.

**Nothing in these files changes a decision already in the package.** Several items confirmed existing requirements. Modelling the processes in full surfaced six new requirements, three business rules and several design gaps. All but one came from the process-modelling work itself; BR-PRC-05 (a PO may not order more than its PR) came from mapping ChatGPT's tolerance question BQ-32. They are listed in [process model §14](../2-requirements/MOS-PROCESS-MODEL.md#14-gaps-found-and-fed-back).

---

## 2. What to keep

| Item (where) | Kept as | Where in MOS |
|---|---|---|
| The procure-to-pay spine and role sequence (PDF §3, §4.1; swimlane) | Matches the client's own narrative (client draft page 1) | FRD §9; process model P03, P04, P05–P07, P09 |
| **End-to-end traceability chain** (PDF §12, §13): Supplier → PO → gate entry → inspection → GRN → stock ledger → invoice → payment request → approval → payment, and forward to work order → output → sales order → delivery → customer | A test requirement: backward and forward trace must work from any document | FR-INV-004, FR-MFG-009; process model §8 |
| **Ten key controls** (PDF §9): unique numbers, recorded approvals, GRN ≤ PO within tolerance, blocked stock not consumable, payment needs PO+GRN+invoice, transaction-driven stock, reasoned adjustments, RBAC, audited master data, linked evidence, duplicate-payment prevention, timestamped gate records | All already in the FRD. Each now has a testable rule and an owner | FRD §15; process model §11, §12 |
| "One integrated transaction platform, not six applications; each module owns its rules" (PDF §12) | Agrees with the modular monolith | ADR-0003; TDD §1 |
| Integration matrix and exception table **as formats** (PDF §5, §8) | Extended: 33 numbered hand-offs, an event catalogue with producers and consumers, 64 exception cases | Process model §3, §9, §10 |
| Rules not confirmed by the client are "proposed design controls, not confirmed requirements" (PDF §14) | Same practice: open points become client questions | Review §6; Phase 5 register |
| Return/replacement loop from inspection back to the supplier (swimlane) | A process in its own right | Process model P08; FR-PRC-015 |
| Payment evidence / deposit-slip upload (PDF §4.1 step 11; client page 18 "Upload deposit slip") | Proof-of-payment attachment, required by setting | FR-AP-008 |
| Vehicle and quality-specification masters (PDF §6) | Already in the FRD | FR-GATE-001, FR-QC-002 |
| Reporting and administration panels (picture panels 10, 11) | Consistent with the FRD | FR-RPT-001…005; P19, P20 |

---

## 3. Findings

**Severity:**
- **High:** building it as drawn would lose money, break a control, or misdirect a whole module.
- **Medium:** wrong or missing, and it would cause rework or confusion.
- **Low:** cosmetic, or a minor omission.
- **Info:** a note, nothing wrong.

**Where:** PDF §n is the supporting document's section; "panel n" is the ERP picture; "swimlane" is the P2P image.

### 3.1 High

| ID | Where | What the brainstorm shows | Why it matters | What MOS does |
|---|---|---|---|---|
| B-01 | All three titles; PDF question 28 ("floor-manufacturing products") | "Floor Manufacturing" read literally | The client is a flour mill: its own inspection screen tests durum wheat for "Addis Flour Factory"; stock lives in silos; bran is a stock item. Products, QC parameters and production are completely different for flooring | "Floor" is read as **flour** until the client confirms (client question Q0). v1 ships the grain/flour-milling pack (FRD §1, FR-MD-011) |
| B-02 | PDF §2, §3, §4.1, §10; panels 3 and 8; swimlane | **No weighing anywhere:** no weighbridge, no gross/tare/net, no bag tare, no second-person verification | Wheat is bought and paid for by weight. The weigh ticket is the quantity source for the GRN, the payable quantity and the supplier's claim. It is also the biggest fraud surface (tare manipulation, water tanks, a driver in the cab) | Weigh tickets and weighings with division, photo and blind verification (FR-GATE-009…015, 030); bag tare (FR-PRC-021). With the gate module off, the weights sit on the inspection lot (FR-QC-025). Process model P05, P07 |
| B-03 | PDF §3 stage 9, §4.1 step 9; panel 4 ("Approve Material → Trigger GRN & Payment Process"); panel 9; swimlane steps 6–7 | The supplier invoice is only "used". No invoice capture, no three-way match, no tolerances, no holds. The picture triggers payment from the QC approval | Payment triggered at QC approval would come before tare, unloading, receipt and the invoice exist. Matching is the one control that stops paying for more than was received at the agreed price | Settlement process P09: invoice capture → match against the GRN's payable quantity and quality-adjusted price → holds → PV → approval → payment (FR-AP-001…008). A GRN posting only **drafts** a PV for the finance officer (FR-INB-003, ADR-0016) |
| B-04 | PDF §4.1 step 6, §10 inspection lifecycle; panel 4; swimlane decision diamond | Inspection is binary: pass or fail | The client's own screen shows a grade, a quality score ("92.4 / 100"), and accepted and rejected quantities. Mills pay for wheat by grade, with deductions for moisture and foreign matter. A binary outcome loses exactly that money | Usage decisions ACCEPT, ACCEPT WITH DEDUCTION, ACCEPT UNDER CONCESSION, PARTIAL ACCEPT, REJECT (FR-QC-012); grade and score (FR-QC-022/023); payable-quantity and price rules (FR-QC-013) |
| B-05 | Picture: no panel 7, although the legend lists "7 Sales Management"; PDF §4.3 | **Sales, one of the six contracted modules, has no panel in the picture.** The PDF's sales flow has no credit control, customer receipts, cash sales, fiscal receipts or returns | A stakeholder reading the picture sees five modules plus a "Gate Exit" module. Receivables, the money coming in, are missing from both files | Order to cash (P14), with the cash-sale variant, and returns (P15); FR-SAL-001…015 |
| B-06 | PDF §4.3 step 24, §5 row "Gate → Inventory: exit confirmation"; panel 8 | Outbound trucks: no weighing, and the gate checks paper (sales order, invoice, delivery note). The PDF has gate exit updating inventory; the picture has it "closing the transaction" | The goods issue must be **posted before the truck leaves**, with the quantity taken from a verified outbound weight. Over-loading is the main loss route at a mill. The gate never updates stock | Outbound visit: tare → load → gross (verified) → goods issue posts → exit clearance checks the posted document and the weight tolerance. This check **cannot be overridden** (FR-GATE-016, BR-GATE-09, FR-SAL-006) |
| B-07 | PDF §4.2; panel 6 | Production is a generic discrete flow: BOM components, routing, "scrap qty", OEE | Milling is a process: a wheat blend from silos goes in, and several products come out (flours, bran, shorts, screenings). Tempering water adds mass; extraction rate is the key KPI; runs are continuous campaigns; bulk flour is packed into bag sizes. Bran is a saleable by-product, not scrap | Campaign work orders, grist and tempering, mass balance, co- and by-products, material-cost settlement, packing (FR-MFG-003, 005, 006, 013–016); process model P12, P13 |
| B-08 | Panel 6 (output → finished goods → "Update FG stock"); PDF §4.2 step 17 ("quality checks **may** be performed") | Finished goods go straight into saleable stock | Flour is food. Output is held until the lab releases it (moisture, ash, protein, gluten, falling number) and must not be sold before release | Output goes into QUARANTINE; release, regrade or reject (FR-MFG-007/008, FR-QC-019/026); the releaser must not be the supervisor who posted the output (SOD-12) |

### 3.2 Medium

| ID | Where | What the brainstorm shows | Why it matters | What MOS does |
|---|---|---|---|---|
| B-09 | Panel 3 (gate: "Update stock & link to PO/Delivery"); swimlane ("Add accepted quantity to raw material stock" in the Finance lane, after "Receive goods, generate GRN and update stock") | Stock updated by the gate, by Finance, or twice | Stock changes only through posting documents. Two boxes for one posting reproduce the double count in the client's stock screen (review §5 #9) | Only the GRN posting writes stock (FR-INV-001, BR-INV-01); the stock-balance fixture (FR-INV-014) |
| B-10 | Swimlane lanes | Boxes in the wrong lanes:<br>• "Reject materials and notify supplier" sits in the Storekeeper (Receiving) lane<br>• stock update sits in the Finance lane<br>• payment approval and payment processing are drawn across the Supplier, Inspection and Storekeeper lanes; only "Raise Payment Request" sits in Finance | Read as a swimlane, the supplier and QC appear to approve and process payments | L2 swimlanes with one lane per role (process model P05–P09) |
| B-11 | Swimlane ("Management" approves the PR only); panel 2 | No PO approval | The client's draft has a PO approvals dashboard (draft page 7) | PO approval through the approval matrix (FR-PRC-010, FR-PLT-023); process model P04 |
| B-12 | PDF §3 stage 7, §4.1 step 7 | One inspection order only: inspect, then receive | Bulk wheat is inspected **before unloading**, so a bad load is rejected on the truck. Packaged additives are received into **quarantine** and tested afterwards. Spare parts are not inspected at all | QC policy per item: inspection point BEFORE_UNLOADING, IN_QUARANTINE or NONE (FR-QC-004); flows A, B, C (FRD §9.1–9.3; process model P05–P07) |
| B-13 | PDF §4.1 step 7; §8 "Inspection failed" | Rejected material is only "blocked and returned" | A rejected quantity is either **left on the truck** (never received) or **unloaded and held** as REJECTED stock; the client's GRN has a rejected-quantity column. The two have different stock and payable effects | Rejected disposition RETURNED_ON_TRUCK or HELD_AS_REJECTED (FR-PRC-013); supplier return with a debit (FR-PRC-015); client question Q4 |
| B-14 | PDF §10 | The suggested lifecycles duplicate or fuse states (details in §5 below): inspection Passed/Failed **and then** Approved/Rejected; GRN Draft → Pending Confirmation → Confirmed → Posted; PR "Under Review"; delivery mixes the QC outcome with a stock status ("Blocked"); work order "Material Issued" as a state; no partial payment | Every extra state is a place where records get stuck. A GRN that is "Confirmed" but not "Posted" tells the storekeeper stock exists when it does not | Canonical vocabulary (FRD §1.5), drawn as state machines in process model §6 |
| B-15 | PDF §5; panel 12 | The integration matrix has no QC → Finance hand-off (payable quantity, adjusted price), no Gate → QC (weights) and no QC → Gate (decision). It shows "Production ↔ Sales" as a direct integration. All the picture's arrows are two-way, and nothing says which hand-offs are synchronous | The money-relevant hand-off (deductions → GRN value → match) is missing. Some steps must happen **in the same transaction**, e.g. QC decision → unload authorised, or GRN → stock. Doing them asynchronously would open race windows | 33 numbered hand-offs (process model §3); an event catalogue and a list of synchronous ports (§9) |
| B-16 | PDF §6 (silo master: "current balance/status") | The current balance is stored on a master record | Balances must be computed from the ledger and never edited. The client's silo master has the same problem | A silo's current level is a view over the ledger (TDD §5, FR-INV-019) |
| B-17 | PDF §6, §7 | Master data and the document chain omit the records that carry the controls.<br>**Masters:** units and conversions (bag ↔ kg), weighbridges, bank master, tax codes, payment terms, grade schemes and price rules, reason codes, price lists, work centres.<br>**Documents:** weigh ticket, usage decision, debit note, self-billed voucher, WHT certificate, delivery order and goods issue as separate documents, customer invoice, receipt, transfer, count | Without these records, the controls in PDF §9 have nothing to act on | FRD §4 (FR-MD-001…014); document chain in process model §8 |
| B-18 | PDF §8 | 10 exceptions, and vague on the hard ones ("do not silently increase stock"). Missing, among others:<br>• a truck that leaves without tare; a suspicious tare<br>• weight disputes; deduction disputes<br>• an invoice that arrives before the GRN, or while stock is in QC; duplicate invoices<br>• bank-detail fraud<br>• an outage at the gate<br>• a lab result corrected after the decision | These are the cases that cost a mill money | FRD §9.5 (34 inbound cases), plus 30 further cases across the other processes (process model §10) |
| B-19 | PDF §9 | The controls are correct but generic. Missing:<br>• specific SoD pairs (e.g. PO creator ≠ GRN poster)<br>• dual control and a cooling-off period on bank-detail changes<br>• blind weight verification; gap-free numbering<br>• period close and backdating limits<br>• no exit without a posted document; credit limits | A generic control cannot be tested | FRD §15; SoD matrix and controls per process (process model §11, §12) |
| B-20 | Panel 9 ("Update ledger", "Payment Voucher", "Supplier Ledger") | Implies a general ledger. Uses four documents (payment request → approved request → payment voucher → payment confirmation) where there are two | The general ledger is out of scope: MOS keeps sub-ledgers and exports journals. Four document names confuse users; the client calls the request a "payment request" | One PV (the payment request, numbered `PV-`) and one payment record; journals exported (FR-AP-007, 008, 010) |
| B-21 | Panel 4 | The Quality Manager approves every material decision | A bottleneck at the gate, where trucks queue. In the client's draft the inspector decides | The lab analyst confirms the system's suggested outcome; the QA manager is needed only for overrides, concessions and partial acceptance (FR-QC-012) |

### 3.3 Low and Info

| ID | Sev | Where | What the brainstorm shows | What MOS does |
|---|---|---|---|---|
| B-22 | Low | Panel 8; legend; panel 4 | "Gate Exit" is a separate module 8. The legend is garbled ("Gate Lost Management", "System Adminiation" next to "System Administration", two items numbered 8). Typos: "Laboratory Techncian", "Teste Report". Shown to stakeholders, these undermine credibility | One Gate module: a visit runs from arrival to exit (FR-GATE-006) |
| B-23 | Low | Panel 2 ("Compare suppliers"; supplier "Receive PO & confirm") | Implies RFQ comparison and supplier PO confirmation | Quotations are recorded on the PO in v1 (FR-PRC-008); RFQ comparison is Later (FR-PRC-009); suppliers have no login in v1 (FR-PRC-019 Later) |
| B-24 | Low | Panel 1 | Supplier registration with no approval, KYC or bank-detail control | Supplier onboarding with document checklist and approval (P01); controlled bank-detail changes (P02); FR-PRC-001…006 |
| B-25 | Low | PDF §15 | Questions numbered 28–42, continuing the numbering from §4's list | Referred to here as BQ-28…BQ-42 (§4) |
| B-26 | Low | PDF §14 | The page references to the client's draft are **correct** (checked against the PDF: supplier pages 3–5, PR/PO 6–7, inspection 8, masters and receiving 9–13, stock 14–15, payment 16–18). But page 2, sign-in with username/password, "SSO Login" and "operation unit", is not mentioned anywhere | Local accounts by default, optional SSO, operation-unit selection after sign-in (FR-PLT-010, 018, 019; ADR-0015) |
| B-27 | Low | PDF §11 | The report list lacks the mill's own KPIs: extraction rate, acceptance rate, deduction totals, net vs declared weight, truck turnaround, approval cycle time | KPI table per module (process model §12) |
| B-28 | Low | PDF §13 | The forward trace ignores **silo commingling**: wheat lots mix in a silo, so answering "which flour came from this truck" needs the silo draws allocated to lots | First-in-first-out allocation of silo draws in v1; quantitative blend genealogy in v1.1 (FR-INV-019, FR-MFG-009) |
| B-29 | Info | PDF §12 | The layered architecture view agrees with ADR-0003 | Kept |

---

## 4. Missing entirely

None of the three files mentions these. Each one matters for this client, and MOS covers each.

| Missing topic | Why it matters for a flour mill | MOS coverage |
|---|---|---|
| Weighbridge: gross, tare, net, bag tare, blind verification, stored tare with random re-tare | Wheat is paid for by weight | FR-GATE-009…015, 030; FR-PRC-021 |
| Grade, quality score, deductions, payable quantity | This is how wheat is priced | FR-QC-013, 022, 023; FR-INV-022 |
| Inspection point (before unloading vs quarantine) | Reject on the truck, or test after receipt | FR-QC-004; flows A/B/C |
| Self-billing for suppliers who issue no invoice (individual farmers, traders) | Many wheat suppliers have no invoice book or TIN | FR-AP-006, FR-AP-018, FR-MD-014 |
| Supplier returns and debit notes, including the automatic quality debit note | A supplier who invoices at gross weight must be debited the deduction | FR-PRC-015, FR-AP-005 |
| Withholding tax, VAT, WHT certificates | Legal obligations when paying suppliers | FR-AP-013, FR-AP-017 |
| Cheque register and cheque books with gap detection | The client pays by cheque (draft page 18) | FR-AP-012, FR-AP-016 |
| Bank-detail change control (independent approval, call-back, cooling-off, hash check at payment) | Most common payment fraud | FR-PRC-004, BR-AP-04 |
| Segregation-of-duties pairs, gap-free numbering, period close, backdating limits | These make the controls auditable | FRD §15; FR-PLT-041…043 |
| Silo capacity, levels, dip measurement, fumigation holds, first-in-first-out draw-down | Silos are the mill's main storage | FR-INV-019…021 |
| By-products (bran, shorts, screenings) and the mass balance | Bran is roughly a quarter of the output and is sold | FR-MFG-003, 013, 014 |
| Packing into bag SKUs, with backflushed bags and labels | How flour is sold | FR-MFG-016 |
| Credit control (exposure, limits, holds), cash sales, fiscal receipt numbers, cash sessions, customer advances | Cash and credit leakage at dispatch | FR-SAL-004, 008, 012–014 |
| Outbound weighing and the no-exit-without-posted-document rule | Over-loading is the main dispatch loss | FR-GATE-016, BR-GATE-09 |
| Degraded mode at the gate (paper slips, back-entry, gap detection) | Trucks cannot wait for the network | FR-GATE-023, 024 |
| Accounting export (journals, account map) | The client keeps its accounting package | FR-AP-010 |
| Who does what (RACI) | Needed for training and role set-up | Process model §11 |

---

## 5. ChatGPT's 15 business questions, and where each one goes

ChatGPT numbered its questions 28–42, so they appear here as BQ-28…BQ-42. The Phase 5 decision register will merge them with the client list in [review §6](MOS-CLIENT-P2P-DRAFT-REVIEW.md#6-questions-for-the-client), removing duplicates.

| # | Question (ChatGPT, corrected where needed) | Disposition | Where |
|---|---|---|---|
| BQ-28 | Which ~~floor~~ **flour** products and production stages will be managed? | Merged | Q0 (flour?) and Q17 (production workshop) |
| BQ-29 | Are raw materials lot-controlled, and which require mandatory lab testing? | **Designed**; the client supplies the per-item flags | FR-MD-003 (lot tracking, "inspection required"), FR-QC-004. New data request: the item list with lot and QC flags and the inspection point (Phase 5) |
| BQ-30 | Which quality standards and test ranges apply per raw material? | Partly merged; the rest is a new data request | Q2 (grade table). The grain pack seeds default specs; the client confirms the ranges (Phase 5) |
| BQ-31 | Can partial deliveries and partial GRNs be posted against one PO? | **Answered by the design: yes** | FR-PRC-013, FR-PRC-014; FRD §9.5 #14. Confirm only |
| BQ-32 | What quantity and price tolerances apply between PR, PO, GRN and invoice? | **New client question**; the design has defaults | FR-PRC-014, FR-AP-003; new BR-PRC-05 (PO vs PR quantity). Phase 5 |
| BQ-33 | What is the approval matrix by amount, department, category and document type? | Merged and widened from PRs to every document type | Q5; FR-PLT-023 |
| BQ-34 | Is three-way matching a hard control before payment? | **Answered by the design: yes** for stock items; services use a service confirmation (new FR-PRC-022); farmers use self-billing | FR-AP-002, BR-AP-03. Confirm only |
| BQ-35 | How is rejected material returned, replaced or disposed of? | Merged | Q4 |
| BQ-36 | How many warehouses, stores and silos exist, and can stock move between them? | Merged; plus a data request | Q10 (transfer approval); the storage list for set-up (P19) |
| BQ-37 | Which production transactions need reservation, issue, return and scrap? | Merged | Q17 |
| BQ-38 | What are the sales approval and credit-control rules? | Merged | Q18 |
| BQ-39 | Which gate documents are checked inbound and outbound? | Merged | Q17 |
| BQ-40 | Which accounting, ERP or payment systems must integrate? | **New client question** | The accounting package and its import format for the CSV export (FR-AP-010). Phase 5 |
| BQ-41 | What are the tax, invoice, withholding and payment rules? | Merged | Q14 (VAT/WHT) and Q18 (fiscal device) |
| BQ-42 | What audit, retention, backup and disaster-recovery requirements apply? | Merged; retention is a new question | Q19 (infrastructure); the legal retention periods (Phase 5) |

---

## 6. Lifecycles: ChatGPT's suggestion vs MOS

| Document | ChatGPT (PDF §10) | MOS canonical (FRD §1.5) | Why MOS differs |
|---|---|---|---|
| PR | Draft → Submitted → **Under Review** → Approved / Rejected / **Returned** → Converted to PO | DRAFT → SUBMITTED → APPROVED → PARTIALLY_ORDERED → ORDERED; CLOSED; REJECTED; CANCELLED | No action separates "Submitted" from "Under Review". "Returned" puts the PR back in DRAFT, with the return recorded in its history. A PR can be partly converted into several POs |
| PO | Draft → Submitted → Approved → Sent to Supplier → Partially / Fully Received → Closed / Cancelled | DRAFT → SUBMITTED → APPROVED → ISSUED → PARTIALLY_RECEIVED → RECEIVED → CLOSED; REJECTED; CANCELLED (only with zero receipts) | Adds REJECTED, and restricts cancellation to POs with nothing received. Amendments are revisions (FR-PRC-011). A PO closes when fully received **and invoiced**, not merely received |
| Delivery | Expected → Gate Entered → Inspection Pending → Accepted / Rejected / **Blocked** → Received / Returned → Gate Exited | Gate visit: EXPECTED → CHECKED_IN → FIRST_WEIGHED → SAMPLED → QC_DECIDED → UNLOAD_AUTHORIZED → UNLOADED → GRN_DRAFTED → RECEIPTED → EXITED, plus side states | Weighing and unloading are real steps. "Blocked" is a stock status, not a visit state. The QC outcome lives on the inspection lot |
| Inspection | Pending → Sampled → Testing → **Passed / Failed → Approved / Rejected** | CREATED → IN_TESTING → DECISION_PENDING → DECIDED → CLOSED; CANCELLED | Pass/fail is the system's *evaluation*, a suggestion. The usage decision is the one state change, and it carries grade, deduction, concession or partial quantities |
| GRN | Draft → **Pending Confirmation → Confirmed** → Posted / Cancelled | DRAFT (prepared) → POSTED → REVERSED; a DRAFT can be CANCELLED | Confirming *is* posting: the client's "Save & Generate GRN". Adds REVERSED, the only way to correct a posted GRN |
| Payment request | Draft → Submitted → Under Review → Approved / Rejected / Returned → **Paid** / Cancelled | DRAFT → SUBMITTED → APPROVED → PARTIALLY_PAID → PAID; REJECTED; CANCELLED | The client's draft pays partially. The PV number is kept when a PV is cancelled or rejected |
| Work order | Planned → Released → **Material Issued** → In Production → Completed / Cancelled | PLANNED → RELEASED → IN_PROGRESS → COMPLETED → SETTLED → CLOSED; CANCELLED (only with no postings) | Issues happen repeatedly during a run, so "issued" is not a state. Settlement values the output |
| Sales order | Draft → Submitted → Approved → **Allocated** → Dispatched → Delivered / Cancelled | DRAFT → SUBMITTED → APPROVED (or ON_HOLD for credit or price) → PARTIALLY_DELIVERED → DELIVERED → CLOSED; REJECTED; CANCELLED | Credit and price holds are explicit. Reservation is a quantity, not a status. Dispatch belongs to the **delivery order**, which has its own lifecycle |

---

## 7. Integration matrix: ChatGPT's 13 rows vs MOS

| ChatGPT row (PDF §5) | Verdict | MOS hand-off / event (process model §3, §9) |
|---|---|---|
| Procurement → Inventory: approved PO / expected receipt | Kept, reworded. Open PO lines are what receipts are posted against | H10 (GRN posting updates PO receipt quantities, synchronous) |
| Procurement → Gate: supplier delivery | Kept, made concrete | H04 `po.approved` → expected arrivals |
| Gate → Quality: vehicle/material arrival | Kept, with weights and bags | H06 (inspection lot created at first weighing, synchronous) |
| Quality → Inventory: inspection accepted | Kept; the payable quantity and adjusted price are added | H07, H09; `qc.decision_made` |
| Quality → Procurement: inspection rejected | Kept; the supplier return process is added | H16; P08 |
| Inventory → Finance: GRN completed | Kept | H11 `grn.posted` → PV draft |
| Procurement/Inventory → Finance: payment eligibility | **Replaced**: eligibility is the invoice match result, not a hand-off | P09 matching; `supplier_invoice.posted` |
| Finance → Procurement: payment completed | Changed: a PO closes on **invoicing**, and the payment advice goes to the supplier | H15; `payment.posted` |
| Inventory → Production: material availability | Kept | H18 (work-order release reserves stock; issues draw silo lots first-in-first-out; synchronous) |
| Production → Inventory: consumption/output | Kept; the output lands in QUARANTINE until released | H19, H20, H21 |
| Sales → Inventory: order/delivery | Kept | H24 (reservation), H27 (goods issue) |
| Sales → Gate: dispatch | Kept | H25 `delivery_order.released` → expected outbound visit |
| Gate → Inventory: exit confirmation | **Wrong direction, removed.** The gate never updates stock; the exit *checks* that the goods issue is posted | H28 (exit clearance through the `OutboundDocumentChecker` port) |

**Rows added by MOS include:**
- QC → Gate (decision → unload or reject);
- Gate → Sales (verified outbound net → goods-issue draft);
- Production → QC (finished-goods lot);
- QC → Inventory (release);
- Sales → Receivables;
- Customer → Receivables (receipts, advances);
- every posting module → the journal export.

---

## 8. What this review changed

- **Owner-brainstorm README:** now points to this review; the files are marked "reviewed, superseded".
- **Process model:** written as the replacement ([MOS-PROCESS-MODEL.md](../2-requirements/MOS-PROCESS-MODEL.md) and its local HTML view).
- **FRD, TDD and ADRs:** changes that came from modelling the processes in full are listed in [process model §14](../2-requirements/MOS-PROCESS-MODEL.md#14-gaps-found-and-fed-back). Only BR-PRC-05 was prompted by the brainstorm (question BQ-32).
- **Client questions:** BQ-29, 30, 32, 36, 40 and 42 add new questions or data requests. They go into the Phase 5 decision register and questionnaire; everything else merges with an existing client question.
