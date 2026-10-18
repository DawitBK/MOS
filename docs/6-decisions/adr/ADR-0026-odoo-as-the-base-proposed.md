# ADR-0026: Odoo as the base for MOS (PROPOSED, recommendation: no-go for now)

<!-- nav -->[MOS](../../../README.md) › [6. Decisions](../README.md) › [ADRs](README.md)

- **Status:** **Owner overruled the recommendation on 2026-10-18: MOS is being built on Odoo 20 anyway** (repository `mos-odoo`, private). The analysis below stands as the record of the risks accepted. **Decision on the stack (owner, 2026-10-18: "do what is best"):** [ADR-0021](ADR-0021-stack-lock-in.md) is **superseded for the MOS build**: one build, on Odoo 20 (Python server, PostgreSQL, the OWL JavaScript web client). The NestJS documents (database design, TDD, FRD) are kept as reference and are not deleted; the FRD remains the requirements source. Re-open only if a trigger below fires.
- **Question:** should MOS be built as custom addons on Odoo instead of the planned custom NestJS system?
- **Evidence:** [ODOO-FIT-GAP.md](../../3-design/ODOO-FIT-GAP.md), every one of the 231 requirement ids classified, plus a check of OCA coverage on 20.0, 19.0 and 18.0.

## Findings

1. **Odoo as shipped meets 31% of the requirements** (61 standard, 10 configuration of 231). They are the commodity part: stock objects, purchase and sales documents, ledger-level accounting with the Ethiopian chart and taxes, manufacturing orders.
2. **55% (128) are custom work on Odoo as well**, and every requirement that makes MOS what it is sits there: gate and weighbridge (30 of 30), inbound process managers (10 of 10), lab and quality (22 of 26), the approvals and segregation-of-duties engine, quality price rules, hash-chained audit. Odoo Community has no quality module, no gate concept, no approvals.
3. **Another 13% (29) assume OCA modules, and on 20.0 only 1 of the 20 modules checked exists** (19.0: 11 of 20; 18.0: 16 of 20). The approvals engine (`base_tier_validation`) is missing on 19.0 and 20.0. Waiting means a schedule that depends on volunteers; building them is more custom work.
4. **One non-negotiable cannot be kept:** posted rows immutable even for administrators, enforced by database grants (FR-PLT-021, ADR-0007/0008), and tests that run as a non-owner database role. Odoo runs as the database owner. A weaker, trigger-based form is possible.
5. **Everything in ADR-0021 would be reversed** (Python and Odoo's framework instead of TypeScript), and the 230-table database design, the TDD and the work already planned would not carry over. The team would need Odoo and Python skills the 26-week plan does not include.

## Options

| | A. Keep the custom build (ADR-0021) | B. Odoo 20 + custom addons | C. Odoo 18 + OCA + custom addons | D. Hybrid |
|---|---|---|---|---|
| What it is | The plan as written | Rebuild MOS on Odoo 20 | As B, on 18.0 where 16 of 20 OCA modules exist | MOS stays custom; accounting posts to Odoo (or any ledger) through the existing accounting export (FR-AP-010) |
| Meets the immutability non-negotiable | Yes | Weaker form only | Weaker form only | Yes (in MOS) |
| Custom work | All, but on a design already done | 128 requirements, plus the 29 OCA ones until the modules exist | About 128, plus 13 | All of MOS; only the ledger is Odoo |
| Schedule risk | Known (26-week plan) | High: unproven modules, new stack | Medium-high: older base | Low |
| Skills | House stack | Python and Odoo | Python and Odoo | House stack, plus integration |

An effort estimate for B and C is **not** given here: it would mean re-estimating all 199 features on a different framework, which is a separate piece of work to do only if the owner wants to keep B or C open.

## Recommendation

**Do not adopt B or C now. Keep A (ADR-0021).** Odoo would replace the part of MOS that is already clear (stock, purchase, sales documents) and leave all the hard, differentiating part to be written from scratch on an unfamiliar framework, while giving up the immutability guarantee. If the client later wants Odoo for finance, option D uses the accounting export that is already in v1.

**Triggers to re-open:** `base_tier_validation`, `auth_oidc`, `purchase_request` and `quality_control_oca` all published on the 20.0 branch (or the client commits to Odoo 18 and accepts C); or the client states that "it must be Odoo". Re-run the OCA check from the fit-gap section 3 first.

## Consequences if accepted as recommended

None for the plan: ADR-0021 and the 26-week schedule stand, and no `mos-odoo` repository is created. `MOS_ENABLED` and the `mos` OIDC client in SSO are unaffected: they follow the MOS build, not Odoo.
