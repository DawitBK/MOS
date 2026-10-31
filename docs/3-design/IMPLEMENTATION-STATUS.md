# MOS on Odoo: implementation status against the FRD

- **Date:** 2026-10-31. **Source:** FRD 0.3.6 (every id row, 235), compared with the code in `mos-odoo` (40 addons, 223 models).
- **How to read the evidence.** *Built (cited)*: the addon's code names the requirement id and the addon's tests pass. *Built*, *Partial*, *GAP*: checked by hand against the models, data and reports on 2026-10-31, with what was found. This table does **not** yet re-prove each acceptance criterion in a browser. The process model, TDD, database design, sprint plan and ADRs are audited separately (see the end).

**Totals:** Built 227, Later 4, Out of v1 2, Partial 2

## Open items (gaps and partials)

| Id | Status | What is missing or different |
|---|---|---|
| FR-PLT-047 | Partial | Odoo import with its Test run for masters; counts import (mos_counts); opening stock with lots, dates and costs through mos.stock.receipt (opening balance). Open supplier and customer balances at go-live have no import yet |
| FR-RPT-002 | Partial | Filters and export on lists; not every report has a date range and site filter |

## FR-AP

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-AP-001 | Supplier invoice capture: supplier, invoice number (normalised for duplicate detection), date, lines linked to PO lines  | v1 (M2–M4) · standard depth: p | Built (cited) | mos_payables |
| FR-AP-002 |  Three-way match (PO ↔ GRN ↔ invoice) for stock items; two-way (PO ↔ invoice) for services and charges. Matching uses th | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-003 | Tolerances (installation default, optionally per category or supplier): price % and absolute (both directions), and invo | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-004 | Match exceptions are resolved through the approval matrix: accept the variance (posted as PPV), request a credit note, o | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-005 |  Quality debit note (gap-free number, printable), created automatically when an invoice exceeds the payable quantity or  | v1 (M2–M4) · standard depth: p | Built (cited) | mos_payables |
| FR-AP-006 | Self-billing: MOS generates the purchase voucher (gap-free number) from the GRN for flagged suppliers, including deducti | v1 (pull-in; else v1.1) | Built (cited) | mos_finance_plus, mos_payables |
| FR-AP-007 |  PV (payment request) , numbered `PV-…` at submission, raised by APC or drafted by the system (FR-INB-003). Header: requ | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-008 |  Payment recording against an approved PV: • payment number and date; • method, which must equal the PV's; • the company | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-009 | Supplier balance, statement (opening, transactions, closing) and ageing with configurable buckets as of any date | v1 (M2–M4) | Built (cited) | mos_finance_plus, mos_payables |
| FR-AP-010 |  Accounting export: journals are generated at every posting and mapped to external account codes through a configurable  | v1 (M2–M4) CSV · Later: packag | Built (cited) | mos_finance_plus, mos_payables |
| FR-AP-011 | Advances/prepayments against POs and payment runs (batch proposals). Later: cheque printing | v1.1 advances, payment runs ·  | Built (cited) | mos_finance_plus, mos_payables |
| FR-AP-012 |  Cheque register per company bank account: cheque numbers unique per account; spoiled or voided cheques recorded (number | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-013 |  Gross, VAT, WHT and net on PVs and payments: each PV line and payment shows gross, VAT, withholding and net. The WHT th | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-014 |  PV lifecycle (§1.5): DRAFT → SUBMITTED → APPROVED → PARTIALLY_PAID → PAID; REJECTED; CANCELLED, with an ageing report o | v1 (M2–M4) | Built (cited) | mos_payables |
| FR-AP-015 |  Company paying accounts: bank accounts, cash boxes and mobile-money wallets, each with its own export account code, cur | v1 (M1–M4) | Built (cited) | mos_payables |
| FR-AP-016 |  Cheque books: books registered by number range per bank account, with gap detection (every leaf is issued, voided or re | v1 (pull-in; else v1.1) | Built | Cheque books with gap detection; leaves can be cleared (with the day), marked stale (by hand or by the daily job after a configured age) or reported missing (mos_payables) |
| FR-AP-017 |  Tax registers per period, exportable to CSV/XLSX: input VAT by supplier and invoice; withholding by supplier TIN and ce | v1 (pull-in; else v1.1) · outp | Built (cited) | mos_finance_plus, mos_payables |
| FR-AP-018 |  Self-billing rules: only suppliers flagged for self-billing; no input VAT unless the supplier is VAT-registered; the vo | v1 (pull-in; else v1.1) | Built (cited) | mos_payables |
| FR-AP-019 |  GRNI reconciliation report as of any date: posted GRNs not yet invoiced, invoices not yet received, and unresolved invo | v1 (pull-in; else v1.1) | Built (cited) | mos_finance_plus, mos_payables |
| FR-AP-020 |  Optional approval of every supplier invoice (a setting; the default is that only match variances need approval, FR-AP-0 | v1 (M3–M4) | Built (cited) | mos_documents |

## FR-GATE

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-GATE-001 | Master data: gate posts (direction, allowed purposes), weighbridges (capacity, division, minimum weight, calibration cer | v1 (M3–M4) | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-002 | Blacklist for vehicles, drivers and transporters with severity BLOCK or WARN, reason, validity dates, approval to add or | v1 (pull-in; else v1.1) | Built (cited) | mos_gate |
| FR-GATE-003 | Guard shifts per gate post: open, members, close. Gate actions need an open shift. v1.1: handover checklist (vehicles on | v1 (M3–M4) · v1.1: handover ch | Built (cited) | mos_gate |
| FR-GATE-004 | Expected arrivals generated automatically from approved PO delivery schedules, transfer dispatches and (with sales) rele | v1 (M3–M4) · v1.1: no-show aut | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-005 | Arrival registration by plate search (scan or type): picks the matching expected arrival or registers a walk-in with a p | v1 (M3–M4) | Built (cited) | mos_gate, mos_gate_plus, mos_operations_ui |
| FR-GATE-006 | Visit purposes (behaviour classes): inbound PO delivery, inbound transfer, outbound transfer, outbound purchase return,  | v1 (M3–M4) · Later: flow templ | Built (cited) | mos_gate, mos_gate_plus, mos_operations_ui |
| FR-GATE-007 | Gate entry captures vehicle, trailer, driver, transporter, supplier/customer, reference documents (PO or delivery order, | v1 (M3–M4) | Built (cited) | mos_gate, mos_operations_ui |
| FR-GATE-008 | One open visit per vehicle at a time | v1 (M3–M4) | Built (cited) | mos_gate, mos_operations_ui |
| FR-GATE-009 |  Weigh tickets (manual in v1): first and second weighing with gross/tare/net; the weight must be a multiple of the weigh | v1 (M3–M4) | Built (cited) | mos_gate, mos_operations_ui |
| FR-GATE-010 |  Blind second-person verification of manual weights: a different user re-enters the value without seeing the first; a mi | v1 (M3–M4) | Built (cited) | mos_gate |
| FR-GATE-011 | Direction logic: inbound loaded = first gross, second tare; outbound = first tare, second gross. An inbound first weight | v1 (M3–M4) | Built (cited) | mos_gate |
| FR-GATE-012 | Tolerance rules (most specific wins: site/purpose/item category/item/partner): net vs declared, net vs expected, tare vs | v1 (M3–M4) | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-013 | Stored tare per vehicle (+ trailer combination) with validity. Its use is allowed only where the purpose and tolerance r | v1 (M3–M4) | Built (cited) | mos_gate |
| FR-GATE-014 | Re-weighing creates a superseding weighing with a reason and never deletes the original. No re-weighing after the ticket | v1 (M3–M4) | Built (cited) | mos_gate |
| FR-GATE-015 | Payable weight basis per supplier/PO: our weight, supplier's weight, or lower of the two. Weight disputes are recorded w | v1 (M3–M4) | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-016 |  Exit clearance evaluates, and records every attempt with results: no open blocking hold; all mandatory steps done; ever | v1 (M3–M4) | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-017 | Overridable clearance checks (e.g. seal missing, plate mismatch explained) need an SSUP override with reason code and co | v1 (M3–M4) | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-018 | Exit pass (printable, QR) issued when the visit reaches an exit-eligible state; scanned or typed at exit | v1 (M3–M4) | Built (cited) | mos_gate |
| FR-GATE-019 | Material gate passes: returnable / non-returnable, out/in, reason, requester, lines (item or asset tag/serial, qty, esti | v1 (pull-in; else v1.1) | Built | mos.gate.pass with approval, rejection, validity and expiry job, partial out and return, asset tag and serial, estimated value, and the posted-document rule for stock items on a final pass (mos_gate, mos_inventory; completed 2026-10-31) |
| FR-GATE-020 | Service/contractor visits: persons count, work-permit reference, tools declared in and verified out; minimal people-visi | v1.1 | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-021 | Yard board: vehicles on site with purpose, partner, current step, time in step, total dwell and flags (hold, blacklist w | v1 (pull-in; else v1.1) · v1.1 | Built | Yard board (mos_gate_plus) |
| FR-GATE-022 | Incidents (seal broken, tamper suspected, document forgery, blacklist attempt, safety, theft, unauthorised entry, spilla | v1 (pull-in; else v1.1) | Built | mos.gate.incident (mos_gate) |
| FR-GATE-023 |  Paper mode when the system or network is down: registered, pre-numbered paper slip books per gate post, and rules for w | v1 (pull-in; else v1.1) · v1.1 | Built (cited) | mos_gate_plus |
| FR-GATE-024 |  Back-entry workbench: paper slips entered after recovery with the slip's actual time kept separate from entry time, sli | v1 (pull-in; else v1.1) · v1.1 | Built (cited) | mos_gate_plus |
| FR-GATE-025 | Gate reports: daily gate register, weigh ticket register (incl. voided/superseded), turnaround and dwell times, override | v1 (M3–M4) · v1.1: extended KP | Built (cited) | mos_gate, mos_gate_plus |
| FR-GATE-026 |  Edge agent weighbridge capture: live weight display, stability indication, capture only when stable, above minimum and  | Later | Later | Edge-agent capture |
| FR-GATE-027 | Direct label/ticket printing to thermal/ZPL printers via the edge agent | Later | Later | Thermal printing through an agent |
| FR-GATE-028 | Multi-document trucks (one truck, several POs or delivery orders, intermediate weighings with allocation) | v1.1 | Built (cited) | mos_gate_multi |
| FR-GATE-029 | ANPR plate capture, barrier/traffic-light control, RFID driver cards, transporter pre-registration, SMS to drivers, offl | Later | Later | ANPR, RFID, SMS |
| FR-GATE-030 |  Weigh tickets are numbered without gaps per weighbridge; voided tickets keep their number | v1 (M3–M4) | Built (cited) | mos_gate |

## FR-INB

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-INB-001 | The system runs flows A, B and C end to end with no re-keying: data captured at the gate or inspection (supplier, PO, we | v1 (M3–M4) | Built (cited) | mos_inbound |
| FR-INB-002 |  On acceptance, the GRN is prepared automatically : at the second weighing (flow A), from the decided inspection lot (fl | v1 (M3–M4 gate off · M3–M4 at  | Built (cited) | mos_flows |
| FR-INB-003 |  The supplier payment process is triggered automatically: when the GRN posts, a PV draft is created from PO + GRN (+ mat | v1 (M2–M4) | Built (cited) | mos_flows |
| FR-INB-004 | Unloading is authorised only after pre-flight checks that could otherwise make the GRN fail later: PO line open with eno | v1 (M3–M4) | Built (cited) | mos_flows |
| FR-INB-005 | If GRN preparation or confirmation fails (e.g. over tolerance), the weighing is kept, the visit goes to RECEIPT_EXCEPTIO | v1 (M3–M4) | Built (cited) | mos_flows |
| FR-INB-006 | Missing finance setup (tax profile, bank account for a transfer-paid supplier) never blocks receiving. The PV step creat | v1 (M3–M4) | Built (cited) | mos_flows |
| FR-INB-007 | Every step is idempotent: double-clicks, retries and duplicate events never create duplicate weighings, inspection lots, | v1 (M3–M4) | Built (cited) | mos_inbound |
| FR-INB-008 | The failure and exception cases in §9.5 are handled exactly as specified for the modules enabled. Each has an acceptance | v1 (M3–M4) · standard depth: p | Built | The 34 failure cases of 9.5 have scenarios in mos_flows (exit without tare, photo pending, and so on); the receipt-draft limit now opens an exception |
| FR-INB-009 | Supplier notifications (where email is configured): arrival acknowledged, rejection note with results, receipt with dedu | v1 (pull-in; else v1.1) | Built (cited) | mos_notify |
| FR-INB-010 |  Automation mode settings per installation: GRN generation (confirm / automatic) and PV (draft for APC / auto-submit). C | v1 (M2–M4) · v1.1: per site | Built (cited) | mos_flows |

## FR-INV

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-INV-001 | Real-time stock by item × storage unit × lot × status, with quantity and value, filterable by site, warehouse and catego | v1 (M2–M3) | Built (cited) | mos_platform_plus |
| FR-INV-002 | Stock statuses QUARANTINE, AVAILABLE, BLOCKED, DAMAGED, REJECTED. Only AVAILABLE stock can be issued to consumption, pro | v1 (M2–M3) | Built | Five statuses on the place stock sits in (available, quarantine, blocked, damaged, rejected); only available stock can be issued, sold, transferred or reserved; scrap can leave damaged and rejected places (mos_inventory; fixed 2026-10-31, the first audit had marked it built with two statuses missing) |
| FR-INV-003 | Status changes are documents with a reason. QUARANTINE → AVAILABLE only through a QC decision. AVAILABLE ↔ BLOCKED by WS | v1 (M2–M3) | Built | Status changes are documents with a reason and an inventory manager approving; damaged recovers to available only; rejected has no status exit; only quarantine is rejected; on-hand is unchanged by a status change (mos_inventory) |
| FR-INV-004 | Lot tracking: lot number (auto per the batch pattern, or the supplier's), supplier lot, manufacture and expiry dates, at | v1 (M2–M3) · standard depth: p | Built (cited) | mos_stock_plus |
| FR-INV-005 | Goods receipt kinds: from PO (Procurement), from another site (transfer), from production output (with production), from | v1 (M2–M3) | Built | Receipts from PO, transfer, production, customer return, adjustment, and mos.stock.receipt for miscellaneous receipts (reason, cost, restricted to inventory managers) and the opening balance |
| FR-INV-006 | Goods issue: to cost centre/department (consumption), to production (with production), to customer via delivery (with sa | v1 (M2–M3) | Built | mos.stock.issue: consumption by a department or scrap, available stock only, approval by the matrix, valued at average cost (mos_inventory) |
| FR-INV-007 | Intra-site transfer (unit to unit) as one posting. Inter-site transfer as a transfer order → dispatch (stock enters in-t | v1 (M2–M3) · standard depth: p | Built (cited) | mos_stock_plus |
| FR-INV-008 | Stock adjustments (+/−) with adjustment type, mandatory reason code, optional reference document and attachment, and app | v1 (M2–M3) | Built | mos.stock.adjustment with reason and value-routed approval (mos_inventory) |
| FR-INV-009 | Cycle counts (by storage unit/item selection) and full physical counts. Count mode per count: FREEZE (postings to the co | v1 (M2–M3) · standard depth: p | Built (cited) | mos_counts |
| FR-INV-010 | Valuation: moving weighted average per item at company level in v1. The ledger keeps each receipt's cost, so a FIFO opti | v1 (M2–M3) MWA · Later: FIFO o | Built (cited) | mos_inventory |
| FR-INV-011 | Negative stock is never allowed. Where a process must not be blocked by a shortage (production backflush, FR-MFG-006), t | v1 (M2–M3) | Built (cited) | mos_inventory |
| FR-INV-012 | Inventory period close: checklist (no unposted documents dated in the period, no open counts, in-transit report reviewed | v1 (M2–M3) | Built (cited) | mos_inventory, mos_platform_plus |
| FR-INV-013 | Reorder report: items below reorder level (FR-MD-010 precedence) with a suggested quantity up to maximum level, which ca | v1 (pull-in; else v1.1) | Built (cited) | mos_inventory |
| FR-INV-014 |  Stock balance per material × warehouse/storage unit × batch with columns *opening, received, issued, adjusted, closing  | v1 (M2–M3) · v1.1: ageing, slo | Built (cited) | mos_inventory, mos_platform_plus, mos_stock_cards, mos_stock_plus |
| FR-INV-015 | Reservations and available-to-use: soft reservations from approved sales orders and released work orders; available-to-u | v1 (M4–M5) · v1.1: hard reserv | Built (cited) | mos_inventory |
| FR-INV-016 | Landed cost allocation (freight, duty) to receipts | Later | Built (cited) | mos_inventory |
| FR-INV-017 | Serial numbers and catch-weight items | Later | Built (cited) | mos_inventory |
| FR-INV-018 | Labels: PDF lot/storage-unit/pallet labels with barcode/QR. Every entry field that takes a lot, storage unit or item acc | v1 (pull-in; else v1.1) · Late | Built (cited) | mos_inventory |
| FR-INV-019 |  Silo and capacity control: each storage unit shows its current level (calculated from the ledger) against capacity and  | v1 (M2–M3) | Built (cited) | mos_inventory, mos_silo |
| FR-INV-020 |  Silo measurement: a dip or level reading (height or %) converts to volume via the silo's geometry table, then to mass v | v1 (pull-in; else v1.1) | Built (cited) | mos_inventory, mos_production_plus |
| FR-INV-021 |  Fumigation / treatment hold: stock in a silo, stack or lot can be put on hold with reason FUMIGATION (or other treatmen | v1 (pull-in; else v1.1) | Built (cited) | mos_production_plus |
| FR-INV-022 |  Receipt valuation: GRN value = payable quantity × quality-adjusted unit price (PO price after grade and deduction effec | v1 (M2–M3) | Built (cited) | mos_platform_plus |
| FR-INV-023 |  Bin card: for one bin and one item over a period: the bin's information (code, warehouse, storage location, item, capac | v1 (M2–M3) | Built (cited) | mos_stock_cards |
| FR-INV-024 |  Stock card: for one item across a warehouse over a period: current stock, the bins that hold it with their quantities,  | v1 (M2–M3) | Built (cited) | mos_stock_cards |

## FR-MD

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-MD-001 | Units of measure with dimensions (mass, volume, count, length, area, time) and same-dimension factors (kg, g, t, quintal | v1 (M1–M2) | Built | Odoo units of measure with categories and factors |
| FR-MD-002 | Item-specific conversions (e.g. 1 bag = 50 kg for wheat; 1 bale = 500 bags for packaging). Conversions referenced by any | v1 (M1–M2) | Built (cited) | mos_master_plus |
| FR-MD-003 | Items: system ID (auto) and user-defined material code, name, type (raw, packaging, semi-finished, finished, by-product, | v1 (M1–M2) | Built | Every material type, shelf life (a lot takes its expiry from it), packaging tare, lot tracking on the item form, unique item codes (mos_master_plus, mos_stock_plus) |
| FR-MD-004 | Item categories are hierarchical, and each carries an attribute schema that lots of that category must satisfy. The flou | v1 (pull-in; else v1.1) | Built (cited) | mos_stock_plus |
| FR-MD-005 | Business partners (organisation or individual) with roles supplier / customer / transporter, tax id (TIN; unique when pr | v1 (M1–M2) supplier, transport | Built (cited) | mos_master_plus |
| FR-MD-006 | Payment terms (days; basis invoice date / GRN date / end of month) | v1 (M1–M2) | Built | Odoo payment terms |
| FR-MD-007 | Tax codes (VAT, withholding, excise, other) with rate, base, threshold, recoverability, timing (at invoice / at payment) | v1 (M1–M2) | Built | Odoo taxes (rate, group, effective dates) |
| FR-MD-008 | Currencies with minor units, and exchange rates per date. One currency per document; the company currency is the default | v1 (M1–M2) | Built | Odoo currencies and rates |
| FR-MD-009 | Reason codes by domain (adjustment, scrap, status change, transit loss, count, gate rejection, re-weigh, dispute, overri | v1 (M1–M2) | Built (cited) | mos_core |
| FR-MD-010 |  Storage hierarchy: site → warehouse (physical facility: code, name, storekeeper, capacity and UoM, temperature/humidity | v1 (M1–M2) · standard depth: p | Built | Warehouse and storage-unit fields on stock.warehouse and stock.location (mos_master_plus, mos_inventory) |
| FR-MD-011 | Industry pack: installing a pack seeds items/categories, attribute schemas, QC parameters, specs, grade tables, deductio | v1 (grain/flour milling, M1–M4 | Built (cited) | mos_inventory |
| FR-MD-012 |  Bank master list (bank name, branch, SWIFT/BIC), selected on supplier and company bank accounts | v1 (M1–M2) | Built | Odoo bank list on supplier and company accounts |
| FR-MD-013 |  Product and service categories (tags) for classifying what suppliers provide, in addition to material categories | v1 (M1–M2) | Built (cited) | mos_stock_plus |
| FR-MD-014 |  Individual suppliers (e.g. farmers and traders without a company): TIN optional; national ID or other identifier; flagg | v1 (M1–M2) | Built (cited) | mos_master_plus |

## FR-MFG

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-MFG-001 | Work centres (e.g. cleaning, milling line 1, packing line), with line-side and output storage units, and shift calendars | v1 (M4–M5, lean) | Built (cited) | mos_manufacturing |
| FR-MFG-002 | Routings (versioned): operations with work centre, run rate, setup, reporting point | v1.1 | Built (cited) | mos_planning_plus |
| FR-MFG-003 |  Recipes/BOMs in one model , versioned and approved: inputs (qty per base, scrap %, issue method manual/backflush) and o | v1 (M4–M5) | Built (cited) | mos_production_plus |
| FR-MFG-004 | MPS-lite and MRP-lite (demand-driven suggestions, netting, pegging) | Later | Built (cited) | mos_planning_plus |
| FR-MFG-005 | Work orders: standard (batch), campaign (a milling run per shift or day, settled at its end) and packing orders . Releas | v1 (M4–M5) · v1.1: rework, rep | Built (cited) | mos_production, mos_production_plus |
| FR-MFG-006 | Material issue: silo draw-down by quantity (lots allocated first-in-first-out, FR-INV-019), scan issue for packaged inpu | v1 (M4–M5) | Built (cited) | mos_production |
| FR-MFG-007 | Output recording by lot with lot attributes (e.g. flour type, grade), labels, and initial status QUARANTINE when finishe | v1 (M4–M5) | Built (cited) | mos_production |
| FR-MFG-008 | Finished-goods QC and release using the QC engine (FR-QC-019, FR-QC-026): sample per lot or per shift; decision release  | v1 (M4–M5) · v1.1: in-process  | Built (cited) | mos_manufacturing, mos_production |
| FR-MFG-009 |  Traceability: backward and forward trace through work orders, including the silo lots allocated first-in-first-out to e | v1 (M4–M5) · v1.1: blend genea | Built (cited) | mos_manufacturing, mos_production |
| FR-MFG-010 | Full actual costing: labour and overhead absorption, variance analysis (usage, mix, yield, absorption) | Later (v1: FR-MFG-014) | Built (cited) | mos_planning_plus, mos_production |
| FR-MFG-011 | Supervisor screens for the line: my orders today, issue by scan or quantity, record output in ≤ 3 interactions, downtime | v1 (M4–M5, responsive) | Built (cited) | mos_production |
| FR-MFG-012 | Shift logs and downtime (non-overlapping per work centre, with reason codes). v1.1: OEE from manual logs with a data-com | v1 (pull-in; else v1.1) · v1.1 | Built (cited) | mos_manufacturing, mos_production |
| FR-MFG-013 | Production reports: extraction rate (flour output ÷ wheat input) per run and per day, yield and by-product % against the | v1 (M4–M5, lean) · standard de | Built (cited) | mos_manufacturing, mos_production |
| FR-MFG-014 |  Material-cost settlement (the v1 valuation of production output): the work order's cost = issued inputs at moving avera | v1 (M4–M5) | Built (cited) | mos_production |
| FR-MFG-015 |  Milling run: the grist (blend of wheat lots or silos with target ratios) is recorded, as are tempering water (litres, c | v1 (M4–M5) · standard depth: p | Built (cited) | mos_production, mos_production_plus |
| FR-MFG-016 |  Packing: bulk flour is packed into bag SKUs (e.g. 50 / 25 / 10 / 5 kg); bags, labels and thread are backflushed; the lo | v1 (M4–M5) | Built (cited) | mos_manufacturing |
| FR-MFG-017 |  Fortification dosing (target vs actual premix per tonne), if mandatory for the client's products | v1 (pull-in; else v1.1) | Built (cited) | mos_manufacturing |
| FR-MFG-018 |  Planner's demand and material view: per finished item, the open sales-order quantity, available-to-use stock, planned o | v1 (pull-in; else v1.1) | Built (cited) | mos_manufacturing |

## FR-PLT

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-PLT-001 | The system isolates every tenant's data completely. No user, API key or background job can read or write another tenant' | Automated tests with two tenan | Out of v1 | Appendix F, multi-tenant (ADR-0017: one company, one database) |
| FR-PLT-002 | The organisation hierarchy is company → site → warehouse → storage unit. v1 holds exactly one company; every document he | v1 (M1–M2) | Built (cited) | mos_core |
| FR-PLT-003 | The company has a base currency, fiscal-year start, time zone and rounding mode. Stock is valued at company level in v1  | v1 (M1–M2) | Built | Odoo company currency and fiscal year; site time zone in mos_core; ETB set by mos_dashboard post-init (not yet committed) |
| FR-PLT-004 | Each site has a time zone. Business dates such as "today" are computed on the server in the site's time zone | v1 (M1–M2) | Built (cited) | mos_core, mos_dashboard, mos_production |
| FR-PLT-005 | A tenant can run in dedicated mode (single tenant per installation) or shared mode, with no functional differences | The same build passes the same | Out of v1 | Appendix F, shared mode |
| FR-PLT-006 | Module enablement per installation (feature flags). The Gate module can be disabled independently. Its visit flow (visit | v1 (M1–M2) | Built | Modules are installed per installation (Gate is its own addon); receiving and PV automation modes in mos_flows |
| FR-PLT-007 |  Departments (hierarchical, with a head), and user profile attributes employee ID and home department. Documents record  | v1 (M1–M2) | Built | mos.department: hierarchy with a head and people; home department on the user; the department picked from the list on requests and purchase orders; employee ID and sites on the user form (mos_core, mos_procurement, mos_request_forms) |
| FR-PLT-008 |  Installation model: one installation serves one company, with its own database, a company profile (legal name, TIN/VAT  | v1 (M1–M2) | Built | Odoo company profile (name, VAT, address, logo, currency); one company, many sites (mos_core) |
| FR-PLT-010 | Users sign in with a local MOS username and password (default, FR-PLT-018) and/or through an external OpenID Connect ide | v1 (M1) · standard depth: pull | Built (cited) | mos_auth_oidc |
| FR-PLT-011 | Users are created by the administrator, or by accepting an invitation. A first SSO sign-in succeeds only for a user who  | v1 (M1) · standard depth: pull | Built (cited) | mos_auth_oidc |
| FR-PLT-012 | Roles are assigned per user, optionally scoped to one or more sites. A user sees and acts only on documents within their | v1 (M1) | Built | Site scoping on users and record rules (mos_core, tests) |
| FR-PLT-013 | Optionally, IdP groups map to MOS roles. Mapped roles sync at each SSO sign-in | v1.1 | Built (cited) | mos_platform_plus |
| FR-PLT-014 | Permission changes take effect on the user's next request, with no re-login | v1 (M1) | Built | Odoo checks groups on every request |
| FR-PLT-015 | Sessions expire after 8 h idle / 12 h absolute (configurable). Sign-out ends the MOS session and, for SSO users, the IdP | v1 (M1) | Built (cited) | mos_platform_plus |
| FR-PLT-016 | API keys and service accounts with scopes, expiry, rotation and last-used tracking (for integrations and on-site devices | Later | Later | Odoo ships per-user API keys |
| FR-PLT-017 | An access-review export lists every user, role, scope, grant date and grantor | v1 (pull-in; else v1.1) | Built (cited) | mos_platform_plus |
| FR-PLT-018 |  Local accounts: username + password with a configurable policy (minimum length, complexity); lockout after repeated fai | v1 (M1) · v1.1: breached-passw | Built | Odoo password policy (minimum length) plus a letter-and-digit rule; lockout through Odoo's own login cooldown (5 failures, 5 minutes by default on a new installation); an administrator's reset forces a change at the next sign-in; two-step sign-in can be required for sensitive roles (mos_platform_plus) |
| FR-PLT-019 |  Operation unit selection: right after sign-in, the user picks an operation unit ( a site , pending client confirmation) | v1 (M1) | Built (cited) | mos_platform_plus |
| FR-PLT-020 | Business documents follow the lifecycles in §1.5. Commitment documents close instead of posting; posting documents post  | v1 (M1) | Built | mos.posted rules and approval-controlled lifecycles (mos_core, mos_approvals) |
| FR-PLT-021 | Posted documents and ledger rows cannot be edited or deleted by anyone, including administrators through the application | v1 (M1) · standard depth: pull | Built (cited) | mos_core, mos_production |
| FR-PLT-022 | Corrections of posted documents are made only through a reversal document that references the original. Reversal is bloc | v1 (M1) | Built (cited) | mos_core |
| FR-PLT-023 |  Approval matrix per document type, defined as data: rows of amount band × site × item category, plus named flags (sole- | v1 (M1–M2) · v1.1: general con | Built (cited) | mos_approvals |
| FR-PLT-024 | Parallel steps within a stage, with ALL or QUORUM(n) completion | v1.1 (v1: sequential stages, o | Built (cited) | mos_approvals |
| FR-PLT-025 | Approvers can approve, reject (comment mandatory) or request changes (comment mandatory; the document returns to DRAFT a | v1 (M1–M2) · standard depth: p | Built (cited) | mos_approvals |
| FR-PLT-026 | Delegation: a user can delegate approvals for a date range, optionally limited by document type and maximum amount | v1 (pull-in; else v1.1) | Built (cited) | mos_approvals |
| FR-PLT-027 | Escalation: overdue steps trigger a reminder, then reassignment to the matrix row's fallback role | v1 (pull-in; else v1.1) | Built (cited) | mos_approvals |
| FR-PLT-028 |  Segregation of duties rules are enforced (list in §15.1). Conflicted users are excluded from approval tasks and blocked | v1 (M1–M2) | Built (cited) | mos_approvals |
| FR-PLT-029 | At submission, the system verifies that every approval step has at least one eligible approver after SoD exclusion. Othe | v1 (M1–M2) | Built (cited) | mos_approvals |
| FR-PLT-030 | An approvals inbox shows all pending tasks across modules with filters (status, requester, date range), SLA due time, KP | v1 (M1) · standard depth: pull | Built (cited) | mos_approvals, mos_dashboards |
| FR-PLT-031 |  Status is action-driven. Document and master-record status is shown as a badge and changes only through actions (Submit | v1 (M1) | Built (cited) | mos_approvals |
| FR-PLT-032 |  Bulk approve / reject of selected inbox items. Each item is still checked individually (authority, SoD, content hash);  | v1 (pull-in; else v1.1) | Built (cited) | mos_approvals |
| FR-PLT-040 | Number series per document type (optionally per site), with a configurable pattern and reset (never, fiscal year, calend | v1 (M1–M2) | Built | Odoo sequences with a restart policy per series (never, each fiscal year, calendar year, month) and the pattern tokens of Odoo plus %(fy)s for the fiscal year (mos_core) |
| FR-PLT-041 | Legally or audit-relevant documents are numbered without gaps : GRN, self-billed voucher, debit note, PV, payment, WHT c | v1 (M1–M2) | Built (cited) | mos_core |
| FR-PLT-042 | Fiscal years and periods, each module with status OPEN / SOFT_CLOSED / CLOSED. Postings are allowed only into open perio | v1 (M1–M2) | Built (cited) | mos_platform_plus |
| FR-PLT-043 | Backdating windows per document type (e.g. receipts up to 3 days back, issues 0). Posting dates can never be in the futu | v1 (M1–M2) | Built | mos.date.policy and mos.date.guard (mos_controls) |
| FR-PLT-044 | Attachments (photos, PDFs, spreadsheets) on any document or master record: type-checked by content (not extension), size | v1 (M1) · standard depth: pull | Built (cited) | mos_documents |
| FR-PLT-045 | In-app notifications for approval tasks, rejections, QC decisions, arrivals, exceptions and expiring supplier documents. | v1 (M1) · standard depth: pull | Built | Chatter, approval inbox, exception queue and supplier notices in the application; email copies go out through Odoo mail when a relay is configured, and each person chooses inbox-only in their preferences (the opt-out) |
| FR-PLT-046 | A tamper-evident audit log records who did what, when and from where for every create, submit, approve, post, reverse, o | v1 (M1) · standard depth: pull | Built (cited) | mos_core |
| FR-PLT-047 | Import from CSV/XLSX templates for master data and opening balances (items, UoM conversions, suppliers, customers, stora | v1 (M1–M2) · standard depth: p | Partial | Odoo import with its Test run for masters; counts import (mos_counts); opening stock with lots, dates and costs through mos.stock.receipt (opening balance). Open supplier and customer balances at go-live have no import yet |
| FR-PLT-048 | Every list screen exports to XLSX/CSV, respecting the user's scopes and filters | v1 (M1) | Built | Odoo list export to XLSX/CSV, record rules respected |
| FR-PLT-049 | Documents print to PDF from company-branded templates (PO, GRN, weigh ticket, gate pass, exit pass, sample label, reject | v1 (M1–M5) | Built | All 24 documents print from the company layout, each with a QR code that carries the document and a database-only hash (mos_print adds the nine that were missing) |
| FR-PLT-050 | "Process exceptions" work queue: business failures in automated steps (e.g. "cannot draft PV: supplier has no approved p | v1 (M1) | Built (cited) | mos_controls |
| FR-PLT-051 | Global search by document number, item code/name, supplier/customer, plate number, lot number | v1 (pull-in; else v1.1) | Built (cited) | mos_controls |
| FR-PLT-052 | UI language and calendar display are pluggable: English/Gregorian in v1; Amharic and Ethiopian-calendar display as a lat | v1 (M1) hooks · Later: locale  | Built (cited) | mos_controls, mos_platform_plus |
| FR-PLT-053 |  Masters are deactivated, never deleted, once referenced by any document. Deactivation is refused while open balances or | v1 (M1–M3) | Built (cited) | mos_controls, mos_platform_plus |
| FR-PLT-054 |  Approval history is immutable. Approval chains, decisions and comments cannot be edited or deleted, including for rejec | v1 (M1–M2) | Built (cited) | mos_approvals |
| FR-PLT-055 |  Date sanity checks: required date ≥ request date (PR); delivery date ≥ PO date; expiry ≥ manufacture date; cheque date  | v1 (M1–M2) | Built | mos.date.policy (mos_controls) and field checks on requests, lots, contracts |

## FR-PRC

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-PRC-001 | Supplier registration as a 5-step wizard (from the client draft), used by procurement staff in v1: (1) Basic: supplier I | v1 (M2–M3) · standard depth: p | Built (cited) | mos_procurement, mos_procurement_plus |
| FR-PRC-002 | Only APPROVED suppliers without a payment block can receive POs, deliver goods and be paid | v1 (M2–M3) | Built (cited) | mos_procurement |
| FR-PRC-003 | Supplier documents nearing expiry notify procurement 30 and 7 days ahead. v1.1: expired mandatory documents suspend the  | v1 (pull-in; else v1.1) · v1.1 | Built (cited) | mos_procurement |
| FR-PRC-004 |  Bank details are never edited in place. A change creates a new pending bank account and a change request that needs one | v1 (M2–M3) | Built (cited) | mos_procurement |
| FR-PRC-005 | Bank account numbers are stored encrypted and masked in the UI. Revealing the full number needs a permission and is audi | v1 (M2–M3) | Built (cited) | mos_procurement |
| FR-PRC-006 | Approved supplier list per item or category (optionally per site), with enforcement per category: none / warn / block, a | v1 (pull-in; else v1.1) | Built (cited) | mos_procurement |
| FR-PRC-007 | Purchase requisitions. Header: request number (auto), request date, required date (≥ request date), priority (low / medi | v1 (M2–M3) · standard depth: p | Built (cited) | mos_procurement, mos_request_forms |
| FR-PRC-008 | Sourcing policy per category and amount band: minimum number of quotations (e.g. 3) and whether sole-source is allowed.  | v1 (pull-in; else v1.1) | Built (cited) | mos_procurement, mos_procurement_plus |
| FR-PRC-009 | RFQ workflow: invite suppliers, record received quotations (price, lead time, validity, attachment), side-by-side compar | Later | Built (cited) | mos_procurement |
| FR-PRC-010 | Purchase orders. Header: PO number (auto), PO date, linked PR (required by default, as in the client draft; POs without  | v1 (M2–M3) · standard depth: p | Built (cited) | mos_procurement |
| FR-PRC-011 | PO amendments create a new revision that is re-approved per the matrix (e.g. quantity decrease or date change can auto-a | v1 (pull-in; else v1.1) · v1.1 | Built (cited) | mos_procurement |
| FR-PRC-012 | PO closure: auto-close when fully received (within tolerance) and invoiced; manual short-close with a reason; cancel onl | v1 (M2–M3) | Built (cited) | mos_procurement, mos_procurement_plus |
| FR-PRC-013 | Goods receipt (GRN) against a PO, prepared by the system and confirmed by the storekeeper ("Save & Generate GRN"; automa | v1 (M2–M3) | Built | action_prepare_receipt (mos_flows); confirmed by the storekeeper |
| FR-PRC-014 | Receipt tolerances: over-receipt beyond tolerance follows the line's excess policy: BLOCK (reload the excess) or REQUIRE | v1 (M2–M3) | Built (cited) | mos_procurement |
| FR-PRC-015 | Supplier returns from REJECTED or AVAILABLE stock, valued at the original receipt cost, creating a debit expectation for | v1 (M2–M3) | Built | mos.supplier.return (mos_procurement) |
| FR-PRC-016 | GRN PO-reassignment: fix a GRN posted against the wrong PO (same supplier and item) in one controlled document. (In v1 t | v1.1 | Built (cited) | mos_procurement |
| FR-PRC-017 | Procurement reports: open PRs/POs, PR ageing, PO status, outstanding deliveries, price history per item, spend by suppli | v1 (M2–M3) · v1.1: scorecard,  | Built (cited) | mos_procurement_plus |
| FR-PRC-018 | Purchase contracts / blanket agreements with call-offs and contract-level quality rules | Later | Built (cited) | mos_planning_plus, mos_procurement |
| FR-PRC-019 | Supplier self-service portal (quotations, delivery schedules, invoice upload, statements) | Later | Built (ahead of plan) | mos_supplier_portal |
| FR-PRC-020 |  Supplier self-registration via invite link: procurement sends a single-use, expiring link; the supplier completes the s | v1.1 | Built (cited) | mos_procurement |
| FR-PRC-021 |  Bagged bulk receipts: bag count and bag type (tare per bag type) are captured at weighing or inspection. Payable and st | v1 (M2–M3) | Built (cited) | mos_procurement, mos_procurement_plus |
| FR-PRC-022 |  Service and non-stock receipt confirmation: the requester (or buyer) confirms service and non-stock PO lines as deliver | v1 (pull-in; else v1.1) | Built (cited) | mos_procurement, mos_procurement_plus |
| FR-PRC-023 |  Supplier delivery entry: a delivery has its delivery-note number, supplier and purchase order, delivery date, vehicle,  | v1 (M3) | Built (cited) | mos_deliveries, mos_procurement |

## FR-QC

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-QC-001 | Test method library (code, reference standard, description, document) and parameter library: quantitative (UoM, decimals | v1 (M3–M4) | Built (cited) | mos_quality |
| FR-QC-002 | Specifications per item or category, optionally per supplier and/or site, and per purpose (incoming, retest, in-process, | v1 (M3–M4) · v1.1: replicates, | Built (cited) | mos_lab |
| FR-QC-003 | Specifications are versioned and approved. A new version is required for any change. The spec in force is frozen onto ea | v1 (M3–M4) | Built (cited) | mos_lab |
| FR-QC-004 | QC policy per item or category (optionally per site): inspection required; inspection point = BEFORE UNLOADING (at the g | v1 (M3–M4) | Built (cited) | mos_quality |
| FR-QC-005 | Sampling plans: fixed count, or percentage of containers/bags; composite or individual samples. v1.1: √n+1 and ISO 2859  | v1 (M3–M4) · v1.1: statistical | Built (cited) | mos_lab |
| FR-QC-006 | Inspection lots are created: automatically at first weighing (gate on, before-unloading items); manually by LAB against  | v1 (M3–M4) | Built | Inspection creation in mos_lab, mos_flows, mos_deliveries |
| FR-QC-007 | Samples: unique sample number, printed label with barcode, drawn by/at, quantity, container, storage location, type (pri | v1 (M3–M4) | Built | Samples, label report, scan entry (mos_lab, mos_quality) |
| FR-QC-008 | Result entry grid per sample, keyboard-first, with live pass/fail indication. Results are never overwritten : a correcti | v1 (M3–M4) | Built (cited) | mos_lab |
| FR-QC-009 | Automatic evaluation against the frozen spec: per-line pass/fail and a suggested lot outcome: PASS / PASS WITH DEDUCTION | v1 (M3–M4) | Built (cited) | mos_lab |
| FR-QC-010 | Out-of-spec handling: a result can be invalidated with an assignable cause and re-tested from a retest or retention samp | v1 (M3–M4) · standard depth: p | Built | Invalidate wizard and retests (mos_lab) |
| FR-QC-011 | Concessions: accept as-is or accept with deduction despite a failure, with justification, routed through the approval ma | v1 (pull-in; else v1.1) | Built (cited) | mos_master_plus |
| FR-QC-012 | Usage decision: ACCEPT, ACCEPT WITH DEDUCTION, ACCEPT UNDER CONCESSION, PARTIAL ACCEPT (accepted and rejected quantities | v1 (M3–M4) · standard depth: p | Built (cited) | mos_lab |
| FR-QC-013 |  Quality price and weight rules (versioned rule sets attached to PO lines or to the approved supplier list): per paramet | v1 (M3–M4) | Built (cited) | mos_lab |
| FR-QC-014 | The deduction result (payable quantity, price adjustment, breakdown, rule-set version) is stored with the decision as im | v1 (M3–M4) | Built (cited) | mos_lab |
| FR-QC-015 | Changing a decision before unloading/receipt is allowed with a reason. After stock or financial effects, it requires QAM | v1 (M3–M4) | Built (cited) | mos_lab |
| FR-QC-016 | Retention samples: storage location, retain-until date, disposal list and disposal record | v1 (pull-in; else v1.1) | Built | Retention samples with retain-until, due list, discard record (mos_lab, mos_controls) |
| FR-QC-017 | Supplier Certificate of Analysis as an attachment. When the policy requires a CoA, a decision cannot be made without one | v1 (pull-in; else v1.1) | Built (cited) | mos_lab |
| FR-QC-018 | Lab dashboards: pending samples by age and priority, OOS investigations, decisions today, rejection reasons. v1.1: suppl | v1 (M3–M4) · v1.1: supplier tr | Built (cited) | mos_quality |
| FR-QC-019 | In-process and finished-goods QC with batch release (hold → release / regrade / reject), using the same engine. v1.1: a  | v1 (M4–M5) · v1.1: generated C | Built (cited) | mos_manufacturing |
| FR-QC-020 | Instrument register and calibration records; result entry blocked on out-of-calibration instruments. Later: instrument r | v1.1 · Later: import | Built (cited) | mos_lab, mos_quality |
| FR-QC-021 | Skip-lot inspection and calculated parameters | Later | Built (cited) | mos_lab |
| FR-QC-022 |  Grading schemes per item or category (versioned): an ordered list of grades (e.g. Grade 1, 2, 3, Reject), each defined  | v1 (M3–M4) | Built | Grade bands on specs (mos_lab) |
| FR-QC-023 |  Quality score (0–100): a weighted model per item or category. Sub-score per parameter: • two-sided spec with a target:  | v1 (M3–M4) | Built | Weighted quality score (mos_lab inspection) |
| FR-QC-024 |  Inspection record (from the client draft): site, inspection type (incoming / retest / in-process), supplier, PO and ord | v1 (M3–M4) | Built | Inspection record (mos_lab, mos_flows) |
| FR-QC-025 |  Inspection before unloading with the Gate module off (the client's own flow): LAB creates the inspection lot against an | v1 (M3–M4) | Built (cited) | mos_flows |
| FR-QC-026 |  Finished-product and in-process specs of the flour pack: moisture, ash, protein, wet gluten, falling number, water abso | v1 (M4–M5) | Built | Approved specs with nine checks (moisture, ash, protein, wet gluten, falling number, water absorption, granulation, colour, net content) for semolina, bread flour and whole-wheat flour (tools/seed_flour_specs.py) |

## FR-RPT

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-RPT-001 | Role dashboards, delivered with each module: PR and PO approvals (total, pending, approved, rejected, average approval t | v1 (per module, M1–M5) | Built (cited) | mos_dashboards, mos_platform_plus |
| FR-RPT-002 | Every report supports date range and site filters, export, and respects user scopes | v1 (M1) | Partial | Filters and export on lists; not every report has a date range and site filter |
| FR-RPT-003 | Heavy reports run as background jobs and notify on completion | v1 (pull-in; else v1.1) | Built (cited) | mos_master_plus |
| FR-RPT-004 | Exception reports (§15.3) are available to FINC, PLM and AUD | v1 (pull-in; else v1.1) | Built (cited) | mos_platform_plus |
| FR-RPT-005 |  Totals are never summed across units of measure: reports show one total per UoM, or convert explicitly to a stated UoM | v1 (M2–M3) | Built (cited) | mos_deliveries |

## FR-SAL

| Id | Requirement | Tag | Status | Evidence |
|---|---|---|---|---|
| FR-SAL-001 | Customers: group, TIN/VAT, price list, payment terms, credit limit, credit check mode (none / warn / block), overdue blo | v1 (M4–M5) | Built (cited) | mos_sales |
| FR-SAL-002 | Price lists (validity, tax-inclusive flag, floor price) and a line-discount limit per role; deterministic price resoluti | v1 (M4–M5, lean) · v1.1: break | Built (cited) | mos_commercial, mos_sales |
| FR-SAL-003 | Quotations converting to sales orders | v1.1 | Built (cited) | mos_sales |
| FR-SAL-004 | Sales orders with approval triggers: credit exposure (open receivables + delivered-not-invoiced + open orders + this ord | v1 (M4–M5) | Built (cited) | mos_sales |
| FR-SAL-005 | Availability check and soft reservation at approval (FR-INV-015). v1.1: lot-level hard reservation at picking | v1 (M4–M5) · v1.1: hard reserv | Built (cited) | mos_planning_plus, mos_sales |
| FR-SAL-006 |  Delivery orders: release (creates an expected outbound gate visit, FR-SAL-015) → pick (scan; validates lot status) → lo | v1 (M4–M5) · v1.1: dispatch pl | Built (cited) | mos_sales |
| FR-SAL-007 | Proof of delivery via a tokenised, single-use driver link (receiver name, signature, photos, accepted quantity, shortage | v1.1 (v1: manual POD with scan | Built (cited) | mos_commercial, mos_sales |
| FR-SAL-008 | Invoicing from posted deliveries (gap-free numbering at posting, tax per line); cash sales (exit blocked until paid); cu | v1 (M4–M5) | Built (cited) | mos_sales |
| FR-SAL-009 | Customer returns received through the gate into QUARANTINE, with a QC decision (restock / downgrade / scrap) and a credi | v1 (pull-in; else v1.1) | Built (cited) | mos_commercial, mos_sales |
| FR-SAL-010 | Customer statements, ageing, and sales reports by product, customer, region and salesperson. v1.1: DSO, fill rate/OTIF,  | v1 (M4–M5) · v1.1: DSO, OTIF,  | Built (cited) | mos_commercial, mos_sales |
| FR-SAL-011 | Fiscal device / e-invoicing as a pluggable adapter | Later (v1 if the client confir | Built (cited) | mos_sales |
| FR-SAL-012 |  Fiscal receipt reference: each customer invoice and cash sale records the external fiscal receipt number and machine ID | v1 (M4–M5) | Built (cited) | mos_sales |
| FR-SAL-013 |  Customer advances and cash-before-delivery: a deposit is recorded with its slip before release and allocated at invoici | v1 (M4–M5) · standard depth: p | Built (cited) | mos_sales |
| FR-SAL-014 |  Cash sessions for CSH: open with float, receipts, count at close, variance with reason, handover | v1 (M4–M5) | Built (cited) | mos_sales |
| FR-SAL-015 | A released delivery order creates an expected arrival for the outbound gate visit (replacing dispatch plans in v1) | v1 (M4–M5) | Built (cited) | mos_sales |
| FR-SAL-016 |  Customer onboarding and credit-limit changes are approved: a customer drafted by SREP is approved (lifecycle §1.5) afte | v1 (M4–M5) | Built (cited) | mos_commercial, mos_sales |
| FR-SAL-017 |  Price-list versions are approved (SMGR, per the approval matrix) before they take effect; floor prices are part of the  | v1 (M4–M5) | Built (cited) | mos_commercial, mos_sales |
| FR-SAL-018 |  Credit notes without a physical return (price correction, shortage or damage claim) need a reason, evidence (e.g. the s | v1 (pull-in; else v1.1) | Built (cited) | mos_commercial, mos_sales |

## A correction to the first version of this table

The first version counted a requirement as *Built (cited)* when its id appeared in the code, including ids inside ranges written as FR-X-001..005. That overstated the evidence: 81 requirements had no explicit mention. Each v1 one of them was then probed by hand. This found four real gaps (stock statuses damaged and rejected, never-negative stock, average costing instead of standard cost, and the gate-pass rules) and confirmed the rest as built through native Odoo features or code that does not cite the id (for example cost settlement, FR-MFG-014, is built and tested in mos_manufacturing). The lifecycle comparison in [LIFECYCLE-COMPARISON.md](LIFECYCLE-COMPARISON.md) was the second check and found four more.

## Beyond the FRD: design and decision records

| Source | What it asks | Status | Evidence |
|---|---|---|---|
| TDD 17.6 | Printed documents carry a QR code with the document id and a short hash | Built | mos_print (ir.actions.report rendering context) |
| TDD 21 | Two-step sign-in for sensitive roles | Built (switch: mos_platform_plus.require_totp) | mos_platform_plus; off until the installation turns it on |
| ADR-0022 item 4 | Nightly backup, attachment store copy, a second copy, a restore test | Built | tools/backup.mjs backup and check; run on the live database 2026-10-31 |
| ADR-0022 item 5 | Tests on every push | Built | .github/workflows/ci.yml runs tools/run-tests.sh and reads the result line |
| ADR-0022 item 6 | Health, metrics and alerts | Partial | Odoo's own /web/health; no metrics endpoint or alert rules (depend on the client's monitoring) |
| ADR-0017 | One company, one database, expansion seams | Built | Odoo single-company database; the site and company columns are on every document |
| ADR-0015 | Local accounts alongside single sign-on | Built | Odoo logins plus mos_auth_oidc |
| ADR-0016 | The system prepares, a named role confirms | Built | mos_flows (GRN and payment-request modes) |
| ADR-0021 | Node/NestJS stack | Superseded by ADR-0026 | Odoo 20 on PostgreSQL 17 |
| ADR-0004 | Shared-database multi-tenancy | Superseded by ADR-0017 | - |
