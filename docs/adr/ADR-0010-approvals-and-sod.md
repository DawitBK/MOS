# ADR-0010: A data-driven approvals engine with a segregation-of-duties rule engine

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0018](ADR-0018-lean-v1-six-months.md)** (2026-09-27) for the 6-month v1:
  - **v1:** sequential stages; one-of-N approvers per step; conditions from a **structured approval matrix** (document type × amount band × site × category, plus a few named flags such as sole-source or recent bank change); simple delegation; reminder plus fallback escalation.
  - **v1.1:** the general JSON-AST conditions and parallel / ALL / QUORUM steps described below.
  - **Unchanged:** the SoD rule types and the "can anyone decide" check. In the Decision text below, `ROLE_CONFLICT` "reported at MVP, blocked in P2" now reads **reported in v1, blocked in v1.1** (FRD SOD-13).

## Context
Approval matrices and SoD vary per customer and must be data, not code. DMS's chain model is a good base: locked steps, row lock plus a status-guarded decision, one live chain per item, and a check that someone can decide. What it lacks is amount- and condition-based routing, parallel and quorum steps, delegation, escalation that reassigns, and SoD.

## Decision
- **Policies and rules:** policies per document type (and optionally per company) hold rules with **typed JSON-AST conditions** over whitelisted facts. Conditions are never eval'd.
- **Step structure:** each matching rule contributes a step. Stages run sequentially; steps within a stage run in parallel, completing on ANY_ONE, ALL or QUORUM(n).
- **Assignments** are materialised into users after SoD exclusion. Authority is re-checked at decision time.
- **Submit** checks that every step has at least one eligible approver.
- **Delegation** is not chained, and SoD applies to both delegator and delegate. **Escalation** goes remind → reassign → alert.
- **SoD rule types** form a closed coded set, parameterised by data: `NOT_DOC_ACTOR`, `CROSS_DOC_DISTINCT`, `MASTER_EDITOR_NOT_APPROVER`, `ONE_STAGE_PER_USER`, `RESULT_ENTRY_NOT_DECIDER`, and `ROLE_CONFLICT` (reported at MVP, blocked in P2). The data sources are `core.doc_actor` and `core.doc_link`.

## Consequences
- Administrators configure thresholds and routes without code changes.
- New *kinds* of rules are deliberate code changes, which keeps "configurable" from becoming unbounded.
