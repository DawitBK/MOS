# ADR-0014: MOS is standalone; gate management is built inside MOS; GMS is not used

<!-- nav -->[MOS](../../../README.md) › [6. Decisions](../README.md) › [ADRs](README.md)

- **Status:** Accepted (planning), 2026-09-26

## Context
The existing GMS is a visitor and appointment system: guests, hosts, time slots and approvals. It has no concept of vehicles, drivers, goods, weighbridges or gate posts, and it holds the platform's only live production data. Gate management in MOS must commit in the same transaction as goods receipts and dispatch. The product owner confirmed that MOS is company-neutral and standalone: it may use the SSO/IdP and, if needed, DMS or DMS patterns, but not GMS.

## Decision
- Build gate management (vehicles, drivers, blacklist, shifts, appointments, visits, weigh tickets, exit clearance, gate passes, incidents, paper mode) as the `gate` module inside MOS.
- Copy useful *patterns* from GMS where relevant: the state-machine class, idempotency middleware, and raw-event storage for webhooks. Share no code or data with it.
- The minimal people-visitor log in MOS is intentionally basic. Organisations that need full visitor management keep their own system.

## Consequences
- No coupling to any one company's systems; no risk to GMS's live data.
- Gate, inventory, QC and procurement stay transactionally consistent.
