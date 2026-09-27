# ADR-0018: A lean but complete v1: all six modules in ≤ 6 months of development, then 2 months to go live

- **Status:** Accepted (planning), 2026-09-27. **Amended by [ADR-0020](ADR-0020-six-modules-one-release-26-weeks.md)** (2026-09-28): the release is all six modules at core depth in 26 weeks on every path (date and scope both fixed; capacity is the lever), and the month weights below are replaced by the week-level schedule of implementation plan v0.4. Replaces the MVP / Phase 2 / Phase 3 structure of the v0.2 implementation plan. It amends [ADR-0010](ADR-0010-approvals-and-sod.md) (a leaner approvals engine), and it moves the edge agent in [ADR-0009](ADR-0009-inbound-process-managers.md) and [ADR-0005](ADR-0005-idp-agnostic-oidc.md) to Later.
- **Trigger:** owner direction (2026-09-27):
  - the whole ERP within 6 months of development, roughly one module per month, with judgment on the weights;
  - then 2 months for production testing, hosting, deployment and the rest;
  - three team options to present to stakeholders.

## Context
v0.2 planned a narrow 10.4-month MVP covering procurement, inventory, QC, gate and AP. Production and sales followed in Phase 2, and the full scope took about 22–24 months at 1.5 FTE.

The owner's commercial reality is different: the client expects all six scope-of-work modules. The owner's capacity is also higher than planned. The lead developer works 7 days × 8 hours with heavy AI assistance, and a second developer and a QA engineer are options.

v0.3 also removes large cost centres:
- multi-tenancy ([ADR-0017](ADR-0017-single-tenant-single-company-v1.md));
- SaaS operations;
- the bundled IdP;
- the edge agent;
- an assumed infrastructure ([ADR-0019](ADR-0019-infrastructure-not-assumed.md)).

## Decision

1. **v1 = all six modules at "lean-complete" depth**: procurement (including supplier payment), inventory, lab/QC, gate, production and sales. Every module's core flow works end to end, with its controls. Depth that isn't needed to run the plant on day one moves out.
2. **Three scope tags replace MVP/P2/P3:**
   - **v1:** in the first release. Each v1 requirement also names the month it is built in (M1–M6).
   - **v1.1:** the first follow-up release, planned during hypercare and not in the 6-month commitment.
   - **Later:** product roadmap items with no date (SaaS, hardware integration, portals, MRP, costing and so on).
3. **Development window ≤ 26 weeks, then ≈ 9 weeks to go live:**
   - system integration testing;
   - user acceptance testing;
   - data migration;
   - training;
   - production hosting and deployment setup;
   - go-live;
   - hypercare.

   Maintenance and support follow as a separate service.
4. **Month-by-module roadmap.** The effort weights below were revised by the v0.3 audit (finding P-08). The first draft gave Foundation + Procurement 1.25 months and did not weight payables at all. Phase 4 converts these weights into hours per team path.

   | Effort weight (months of team capacity) | Foundation | Procurement + payables | Inventory | Lab/QC | Gate | Production | Sales | Reserve | Total |
   |---|---|---|---|---|---|---|---|---|---|
   | v0.3 | **1.5** | **0.75** | **0.75** | **0.5** | **0.5** | **1.0** | **0.75** | **0.25** | **6.0** |

   **Calendar** (two build streams on paths A/B; each month ends with a demo):

   | Month | Primary focus (demo) | Parallel stream |
   |---|---|---|
   | M1 | **Foundation I:** platform core, identity (local accounts + optional SSO), roles and site scopes, org and master data, storage units and silos, web shell | Inventory ledger engine starts |
   | M2 | **Foundation II + Procurement:** approval matrix, numbering, attachments, PDFs; supplier registration, PR, PO issued | Ledger engine proven by property tests |
   | M3 | **Procurement completes + payables + Inventory:** GRN (gate off), returns; PV → approval → payment; issues, transfers, adjustments, counts, stock balance. **Procure-to-pay works end to end without inspection** | Counts, period close |
   | M4 | **Lab/QC + Gate:** inspection, grading and score, decisions, deductions; gate entry/exit, weigh tickets, exit clearance, the automated inbound loop. **End of M4: the client's full procure-to-pay flow with inspection (AS-13)** and the gate loop (AS-01, AS-02) | Production master data |
   | M5 | **Production:** recipes with co- and by-products, milling runs, packing, finished-goods QC, material-cost settlement, yield KPIs | Sales master data and orders |
   | M6 | **Sales + receivables:** orders with credit checks, delivery with outbound gate, invoicing, receipts, returns; dashboards complete; the 0.25-month **reserve**. **Feature freeze at W26** | Hardening |
   | M7–M8 | SIT, UAT, data migration, training, production deployment, go-live, hypercare | — |

   **Why these weights:**
   - **Foundation carries the cross-cutting services** every module needs from its first document: approvals, numbering, attachments, PDFs, notifications, export and search.
   - **The inventory ledger is the correctness core.** It starts in M1 in parallel, because every posting goes through it.
   - **QC and gate are narrower but control-heavy.**
   - **Production is the most domain-specific** (co- and by-products, extraction rate, silo draw-down) and has no client input yet.
   - **Sales reuses** the approval, inventory, reservation and gate machinery built earlier.
   - **The reserve is explicit,** because the go-live window cannot double as build buffer without eating UAT.

   **Capacity check** (the audit's estimate in v0.2 dev-week units; Phase 4 re-estimates in hours):
   - Lean-complete v1 ≈ 58 dev-weeks, i.e. ≈ 2.2 effective FTE over 26 weeks.
   - Path A just fits.
   - Path B needs a 15–20% depth cut.
   - Path C needs a ≈ 45% depth cut, the "core depth" named below.
5. **Three team paths.** Phase 4 prices each one:
   - **A: 2 developers + AI + QA.** Full v1 scope, lowest schedule risk.
   - **B: 2 developers + AI.** The same v1 scope. The developers absorb testing, which puts more risk on the date.
   - **C: 1 developer with heavy AI (56 h/week).** Still 6 months, but at **core depth**. The implementation plan names which v1 items drop to v1.1 under path C.
6. **Leaner engineering to fit the window.** The design principles are unchanged; the machinery is smaller:
   - **Approvals** (amends ADR-0010):
     - v1 has sequential stages with one-of-N approvers per step, conditions from a **structured approval matrix** (document type × amount band × site × category), simple delegation, and reminder plus fallback escalation.
     - The general JSON-AST conditions and parallel / ALL / QUORUM steps move to v1.1.
     - The SoD rule types stay.
   - **Audit:** a single HMAC hash chain; sharding moves to Later.
   - **Weighbridge:** manual entry with the full manual controls (photo, blind verification, forced re-tare). Edge-agent capture and ZPL printing move to Later.
   - **Industry packs:** grain milling (the client's domain) ships in v1; flooring moves to Later.
7. **Scope rule:** nothing enters v1 unless something of equal effort leaves it, or the date moves by an agreed amount (implementation plan §change control).

## Explicitly deferred (Later unless noted)
- **Platform:** shared multi-tenant SaaS, tenant onboarding, plans/billing, bundled IdP, public API, webhooks, API keys, Amharic/Ethiopian-calendar pack.
- **Hardware:** edge agent, weighbridge capture, ZPL printers, ANPR, barriers.
- **Procurement:** supplier portal, RFQ comparison workflow, contracts. Supplier self-registration link is v1.1.
- **Production:** MRP/MPS, finite scheduling, labour and overhead costing, variance analysis. OEE and routings are v1.1.
- **Inventory:** FIFO valuation option, landed cost, serial numbers, catch-weight.
- **Sales:** quotations and dispatch planning are v1.1. Tokenised proof-of-delivery link is v1.1. Fiscal device / e-invoicing is Later, **pending a client answer: it may be legally required for sales invoices.**
- **Finance:** accounting-package adapters (CSV export is v1), advances and payment runs (v1.1), a general ledger (never, unless re-scoped).

## Consequences
- The client gets the whole scope of work in about 8 months (6 build + 2 roll-out), instead of an MVP at 10 months and the rest after 2 years.
- Depth is traded for breadth. v1.1 and Later hold real value, and the client must see those lists before signing.
- The schedule depends on:
  - timely client answers (the decision register lists them with needed-by weeks);
  - a scope freeze per module;
  - sustainable hours. A 7-day week for 8 months is a burnout risk, recorded in the risk register.
- Phase 4 validates this ADR. If the hour estimates show a path cannot fit its depth in 26 weeks, the scope moves (per path) rather than the date. The estimate exposes that honestly.

## Alternatives considered
- **Keep the v0.2 phasing (MVP 10.4 months, then Phase 2):** rejected; it doesn't meet the client's expectation of all six modules.
- **Six months with v0.2's full depth:** rejected; not credible for this team size without cutting quality controls.
- **Cut whole modules instead of depth:** rejected; every module in the scope of work is contractual.
