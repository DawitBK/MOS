# ADR-0003: Modular monolith with one Postgres schema per bounded context

- **Status:** Accepted (planning), 2026-09-26. **Amended by [ADR-0018](ADR-0018-lean-v1-six-months.md)** (2026-09-27): `mfg` and `sales` are v1 schemas, and the dependency direction is corrected to explicit tiers with ports (the v0.2 direction had cycles; audit T-18).

## Context
The inbound flow must be consistent in a single transaction across gate, QC, inventory and procurement. The team is 1–2 developers. Microservices would multiply deployment, observability and consistency work with no benefit at the target scale (≤ 300 users and 5 sites per installation).

## Decision
- One API process and one worker process share one PostgreSQL database.
- Schemas: `core`, `md`, `inv`, `qc`, `proc`, `gate`, `mfg`, `sales`, `fin`.
- Modules interact only through each module's exported application services (`index.ts`), or through outbox events.
- dependency-cruiser enforces **tiers**: core (0) ← md (1) ← inv (2) ← qc, proc (3) ← gate, mfg, sales (4) ← fin (5). A module imports only lower tiers; upward calls use ports the lower module declares (e.g. `UsageDecisionListener`, `JournalPort`, `OutboundDocumentChecker`; TDD §1.3).

## Consequences
- Simple to run; atomic cross-module postings where needed.
- The boundaries allow a later split along schema lines if scale ever demands it.
- Discipline is enforced by lint in CI, not by the network.

## Alternatives considered
- **Microservices:** rejected; distributed consistency and operational cost with no benefit at this scale.
- **Single-schema monolith:** rejected; module ownership of tables would be unenforceable.
