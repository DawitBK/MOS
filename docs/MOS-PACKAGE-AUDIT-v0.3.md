# MOS: Planning package audit and re-baseline (v0.2 → v0.3)

| | |
|---|---|
| Document | Audit report for planning Phase 1 ("audit and re-baseline") |
| Date | 2026-09-27 |
| Audited | v0.2 of the FRD, TDD, implementation plan, master plan (`.md` + `.html`), client-draft review, ADR-0001…0016, README, CLAUDE.md, PROGRESS |
| Method | <br>1. Three parallel line-by-line reviews: FRD; TDD + ADRs; plan + master + review + HTML. <br>2. The client's PDF was re-checked page by page and all 21 embedded images inspected. <br>3. Library and version claims were verified on the web. <br>4. The author verified findings before fixing them. Key domain claims were re-checked against the client's screens: "Addis Flour Factory", 450 bags = 22,500 kg, the stock-screen double counts, the payment screen's totals and method, the "Supplier Portal" title. <br>5. Mechanical checks ran on IDs, links and columns. |
| Result | **135 findings** (some overlap between reviews): 24 High, 64 Medium, 46 Low, 1 Info. All resolved in v0.3, or explicitly deferred to a named later planning phase |
| Audience | Product owner (review), developers (traceability) |

---

## 1. The re-framing applied (owner direction, 2026-09-27)

| Change | Where recorded | Effect on the package |
|---|---|---|
| **Single tenant, single company in v1**, with expansion seams; SaaS later | [ADR-0017](adr/ADR-0017-single-tenant-single-company-v1.md) (supersedes ADR-0004) | <br>• tenancy requirements moved to FRD Appendix F, IDs kept <br>• the TDD tenancy design became Appendix A (expansion playbook) <br>• new guards: privilege boot probe and grant/trigger catalog test <br>• the company dimension is kept in the schema |
| **All six modules in ≤ 6 months of development + 2 months to go live**; team paths A/B/C | [ADR-0018](adr/ADR-0018-lean-v1-six-months.md) | <br>• MVP/P2/P3 replaced by v1 (month) / v1.1 / Later <br>• production and sales became v1 <br>• leaner approvals, audit and lock design <br>• edge agent deferred |
| **Infrastructure not assumed** | [ADR-0019](adr/ADR-0019-infrastructure-not-assumed.md) | <br>• deployment options became proposals <br>• queue and file storage behind ports <br>• NFR targets became proposed defaults <br>• device-dependent features got fallbacks (NFR-14) |
| **Documents stay local; phase-gated work** | `MOS/CLAUDE.md`, root `CLAUDE.md` | The HTML view is a local file; nothing is republished |

---

## 2. Most important findings

1. **The swimlane is not the client's** (A-01).
   - The client's PDF and DOCX contain no swimlane: their 21 images are the sign-in screen and 16 other screens.
   - The swimlane is byte-identical to the owner's ChatGPT drawing.
   - Moved to `reference-input/owner-brainstorm/`; the attribution is corrected everywhere.
2. **The client is a flour mill** (A-02, F-01). "Addis Flour Factory", durum wheat in 50 kg bags, bran in stock.
   - The "Flooring" reference pack and the tile-style examples came from reading "Floor Manufacturing" literally.
3. **Three design defects would have failed in production** (T-01, T-02, T-06):
   - the posted-record lock would block matching and payment;
   - QC result correction needed a forbidden UPDATE;
   - streaming routes held database transactions open for hours.
4. **Foundation was under-weighted and payables unweighted** in the first 6-month draft (P-08). The weights were revised, and the lean v1 needs about 2.2 effective FTE: path A just fits, B and C need depth cuts.
5. **Production and sales had no design, no acceptance criteria and no scenarios** (T-04, F-08, F-38). They now have full v1 requirements (with flour-mill specifics), a TDD design and scenarios AS-14…19.
6. **Several money rules contradicted each other or were missing:**
   - invoice-over-payable (F-04);
   - gross vs net WHT (F-05);
   - a non-existent "invoice approver" in the SoD rules (F-03);
   - output valuation (F-06);
   - legal sales invoicing (F-07).
7. **The client draft has about 40 defects**, not 20. The v0.3 review adds 20 missed items, including:
   - the USD vs ETB currency mix;
   - a payment-method change after approval;
   - screens that don't form one scenario;
   - over-invoicing;
   - kg + pcs totals.

---

## 3. Findings

**Status values:**
- **Fixed:** resolved in v0.3 documents.
- **Retired:** it concerned the v0.2 schedule, which is replaced.
- **Phase n:** deferred to that planning phase, where it belongs.
- **Question:** needs a client or owner answer, and is carried to the Phase 5 register.

### 3.1 Author's own findings (A)
| ID | Sev | Where | Finding | Resolution | Status |
|---|---|---|---|---|---|
| A-01 | High | Review header and §1, master plan, HTML Client tab, README, `client-input/` | The swimlane was attributed to the client, but it is the owner's ChatGPT image (same SHA-256); the client files contain no swimlane | File moved to `reference-input/owner-brainstorm/` with a README; every reference corrected; review §1 re-sourced to the narrative | Fixed |
| A-02 | High | FRD §1.3, glossary, examples; plan; master | "Floor" read literally (flooring pack, tile examples) | Flour-milling pack only in v1; examples changed; client question Q0 | Fixed + Question |
| A-03 | Med | ADR-0018 (first draft), plan | The owner's 6-month constraint made the v0.2 phasing (10.4-month MVP, 22–24 months in total) obsolete | ADR-0018; plan rewritten as a 6+2-month roadmap | Fixed |
| A-04 | Med | TDD §14.1 vs review §8 | `{FY}` and `{YYYY}` used inconsistently; the review's note said `{YYYY}` meant the fiscal year | Token set defined once (FR-PLT-040): `{FY}`, `{YYYY}`, `{YYYYMMDD}`, `{SITE}`, `{SEQ:n}` | Fixed |
| A-05 | Low | Review header | "17 screen mock-ups" vs 21 images | Stated as 17 screens (21 images; the supplier wizard is 5 images) | Fixed |
| A-06 | Low | Master plan §12, HTML | The HTML was described as published | Local-only everywhere; HTML footer and pills updated | Fixed |
| A-07 | Med | FRD, plan | No team-path concept (the owner wants A/B/C options) | ADR-0018 and plan §5 with depth lists per path | Fixed |
| A-08 | Med | FRD NFR, TDD §18 | Hosting targets stated as facts | Proposed defaults pending ADR-0019 answers | Fixed |
| A-09 | Low | Memory / conventions | Owner preferences (local-only, phase-gated) not recorded | Saved as feedback memories and in `MOS/CLAUDE.md` | Fixed |

### 3.2 FRD findings (F)
| ID | Sev | Where (v0.2) | Finding | Resolution (v0.3) | Status |
|---|---|---|---|---|---|
| F-01 | High | §1.3; MD-002/004; MFG-007; SAL-005 | Flooring pack and tile examples come from the "Floor" typo | Flour pack; flour examples; shade constraint dropped | Fixed |
| F-02 | High | QC-004/006; PRC-013; AS-13 | No inspection-lot path with the gate off (the client's own flow) | New FR-QC-025; flow C (§9.3); QC-004 acceptance criterion for gate-off | Fixed |
| F-03 | High | SOD-03/04/05; BR-AP-05 | SoD rules reference an undefined "invoice approver" | SoD restated around the PV approver and the match-exception approver; BR-AP-05 rewritten | Fixed |
| F-04 | High | AP-005 vs BR-AP-01 vs §9.4 #24 | Three conflicting rules for invoice > payable quantity | One rule: auto debit note if the excess equals the deduction, otherwise hold; BR-AP-01 reworded | Fixed |
| F-05 | High | AP-007/008 | No gross/WHT/net on the PV; the approver approves gross while the cheque is net | New FR-AP-013; WHT agent status and rates are client questions | Fixed + Question |
| F-06 | High | MFG-010 | No v1 rule values production output | New FR-MFG-014 material-cost settlement; TDD §14.3 | Fixed |
| F-07 | High | SAL-008/011 | Legal sales invoicing (fiscal device) undefined | New FR-SAL-012 (fiscal reference); SAL-011 "v1 if legally required"; question Q18 | Fixed + Question |
| F-08 | High | §16 | No production or sales acceptance scenarios | AS-14…AS-19 added | Fixed |
| F-09 | High | Whole document | Multi-tenant framing contradicts ADR-0017 | Re-framed; Appendix F | Fixed |
| F-10 | Med | Role table | "The swimlane's 'Management'" | "The client narrative's 'Management'" | Fixed |
| F-11 | Med | INB-002; AS-01; INB-005 | "GRN generated/auto-posted" contradicts ADR-0016's confirm default | Reworded: prepared → confirmed; AS-01 covers both modes | Fixed |
| F-12 | Med | QC-013/022 | Combination of multiple deductions undefined | Order, caps and a "no stacking" setting defined; acceptance criterion with two deductions | Fixed |
| F-13 | Med | PRC-013; BR-QC-03 | GRN valuation with payable ≠ physical undefined | New FR-INV-022 | Fixed |
| F-14 | Med | AP-002 | Match against the raw PO price creates false price exceptions | Expected price = quality-adjusted GRN price | Fixed |
| F-15 | Med | AP-003 vs BR-AP-01 | Quantity tolerance contradicts a hard cap | Tolerance semantics defined; BR-AP-01 includes the tolerance | Fixed |
| F-16 | Med | BR-AP-04 | Bank account required even for cheque or cash payments | Rule per method (transfer / cheque / cash) | Fixed |
| F-17 | Med | §9.4 #34 | WHT correction path ignores WHT timing | Path depends on timing | Fixed |
| F-18 | Med | AP-012 | Cheque register gaps (clearing, books, stale, voids after posting) | New FR-AP-016 | Fixed |
| F-19 | Med | PLT-043 | Future-date ban blocks post-dated cheques | The ban applies to posting dates only | Fixed |
| F-20 | Med | PLT-041 vs review §8 | Gap-free list inconsistent; PV irreversible point undefined | List aligned (incl. weigh tickets and sales documents); PV numbered at submission, number kept on cancel | Fixed |
| F-21 | Med | INV-014 | Acceptance criterion untestable (the draft's example doesn't reconcile) | Corrected fixture in the acceptance criterion; damage is a status move | Fixed |
| F-22 | Med | INV-009 | Freezing impractical for silos; system quantity basis wrong | FREEZE or CUT_OFF modes; on-hand across statuses; silo measurement (FR-INV-020) | Fixed |
| F-23 | Med | MFG-003 | A strict mass-balance check rejects real milling | Gain/loss band; tempering water (FR-MFG-015) | Fixed |
| F-24 | Med | GATE-010 | Deferred verification: does the GRN post or block? | The GRN posts; matching and payment are blocked until verified | Fixed |
| F-25 | Med | GATE-016 vs 023 | An outage stops all dispatch without saying so | Paper-mode exit rule stated as a deliberate stop | Fixed |
| F-26 | Med | Several | Status vocabulary inconsistent; missing lifecycles | Canonical vocabulary (FRD §1.5); diagrams in Phase 2 | Fixed; diagrams drawn in Phase 2 ([process model §6](MOS-PROCESS-MODEL.md#6-l3-lifecycles)) |
| F-27 | Med | PLT-002/003; INV-010; AP-010 | Multi-company acceptance criteria contradict single company | One company in the acceptance criteria; company-level valuation in v1 | Fixed |
| F-28 | Med | AS-13 | The draft's screens don't form one scenario | Coherent AS-13 fixture defined; confirm with the client | Fixed + Question |
| F-29 | Med | §15.1 | The PV approver could record the payment | SOD-15 | Fixed |
| F-30 | Med | PRC-004 | 3 distinct people for bank changes may not exist; initial account unclear | 1 independent approver + call-back, 2nd approver as a setting; initial account verified at supplier approval | Fixed |
| F-31 | Med | PLT-041 | Weigh tickets not gap-free | FR-GATE-030 | Fixed |
| F-32 | Med | PRC-001/020 | "Supplier Portal" title suggests self-registration | Noted in PRC-001; question Q12 | Question |
| F-33 | Med | PRC-013; QC-024 | Bagged wheat: no bag tare | New FR-PRC-021 | Fixed |
| F-34 | Med | AP-006 | Self-billing lacks tax rules and SoD | New FR-AP-018, SOD-16 | Fixed |
| F-35 | Med | AP-010 | Journal sources omit production and sales; GRNI unnamed | Sources extended; GRNI in the glossary; TDD §13.4 | Fixed |
| F-36 | Med | Many (AV scan, SMTP, camera, TV, printers, tablets, uptime, containers) | Infrastructure stated as requirements | Settings with fallbacks (NFR-14); proposed defaults; questions to Phase 5 | Fixed + Phase 5 |
| F-37 | Med | QC-022/023 | Scoring formula undefined; acceptance criterion depends on a missing table | Formula defined; the "92.4" example pending the client's weights | Fixed + Question |
| F-38 | Med | 28 rows | Acceptance criterion "—" | Every non-appendix requirement now has an acceptance criterion (checked by script) | Fixed |
| F-39 | Low | Glossary | DAMAGED missing | Added | Fixed |
| F-40 | Low | GATE-011 | False direction anomaly for rejected trucks | Excluded | Fixed |
| F-41 | Low | §9.4 #18 | "X hours" undefined | `photo_pending_max_hours`, default 4 | Fixed |
| F-42 | Low | INV-019; MD-003/010 | Stock-level semantics and precedence undefined | Defined with validation and precedence | Fixed |
| F-43 | Low | PRC-015 | Acceptance criterion unconditional | Made conditional | Fixed |
| F-44 | Low | AP-008 | Cash and mobile payments have no account | FR-AP-015 | Fixed |
| F-45 | Low | Several | Actor names inconsistent | Role codes used; TRS/CSH clarified | Fixed |
| F-46 | Low | QC-012 | Inspector vs QAM decision rights unclear | LAB confirms the suggested outcome; QAM overrides, approves concessions and partial acceptance | Fixed |
| F-47 | Low | Review "As-is" | Delete icons on approvals; "Close Warehouse" | FR-PLT-053/054 | Fixed |
| F-48 | Low | Client stock screen | kg + pcs summed | FR-RPT-005 | Fixed |
| F-49 | Low | Review §5 | Missed defects (paying account; PR dates) | Review #21, #26; FR-PLT-055 | Fixed |
| F-50 | Low | §17 | Assumptions still v0.2 | Rewritten | Fixed |
| F-51 | Low | Everywhere | MVP/P2/P3 vocabulary | Re-tagged (script-checked) | Fixed |
| F-52 | Low | GATE-006 | Step sequences per visit class undefined | Tabulated in the process model | Fixed in Phase 2 ([process model §7](MOS-PROCESS-MODEL.md#7-gate-step-sequences-per-visit-class)) |
| F-53 | Low | INV-002 | "Only AVAILABLE can be issued" conflicts with scrap/return | Narrowed | Fixed |
| F-54 | Low | INV-011 vs MFG-006 | Backflush shortage vs no negative stock | Pending-issue mechanism | Fixed |
| F-55 | Low | PRC-007 | Available stock ignores reservations | Available-to-use | Fixed |
| F-56 | Low | AS-02 | Aflatoxin at the gate conflicts with long-test timing | Rapid test at the gate + a long-test variant | Fixed |
| F-57 | Low | Several acceptance criteria; NFRs | Weak acceptance criteria; NFRs untestable | NFR verification column; key acceptance criteria tightened; the rest tightened as stories are written | Partly fixed; Phase 4 |

### 3.3 TDD and ADR findings (T)
| ID | Sev | Where (v0.2) | Finding | Resolution (v0.3 TDD) | Status |
|---|---|---|---|---|---|
| T-01 | High | §7.4 vs §9, §12 | The immutability trigger blocks matching, payment and export | Side tables (`grn_line_settlement`, `invoice_open_item`, `delivery_line_billing`, `export_batch_item`) (§8.4) | Fixed |
| T-02 | High | §10 vs §7.4 | QC correction needs an UPDATE the grants revoke | `qc.result_invalidation` insert-only (§11) | Fixed |
| T-03 | High | §2 | Node 22 end of life (2027-04-30) falls at go-live | Node 24 LTS recommended; Phase 6 confirms | Fixed + Phase 6 |
| T-04 | High | §1, §12 | Production, sales and AR have no design | §14 production, §15 sales and receivables; AR invariants | Fixed |
| T-05 | Med | §2; §13.3 | NestJS 12 and BullMQ 6 released; repeatable jobs removed | Versions recorded; Job Schedulers / pg-boss cron; Phase 6 decides | Fixed + Phase 6 |
| T-06 | High | §6.2 | SSE and download routes inside long transactions | `@NoUnitOfWork`; short read-only GETs (§7.2) | Fixed |
| T-07 | Med | §14.1 | Separate numbering transaction can starve the pool | One same-transaction mechanism (§17.1); ADR-0011 amended | Fixed |
| T-08 | Med | §14.2 | Outbox semantics under-specified | Lease, `jobId`, DONE on consumer success, AOF, source re-check (§17.2) | Fixed |
| T-09 | Med | §8.3/8.6/12.4 | Rounding inconsistent between ledger and journals | One helper; 4 dp internally; export rounding lines; company rounding mode (§9.6) | Fixed |
| T-10 | Med | §7.6, §8.3 | Moving-average residual and negative-value cases | Residual clamp in the same apply; fungible-reversal limitation stated (§8.6, §9.3) | Fixed |
| T-11 | Med | §8.5 | In-transit valued at the destination average | `AT_REFERENCE` to the dispatch entry (§9.5) | Fixed |
| T-12 | Med | §6.3, §7.5 | Lock hierarchy inconsistent and incomplete | Six ranks with table order; explicit R3 step (§7.3, §8.5) | Fixed |
| T-13 | Med | §6.3; ADR-0008 | Deadlock risks; "structurally impossible" overstated | `FOR NO KEY UPDATE`; header before lines; wording corrected | Fixed |
| T-14 | Med | §4.6 | Capacity BLOCK check races | BLOCK-mode units locked `FOR NO KEY UPDATE` (§5) | Fixed |
| T-15 | Med | §6.4 | Idempotency keys global | PK (principal, key) + route; TTL; 2xx only (§7.4) | Fixed |
| T-16 | Med | §11.2 | PV uniqueness assumes one invoice per PV | One automatic PV per GRN; open-item CHECK (§12.3) | Fixed |
| T-17 | Med | §11.2 | Settlement triggers contradict ADR-0016 | Per-mode settlement (§12.3) | Fixed |
| T-18 | Med | §1.3; ADR-0003 | Dependency rule broken (cycles) | Tiers with ports (§1.3); ADR-0003 amended | Fixed |
| T-19 | Med | §7.3; §11.2 | State machines drift from the FRD; exit-without-tare and abandoned missing | Stored vs derived statuses (§8.3); visit table redrawn on confirm default with #6/#7 and flow C (§12.2) | Fixed |
| T-20 | Low | §5 | §5.5 missing | Renumbered (sections 1–22 contiguous) | Fixed |
| T-21 | Low | §15.1 | Cited AS-01…12 only | AS-01…19 | Fixed |
| T-22 | Low | §4.6 | Org/storage model inside the tenancy section | Own section (§5) | Fixed |
| T-23 | Med | §5.1 | `email_verified` blocks Entra ID | Per-IdP email-verification config (§6.2) | Fixed |
| T-24 | Med | §5.3 | CSP in the wrong layer | Nonce in Next `proxy.ts`; API `default-src 'none'` (§6.5) | Fixed |
| T-25 | Low | §5.6 | k-anonymity wording wrong for an offline list | Bundled top-N list, v1.1 (§6.1) | Fixed |
| T-26 | Med | §6.2 | Retry policy conflicts with the p95 target | Retry 40P01 only; 55P03 → 409; savepoint catch scope (§7.2) | Fixed |
| T-27 | Low | ADR-0006; §11.2 | "Exactly once" overstated | Database effects only; notification dedupe (§17.2) | Fixed |
| T-28 | Low | §6.1 | Pipes vs interceptors order | Documented (§7.1) | Fixed |
| T-29 | Low | §14.1; ADR-0011 | Cancelled numbered documents; per-site collisions | Number kept; `{SITE}` required | Fixed |
| T-30 | Low | §8.3/8.6; ADR-0008 | `valuation_layer` redundant | Dropped | Fixed |
| T-31 | Low | §8.1, §12.4 | Per-row constraint triggers are quadratic | Poster check + flagged backstop (§9.1, §13.4) | Fixed |
| T-32 | Low | §7.5, §12.3 | Master-data checks at payment unlocked | R4 `FOR SHARE` on supplier and bank rows | Fixed |
| T-33 | Low | §9 | CHECK "unless approval exists" is inexpressible | `+ approved_excess_qty`; ACCEPT_UNPAID removed | Fixed |
| T-34 | Low | FR-PLT-032 | Bulk approve undesigned | Savepoint per item (§16.2) | Fixed |
| T-35 | Low | §14.2 | SSE through BullMQ and Redis pub/sub unnecessary | In-process emitter / LISTEN-NOTIFY (§17.7) | Fixed |
| T-36 | Low | §19 | "Short idle timeout" vs 8 h | Wording aligned | Fixed |
| T-37 | Low | §18.3; ADR-0013 | Presigned URLs assume an S3 endpoint | Downloads streamed through the API by default; ADR-0019 | Fixed |
| T-38 | Info | ADR-0005 | "MOS-native passwords rejected" not annotated | Annotated "reversed by ADR-0015" | Fixed |

### 3.4 Plan, master, review and HTML findings (P)
| ID | Sev | Where (v0.2) | Finding | Resolution (v0.3) | Status |
|---|---|---|---|---|---|
| P-01 | High | Review, master, HTML, README | Swimlane attributed to the client; fail branch only in the ChatGPT image | See A-01; the fail branch flagged as a question | Fixed |
| P-02 | High | HTML; review header | Broken image and file reference after the move | HTML rewritten without the image; review header corrected | Fixed |
| P-03 | High | All plan documents | v0.2 timeline figures conflict with 6+2 months | Replaced everywhere; v0.2 figures appear only in history rows | Fixed |
| P-04 | High | Master, HTML, plan, README, CLAUDE | Tenancy/SaaS framing stale | Re-framed per ADR-0017 | Fixed |
| P-05 | High | Plan §3 | FR-PLT-006 and 044 (and 045, 048, 049, 051) had no owning work package | Owners assigned (plan §4: WP-03, WP-04) | Fixed |
| P-06 | High | Review #9; plan acceptance criterion; FRD INV-014 | Stock example understated; acceptance criterion unmeetable | Review #9 extended; FRD fixture | Fixed |
| P-07 | High | Plan; FRD §11–12 | Gate, production and sales have no client input or acceptance criteria | Workshops before M4/M5 (plan §7); acceptance criteria and scenarios written | Fixed + Question |
| P-08 | High | ADR-0018 draft | Foundation under-weighted; payables not weighted | Weights revised: 1.5 / 0.75 / 0.75 / 0.5 / 0.5 / 1.0 / 0.75 / 0.25 | Fixed |
| P-09 | Med | v0.2 Gantt | Weekly over-allocation (peak 2.83 FTE) | Retired; Phase 4 levels the new plan per path | Retired / Phase 4 |
| P-10 | Med | v0.2 plan | Three dependency violations | Retired; the new WP table respects the order | Retired |
| P-11 | Med | v0.2 plan | AS-05 and AS-03 owned by the wrong work packages | AS-05 → WP-11; AS-03 split WP-09/WP-11 | Fixed |
| P-12 | Med | Master §3 | Promised scope with no estimate | Listed as v1.1 / Later with no date | Fixed |
| P-13 | Med | Master, HTML, plan, FRD | Flooring pack from the "Floor" misreading | See F-01 | Fixed |
| P-14 | Med | Review | USD PO vs ETB payment conflicts with §9.4 #33 | Review #25; FRD §9.5 #33; question Q11 | Fixed + Question |
| P-15 | Med | Review | Payment method change; paying account shows the supplier's | Review #26; FR-AP-007/008 | Fixed |
| P-16 | Med | FRD AS-13; plan | The draft does not chain as one scenario | See F-28 | Fixed + Question |
| P-17 | Med | HTML head | No doctype, charset or viewport | Added | Fixed |
| P-18 | Med | Master, HTML, plan | Infrastructure assumed | Proposals only (ADR-0019) | Fixed |
| P-19 | Med | Review §6 | Q6/Q7/Q8 partly answered by the PDF | Re-phrased as confirmations | Fixed |
| P-20 | Med | Review §6 | Missing questions (currency, fiscal, flour, production, sales, gate, Document No., partial accept, hosting) | Q0, Q11–Q19 added | Fixed |
| P-21 | Med | Master; HTML | SoD overstated vs the FRD | FRD SoD extended (SOD-03/04/15/16), wording aligned | Fixed |
| P-22 | Med | README, CLAUDE, master | Residual issues in the rewritten files (missing audit doc, "published" claim, dropped `mos_app` test rule) | Audit written; claims corrected; rule restored | Fixed |
| P-23 | Low | v0.2 plan | Rounding of 45.6 weeks stated as 45 | Retired (noted in review §7) | Retired |
| P-24 | Low | v0.2 plan | "Was" annotations untraceable | Retired | Retired |
| P-25 | Low | Review; plan | Storage-unit type lists disagree | The FRD list is used everywhere | Fixed |
| P-26 | Low | Plan; HTML | "FIFO" deferred while commingled FIFO is built | "FIFO valuation option" wording | Fixed |
| P-27 | Low | Plan | Undefined alias "WP-40 (WP-D)" | Removed | Fixed |
| P-28 | Low | HTML; FRD; plan | "Every requirement has an acceptance criterion" was false | Now true (script-checked) | Fixed |
| P-29 | Low | PROGRESS | Client list mismatched review §6 | PROGRESS rewritten | Fixed |
| P-30 | Low | HTML | Grouped issues table incomplete | Rewritten; the full list lives in review §5 | Fixed |
| P-31 | Low | v0.2 plan | M-F set at the end of hardening | Retired | Retired |

---

## 4. What changed per document

| Document | v0.3 change |
|---|---|
| [MOS-FRD.md](MOS-FRD.md) | Rewritten:<br>• 225 requirements (198 kept, 27 new), every one with an acceptance criterion; tags v1 (M1–M6) / v1.1 / Later / Future<br>• status vocabulary §1.5; flow C<br>• NFRs with verification; SoD-15/16<br>• AS-14…19; Appendix F |
| [MOS-TDD.md](MOS-TDD.md) | Rewritten:<br>• installation model and guards<br>• defects T-01/02/06/07/15/16/18 fixed<br>• six lock ranks; approval matrix; sealed audit log<br>• production (§14) and sales/receivables (§15)<br>• queue options for Phase 6; deployment proposals<br>• Appendix A expansion playbook; Appendix B deferred edge agent |
| [MOS-IMPLEMENTATION-PLAN.md](MOS-IMPLEMENTATION-PLAN.md) | Rewritten:<br>• 6+2-month roadmap with revised weights<br>• work packages with owners for every cross-cutting requirement<br>• team paths A/B/C with depth lists<br>• go-live plan; client inputs by month; risks R17–R21 |
| [MOS-MASTER-PLAN.md](MOS-MASTER-PLAN.md) + [.html](MOS-MASTER-PLAN.html) | Rewritten for v0.3; the HTML is local-only with a new roadmap chart |
| [MOS-CLIENT-P2P-DRAFT-REVIEW.md](MOS-CLIENT-P2P-DRAFT-REVIEW.md) | Attribution corrected; 20 defects added (40 in total); questions re-worked; v0.3 timeline column |
| [adr/](adr/) | New 0017, 0018, 0019; 0004 superseded; 0001, 0003, 0005, 0006, 0008, 0010, 0011, 0013 amended; wording fixes in 0002, 0009, 0012, 0015, 0016; README updated |
| `reference-input/owner-brainstorm/` | New: the ChatGPT PDF, DOCX, ERP picture and swimlane, with a README |
| `README.md`, `CLAUDE.md`, `PROGRESS.md` | v0.3 framing; local-only and phase-gating rules; progress tracker for the phase programme |
| Root `CLAUDE.md` | MOS paragraph: v0.3 framing, local-only rule |

---

## 5. Verification (run 2026-09-27)

| Check | Result |
|---|---|
| FR IDs unique; all 198 v0.2 IDs still present | Pass: 225 unique; 0 missing |
| Every FR row has a valid tag and an acceptance criterion | Pass: 199 v1, 11 v1.1, 13 Later, 2 Future; 0 without an acceptance criterion |
| Every FR ID cited by the TDD, plan and ADRs exists | Pass |
| Table rows well-formed (FRD) | Pass |
| TDD sections contiguous (1–22) | Pass |
| Tenancy/RLS/SaaS terms only in history rows, expansion sections, Appendix A/F and superseded ADR-0004 | Pass (see §6) |
| Old v0.2 figures (59.5 dev-weeks, ≈45 weeks, 22–24 months) only in history/comparison rows | Pass (see §6) |
| Relative links resolve | Pass (see §6) |
| No code files in `MOS/` | Pass |
| Git: `MOS/` untracked, nothing committed; root shows only `.gitignore` and `CLAUDE.md` modified | Pass (see §6) |

## 6. Verification detail

- **Links:** every relative link in the 30 Markdown files resolves (checked by script). The one Phase 2 forward reference is plain text, not a link.
- **v0.2 figures** (10.4 months, 59.5 dev-weeks, ≈ 45 weeks, 22–24 months) remain only in these places:
  - revision-history rows (implementation plan);
  - the v0.1/v0.2/v0.3 comparison table in review §7;
  - ADR-0018's context and alternatives;
  - this audit;
  - the PROGRESS session log.
- **Tenancy wording** remains only in these places:
  - statements of its absence (`MOS/CLAUDE.md`, TDD §4.1) and the expansion seams (TDD §4.4);
  - TDD Appendix A and FRD Appendix F;
  - superseded ADR-0004 and the historical bodies of ADR-0002/0005/0006 (each carries an amendment line);
  - the Later lists.
- **HTML view:**
  - it has a doctype, a UTF-8 charset and a viewport;
  - its inline script passes `node --check`;
  - its tags balance (55 div, 12 table and 3 svg pairs);
  - it has 6 tab panels and a print stylesheet;
  - it references no local images, so it cannot break when files move.
- **Files in `MOS/`:** 30 `.md`, 1 `.html`, 2 `.pdf`, 2 `.docx`, 2 `.png`. There are no code or DDL files.
- **Git:** `MOS/` is untracked (no commits). The root repo shows only ` M .gitignore` and ` M CLAUDE.md`. Nothing was committed.
- **Publishing:** no Artifact was published or republished in Phase 1. The v0.2 artifact on claude.ai is unchanged and now stale; it is deleted only if the owner asks.
