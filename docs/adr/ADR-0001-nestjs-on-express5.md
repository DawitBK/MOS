# ADR-0001: NestJS 11 on the Express 5 adapter as the backend framework

- **Status:** Accepted (planning), 2026-09-26.
  - **Amended by [ADR-0017](ADR-0017-single-tenant-single-company-v1.md)** (2026-09-27): no tenancy concern; the interceptor is `UnitOfWorkInterceptor`.
  - **Version decided in the Phase 6 session (2026-09-28, [ADR-0021](ADR-0021-stack-lock-in.md)): NestJS 12** (ESM packages, built-in Standard Schema validation, Vitest) on **Node 24 LTS**. The title's "NestJS 11" is historical; the Express-adapter decision stands.
- **Deciders:** Product owner (delegated the choice), planning session

## Context
The house standard is bare Express 5. MOS is an ERP with around ten bounded contexts. Many concerns cut across all of them: the transaction per request, idempotency, authorization, audit and validation. It will be built by 1–2 developers who lean heavily on AI assistance. With bare Express, structure depends on discipline, and AI-generated code drifts in style from one session to the next.

## Decision
Use **NestJS 11 with the Express 5 platform adapter**:
- Modules map to bounded contexts.
- Guards handle authn/authz, an interceptor owns the request transaction, and pipes validate with Zod via `nestjs-zod`.
- `nestjs-cls` provides request context through AsyncLocalStorage; `nestjs-pino` handles logging.

## Consequences
- Express 5 still runs underneath, so house middleware (helmet, rate limiting, pino-http) and house knowledge carry over.
- Enforced conventions and dependency injection make modules testable, and give AI sessions a consistent pattern to follow.
- **Pitfalls to avoid** (recorded in `MOS/CLAUDE.md`):
  - No request-scoped providers: they are slow and cascade. Use CLS instead.
  - Don't use class-validator: Zod is the house standard.
  - Only one place opens transactions, the `UnitOfWorkInterceptor` (TDD §7.2).
- This deviates from the handbook's "Express by default", and this ADR is the record of that deviation.

## Alternatives considered
- **Bare Express 5:** rejected; structure would rely on discipline alone.
- **Fastify:** rejected; it adds no benefit at this scale and moves further from house knowledge.
