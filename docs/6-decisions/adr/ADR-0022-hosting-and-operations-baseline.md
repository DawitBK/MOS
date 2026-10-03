# ADR-0022: Hosting proposal and operations baseline

<!-- nav -->[MOS](../../../README.md) › [6. Decisions](../README.md) › [ADRs](README.md)

- **Status:** Accepted, 2026-09-28 (owner decisions in the planning Phase 6 session).
- **Amends** [ADR-0019](ADR-0019-infrastructure-not-assumed.md): the options stay open for the client, but the proposal now leads with defaults.

## Decision

1. **Hosting proposed first: on-premises, on the client's server.**
   - Windows Server or Linux, whichever the client runs.
   - The gate, weighbridge and stores keep working when the plant's internet link is down.
   - A local data centre or cloud stays an option, chosen from the client's answers (register part D).
2. **Packaging: both, and the client chooses.**
   - **Native services:** API, worker and web as Node processes, with PostgreSQL and Redis/Valkey as system services. This is the house model (`production-start.bat` style), with PM2 optional.
   - **Docker Compose:** the same services as containers, for clients that run Docker.
   - Both are documented, and both are tested in the staging environment before go-live.
   - The go-live estimate carries the environment set-up hours; the second packaging adds test time inside it.
3. **Environments:** development, staging and production. Staging mirrors production's packaging and is where UAT and the restore drills run.
4. **Backup and recovery baseline:**
   - a nightly full PostgreSQL backup plus continuous WAL archiving;
   - a nightly copy of the attachment store and of the two keys (encryption, audit HMAC), held separately;
   - one copy off the server and one off site;
   - a **weekly restore test on staging**;
   - proposed targets: RPO 15 minutes, RTO 4 hours, until the client confirms (register I-09).
5. **Source and CI:**
   - a **private GitHub repository** with GitHub Actions running the TDD §18.4 gates on every push: types, lint, dependency rules, unit and integration tests as `mos_app`, migration safety and the build fingerprint;
   - release builds are produced from tags.
6. **Observability:**
   - structured Pino logs with correlation IDs and secret redaction;
   - `/api/v1/health` (database, Redis, worker heartbeat) and protected `/metrics`;
   - alerts on failed backups, dead outbox events, a stopped worker, disk space and the size of `core.audit_event` (ADR-0021).

## Consequences

- **The commercial proposal** leads with on-premises hosting. It lists the indicative server sizing, and infrastructure is at cost.
- **The client's IT** must provide what the chosen packaging needs: services, or Docker.
- **The house model** (Windows Server, native Node) is supported without containers, as ADR-0019 required.
- **Correction (2026-10-02, [client documents v2 review](../../7-reviews/MOS-CLIENT-DOCS-V2-REVIEW.md) §6):** neither Redis nor Valkey ships a supported native build for Windows Server. On a Windows installation with native services, the queue store needs Memurai (a commercial Redis-compatible Windows service), WSL2 or Docker. The requirement disappears if owner decision O-28 replaces BullMQ with pg-boss on PostgreSQL. Decision 2's "PostgreSQL and Redis/Valkey as system services" holds as written only on Linux.
