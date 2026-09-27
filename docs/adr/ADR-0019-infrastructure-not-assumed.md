# ADR-0019: Infrastructure is not assumed; hosting and deployment are decided after stakeholder answers

- **Status:** Accepted (planning), 2026-09-27. **Amended by [ADR-0022](ADR-0022-hosting-and-operations-baseline.md)** (2026-09-28): the proposal leads with on-premises hosting; native services and Docker Compose are both offered; dev, staging and production; a backup and observability baseline. The queue is Redis/Valkey + BullMQ ([ADR-0021](ADR-0021-stack-lock-in.md)). Turns the earlier "on-prem = Linux + Docker Compose" decision (v0.1 master plan, TDD §18.3) into a **proposal**. Amends [ADR-0013](ADR-0013-attachments-and-dms.md) (local-disk storage by default).
- **Trigger:** owner direction (2026-09-27). We don't currently know the detailed infrastructure, so we don't assume it; we list the questions for the stakeholders instead.

## Context
v0.1/v0.2 designed three deployment modes (shared SaaS, dedicated cloud, on-prem Linux + Docker Compose) and an edge agent. None of that is known for the client:
- where the system will run (their server room, a local data centre, a cloud);
- which operating system;
- whether Docker is acceptable;
- internet quality at the gate and the weighbridge;
- backup targets;
- email relay;
- identity provider;
- devices (PCs, tablets, printers, scanners, weighbridge indicator).

Designing the deployment before these answers would be guesswork. It would also add infrastructure (Redis, MinIO, ClamAV, a container runtime) that the client might not be able to operate.

## Decision
1. **No hosting or deployment target is fixed now.** The only environment operated before the stakeholder answers is **development**, as before.
2. **The application stays portable,** so any reasonable target works:
   - **Configuration:** 12-factor config through env files, per the house env contract. Production reads `.env.production` only, refuses placeholders and has no hardcoded hosts.
   - **Required runtime dependencies are kept minimal:** Node.js and PostgreSQL. *(Phase 6, ADR-0021: Node.js 24 LTS, PostgreSQL 18, and Redis/Valkey for the queue.)*
   - **Everything else is optional or swappable behind an adapter:**
     - file storage on **local disk by default**, S3-compatible optional;
     - virus scanning optional (ClamAV adapter, fail-open or fail-closed by setting);
     - email through any SMTP relay;
     - the queue backend: Redis + BullMQ or a Postgres-backed queue, **decided in the Phase 6 session: BullMQ + Redis/Valkey** ([ADR-0021](ADR-0021-stack-lock-in.md)) (the trade-off is recorded in [ADR-0006](ADR-0006-outbox-bullmq.md)).
   - **Serving:** under a sub-path or a sub-domain, behind any reverse proxy (Apache, nginx, IIS). Next.js `basePath` is baked at build time, so it is a build parameter.
   - **Supported hosts:** Linux and Windows Server, without requiring containers. Container images are an optional packaging.
3. **Candidate deployment options are documented as proposals** (TDD §18.3), each with what it needs from the client:
   - on-prem on the client's server (Windows or Linux);
   - a virtual server at a local or cloud provider;
   - a managed cloud setup.
4. **Stakeholder questions** on hosting, servers, OS, network, internet at the gate, backup/DR, domain/TLS, email, identity provider, devices, power/UPS, security policy, retention and integrations:
   - They go to the Phase 5 decision register and stakeholder questionnaire, with needed-by dates.
   - The hosting decision is needed **before month 5** (W18 in the committed schedule), so production setup can be prepared during M6 and executed in M7–M8.
5. **NFR targets** (availability, RPO/RTO, backup frequency) are **proposed defaults** until the client confirms what the chosen infrastructure can support.

## Consequences
- No work is wasted on a deployment shape the client can't use.
- The house platform runs on Windows Server with plain Node processes (`production-start.bat`, optional PM2). MOS can follow that model if the client is similar, without a container runtime.
- Hosting and deployment costs in the commercial proposal (Phase 4) are line items "priced at actuals after the infrastructure answers", not guesses.
- The development environment still runs PostgreSQL (and, if chosen, Redis) locally. Docker for dev dependencies is a developer convenience and not a production decision.
