# MOS on Odoo: fit-gap of every requirement

<!-- nav -->[MOS](../../README.md) › [3. Design](README.md)

- **Status:** v0.1, 2026-10-18. A planning document (no code). Written so the owner can decide ADR-0026 on evidence.
- **Source of the requirements:** [MOS-FRD.md](../2-requirements/MOS-FRD.md) v0.3.5, every row with an id of the form `FR-AREA-nnn`: **231 unique ids** (the plan quoted 235; repeated ids and the two Appendix F rows account for the difference). The non-functional requirements and the controls catalogue (sections 14 and 15) are not id rows and are covered in section 4.
- **Odoo checked:** the `odoo/` clone, Odoo 20.0 Community (LGPL-3), commit 47ad8898, 2026-10-15. It is read-only vendor source and is never edited.
- **OCA checked:** by looking for 20 named modules on each OCA repository's `20.0`, `19.0` and `18.0` branches on 2026-10-18 (section 3).

## 1. The result in one table

Classes: **STD** Odoo standard; **CFG** configuration of standard features; **OCA** a community module; **CUST** a custom `mos_*` addon; **CORE** cannot be met without a core change, or goes against how Odoo works; **FUT** outside v1.

| Area | Ids | STD | CFG | OCA | CUST | CORE | FUT |
|---|---:|---:|---:|---:|---:|---:|---:|
| Platform | 47 | 9 | 4 | 9 | 22 | 1 | 2 |
| Master data | 14 | 10 | 1 | 0 | 3 | 0 | 0 |
| Inventory | 22 | 9 | 1 | 4 | 8 | 0 | 0 |
| Procurement | 22 | 8 | 0 | 2 | 12 | 0 | 0 |
| Lab and quality | 26 | 0 | 0 | 4 | 22 | 0 | 0 |
| Gate | 30 | 0 | 0 | 0 | 30 | 0 | 0 |
| Inbound process | 10 | 0 | 0 | 0 | 10 | 0 | 0 |
| Payables | 19 | 6 | 2 | 4 | 7 | 0 | 0 |
| Production | 18 | 8 | 2 | 1 | 7 | 0 | 0 |
| Sales | 18 | 10 | 0 | 4 | 4 | 0 | 0 |
| Reporting | 5 | 1 | 0 | 1 | 3 | 0 | 0 |
| **Total** | **231** | **61** | **10** | **29** | **128** | **1** | **2** |

**Reading it.** Only 71 of 231 requirements (31%) are met by Odoo as it ships. 29 more (13%) depend on OCA modules, and section 3 shows almost none of those exist on 20.0 yet. **128 (55%) are custom work in either case.** The custom part is not the edge of MOS, it is MOS's identity: gate and weighbridge, the lab and quality engine, the inbound process managers, the approvals and segregation-of-duties engine, the quality price rules and the gap-free, hash-chained, immutable posting model. Odoo gives these nothing to build on except the stock, purchase, sales, accounting and manufacturing objects they connect to.

## 2. What Odoo Community does give

- **Stock:** locations, lots, quants, pickings, reservations, counts, reordering, AVCO valuation, landed costs, expiry.
- **Purchase and sales:** RFQ, orders, blanket orders, price lists, deliveries, returns, portal.
- **Accounting at ledger level:** journals, vendor bills, payments, taxes, `l10n_et` (chart, VAT, withholding), check printing, debit notes.
- **Manufacturing:** BOMs, work centres, manufacturing orders, traceability, costing.
- **Platform:** users, groups, record rules, API keys, mail activities, import and export, sequences (including gap-free), Amharic as a language.
- **Missing from Community, confirmed in the clone:** a quality module, the work-order tablet app, the barcode app, approvals, an OIDC code-flow login, and aged-payable and partner-statement reports (the reporting app is Enterprise).

## 3. OCA coverage today (the deciding evidence)

Twenty OCA modules that the matrix relies on, looked for on each version's branch (2026-10-18):

| Branch | Present | Missing |
|---|---:|---|
| **20.0** | **1 of 20** | everything except `queue_job` |
| 19.0 | 11 of 20 | base_tier_validation, base_substitution, purchase_order_revision, purchase_tier_validation, stock_barcodes, stock_card_report, mrp_bom_version, sale_tier_validation, account_tier_validation |
| 18.0 | 16 of 20 | base_substitution, purchase_order_revision, stock_card_report, account_tier_validation |

The set: auth_oidc, auth_session_timeout, password_security, base_tier_validation, base_substitution, purchase_request, purchase_order_revision, purchase_tier_validation, stock_no_negative, stock_barcodes, stock_card_report, quality_control_oca, mrp_bom_version, account_payment_order, account_financial_report, partner_statement, queue_job, auditlog, sale_tier_validation, account_tier_validation. A module can be named differently or live in another repository, so this is a strong signal rather than proof; repeat the check before any decision is final.

**Consequence.** On 20.0 every row classed OCA is in practice custom work or a wait, which moves another 29 requirements into the custom column. The approvals engine (`base_tier_validation`) is absent even on 19.0. A build on 18.0 has the modules (16 of 20) but starts on a version two behind the current one.

## 4. Requirements that are not id rows

| Area | Fit |
|---|---|
| Immutable posting, ledger rows, insert-only grants, `guard_posted` trigger (ADR-0007/0008) | **Against Odoo's model.** The application connects as the database owner and can alter any table. An addon can add triggers, but the owner can drop them. The spec's guarantee (not even an administrator through the application, enforced by database grants) cannot be kept; a weaker form (triggers plus an external copy of the audit chain) can. |
| Tests run as a non-owner role (non-negotiable in MOS CLAUDE.md) | Not possible with Odoo's test runner, which needs rights to create and alter tables. |
| One company, one database (ADR-0017) | Fits: Odoo single-company databases. |
| UUIDv7 keys, repositories inside one unit of work, nestjs-cls context | Do not apply. Odoo uses integer ids and its own ORM transaction per request: a different architecture, not a defect. |
| Stack (Node 24, NestJS, Next.js, Drizzle, PostgreSQL 18, BullMQ) | **All replaced** by Python and Odoo's framework. ADR-0021's whole table would be reversed. PostgreSQL 18 support for Odoo 20 is not confirmed here. |
| Security (OWASP ASVS, rate limiting, CSP) | Mostly configuration and a reverse proxy; Odoo has its own CSRF and session model. |
| Hosting on premises (ADR-0022) | Fits: Odoo runs as Linux or Windows services, or in Docker. |
| Team skills | The house stack is TypeScript. Odoo needs Python, XML views and the Odoo framework: a hiring and training cost that is not in the 26-week plan. |

## 5. Full matrix

### Platform (FR-PLT)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-PLT-001 | Moved to Appendix F (tenant isolation in a shared database). In v1, customers are isolat… | Future (App. F) | FUT | Moved to Appendix F (multi-tenant); v1 is one installation per company |
| FR-PLT-002 | The organisation hierarchy is company → site → warehouse → storage unit. v1 holds exactl… | v1 (M1–M2) | CUST | Odoo has no "site" object: map site to stock.warehouse and carry it on every header (mos_core) |
| FR-PLT-003 | The company has a base currency, fiscal-year start, time zone and rounding mode. Stock i… | v1 (M1–M2) | STD | res.company currency, lock dates; AVCO is valued per company by default |
| FR-PLT-004 | Each site has a time zone. Business dates such as "today" are computed on the server in … | v1 (M1–M2) | CUST | Odoo time zones are per user/partner, not per warehouse: site tz field and server-side business date (mos_core) |
| FR-PLT-005 | Moved to Appendix F (dedicated vs shared operating modes). | Future (App. F) | FUT | Moved to Appendix F (multi-tenant); v1 is one installation per company |
| FR-PLT-006 | Module enablement per installation (feature flags). The Gate module can be disabled inde… | v1 (M1–M2) | CFG | Install or remove modules per database plus config parameters |
| FR-PLT-007 | Departments (hierarchical, with a head), and user profile attributes employee ID and hom… | v1 (M1–M2) | STD | hr.department (hierarchical, head) and hr.employee |
| FR-PLT-008 | Installation model: one installation serves one company, with its own database, a compan… | v1 (M1–M2) | CFG | One company per database (single-company mode); company profile on res.company |
| FR-PLT-010 | Users sign in with a local MOS username and password (default, FR-PLT-018) and/or throug… | v1 (M1) · standard depth | OCA | Core auth_oauth is the implicit flow; code flow with PKCE is OCA auth_oidc (not on 20.0 today) |
| FR-PLT-018 | Local accounts: username + password with a configurable policy (minimum length, complexi… | v1 (M1) · v1.1: breached | OCA | Local login is standard; lockout and password policy are OCA (password_security) |
| FR-PLT-019 | Operation unit selection: right after sign-in, the user picks an operation unit (a site,… | v1 (M1) | CUST | Operation-unit picker after sign-in (mos_core) |
| FR-PLT-011 | Users are created by the administrator, or by accepting an invitation. A first SSO sign-… | v1 (M1) · standard depth | CFG | Disable open sign-up; users invited or created by an administrator; email match needs a small override of the OAuth sign-up hook |
| FR-PLT-012 | Roles are assigned per user, optionally scoped to one or more sites. A user sees and act… | v1 (M1) | CUST | Record rules per site on every MOS model (mos_core) |
| FR-PLT-013 | Optionally, IdP groups map to MOS roles. Mapped roles sync at each SSO sign-in | v1.1 | CUST | Group-to-role sync at sign-in (v1.1) on top of auth_oidc |
| FR-PLT-014 | Permission changes take effect on the user's next request, with no re-login | v1 (M1) | STD | Access rights and groups are evaluated per request |
| FR-PLT-015 | Sessions expire after 8 h idle / 12 h absolute (configurable). Sign-out ends the MOS ses… | v1 (M1) | OCA | auth_session_timeout for idle expiry; IdP logout is custom |
| FR-PLT-016 | API keys and service accounts with scopes, expiry, rotation and last-used tracking (for … | Later | STD | res.users.apikeys (scopes limited in core; Later) |
| FR-PLT-017 | An access-review export lists every user, role, scope, grant date and grantor | v1 (pull-in; else v1.1) | CUST | Access-review export report (mos_core) |
| FR-PLT-020 | Business documents follow the lifecycles in §1.5. Commitment documents close instead of … | v1 (M1) | CUST | Per-document state machines as in section 1.5; Odoo states differ (mos_core) |
| FR-PLT-021 | Posted documents and ledger rows cannot be edited or deleted by anyone, including admini… | v1 (M1) · standard depth | CORE | Odoo connects as the database owner and can drop or alter anything; insert-only grants and the guard_posted trigger cannot be enforced against it. Mitigation: triggers plus a separate read-only audit copy, weaker than the spec |
| FR-PLT-022 | Corrections of posted documents are made only through a reversal document that reference… | v1 (M1) | CUST | Reversal exists for moves and invoices; "blocked while dependents exist" is custom (mos_core) |
| FR-PLT-023 | Approval matrix per document type, defined as data: rows of amount band × site × item ca… | v1 (M1–M2) · v1.1: gener | OCA | base_tier_validation (not on 19.0 or 20.0 today) plus a custom matrix by amount, site and category |
| FR-PLT-024 | Parallel steps within a stage, with ALL or QUORUM(n) completion | v1.1 (v1: sequential sta | OCA | base_tier_validation (not on 19.0 or 20.0 today) plus a custom matrix by amount, site and category |
| FR-PLT-025 | Approvers can approve, reject (comment mandatory) or request changes (comment mandatory;… | v1 (M1–M2) · standard de | OCA | base_tier_validation (not on 19.0 or 20.0 today) plus a custom matrix by amount, site and category |
| FR-PLT-026 | Delegation: a user can delegate approvals for a date range, optionally limited by docume… | v1 (pull-in; else v1.1) | OCA | base_substitution for delegation (not on 20.0 today) |
| FR-PLT-027 | Escalation: overdue steps trigger a reminder, then reassignment to the matrix row's fall… | v1 (pull-in; else v1.1) | OCA | Reminder or escalation on top of tier validation |
| FR-PLT-028 | Segregation of duties rules are enforced (list in §15.1). Conflicted users are excluded … | v1 (M1–M2) | CUST | Segregation-of-duties engine, pre-submission eligibility check and immutable approval history (mos_approvals) |
| FR-PLT-029 | At submission, the system verifies that every approval step has at least one eligible ap… | v1 (M1–M2) | CUST | Segregation-of-duties engine, pre-submission eligibility check and immutable approval history (mos_approvals) |
| FR-PLT-030 | An approvals inbox shows all pending tasks across modules with filters (status, requeste… | v1 (M1) · standard depth | OCA | base_tier_validation (not on 19.0 or 20.0 today) plus a custom matrix by amount, site and category |
| FR-PLT-031 | Status is action-driven. Document and master-record status is shown as a badge and chang… | v1 (M1) | STD | Action buttons drive state; status fields read-only |
| FR-PLT-032 | Bulk approve / reject of selected inbox items. Each item is still checked individually (… | v1 (pull-in; else v1.1) | CUST | Bulk approve with per-item checks (mos_approvals) |
| FR-PLT-054 | Approval history is immutable. Approval chains, decisions and comments cannot be edited … | v1 (M1–M2) | CUST | Segregation-of-duties engine, pre-submission eligibility check and immutable approval history (mos_approvals) |
| FR-PLT-040 | Number series per document type (optionally per site), with a configurable pattern and r… | v1 (M1–M2) | STD | ir.sequence with patterns and date-range reset |
| FR-PLT-041 | Legally or audit-relevant documents are numbered without gaps: GRN, self-billed voucher,… | v1 (M1–M2) | CFG | ir.sequence implementation "no_gap" |
| FR-PLT-042 | Fiscal years and periods, each module with status OPEN / SOFT_CLOSED / CLOSED. Postings … | v1 (M1–M2) | CUST | OPEN / SOFT_CLOSED / CLOSED per module: Odoo only has accounting lock dates (mos_core) |
| FR-PLT-043 | Backdating windows per document type (e.g. receipts up to 3 days back, issues 0). Postin… | v1 (M1–M2) | CUST | OPEN / SOFT_CLOSED / CLOSED per module: Odoo only has accounting lock dates (mos_core) |
| FR-PLT-044 | Attachments (photos, PDFs, spreadsheets) on any document or master record: type-checked … | v1 (M1) · standard depth | CUST | ir.attachment exists; content-type check, SHA-256, antivirus hook are custom (mos_core) |
| FR-PLT-045 | In-app notifications for approval tasks, rejections, QC decisions, arrivals, exceptions … | v1 (M1) · standard depth | STD | mail activities and notifications |
| FR-PLT-046 | A tamper-evident audit log records who did what, when and from where for every create, s… | v1 (M1) · standard depth | CUST | Hash-chained audit log with daily verification (OCA auditlog records changes but has no chain) (mos_audit) |
| FR-PLT-047 | Import from CSV/XLSX templates for master data and opening balances (items, UoM conversi… | v1 (M1–M2) · standard de | STD | base_import with test mode for CSV/XLSX |
| FR-PLT-048 | Every list screen exports to XLSX/CSV, respecting the user's scopes and filters | v1 (M1) | STD | List export to XLSX/CSV, scoped by record rules |
| FR-PLT-049 | Documents print to PDF from company-branded templates (PO, GRN, weigh ticket, gate pass,… | v1 (M1–M5) | CUST | QWeb PDF templates for each document (mos_reports) |
| FR-PLT-050 | "Process exceptions" work queue: business failures in automated steps (e.g. "cannot draf… | v1 (M1) | CUST | Process-exceptions queue (queue_job is on 20.0; the queue UI is custom) |
| FR-PLT-051 | Global search by document number, item code/name, supplier/customer, plate number, lot n… | v1 (pull-in; else v1.1) | CUST | Global search across documents, plates, lots (mos_core) |
| FR-PLT-052 | UI language and calendar display are pluggable: English/Gregorian in v1; Amharic and Eth… | v1 (M1) hooks · Later: l | CUST | Amharic language exists; Ethiopian calendar display is custom (Later) |
| FR-PLT-053 | Masters are deactivated, never deleted, once referenced by any document. Deactivation is… | v1 (M1–M3) | CUST | Archive instead of delete is standard; refusal while balances or open documents exist is custom |
| FR-PLT-055 | Date sanity checks: required date ≥ request date (PR); delivery date ≥ PO date; expiry ≥… | v1 (M1–M2) | CUST | Date sanity constraints (mos_core) |

### Master data (FR-MD)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-MD-001 | Units of measure with dimensions (mass, volume, count, length, area, time) and same-dime… | v1 (M1–M2) | STD | uom.uom categories and factors |
| FR-MD-002 | Item-specific conversions (e.g. 1 bag = 50 kg for wheat; 1 bale = 500 bags for packaging… | v1 (M1–M2) | STD | product.packaging gives item-specific conversions |
| FR-MD-003 | Items: system ID (auto) and user-defined material code, name, type (raw, packaging, semi… | v1 (M1–M2) | STD | product.template with default_code |
| FR-MD-004 | Item categories are hierarchical, and each carries an attribute schema that lots of that… | v1 (pull-in; else v1.1) | CUST | Category attribute schema that lots must satisfy (mos_core) |
| FR-MD-005 | Business partners (organisation or individual) with roles supplier / customer / transpor… | v1 (M1–M2) supplier, tra | STD | product.template with default_code |
| FR-MD-014 | Individual suppliers (e.g. farmers and traders without a company): TIN optional; nationa… | v1 (M1–M2) | CFG | res.partner individual with an extra identification field |
| FR-MD-006 | Payment terms (days; basis invoice date / GRN date / end of month) | v1 (M1–M2) | STD | product.template with default_code |
| FR-MD-007 | Tax codes (VAT, withholding, excise, other) with rate, base, threshold, recoverability, … | v1 (M1–M2) | STD | account.tax with the l10n_et chart: VAT and withholding |
| FR-MD-008 | Currencies with minor units, and exchange rates per date. One currency per document; the… | v1 (M1–M2) | STD | product.template with default_code |
| FR-MD-009 | Reason codes by domain (adjustment, scrap, status change, transit loss, count, gate reje… | v1 (M1–M2) | CUST | Reason codes by domain (mos_core) |
| FR-MD-010 | Storage hierarchy: site → warehouse (physical facility: code, name, storekeeper, capacit… | v1 (M1–M2) · standard de | STD | product.template with default_code |
| FR-MD-012 | Bank master list (bank name, branch, SWIFT/BIC), selected on supplier and company bank a… | v1 (M1–M2) | STD | product.template with default_code |
| FR-MD-013 | Product and service categories (tags) for classifying what suppliers provide, in additio… | v1 (M1–M2) | STD | product.template with default_code |
| FR-MD-011 | Industry pack: installing a pack seeds items/categories, attribute schemas, QC parameter… | v1 (grain/flour milling, | CUST | Industry pack as a data module (mos_pack_milling) |

### Inventory (FR-INV)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-INV-001 | Real-time stock by item × storage unit × lot × status, with quantity and value, filterab… | v1 (M2–M3) | CUST | Stock status per lot is not an Odoo concept: model status as sub-locations plus a status field (mos_inventory) |
| FR-INV-002 | Stock statuses QUARANTINE, AVAILABLE, BLOCKED, DAMAGED, REJECTED. Only AVAILABLE stock c… | v1 (M2–M3) | CFG | Status locations (quarantine, blocked, damaged, rejected); only the available location is reservable |
| FR-INV-003 | Status changes are documents with a reason. QUARANTINE → AVAILABLE only through a QC dec… | v1 (M2–M3) | CUST | Stock status per lot is not an Odoo concept: model status as sub-locations plus a status field (mos_inventory) |
| FR-INV-004 | Lot tracking: lot number (auto per the batch pattern, or the supplier's), supplier lot, … | v1 (M2–M3) · standard de | STD | stock.lot with expiry (product_expiry) |
| FR-INV-005 | Goods receipt kinds: from PO (Procurement), from another site (transfer), from productio… | v1 (M2–M3) | STD | stock.picking types for receipts and issues, transit routes for inter-site transfers |
| FR-INV-006 | Goods issue: to cost centre/department (consumption), to production (with production), t… | v1 (M2–M3) | STD | stock.picking types for receipts and issues, transit routes for inter-site transfers |
| FR-INV-007 | Intra-site transfer (unit to unit) as one posting. Inter-site transfer as a transfer ord… | v1 (M2–M3) · standard de | STD | stock.picking types for receipts and issues, transit routes for inter-site transfers |
| FR-INV-008 | Stock adjustments (+/−) with adjustment type, mandatory reason code, optional reference … | v1 (M2–M3) | CUST | Stock status per lot is not an Odoo concept: model status as sub-locations plus a status field (mos_inventory) |
| FR-INV-009 | Cycle counts (by storage unit/item selection) and full physical counts. Count mode per c… | v1 (M2–M3) · standard de | STD | Inventory counts, scheduled counts |
| FR-INV-010 | Valuation: moving weighted average per item at company level in v1. The ledger keeps eac… | v1 (M2–M3) MWA · Later:  | STD | AVCO costing at company level |
| FR-INV-011 | Negative stock is never allowed. Where a process must not be blocked by a shortage (prod… | v1 (M2–M3) | OCA | stock_no_negative (on 18.0 and 19.0, not on 20.0 today) |
| FR-INV-012 | Inventory period close: checklist (no unposted documents dated in the period, no open co… | v1 (M2–M3) | CUST | Stock status per lot is not an Odoo concept: model status as sub-locations plus a status field (mos_inventory) |
| FR-INV-013 | Reorder report: items below reorder level (FR-MD-010 precedence) with a suggested quanti… | v1 (pull-in; else v1.1) | STD | Reordering rules |
| FR-INV-014 | Stock balance per material × warehouse/storage unit × batch with columns opening, receiv… | v1 (M2–M3) · v1.1: agein | OCA | stock_card_report for opening/received/issued/closing (not on 19.0 or 20.0 today) |
| FR-INV-015 | Reservations and available-to-use: soft reservations from approved sales orders and rele… | v1 (M4–M5) · v1.1: hard  | STD | Reservation on pickings and sale orders |
| FR-INV-016 | Landed cost allocation (freight, duty) to receipts | Later | STD | stock_landed_costs (Later) |
| FR-INV-017 | Serial numbers and catch-weight items | Later | OCA | Catch-weight is not standard (Later) |
| FR-INV-018 | Labels: PDF lot/storage-unit/pallet labels with barcode/QR. Every entry field that takes… | v1 (pull-in; else v1.1)  | OCA | Scan-driven entry needs stock_barcodes (OCA) because the barcode app is Enterprise |
| FR-INV-019 | Silo and capacity control: each storage unit shows its current level (calculated from th… | v1 (M2–M3) | CUST | Silo capacity and dip-reading geometry (mos_inventory) |
| FR-INV-020 | Silo measurement: a dip or level reading (height or %) converts to volume via the silo's… | v1 (pull-in; else v1.1) | CUST | Silo capacity and dip-reading geometry (mos_inventory) |
| FR-INV-021 | Fumigation / treatment hold: stock in a silo, stack or lot can be put on hold with reaso… | v1 (pull-in; else v1.1) | CUST | Stock status per lot is not an Odoo concept: model status as sub-locations plus a status field (mos_inventory) |
| FR-INV-022 | Receipt valuation: GRN value = payable quantity × quality-adjusted unit price (PO price … | v1 (M2–M3) | CUST | Stock status per lot is not an Odoo concept: model status as sub-locations plus a status field (mos_inventory) |

### Procurement (FR-PRC)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-PRC-001 | Supplier registration as a 5-step wizard (from the client draft), used by procurement st… | v1 (M2–M3) · standard de | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-002 | Only APPROVED suppliers without a payment block can receive POs, deliver goods and be paid | v1 (M2–M3) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-003 | Supplier documents nearing expiry notify procurement 30 and 7 days ahead. v1.1: expired … | v1 (pull-in; else v1.1)  | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-004 | Bank details are never edited in place. A change creates a new pending bank account and … | v1 (M2–M3) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-005 | Bank account numbers are stored encrypted and masked in the UI. Revealing the full numbe… | v1 (M2–M3) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-006 | Approved supplier list per item or category (optionally per site), with enforcement per … | v1 (pull-in; else v1.1) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-007 | Purchase requisitions. Header: request number (auto), request date, required date (≥ req… | v1 (M2–M3) · standard de | OCA | purchase_request (on 18.0 and 19.0, not 20.0 today) |
| FR-PRC-008 | Sourcing policy per category and amount band: minimum number of quotations (e.g. 3) and … | v1 (pull-in; else v1.1) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-009 | RFQ workflow: invite suppliers, record received quotations (price, lead time, validity, … | Later | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-010 | Purchase orders. Header: PO number (auto), PO date, linked PR (required by default, as i… | v1 (M2–M3) · standard de | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-011 | PO amendments create a new revision that is re-approved per the matrix (e.g. quantity de… | v1 (pull-in; else v1.1)  | OCA | purchase_order_revision (not on 18.0, 19.0 or 20.0) |
| FR-PRC-012 | PO closure: auto-close when fully received (within tolerance) and invoiced; manual short… | v1 (M2–M3) | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-013 | Goods receipt (GRN) against a PO, prepared by the system and confirmed by the storekeepe… | v1 (M2–M3) | CUST | System-prepared GRN confirmed by the storekeeper (mos_inbound) |
| FR-PRC-014 | Receipt tolerances: over-receipt beyond tolerance follows the line's excess policy: BLOC… | v1 (M2–M3) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-015 | Supplier returns from REJECTED or AVAILABLE stock, valued at the original receipt cost, … | v1 (M2–M3) | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-016 | GRN PO-reassignment: fix a GRN posted against the wrong PO (same supplier and item) in o… | v1.1 | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-017 | Procurement reports: open PRs/POs, PR ageing, PO status, outstanding deliveries, price h… | v1 (M2–M3) · v1.1: score | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-018 | Purchase contracts / blanket agreements with call-offs and contract-level quality rules | Later | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-019 | Supplier self-service portal (quotations, delivery schedules, invoice upload, statements) | Later | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |
| FR-PRC-020 | Supplier self-registration via invite link: procurement sends a single-use, expiring lin… | v1.1 | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-021 | Bagged bulk receipts: bag count and bag type (tare per bag type) are captured at weighin… | v1 (M2–M3) | CUST | Supplier wizard, bank-change control, encrypted bank numbers, approved-supplier enforcement, sourcing policy, expiry notices, invite-link registration, bag/tare capture, tolerance policy, GRN reassignment (mos_procurement) |
| FR-PRC-022 | Service and non-stock receipt confirmation: the requester (or buyer) confirms service an… | v1 (pull-in; else v1.1) | STD | Purchase orders, RFQs, blanket orders, portal, returns, service receipts |

### Lab and quality (FR-QC)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-QC-001 | Test method library (code, reference standard, description, document) and parameter libr… | v1 (M3–M4) | OCA | quality_control_oca for tests, parameters and specifications (not on 20.0 today) |
| FR-QC-002 | Specifications per item or category, optionally per supplier and/or site, and per purpos… | v1 (M3–M4) · v1.1: repli | OCA | quality_control_oca for tests, parameters and specifications (not on 20.0 today) |
| FR-QC-003 | Specifications are versioned and approved. A new version is required for any change. The… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-004 | QC policy per item or category (optionally per site): inspection required; inspection po… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-005 | Sampling plans: fixed count, or percentage of containers/bags; composite or individual s… | v1 (M3–M4) · v1.1: stati | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-006 | Inspection lots are created: automatically at first weighing (gate on, before-unloading … | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-007 | Samples: unique sample number, printed label with barcode, drawn by/at, quantity, contai… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-008 | Result entry grid per sample, keyboard-first, with live pass/fail indication. Results ar… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-009 | Automatic evaluation against the frozen spec: per-line pass/fail and a suggested lot out… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-010 | Out-of-spec handling: a result can be invalidated with an assignable cause and re-tested… | v1 (M3–M4) · standard de | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-011 | Concessions: accept as-is or accept with deduction despite a failure, with justification… | v1 (pull-in; else v1.1) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-012 | Usage decision: ACCEPT, ACCEPT WITH DEDUCTION, ACCEPT UNDER CONCESSION, PARTIAL ACCEPT (… | v1 (M3–M4) · standard de | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-013 | Quality price and weight rules (versioned rule sets attached to PO lines or to the appro… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-014 | The deduction result (payable quantity, price adjustment, breakdown, rule-set version) i… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-015 | Changing a decision before unloading/receipt is allowed with a reason. After stock or fi… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-016 | Retention samples: storage location, retain-until date, disposal list and disposal record | v1 (pull-in; else v1.1) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-017 | Supplier Certificate of Analysis as an attachment. When the policy requires a CoA, a dec… | v1 (pull-in; else v1.1) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-018 | Lab dashboards: pending samples by age and priority, OOS investigations, decisions today… | v1 (M3–M4) · v1.1: suppl | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-022 | Grading schemes per item or category (versioned): an ordered list of grades (e.g. Grade … | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-023 | Quality score (0–100): a weighted model per item or category. Sub-score per parameter: •… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-024 | Inspection record (from the client draft): site, inspection type (incoming / retest / in… | v1 (M3–M4) | OCA | quality_control_oca for tests, parameters and specifications (not on 20.0 today) |
| FR-QC-025 | Inspection before unloading with the Gate module off (the client's own flow): LAB create… | v1 (M3–M4) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-026 | Finished-product and in-process specs of the flour pack: moisture, ash, protein, wet glu… | v1 (M4–M5) | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-019 | In-process and finished-goods QC with batch release (hold → release / regrade / reject),… | v1 (M4–M5) · v1.1: gener | OCA | quality_control_oca for tests, parameters and specifications (not on 20.0 today) |
| FR-QC-020 | Instrument register and calibration records; result entry blocked on out-of-calibration … | v1.1 · Later: import | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |
| FR-QC-021 | Skip-lot inspection and calculated parameters | Later | CUST | Lab and quality engine: versioned specs, policies, sampling, results, out-of-spec handling, concessions, usage decision, quality price rules, grading, score, retention samples, CoA gate, dashboards, instrument calibration (mos_lab). Odoo Community has no quality module; Enterprise quality is out of scope |

### Gate (FR-GATE)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-GATE-001 | Master data: gate posts (direction, allowed purposes), weighbridges (capacity, division,… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-002 | Blacklist for vehicles, drivers and transporters with severity BLOCK or WARN, reason, va… | v1 (pull-in; else v1.1) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-003 | Guard shifts per gate post: open, members, close. Gate actions need an open shift. v1.1:… | v1 (M3–M4) · v1.1: hando | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-004 | Expected arrivals generated automatically from approved PO delivery schedules, transfer … | v1 (M3–M4) · v1.1: no-sh | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-005 | Arrival registration by plate search (scan or type): picks the matching expected arrival… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-006 | Visit purposes (behaviour classes): inbound PO delivery, inbound transfer, outbound tran… | v1 (M3–M4) · Later: flow | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-007 | Gate entry captures vehicle, trailer, driver, transporter, supplier/customer, reference … | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-008 | One open visit per vehicle at a time | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-009 | Weigh tickets (manual in v1): first and second weighing with gross/tare/net; the weight … | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-010 | Blind second-person verification of manual weights: a different user re-enters the value… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-011 | Direction logic: inbound loaded = first gross, second tare; outbound = first tare, secon… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-012 | Tolerance rules (most specific wins: site/purpose/item category/item/partner): net vs de… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-013 | Stored tare per vehicle (+ trailer combination) with validity. Its use is allowed only w… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-014 | Re-weighing creates a superseding weighing with a reason and never deletes the original.… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-015 | Payable weight basis per supplier/PO: our weight, supplier's weight, or lower of the two… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-016 | Exit clearance evaluates, and records every attempt with results: no open blocking hold;… | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-017 | Overridable clearance checks (e.g. seal missing, plate mismatch explained) need an SSUP … | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-018 | Exit pass (printable, QR) issued when the visit reaches an exit-eligible state; scanned … | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-019 | Material gate passes: returnable / non-returnable, out/in, reason, requester, lines (ite… | v1 (pull-in; else v1.1) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-020 | Service/contractor visits: persons count, work-permit reference, tools declared in and v… | v1.1 | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-021 | Yard board: vehicles on site with purpose, partner, current step, time in step, total dw… | v1 (pull-in; else v1.1)  | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-022 | Incidents (seal broken, tamper suspected, document forgery, blacklist attempt, safety, t… | v1 (pull-in; else v1.1) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-023 | Paper mode when the system or network is down: registered, pre-numbered paper slip books… | v1 (pull-in; else v1.1)  | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-024 | Back-entry workbench: paper slips entered after recovery with the slip's actual time kep… | v1 (pull-in; else v1.1)  | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-025 | Gate reports: daily gate register, weigh ticket register (incl. voided/superseded), turn… | v1 (M3–M4) · v1.1: exten | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-030 | Weigh tickets are numbered without gaps per weighbridge; voided tickets keep their number | v1 (M3–M4) | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-026 | Edge agent weighbridge capture: live weight display, stability indication, capture only … | Later | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-027 | Direct label/ticket printing to thermal/ZPL printers via the edge agent | Later | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-028 | Multi-document trucks (one truck, several POs or delivery orders, intermediate weighings… | v1.1 | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |
| FR-GATE-029 | ANPR plate capture, barrier/traffic-light control, RFID driver cards, transporter pre-re… | Later | CUST | Gate, weighbridge, visits, weigh tickets, blacklist, clearance, passes, yard board, incidents, paper mode (mos_gate). Odoo has no gate or weighbridge module; fleet is vehicles only |

### Inbound process (FR-INB)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-INB-001 | The system runs flows A, B and C end to end with no re-keying: data captured at the gate… | v1 (M3–M4) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-002 | On acceptance, the GRN is prepared automatically: at the second weighing (flow A), from … | v1 (M3–M4 gate off · M3– | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-003 | The supplier payment process is triggered automatically: when the GRN posts, a PV draft … | v1 (M2–M4) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-010 | Automation mode settings per installation: GRN generation (confirm / automatic) and PV (… | v1 (M2–M4) · v1.1: per s | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-004 | Unloading is authorised only after pre-flight checks that could otherwise make the GRN f… | v1 (M3–M4) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-005 | If GRN preparation or confirmation fails (e.g. over tolerance), the weighing is kept, th… | v1 (M3–M4) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-006 | Missing finance setup (tax profile, bank account for a transfer-paid supplier) never blo… | v1 (M3–M4) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-007 | Every step is idempotent: double-clicks, retries and duplicate events never create dupli… | v1 (M3–M4) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-008 | The failure and exception cases in §9.5 are handled exactly as specified for the modules… | v1 (M3–M4) · standard de | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |
| FR-INB-009 | Supplier notifications (where email is configured): arrival acknowledged, rejection note… | v1 (pull-in; else v1.1) | CUST | Orchestration of gate, lab, GRN and payment request without re-keying, idempotent and with the exception handling of section 9.5 (mos_inbound) |

### Payables (FR-AP)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-AP-001 | Supplier invoice capture: supplier, invoice number (normalised for duplicate detection),… | v1 (M2–M4) · standard de | STD | Vendor bills with duplicate-reference warning |
| FR-AP-002 | Three-way match (PO ↔ GRN ↔ invoice) for stock items; two-way (PO ↔ invoice) for service… | v1 (M2–M4) | CFG | Bill control policy on received quantities (three-way match for stock items) |
| FR-AP-003 | Tolerances (installation default, optionally per category or supplier): price % and abso… | v1 (M2–M4) | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |
| FR-AP-004 | Match exceptions are resolved through the approval matrix: accept the variance (posted a… | v1 (M2–M4) | OCA | Approval of match exceptions through tier validation (not on 19.0 or 20.0 today) |
| FR-AP-005 | Quality debit note (gap-free number, printable), created automatically when an invoice e… | v1 (M2–M4) · standard de | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |
| FR-AP-006 | Self-billing: MOS generates the purchase voucher (gap-free number) from the GRN for flag… | v1 (pull-in; else v1.1) | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |
| FR-AP-007 | PV (payment request), numbered `PV-…` at submission, raised by APC or drafted by the sys… | v1 (M2–M4) | OCA | account_payment_order for payment requests and runs (on 18.0 and 19.0) |
| FR-AP-008 | Payment recording against an approved PV: • payment number and date; • method, which mus… | v1 (M2–M4) | STD | account.payment and registration |
| FR-AP-009 | Supplier balance, statement (opening, transactions, closing) and ageing with configurabl… | v1 (M2–M4) | OCA | Partner statements and aged payables are not in Community: account_financial_report / partner_statement (not on 20.0 today) |
| FR-AP-010 | Accounting export: journals are generated at every posting and mapped to external accoun… | v1 (M2–M4) CSV · Later:  | STD | Odoo is the ledger: journals post at source, no external export needed (the requirement is met by replacing the external accounting system) |
| FR-AP-011 | Advances/prepayments against POs and payment runs (batch proposals). Later: cheque print… | v1.1 advances, payment r | OCA | account_payment_order for payment requests and runs (on 18.0 and 19.0) |
| FR-AP-012 | Cheque register per company bank account: cheque numbers unique per account; spoiled or … | v1 (M2–M4) | CFG | account_check_printing: check numbering and voiding; uniqueness per account by configuration |
| FR-AP-013 | Gross, VAT, WHT and net on PVs and payments: each PV line and payment shows gross, VAT, … | v1 (M2–M4) | STD | Gross, VAT, withholding and net with the l10n_et taxes |
| FR-AP-014 | PV lifecycle (§1.5): DRAFT → SUBMITTED → APPROVED → PARTIALLY_PAID → PAID; REJECTED; CAN… | v1 (M2–M4) | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |
| FR-AP-015 | Company paying accounts: bank accounts, cash boxes and mobile-money wallets, each with i… | v1 (M1–M4) | STD | Bank, cash and wallet journals |
| FR-AP-016 | Cheque books: books registered by number range per bank account, with gap detection (eve… | v1 (pull-in; else v1.1) | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |
| FR-AP-017 | Tax registers per period, exportable to CSV/XLSX: input VAT by supplier and invoice; wit… | v1 (pull-in; else v1.1)  | STD | Tax report from l10n_et and partner ledger |
| FR-AP-018 | Self-billing rules: only suppliers flagged for self-billing; no input VAT unless the sup… | v1 (pull-in; else v1.1) | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |
| FR-AP-019 | GRNI reconciliation report as of any date: posted GRNs not yet invoiced, invoices not ye… | v1 (pull-in; else v1.1) | CUST | Tolerance rules, quality debit note, self-billing and its rules, PV lifecycle, cheque books, GRNI reconciliation (mos_payables) |

### Production (FR-MFG)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-MFG-001 | Work centres (e.g. cleaning, milling line 1, packing line), with line-side and output st… | v1 (M4–M5, lean) | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-002 | Routings (versioned): operations with work centre, run rate, setup, reporting point | v1.1 | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-003 | Recipes/BOMs in one model, versioned and approved: inputs (qty per base, scrap %, issue … | v1 (M4–M5) | OCA | BOM versioning and approval: mrp_bom_version (on 18.0; not on 19.0 or 20.0) |
| FR-MFG-004 | MPS-lite and MRP-lite (demand-driven suggestions, netting, pegging) | Later | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-005 | Work orders: standard (batch), campaign (a milling run per shift or day, settled at its … | v1 (M4–M5) · v1.1: rewor | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |
| FR-MFG-006 | Material issue: silo draw-down by quantity (lots allocated first-in-first-out, FR-INV-01… | v1 (M4–M5) | CFG | Removal strategy FIFO/FEFO; packing as a manufacturing order from bulk to bag SKUs |
| FR-MFG-007 | Output recording by lot with lot attributes (e.g. flour type, grade), labels, and initia… | v1 (M4–M5) | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-008 | Finished-goods QC and release using the QC engine (FR-QC-019, FR-QC-026): sample per lot… | v1 (M4–M5) · v1.1: in-pr | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |
| FR-MFG-009 | Traceability: backward and forward trace through work orders, including the silo lots al… | v1 (M4–M5) · v1.1: blend | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-010 | Full actual costing: labour and overhead absorption, variance analysis (usage, mix, yiel… | Later (v1: FR-MFG-014) | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-011 | Supervisor screens for the line: my orders today, issue by scan or quantity, record outp… | v1 (M4–M5, responsive) | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |
| FR-MFG-012 | Shift logs and downtime (non-overlapping per work centre, with reason codes). v1.1: OEE … | v1 (pull-in; else v1.1)  | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-013 | Production reports: extraction rate (flour output ÷ wheat input) per run and per day, yi… | v1 (M4–M5, lean) · stand | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |
| FR-MFG-014 | Material-cost settlement (the v1 valuation of production output): the work order's cost … | v1 (M4–M5) | STD | mrp.workcenter, BOMs, manufacturing orders, traceability, loss reasons, mrp_account costing |
| FR-MFG-015 | Milling run: the grist (blend of wheat lots or silos with target ratios) is recorded, as… | v1 (M4–M5) · standard de | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |
| FR-MFG-016 | Packing: bulk flour is packed into bag SKUs (e.g. 50 / 25 / 10 / 5 kg); bags, labels and… | v1 (M4–M5) | CFG | Removal strategy FIFO/FEFO; packing as a manufacturing order from bulk to bag SKUs |
| FR-MFG-017 | Fortification dosing (target vs actual premix per tonne), if mandatory for the client's … | v1 (pull-in; else v1.1) | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |
| FR-MFG-018 | Planner's demand and material view: per finished item, the open sales-order quantity, av… | v1 (pull-in; else v1.1) | CUST | Campaign work orders, release workflow, supervisor screens, extraction-rate reports, milling-run grist, fortification dosing, planner view (mos_production). The shop-floor tablet app is Enterprise |

### Sales (FR-SAL)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-SAL-001 | Customers: group, TIN/VAT, price list, payment terms, credit limit, credit check mode (n… | v1 (M4–M5) | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-002 | Price lists (validity, tax-inclusive flag, floor price) and a line-discount limit per ro… | v1 (M4–M5, lean) · v1.1: | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-003 | Quotations converting to sales orders | v1.1 | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-004 | Sales orders with approval triggers: credit exposure (open receivables + delivered-not-i… | v1 (M4–M5) | OCA | sale_tier_validation, tier validation on partners and price lists (not on 19.0 or 20.0 today) |
| FR-SAL-005 | Availability check and soft reservation at approval (FR-INV-015). v1.1: lot-level hard r… | v1 (M4–M5) · v1.1: hard  | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-006 | Delivery orders: release (creates an expected outbound gate visit, FR-SAL-015) → pick (s… | v1 (M4–M5) · v1.1: dispa | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-007 | Proof of delivery via a tokenised, single-use driver link (receiver name, signature, pho… | v1.1 (v1: manual POD wit | CUST | Proof-of-delivery link, fiscal-device adapter and receipt reference, delivery order creating an expected gate visit (mos_sales) |
| FR-SAL-008 | Invoicing from posted deliveries (gap-free numbering at posting, tax per line); cash sal… | v1 (M4–M5) | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-009 | Customer returns received through the gate into QUARANTINE, with a QC decision (restock … | v1 (pull-in; else v1.1) | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-010 | Customer statements, ageing, and sales reports by product, customer, region and salesper… | v1 (M4–M5) · v1.1: DSO,  | OCA | partner_statement for customer statements (not on 20.0 today) |
| FR-SAL-011 | Fiscal device / e-invoicing as a pluggable adapter | Later (v1 if the client  | CUST | Proof-of-delivery link, fiscal-device adapter and receipt reference, delivery order creating an expected gate visit (mos_sales) |
| FR-SAL-012 | Fiscal receipt reference: each customer invoice and cash sale records the external fisca… | v1 (M4–M5) | CUST | Proof-of-delivery link, fiscal-device adapter and receipt reference, delivery order creating an expected gate visit (mos_sales) |
| FR-SAL-013 | Customer advances and cash-before-delivery: a deposit is recorded with its slip before r… | v1 (M4–M5) · standard de | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-014 | Cash sessions for CSH: open with float, receipts, count at close, variance with reason, … | v1 (M4–M5) | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |
| FR-SAL-015 | A released delivery order creates an expected arrival for the outbound gate visit (repla… | v1 (M4–M5) | CUST | Proof-of-delivery link, fiscal-device adapter and receipt reference, delivery order creating an expected gate visit (mos_sales) |
| FR-SAL-016 | Customer onboarding and credit-limit changes are approved: a customer drafted by SREP is… | v1 (M4–M5) | OCA | sale_tier_validation, tier validation on partners and price lists (not on 19.0 or 20.0 today) |
| FR-SAL-017 | Price-list versions are approved (SMGR, per the approval matrix) before they take effect… | v1 (M4–M5) | OCA | sale_tier_validation, tier validation on partners and price lists (not on 19.0 or 20.0 today) |
| FR-SAL-018 | Credit notes without a physical return (price correction, shortage or damage claim) need… | v1 (pull-in; else v1.1) | STD | Customers, price lists, quotations, sale orders, reservation, deliveries, invoicing, down payments, returns, credit notes, POS cash sessions |

### Reporting (FR-RPT)

| Id | Requirement (short) | FRD tag | Class | Odoo basis and addon |
|---|---|---|---|---|
| FR-RPT-001 | Role dashboards, delivered with each module: PR and PO approvals (total, pending, approv… | v1 (per module, M1–M5) | CUST | Role dashboards, exception reports, one-total-per-UoM rule (mos_reports) |
| FR-RPT-002 | Every report supports date range and site filters, export, and respects user scopes | v1 (M1) | STD | Filters, export and record-rule scoping on every report |
| FR-RPT-003 | Heavy reports run as background jobs and notify on completion | v1 (pull-in; else v1.1) | OCA | queue_job (on 20.0) for background reports |
| FR-RPT-004 | Exception reports (§15.3) are available to FINC, PLM and AUD | v1 (pull-in; else v1.1) | CUST | Role dashboards, exception reports, one-total-per-UoM rule (mos_reports) |
| FR-RPT-005 | Totals are never summed across units of measure: reports show one total per UoM, or conv… | v1 (M2–M3) | CUST | Role dashboards, exception reports, one-total-per-UoM rule (mos_reports) |


[← Design](README.md) · [All documents](../../README.md) · [Start page](../../index.html)
