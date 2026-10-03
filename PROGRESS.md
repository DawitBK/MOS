# MOS: progress tracker

**Overall status (2026-10-03):** planning only; **implementation not started** (it waits for the owner's go-ahead and the client's choice of team path). **Everything now fits in 26 weeks** ([ADR-0025](docs/6-decisions/adr/ADR-0025-everything-in-six-months.md)): build W1–W20 with rolling UAT, test server by W4, production server by W16, system test and final UAT W21–W22, training W23, **go-live W24**, hypercare W25–W26. Speed needed: **path A+ (3 developers + QA) ×1.33**, path A ×1.89, path B ×2.22; path C does not fit. The client's v2 documents are reviewed and their effort counted (core 2,439 h). Discussed with the client on 3 October ([discussion pack](docs/8-meetings/2026-10-03-client-documents/DISCUSSION-PACK.md)). **Price: fixed at ETB 986,615** within the owner's ETB 1,000,000 cap ([pricing and timeline](docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.md); register A-43). Open items: the [decision register](docs/6-decisions/MOS-DECISION-REGISTER.md) (owner: O-34 team, O-27/O-28 stack). Start at [index.html](index.html), [README.md](README.md) or the [reading guide](READING-GUIDE.md).

## v0.3 planning programme

| # | Phase | Owner's points covered | Outputs | Status |
|---|---|---|---|---|
| 1 | **Audit and re-baseline** | 1 (re-check everything), 6 (single tenant/company, infrastructure as questions), the 6-month frame | [Audit](docs/7-reviews/MOS-PACKAGE-AUDIT-v0.3.md); FRD, TDD, implementation plan, master plan (`.md` + `.html`), client-draft review at v0.3; ADR-0017/18/19 and amendments; `9-inputs/owner-brainstorm/` | **Done** (owner go-ahead for Phase 2, 2026-09-27) |
| 2 | End-to-end process model | 3 (ChatGPT outputs → better flows and supporting doc) | [Brainstorm review](docs/7-reviews/MOS-BRAINSTORM-OUTPUT-REVIEW.md); [process model](docs/2-requirements/MOS-PROCESS-MODEL.md) + local `.html` (value chain, whole-ERP flow with 33 hand-offs, 21 swimlanes + 2 sub-processes with 199 steps, 27 lifecycles, gate step sequences, document chain, 36 events + 11 jobs, 64 exceptions, RACI, SoD, controls, KPIs, traceability); FRD/TDD/plan 0.3.1 | **Done** (owner go-ahead for Phase 3, 2026-09-27) |
| 3 | Database design | 4 | [Database design](docs/3-design/MOS-DATABASE-DESIGN.md) + local `.html` (230 tables in 9 schemas, 3,304 columns, 1,144 foreign keys, 35 entity diagrams; conventions, deep dives with worked rows, transaction write-sets, invariants and reconciliation, operations, ownership matrix); TDD and FRD 0.3.2, process model 0.2 | **Done** (owner go-ahead for Phases 4–6, 2026-09-28) |
| 4 | Estimate, roadmap, Module 1 plan, pricing | 2 | [Implementation plan v0.4](docs/4-plan/MOS-IMPLEMENTATION-PLAN.md) (187-feature estimate, capacity per path, week-level roadmap, go-live hours); [procurement module plan](docs/4-plan/MOS-PROCUREMENT-MODULE-PLAN.md) + `.html` (48 stories, UAT script, migration, training, go-live); [commercial proposal](docs/5-for-the-client/MOS-COMMERCIAL-PROPOSAL.md) + `.html` | **Done** (revised after the Phase 6 decisions) |
| 5 | Decision register and stakeholder questionnaire | 5a, 6 | [Decision register](docs/6-decisions/MOS-DECISION-REGISTER.md) + `.html` (41 decided, 10 owner items open, 35 client business and 17 infrastructure questions; 54 earlier questions traced once each); [stakeholder questionnaire](docs/5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.md) + `.html` | **Done** |
| 6 | Architecture grilling and lock-in | 5b | Three question rounds with the owner; [ADR-0020](docs/6-decisions/adr/ADR-0020-six-modules-one-release-26-weeks.md) (delivery), [ADR-0021](docs/6-decisions/adr/ADR-0021-stack-lock-in.md) (stack), [ADR-0022](docs/6-decisions/adr/ADR-0022-hosting-and-operations-baseline.md) (hosting and operations); ADR-0001/0006/0012/0018/0019 amended; TDD 0.3.3, FRD 0.3.3, database design 0.1.1; register updated | **Done** |

**Rules for every phase:**
- Documents only; no code, DDL or scaffolding.
- Everything stays local in `MOS/`; nothing is published to claude.ai.
- Nothing is committed unless the owner asks.

### Inputs recorded for Phase 4 (pricing)
- ETB client quote at **US$5 per dev-hour**. The lead developer works **7 days × 8 h**; other roles are parameterised.
- Present **team paths A (2 devs + AI + QA), B (2 devs + AI), C (1 dev with heavy AI)**.
- Price separately **development, QA/testing/UAT, production deployment and hosting, maintenance/support**.
- The window is ≤ 6 months of development + 2 months for the other work.
- **Reference view from the owner** (2026-09-27, "not to be suggestive"):
  - estimate = number of developers × hours × hourly rate;
  - a contract can add expenses such as travel to site and hours spent on non-development work;
  - development work should include testing and infrastructure setup for the production release.
- The ETB/USD rate is looked up at Phase 4 time, with its date and source recorded.

## Implementation work packages (six-month plan; details in the [implementation plan](docs/4-plan/MOS-IMPLEMENTATION-PLAN.md) §6–§7 and the [sprint plan](docs/4-plan/MOS-SPRINT-PLAN.md))

Every package's core is in the release; standard and full features are pull-ins. Path B uses path A's weeks.

| WP | Title | Weeks (path A+) | Weeks (path A) | Status |
|---|---|---|---|---|
| WP-00 | Repository and tooling bootstrap | W1–W2 | W1 | Not started |
| WP-01 | Platform core | W1–W4 | W1–W3 | Not started |
| WP-04 | Web shell and shared services | W1–W4 | W1–W3 | Not started |
| WP-02 | Identity and access | W2–W4 | W3–W4 | Not started |
| WP-03 | Organisation and master data | W4–W8 | W3–W6 | Not started |
| WP-07 | Approvals and SoD | W4–W5 | W4–W5 | Not started |
| WP-05 | Inventory ledger engine | W6–W9 | W5–W7 | Not started |
| WP-08 | Procurement | W8–W13 | W6–W10 | Not started |
| WP-11 | Payables and supplier payment | W10–W15 | W8–W12 | Not started |
| WP-06 / WP-12 | Inventory documents, counts, close | W7–W11 | W10–W12 | Not started |
| WP-09 | Lab and quality | W11–W14 | W12–W14 | Not started |
| WP-10 | Gate and the automated inbound loop | W13–W17 | W13–W17 | Not started |
| WP-14 | Production | W14–W18 | W14–W17 | Not started |
| WP-15 | Sales and receivables | W15–W20 | W17–W19 | Not started |
| WP-13 | Hardening and feature freeze | W20 | W20 | Not started |
| WP-16 | Go live (servers, rolling UAT, system test, training, cutover, hypercare) | W3–W26; go-live W24 | W3–W26; go-live W24 | Not started |

Velocity checkpoints at **W4** and **W8** (ADR-0025).

## Open questions
All open items are in the [decision register](docs/6-decisions/MOS-DECISION-REGISTER.md) 0.2:
- **Part B:** 18 owner items, among them O-34 (which team to plan with), O-27/O-28 (stack, before S1), O-26 (who pays for capacity added after a checkpoint). The pricing basis (O-35) is decided: a fixed price (A-43).
- **Parts C and D:** 49 client business and 18 infrastructure questions, packaged for the client in the [stakeholder questionnaire](docs/5-for-the-client/MOS-STAKEHOLDER-QUESTIONNAIRE.md) 0.2. The most urgent: the four sizing questions (Q-01–Q-04 of the 3 October pack) and the infrastructure questions for the test server, all **before W1**.

## Session log
- **2026-09-26:** Planning package v0.1 written: master plan, FRD, TDD, implementation plan, ADR-0001…0014, HTML view. No code.
- **2026-09-27 (a):** The client's procure-to-pay draft was reviewed, and the package revised to v0.2 (ADR-0015 local accounts, ADR-0016 system prepares / role confirms; MVP ≈ 10.4 months). No code.
- **2026-09-27 (b), planning Phase 1:**
  - **Reviews:** three parallel reviews of v0.2 and author verification produced 135 findings ([audit](docs/7-reviews/MOS-PACKAGE-AUDIT-v0.3.md)).
  - **Re-framed:** single company, no tenancy (ADR-0017); all six modules in ≤ 6 + 2 months with team paths A/B/C (ADR-0018); infrastructure not assumed (ADR-0019).
  - **Found:** the client is a flour mill ("Addis Flour Factory"). The swimlane is the owner's ChatGPT image, not the client's; it was moved to `docs/9-inputs/owner-brainstorm/` with the ChatGPT PDF, DOCX and ERP picture.
  - **FRD v0.3:** 225 requirements, each with an acceptance criterion; production and sales in v1.
  - **TDD v0.3:** three production-breaking defects fixed; production and sales/AR designed.
  - **Versions checked on the web:** Node 22 end of life 2027-04-30; NestJS 12 released 2026-08-28; BullMQ 6 removed repeatable jobs.
  - Nothing published, nothing committed, no code.
  - **Next:** owner review of Phase 1, then Phase 2 on the owner's go-ahead.
- **2026-09-27 (c), planning Phase 2** (owner: "go do phase 2"):
  - **Brainstorm review:** ChatGPT's PDF, ERP picture and swimlane checked against the client's draft page by page: 29 findings (8 High), e.g. no weighbridge, pass/fail instead of grades and deductions, no invoice matching, discrete instead of milling production, no Sales panel in the picture, outbound exit on paperwork. Its page references to the client draft were correct.
  - **Process model:** L0 value chain; L1 whole-ERP flow (33 hand-offs); 21 processes + 2 shared sub-processes as sequence-form swimlanes with 199 numbered steps; 27 lifecycle diagrams matching FRD §1.5; gate step sequences per visit class (closes audit F-52); document chain and silo-commingling trace; 15 synchronous hand-offs, 36 event types, 11 scheduled jobs; 64 exception cases; RACI; SoD pairs; controls, KPIs, reports; traceability generated from the step tables.
  - **Fed back (FRD, TDD, plan → 0.3.1):** 6 new v1 requirements (FR-PRC-022, FR-AP-019, FR-MFG-018, FR-SAL-016…018), BR-PRC-05, BR-MFG-01, SOD-17; 12 lifecycles added to FRD §1.5 and 3 rows corrected; a TDD tier violation fixed (`ReceiptCommitmentPort`), `OutboundLoadListener`, weigh-ticket verification status, invoice journal, weighbridge-scoped numbering, event catalogue. FRD now 231 requirements, 205 in v1.
  - **Incidental fixes:** v0.2 vocabulary notes in ADR-0005/0010/0013; stale TDD section references in ADR-0015, WP-00 and R16.
  - **Checks:** every v1 requirement maps to a step; every cited ID exists; lifecycle states equal FRD §1.5; every event has a producer and consumer; all 51 Mermaid diagrams parse (mermaid 12 parser, scratchpad only); the HTML renders in headless Chrome.
  - Nothing published, nothing committed, no code.
  - **Next:** owner review of Phase 2, then Phase 3 (database design) on the owner's go-ahead.
- **2026-09-27 (d), planning Phase 3** (owner: "go to phase 3"):
  - **Database design:** 230 tables in 9 schemas (core 45, md 30, inv 30, qc 22, proc 9, gate 33, mfg 18, sales 20, fin 23); 3,304 columns; 1,144 foreign keys; 35 entity diagrams. Conventions (keys, types, standard column sets, table classes and grants, stored vs displayed statuses, deletion, polymorphic references, time, currency, lock ranks); a data dictionary with class, writing steps and volume per table; 13 deep dives with worked rows (ledger, commingled silos, document links, approvals, numbering, audit, attachments, outbox, weigh tickets, QC, payables, production, sales); 10 transaction write-sets in lock order; invariants P1–P22 and 12 reconciliation jobs; operations (indexing, volumes, partitioning, retention, backup requirements, migrations and seeds, grants, encryption and personal data, expansion); ownership and CRUD matrix.
  - **Decisions (D-01…D-19), fed back:** one `fin.open_item` for every partner document; company accounts and tax codes moved to `md` (two tier fixes); new ports `PartnerLedgerPort` and `WeighTicketReader`; insert-only usage and approval decisions; per-module period status; goods issue as its own posting; supplier-return journal split with a new `supplier_return.posted` event. TDD and FRD → 0.3.2 (FR-PLT-006: the weighbridge station stays with the gate module off); process model → 0.2.
  - **Incidental fixes:** MOS `CLAUDE.md` pointed to TDD §6.3 for locks (now §7.3) and named `TxInterceptor` (now `UnitOfWorkInterceptor`); ADR-0017 had the same old name.
  - **Checks:** every FK target exists; every table has a key; all 28 FRD §1.5 lifecycles map to stored or derived states; all 32 process-model documents map to tables; every "written by" step exists; TDD insert-only tables are insert-only; every cited ID exists; the worked-example arithmetic is recomputed; all 88 Mermaid diagrams in the package parse; the HTML renders in headless Chrome.
  - Nothing published, nothing committed, no DDL or code.
  - **Next:** owner review of Phase 3, then Phase 4 (roadmap, Module 1 plan, pricing) on the owner's go-ahead.
- **2026-09-27/28 (e), planning Phase 4** (owner: "do phase 4 then 5 then 6"):
  - **Estimate:** bottom-up over 187 features that map every one of the 205 v1 requirements (checked by script). Lean-complete v1 = 3,102 AI-assisted build hours (likely; range ×0.75–×1.25); core = 2,380 h; standard 606 h; full 116 h.
  - **Capacity** at the owner's hours (lead 56 h, second developer 48 h, QA 40 h; 80% focus; one reserve week; +15% testing without QA): path A 2,080, B 1,809, C 974 build hours in 26 weeks.
  - **Finding:** at the likely estimate, no path builds all six modules in 26 weeks (A: procure-to-pay only; C: platform only). The v0.3 "≈ 58 dev-weeks, path A just fits" was about a third too low.
  - **Outputs:** a scheduler and pricing model in the scratchpad generated every table and Gantt chart; implementation plan v0.4, procurement module plan (48 stories = 640 dev hours, UAT script with 21 cases), commercial proposal. FX looked up on 2026-09-27: Wise 161.20, XE 162.17 mid-market; CBE 160.23/163.44 (2026-09-17); planning rate ETB 162.00.
  - **Incidental finding, fixed:** the FRD month tags and the v0.3 month plan disagreed (e.g. QC tagged M3 in the FRD, planned M4). The FRD tags were re-aligned to the committed schedule in Phase 6 (FRD 0.3.3).
- **2026-09-28 (f), planning Phase 5:** decision register (A decided, B owner, C client business by module, D infrastructure, E traceability of 54 earlier questions, each exactly once) and the client questionnaire with answer fields and needed-by weeks.
- **2026-09-28 (g), planning Phase 6** (three rounds with the owner):
  - **Delivery (ADR-0020):** one release with all six modules at core depth in 26 weeks, live at W35, on every path. The owner rejected the longer and phased options. Each path is planned at the velocity it needs (A ×1.32, B ×1.52, C ×2.50 the likely estimate), with checkpoints at W4 and W9; capacity is the lever if behind. Prices build-to-go-live: A $23,200, B $18,000, C $11,760.
  - **Commercial:** second developer 48 h; no free warranty (defects after hypercare through the retainer); the client owns its build, and the vendor may sell MOS to others.
  - **Stack (ADR-0021):** Node 24, NestJS 12 + Vitest, PostgreSQL 18 (audit log unpartitioned in v1, D-18 declined), BullMQ 6 + Redis/Valkey, Next.js 16.
  - **Hosting and operations (ADR-0022):** on-premises first; native services and Docker Compose both documented and tested; dev, staging and production; nightly full + WAL backups with a weekly restore test; GitHub private repository with Actions.
  - **Other:** the stale v0.2 claude.ai page was deleted at the owner's request. MOS was committed and pushed to a new private GitHub repository at the owner's request.
  - **Checks:** every placeholder filled; every cited requirement, table and link exists; every money cell satisfies hours × 5 = US$ and US$ × 162 = ETB; every procurement requirement has a story; story hours equal feature hours; all Mermaid diagrams parse; a Gantt chart rendered in headless Edge.
  - Nothing published; no DDL or code.
- **2026-10-02 (h), client documents v2** (owner: "analyze and audit them; plan how we integrate them; compare the stakeholders' architecture, design and stack decisions against ours and decide what to update or keep"):
  - Copied the three documents (PDF + DOCX) to `docs/9-inputs/from-client/` with a README.
  - Wrote [MOS-CLIENT-DOCS-V2-REVIEW.md](docs/7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md):
    - the v2 audit: 19 first-draft defects still present, 29 new findings;
    - the data-dictionary audit: 25 defects, 7 critical; 30 of 32 tables already covered; 14 fields to add;
    - the architecture comparison: 39 decisions;
    - the integration plan, with the effort, the owner decisions and the client questions.
  - **Recommended stack updates, awaiting the owner:** Vite SPA instead of Next.js (O-27); pg-boss on PostgreSQL instead of BullMQ + Redis (O-28); OpenAPI now.
  - **Incidental finding, fixed:** ADR-0022 assumed native Redis/Valkey on Windows Server, which doesn't exist; a correction note was added.
  - Nothing else changed (FRD, database design, TDD, plan, proposal and register untouched until the owner decides). Nothing published; no DDL or code.
- **2026-10-03 (i), sprint plan** (owner: "sprint by sprint, phase by phase, feature by feature implementation plan with clear timelines, tasks, deliverables, detailed for every module"):
  - Wrote [MOS-SPRINT-PLAN.md](docs/4-plan/MOS-SPRINT-PLAN.md) + local `.html`, generated from the implementation plan's Appendix A and its Gantt days:
    - seven phases (0 mobilisation … 6 maintenance and v1.1);
    - 13 build sprints and 4 go-live sprints, each with goal, features by stream, load against capacity, QA work, client inputs, deliverables and exit criteria;
    - per module, all 187 features with tasks, a "done when" test, requirements and the sprint on paths A/B and C;
    - a 51-item pull-in queue for standard features; the v1.1 list; where each pending INT item lands.
  - **Finding 1, disclosed:** dependency gaps leave the second developer about 260 estimated hours free (S5, S8, S11–S12). They are named as buffer slots with a use order: own overrun, then pending integration items, then relief of the lead's critical path, then pull-ins.
  - **Finding 2, fixed:** M4 (AS-13) was at W17, but QC core finishes in W18. The milestone reviews moved to sprint ends (M2 W10, M3 W14, M4 W18) in the implementation plan (0.4.1), the master plan (`.md` + `.html` timeline) and the procurement module plan (`.md` + `.html`). The velocity checkpoints stay at W4 and W9.
  - **Finding 3, disclosed (double-check pass, owner: "double check, make sure it is fully complete, detailed and clear"):** four of the FRD's 19 acceptance scenarios (AS-06, AS-10, AS-17, AS-19) consist only of standard-depth features, and eight more have a standard-depth step. The plan's §3.5 now maps every scenario to the sprint it can first pass, its core variant and the pull-ins its full variant needs. The release exit criterion is every core variant green.
  - **Added in the double-check:**
    - the acceptance-scenario map;
    - "depends on" and "hands off to" for every module;
    - go-live content per module (migration objects, UAT focus, training, go-live checks);
    - a "watch" line per sprint;
    - path C differences per milestone;
    - slot B0 (W1) and the QA capacity per sprint;
    - story ranges and line tables spelled out;
    - path C code review (AI review plus a checklist, as there is no second developer).
  - **Checks:**
    - every feature placed or queued once; hours 2,380 + 606 + 116 = 3,102;
    - sprint loads within about 8 h of capacity (10 h on path C); go-live hours 896 equal the implementation plan §8;
    - every one of the 230 tables, 48 stories, 19 scenarios and 205 v1 requirements is named, and every cited ID exists in its source;
    - links resolve; the Gantt chart renders in headless Edge; no horizontal scroll at 375 px on any tab.
  - Nothing published; no DDL or code.
- **2026-10-03 (j), client meeting pack and re-check of the client documents** (owner: fit everything, including deployment, testing, hosting and UAT, in under 6 months; plain wording and easy-to-read pages everywhere; check the client-input folder in detail; clean up the MOS folder; prepare the discussion material for the client's meeting on "the two documents"):
  - **Order chosen:** the meeting pack first, because the meeting is the same evening (7 PM local, "1 o'clock at night"), and its answers Q-01…Q-04 change what fits in six months. The other parts are phased below and wait for the owner's go-ahead.
  - **Re-check of the client inputs:** three parallel page-by-page and column-by-column passes over P2P v2 (28 images, every page viewed), the data dictionary (32 tables, 505 column rows) and the architecture proposal; DOCX against PDF for all three (identical content).
  - **Our review was corrected in place:**
    - "24 mock-ups" is in fact 28 images (27 distinct);
    - page 24 is a second version of page 23, not a copy;
    - at least 22 first-draft defects remain, not 19;
    - valuation is set per store, not per silo;
    - the dictionary's GRN line does separate rejected from short quantities;
    - the single-warehouse defect applies to the transfer header only;
    - the client's default language is JavaScript;
    - the client also asks for end-to-end tests and ER diagrams.

    **New sections:** §3.6 (11 new screen findings N-30…N-40, including one person in three roles, only wheat inspected, an invoice numbered three ways), §4.5 (13 missed client fields R-01…R-13, so 27 field additions in all), and §5.4 (CORS and other missing rows, the client's per-module page lists: non-conformance, production plans, finished-flour release, availability check, sales returns, plus branch connectivity). The review HTML was regenerated.
  - **Incidental finding, disclosed:** finished-flour QC release (f14i) is a pull-in, yet production output starts in QUARANTINE, so at core depth output could never be sold. Recommendation: promote f14i (and the availability check f15e) to core in the re-plan, about +16 h.
  - **Meeting folder** `docs/8-meetings/2026-10-03-client-documents/`:
    - `DISCUSSION-PACK.md` + `.html` + `.pdf`: agenda, the client's screens with each one's "keep / add / change / question", the data dictionary in plain words, the four decisions that size the work, 23 questions with defaults and answer columns, next steps, a glossary;
    - `OWNER-BRIEFING.md` + `.html`: for the owner only;
    - `img/`: the 28 screen images extracted from the client's PDF.
  - **Verification pass** (owner: "double check and verify your work"):
    - **Independent fact-check:** an agent checked about 95 claims in the pack against all 25 page images, the dictionary and the architecture proposal. It found 23 wrong or doubtful items, all corrected. The main ones:
      - the first draft had a grade and a score but **no price deductions**;
      - a failed result rejects **the wheat**, not the whole truck;
      - v2 has **no weighbridge** weights, but it does have weight quantities;
      - page 25 lists two invoices but pays one;
      - five spellings of the operation-unit column, not three;
      - the invoice step is already in the plan;
      - the Excel count import is already on page 13;
      - "SSO Login" and `is_primary` are the client's own terms.
    - **Own read-through:** stale counts in the briefing; a duplicated item; a contradiction in "we keep every field"; React Router presented as adopted although it depends on O-27; the next-steps range; glossary gaps (now 33 terms); an out-of-order §5.4 and a table that didn't render in the review.
    - **Pack:** it now says which additions come in the first release and which in the follow-up release, because blind count, the count import, "checked by" and invoice approval are standard depth today.
    - **Review:** its effort figures now include the re-check items INT-15…INT-18 (core +59, standard +16). New register rows C-48 (the module-list items), C-49 (one person in three roles) and I-18 (branch connectivity); C-24 updated (only wheat inspected). The briefing maps every pack question Q-01…Q-23 to its register row.
  - **Checks:**
    - every cited FR ID exists;
    - all 23 Q numbers are defined, and none is used without a definition;
    - all 28 screen images are present;
    - no markdown leaks into the three HTML views;
    - no sideways scroll at 375 px;
    - the pack prints to 28 pages;
    - 0 broken links across every MOS `.md`.
  - Nothing published, nothing committed, no code.
  - **Next (owner-gated):**
    - **Phase B, folder cleanup:** structure, archive the superseded documents, links.
    - **Phase C, the all-in six-month re-plan:** best after the meeting's answers on the gate and inspection.
    - **Phase D, plain-language and visual pass:** every document and HTML view, plus a shared glossary.
- **2026-10-03 (k), Phase B: folder cleanup and navigation** (owner: "organize and clean up, making navigation intuitive and easier on the MOS folder"):
  - **New layout.** `docs/` had 24 documents side by side. They are now in numbered folders, in reading order:
    - `1-overview`, `2-requirements`, `3-design`;
    - `4-plan`, `5-for-the-client`;
    - `6-decisions` (the register and `adr/`), `7-reviews` (the newest is current; the others are history);
    - `8-meetings` (was `meetings/`);
    - `9-inputs` (`from-client/`, was `client-input/`; `owner-brainstorm/`, was `reference-input/owner-brainstorm/`).
  - **File names are unchanged** (MOS- prefix), so references elsewhere and in the client's copies stay valid. 94 files moved; the 50 already tracked were moved with `git mv`, so git shows them as renames and their history follows.
  - **Navigation:**
    - **`index.html`:** a browser start page with a card per document (what it answers, version, "Open page" / "Text").
    - **`README.md`:** rewritten around an "I want to… → open" table and a folder map.
    - **`docs/README.md`:** the document map.
    - **A README in every numbered folder and in the meeting folder.**
    - **A breadcrumb line under every document title,** and a slim "← MOS start page" bar on every HTML view. The bar is hidden when printed, and is left off the client-facing discussion pack.
  - **Links:**
    - 278 links were recomputed by a script that maps each file's old location to its new one.
    - Plain-text path mentions were updated in 20 files, including the HTML footers and `CLAUDE.md`.
    - 21 link texts that showed old relative paths now show the file name.
    - `CLAUDE.md` gained a "Where things live" section and updated input notes.
  - **Checks:**
    - 712 links checked, 0 broken;
    - the database-design and process-model pages are intact (every embedded diagram present; sizes changed only by the path text);
    - no loose files left in `docs/` except its README;
    - the start page has no sideways scroll at 375 px;
    - a full backup taken before the move (scratchpad).
  - Nothing deleted, published or committed.
  - **Next:** Phase C (the six-month re-plan), ideally after the meeting's answers on the gate and inspection, then Phase D.
- **2026-10-03 (l), Phase C: everything in six months** (owner: "make it so that everything including deployment, testing, hosting, UAT etc. fits in under 6 months"):
  - **New calendar** ([ADR-0025](docs/6-decisions/adr/ADR-0025-everything-in-six-months.md)): build W1–W20 with rolling UAT from about W8; test server by W4, production server by W16; system test and final UAT W21–W22; training and migration rehearsal W23; **go-live W24**; hypercare W25–W26. Checkpoints W4 and W8. No reserve week.
  - **Estimate:** the client-documents review's items counted: 12 new features and 3 changed (199 features; core 2,439 h, standard 622, full 116, total 3,177). Hours for INT-10/INT-11 assume O-27/O-28 as recommended.
  - **Model:** a new six-month model (scratchpad `p9/model6.py`, same method as Phase 4/6): speeds needed **A+ ×1.33, A ×1.89, B ×2.22; C does not fit** (go-live work 598 h vs 336 h available). Without the gate module and with pass/fail inspection: A+ ×1.19, A ×1.59. Prices at US$5/h: A+ $22,190, A $17,390, B $13,390.
  - **Documents updated:** implementation plan 0.5 (rewritten, plain-language glossary); sprint plan 0.2 (regenerated: 13 sprints, A+ primary, A in every feature row, buffer slots, rolling UAT, go-live sprints S11–S13); Module 1 plan 0.2 (54 stories, UAT-22 delivery record); commercial proposal 0.3 (teams A+ and A; B and C marked "owner to confirm", O-34); decision register 0.2 (A-42; O-27…O-35; C-36…C-49; I-18; every needed-by week moved); questionnaire 0.2 (new section 12); FRD 0.3.5 (month tags M1–M5); master plan 0.5 (rewritten, Mermaid diagrams); ADR-0018/0019/0020 status notes; TDD month labels; review status; owner briefing §5, §6, §8.
  - **Web pages:** six documents that were text-only now have pages (FRD, TDD, implementation plan, and the three history reviews); eight pages rebuilt with one builder (`p9/house.py`), which fixes tables nested in lists that the old builder dropped, links `.md` → `.html`, and the start-page bar.
  - **Checks:** 60 Mermaid diagrams parse; every HTML link resolves; every md table appears in its page; no markdown leaks; no horizontal scroll at 375 px on any of the 18 pages; a full backup was taken first (scratchpad `mos_backup_pre_phaseC/`).
  - **Incidental fixes:** the master plan's HTML was a hand-made v0.3 page with stale labels (PostgreSQL 16+, "queue decided in Phase 6", 26 + 9 weeks); replaced. Long code and tokens wrapped on phones.
  - Nothing published. Code: none.
- **2026-10-03 (m), price cap, reading guide, platform reorganisation** (owner: "keep the price within 1 million ETB…"; "the step by step… order… navigation guiding me"; "do the same organisation… for DMS, GMS, SSO and the root"):
  - **Pricing and timeline** ([MOS-PRICING-AND-TIMELINE.md](docs/5-for-the-client/MOS-PRICING-AND-TIMELINE.md) + `.html`, generated by `p9/build_pricing.py`): one **fixed price of ETB 986,615** (98.7% of the cap) for every given requirement (core, standard and full) and every go-live service, allocated over 4,027 hours of estimated work at ETB 245; broken down by phase, module, sprint (13 + v1.1), sprint × module, every one of the 199 features, the go-live services, and a pay-as-accepted schedule (10% at signature). Outside it: maintenance, new features, hosting, third-party services. Every breakdown adds up to the total (checked by script).
  - **Owner note (A-43):** at the US$5 rate card the six-month team costs ETB 3.59 M (A+), 2.82 M (A), 2.17 M (B); the fixed price covers about 27% of A+'s cost. Recorded in the register and the owner briefing, not in client documents.
  - **Commercial proposal 0.4:** the fixed price, packages, modules, payment schedule; team section rewritten (price independent of the team); change requests at ETB 810/h. Implementation plan, master plan, ADR-0025 and briefing now call the dollar figures "team cost at the rate card".
  - **Reading guide** ([READING-GUIDE.md](READING-GUIDE.md) + `.html`): 20 stops in seven parts with time, focus and a "done when" line each; a fast route (★, about 1 hour); the numbers to know; the question → page table; a pre-meeting checklist. Linked first on the start page and in the README.
  - **Platform reorganisation (root, SSO, GMS, DMS):** start pages and indexes added; dated records and transcripts moved to `history/`; old Office files and orphan manuals moved into labelled subfolders; stale bundle copies refreshed; banners on HTML pages older than their `.md` (see the root `CLAUDE.md`, "Other root folders").
