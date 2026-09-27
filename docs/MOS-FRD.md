# MOS: Functional Requirements Document (FRD)

| | |
|---|---|
| Product | MOS (Manufacturing Operations Suite). The name is a working codename; the client's draft uses "Factory ERP" |
| Document | Functional Requirements Document |
| Version | **0.3.3**, draft for review (package v0.3) |
| Date | 2026-09-27 |
| Audience | Product owner, developers (human and AI-assisted), the client's process owners, QA |
| Companion docs | [MOS-MASTER-PLAN.md](MOS-MASTER-PLAN.md) · [MOS-PROCESS-MODEL.md](MOS-PROCESS-MODEL.md) · [MOS-TDD.md](MOS-TDD.md) · [MOS-IMPLEMENTATION-PLAN.md](MOS-IMPLEMENTATION-PLAN.md) · [MOS-CLIENT-P2P-DRAFT-REVIEW.md](MOS-CLIENT-P2P-DRAFT-REVIEW.md) · [MOS-PACKAGE-AUDIT-v0.3.md](MOS-PACKAGE-AUDIT-v0.3.md) · [adr/](adr/) |

**Revision history**

| Version | Date | Change |
|---|---|---|
| 0.1 | 2026-09-26 | First draft |
| 0.2 | 2026-09-27 | Incorporated the client's procure-to-pay draft ([review](MOS-CLIENT-P2P-DRAFT-REVIEW.md)). New: FR-PLT-007, 018, 019, 031, 032; FR-MD-012, 013; FR-INV-019; FR-QC-022, 023, 024; FR-INB-010; FR-AP-012; FR-PRC-020. Revised: roles, SoD, AS-13 and others |
| **0.3** | 2026-09-27 | **Re-framed and audited** ([audit](MOS-PACKAGE-AUDIT-v0.3.md)):<br>• **single company per installation, no tenancy** ([ADR-0017](adr/ADR-0017-single-tenant-single-company-v1.md)); tenancy/SaaS items moved to Appendix F with their IDs kept<br>• **all six modules in v1** ([ADR-0018](adr/ADR-0018-lean-v1-six-months.md)); tags MVP/P2/P3 replaced by **v1 (month) / v1.1 / Later**<br>• **no infrastructure assumed** ([ADR-0019](adr/ADR-0019-infrastructure-not-assumed.md))<br>• the client is a **flour** mill (the draft's inspection screen: "Addis Flour Factory", durum wheat in 50 kg bags); examples changed from flooring to flour<br>• **new:** FR-PLT-008, 053–055; FR-MD-014; FR-INV-020–022; FR-PRC-021; FR-QC-025, 026; FR-GATE-030; FR-AP-013–018; FR-MFG-014–017; FR-SAL-012–015; FR-RPT-005; NFR-14; SOD-15; AS-14–19; status vocabulary §1.5<br>• **revised:** approval, SoD, AP matching, deduction, WHT, cheque, count, valuation and gap-free rules; acceptance criteria for every v1 production and sales requirement |
| **0.3.1** | 2026-09-27 | **Feedback from the process model** (planning Phase 2, [process model §14](MOS-PROCESS-MODEL.md#14-gaps-found-and-fed-back)):<br>• **new:** FR-PRC-022 (service receipt confirmation), FR-AP-019 (GRNI reconciliation), FR-MFG-018 (planner's demand and material view), FR-SAL-016 (customer and credit-limit approval), FR-SAL-017 (price-list approval), FR-SAL-018 (credit note without return); BR-PRC-05, BR-MFG-01; SOD-17<br>• **§1.5:** 12 lifecycles added (customer, bank account, expected arrival, outbound visit, weigh ticket, stock status, cheque, credit note, cash session, gate pass, fiscal period, process exception); supplier and supplier-invoice rows corrected; the inbound visit starts at CHECKED_IN<br>• **revised:** FR-PLT-049 (templates), FR-INV-012 (close checklist), FR-AP-010 (GRNI criterion), FR-GATE-006 and §9 (point to the process model), FR-SAL-015 (wording)<br>Counts: 231 requirements, 205 tagged v1 |
| **0.3.2** | 2026-09-27 | Feedback from the [database design](MOS-DATABASE-DESIGN.md) (planning Phase 3): FR-PLT-006 states that the weighbridge station stays available when the Gate module is off. No other change; every §1.5 lifecycle and every v1 requirement has its storage |
| **0.3.3** | 2026-09-28 | **Month tags re-aligned to the committed schedule** ([ADR-0020](adr/ADR-0020-six-modules-one-release-26-weeks.md), [implementation plan v0.4](MOS-IMPLEMENTATION-PLAN.md) §6, path A): the release is every module's core in W1–W26. 162 v1 requirements carry the month their core is built; 42 are delivered only at standard or full depth and are tagged **v1 (pull-in; else v1.1)**; requirements with both core and deeper parts add "standard depth: pull-in, else v1.1". No requirement text changed |

**How to read this document.**
- Every requirement has an ID in the form `FR-<MODULE>-<nnn>`, a tag and an acceptance criterion (AC). IDs are never reused: a moved or deferred requirement keeps its ID.
- Business rules are labelled `BR-<MODULE>-<nn>`. These are invariants the system enforces rather than features.
- **Tags:**
  - **v1 (Mn):** in the first release, built in month *n* of the development window (M1–M6; see the [implementation plan](MOS-IMPLEMENTATION-PLAN.md)). "lean" means reduced depth, spelled out in the text.
  - **v1 (pull-in; else v1.1):** in the v1 scope, but delivered only at standard or full depth (implementation plan Appendix A). It is built in the release only if the team runs ahead of plan; otherwise it moves to v1.1 (ADR-0020).
  - **v1.1:** the first follow-up release, planned during hypercare.
  - **Later:** on the product roadmap, with no date.
  - **Future (App. F):** belongs to a future multi-tenant/SaaS product (Appendix F).
  - A requirement can carry two tags when its parts ship at different times, e.g. "v1 (M2) · Later: FIFO option".
- **Module codes:**
  - `PLT` platform · `MD` master data · `INV` inventory · `PRC` procurement · `QC` lab/quality · `GATE` gate
  - `INB` inbound end-to-end process · `AP` payables sub-ledger · `MFG` production · `SAL` sales and receivables · `RPT` reporting
- **Role codes** (§2) are used throughout for actors, e.g. STK for the storekeeper and APC for the finance officer.

---

## 1. Purpose and scope

MOS is a **company-neutral** system for running a manufacturing plant's core operations:
- buying and receiving raw materials, and testing them;
- storing and moving stock;
- producing;
- selling and dispatching;
- controlling what crosses the factory gate.

**v1 runs as one installation per company** ([ADR-0017](adr/ADR-0017-single-tenant-single-company-v1.md)): its own database, one company, any number of sites. Multi-company and multi-tenant SaaS are later expansions with deliberate seams.

The defining capability is the **inbound raw-material loop**, which runs end to end with no re-keying:

1. the truck arrives (at the gate, or at stores when the gate module is off);
2. it is weighed;
3. the lab samples, tests and grades the material;
4. on acceptance, **the raw-material receipt (GRN) is prepared automatically** and confirmed by the storekeeper (or posted automatically, by setting; ADR-0016);
5. **the supplier payment process is triggered:** a payment request is drafted for the finance officer.

**The first client** is a flour mill. The scope of work's title says "Floor Manufacturing". The client's own draft inspects durum wheat for "Addis Flour Factory", stores it in silos, and lists bran as a material. MOS therefore treats "Floor" as **flour** until the client confirms. This is the first open client question (review §6).

### 1.1 In scope
- Procurement, including supplier payment.
- Inventory: multi-store, multi-site, silos.
- Raw-material testing and laboratory.
- Production.
- Sales, including receivables.
- Gate management.
- An operational payables/receivables sub-ledger that exports journals to an external accounting package.

### 1.2 Out of scope
- **Accounting:** general ledger, chart-of-accounts maintenance and financial statements. MOS exports journals instead.
- **People processes:** payroll, HR, and people-visitor appointment management.
- **Plant hardware:** PLC/SCADA integration. Weighbridge capture, label printers, ANPR and barriers are Later; v1 uses manual entry with controls.
- **Out-of-product services:** banking integration (payments are executed at the bank and recorded in MOS), and customer and supplier self-service portals (Later).

### 1.3 Product principles
1. **Generic engine, industry packs as data.**
   - Process manufacturing (one input, co- and by-products, e.g. flour milling) and discrete/batch manufacturing (multi-level BOM) are both supported by the model.
   - Test parameters, specifications, grade tables, quality price rules, recipe templates, reason codes, checklists and label templates ship as *industry packs*: seed data, never code.
   - **v1 ships the grain/flour-milling pack.** Other packs (e.g. flooring) are Later.
2. **Posted means permanent.** Posted documents and ledgers cannot be edited. Mistakes are corrected by reversal documents, which leave a complete audit trail.
3. **The plant must never stop because of the system.** Every station a truck or batch depends on (gate, weighbridge, receiving) has a documented degraded mode. Where a control deliberately stops a movement (e.g. no outbound exit without a posted document), that stop is stated.
4. **Controls are built in.** Segregation of duties, approval matrices, tolerances and dual control are part of the core model, configured as data by the system administrator.
5. **One installation per company, expandable.** v1 is a single-company installation. The design keeps the seams that make multi-company and multi-tenant SaaS later additions, not rewrites (ADR-0017).
6. **No infrastructure is assumed.** Every feature that depends on a device or an external service (camera, printer, scanner, TV screen, email relay, virus scanner, internet) has a manual fallback and an on/off setting (NFR-14, ADR-0019).

### 1.4 Glossary
| Term | Meaning |
|---|---|
| Installation | One deployment of MOS with its own database. In v1 it serves one company |
| Company | The legal entity running the installation (books, tax registration, numbering, fiscal calendar). Exactly one in v1 |
| Site / operation unit | A physical plant or depot of the company. The operation unit chosen after sign-in is a site (pending client confirmation, review §6) |
| Warehouse / storage unit | A stock-holding facility at a site, and a place inside it: store, silo, tank, bin, yard, quarantine, reject, in-transit |
| Bucket | The unit stock is counted in: item × storage unit × lot × stock status |
| Stock status | QUARANTINE (awaiting QC), AVAILABLE, BLOCKED (held), DAMAGED (present but unusable), REJECTED |
| Lot / batch | An identifiable quantity of one item with common origin and quality |
| GRN | Goods Received Note: the posted raw-material receipt record |
| GRNI | Goods received not invoiced: the clearing account between a GRN and its supplier invoice |
| Inspection lot | The QC work item for a delivery or stock lot: its samples, results, grade, score and decision |
| Usage decision | The QC outcome for an inspection lot: accept, accept with deduction, accept under concession, partial accept, reject |
| Payable quantity | The quantity the supplier is paid for, after weight-based quality deductions. It can differ from the physical quantity |
| PV (payment request) | The finance officer's request to pay a supplier, numbered `PV-…` (payment voucher). The client's draft called it "payment request" |
| Self-billed voucher | A purchase voucher MOS generates for a supplier who issues no invoice (e.g. an individual farmer) |
| Commitment document | A document that promises something but moves no stock or money (PR, PO, transfer order, PV, sales order) |
| Posting document | A document that writes to a ledger (GRN, issue, transfer, adjustment, invoice, payment, production output, delivery) |
| Reversal | A new posting document that exactly cancels an earlier one |
| Extraction rate | Flour output ÷ wheat input for a milling run, the key yield KPI of the flour pack |
| Campaign work order | A continuous-process work order (e.g. a milling day or shift) settled at its end rather than per batch |
| SoD | Segregation of duties |
| MWA | Moving weighted average cost |
| Industry pack | Seed configuration for an industry (parameters, specs, grades, rules, templates) |

### 1.5 Status vocabulary (canonical)
Statuses change **only through named actions** (FR-PLT-031). The same word means the same thing in every module. The [process model §6](MOS-PROCESS-MODEL.md#6-l3-lifecycles) draws each lifecycle as a state diagram. Statuses marked *derived* there (e.g. PARTIALLY_RECEIVED) are computed from the stored status and the fulfilment quantities (TDD §8.3).

| Document | Lifecycle |
|---|---|
| Generic approval-controlled document | DRAFT → SUBMITTED → APPROVED · REJECTED. *Request changes* returns it to DRAFT and records a RETURNED action in its history. *Recall* by the submitter does the same |
| Supplier | DRAFT → SUBMITTED → APPROVED ⇄ SUSPENDED; BLOCKED (by action, with reason); REJECTED; INACTIVE (deactivated, FR-PLT-053; reactivation needs approval) |
| Customer | As supplier: DRAFT → SUBMITTED → APPROVED ⇄ SUSPENDED; BLOCKED; REJECTED; INACTIVE (FR-SAL-016). A manual credit hold is a flag, not a status |
| Supplier bank account | PENDING → ACTIVE (usable for transfers after the cooling-off period) → INACTIVE; REJECTED. The change request follows the generic lifecycle (FR-PRC-004) |
| Purchase requisition (PR) | DRAFT → SUBMITTED → APPROVED → PARTIALLY_ORDERED → ORDERED; CLOSED (short-closed with reason); REJECTED; CANCELLED |
| Purchase order (PO) | DRAFT → SUBMITTED → APPROVED → ISSUED → PARTIALLY_RECEIVED → RECEIVED → CLOSED; REJECTED; CANCELLED (only with zero receipts) |
| Expected arrival | PLANNED → ARRIVED → CLOSED; CANCELLED (when its PO, transfer or delivery order is cancelled or closed). The yard board shows PLANNED arrivals as "Expected". v1.1: NO_SHOW |
| Gate visit (inbound) | CHECKED_IN (or HOLD_NO_PO) → FIRST_WEIGHED → SAMPLED → QC_DECIDED → UNLOAD_AUTHORIZED → UNLOADED → GRN_DRAFTED → RECEIPTED → EXITED. Side states: REJECTED_AT_GATE, RECEIPT_EXCEPTION, ABANDONED, CANCELLED. Before check-in the truck is an expected arrival |
| Gate visit (outbound) | CHECKED_IN → FIRST_WEIGHED (tare) → LOADING → SECOND_WEIGHED (gross, verified) → GOODS_ISSUED (the goods issue, supplier return or transfer dispatch is posted) → EXITED; CANCELLED (before goods issue) |
| Weigh ticket | OPEN → COMPLETE → LOCKED (consumed by a posting); VOIDED (number kept). Verification is a separate status: NOT_REQUIRED, PENDING, VERIFIED (FR-GATE-010) |
| Inspection lot | CREATED → IN_TESTING → DECISION_PENDING → DECIDED → CLOSED; CANCELLED. Out-of-spec investigation is a flag, not a state |
| Stock status (per bucket) | QUARANTINE, AVAILABLE, BLOCKED, DAMAGED, REJECTED, with the transitions of FR-INV-003. A status change never changes on-hand quantity |
| GRN | DRAFT (prepared) → POSTED → REVERSED; a DRAFT can be CANCELLED |
| Supplier invoice | DRAFT → POSTED → REVERSED. "Submit" posts it to the payables sub-ledger and runs matching. It is shown with its match status (AWAITING_RECEIPT, MATCHED, ON_HOLD with the reason: price / quantity / weight / QC / verification) and its payment status (PARTIALLY_PAID, PAID) |
| PV (payment request) | DRAFT → SUBMITTED → APPROVED → PARTIALLY_PAID → PAID; REJECTED; CANCELLED (FR-AP-014) |
| Payment, customer receipt | DRAFT → POSTED → REVERSED |
| Cheque | ISSUED → CLEARED · STALE; VOIDED (number kept); MISSING (an unissued leaf reported missing) |
| Stock adjustment, transfer, issue | DRAFT → SUBMITTED → APPROVED → POSTED → REVERSED; REJECTED (documents below their approval threshold go DRAFT → POSTED) |
| Count | PLANNED → IN_PROGRESS → COUNTED → (RECOUNT) → APPROVED → POSTED; CANCELLED |
| Work order | PLANNED → RELEASED → IN_PROGRESS → COMPLETED → SETTLED → CLOSED; CANCELLED (only with no postings) |
| Sales order | DRAFT → SUBMITTED → APPROVED (or ON_HOLD for credit or price) → PARTIALLY_DELIVERED → DELIVERED → CLOSED; REJECTED; CANCELLED |
| Delivery order | RELEASED → PICKED → LOADED → GOODS_ISSUED → EXITED → DELIVERED; CANCELLED (before goods issue) |
| Customer invoice | DRAFT → POSTED → PARTIALLY_PAID → PAID; corrections by credit note, never by edit |
| Credit note | DRAFT → SUBMITTED → POSTED (posted automatically on approval); REJECTED |
| Cash session | OPEN → CLOSED (a count, and a reason for any variance, are required) |
| Material gate pass | DRAFT → SUBMITTED → APPROVED → OUT (partial movements allowed) → CLOSED; REJECTED; CANCELLED; EXPIRED (validity passed before any movement). Overdue is a flag |
| Fiscal period (per module) | OPEN → SOFT_CLOSED → CLOSED; reopening (by approval) returns it to OPEN |
| Process exception | OPEN → RESOLVED (fixed and retried) · CANCELLED (no longer applicable, with reason) |

---

## 2. Users, roles and personas

Roles may be scoped to a **site**. A user may hold several roles. Permissions are fine-grained (`module.object.action`). Roles are bundles of permissions that the system administrator edits. The table lists the seeded default roles; all ship in v1.

| Code | Role | Typical person | Key permissions (summary) |
|---|---|---|---|
| ADM | System administrator | IT/ERP admin | Users, roles, sign-in settings, numbering, org structure, settings, industry pack |
| FINC | Finance controller | Chief accountant | Periods, account mapping, journal export, variance approvals, AP/AR oversight |
| REQ | Requester | Any department staff (in the client's process, the **storekeeper** raises raw-material PRs) | Raise purchase requisitions, view own requests |
| MGMT | Management approver | Department head / GM (the client narrative's "Management") | Approve PRs and POs within their approval bands |
| BUY | Buyer / procurement officer | Procurement staff | Suppliers (draft), quotations, POs |
| PMGR | Procurement manager | Head of procurement | Approve suppliers, POs and sourcing exceptions |
| STK | Storekeeper | Warehouse staff | Receipts (GRN confirmation), issues, transfers, counts (blind) |
| WSUP | Warehouse supervisor | Warehouse lead | Approve adjustments and counts, block/unblock stock |
| LAB | Lab analyst / inspector | QC technician (the client's "Quality Inspector") | Inspection lots, samples, results; confirm ACCEPT when all results pass (FR-QC-012) |
| QAM | QA/QC manager | Quality head | Specs, QC policies, grade tables, deductions, concessions, rejections |
| GRD | Gate guard | Security guard | Arrivals, entry, exit, gate passes (execute) |
| WBO | Weighbridge operator | Weighbridge clerk | Weighings, weigh-ticket verification (never their own) |
| SSUP | Security supervisor | Security head | Shifts, overrides, blacklist, incidents, paper-mode back-entry approval |
| APC | Finance officer (payables) | Accountant (the client's **finance officer**) | Supplier invoices, matching, debit notes, **raise PVs** |
| PAP | Payment approver | Finance manager / GM (the client's "authorised approver") | Approve PVs |
| TRS | Treasury (payments) | Finance officer or treasurer (in the client's process, the same finance officer who raised the PV) | Record and reverse supplier payments, cheque register |
| PLM | Plant manager | Plant/general manager | High-value approvals, period reopen approval, emergency overrides |
| AUD | Auditor | Internal/external auditor | Read-only on everything, audit and exception reports |
| PPL | Production planner | Planner (may be the same person as PSUP) | Recipes/BOMs, work orders, production plan |
| PSUP | Production supervisor | Shift supervisor | Issue, output, downtime, shift logs |
| SREP | Sales representative | Sales staff | Customers (draft), sales orders |
| SMGR | Sales manager | Head of sales | Approve orders, prices and discounts |
| CRC | Credit controller | Credit officer | Credit limits, holds, collections |
| DSP | Dispatcher | Logistics clerk | Delivery orders, loading |
| CSH | Sales cashier | Cashier | Cash sales, customer receipts, cash sessions |

**Persona notes that shape the design:**
- **Gate guards and weighbridge operators** may have low digital literacy and work in a noisy gatehouse on a shared PC or tablet.
  - Screens are large-target, few-field and scan-first. A keyboard-wedge barcode scanner, where one exists, types into the focused field.
- **Lab analysts** enter many numbers quickly.
  - The result-entry grid is keyboard-first: Tab and Enter move through parameters, and values out of spec are highlighted as they are typed.
- **Approvers** are often away from a desk. The approvals inbox works on a phone browser.
- **Shift handover** happens at every station, so shift context (who, which post, which shift) is explicit, not inferred.

---

## 3. Platform requirements (FR-PLT)

### 3.1 Installation and organisation structure
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-PLT-001 | *Moved to Appendix F (tenant isolation in a shared database).* In v1, customers are isolated physically: one installation and one database per company (FR-PLT-008) | Future (App. F) | — |
| FR-PLT-002 | The organisation hierarchy is company → site → warehouse → storage unit. v1 holds exactly one company; every document header carries its site (and company, for later multi-company) | v1 (M1–M2) | An admin can create 3 sites, 5 warehouses and their storage units under the one company. Every document header shows its site. Creating a second company is refused in v1 |
| FR-PLT-003 | The company has a base currency, fiscal-year start, time zone and rounding mode. **Stock is valued at company level in v1** (one moving average per item); site-level valuation is Later | v1 (M1–M2) | Base currency cannot be changed after the first posting (the UI disables it; the API returns an error) |
| FR-PLT-004 | Each site has a time zone. Business dates such as "today" are computed on the server in the site's time zone | v1 (M1–M2) | A posting at 23:30 UTC for a UTC+3 site gets the next calendar day as its posting date |
| FR-PLT-005 | *Moved to Appendix F (dedicated vs shared operating modes).* | Future (App. F) | — |
| FR-PLT-006 | Module enablement per installation (feature flags). **The Gate module can be disabled independently.** Its visit flow (visits, shifts, yard board, exit clearance) is then off; weights are recorded at the **weighbridge station**, which stays available where weighbridges exist, or typed on the inspection record, and flow into the GRN (FR-QC-025, FR-PRC-013) | v1 (M1–M2) | A disabled module's menus, API routes and events are unavailable, except the weighbridge station of a disabled Gate module where weighbridges are configured |
| FR-PLT-007 | **Departments** (hierarchical, with a head), and user profile attributes **employee ID** and home department. Documents record the requesting department and, for POs, the purchasing department | v1 (M1–M2) | A PR defaults the requester's department and employee ID; approval rules can use the department |
| FR-PLT-008 | **Installation model:** one installation serves one company, with its own database, a company profile (legal name, TIN/VAT number, address, logo, base currency, fiscal-year start, time zone) and any number of sites. The data model keeps the company dimension and the expansion seams of ADR-0017 | v1 (M1–M2) | A fresh installation runs a setup wizard that creates the company profile and first site; the profile appears on every printed document |

### 3.2 Identity and access
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-PLT-010 | Users sign in with a **local MOS username and password** (default, FR-PLT-018) **and/or** through an external OpenID Connect identity provider ("SSO Login"), configured per installation (issuer, client id/secret, scopes, group-claim path). The administrator enables either or both | v1 (M1–M2) · standard depth: pull-in, else v1.1 | Sign-in works with a local account, and against the reference SSO when enabled, in tests |
| FR-PLT-018 | **Local accounts:** username + password with a configurable policy (minimum length, complexity); lockout after repeated failures; "forgot password" via admin reset, or an email link where an email relay is configured; admin reset forces a change at next sign-in; "remember me" only extends the session up to the absolute limit; optional **TOTP second factor**, enforceable per role (e.g. PAP, TRS, ADM). **v1.1:** breached-password check against a bundled common-password list | v1 (M1–M2) · v1.1: breached-password check · standard depth: pull-in, else v1.1 | 5 failed attempts lock the account for 15 minutes; a reset user must change the password before any other action; a PAP without TOTP enrolled cannot approve when the role requires it; passwords are never logged or retrievable; unknown-user and wrong-password responses are identical |
| FR-PLT-019 | **Operation unit selection:** right after sign-in, the user picks an operation unit (**a site**, pending client confirmation) from those they are assigned to. It is remembered as their default and switchable from the header. It sets default scope and document defaults, but never grants access beyond the user's role scopes. The page is skipped when only one applies | v1 (M1–M2) | A user assigned to two plants sees only those two; documents created after switching default to the new site |
| FR-PLT-011 | Users are created by the administrator, or by accepting an invitation. A first SSO sign-in succeeds only for a user who already exists or was invited (matched on verified email); otherwise access is refused with a clear message. *(Domain-based auto-join: Appendix F.)* | v1 (M1–M2) · standard depth: pull-in, else v1.1 | An uninvited SSO user sees "no access" and no user row is created |
| FR-PLT-012 | Roles are assigned per user, optionally scoped to one or more sites. A user sees and acts only on documents within their scopes | v1 (M1–M2) | A storekeeper scoped to Site A cannot list, open, export, search or post Site B documents (UI and API; AS-11) |
| FR-PLT-013 | Optionally, IdP groups map to MOS roles. Mapped roles sync at each SSO sign-in | v1.1 | Removing a user from an IdP group removes the mapped role at next sign-in |
| FR-PLT-014 | Permission changes take effect on the user's next request, with no re-login | v1 (M1–M2) | Revoking `proc.po.approve` makes the next approve call fail with 403 |
| FR-PLT-015 | Sessions expire after 8 h idle / 12 h absolute (configurable). Sign-out ends the MOS session and, for SSO users, the IdP session | v1 (M1–M2) | After idle expiry the next request redirects to sign-in |
| FR-PLT-016 | API keys and service accounts with scopes, expiry, rotation and last-used tracking (for integrations and on-site devices) | Later | A revoked key is rejected immediately; the key is stored hashed and shown once |
| FR-PLT-017 | An access-review export lists every user, role, scope, grant date and grantor | v1 (pull-in; else v1.1) | The export matches the database for a seeded installation |

### 3.3 Document lifecycle, approvals and SoD
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-PLT-020 | Business documents follow the lifecycles in §1.5. Commitment documents close instead of posting; posting documents post and can only be reversed | v1 (M1) | An exhaustive (state × action) test matrix passes for every document type |
| FR-PLT-021 | Posted documents and ledger rows cannot be edited or deleted by anyone, including administrators through the application | v1 (M1) · standard depth: pull-in, else v1.1 | Direct API attempts return 409. Database-level UPDATE/DELETE as the application role fails |
| FR-PLT-022 | Corrections of posted documents are made only through a **reversal** document that references the original. Reversal is blocked while dependent documents exist (e.g. an invoice matched to a GRN) | v1 (M1) | Reversing a matched GRN is refused with the blocking invoice named |
| FR-PLT-023 | **Approval matrix** per document type, defined as data: rows of amount band × site × item category, plus named flags (sole-source, variance above tolerance, payment to a recently changed bank account, priority). Each matching row contributes an approval stage with its approver role(s). **v1.1:** general conditions over any whitelisted fact | v1 (M2) · v1.1: general conditions | A PO of 150,000 in category RAW triggers a two-stage chain per the seeded matrix. A PO of 5,000 auto-approves |
| FR-PLT-024 | Parallel steps within a stage, with ALL or QUORUM(n) completion | v1.1 (v1: sequential stages, one-of-N approvers) | A quorum-2-of-3 committee step completes on the second approval |
| FR-PLT-025 | Approvers can approve, reject (comment mandatory) or request changes (comment mandatory; the document returns to DRAFT and its history shows RETURNED). A submitted document is locked; the submitter can recall it to DRAFT | v1 (M2) | Editing a submitted PO is refused. Recall cancels the chain and returns it to DRAFT. A returned PR shows the approver's comment to the requester |
| FR-PLT-026 | Delegation: a user can delegate approvals for a date range, optionally limited by document type and maximum amount | v1 (pull-in; else v1.1) | During delegation, the delegate sees and decides the task "on behalf of"; the audit shows both names; SoD applies to both |
| FR-PLT-027 | Escalation: overdue steps trigger a reminder, then reassignment to the matrix row's fallback role | v1 (pull-in; else v1.1) | A step overdue past its SLA gains the fallback assignee automatically |
| FR-PLT-028 | **Segregation of duties** rules are enforced (list in §15.1). Conflicted users are excluded from approval tasks and blocked at posting | v1 (M2) | The PO creator never receives that PO's approval task. The GRN poster never receives the approval task of a PV covering that GRN |
| FR-PLT-029 | At submission, the system verifies that every approval step has at least one eligible approver after SoD exclusion. Otherwise it routes to the fallback role or refuses with a clear error | v1 (M2) | A matrix row whose only approver is the submitter cannot be submitted |
| FR-PLT-030 | An approvals inbox shows all pending tasks across modules with filters (status, requester, date range), SLA due time, KPI tiles (total, pending, approved, rejected, average approval time) and one-click open or history. It is usable on a phone browser | v1 (M1) · standard depth: pull-in, else v1.1 | Manual test at 375 px width. Approve and reject work |
| FR-PLT-031 | **Status is action-driven.** Document and master-record status is shown as a badge and changes only through actions (Submit, Approve, Reject, Issue, Cancel, Suspend…), each audited. It is never an editable field | v1 (M1) | No API accepts a status value in a create or update payload |
| FR-PLT-032 | **Bulk approve / reject** of selected inbox items. Each item is still checked individually (authority, SoD, content hash); reject requires remarks; bulk action can be disabled per document type (off by default for PVs and bank-detail changes). Only SUBMITTED items appear in an approval queue | v1 (pull-in; else v1.1) | A bulk selection that includes a conflicted item approves the others and reports the conflict |
| FR-PLT-054 | **Approval history is immutable.** Approval chains, decisions and comments cannot be edited or deleted, including for rejected or cancelled documents | v1 (M2) | No UI or API action deletes an approval record; the client draft's delete icon on approval rows is not built (review §5) |

### 3.4 Numbering, periods, attachments, notifications, audit, import
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-PLT-040 | Number series per document type (optionally per site), with a configurable pattern and reset (never, fiscal year, calendar year, month). Pattern tokens: `{FY}` fiscal year of the posting date, `{YYYY}` calendar year, `{YYYYMMDD}` calendar date, `{SEQ:n}` zero-padded counter. Defaults follow the client draft's style (review §8), with PVs as `PV-` so they never clash with purchase requisitions (`PR-`) | v1 (M1–M2) | Patterns render correctly and counters reset at the configured boundary |
| FR-PLT-041 | Legally or audit-relevant documents are numbered **without gaps**: GRN, self-billed voucher, debit note, PV, payment, WHT certificate, stock issue, stock adjustment, weigh ticket (per weighbridge), customer invoice, customer receipt, credit note. The number is assigned at the irreversible point (posting), except for PVs, which are numbered at submission; a cancelled or rejected PV keeps its number and stays visible | v1 (M1–M2) | A concurrency test with injected failures yields exactly 1..n with no gaps or duplicates; a cancelled PV number is listed as CANCELLED, not reused |
| FR-PLT-042 | Fiscal years and periods, each module with status OPEN / SOFT_CLOSED / CLOSED. Postings are allowed only into open periods (soft-closed needs a permission). Reopening needs approval | v1 (M1–M2) | A posting into a closed period is refused. Reopening creates an approval chain |
| FR-PLT-043 | Backdating windows per document type (e.g. receipts up to 3 days back, issues 0). **Posting dates** can never be in the future. Reference dates such as a cheque date may be future-dated (post-dated cheques). An override of the backdating window needs a permission and a reason | v1 (M1–M2) | A 5-day backdated receipt without the permission is refused; a payment with a cheque date next month posts today |
| FR-PLT-044 | Attachments (photos, PDFs, spreadsheets) on any document or master record: type-checked by content (not extension), size-limited, SHA-256 fingerprinted. **Virus scanning** runs when a scanner is configured (fail-open or fail-closed by setting) | v1 (M1) · standard depth: pull-in, else v1.1 | A renamed .exe is refused. With a scanner configured, an EICAR test file is rejected or quarantined per the setting |
| FR-PLT-045 | In-app notifications for approval tasks, rejections, QC decisions, arrivals, exceptions and expiring supplier documents. **Email** copies when an email relay is configured. Per-user opt-out for non-critical email | v1 (M1) · standard depth: pull-in, else v1.1 | Each event notifies the correct recipients; opt-out suppresses email but not the in-app item; with no relay configured, nothing fails |
| FR-PLT-046 | A tamper-evident audit log records who did what, when and from where for every create, submit, approve, post, reverse, override, permission change and sign-in. A daily job verifies the hash chain | v1 (M1) · standard depth: pull-in, else v1.1 | Altering an audit row in the database is detected by the next verification run |
| FR-PLT-047 | Import from CSV/XLSX templates for master data and opening balances (items, UoM conversions, suppliers, customers, storage units, opening stock with lots and costs, open supplier and customer balances), with a dry-run validation report | v1 (M1–M2) · standard depth: pull-in, else v1.1 | Importing 5,000 items: the dry run reports row-level errors, and commit is all-or-nothing |
| FR-PLT-048 | Every list screen exports to XLSX/CSV, respecting the user's scopes and filters | v1 (M1) | The export row count equals the filtered list count |
| FR-PLT-049 | Documents print to PDF from company-branded templates (PO, GRN, weigh ticket, gate pass, exit pass, sample label, rejection note, supplier receipt with deduction breakdown, self-billed voucher, PV, payment voucher, debit note, WHT certificate, delivery note, customer invoice, credit note, receipt) | v1 (M1–M6) | Each template renders with the company logo, legal details and a QR code of the document reference |
| FR-PLT-050 | "Process exceptions" work queue: business failures in automated steps (e.g. "cannot draft PV: supplier has no approved payment details") appear as tasks assigned to a role, with resolve and retry actions | v1 (M1) | Removing a transfer-paid supplier's bank account and receiving a delivery creates an exception task; fixing it and retrying drafts the PV |
| FR-PLT-051 | Global search by document number, item code/name, supplier/customer, plate number, lot number | v1 (pull-in; else v1.1) | Search returns results within the user's scope in under 1 s for 100k documents |
| FR-PLT-052 | UI language and calendar display are pluggable: English/Gregorian in v1; Amharic and Ethiopian-calendar display as a later locale pack | v1 (M1) hooks · Later: locale pack | All UI strings come from message catalogs, and dates render through one formatter |
| FR-PLT-053 | **Masters are deactivated, never deleted,** once referenced by any document. Deactivation is refused while open balances or open documents exist (e.g. a warehouse holding stock, a supplier with open POs) | v1 (M1–M3) | Deleting a used item is refused; deactivating a storage unit with stock is refused and names the stock |
| FR-PLT-055 | **Date sanity checks:** required date ≥ request date (PR); delivery date ≥ PO date; expiry ≥ manufacture date; cheque date within a configurable window | v1 (M1–M2) | A PR whose required date precedes its request date cannot be submitted (the client draft's PR example has this defect; review §5) |

---

## 4. Master data (FR-MD)

| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-MD-001 | Units of measure with dimensions (mass, volume, count, length, area, time) and same-dimension factors (kg, g, t, quintal, l, ml, hl, pcs, m³) | v1 (M1–M2) | 1 t = 1000 kg = 10 quintal converts automatically |
| FR-MD-002 | Item-specific conversions (e.g. 1 bag = 50 kg for wheat; 1 bale = 500 bags for packaging). Conversions referenced by any document are immutable; a change creates a new version | v1 (M1–M2) | Editing a used conversion is refused; a new one can be added with an effective date |
| FR-MD-003 | Items: system ID (auto) and user-defined material code, name, type (raw, packaging, semi-finished, finished, by-product, scrap, consumable, **spare part (MRO)**, non-stock), category, base UoM, lot tracking (none/lot), shelf life, packaging tare, **inspection required** (drives the QC policy), **reference cost** (used for PR estimates and variance reports, never for valuation), default **reorder level, safety stock and maximum level** (overridable per storage unit; FR-MD-010), status | v1 (M1–M2) | Item codes are unique; changing the base UoM after the first posting is refused; "inspection required" routes receipts through QC |
| FR-MD-004 | Item categories are hierarchical, and each carries an **attribute schema** that lots of that category must satisfy. The flour pack seeds, for wheat: variety (e.g. durum), origin (region), crop year, grade | v1 (pull-in; else v1.1) | A wheat lot cannot be created without its required attributes |
| FR-MD-005 | Business partners (organisation or individual) with roles supplier / customer / transporter, tax id (TIN; unique when present), VAT registration, addresses, contacts | v1 (M1–M2) supplier, transporter · v1 (M5) customer · standard depth: pull-in, else v1.1 | A duplicate TIN is refused, and a fuzzy-name duplicate warning is shown on create |
| FR-MD-014 | **Individual suppliers** (e.g. farmers and traders without a company): TIN optional; national ID or other identifier; flagged for self-billing (FR-AP-018); a configurable withholding rate applies when no TIN is supplied (rates are seed data confirmed by the client) | v1 (M1–M2) | A farmer without a TIN can be registered, receive a PO and be paid by self-billed voucher; the no-TIN WHT rate is applied |
| FR-MD-006 | Payment terms (days; basis invoice date / GRN date / end of month) | v1 (M1–M2) | Due dates compute correctly for each basis |
| FR-MD-007 | Tax codes (VAT, withholding, excise, other) with rate, base, threshold, recoverability, timing (at invoice / at payment), effective dates, and tax groups. No country rules in code; rates are seed data confirmed by the client | v1 (M1–M2) | A VAT code and a withholding code with a threshold compute correctly on a sample invoice, using the rates configured at that date |
| FR-MD-008 | Currencies with minor units, and exchange rates per date. One currency per document; the company currency is the default | v1 (M1–M2) | A foreign-currency PO stores its rate; reports in company currency use the stored rate. (Whether POs in USD are paid in ETB is a client question, review §6) |
| FR-MD-009 | Reason codes by domain (adjustment, scrap, status change, transit loss, count, gate rejection, re-weigh, dispute, override, downtime, return, price override, fumigation) | v1 (M1–M2) | Mandatory reason codes are enforced on the documents that need them |
| FR-MD-010 | **Storage hierarchy:** site → **warehouse** (physical facility: code, name, storekeeper, capacity and UoM, temperature/humidity flags, status) → **storage unit** (type STORE / SILO / TANK / BIN / YARD / QUARANTINE / REJECT / IN_TRANSIT; code, name, storekeeper(s), capacity and UoM, allowed material categories and stock statuses, restricted access, permissions *allow receipt / allow issue / allow transfer*, where transfer may require approval) → optional bins. **Stock levels:** capacity (physical maximum); maximum level (order-up-to ceiling); reorder level (triggers a PR suggestion); safety level (critical alert). The rule safety ≤ reorder ≤ maximum ≤ capacity is validated. Precedence: storage-unit level → item-per-warehouse level → item default. Silo capacity may be held in volume (m³) and converted with the latest bulk density | v1 (M1–M2) · standard depth: pull-in, else v1.1 | Posting AVAILABLE stock into a quarantine-only unit is refused; a unit with "allow issue" off refuses issues; users without access to a restricted unit cannot post to it; a silo with safety 10,000 > reorder 5,000 cannot be saved (the client draft's silo example) |
| FR-MD-012 | **Bank master list** (bank name, branch, SWIFT/BIC), selected on supplier and company bank accounts | v1 (M1–M2) | A supplier bank account can only reference a bank from the list |
| FR-MD-013 | **Product and service categories** (tags) for classifying what suppliers provide, in addition to material categories | v1 (M1–M2) | Suppliers can be filtered by category |
| FR-MD-011 | Industry pack: installing a pack seeds items/categories, attribute schemas, QC parameters, specs, grade tables, deduction rules, reason codes, checklists, label templates and recipe templates. **v1 ships the grain/flour-milling pack**, installed once on a new installation | v1 (grain/flour milling, M1–M5) · Later: other packs | Installing the flour pack on an empty installation yields a working demo, including wheat inspection parameters and a milling recipe template |

---

## 5. Inventory (FR-INV)

### 5.1 Requirements
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-INV-001 | Real-time stock by item × storage unit × lot × status, with quantity and value, filterable by site, warehouse and category | v1 (M2) | The on-hand report equals the sum of ledger movements for every bucket (automated reconciliation) |
| FR-INV-002 | Stock statuses QUARANTINE, AVAILABLE, BLOCKED, DAMAGED, REJECTED. Only AVAILABLE stock can be **issued to consumption, production or sale**, transferred between sites or reserved. DAMAGED and REJECTED stock leave only by scrap, supplier return or (DAMAGED) approved recovery | v1 (M3–M4) | Issuing quarantined or damaged stock to production is refused; scrapping damaged stock is allowed |
| FR-INV-003 | Status changes are documents with a reason. QUARANTINE → AVAILABLE only through a QC decision. AVAILABLE ↔ BLOCKED by WSUP; releasing a QC-originated block requires QC. AVAILABLE → DAMAGED by STK with reason (photo optional); DAMAGED → AVAILABLE (recovery) needs WSUP approval. **A status change never changes on-hand quantity** | v1 (M3–M4) | A storekeeper cannot release quarantine; a QC decision can; a damage move leaves on-hand unchanged |
| FR-INV-004 | Lot tracking: lot number (auto per the batch pattern, or the supplier's), supplier lot, manufacture and expiry dates, attributes, QC status. Lots can be split (relabelled) with genealogy kept | v1 (M2–M4) · standard depth: pull-in, else v1.1 | Backward trace from any lot shows its supplier and GRN; forward trace shows its transfers, issues and work orders |
| FR-INV-005 | Goods receipt kinds: from PO (Procurement), from another site (transfer), from production output (M5), from customer return (M6), opening balance (import), and miscellaneous receipt with reason and cost (restricted) | v1 (M3–M4) | Each receipt kind posts stock, value and journal lines per its posting rule (TDD §12.4); a miscellaneous receipt without the permission is refused |
| FR-INV-006 | Goods issue: to cost centre/department (consumption), to production (M5), to customer via delivery (M6), scrap. Valued at current moving average | v1 (M3–M4) | Issue value equals quantity × average at posting |
| FR-INV-007 | Intra-site transfer (unit to unit) as one posting. Inter-site transfer as a transfer order → dispatch (stock enters in-transit, valued at dispatch cost) → receipt at destination. Short receipts stay in transit until resolved as transit loss (approval) or received later | v1 (M3–M4) · standard depth: pull-in, else v1.1 | An in-transit ageing report lists unresolved quantities; a transit loss is valued at the dispatch cost; period close warns about open in-transit |
| FR-INV-008 | Stock adjustments (+/−) with adjustment type, mandatory reason code, optional reference document and attachment, and approval routed by value through the approval matrix (e.g. < X WSUP, X–Y PLM, > Y PLM + FINC). **The approver comes from the matrix (warehouse authority, never QC), not typed in.** Creator ≠ approver. The action is "Submit"; posting follows approval, with auto-post only below a configurable value threshold | v1 (M3–M4) | A 60,000-value write-off routes to PLM and FINC per the seeded matrix; an adjustment cannot name its own approver |
| FR-INV-009 | Cycle counts (by storage unit/item selection) and full physical counts. **Count mode per count:** FREEZE (postings to the counted units are refused until posted) or CUT_OFF (counted against a snapshot at the cut-off time; postings after it roll forward; default for silos). Counts can be blind (system quantity hidden). A variance above threshold forces a recount by a different user. The system quantity compared is the bucket's **on-hand across all statuses**. Silos are counted by measurement (FR-INV-020). **An approved count generates its adjustment document automatically** (system stock, counted, adjustment per line, referencing the count) | v1 (M3–M4) · standard depth: pull-in, else v1.1 | A frozen unit refuses postings with a clear message; a cut-off count stays correct when an issue posts during counting; a blind counter's API responses omit system quantities; the generated adjustment references its count |
| FR-INV-010 | Valuation: moving weighted average per item **at company level** in v1. The ledger keeps each receipt's cost, so a FIFO option can be derived later | v1 (M2) MWA · Later: FIFO option | Average cost after a sequence of receipts and issues matches a hand-calculated fixture |
| FR-INV-011 | Negative stock is never allowed. Where a process must not be blocked by a shortage (production backflush, FR-MFG-006), the shortage is recorded as a **pending issue** to resolve, never as negative stock | v1 (M2) | Issuing more than on-hand is refused and names the short bucket; a backflush shortage creates a pending-issue task |
| FR-INV-012 | Inventory period close: checklist (no unposted documents dated in the period, no open counts, in-transit report reviewed, **no unsettled work orders with postings in the period**, BR-MFG-01) → soft close → value snapshot → close | v1 (M3–M4) | After close, "as of" reports for that period are reproducible and fast |
| FR-INV-013 | Reorder report: items below reorder level (FR-MD-010 precedence) with a suggested quantity up to maximum level, which can create purchase requisitions | v1 (pull-in; else v1.1) | Items below reorder level appear in the report, and "create PR" pre-fills a requisition |
| FR-INV-014 | **Stock balance** per material × warehouse/storage unit × batch with columns *opening, received, issued, adjusted, closing on-hand* and the status breakdown *available (of which reserved), quarantine, damaged, blocked, rejected*, plus a **transaction history** per row (date, type, reference no., description, received, issued, adjustment, balance after, user). Closing on-hand = opening + received − issued ± adjustments = Σ status columns. **Status changes (inspection rejection, damage) move stock between status columns and never deduct from on-hand a second time.** "Received" includes quantity held as REJECTED at receipt. **Totals are never summed across units of measure** (FR-RPT-005). Also: stock movement ledger, lot trace (backward/forward), expiry list, inventory valuation, adjustments register, count variance, in-transit. **v1.1:** stock ageing, slow/non-moving | v1 (M3–M4) · v1.1: ageing, slow-moving · standard depth: pull-in, else v1.1 | **Fixture (corrected client example, review §5 #9):** opening 10,000 kg + received 20,000 (19,800 accepted + 200 held-rejected) − issued 2,500 = on-hand 27,500 = available 27,250 + damaged 50 + rejected 200. If the 200 kg is returned on the truck instead: received 19,800 and on-hand 27,300 |
| FR-INV-015 | Reservations and available-to-use: soft reservations from approved sales orders and released work orders; available-to-use = available − reserved. **v1.1:** hard lot-level reservations | v1 (pull-in; else v1.1) · v1.1: hard reservations | Reserving 1,000 kg reduces available-to-use by 1,000 and leaves on-hand unchanged; an issue against another order cannot consume reserved quantity without an override |
| FR-INV-016 | Landed cost allocation (freight, duty) to receipts | Later | Allocation by value/weight/qty updates item cost correctly |
| FR-INV-017 | Serial numbers and catch-weight items | Later | A serialised item cannot be issued without its serial |
| FR-INV-018 | Labels: PDF lot/storage-unit/pallet labels with barcode/QR. Every entry field that takes a lot, storage unit or item accepts scanner input where a scanner exists; typing works everywhere | v1 (pull-in; else v1.1) · Later: direct ZPL printing | Scanning a lot label into an issue line fills the lot and item |
| FR-INV-019 | **Silo and capacity control:** each storage unit shows its current level (calculated from the ledger) against capacity and levels. Receipts that would exceed capacity warn, or block (setting). Falling below reorder or safety level raises an alert. Silos are **commingled**: several lots may sit in one silo, and issues from a silo consume lots first-in-first-out automatically (**v1.1:** quantitative blend genealogy) | v1 (M2–M4) | Receiving 16,000 kg into a 50,000 kg silo holding 35,000 kg warns (or is refused); the level updates immediately; two concurrent receipts cannot both pass a blocking capacity check |
| FR-INV-020 | **Silo measurement:** a dip or level reading (height or %) converts to volume via the silo's geometry table, then to mass via bulk density (latest hectolitre weight of the silo's contents or a configured value). It is compared with the book level and can serve as the count method for silos. Sensor integration is Later | v1 (pull-in; else v1.1) | A dip reading produces a computed mass and a variance against the book level; approving it as a count generates the adjustment |
| FR-INV-021 | **Fumigation / treatment hold:** stock in a silo, stack or lot can be put on hold with reason FUMIGATION (or other treatment), a treatment record (chemical, dose, operator, date) and a **release-not-before date**. Release is by QC after that date | v1 (pull-in; else v1.1) | Issuing from a fumigated silo before its release date is refused; release before the date is refused even for QC |
| FR-INV-022 | **Receipt valuation:** GRN value = payable quantity × quality-adjusted unit price (PO price after grade and deduction effects, FR-QC-013). Unit cost = value ÷ physical quantity. Quality deductions create no purchase price variance | v1 (M2–M3) | A receipt of 20,000 kg with a 1% payable-weight deduction at 30.00/kg is valued at 19,800 × 30.00 = 594,000, i.e. a unit cost of 29.70 |

### 5.2 Business rules
- **BR-INV-01:** The stock and value of every bucket equal the sum of its ledger movements, always.
- **BR-INV-02:** Internal moves (transfer, status change, lot split) are zero-sum in quantity and value.
- **BR-INV-03:** A bucket or item can never go negative in quantity. An item with zero quantity has zero value; any residual is posted as revaluation.
- **BR-INV-04:** A backdated movement is valued at the average current at posting time. Its posting date only selects the period (documented moving-average behaviour).
- **BR-INV-05:** Status transitions follow FR-INV-003. Rejected and damaged stock leave only by supplier return, scrap or (damaged) an approved recovery back to AVAILABLE.
- **BR-INV-06:** Quantities are converted to the base UoM once, at entry. The ledger stores base quantities only.

---

## 6. Procurement (FR-PRC)

### 6.1 Requirements
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-PRC-001 | Supplier registration as a **5-step wizard** (from the client draft), used by procurement staff in v1:<br>**(1) Basic:** supplier ID (auto), name, business registration number, TIN/VAT, company description.<br>**(2) Contacts:** primary contact person and position, phone and mobile (with country code), email.<br>**(3) Address:** country, region/state, city, postal code, physical address, mailing address ("same as physical").<br>**(4) Financial:** payment method preference; one or more bank accounts (bank from the bank master, account name, account number, currency).<br>**(5) Products & services:** materials supplied, product categories, service categories.<br>Plus an onboarding document checklist per supplier group (trade licence, tax registration, VAT certificate, bank letter) with expiry tracking. **Registration date and status are system-set** (lifecycle §1.5). Drafts can be saved at any step. *(The client's screens are titled "Supplier Portal"; whether suppliers should self-register is a client question. The invite link is FR-PRC-020)* | v1 (M2–M3) · standard depth: pull-in, else v1.1 | A supplier with a missing mandatory field or document cannot be submitted; status cannot be edited directly |
| FR-PRC-002 | Only APPROVED suppliers without a payment block can receive POs, deliver goods and be paid | v1 (M2–M3) | Creating a PO for a SUBMITTED supplier is refused |
| FR-PRC-003 | Supplier documents nearing expiry notify procurement 30 and 7 days ahead. **v1.1:** expired mandatory documents suspend the supplier automatically | v1 (pull-in; else v1.1) · v1.1 auto-suspend | Expiry notifications fire on schedule |
| FR-PRC-004 | **Bank details are never edited in place.** A change creates a new pending bank account and a change request that needs **one independent approver** (not the requester) plus a recorded call-back verification with evidence; a **second approver** is a setting (recommended). A new account becomes usable only after a cooling-off period (default 24 h). The first account captured at registration is verified the same way, as part of supplier approval | v1 (M2–M3) | Requester and approver must differ (and approver 2, when enabled); a payment by transfer before `usable_from` is refused; a missing call-back record blocks approval |
| FR-PRC-005 | Bank account numbers are stored encrypted and masked in the UI. Revealing the full number needs a permission and is audited | v1 (M2–M3) | The audit log records every reveal |
| FR-PRC-006 | Approved supplier list per item or category (optionally per site), with enforcement per category: none / warn / block, at PO submit and at receipt | v1 (pull-in; else v1.1) | With "block", a PO line for a non-approved supplier–item pair is refused |
| FR-PRC-007 | Purchase requisitions.<br>**Header:** request number (auto), request date, **required date** (≥ request date), **priority** (low / medium / high / urgent), **requesting department** and **requester employee ID** (defaulted from the user), purpose, remarks, site.<br>**Lines:** material code and name (lookup), category, UoM, requested quantity, **available stock** (live available-to-use at the site), **quantity to purchase** (computed: requested − available, optionally also − open PR/PO quantity; editable only upward with a reason), estimated unit cost (defaults from the item's reference cost), estimated total, **specification** (link or attachment), notes; free-text lines for non-stock items.<br>Lifecycle per §1.5; approval per the matrix; the PR reaches ORDERED when fully converted to POs | v1 (M2–M3) | A PR for 500 kg with 120 kg available proposes 380 kg to purchase; a PR converts into one or more POs; ordered quantity is tracked per line |
| FR-PRC-008 | Sourcing policy per category and amount band: minimum number of quotations (e.g. 3) and whether sole-source is allowed. **Quotations are recorded on the PO** (supplier, amount, attachment per quotation, count obtained), or the PO carries a sole-source justification; both are approval-matrix flags | v1 (pull-in; else v1.1) | A PO above the band without enough recorded quotations or a sole-source justification cannot be submitted |
| FR-PRC-009 | RFQ workflow: invite suppliers, record received quotations (price, lead time, validity, attachment), side-by-side comparison, award with a reason if not lowest | Later | The comparison screen highlights the lowest price per line; awarding a non-lowest line requires a reason |
| FR-PRC-010 | Purchase orders.<br>**Header:** PO number (auto), PO date, **linked PR** (required by default, as in the client draft; POs without a PR are a setting), supplier (lookup), payment terms (e.g. Net 30), **delivery terms** (Incoterm), **purchasing department**, buyer, **plant / factory location**, deliver-to warehouse or storage unit, required delivery date (≥ PO date), currency, **header discount** (allocated to lines for costing), tax (from line tax codes).<br>**Lines:** item, qty, UoM, unit price, line discount, tax codes, need-by, over/under receipt tolerance, excess policy, QC spec, price and grade rule set; delivery schedule.<br>Lifecycle per §1.5, including **ISSUED** (sent to supplier; method and time recorded). Approval per the matrix | v1 (M2–M3) | Issuing a PO queues the PDF email to the supplier (where email is configured) and records the issue; header discount allocation sums exactly to the header discount |
| FR-PRC-011 | PO amendments create a new revision that is re-approved per the matrix (e.g. quantity decrease or date change can auto-approve). Received quantities cannot be reduced below what was received. History of every revision | v1 (pull-in; else v1.1) · v1.1: revision diff view | Revision 2 is re-approved; receipts continue against the approved revision |
| FR-PRC-012 | PO closure: auto-close when fully received (within tolerance) and invoiced; manual short-close with a reason; cancel only with zero receipts; stale-PO report | v1 (M2–M3) | A short-closed PO stops accepting receipts |
| FR-PRC-013 | Goods receipt (GRN) against a PO, **prepared by the system and confirmed by the storekeeper** ("Save & Generate GRN"; automatic posting is a setting, FR-INB-010).<br>**Pre-filled** from the PO, the gate visit or (gate off) the inspection lot (FR-QC-025): vehicle number, gross/tare/net weights, bag count and bag tare (FR-PRC-021), inspection reference and decision, accepted and rejected quantity.<br>**Per line:** material, UoM (shown once, in its own column), PO quantity, accepted quantity, rejected quantity with **disposition** (RETURNED_ON_TRUCK = not received; HELD_AS_REJECTED = received into REJECTED status for return or disposal), batch number (auto, pattern `BATCH-{YYYYMMDD}-{SEQ:2}`, or the supplier's batch), storage unit (store or silo). Totals: PO quantity, accepted, rejected, net weight.<br>**A truck may carry one weighed line plus any number of counted lines;** the weighed line's quantity = truck net − bag tare − nominal weight of the counted lines, and a mismatch beyond tolerance blocks generation until explained.<br>**When the Gate module is disabled,** vehicle and weights are entered on the inspection lot or GRN with the same controls (photo where a camera exists, blind verification).<br>Receipt status per line follows the item's QC policy | v1 (M2–M3) | A GRN for a quarantine-inspected item lands in QUARANTINE and creates an inspection lot; a GRN whose accepted + rejected-held quantity differs from the computed net beyond tolerance cannot be generated; the client draft's example (20,000 kg net vs 20,500 kg of lines) is refused with the mismatch named |
| FR-PRC-014 | Receipt tolerances: over-receipt beyond tolerance follows the line's excess policy: BLOCK (reload the excess) or REQUIRE_APPROVAL (the approved excess is received and payable). *(ACCEPT_UNPAID was removed in v0.3: zero-value receipts distort the moving average)* | v1 (M2–M3) | Each policy behaves as specified in an acceptance test |
| FR-PRC-015 | Supplier returns from REJECTED or AVAILABLE stock, valued at the original receipt cost, creating a debit expectation for AP and, where configured, reopening PO quantity for replacement | v1 (M2–M3) | Returning a rejected lot creates the AP debit expectation; with "reopen for replacement" on, the PO's open quantity increases by the returned quantity |
| FR-PRC-016 | GRN PO-reassignment: fix a GRN posted against the wrong PO (same supplier and item) in one controlled document. (In v1 the correction is reversal + re-post, allowed while nothing is invoiced or consumed) | v1.1 | Stock is unchanged, PO quantities move from PO A to PO B, and any price difference is posted as revaluation/PPV |
| FR-PRC-017 | Procurement reports: open PRs/POs, PR ageing, PO status, outstanding deliveries, price history per item, spend by supplier/category. **v1.1:** supplier performance scorecard (on-time, quality acceptance, weight variance) and sourcing compliance | v1 (M2–M3) · v1.1: scorecard, compliance · standard depth: pull-in, else v1.1 | Each report's totals equal the sum of its source documents for a seeded dataset |
| FR-PRC-018 | Purchase contracts / blanket agreements with call-offs and contract-level quality rules | Later | A call-off cannot exceed the contract's remaining quantity |
| FR-PRC-019 | Supplier self-service portal (quotations, delivery schedules, invoice upload, statements) | Later | A supplier sees only their own documents |
| FR-PRC-020 | **Supplier self-registration via invite link:** procurement sends a single-use, expiring link; the supplier completes the same 5-step wizard and uploads KYC documents; the record arrives as SUBMITTED for internal review. No supplier login | v1.1 | An expired or used link is refused; uploads are content-checked; nothing becomes active without internal approval |
| FR-PRC-021 | **Bagged bulk receipts:** bag count and bag type (tare per bag type) are captured at weighing or inspection. Payable and stock net = weighed net − bags × bag tare. Empty bags are returned to the supplier, kept as packaging stock, or credited (setting per supplier) | v1 (M2–M3) | 450 bags of 0.15 kg tare on a 22,500 kg net weighing give a material net of 22,432.5 kg; kept bags are received into packaging stock |
| FR-PRC-022 | **Service and non-stock receipt confirmation:** the requester (or buyer) confirms service and non-stock PO lines as delivered, by quantity or percentage, as a receipt line with no stock effect. Invoices for these lines then match three ways against the confirmation, instead of two ways against the PO | v1 (pull-in; else v1.1) | An invoice for a service line with no confirmation goes to "awaiting receipt"; confirming 50% of a service lets 50% of its value match |

### 6.2 Business rules
- **BR-PRC-01:** Received minus returned quantity per PO line ≤ ordered × (1 + over-tolerance) + approved excess.
- **BR-PRC-02:** A GRN snapshots the PO price (and its quality-adjusted price) at receipt time. Later PO price amendments affect only future receipts.
- **BR-PRC-03:** One live GRN per gate visit (or inspection lot) per PO line.
- **BR-PRC-04:** Supplier, item and PO must be active and approved at both PO submission and receipt posting.
- **BR-PRC-05:** The quantity ordered against a PR line cannot exceed its quantity to purchase by more than the PR tolerance (default 0%). Buying more needs a new or amended PR.

---

## 7. Lab and quality (FR-QC)

### 7.1 Requirements
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-QC-001 | Test method library (code, reference standard, description, document) and parameter library: quantitative (UoM, decimals), qualitative (list of options), boolean, text | v1 (M4–M5) | One parameter (e.g. moisture %) is used by the wheat and flour specs; changing its decimals changes both result grids |
| FR-QC-002 | Specifications per item or category, optionally per supplier and/or site, and per purpose (incoming, retest, in-process, finished). Each line: parameter, mandatory flag, min/max (inclusive or exclusive), target, accepted options, action on failure (reject / deduct / concession allowed / info only). **v1.1:** replicate count and aggregation, rounding before comparison, second-person verification per line | v1 (M4–M5) · v1.1: replicates, verification per line | Two approved specs for the same scope cannot overlap in effective dates |
| FR-QC-003 | Specifications are versioned and approved. A new version is required for any change. The spec in force is **frozen onto each inspection lot** when it is created | v1 (M4–M5) | Approving spec v2 does not change the evaluation of inspection lots created under v1 |
| FR-QC-004 | QC policy per item or category (optionally per site): inspection required; **inspection point = BEFORE UNLOADING** (at the gate, or at stores when the gate module is off) or **IN QUARANTINE (after receipt)** or none; release mode (immediate / hold pending long tests); sampling plan; auto-release on pass; retention sample required and duration; supplier CoA required | v1 (M4–M5) | A bulk wheat item set to BEFORE UNLOADING blocks unloading (gate on) or GRN generation (gate off) until a decision. A packaged item set to IN QUARANTINE receives into quarantine |
| FR-QC-005 | Sampling plans: fixed count, or percentage of containers/bags; composite or individual samples. **v1.1:** √n+1 and ISO 2859 tables | v1 (M4–M5) · v1.1: statistical plans · standard depth: pull-in, else v1.1 | For 450 bags at 2%, the plan suggests 9 bag samples |
| FR-QC-006 | Inspection lots are created: automatically at first weighing (gate on, before-unloading items); **manually by LAB against an open PO line and truck (gate off; FR-QC-025)**; automatically at GRN posting for quarantine items; manually for stock re-inspection (e.g. before expiry, after fumigation) | v1 (M4–M5) | Each creation path yields exactly one live inspection lot per delivery and item |
| FR-QC-007 | Samples: unique sample number, printed label with barcode, drawn by/at, quantity, container, storage location, type (primary, retest, retention, reference), status. Scanning a sample label (where a scanner exists) opens result entry | v1 (M4–M5) | Scanning or typing a sample number opens the right inspection lot |
| FR-QC-008 | Result entry grid per sample, keyboard-first, with live pass/fail indication. Results are **never overwritten**: a correction invalidates the old value with a reason and records a new one. Both stay visible | v1 (M4–M5) | Both old and new values remain visible with who/when/why |
| FR-QC-009 | Automatic evaluation against the frozen spec: per-line pass/fail and a suggested lot outcome: PASS / PASS WITH DEDUCTION / NEEDS DECISION / FAIL-REJECT. Evaluation runs live as values are typed, and also on an explicit **"Calculate quality"** action that fills grade, score, suggested acceptance status and suggested rejection reason | v1 (M4–M5) | Boundary tests (exactly on min/max, inclusive vs exclusive) pass |
| FR-QC-010 | Out-of-spec handling: a result can be invalidated with an assignable cause and re-tested from a retest or retention sample, up to a maximum number of retests per parameter. The final value follows the spec's rule (original stands / retest replaces if invalidated / average of valid results) | v1 (M4–M5) · standard depth: pull-in, else v1.1 | A retest beyond the configured maximum is refused |
| FR-QC-011 | Concessions: accept as-is or accept with deduction despite a failure, with justification, routed through the approval matrix by severity and deduction amount. The analyst who entered the results cannot approve | v1 (pull-in; else v1.1) | SoD test: the analyst never receives the concession approval task |
| FR-QC-012 | Usage decision: ACCEPT, ACCEPT WITH DEDUCTION, ACCEPT UNDER CONCESSION, PARTIAL ACCEPT (accepted and rejected quantities), REJECT, with reason. **LAB confirms the suggested outcome** (ACCEPT; ACCEPT WITH DEDUCTION computed by the rules; REJECT at a reject threshold). **QAM is required** to override the suggestion, and for concessions and partial acceptance. The decision immediately drives the next step: gate on, authorise unloading or reject the truck; gate off, allow GRN generation; quarantine, move stock to AVAILABLE and/or REJECTED | v1 (M4–M5) | A partial accept of a quarantined lot moves each quantity to the correct status in one transaction; LAB cannot override a suggested REJECT |
| FR-QC-013 | **Quality price and weight rules** (versioned rule sets attached to PO lines or to the approved supplier list): per parameter, a threshold with step, rate and cap. Effect: price % adjustment, amount per unit, or **payable-quantity %** reduction; a reject threshold; optional premiums. **Grade-based price differentials** (e.g. Grade 2 = −3% of the PO price) are a rule basis too.<br>**Combination order:**<br>(1) payable-quantity % effects are added together (per-parameter caps, then an overall cap) and applied to the physical net → payable quantity;<br>(2) price % effects, grade differential included, are added together (caps) and applied to the PO price → quality-adjusted price;<br>(3) amount-per-unit effects apply to the payable quantity.<br>A **"no stacking" setting** skips per-parameter price effects for parameters that already define the grade | v1 (M4–M5) | **Two simultaneous deductions:** 20,000 kg at 30.00/kg; moisture 12.0% against a base of 11.5% with 0.1% payable weight per 0.1 point → payable 19,900 kg; foreign matter 2.0% with −1% price per point above 1.0% → price 29.70; value 19,900 × 29.70 = 591,030.00 |
| FR-QC-014 | The deduction result (payable quantity, price adjustment, breakdown, rule-set version) is stored with the decision as immutable evidence for supplier disputes, and printed on the supplier's receipt/deduction note | v1 (M4–M5) | A reprint months later shows the same breakdown even if the rule set changed since |
| FR-QC-015 | Changing a decision before unloading/receipt is allowed with a reason. After stock or financial effects, it requires QAM approval and generates compensating documents (status change, revaluation, AP adjustment) | v1 (M4–M5) | A superseded decision is visible in history; compensations reconcile |
| FR-QC-016 | Retention samples: storage location, retain-until date, disposal list and disposal record | v1 (pull-in; else v1.1) | Samples past retain-until appear on the disposal list |
| FR-QC-017 | Supplier Certificate of Analysis as an attachment. When the policy requires a CoA, a decision cannot be made without one | v1 (pull-in; else v1.1) | A decision without a required CoA is refused |
| FR-QC-018 | Lab dashboards: pending samples by age and priority, OOS investigations, decisions today, rejection reasons. **v1.1:** supplier quality trend | v1 (M4–M5) · v1.1: supplier trend · standard depth: pull-in, else v1.1 | Counts match the underlying records |
| FR-QC-022 | **Grading schemes** per item or category (versioned): an ordered list of grades (e.g. Grade 1, 2, 3, Reject), each defined either by **parameter bands** (the worst parameter determines the grade) or by **quality-score bands**. The grade is stored on the inspection lot and the lot, and can drive acceptance and price (FR-QC-013). The client's actual grade table is a client question (review §6) | v1 (M4–M5) | With the seeded flour-pack table, a sample with moisture 12.5% and impurity 0.8% grades as the table defines; changing the table creates a new version that affects only new lots |
| FR-QC-023 | **Quality score** (0–100): a weighted model per item or category.<br>**Sub-score per parameter:**<br>• two-sided spec with a target: 100 × (1 − abs(value − target) ÷ abs(limit − target)), where *limit* is the bound on the value's side;<br>• one-sided spec: 100 × (limit − value) ÷ (limit − best), with a configured best value (e.g. 0% impurity);<br>• a value at or beyond the limit scores 0;<br>• qualitative parameters map options to sub-scores (e.g. odour Normal = 100, Abnormal = 0).<br>Score = Σ(weight × sub-score) ÷ Σ weight, rounded to one decimal. The score and its breakdown are stored with the decision | v1 (M4–M5) | Recomputing a stored inspection reproduces the same score and breakdown; a worse value never raises the score (property test); with the client's weights, the draft's "92.4 / 100" example reproduces (pending their weights) |
| FR-QC-024 | **Inspection record** (from the client draft): site, inspection type (incoming / retest / in-process), supplier, PO and ordered quantity, supplier document number (delivery note / waybill), truck plate, gross/tare/net weight (net **provisional** while the tare is not yet weighed), bag count, quantity in secondary units (e.g. quintals), material type or variety (e.g. durum wheat) and origin as lot attributes, inspection date, inspector. **Additional parameters:** the spec can mark parameters as optional, and the inspector adds them per inspection (e.g. protein, ash, foreign matter). Result section: grade, score, acceptance status (badge, set by the decision), accepted and rejected quantity, rejection reason (from a list), remark | v1 (M4–M5) | An optional parameter added at inspection is evaluated like a spec line; the grain pack includes moisture, hectolitre weight (**kg/hl**), impurity, germination, hardness, mould, infestation, odour and discolouration |
| FR-QC-025 | **Inspection before unloading with the Gate module off** (the client's own flow): LAB creates the inspection lot against an open PO line and the truck (plate, supplier document number, gross weight, stored or provisional tare, bag count), with the same weight controls as the gate. The GRN is prepared from the decided lot and **cannot be generated before a decision** | v1 (M4–M5) | With the gate off, a wheat delivery's GRN draft carries the inspection's weights, grade and accepted/rejected quantities; generating it before a decision is refused |
| FR-QC-026 | **Finished-product and in-process specs** of the flour pack: moisture, ash, protein, wet gluten, falling number, water absorption, granulation, colour, and net-content checks for packed products (seed data) | v1 (pull-in; else v1.1) | A flour lot is evaluated against its finished-product spec before release (FR-MFG-008) |
| FR-QC-019 | In-process and finished-goods QC with batch release (hold → release / regrade / reject), using the same engine. **v1.1:** a Certificate of Analysis generated per customer shipment | v1 (pull-in; else v1.1) · v1.1: generated CoA | Finished flour produced into QUARANTINE cannot be sold until released |
| FR-QC-020 | Instrument register and calibration records; result entry blocked on out-of-calibration instruments. **Later:** instrument result import (CSV) | v1.1 · Later: import | Entering a result against an overdue instrument is refused |
| FR-QC-021 | Skip-lot inspection and calculated parameters | Later | A supplier on skip-lot is inspected at the configured frequency |

### 7.2 Business rules
- **BR-QC-01:** Stock in QUARANTINE cannot be issued, transferred off-site, reserved or dispatched. It leaves quarantine only by a usage decision.
- **BR-QC-02:** A supplier invoice for goods still in quarantine cannot be matched, so it cannot be paid.
- **BR-QC-03:** Weight-based deductions reduce the **payable** quantity only. Physical stock always equals the measured net quantity.
- **BR-QC-04:** One live inspection lot per delivery per item, and one per GRN line.
- **BR-QC-05:** QC results are insert-only. Corrections are recorded as invalidation plus a new value.

---

## 8. Gate management (FR-GATE)

### 8.1 Requirements
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-GATE-001 | Master data: gate posts (direction, allowed purposes), weighbridges (capacity, division, minimum weight, calibration certificate and due date, status), vehicle types, vehicles (normalised plate, type, ownership, transporter, capacity), drivers (ID, licence, expiry, photo), transporters | v1 (M4–M5) | Duplicate plates (after normalising case, spaces and dashes) are refused |
| FR-GATE-002 | Blacklist for vehicles, drivers and transporters with severity BLOCK or WARN, reason, validity dates, approval to add or lift, and full history | v1 (pull-in; else v1.1) | A BLOCK-listed vehicle cannot enter. WARN shows a banner the supervisor must acknowledge |
| FR-GATE-003 | Guard shifts per gate post: open, members, close. Gate actions need an open shift. **v1.1:** handover checklist (vehicles on site vs system, paper-slip range used, open incidents) | v1 (M4–M5) · v1.1: handover checklist | Recording an arrival without an open shift prompts to open one |
| FR-GATE-004 | Expected arrivals generated automatically from approved PO delivery schedules, transfer dispatches and (M6) released delivery orders, with a time window. They are cancelled when the source is cancelled or closed. **v1.1:** no-show automation | v1 (M4–M5) · v1.1: no-show automation | Approving a PO with 3 schedule lines creates 3 expected arrivals; cancelling the PO cancels them |
| FR-GATE-005 | Arrival registration by plate search (scan or type): picks the matching expected arrival or registers a walk-in with a purpose. A walk-in inbound delivery without an open PO goes on HOLD for a procurement decision (link a PO, or reject at gate). The truck does not enter while on hold | v1 (M4–M5) | An unplanned delivery cannot proceed to weighing until BUY or PMGR acts |
| FR-GATE-006 | Visit purposes (behaviour classes): inbound PO delivery, inbound transfer, outbound transfer, outbound purchase return, **outbound sales dispatch (M6)**, **inbound customer return (M6)**, material gate pass (out/in), empty/non-cargo. Each class has a defined sequence of steps with configurable switches (inspection point, weighing required, checklist). The step sequences are tabulated in [process model §7](MOS-PROCESS-MODEL.md#7-gate-step-sequences-per-visit-class). **Later:** data-driven flow templates | v1 (M4–M5) · Later: flow templates | Each class follows its step sequence in acceptance tests |
| FR-GATE-007 | Gate entry captures vehicle, trailer, driver, transporter, supplier/customer, reference documents (PO or delivery order, supplier delivery note number, declared quantity/weight/bags), seal numbers, checklist responses, photos (where a camera exists), persons count | v1 (M4–M5) | Mandatory fields per purpose are enforced |
| FR-GATE-008 | One open visit per vehicle at a time | v1 (M4–M5) | Registering a second arrival for a vehicle inside the yard is refused |
| FR-GATE-009 | **Weigh tickets (manual in v1):** first and second weighing with gross/tare/net; the weight must be a multiple of the weighbridge division and within minimum/capacity; a photo of the indicator display is mandatory where a camera exists (setting). Out-of-service or calibration-overdue weighbridges cannot be used without a QAM override | v1 (M4–M5) | A 12,345 kg entry on a 10 kg-division bridge is refused. An overdue bridge requires the override |
| FR-GATE-010 | **Blind second-person verification** of manual weights: a different user re-enters the value without seeing the first; a mismatch rejects the verification and logs both attempts. **Coverage is configurable:** every manual weighing (default), a random %, or above a value. Verification is required before exit (outbound). Inbound, it may be deferred a configurable number of hours for single-operator shifts: the GRN can then post, but **matching and payment stay blocked until verified** | v1 (M4–M5) | The operator cannot verify their own weighing; an unverified inbound ticket blocks the invoice match of its GRN |
| FR-GATE-011 | Direction logic: inbound loaded = first gross, second tare; outbound = first tare, second gross. An inbound first weight lower than the second raises a "direction anomaly" hold, **except for visits rejected at the gate**, whose exit weight is checked by BR-GATE-06 instead | v1 (M4–M5) | The anomaly hold requires SSUP to resolve; a rejected truck re-weighed slightly heavier raises no anomaly |
| FR-GATE-012 | Tolerance rules (most specific wins: site/purpose/item category/item/partner): net vs declared, net vs expected, tare vs stored tare, rejected-exit vs entry gross. Each has absolute and % limits and an action: info / warn / approval / block | v1 (M4–M5) | Exceeding a BLOCK tolerance prevents exit until resolved |
| FR-GATE-013 | Stored tare per vehicle (+ trailer combination) with validity. Its use is allowed only where the purpose and tolerance rules permit. Where stored tare is used, a **random forced re-tare** (every Nth visit or p%) is decided on the server and revealed only at the weighing step. A tare deviating from history beyond tolerance raises an alert | v1 (M4–M5) | A forced re-tare cannot be skipped by the operator |
| FR-GATE-014 | Re-weighing creates a superseding weighing with a reason and never deletes the original. No re-weighing after the ticket is consumed by a posted GRN/issue; correction then goes through reversal + void + new linked ticket with approval | v1 (M4–M5) | The ticket history shows superseded weighings |
| FR-GATE-015 | Payable weight basis per supplier/PO: our weight, supplier's weight, or lower of the two. Weight disputes are recorded with evidence and resolved as ours/theirs/split. The resolution produces a debit/credit request and **never modifies the ticket** | v1 (M4–M5) | A resolved dispute creates an AP adjustment request; the ticket is unchanged |
| FR-GATE-016 | **Exit clearance** evaluates, and records every attempt with results: no open blocking hold; all mandatory steps done; every outbound goods movement has its document in the required posted state; weigh ticket complete, verified and within tolerance (or pre-approved); seals recorded if required; exit plate/driver match entry. **In paper mode, only dispatches already posted and listed in the degraded-mode list may exit;** this stop is deliberate | v1 (M4–M5) | Outbound goods without a posted dispatch document or with weight out of tolerance **cannot exit, and this cannot be overridden** |
| FR-GATE-017 | Overridable clearance checks (e.g. seal missing, plate mismatch explained) need an SSUP override with reason code and comment. All overrides appear on an override report | v1 (M4–M5) | The override report lists who, which rule, why and when |
| FR-GATE-018 | Exit pass (printable, QR) issued when the visit reaches an exit-eligible state; scanned or typed at exit | v1 (M4–M5) | An exit pass for an ineligible visit shows the blocking reasons |
| FR-GATE-019 | Material gate passes: returnable / non-returnable, out/in, reason, requester, lines (item or asset tag/serial, qty, estimated value), expected return date, validity, approval. Movements recorded at the gate; partial out/return; overdue alerts | v1 (pull-in; else v1.1) | A non-returnable pass for a stock item cannot exit without its posted inventory document (e.g. scrap sale issue) |
| FR-GATE-020 | Service/contractor visits: persons count, work-permit reference, tools declared in and verified out; minimal people-visitor log | v1.1 | Tools not reconciled block exit unless overridden |
| FR-GATE-021 | Yard board: vehicles on site with purpose, partner, current step, time in step, total dwell and flags (hold, blacklist warning, tolerance issue, verification pending), colour-coded against per-step time targets, with live updates. **v1.1:** TV display mode | v1 (pull-in; else v1.1) · v1.1: TV mode | The board updates within 5 s of a step change |
| FR-GATE-022 | Incidents (seal broken, tamper suspected, document forgery, blacklist attempt, safety, theft, unauthorised entry, spillage, lost slip…) with severity, photos, actions, status, and optional blacklist proposal | v1 (pull-in; else v1.1) | An approved blacklist proposal creates a blacklist entry |
| FR-GATE-023 | **Paper mode** when the system or network is down: registered, pre-numbered paper slip books per gate post, and rules for what may enter or exit during an outage (FR-GATE-016). **v1.1:** a printable degraded-mode pack generated at shift open | v1 (pull-in; else v1.1) · v1.1: printed pack | Slips come only from registered series; the outage rules are shown on the gate screen |
| FR-GATE-024 | **Back-entry workbench:** paper slips entered after recovery with the slip's actual time kept separate from entry time, slip scan or photo mandatory, second-person verification, and **gap detection** (every issued slip number must be used, voided or reported lost). **v1.1:** completion SLA timers and retroactive clearance re-evaluation | v1 (pull-in; else v1.1) · v1.1: SLA, re-evaluation | A missing slip number creates an incident; a back-entered weighing keeps its slip time |
| FR-GATE-025 | Gate reports: daily gate register, weigh ticket register (incl. voided/superseded), turnaround and dwell times, override report, net-vs-declared variance. **v1.1:** appointment adherence, transporter scorecard, stored-tare usage | v1 (M4–M5) · v1.1: extended KPIs | Each report reconciles with the visit and ticket records |
| FR-GATE-030 | **Weigh tickets are numbered without gaps** per weighbridge; voided tickets keep their number | v1 (M4–M5) | A concurrency test yields contiguous ticket numbers per weighbridge; a voided ticket is listed as VOID |
| FR-GATE-026 | **Edge agent weighbridge capture:** live weight display, stability indication, capture only when stable, above minimum and after the scale returned to zero; digitally signed readings; offline capture; manual fallback | Later | A reading can feed only one weighing; the signature verifies in an auditor tool |
| FR-GATE-027 | Direct label/ticket printing to thermal/ZPL printers via the edge agent | Later | A ZPL label prints from a weigh ticket |
| FR-GATE-028 | Multi-document trucks (one truck, several POs or delivery orders, intermediate weighings with allocation) | v1.1 | Allocated quantities sum to the ticket net |
| FR-GATE-029 | ANPR plate capture, barrier/traffic-light control, RFID driver cards, transporter pre-registration, SMS to drivers, offline-capable gate app | Later | A plate read opens the matching expected arrival |

### 8.2 Business rules
- **BR-GATE-01:** A BLOCK-listed vehicle, driver or transporter cannot enter.
- **BR-GATE-02:** One open visit per vehicle.
- **BR-GATE-03:** Gate actions require an open shift at that post.
- **BR-GATE-04:** Inbound PO deliveries must link an open PO line, or be approved as unplanned by procurement before entry.
- **BR-GATE-05:** Where the item's QC policy says BEFORE UNLOADING, sampling and a QC decision precede unloading.
- **BR-GATE-06:** A rejected inbound load's exit weight must be within tolerance of its entry gross (proves nothing was left behind), where configured.
- **BR-GATE-07:** Manual weights need a display photo (where a camera exists) and blind verification by a different user, per FR-GATE-010's coverage setting.
- **BR-GATE-08:** Re-weighing supersedes and never deletes. Tickets consumed by postings are immutable.
- **BR-GATE-09:** **No outbound goods exit without the required posted document and weight within tolerance or pre-approved. Not overridable.**
- **BR-GATE-10:** Every override records who, rule, reason code and comment.
- **BR-GATE-11:** Paper slips come only from registered series. Every number ends used, void or lost.
- **BR-GATE-12:** Weight disputes never modify tickets.

---

## 9. The inbound raw-material process, end to end (FR-INB)

This is the headline flow. It connects Gate, QC, Inventory, Procurement and AP. The process is configurable per item through the QC policy (inspection point BEFORE UNLOADING vs IN QUARANTINE; release immediate vs hold pending long tests) and per installation through the Gate module switch. The [process model](MOS-PROCESS-MODEL.md) draws flows A, B and C as swimlanes P05, P06 and P07.

### 9.1 Flow A: bulk material inspected before unloading, Gate module on (e.g. wheat)
```
Arrive (expected or walk-in) ─► Check-in (supplier & PO valid) ─► First weighing (gross, photo, blind verify)
   ─► Sampling (inspection lot + samples + labels auto-created) ─► Lab tests ─► "Calculate quality" ─► Usage decision
        ├─ REJECT ─► (optional exit weighing ≈ entry gross) ─► Rejection note to supplier ─► Exit pass ─► Exit
        └─ ACCEPT / ACCEPT WITH DEDUCTION / CONCESSION / PARTIAL
              ─► Unload authorised (pre-flight checks) ─► Unload to storage unit / silo ─► Second weighing (tare)
              ─► Net (− bag tare) & payable quantity computed ─► GRN PREPARED by the system
              ─► Exit pass ─► Exit (the empty truck may leave once the tare is verified)
              ─► Storekeeper confirms storage unit, batch, rejected disposition ─► GRN generated (posted)
              ─► [async] PV DRAFTED from PO + GRN (+ invoice, or self-billed voucher)
              ─► Finance officer captures/confirms invoices and amounts, submits ─► Approval
              ─► Payment recorded with proof (cheque / transfer; executed at the bank) ─► Journals exported
```

### 9.2 Flow B: packaged material inspected in quarantine
Arrive → check-in → (optional weighing) → unload and count → **GRN into QUARANTINE** (inspection lot auto-created) → exit → sampling and tests → usage decision → quarantine stock moves to AVAILABLE and/or REJECTED → invoice match unblocked → PV.

### 9.3 Flow C: the client's own flow, Gate module off
PO issued → truck arrives at stores → **LAB creates the inspection lot** against the PO line and truck (plate, supplier document no., gross, stored or provisional tare, bags; FR-QC-025) → samples, results, grade and score → usage decision → unload to silo/store → tare (where weighed) → **GRN prepared from the inspection** → storekeeper confirms and generates it → PV drafted → finance officer submits → approval → payment with proof. This is acceptance scenario AS-13.

### 9.4 Requirements
| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-INB-001 | The system runs flows A, B and C end to end with no re-keying: data captured at the gate or inspection (supplier, PO, weights, bags, delivery note) flows into the inspection lot, GRN, invoice match and PV | v1 (M4–M5) | The end-to-end acceptance tests create a GRN and a PV with no manual data re-entry |
| FR-INB-002 | **On acceptance, the GRN is prepared automatically**: at the second weighing (flow A), from the decided inspection lot (flow C), or at receipt (flow B), with net quantity, payable quantity, quality deductions, lot and suggested storage unit. **Default mode "confirm":** the GRN is prepared as a DRAFT, and STK checks storage unit, batch and rejected disposition, then generates it (posting). **Mode "automatic":** it posts immediately when every required field is determined | v1 (M4–M5 gate off · M4–M5 at tare) | In confirm mode the draft appears in the storekeeper's queue within the same user action; in automatic mode the GRN number appears on the visit immediately |
| FR-INB-003 | **The supplier payment process is triggered automatically:** when the GRN posts, a **PV draft** is created from PO + GRN (+ matched invoice, or a posted self-billed voucher for self-billing suppliers) and assigned to APC, who confirms invoices and amounts and submits it for approval. In mode "automatic", a PV whose invoice matched within tolerance is submitted directly | v1 (M3–M4) | Duplicate event delivery never creates a second PV for the same GRN; a draft whose invoice has not arrived shows "awaiting invoice"; a PV is never drafted for a GRN that was reversed before the event was processed |
| FR-INB-010 | **Automation mode settings** per installation: GRN generation (confirm / automatic) and PV (draft for APC / auto-submit). Changing a mode is audited and does not affect documents already in flight. **v1.1:** per-site modes | v1 (M3–M4) · v1.1: per site | Switching GRN mode affects only deliveries that reach GRN preparation after the change |
| FR-INB-004 | Unloading is authorised only after pre-flight checks that could otherwise make the GRN fail later: PO line open with enough tolerance headroom (estimated from tare history), period open, item/storage unit active and with capacity, supplier not blocked | v1 (M4–M5) | A PO short-closed after check-in blocks unloading with a clear reason |
| FR-INB-005 | If GRN preparation or confirmation fails (e.g. over tolerance), the weighing is kept, the visit goes to RECEIPT_EXCEPTION, and an exception task is assigned. The truck can exit only with a supervisor-override exit pass | v1 (M4–M5) | The exception task appears; its resolution posts the GRN |
| FR-INB-006 | Missing finance setup (tax profile, bank account for a transfer-paid supplier) never blocks receiving. The PV step creates an exception task instead | v1 (M4–M5) | See FR-PLT-050 |
| FR-INB-007 | Every step is idempotent: double-clicks, retries and duplicate events never create duplicate weighings, inspection lots, GRNs or PVs | v1 (M4–M5) | Replay tests pass for every step |
| FR-INB-008 | The failure and exception cases in §9.5 are handled exactly as specified for the modules enabled. Each has an acceptance scenario | v1 (M4–M5) · standard depth: pull-in, else v1.1 | The scenario suite for §9.5 is green (gate-dependent cases only with the gate on) |
| FR-INB-009 | Supplier notifications (where email is configured): arrival acknowledged, rejection note with results, receipt with deductions breakdown, payment advice | v1 (pull-in; else v1.1) | Each notification is sent once with the correct content; the same documents can be printed when email is not configured |

### 9.5 Failure and exception handling (normative)
| # | Situation | Required behaviour |
|---|---|---|
| 1 | No PO / PO unapproved / wrong supplier | Visit on HOLD_NO_PO. BUY/PMGR links a PO (audited) or rejects → rejection exit pass. No unloading without a PO |
| 2 | Wrong PO found before GRN | Relink with reason. If item, supplier or spec changes, the inspection lot re-resolves its spec and re-evaluates |
| 3 | Wrong PO found after GRN | If nothing invoiced or consumed: reverse and re-post. Otherwise reverse the dependent invoice first, or return and re-receive. (v1.1: a GRN PO-reassignment document, FR-PRC-016) |
| 4 | Gross weight typo | Supersede with reason before the next step; after it, supersede + SSUP approval if the difference exceeds tolerance |
| 5 | Suspicious tare (e.g. water tank, driver in cab) | Tare-history deviation → SSUP confirmation with photo; flag carried to the GRN and visible to AP |
| 6 | Truck leaves without second weighing | GRD records "exit without tare" (SSUP). GRN drafted with estimated tare or counted bags, flagged; PMGR approval needed to post; payment block until resolved; supplier notified |
| 7 | Accepted truck leaves without unloading | Visit ABANDONED; inspection lot closed without GRN; samples retained; pre-created lot voided |
| 8 | Lab rejects before unloading | REJECTED_AT_GATE; no GRN; optional exit weighing within tolerance of entry gross; rejection note + retention sample kept |
| 9 | Lab rejects after unload (quarantine flow or long tests) | QUARANTINE → REJECTED; then supplier return, scrap (approval) or concession. The invoice cannot match while in QC hold, so no payment can have happened |
| 10 | Partial acceptance | Before unloading: the rejected part stays on the truck (the tare includes it; net = accepted). In quarantine: split status change on the same lot. Held-rejected at receipt: REJECTED status (FR-PRC-013) |
| 11 | Weight dispute | GRN per the agreed weight basis; the variance vs declared is recorded and shown at matching; resolution becomes a quantity correction + AP adjustment |
| 12 | Quality deduction disputed | Retention sample → third-party test → superseding decision (approval) → revaluation of the on-hand share + AP credit/debit adjustment |
| 13 | Over-delivery beyond tolerance | Per excess policy: block (reload) or require approval (FR-PRC-014) |
| 14 | Under-delivery | PO line stays open; auto-closes if within under-tolerance |
| 15 | PO closed/amended, period closed or item blocked between pre-flight and GRN | RECEIPT_EXCEPTION with typed reason and resolution actions (reopen PO, post in a soft-closed period with permission, choose another PO) |
| 16 | Duplicate check-in / double click | Refused (one open visit per vehicle; idempotency keys) |
| 17 | Two operators act at once | One succeeds; the other receives a conflict with the fresh state |
| 18 | Photo upload failed | Weighing saved as "photo pending", allowed for `photo_pending_max_hours` (default 4); it blocks invoice matching, not the GRN |
| 19 | Sample mislabelled or lost | New retest sample with reason; results on the invalid sample invalidated |
| 20 | Result error found after decision | Before unloading: reopen the decision. After GRN: superseding decision (approval) + compensating documents |
| 21 | System crash mid-step | Nothing is half-done (single transaction); the user retries safely |
| 22 | Automated step fails (technical) | Automatic retry, then dead-letter with alert. Business failures → exception task |
| 23 | Invoice price ≠ quality-adjusted GRN price | Match exception (price hold): accept the variance (approval → purchase price variance), request a credit note, or amend the PO for future receipts |
| 24 | Invoice quantity > payable quantity | If the excess equals the computed quality deduction (within rounding): a quality debit note is created automatically and the invoice matches (FR-AP-005). Otherwise: quantity hold (partial match up to payable, or wait for more receipts) |
| 25 | Invoice arrives before GRN | "Awaiting receipt"; re-matched automatically when the GRN posts |
| 26 | Invoice while stock is in QC | QC hold until the usage decision |
| 27 | Duplicate supplier invoice | Refused (same supplier + normalised invoice number + fiscal year); soft warning for same amount and date |
| 28 | PV rejected / cancelled | Its invoice lines return to open; a new PV may be raised; the PV keeps its number |
| 29 | Bank account changed after PV approval | Mismatch detected at payment recording → blocked → re-approval required |
| 30 | Supplier blocked after approval | Payment recording refused |
| 31 | Payment recorded wrongly | Payment reversal document → allocations reversed → WHT certificate voided (number kept) → re-record |
| 32 | GRN reversal after match or payment | Blocked. Reverse the invoice first, or use supplier return + debit note after payment |
| 33 | Invoice currency ≠ PO currency | Refused in v1, unless the client confirms that USD POs are invoiced and paid in ETB (review §6); then the invoice converts at a recorded rate and the difference posts as an FX variance |
| 34 | Withholding miscomputed | WHT timed at payment: payment reversal and re-record. WHT timed at invoice: invoice reversal and re-entry. Either way, the WHT certificate is voided (number kept as VOID) and a new one issued |

---

## 10. Payables sub-ledger and supplier payment (FR-AP)

| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-AP-001 | Supplier invoice capture: supplier, invoice number (normalised for duplicate detection), date, lines linked to PO lines (goods, services, charges), tax codes, totals, attachment. Due date from payment terms. **Invoices can be captured inline from the PV screen ("Add invoice")** as well as from an invoice list; either way they become separate invoice records that go through matching | v1 (M3–M4) | Duplicate detection per §9.5 #27; an invoice added on the PV screen appears in the invoice list |
| FR-AP-002 | **Three-way match** (PO ↔ GRN ↔ invoice) for stock items; two-way (PO ↔ invoice) for services and charges. Matching uses the GRN's **payable** quantity and **quality-adjusted price** (both snapshotted on the GRN), so it is effectively four-way with QC | v1 (M3–M4) | Match results: MATCHED, AWAITING RECEIPT, ON_HOLD (price / quantity / weight / QC / verification pending); an invoice at the grade-adjusted price matches without a price exception |
| FR-AP-003 | Tolerances (installation default, optionally per category or supplier): price % and absolute (both directions), and invoiced-vs-payable quantity %, weight %, and a total rounding amount. Differences within tolerance post to purchase price variance (price) or rounding (amount) | v1 (M3–M4) | An invoice 0.5% above the adjusted price with a 1% tolerance matches and posts 0.5% to PPV; 2% goes to price hold |
| FR-AP-004 | Match exceptions are resolved through the approval matrix: accept the variance (posted as PPV), request a credit note, or keep on hold. The match-exception approver is recorded for SoD | v1 (M3–M4) | An accepted variance posts to PPV in the journal |
| FR-AP-005 | **Quality debit note** (gap-free number, printable), created automatically when an invoice exceeds the payable quantity or adjusted price by exactly the computed quality deduction (within rounding), and allocated against that invoice. Any other excess goes to hold (§9.5 #24) | v1 (M3–M4) · standard depth: pull-in, else v1.1 | An invoice at gross 20,000 kg against payable 19,900 kg with a recorded 100 kg deduction matches with an auto debit note; an invoice at 20,100 kg goes to quantity hold |
| FR-AP-006 | Self-billing: MOS generates the purchase voucher (gap-free number) from the GRN for flagged suppliers, including deduction lines (rules in FR-AP-018) | v1 (pull-in; else v1.1) | The voucher PDF matches the GRN and its deductions |
| FR-AP-007 | **PV (payment request)**, numbered `PV-…` at submission, raised by APC or drafted by the system (FR-INB-003).<br>**Header:** request date, requested by, department, supplier, **payment method**, currency, priority, purpose.<br>**Reference documents:** PO(s) and GRN(s) with dates, shown as linked cards.<br>**Supplier payment details** (for transfers: bank, branch, account name and number, SWIFT) are read-only from the supplier master and snapshotted.<br>**Invoice lines:** invoice no., date, PO, GRN, invoice amount, previously paid, balance due, **amount to pay** (full or partial), and the gross / WHT / net breakdown (FR-AP-013).<br>**Requested amount = Σ amounts to pay** (derived, never typed).<br>Approval per the matrix (amount; payment to a recently changed account adds a stage); approver comments are mandatory. **Changing the payment method after approval requires re-approval** | v1 (M3–M4) | The requested amount can never exceed the open, unreserved balance; a PV cannot be submitted with a total that disagrees with its lines; switching an approved PV from transfer to cheque sends it back for approval |
| FR-AP-008 | **Payment recording** against an **approved** PV:<br>• payment number and date;<br>• method, which must equal the PV's;<br>• the **company paying account** (FR-AP-015; for a cheque, the account it is drawn on);<br>• **cheque number and cheque date** (a future cheque date is allowed), linked to the cheque register;<br>• bank reference (unique per account);<br>• amount (= net payable) with **amount in words**;<br>• invoices to pay, selectable only from the approved PV's lines, amounts ≤ approved;<br>• a **proof-of-payment attachment** (deposit slip, transfer advice or cheque copy), required before posting (setting).<br>Posting allocates to invoices, computes withholding when it is due at payment, and issues a WHT certificate (gap-free) | v1 (M3–M4) | The bank-detail hash is re-verified at recording for transfers (§9.5 #29); a payment exceeding the approved amount, drawn on a supplier account, or without required proof, is refused |
| FR-AP-009 | Supplier balance, statement (opening, transactions, closing) and ageing with configurable buckets as of any date | v1 (M3–M4) | The statement closing balance equals the ledger balance |
| FR-AP-010 | **Accounting export:** journals are generated at every posting and mapped to external account codes through a configurable account map. Sources:<br>• procurement: GRN (Dr inventory / Cr GRNI), supplier invoice, debit note, self-billed voucher, payment, supplier return;<br>• inventory: adjustment, count, revaluation, transfer via in-transit;<br>• production: issue, output and settlement;<br>• sales: goods issue (COGS), customer invoice, credit note, receipt.<br>Exported as CSV batches (idempotent re-download, checksum) | v1 (M3–M4) CSV · Later: package adapters | The sum of debits = credits per entry; inventory journals reconcile to inventory valuation; the GRNI balance equals uninvoiced GRN value plus unresolved invoice variances, as listed by the GRNI reconciliation (FR-AP-019) |
| FR-AP-011 | Advances/prepayments against POs and payment runs (batch proposals). **Later:** cheque printing | v1.1 advances, payment runs · Later: cheque printing | An advance is offset against the first invoice of its PO |
| FR-AP-012 | **Cheque register** per company bank account: cheque numbers unique per account; spoiled or voided cheques recorded (number kept, reason) rather than deleted; a cheque number cannot be reused; register report by status (issued, voided, cleared, stale) | v1 (M3–M4) | Recording a duplicate cheque number on the same account is refused; a voided cheque stays in the register |
| FR-AP-013 | **Gross, VAT, WHT and net on PVs and payments:** each PV line and payment shows gross, VAT, withholding and net. The WHT threshold is assessed on the invoice (or contract) value, not per partial payment. The payment amount is the net; the WHT certificate agrees with the withheld amount. Whether the company is a withholding agent, and the rates, are client inputs | v1 (M3–M4) | A 150,000 gross invoice with a withholding code shows net and WHT on the PV; paying it in two parts withholds on the full invoice value once |
| FR-AP-014 | **PV lifecycle** (§1.5): DRAFT → SUBMITTED → APPROVED → PARTIALLY_PAID → PAID; REJECTED; CANCELLED, with an ageing report of approved-but-unpaid PVs | v1 (M3–M4) | A PV with one of two invoices paid shows PARTIALLY_PAID; a cancelled PV releases its reserved amounts |
| FR-AP-015 | **Company paying accounts:** bank accounts, cash boxes and mobile-money wallets, each with its own export account code, currency and (cash) a per-payment limit | v1 (M1–M4) | A cash payment above the cash-box limit is refused; each account exports to its own code |
| FR-AP-016 | **Cheque books:** books registered by number range per bank account, with gap detection (every leaf is issued, voided or reported missing). Actions: CLEARED (with clearing date) and STALE (after a configurable age). Voiding a cheque on a posted payment requires the payment reversal | v1 (pull-in; else v1.1) | A skipped leaf appears on the gap report; voiding a cheque of a posted payment without reversing the payment is refused |
| FR-AP-017 | **Tax registers** per period, exportable to CSV/XLSX: input VAT by supplier and invoice; withholding by supplier TIN and certificate number; (M6) output VAT by customer and invoice | v1 (pull-in; else v1.1) · v1 (M6) output VAT | Register totals equal the VAT and WHT journal totals for the period |
| FR-AP-018 | **Self-billing rules:** only suppliers flagged for self-billing; no input VAT unless the supplier is VAT-registered; the voucher is confirmed by APC (who must not be the GRN poster), printed for the supplier's signature, and the signed copy is attached before payment (setting) | v1 (pull-in; else v1.1) | A self-billed voucher for a non-VAT supplier carries no input VAT; the GRN poster cannot confirm it |
| FR-AP-019 | **GRNI reconciliation report** as of any date: posted GRNs not yet invoiced, invoices not yet received, and unresolved invoice variances (held price or quantity differences), by supplier and PO, with ageing | v1 (pull-in; else v1.1) | The report total equals the GRNI journal balance for a seeded dataset; accepting a held variance moves it from the report to PPV |

**Business rules.**
- **BR-AP-01:** invoiced − quality debit notes ≤ payable (+ quantity tolerance) per GRN line.
- **BR-AP-02:** reserved + paid ≤ payable per invoice.
- **BR-AP-03:** invoices for goods in QC hold, or with an unverified weigh ticket, cannot match.
- **BR-AP-04:** a transfer payment requires an ACTIVE supplier bank account past its cooling-off whose details hash equals the PV snapshot; a cheque requires the payee name to equal the supplier's legal name; a cash payment requires a paying cash box within its limit and a signed receipt attachment.
- **BR-AP-05:** SoD per §15.1: PO creator ≠ GRN poster; neither may approve a PV that covers their PO or GRN; PV requester ≠ PV approver; PV approver ≠ payment recorder.

---

## 11. Production (FR-MFG)

Production is **in v1** (ADR-0018), built in M4–M5 at lean depth. The client has not yet described its production process, so these requirements follow flour-milling practice and are validated at the M4 client workshop (implementation plan §7).

| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-MFG-001 | Work centres (e.g. cleaning, milling line 1, packing line), with line-side and output storage units, and shift calendars. **v1.1:** rates and OEE basis | v1 (M5, lean) | A milling work order defaults its input silo and output storage unit from the work centre |
| FR-MFG-002 | Routings (versioned): operations with work centre, run rate, setup, reporting point | v1.1 | A routing's operations appear on the work-order sheet |
| FR-MFG-003 | **Recipes/BOMs in one model**, versioned and approved: inputs (qty per base, scrap %, issue method manual/backflush) and outputs (**primary, co-product, by-product, scrap, non-stock waste**) with expected yield and a min/max band, a **mass-balance band** (configurable gain/loss, e.g. tempering water adds mass and invisible loss removes it), and a cost-allocation method for co-products (fixed ratio / relative sales value). **v1.1:** alternates and lot constraints (e.g. protein ≥ 12.5) | v1 (M5) | A recipe whose outputs fall outside its mass-balance band cannot be approved; allocation ratios sum to 100% |
| FR-MFG-004 | MPS-lite and MRP-lite (demand-driven suggestions, netting, pegging) | Later | Accepting a suggestion creates a PR |
| FR-MFG-005 | Work orders: **standard** (batch), **campaign** (a milling run per shift or day, settled at its end) and **packing orders**. Release snapshots the recipe, soft-reserves materials (FR-INV-015), checks QC holds, and prints a work-order sheet. **v1.1:** rework and repack orders | v1 (M5) · v1.1: rework, repack · standard depth: pull-in, else v1.1 | Only AVAILABLE, released lots can be issued; a campaign order accepts several output postings before settlement |
| FR-MFG-006 | Material issue: silo draw-down by quantity (lots allocated first-in-first-out, FR-INV-019), scan issue for packaged inputs, returns to stock, and backflush of packaging on output. A backflush shortage never blocks output; it creates a pending issue (FR-INV-011) and blocks work-order close | v1 (M5) | Issuing 20,000 kg from a silo holding two lots consumes the older lot first; a backflush shortage leaves stock non-negative and the order unclosable until resolved |
| FR-MFG-007 | Output recording by lot with lot attributes (e.g. flour type, grade), labels, and initial status QUARANTINE when finished-goods release is required. **v1.1:** pallets / handling units | v1 (M5) | Posting 15,000 kg of flour and 4,500 kg of bran creates two lots with the recipe's statuses and the work order as source |
| FR-MFG-008 | Finished-goods QC and release using the QC engine (FR-QC-019, FR-QC-026): sample per lot or per shift; decision release / regrade (to another item or grade) / reject. **v1.1:** in-process time-window holds; generated CoA | v1 (pull-in; else v1.1) · v1.1: in-process holds, CoA | A flour lot cannot be delivered while in QUARANTINE; a regrade moves the quantity to the lower-grade item at the same cost |
| FR-MFG-009 | **Traceability:** backward and forward trace through work orders, including the silo lots allocated first-in-first-out to each issue, and a recall report listing affected output lots, deliveries and customers. **v1.1:** quantitative blend-lot genealogy for commingled silos | v1 (M5) · v1.1: blend genealogy · standard depth: pull-in, else v1.1 | A mock recall from one supplier wheat lot lists every flour lot and customer delivery that consumed it |
| FR-MFG-010 | Full actual costing: labour and overhead absorption, variance analysis (usage, mix, yield, absorption) | Later (v1: FR-MFG-014) | A worked example with labour and overhead reproduces |
| FR-MFG-011 | Supervisor screens for the line: my orders today, issue by scan or quantity, record output in ≤ 3 interactions, downtime start/stop with reason, shift log. Responsive web; usable on a 10" tablet with ≥ 48 px targets where tablets are provided | v1 (M5, responsive) | Recording an output takes ≤ 3 interactions in a usability test |
| FR-MFG-012 | Shift logs and downtime (non-overlapping per work centre, with reason codes). **v1.1:** OEE from manual logs with a data-completeness indicator | v1 (pull-in; else v1.1) · v1.1: OEE | Two overlapping downtime entries on one work centre are refused |
| FR-MFG-013 | Production reports: **extraction rate** (flour output ÷ wheat input) per run and per day, yield and by-product % against the recipe band, waste by reason, downtime Pareto, production vs plan. **Later:** cost and variance reports | v1 (M5, lean) · standard depth: pull-in, else v1.1 | For a run of 20,000 kg wheat producing 15,000 kg flour and 4,600 kg bran, extraction shows 75.0% and by-product 23.0%, with the 2.0% difference as loss |
| FR-MFG-014 | **Material-cost settlement** (the v1 valuation of production output): the work order's cost = issued inputs at moving average + packaging. By-products are credited at a fixed or net-realisable price. The remaining cost is allocated to primary and co-products by the recipe's method. Output posted before settlement uses a provisional cost (reference cost), trued up at settlement by revaluation. Journals are exported | v1 (M5) | A run costing 600,000 with 4,600 kg bran credited at 8.00/kg (36,800) values 15,000 kg flour at 37.5467/kg; the output lots' total value equals the order's net cost |
| FR-MFG-015 | **Milling run:** the grist (blend of wheat lots or silos with target ratios) is recorded, as are tempering water (litres, conditioning time) and the mass balance against the recipe band. Extraction rate is computed per run and per day | v1 (M5) · standard depth: pull-in, else v1.1 | A run whose actual blend deviates from target ratios beyond tolerance shows a warning; tempering water appears in the mass balance |
| FR-MFG-016 | **Packing:** bulk flour is packed into bag SKUs (e.g. 50 / 25 / 10 / 5 kg); bags, labels and thread are backflushed; the lot and dates print on the bag label; net-content sample checks are recorded against the finished-product spec | v1 (M5) | Packing 200 × 50 kg bags consumes 10,000 kg flour and 200 bags, and creates a packed lot with a label |
| FR-MFG-017 | **Fortification dosing** (target vs actual premix per tonne), if mandatory for the client's products | v1 (pull-in; else v1.1) | A run whose dosing deviates from target beyond tolerance is flagged on release |
| FR-MFG-018 | **Planner's demand and material view:** per finished item, the open sales-order quantity, available-to-use stock, planned output of released work orders and the shortfall, with "create work order"; per raw material, the requirement of planned and released work orders against available-to-use stock, with "create PR". Full MRP stays Later (FR-MFG-004) | v1 (pull-in; else v1.1) | With 40 t of flour on open orders, 25 t available and a released order for 10 t, the view shows a 5 t shortfall and pre-fills a work order |

**Business rules.**
- **BR-MFG-01:** Inventory close for a period is refused while any work order with postings in that period is not SETTLED. Campaign work orders therefore end within a period.

---

## 12. Sales and receivables (FR-SAL)

Sales is **in v1** (ADR-0018), built in M5–M6 at lean depth. The client has not yet described its sales process. These requirements are validated at the M5 client workshop.

| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-SAL-001 | Customers: group, TIN/VAT, price list, payment terms, credit limit, credit check mode (none / warn / block), overdue block days, manual credit hold, sales rep, territory, ship-to addresses | v1 (M5–M6) | A customer on manual credit hold cannot have a new order approved |
| FR-SAL-002 | Price lists (validity, tax-inclusive flag, floor price) and a line-discount limit per role; deterministic price resolution, snapshotted on order lines. **v1.1:** quantity breaks and discount rules | v1 (M5–M6, lean) · v1.1: breaks, rules | An order line's price equals the price list in force on the order date and does not change when the list changes later |
| FR-SAL-003 | Quotations converting to sales orders | v1.1 | A quotation converts to an order with the same lines and prices |
| FR-SAL-004 | Sales orders with approval triggers: credit exposure (open receivables + delivered-not-invoiced + open orders + this order > limit), overdue beyond N days, price below floor, discount above limit, manual hold. Approver ≠ creator. **v1.1:** "first order" trigger | v1 (M5–M6) | Splitting an order cannot bypass the credit limit; an order below floor price routes to SMGR |
| FR-SAL-005 | Availability check and soft reservation at approval (FR-INV-015). **v1.1:** lot-level hard reservation at picking | v1 (pull-in; else v1.1) · v1.1: hard reservation | Approving an order for more than available-to-use warns and reserves only what is available |
| FR-SAL-006 | **Delivery orders:** release (creates an expected outbound gate visit, FR-SAL-015) → pick (scan; validates lot status) → load → outbound weighing for bulk (loaded quantity from the verified net) → **goods issue** (posts stock and COGS) → gate exit → delivered. **v1.1:** dispatch plans with vehicle capacity checks | v1 (M5–M6) · v1.1: dispatch plans | Gate exit requires a posted goods issue and weight within tolerance (BR-GATE-09) |
| FR-SAL-007 | Proof of delivery via a tokenised, single-use driver link (receiver name, signature, photos, accepted quantity, shortage/damage), with a discrepancy workflow. **In v1:** delivery confirmation is recorded manually with the signed delivery note attached | v1.1 (v1: manual POD with scan) | A used or expired link is refused |
| FR-SAL-008 | Invoicing from posted deliveries (gap-free numbering at posting, tax per line); cash sales (exit blocked until paid); customer receipts (cash, bank transfer, cheque with clearance, mobile money) with allocation to invoices; bounced-cheque reversal | v1 (M5–M6) | An invoice cannot exceed its delivered quantity; a bounced cheque reverses its receipt and reopens the invoice |
| FR-SAL-009 | Customer returns received through the gate into QUARANTINE, with a QC decision (restock / downgrade / scrap) and a credit note linked to the original invoice. **v1.1:** rework disposition | v1 (pull-in; else v1.1) | A credit note cannot exceed the invoiced quantity and price of its original invoice line |
| FR-SAL-010 | Customer statements, ageing, and sales reports by product, customer, region and salesperson. **v1.1:** DSO, fill rate/OTIF, margin | v1 (M5–M6) · v1.1: DSO, OTIF, margin · standard depth: pull-in, else v1.1 | The ageing total equals the receivables balance |
| FR-SAL-011 | Fiscal device / e-invoicing as a pluggable adapter | Later (v1 if the client confirms it is legally required) | An invoice is registered with the fiscal device and its fiscal number stored |
| FR-SAL-012 | **Fiscal receipt reference:** each customer invoice and cash sale records the external fiscal receipt number and machine ID. A setting blocks cash-sale exit without it | v1 (M5–M6) | With the setting on, a cash-sale truck cannot exit until the fiscal number is entered |
| FR-SAL-013 | **Customer advances and cash-before-delivery:** a deposit is recorded with its slip before release and allocated at invoicing | v1 (M5–M6) · standard depth: pull-in, else v1.1 | A cash-before-delivery order cannot be released until its deposit covers the order value |
| FR-SAL-014 | **Cash sessions** for CSH: open with float, receipts, count at close, variance with reason, handover | v1 (M5–M6) | A session cannot close without a count; a variance requires a reason |
| FR-SAL-015 | A released delivery order **creates an expected arrival for the outbound gate visit** (replacing dispatch plans in v1) | v1 (M5–M6) | Releasing a delivery order shows it on the yard board as expected |
| FR-SAL-016 | **Customer onboarding and credit-limit changes are approved:** a customer drafted by SREP is approved (lifecycle §1.5) after CRC sets the credit limit, check mode and terms; every later credit-limit change is its own approval, and the requester cannot approve it (SOD-17) | v1 (M5–M6) | An unapproved customer cannot receive a sales order; a credit controller cannot approve their own limit increase |
| FR-SAL-017 | **Price-list versions are approved** (SMGR, per the approval matrix) before they take effect; floor prices are part of the approved version | v1 (M5–M6) | A price-list version in DRAFT is never used for price resolution |
| FR-SAL-018 | **Credit notes without a physical return** (price correction, shortage or damage claim) need a reason, evidence (e.g. the signed delivery note) and approval through the matrix; they never exceed the original invoice line | v1 (pull-in; else v1.1) | A shortage credit without an attachment cannot be submitted; an approved one reduces the customer balance |

**Business rules.**
- **BR-SAL-01:** Credit exposure = open receivables + delivered-not-invoiced + open approved orders; the check runs at order approval and again at delivery release.
- **BR-SAL-02:** No goods issue without an approved sales order and a released delivery order; no gate exit without a posted goods issue (BR-GATE-09).
- **BR-SAL-03:** Invoices come only from posted deliveries (or cash sales); credit notes never exceed the original invoice line.

---

## 13. Reporting and dashboards (FR-RPT)

| ID | Requirement | Tag | Acceptance criterion |
|---|---|---|---|
| FR-RPT-001 | Role dashboards, delivered with each module: PR and PO approvals (total, pending, approved, rejected, **average approval time** with trend), procurement (open PRs/POs, overdue deliveries), warehouse (stock alerts, pending GRN drafts, counts, silo levels), lab (pending samples, OOS), gate (yard board, today's arrivals), finance (match exceptions, PVs, approved-unpaid, ageing), production (orders today, extraction rate, downtime), sales (orders, credit holds, deliveries, receivables), management (inbound volume, acceptance rate, spend, stock value, production, sales) | v1 (per module, M1–M6) | Each tile drills down to its underlying list |
| FR-RPT-002 | Every report supports date range and site filters, export, and respects user scopes | v1 (M1) | A site-scoped user's export contains only their sites' rows |
| FR-RPT-003 | Heavy reports run as background jobs and notify on completion | v1 (pull-in; else v1.1) | A 12-month movement report does not block the UI |
| FR-RPT-004 | Exception reports (§15.3) are available to FINC, PLM and AUD | v1 (pull-in; else v1.1) | Each exception report lists only events of its type, and AUD can open all of them |
| FR-RPT-005 | **Totals are never summed across units of measure:** reports show one total per UoM, or convert explicitly to a stated UoM | v1 (M3–M4) | A stock report with kg and pcs rows shows a total per UoM (the client draft sums them into "KG"; review §5) |

---

## 14. Non-functional requirements (NFR)

Targets that depend on the hosting and hardware choice are **proposed defaults**, confirmed with the client once the infrastructure questions are answered ([ADR-0019](adr/ADR-0019-infrastructure-not-assumed.md)).

| ID | Area | Requirement | Verification |
|---|---|---|---|
| NFR-01 | Scale (per installation, design point) | 50–300 named users, ≤ 5 sites, 200 trucks/day/site, 5,000 stock movements/day, 10 years of history online | A load-test dataset at these volumes |
| NFR-02 | Performance | p95 < 300 ms for reads and lists; p95 < 800 ms for postings; yard board refresh ≤ 5 s; search < 1 s, measured on the agreed production hardware | Load test at NFR-01 volumes (WP-13) |
| NFR-03 | Availability | **Proposed default:** 99.5% monthly during operating hours, with planned maintenance announced in advance. Final target set after the hosting decision | Uptime monitoring report during hypercare |
| NFR-04 | Recoverability | **Proposed default:** RPO ≤ 15 min, RTO ≤ 4 h, daily full backup, an off-host copy, and a restore drill before go-live and quarterly after. Final targets set after the hosting decision | Recorded restore drill (M7) |
| NFR-05 | Security | OWASP ASVS Level 2; TLS; secure headers and CSP; rate limiting; encrypted sensitive fields (bank accounts, TOTP secrets); secrets never in code or logs; least-privilege database roles; dependency scanning with a high/critical gate | Security review checklist and automated tests (WP-13) |
| NFR-06 | Privacy | Personal data (drivers, users, contacts, individual suppliers) is minimised; a person's data can be exported, and deleted where the law allows; audit entries pseudonymise personal data in free-text details | Automated test of the export and deletion job |
| NFR-07 | Auditability | Every business action is attributable to a user (or system actor) with timestamp and source; the tamper-evident audit chain is verified daily | Daily verification job result; tamper test |
| NFR-08 | Usability | Core flows are usable on desktop and phone browsers, and on 10" tablets where tablets are provided; WCAG 2.1 AA for core flows; scan-first entry where scanners exist; ≤ 3 interactions for the most frequent floor actions | Usability test with real operators during UAT |
| NFR-09 | Browser support | Last 2 versions of Chrome, Edge and Firefox; Android Chrome on tablets | Playwright smoke on each |
| NFR-10 | Localisation | English/Gregorian in v1; all strings externalised; dates stored in UTC and rendered through one formatter; Amharic + Ethiopian-calendar pack Later | Lint rule rejecting hard-coded UI strings |
| NFR-11 | Degraded operation | Gate and receiving can continue on paper during an outage (FR-GATE-023/024); outbound exits follow FR-GATE-016's outage rule | Acceptance scenario AS-10 |
| NFR-12 | Maintainability | Modular monolith with enforced module boundaries; ≥ 1 automated acceptance test per v1 requirement; every decision recorded as an ADR | CI coverage report of FR IDs to tests |
| NFR-13 | Portability | Runs on Linux or Windows Server with a supported Node.js LTS and PostgreSQL; containers are optional packaging; served behind any reverse proxy under a path or a sub-domain (ADR-0019) | Install test on the chosen target in M7 |
| NFR-14 | Device and service fallbacks | Every feature that depends on a device or external service (camera, label printer, scanner, TV display, email relay, virus scanner, internet access) has a manual fallback and an on/off setting, so a missing device never blocks a process | For each such feature, an acceptance test with the device switched off |

---

## 15. Controls catalogue

### 15.1 Segregation of duties (default rules; the administrator may tighten them)
| Rule | Conflict |
|---|---|
| SOD-01 | Requester / submitter ≠ approver of the same document |
| SOD-02 | PO creator ≠ GRN poster (for that PO) |
| SOD-03 | GRN poster ≠ approver of a PV that covers that GRN |
| SOD-04 | PO creator ≠ approver of a PV that covers that PO |
| SOD-05 | PV requester ≠ PV approver; match-exception approver ≠ PV requester. (The requester may also record the payment, as in the client's process where the finance officer does both, because an independent approver sits between them. A setting can require the payer to be a different person) |
| SOD-06 | Bank-detail change requester ≠ approver (≠ approver 2 when the second approval is enabled) |
| SOD-07 | Supplier creator ≠ supplier approver |
| SOD-08 | Analyst who entered or verified results ≠ concession approver |
| SOD-09 | Counter ≠ count variance approver; recount by a different user |
| SOD-10 | Weigh operator ≠ weight verifier |
| SOD-11 | One user cannot approve two stages of the same approval chain |
| SOD-12 | Finished-goods releaser ≠ the production supervisor who posted that output; sales order creator ≠ its approver |
| SOD-13 | Risky role combinations (e.g. APC + PAP, BUY + STK) are flagged at role assignment and in the access review. v1: report · v1.1: block |
| SOD-14 | Stock-adjustment approver must hold warehouse authority (WSUP / PLM / FINC per value band), never the QC role, and never the adjuster |
| SOD-15 | PV approver ≠ recorder of the payment against that PV |
| SOD-16 | Confirmer of a self-billed voucher ≠ poster of its GRN |
| SOD-17 | Credit-limit change requester ≠ its approver |

### 15.2 Preventive controls
- Approval matrices per document type (data-driven).
- Tolerances: receipt quantity, price, weight vs declared, tare vs history, rejected-exit weight.
- Dual control: bank changes, adjustments above threshold, manual weights, count variances, period reopen.
- Immutability of posted documents, ledgers, QC results, approval history and the audit log.
- Reversal-only corrections with a dependency guard.
- Period close and backdating windows; no future posting dates.
- QC hold enforcement: no issue, transfer, dispatch or payment for quarantined goods; fumigation holds with release dates.
- **No outbound exit without a posted document and weight within tolerance.**
- Credit limits and floor prices on sales orders.
- Sourcing rule: N quotations, or a justified sole source.
- Blind counts and forced recounts.
- Cooling-off on new bank accounts; bank-detail hash check at payment; payment method fixed at approval.
- Random forced re-tare; weighbridge calibration enforcement.
- Gap-free numbering of receipts, payments, weigh tickets, invoices and cheque books.

### 15.3 Detective controls (exception reports)
Overrides by user/rule · back-entries and lag · stored-tare usage · tare deviations · net vs declared outliers ·
purchase price variances · adjustments and write-offs by user · reversals · open process exceptions ·
late approvals · dormant supplier reactivations · bank-detail changes · payments to recently changed accounts ·
cheque-book gaps · approved-but-unpaid PVs · GRNI ageing and unresolved invoice variances · credit-limit changes and overrides · production loss outside the recipe band ·
audit-chain verification result · quarterly access-review export.

---

## 16. Key acceptance scenarios (end-to-end)

| ID | Scenario | Covers |
|---|---|---|
| AS-01 | **Wheat truck (expected arrival, gate on) accepted with a moisture deduction:**<br>1. gate → gross → sample → results → decision with deduction → unload → tare;<br>2. GRN prepared, STK confirms;<br>3. the supplier invoices at gross → an automatic quality debit note → match;<br>4. PV drafted → APC submits → PAP approves → payment recorded (transfer) → WHT certificate → journals exported.<br>Repeated in automatic mode, where the GRN posts at tare | INB, GATE, QC, PRC, AP |
| AS-02 | **Wheat truck rejected before unloading** (live infestation found by a rapid test): no GRN, exit weight ≈ entry gross, rejection note, retention sample kept. **Long-test variant:** an accepted lot on "hold pending long tests" receives a failing aflatoxin result days later and moves QUARANTINE → REJECTED | INB, GATE, QC |
| AS-03 | **Packaged additive received into quarantine:** partial accept; the rejected part is returned to the supplier with a debit | INB, INV, QC, PRC, AP |
| AS-04 | **Truck leaves without tare:** flagged GRN draft, PMGR approval, payment block until resolved | INB exception #6 |
| AS-05 | **Bank account change fraud attempt:** self-approval refused; payment before cooling-off refused; a hash mismatch after approval blocks payment; a method change after approval forces re-approval | PRC, AP, controls |
| AS-06 | **Inter-site transfer with a short receipt** → transit loss at dispatch cost | INV |
| AS-07 | **Counts:** a silo counted by dip measurement in cut-off mode while issues continue; a store counted blind with a variance above threshold → recount by another user → approval → generated adjustment | INV |
| AS-08 | **Period close** with an in-flight posting; reopen via approval | PLT, INV |
| AS-09 | **Outbound purchase return truck:** exit blocked until the return document is posted and the weight is within tolerance | GATE, PRC |
| AS-10 | **Network outage at the gate:** paper slips, back-entry, slip gap detection | GATE |
| AS-11 | **Site scoping:** a user scoped to site A cannot see or act on site B through the UI, API, exports, search, notifications or background-job outputs | PLT |
| AS-12 | **Approval delegation and escalation** with SoD exclusion | PLT |
| AS-13 | **The client's procure-to-pay walkthrough** (gate module off, one coherent dataset agreed with the client):<br>1. STK raises a PR: durum wheat 25,000 kg (0 available → 25,000 to purchase) and an additive 500 kg (120 available → 380 to purchase);<br>2. MGMT approves (bulk);<br>3. BUY converts it to a PO and issues it;<br>4. the truck arrives: gross 25,000 kg, stored tare 2,500 kg, 450 bags;<br>5. LAB creates the inspection lot (FR-QC-025), records results, calculates grade and score, and decides a partial accept with 200 kg held as rejected;<br>6. STK generates the GRN (wheat to Silo 01, net after bag tare, 200 kg held REJECTED; additive 380 kg counted into the raw-material store);<br>7. the stock balance reconciles (FR-INV-014);<br>8. APC raises one PV with both invoices, paying the wheat in full and the additive partly;<br>9. PAP approves with a comment;<br>10. TRS records a cheque payment with the cheque copy attached; WHT follows the client's rules; the cheque register and supplier balance update.<br>**Negative case from the draft:** invoices totalling more than the PO (175,000 against 150,000) put the excess invoice on hold | PLT, PRC, QC, INV, AP |
| AS-14 | **Milling run:**<br>1. a campaign work order issues 20,000 kg of wheat from Silo 01 (two lots, first-in-first-out) and records tempering water;<br>2. it outputs 15,000 kg flour and 4,600 kg bran; extraction 75.0%, loss within the recipe band;<br>3. settlement values the outputs (FR-MFG-014);<br>4. a trace runs from the supplier lot to the flour lot | MFG, INV, QC |
| AS-15 | **Order to cash:**<br>1. a sales order within credit → approval → delivery order released → expected outbound visit;<br>2. pick and load → outbound weighing → goods issue (COGS) → gate exit;<br>3. invoice with the fiscal reference → receipt allocated.<br>An order over the credit limit routes to CRC/SMGR | SAL, INV, GATE, AR |
| AS-16 | **Packing:** 200 × 50 kg bags packed from bulk flour; bags and labels backflushed; net-content check recorded; bag label printed | MFG, INV, QC |
| AS-17 | **Finished-goods hold and release:** a flour lot in QUARANTINE blocks delivery; QC releases it. Variant: regrade to a lower-grade item | MFG, QC, SAL |
| AS-18 | **Cash sale:** the truck cannot exit until paid and the fiscal receipt number is entered | SAL, GATE |
| AS-19 | **Customer return:** received through the gate into quarantine; QC decides restock or scrap; a credit note is issued against the original invoice | SAL, GATE, QC, INV |

---

## 17. Assumptions and items to validate with the client

1. **One company per installation** (ADR-0017). The client's "operation unit" is taken to be a site, pending confirmation.
2. **The client is a flour mill** ("Floor Manufacturing" read as "Flour"), pending confirmation.
3. **Development takes ≤ 6 months, then 2 months to go live;** the stakeholders choose the team path (ADR-0018).
4. **No infrastructure is assumed** (ADR-0019). Device-dependent features have fallbacks (NFR-14). Hosting targets are proposals until the client answers the infrastructure questions.
5. Bulk raw materials are bought at delivered prices (landed cost is Later).
6. **Currency:** one currency per document. The client's draft shows USD on PRs/POs and ETB on invoices and payments; how that works is a client question.
7. Weighbridges exist at sites that receive bulk material. Where they don't, receipts are by count or volume.
8. Supplier invoices arrive on paper or PDF and APC keys them. OCR is out of scope.
9. Payments are executed in banking systems outside MOS and recorded afterwards.
10. Gate tests (moisture, impurity, hectolitre weight) take under 30 minutes. Long tests (aflatoxin, microbiology) take days, which "hold pending long tests" handles.
11. Quality deduction schedules can be expressed with threshold/step/rate/cap rules and grade differentials. Exotic schedules would need a new rule type (code).
12. **The client's procure-to-pay draft is the first validation input** ([review](MOS-CLIENT-P2P-DRAFT-REVIEW.md)). Production, sales and gate processes are validated at client workshops before M4 (gate), M4 (production) and M5 (sales).
13. Tax rates (VAT, withholding, the no-TIN rate) and whether the company is a withholding agent are client inputs, seeded as data.

---

## Appendix F. Future: multi-tenant / SaaS (not in v1)

These items belonged to the v0.2 multi-tenant design. They are kept here, with their IDs, as the functional target for a future pooled SaaS. The technical path is ADR-0017's expansion path and TDD Appendix A.

| ID | Requirement (as in v0.2) | Acceptance criterion (as in v0.2) |
|---|---|---|
| FR-PLT-001 | The system isolates every tenant's data completely. No user, API key or background job can read or write another tenant's data | Automated tests with two tenants show zero cross-tenant rows via UI, API and workers. A database-level test with the tenant context unset returns zero rows from every tenant table |
| FR-PLT-005 | A tenant can run in dedicated mode (single tenant per installation) or shared mode, with no functional differences | The same build passes the same acceptance suite in both modes |
| FR-PLT-011 (part) | A first sign-in creates the user if their verified email domain matches a tenant auto-join rule | A user from a matching domain is created with the default role |
| NFR-03 (part) | 99.9% availability for shared SaaS | — |
| NFR-06 (part) | Per-tenant data export and deletion on offboarding | — |
| NFR-13 (part) | Runs as containers for shared SaaS | — |
| AS-11 (v0.2) | Two tenants: no data leakage through UI, API, exports, search, notifications or background jobs | — |
| Principle 5 (v0.2) | One codebase, two operating modes: shared multi-tenant SaaS or a dedicated single-company installation | — |
